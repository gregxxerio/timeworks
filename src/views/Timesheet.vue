<template>
  <div class="container">
    <div class="row p-2 d-flex justify-content-between align-items-center">
      <div class="d-flex align-items-center ml-2 mt-1">
        <span class="mr-1">Actividades: </span>
        <v-select v-model="projectSelected" taggable label="code" :options="projects" placeholder="Selecciona actividad"/>
      </div>
      <div class="d-flex align-items-center ml-3 mt-1">
        <span class="mr-1">Mes:</span>
        <month-picker-input @change="onMonthChanged"  :input-pre-filled="true" :default-month="month" :default-year="year" lang="es"></month-picker-input>
      </div>
      <div class="d-flex align-items-center ml-3 mt-1">
        <button v-if="!loading" class="btn-apply" @click.prevent="onApplyClick">Aplicar</button>
        <rotate-square2 size="20px" v-else></rotate-square2>
      </div>
    </div>
    <div class="row p-1 mt-3">
      <div class="table-responsive">
        <table class="table">
          <thead style="background-color: #f4f7f9">
          <tr>
            <th scope="col">Nombre del colaborador</th>
            <th scope="col">Horas registradas</th>
            <th scope="col">Porcentaje registrado</th>
            <th scope="col">Porcentaje previsto</th>
            <th scope="col">Fecha/Hora carga</th>
            <th scope="col">Estatus</th>
            <th scope="col">Excel</th>
            <th scope="col">Pdf</th>
          </tr>
          </thead>
          <tbody>
            <template v-for="(item, index) in data" >
              <tr :key="index" style="border-bottom:none;">
                <td style="text-align: left;font-weight: bold; ">{{ item.user.name }}</td>
                <td style="text-align: left; font-weight: bold;">{{ (item.totalHoursR).toFixed(2) }}</td>
                <td style="text-align: left; font-weight: bold;">{{ (item.percentajeRegistered).toFixed(2) + '%' }}</td>
                <td style="text-align: left; font-weight: bold;">{{ (item.percentage).toFixed(2) + '%' }}</td>
                <td  style="text-align: left; font-weight: bold;">{{item.created_at == null ? '' : (item.created_at.created_at).substring(0, 16)}}</td>
                <td style="text-align: left; font-weight: bold;">
                  <img v-if="item.totalHours >= item.percentajeRegistered" src="/images/ok.png" alt="Ok">
                  <img v-else src="/images/danger.png" alt="Danger">
                </td>
                <td style="text-align: left;">
                  <img style="cursor: pointer" @click="downloadXML(item, index)" src="/images/Excel.png" v-if="!data[index].loading" alt="Descargar Excel">
                  <rotate-square2
                  v-if="data[index].loading"
                    size="20px"
                  ></rotate-square2>
                </td>
                <td style="text-align: left;">
                  <img style="cursor: pointer" @click="generatePdf(item, index)" src="/images/PDF.png" v-if="!data[index].loading" alt="Descargar Excel">
                  <rotate-square2
                  v-if="data[index].loading"
                    size="20px"
                  ></rotate-square2>
                </td>
              </tr>
              <tr :key="index +'one'" style="border: none;">
                <td style="text-align: left;padding-left: 3%; ">Horas laboradas </td>
                <td style="text-align: left;">{{item.totalHours}}</td>
                <td style="text-align: left;">{{ ((parseFloat(item.totalHours) / parseFloat(item.totalHoursR) ) * parseFloat(item.percentajeRegistered)).toFixed(2) +'%' }}</td>
                <!-- <td>{{item.hours}}</td> -->
                <!-- <td>{{ ((item.hours / (item.hours + ((item.percentageTotal / 100) * data.horasNoLaborado)) ) * item.percentageTotal).toFixed(2)  }}%</td> -->
                <td style="text-align: left;">-</td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
              </tr>
              <tr :key="index +'two'">
                <td style="text-align: left;padding-left: 3%; ">Horas no laboradas
                </td>
                <td style="text-align: left;">{{(item.horasNoLaborado).toFixed(2)}}</td>
                <td style="text-align: left;">{{ ((parseFloat(item.horasNoLaborado) / parseFloat(item.totalHoursR) ) * parseFloat(item.percentajeRegistered)).toFixed(2) +'%' }}</td>
                <!-- <td>{{((item.percentageTotal / 100) * data.horasNoLaborado)}}</td> -->
                <!-- <td>{{((((item.percentageTotal / 100) * data.horasNoLaborado) / (item.hours + ((item.percentageTotal / 100) * data.horasNoLaborado)) ) * item.percentageTotal).toFixed(2)}}%</td> -->
                <td style="text-align: left;">-</td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
              </tr>
            </template>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import vSelect from "vue-select";
import {Component, Vue} from "vue-property-decorator";
import {RotateSquare2} from "vue-loading-spinner";
import {MonthPickerInput} from "vue-month-picker";
import {Chart} from "highcharts-vue";
import { Modal } from 'ant-design-vue';
import Loading from "vue-loading-overlay";
import moment from "moment";
import axios from "axios";
import XLSX from "xlsx";

@Component({
  components:{
    RotateSquare2, MonthPickerInput, vSelect, Chart, Loading
  }
})
export default class Timesheet extends Vue {
  projects: any = [];
  projectSelected: any = null;
  month = moment().month();
  year =  moment().year();
  data: any = [];
  datahorasNoLaborado: any = '';
  totalHoursR: any = 0;
  porcentajePrevisto: any = 0;
  porcentajeRegistrado: any = 0;
  loading = false;

  async created(){
    const response = await axios.get('/api/projects/names');
    this.projects = response.data.projects
  }

  async generatePdf(data: any, index: any){
    //window.open(`${endpoint.api.domain}/api/chart?month=${this.month}&year=${this.year}&userId=${this.data[index].id}`, '_blank');
    const params = {
      "month" : this.month + 1,
      "year" : this.year,
      "userId" : data.user.id
    }
    this.data[index].loading =  true;
    try {
      const response = await axios.post('/api/user-registers-report', params);
      this.data[index].loading =  false;
      if (response.data.success){
        window.open(response.data.path, '_blank');
      }else{
        const success = Modal.error;
        success({
          title: "No se ha podido generar el pdf correctamente" ,
          content: '',
          okText: 'Aceptar',
        });
      }
    }catch (e) {
      this.data[index].loading =  false;
      const success = Modal.error;
      success({
        title: "No se ha podido generar el pdf correctamente" ,
        content: '',
        okText: 'Aceptar',
      });
    }

  }

  onMonthChanged (date: any) {
    this.year = date.year;
    this.month = date.monthIndex - 1;
  }

  async onApplyClick(){
    if (!!this.projects == false){
      alert("Necesita selecciona un projecto")
      return
    }
    const data = {
      ...this.projectSelected,
      year: this.year,
      month: this.month  + 1
    }
    this.loading = true
    const response = await axios.post('/api/projects/timesheet', data);
    this.data = response.data;

    this.loading = false
  }


  downloadXML(item: any, index: any) {
    this.data[index].loading =  true;

    const anex = [
      {'project':'', 'detail':'', 'hours':'',  'start_date':''},
      {'project':'', 'detail':'', 'hours':'',  'start_date':''},
      {'project':'Total de horas no laboradas', 'detail':'', 'hours':'',  'start_date':''}];

    const horasNoLaborado = [];

    const porcentaje = (parseFloat(this.data[index].percentajeRegistered)).toFixed(2);
    (item.dataUnlabored).forEach(element => {

        horasNoLaborado.push({
          'project':element.project,
          'detail': element.detail,
          'hours': ((parseFloat(porcentaje) * parseFloat(element.hours)) / 100).toFixed(2),
          'start_date': element.start_date});
    });

    const final = ((item.data).concat(anex)).concat(horasNoLaborado);

    const headers = ['project', 'detail', 'hours',  'start_date'];
    const excel = XLSX.utils.json_to_sheet(final, {
      header: headers
    })

    excel['A1'].v = 'Proyecto'
    excel['B1'].v = 'Detalle'
    excel['C1'].v = 'Horas'
    excel['D1'].v = 'Fecha'

    const workbook = XLSX.utils.book_new()
    const workbookNname = "registros";
    const userName = item.user.name;
    XLSX.utils.book_append_sheet(workbook, excel, workbookNname);
    const fileName = `${this.year}_${this.month}_${userName}.xlsx`;
    XLSX.writeFile(workbook, fileName);
    this.data[index].loading =  false;

  }


}
</script>

<style scoped>
  .btn-apply{
    color: white;
    background-color: #310981;
    border-radius: 25px;
    padding: 5px 20px 5px;
    border: 0px;
    cursor: pointer;
  }

  .btn-apply:active{
    border: 0px;
    opacity: 0.8;
  }

  .table-responsive{
    border: 1px solid #fffffe;
  }

  table {
    border-collapse: collapse;
  }

  tr {
    border-bottom: 1pt solid #cccccc;
  }
</style>
