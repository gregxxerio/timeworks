<script lang="ts">
import {Component, Vue, Watch} from 'vue-property-decorator';
import Rectangle from "@/views/components/Rectangle.vue";
import moment from "moment-timezone";
import 'moment/locale/es';
import {MonthPickerInput} from "vue-month-picker";
import {RotateSquare2} from "vue-loading-spinner";
import axios from "axios";
import {Modal} from "ant-design-vue";
import XLSX from "xlsx";
import collect from "collect.js";
import MonthlyReportUser from "@/views/MonthlyReportUser.vue";
import StepTimesheet from "@/views/components/Steps/StepTimesheet.vue";

declare const gapi: any;
@Component({
  components: {RotateSquare2, MonthPickerInput, Rectangle, MonthlyReportUser, StepTimesheet}
})
export default class StepImport extends Vue {
  // GOOGLE CALENDAR CREDENTIALS AND INFO
  CLIENT_ID = "438182473804-lold6v82v1m26dht1dr3j58e0tmbjg3g.apps.googleusercontent.com";
  API_KEY = "AIzaSyC2eQA62ImF7GlMuscwl26zg2X0_dBkIcY";
  DISCOVERY_DOCS = ["https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest"];
  SCOPES = "https://www.googleapis.com/auth/calendar.readonly";
  authorized = false;
  //LOCAL DATA
  isMonthlyReport = true;
  isLoading = false;
  error = false;
  errorMessage: '';
  month = moment().month() + 1;
  monthName = "";
  year = moment().year();
  tokenVersion = true;
  continueToEnd = false;
  timeMinString = "";
  timeMaxString = "";
  timeMin: any = null;
  timeMax: any = null;
  codes: any = [];
  api: any = null;
  isShowWeekendAlert = false;
  emailNow: any = null;
  infoProjects: Array<any> = [];
  infoRepeatedProjects: Array<any> = [];
  dataGrouped: any = [];
  totalHours: number | string = 0;
  //TOKEN METHOD
  calendarsNumber = 0;
  calendarNumbersProcessed = 0;
  repeatedEvents = false;
  invalidCodes = false;
  invalidCodesCaracter = false;
  weekendEvents = false;
  importedData = false;
  printDataTable = false;
  showDataInWeekend = false;
  itemsOverlappings: any[] = [];
  screenWidth: any = '300px';
  payloadVariable: any = {};
  loadingItem = false;
  hasTimesheet = false;

  items: any[] = [];
  async created() {
    //projects-code
    const result: any = await axios.get("/api/get/data/projects-code");
    if (result.data.length) {
      this.codes = result.data.map((item) => item.codigo);
    }
    //
    const response: any = await axios.get("/api/google-calendar/oauth/valid");
    this.error = response.data.error;
    this.errorMessage = response.data.message;

    this.api = gapi;
    await this.handleClientLoad();
    this.screenWidth = (window.innerWidth - 55) + 'px';
    this.hasTimesheet = this["$store"].state.user.hasTimesheet;

  }

  continueToNext(val: boolean) {
    this.continueToEnd = val;
    //const cotinue = !!this.dataGrouped.length;
    this.$emit("continueToNext", val);
  }
  handleStateChanged(newState: boolean) {
    this.isMonthlyReport = newState;
  }

  dateRangeChanged(date: any) {
    if (date.length === 0) return ;
    const tz = moment.tz.guess();
    this.timeMin = moment(date[0]._d).tz(tz).startOf("day").toISOString();
    this.timeMax = moment(date[1]._d).tz(tz).endOf("day").toISOString();
    this.timeMinString = moment(date[0]._d).tz(tz).endOf("day").format("DD-MM-YYYY");
    this.timeMaxString = moment(date[1]._d).tz(tz).endOf("day").format("DD-MM-YYYY");
    this.year = Number(moment(date[1]._d).tz(tz).endOf("day").format("YYYY"));

    const newDate = moment(date[1]._d).tz(tz).endOf("day");
    newDate.locale('es');
    this.monthName = newDate.format('MMMM');
  }


  dateMonthChanged(date: any){
    const year = date.year;
    const month = date.monthIndex;

    this.year = year;


    const firstDate = moment({ year: year, month: month - 1, date: 1 });
    const endDate =  moment({ year: year, month: month - 1 }).endOf('month');

    firstDate.locale('es');
    this.monthName = firstDate.format('MMMM');

    this.timeMin = firstDate.toISOString();
    this.timeMax = endDate.toISOString();
    this.timeMinString = firstDate.format('DD-MM-YYYY');
    this.timeMaxString = endDate.format('DD-MM-YYYY');
  }

  async importCalendar() {
    const response = await axios.get("/api/get-approbed-moth/" +this["$store"].state.user.id + "&" + this.timeMinString + '&' + this.timeMaxString);
    if (response.data == 'Dato') {
      const confirmError = Modal.error;
      confirmError({
        title: "Atención !!!",
        content: "No se puede cargar un mes ya finalizado, solicite permiso para volver a cargar",
        okText: "Aceptar",
      });
      return;
    }
    this.itemsOverlappings = [];
    if (this.tokenVersion) {
      if(this.error){
        const confirm = Modal.error;
        confirm({
          title: "Configuración del token",
          content: this.errorMessage,
          okText: "Aceptar",
        });
        return;
      }
      await this.tokenMethodImport();
    } else {
      await this.modalPermissionMethodImport();
    }
  }

  async tokenMethodImport() {
    await this.getCalendars();
  }

  async getCalendars() {
    const response = await axios.get("/api/google-calendar/calendars");
    let calendars = [];

    response.data.forEach((group) => {
      const filters = group.data
          .filter((calendar) => calendar.active)
          .map((calendar) => {
            return {
              calendar: calendar.calendar,
              primary: group.primary.calendar,
              name: calendar.summary,
              token: calendar.access_token,
            };
          });
      calendars = [...calendars, ...filters];
    });

    this.processCalendarWithToken(calendars);
  }

  processCalendarWithToken(calendars) {
    this.isLoading = true;
    this.calendarsNumber = calendars.length;
    this.calendarNumbersProcessed = 0;
    this.infoProjects = [];
    calendars.forEach((calendar) => this.getListEvents(calendar));
    this.payloadVariable = calendars;

  }

  refreshCalendar(data, index){
    this.invalidCodes = false;
    this.loadingItem = true;
    const objetoEncontrado = (this.payloadVariable).find(function(elemento) {
      return elemento.calendar === data.email;
    });

    const payload = {
      startDate: this.timeMinString,
      endDate:  this.timeMaxString,
      ...objetoEncontrado,
    };

    axios.post("/api/logs", {
      text: "Click importar Registros con token token",
      description: JSON.stringify(payload),
    });

    this.dataGrouped[index].items = null;
    this.dataGrouped[index].total = 0;

for (let i = (this.infoProjects).length - 1; i >= 0; i--) {
  if (this.infoProjects[i].email === data.email) {
    (this.infoProjects).splice(i, 1);
  }
}
    axios.post("/api/google-calendar/list-events", payload).then((response) => {
      this.infoProjects = [...this.infoProjects, ...response.data];
      this.calendarNumbersProcessed += 1;
    });
  }

  getListEvents(calendar) {
    const payload = {
      startDate: this.timeMinString,
      endDate:  this.timeMaxString,
      ...calendar,
    };

    axios.post("/api/logs", {
      text: "Click importar Registros con token token",
      description: JSON.stringify(payload),
    });

    axios.post("/api/google-calendar/list-events", payload).then((response) => {
      this.infoProjects = [...this.infoProjects, ...response.data];
      this.calendarNumbersProcessed += 1;
    });
  }

  @Watch("calendarNumbersProcessed")
  onPropertyChanged(value: number) {
    if (value === this.calendarsNumber) {

      this.isLoading = false;
      this.groupedItem();
      //this.$store.commit("SET_DATA", this.infoProjects);
      this.calculateTotalHours();
      for (let i= 0; i < this.dataGrouped.length; i++){
        const repeatedEvents = this.checkEventsRepeated(this.dataGrouped[i].items);
        this.dataGrouped[i].repeatedEvents = repeatedEvents;
        if (!this.repeatedEvents) this.repeatedEvents = repeatedEvents;

        const invalidCodes = this.checkEventsWithCodesNonExistent(this.dataGrouped[i].items);
        this.dataGrouped[i].invalidCodes = invalidCodes;
        if (!this.invalidCodes) this.invalidCodes = invalidCodes;

        // const invalidCodesCaracter = this.checkEventsWithCodesNonCaracter(this.dataGrouped[i].items);
        // this.dataGrouped[i].invalidCodesCaracter = invalidCodesCaracter;
        // if (!this.invalidCodesCaracter) this.invalidCodesCaracter = invalidCodesCaracter;

        const weekendEvents = this.checkWeekendEvents(this.dataGrouped[i].items);
        this.dataGrouped[i].weekendEvents = weekendEvents;
        if (!this.weekendEvents) this.weekendEvents = weekendEvents;
      }

      this.checkOverlappingEvents();

      const eventFilteres = this.infoProjects.filter((event: any) => {
        return this.codes.includes(event.project) === true;
      });

      const eventsNotInCodes = this.infoProjects
      .filter(project => !this.codes.includes(project.project))
      .map(event => ({
        calendar: event.calendar,
        dateEnd: event.dateEnd,
        dateStart: event.dateStart,
        detail: event.project + ' ' + event.detail,
        email: event.email,
        hours: event.hours,
        project: "NR"
      }));

      this.$store.commit("SET_DATA", eventFilteres);
      this.$store.commit("SET_DATA_NOT", eventsNotInCodes);

      this.importedData = true;
      this.$emit("continueToNextProcesedata", true);

    }else{

      this.isLoading = false;
      this.groupedItem();
      //this.$store.commit("SET_DATA", this.infoProjects);
      this.calculateTotalHours();
      for (let i= 0; i < this.dataGrouped.length; i++){
        const repeatedEvents = this.checkEventsRepeated(this.dataGrouped[i].items);
        this.dataGrouped[i].repeatedEvents = repeatedEvents;
        if (!this.repeatedEvents) this.repeatedEvents = repeatedEvents;

        const invalidCodes = this.checkEventsWithCodesNonExistent(this.dataGrouped[i].items);
        this.dataGrouped[i].invalidCodes = invalidCodes;
        if (!this.invalidCodes) this.invalidCodes = invalidCodes;

        // const invalidCodesCaracter = this.checkEventsWithCodesNonCaracter(this.dataGrouped[i].items);
        // this.dataGrouped[i].invalidCodesCaracter = invalidCodesCaracter;
        // if (!this.invalidCodesCaracter) this.invalidCodesCaracter = invalidCodesCaracter;

        const weekendEvents = this.checkWeekendEvents(this.dataGrouped[i].items);
        this.dataGrouped[i].weekendEvents = weekendEvents;
        if (!this.weekendEvents) this.weekendEvents = weekendEvents;
      }

      this.checkOverlappingEvents();

      const eventFilteres = this.infoProjects.filter((event: any) => {
        return this.codes.includes(event.project) === true;
      });

      const eventsNotInCodes = this.infoProjects
      .filter(project => !this.codes.includes(project.project))
      .map(event => ({
        calendar: event.calendar,
        dateEnd: event.dateEnd,
        dateStart: event.dateStart,
        detail: event.project + ' ' + event.detail,
        email: event.email,
        hours: event.hours,
        project: "NR"
      }));

      this.$store.commit("SET_DATA", eventFilteres);
      this.$store.commit("SET_DATA_NOT", eventsNotInCodes);

      this.importedData = true;
      this.loadingItem = false;
      this.$emit("continueToNextProcesedata", true);
    }
  }

  checkOverlappingEvents() {
    for (let i = 0; i < this.dataGrouped.length; i++) {
        this.findOverlappingItems(i)
    }
  }



 findOverlappingItems(index: number) {
   const items1 = this.dataGrouped[index].items;
   for (let i = 0; i < this.dataGrouped.length; i++) {
        if (i <= index) continue;
        const items2 = this.dataGrouped[i].items;
        this.compareItems(items1, items2);
   }
 }

 compareItems(items1: any, items2: any){

   items1.forEach( (event1: any) => {
     const startDate = moment(event1.dateStart);
     const endDate = moment(event1.dateEnd);
     const find = items2.find((item: any) => {
       const start = moment(item.dateStart);
       const end = moment(item.dateEnd);
       return startDate.isBetween(start, end) || endDate.isBetween(start, end) || start.isBetween(startDate, endDate) || end.isBetween(startDate, endDate);
     });

     if (find){
       this.itemsOverlappings.push(find);
       this.itemsOverlappings.push(event1);
     }
   });

 }

  async modalPermissionMethodImport() {
    const description = {
      startDate: this.timeMinString,
      endDate: this.timeMaxString,
    };
    await axios.post("/api/logs", {
      text: "Click importar Registros sin token",
      description: JSON.stringify(description),
    });

    if (this.authorized) {
      await this.handleSignOutClick();
    }

    Promise.resolve(this.api.auth2.getAuthInstance().signIn()).then(() => {
      this.authorized = true;
      this.getData();
    });
    // this.$emit("continueToNextProcesedata", true);
  }

  handleSignOutClick() {
    Promise.resolve(this.api.auth2.getAuthInstance().signOut());
  }

  async getData() {
    this.isLoading = true;
    this.isShowWeekendAlert = false;
    const response = await this.api.client.calendar.events.list({
      calendarId: "primary",
      timeMin: this.timeMin,
      timeMax: this.timeMax,
      showDeleted: false,
      singleEvents: true,
      orderBy: "startTime",
      maxResults: 20000,
    });
    this.isLoading = false;
    this.emailNow = response.result.summary;
    this.processResult(response.result.items);
    this.isLoading = false;
    this.$emit("continueToNextProcesedata", true);
    for (let i= 0; i < this.dataGrouped.length; i++){
      const repeatedEvents = this.checkEventsRepeated(this.dataGrouped[i].items);
      this.dataGrouped[i].repeatedEvents = repeatedEvents;
      if (!this.repeatedEvents) this.repeatedEvents = repeatedEvents;

      const invalidCodes = this.checkEventsWithCodesNonExistent(this.dataGrouped[i].items);
      this.dataGrouped[i].invalidCodes = invalidCodes;
      if (!this.invalidCodes) this.invalidCodes = invalidCodes;

      // const invalidCodesCaracter = this.checkEventsWithCodesNonCaracter(this.dataGrouped[i].items);
      // this.dataGrouped[i].invalidCodesCaracter = invalidCodesCaracter;
      // if (!this.invalidCodesCaracter) this.invalidCodesCaracter = invalidCodesCaracter;

      const weekendEvents = this.checkWeekendEvents(this.dataGrouped[i].items);
      this.dataGrouped[i].weekendEvents = weekendEvents;
      if (!this.weekendEvents) this.weekendEvents = weekendEvents;
    }
    //this.repeatedEvents = this.checkEventsRepeated(this.infoProjects);
    //this.invalidCodes = this.checkEventsWithCodesNonExistent(this.infoProjects);
    this.importedData = true;
    /*const confirm = Modal.success;
    confirm({
      title: "Importación de calendario",
      content: "Tus datos se han leido correctamente",
      okText: "Aceptar",
    });*/
  }

  processResult(results: any) {
    if (results.length > 0) {
      for (let i = 0; i < results.length; i++) {
        this.processEvent(results[i]);
      }
    }
    this.groupedItem();
    this.$store.commit("SET_DATA", this.infoProjects);
    this.calculateTotalHours();
  }

  checkEventsRepeated(items: any) {
    items.forEach((event: any) => {
      const startDate = moment(event.dateStart);
      const endDate = moment(event.dateEnd);
      const find = items.find((item: any) => {
        const start = moment(item.dateStart);
        const end = moment(item.dateEnd);
        return startDate.isBetween(start, end) || endDate.isBetween(start, end);
      });
      if (find) this.infoRepeatedProjects.push(find);
    });

    return !!this.infoRepeatedProjects.length;
  }

  checkWeekendEvents(items: any){
    for (let i = 0; i < items.length; i++){
      const isInWeekend = this.isInWeekend(items[i]);
      if (isInWeekend) return true;
    }
    return false;
  }

  checkEventsWithCodesNonExistent(items: any) {
    const find = items.find((event: any) => {
      return this.codes.includes(event.project) === false;
    });

    return !!find;
  }

  checkEventsWithCodesNonCaracter(items: any) {
    const find = items.find((event: any) => {
      return !event.project.includes(":");
    });
    return !!find;
    }

  processEvent(event: any) {
    try {
      const regex = /#\w+:.+/;
      if (regex.test(event.summary)) {
        const previewObject = {
          hours: 0,
          dateStart: "",
          dateEnd: "",
          project: "",
          detail: "",
          email: "",
        };

        let next = false;

        //comprobamos si el usuario es el organizador
        if (event.organizer.self !== undefined) {
          //si lo es comprobamos si hay mas invitados en el evento
          if (event.attendees !== undefined && event.attendees.length) {
            //si hay mas invitados entonces verificar que el creador lo haya aceptado
            const find = event.attendees.find((item: any) => {
              return item.responseStatus === "accepted" && item.self != undefined;
            });
            //si lo encuentra entonces poner en true
            if (find) next = true;
          } else {
            //si solo es el organizador
            next = true;
          }
        }

        if (!next) {
          //si el no es el organizador pero el acepta el evento
          if (event.attendees !== undefined && event.attendees.length) {
            event.attendees.forEach((item: any) => {
              if (item.self !== undefined && item.responseStatus == "accepted")
                next = true;
            });
          }
        }

        if (!next) return;

        //date start with hour
        let start = event.start.dateTime;
        if (!start) start = event.start.date;

        //date end with hour
        let end = event.end.dateTime;
        if (!end) end = event.end.date;

        //transform valur to moments
        const startF = moment(start);
        const endF = moment(end);
        const duration = moment.duration(endF.diff(startF));

        const detail = event.summary.substring(event.summary.indexOf(":") + 1);
        const project = event.summary.substring(1, event.summary.indexOf(":"));

        //format dates
        previewObject.dateStart = startF.format("DD/MM/YYYY HH:mm");
        previewObject.dateEnd = endF.format("DD/MM/YYYY HH:mm");
        previewObject.hours = duration.asHours();
        previewObject.detail = detail.trim();
        previewObject.project = project.trim();
        previewObject.email = this.emailNow;

        this.infoProjects.push(previewObject);
      }
    } catch (e) {
      console.log(e);
    }
  }

  groupedItem() {
    const key = "email";

    const data = this.infoProjects.reduce(function (rv, x) {
      (rv[x[key]] = rv[x[key]] || []).push(x);
      return rv;
    }, {});

    this.dataGrouped = Object.entries(data).map(function (item: any) {
      return {
        email: item[0],
        items: item[1],
        total: collect(item[1]).sum("hours"),
      };
    });
  }

  calculateTotalHours() {
    const data = collect(this.infoProjects);
    this.totalHours = this.infoProjects.length ? data.sum("hours") : 0;
  }

  removeCalendar(index: any) {
    const email = this.dataGrouped[index].email;
    this.dataGrouped.splice(index, 1);

    this.infoProjects = this.infoProjects.filter((item) => item.email != email);
    this.$store.commit("SET_DATA", this.infoProjects);

    axios.post("/api/logs", {
      text: "Click en eliminar calendario",
      description: JSON.stringify({email}),
    });


    this.calculateTotalHours();
  }

  handleClientLoad() {
    this.api.load("client:auth2", this.initClient);
  }

  async initClient() {
    await this.api.client.init({
      apiKey: this.API_KEY,
      clientId: this.CLIENT_ID,
      discoveryDocs: this.DISCOVERY_DOCS,
      scope: this.SCOPES,
    });
    this.api.auth2.getAuthInstance().isSignedIn.listen(this.statusSession);

    this.statusSession(this.api.auth2.getAuthInstance().isSignedIn.get());
  }

  statusSession(isSignedIn: any) {
    this.authorized = isSignedIn;
  }

  openDetailsModal = false;
  openDataModal(item: any){
    this.items = item.items;
    this.openDetailsModal = true;
  }

  openOverlappingModal = false;
  openModalItemsOverlapping(){
    this.openOverlappingModal = true;
  }

  closeDetailModal(){
    this.openDetailsModal = false;
  }

  dataExport() {
    const headers = ["project", "detail", "dateStart", "dateEnd", "hours"];
    const data = XLSX.utils.json_to_sheet(this.infoProjects, {
      header: headers,
    });
    data["A1"].v = "Proyecto";
    data["B1"].v = "Detalle";
    data["C1"].v = "Inicio";
    data["D1"].v = "Fin";
    data["E1"].v = "Horas";

    const userName = this["$store"].state.user.name.replace(/ /g, "_");

    const workbook = XLSX.utils.book_new();
    const filename = `ReporteParcial_de_${userName}_del_${this.timeMinString}_al_${this.timeMaxString}`;
    XLSX.utils.book_append_sheet(workbook, data, "tiempo_parcial");
    XLSX.writeFile(workbook, `${filename}.xlsx`);
  }

  isInWeekend(project) {
    const datetime = project.dateStart.split(" ");

    if (!datetime.length) return false;

    const date = datetime[0].split("/");

    if (!date.length) return false;

    const dayOfWeek = new Date(date[2], Number(date[1]) - 1, date[0]).getDay();

    return !![0, 6].includes(dayOfWeek);
  }

  checkCodeIsInCodes(code: any) {
    return !this.codes.includes(code);
  }

  checkCodeIsInCodesError(code: any) {
    return !this.codes.includes(":");
  }

  isRepeated(item: any) {
    let isRepeated = false;

    this.infoRepeatedProjects.forEach(function (info: any) {
      if (info.dateStart == item.dateStart && info.dateEnd == item.dateEnd) {
        isRepeated = true;
      }
    });

    return isRepeated;
  }

  async closeModal() {
    this.importedData = false;
    await this.$store.commit('SET_REGISTER_WEEKEND', this.showDataInWeekend);
    this.printDataTable = true;
  }



}
</script>

<template>
  <div>
    <!--- Texts -->
    <label class="step">
      Selecciona el tipo de reporte y el mes o rango de fechas con el token encendido. Posteriormente podrás consultar la lista de registros por
cada calendario importado, incidentes en las actividades registradas (errores de nomenclatura, registros traslapados, etc.), desglose del
tiempo registrado por semana y desglose del tiempo registrado en proyectos TimeSheets (si aplica):
      <!-- Para generar un informe completo, selecciona las fechas de inicio y fin del mes requerido
      (sin distinguir fin de semana o feriados). Para generar un reporte parcial, selecciona el rango de fechas deseado.
      Finalmente, agrega el calendario o calendarios deseados y click en el botón “Continuar” -->
    </label>
    <!--- Tools -->
    <div class="contenedor-stim-one">
      <!-- p-3 -->
      <div class="item-stim-one left ">
        <div class="form-group row">
          <label class="col-sm-4 col-form-label"><b>Tipo de reporte:</b></label>
          <div class="col-sm-8">
            <Rectangle :isActive="true" @state-changed="handleStateChanged" />
          </div>
        </div>
        <div class="form-group row">
          <label class="col-sm-4 col-form-label"><b>Mes/Fechas:</b></label>
          <div class="col-sm-8">
            <month-picker-input v-if="isMonthlyReport" @change="dateMonthChanged"
                                :input-pre-filled="true"
                                :default-month="month"
                                :default-year="year" lang="es"/>
            <a-range-picker v-else @change="dateRangeChanged" />
          </div>
        </div>
        <div class="form-group row">
          <label class="col-sm-4 col-form-label"><b>Token:</b></label>
          <div class="col-sm-8">
            <label class="switch" style="margin-bottom: 0;">
              <input type="checkbox" v-model="tokenVersion" />
              <div class="slider"></div>
              <div class="slider-card">
                <div class="slider-card-face slider-card-front"></div>
                <div class="slider-card-face slider-card-back"></div>
              </div>
            </label>
          </div>
        </div>
        <div class="form-group row">
          <div class="col-sm-12 padding-button-act-reg" >
            <button
                v-if="!isLoading"
                type="button"
                class="ml-2 btn btn-outline-success btn-block btn-custom-green btn-sm actions-button"
                @click="importCalendar"
            >
              Actualizar lista de registros &nbsp;&nbsp;<img src="/assets/Icons/reload.png" alt="">
            </button>
            <rotate-square2 v-if="isLoading"></rotate-square2>
          </div>
        </div>
      </div>
      <div class="item-stim-one">
        <div v-for="(item, index) in dataGrouped"  :key="index" class="p-2 calendar d-flex flex-column mb-2">
          <div class="d-flex justify-content-between w-100">
            <div style="cursor: pointer" @click="openDataModal(item)">
              <span style="font-weight: bold; color: #6c757d">Calendario {{ index + 1}}: {{ item.email }}</span>
            </div>
            <div>
              <button class="btn-action" @click="refreshCalendar(item, index)"><img src="/assets/Icons/reload.png" alt=""></button>
              <button class="btn-action" @click="removeCalendar(index)"><img src="/assets/Icons/close.png" alt=""></button>
            </div>
          </div>
          <div style="cursor: pointer" @click="openDataModal(item)">
            <h1 v-if="loadingItem">Cargando calendario</h1>
            <h1 v-else>{{ item.total }} horas</h1>
          </div>
          <div class="d-flex align-items-center" style="gap: 10px">
            <div v-if="item.invalidCodes">
              <img src="/images/alerta-roja.png" alt="" style="width: 18px; height: 18px">
              <span style="color: #fb7980; font-weight: 500">Códigos no reconocidos</span>
            </div>
            <div v-if="item.repeatedEvents">
              <img src="/images/alerta-morada.png" alt="" style="width: 18px; height: 18px">
              <span style="color: #a18ffa; font-weight: 500">Registros traslapados</span>
            </div>
          </div>
        </div>
        <div v-if="itemsOverlappings.length" @click="openModalItemsOverlapping">
          <img src="/images/alerta-morada.png" alt="" style="width: 18px; height: 18px">
          <span style="color: #a18ffa; font-weight: 500; cursor:pointer;">
            Ver registros traslapados entre calendario
          </span>
        </div>
      </div>
    </div>
    <!--- End Tools -->

    <!--- Table -->
    <div class="monthly" v-if="dataGrouped.length" :style="'max-width:'+screenWidth+';'">
      <div class="" style="color: black; font-weight: bold ">
        DESGLOSE DE TIEMPO POR SEMANA EN {{ monthName }}, {{ year }}
      </div>
      <monthly-report-user v-if="printDataTable" @continueToNext="continueToNext" />
    </div>
    <div class="monthly" v-if="!loadingItem && dataGrouped.length && hasTimesheet" :style="'max-width:'+screenWidth+';'">
      <div class="" style="color: black; font-weight: bold ">
        DESGLOSE DE TIEMPO PARA TIMESHEETS EN {{ monthName }}, {{ year }}
      </div>
      <StepTimesheet/>
    </div>
    <!--- End Table -->
    <div class="mt-2" v-if="dataGrouped.length >0 ">
      <button class="btn btn-success" @click="dataExport">Exportar</button>
    </div>

    <a-modal v-model="openDetailsModal" @ok="closeDetailModal" :width="900" :z-index="1000001">
      <div class="scrollable-table table-responsive">
        <table class="table table-bordered">
          <thead class="sticky-header">
          <tr>
            <th>Detalles</th>
            <th>Fecha de inicio</th>
            <th>Fecha de fin</th>
            <th>Proyecto</th>
            <th>Horas</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(item, index) in items" :key="index" :class="{ 'text-blue': isRepeated(item),'text-red': checkCodeIsInCodes(item.project), 'text-weekend': isInWeekend(item)}">
            <td>{{ item.detail }}</td>
            <td >{{ item.dateStart }}</td>
            <td >{{ item.dateEnd }}</td>
            <td >{{ item.project }}</td>
            <td >{{ item.hours }}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </a-modal>

    <a-modal v-model="openOverlappingModal" @ok="openDetailsModal = false" :width="900" :z-index="1000001">
      <div class="scrollable-table">
        <table class="table table-bordered">
          <thead class="sticky-header">
          <tr>
            <th>Detalles</th>
            <th>Fecha de inicio</th>
            <th>Fecha de fin</th>
            <th>Proyecto</th>
            <th>Horas</th>
            <th>Calendario</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(item, index) in itemsOverlappings" :key="index" style="border-bottom: 1px solid silver">
            <td>{{ item.detail }}</td>
            <td >{{ item.dateStart }}</td>
            <td >{{ item.dateEnd }}</td>
            <td >{{ item.project }}</td>
            <td >{{ item.hours }}</td>
            <td> {{ item.calendar }}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </a-modal>

    <a-modal v-model="importedData" title="" :footer="null" :closable="false" :width="700" :z-index="1000001">
      <div style="padding: 20px; text-align: center;" class="d-flex flex-column justify-content-center">
        <h4 style="text-align: center">Tus registros se han leído correctamente</h4>
        <div>
          <img style="margin-top: 10px" src="/images/calendar.png" alt="calendar" width="129px">
        </div>
        <div style="margin-top: 15px" v-if="(invalidCodes | weekendEvents | repeatedEvents )">
          <div style="width: 100%; height: 1px; border-bottom: 1px solid silver; margin-top: 10px" />
          <div v-if="invalidCodes" style="margin-top: 15px; gap: 5px" class="d-flex flex-row justify-content-center align-items-center">
            <img src="/images/alerta-roja.png" alt="" style="width: 18px; height: 18px">
            <span style="color: #fb7980; font-weight: 500">Se han detectado códigos no reconocidos o con error en la nomenclatura </span>
          </div>
          <div v-if="repeatedEvents" style="margin-top: 15px; gap: 5px; margin-left: -20px" class="d-flex flex-row justify-content-center align-items-center">
            <img src="/images/alerta-morada.png" alt="" style="width: 18px; height: 18px">
            <span style="color: #a18ffa; font-weight: 500">Se han detectado registros traslapados</span>
          </div>
          <div v-if="weekendEvents" style="margin-top: 15px; padding-left: 10px">
            <div class="form-check">
              <input v-model="showDataInWeekend" type="checkbox" class="form-check-input" id="exampleCheck1">
              <label style="font-weight: 500" class="form-check-label" for="exampleCheck1">Deseo agregar los registros en fines de semana</label>
            </div>
          </div>
          <div style="width: 100%; height: 1px; border-bottom: 1px solid silver; margin-top: 15px" />
        </div>
        <div class="text-center mt-4">
          <button style="background-color: #42049e; padding: 8px 12px; width: 280px; border-radius: 30px; color: white; border: 1px solid white; font-size: 1.2rem" @click="closeModal">
            Aceptar &nbsp;&nbsp;<i class="fa fa-check-circle" aria-hidden="true"></i>
          </button>
        </div>
      </div>
    </a-modal>
  </div>
</template>

<style scoped>
.scrollable-table {
  max-height: 300px; /* Ajusta la altura máxima según tus necesidades */
  overflow-y: auto;
}

div.items-import {
  gap: 16px;
}
.left{
  border: 1px solid #88C0EF;
  border-radius: 5px;
}

.right{
  border: 0 solid #b0a1fa;
}

div.calendar{
  border: 1px solid #88C0EF;
  border-radius: 5px;
  background-color: #f2f4f8;
}

th, td{
  background-color: transparent;
}

.btn-custom-green{
  border: 2px solid  #006560;
  color:  #006560;
  border-radius: 10px;
  font-weight: 600;
}

.btn-custom-green:hover{
  color: white !important;
}

.btn-action{
  background-color: transparent;
  border: 0 solid;
  cursor: pointer;
}

.monthly{
  border: 1px solid  #88C0EF;
  border-radius: 5px;
  margin-top: 10px;
  padding: 10px;
}

.spinner--rotate-square-2{
  margin: 0 auto;
}

.sticky-header {
  background-color: #f8f9fa; /* Cambia el color de fondo según tus preferencias */
  position: sticky;
  top: 0;
  z-index: 1;
}

.form-check input[type="checkbox"] {
  display: inline !important;
  width: 17px; /* Cambia el ancho según tus preferencias */
  height: 17px; /* Cambia la altura según tus preferencias */
}

.text-red{
  border: 2px solid #fb7980;
  color: #fb7980;
}

.text-blue{
  border: 2px solid #a18ffa;
  color: #a18ffa;
}

.text-weekend{
  border: 2px solid green;
  border: green;
}

.contenedor-stim-one {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.item-stim-one {
  padding: 10px;
}


.month-picker-input-container {
  width: auto !important;
}


/* Media query para cambiar a una disposición de columnas en pantallas más pequeñas */
@media (max-width: 768px) {
  .contenedor-stim-one {
    grid-template-columns: 1fr;
  }
}

@media (min-width: 520px) {
  .padding-button-act-reg {
    padding: 0 50px 0 50px;
  }
}
</style>
