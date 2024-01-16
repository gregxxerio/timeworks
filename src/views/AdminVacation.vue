<template>
  <!-- <div> -->
  <div class="container-fluid" style="
      background: #ffffff;
      padding-right: 0px !important;
      padding-left: 0px !important;
    ">
    <div class="grid-buttons">
      <div v-for="(yearA, indexya) in yearActive"  :key="indexya">
        <button type="button" @click="
        year = yearA;
        indexDetail = indexya;
        idNav = 0;
        " class="btn btn-outline-info btn-w btn-pd fw" :class="year == yearA ? 'active' : ''"> {{yearA}} </button>
      </div>
      <button type="button" @click="addNewYear(yearActive[yearActive.length - 1])"
      class="btn btn-outline-info btn-w btn-pd fw" ><i class="fa fa-plus-circle"></i> Añadir año </button>
      <!-- <button type="button" @click="
      year = yearA;
      indexDetail = indexya;
      idNav = 0;
      " class="btn btn-outline-info btn-center btn-w btn-pd fw" :class="year == yearA ? 'active' : ''"> {{yearA}} </button> -->
      <!-- <button type="button" @click="
          year = 2024;
          indexDetail = 2;
          idNav = 0;
        " class="btn btn-outline-info btn-right btn-w btn-pd fw" :class="year == 2024 ? 'active' : ''"> 2024 </button> -->
    </div>
    <br />
    <div class="grid-check">
      <div class="list-org">
        <label class="container-check fw"> Organizaciones: </label>
        <label class="container-check">C230 Consultores <input v-model="checkboxOrgCm" type="checkbox" />
          <span class="checkmark"></span>
        </label>
        <label class="container-check">Fundación IDEA <input v-model="checkboxOrgFi" type="checkbox" />
          <span class="checkmark"></span>
        </label>
        <label class="container-check">C230 Consulting <input v-model="checkboxOrgCu" type="checkbox" />
          <span class="checkmark"></span>
        </label>
        <label class="container-check">Supernova <input v-model="checkboxOrgSu" type="checkbox" />
          <span class="checkmark"></span>
        </label>
      </div>
      <div class="empty-space"></div>
    </div>
    <div class="table-responsive">
      <table class="table table-hover">
        <thead>
          <tr class="table-info-new">
            <th scope="col" class="fh" style="color: #3d70b3">Nombre del colaborador</th>
            <th scope="col" class="fh" style="color: #3d70b3">Organización</th>
            <th scope="col" class="fh" style="color: #3d70b3;width:10%;">Fecha de ingreso</th>
            <th scope="col" class="fh" style="color: #3d70b3">Días generados</th>
            <th scope="col" class="fh" style="color: #3d70b3">Vacaciones fijas</th>
            <th scope="col" class="fh" style="color: #3d70b3"> Vacaciones flexibles tomadas </th>
            <th scope="col" class="fh" style="color: #3d70b3">Días disponibles</th>
            <th width="15%" scope="col" class="fh" style="color: #3d70b3">Estatus</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="data.length == 0">
            <tr>
              <td colspan="8" style="text-align:center;">
                <div class="loadinghistory"></div>
                <h2>Cargando...</h2>
              </td>
            </tr>
          </template>
          <template v-for="(t, index) in data">
            <tr :key="t.id" class="text-al border-n" :style="t.id == idNav ? 'background: #f0f6ff;' : ''">
              <template v-if="t.organizacion != null">
                <template v-if="
                    (checkboxOrgCm == true &&
                      t.organizacion.search('C230 Consultores') >= 0) ||
                    (checkboxOrgFi == true && t.organizacion.search('IDEA') >= 0) ||
                    (checkboxOrgCu == true &&
                      t.organizacion.search('C230 Consulting') >= 0) ||
                    (checkboxOrgSu == true && t.organizacion.search('Supernova') >= 0)
                  ">
                  <td class="fw" style="padding: 0.5rem !important; display: flex; align-items: center">
                    <button v-show="t.id != idNav" class="btn btn-sm" @click="
                        idNav = t.id;
                        dataRequest = null;
                        request = [];
                        navOption = 2;
                        getRequest(t.id);
                      " style="
                        background-color: transparent !important;
                        padding: 0.25rem 0.3rem !important;
                      ">
                      <i class="fa fa-angle-down"></i>
                    </button>
                    <button v-show="t.id == idNav" class="btn btn-sm" @click="idNav = 0" style="
                        background-color: transparent !important;
                        padding: 0.25rem 0.4rem !important;
                      ">
                      <i class="fa fa-angle-up"></i>
                    </button>
                    {{ t.name }}
                  </td>
                  <td>{{ t.organizacion }}</td>
                  <td>{{ t.date_in }}</td>
                  <template v-if="t.vacationabsencesdetail.length != 0">
                    <template v-for="(detail, index) in t.vacationabsencesdetail">
                      <template v-if="detail.year == year">
                        <td style="text-align: center" :key="'one' + index" :style="(detail.active == 0) ? 'opacity:50%;':''">
                          <!-- {{detail}} -->
                          {{ parseFloat(detail.flexible_holidays) +
                        parseFloat(detail.permanent_holidays)
                          }}
                        </td>
                        <td style="text-align: center" :key="'two' + index" :style="(detail.active == 0) ? 'opacity:50%;':''">
                          {{ parseFloat(detail.permanent_holidays) }}
                        </td>
                        <td style="text-align: center" :key="'three' + index" :style="(detail.active == 0) ? 'opacity:50%;':''">
                          {{ parseFloat(detail.days_taken) }}
                        </td>
                        <td style="text-align: center" :key="'four' + index" :style="(detail.active == 0) ? 'opacity:50%;':''" class="fw">
                          {{ calculateTotalDaysTable(detail) }}
                        </td>
                      </template>
                    </template>
                  </template>
                  <template v-else>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td></td>
                  </template>
                  <td>
                    <div  class="dropdown" >
                      <button style="width: max-content" class="dropdown-toggle"  type="button" id="dropdownMenuButton" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false" :class="getClass(t)"> {{ getStatus(t) }}</button>
                      <div v-show="permission" class="dropdown-menu" aria-labelledby="dropdownMenuButton" >
                        <a class="dropdown-item" @click="activeVacations(getIdDetails(t.vacationabsencesdetail), (getIdDetailsStatus(t.vacationabsencesdetail) == 1 ? 0 : 1))" href="#">
                          {{ getIdDetailsStatus(t.vacationabsencesdetail) == 1 ? 'Desactivar Vacaciones' : 'Activar Vacaciones'}}
                        </a>
                      </div>
                    </div>
                    <!-- <button style="width: max-content" :class="getClass(t)"> {{ getStatus(t) }}</button> -->
                  </td>
                </template>
              </template>
            </tr>
            <tr v-show="t.id == idNav" :key="'child' + index" style="background: #f0f6ff">
              <template v-if="t.organizacion != null">
                <template v-if="
                    (checkboxOrgCm == true &&
                      t.organizacion.search('C230 Consultores') >= 0) ||
                    (checkboxOrgFi == true && t.organizacion.search('IDEA') >= 0) ||
                    (checkboxOrgCu == true &&
                      t.organizacion.search('C230 Consulting') >= 0) ||
                    (checkboxOrgSu == true && t.organizacion.search('Supernova') >= 0)
                  ">
                  <td colspan="8" style="border: 1px solid red">
                    <div class="col-md-12">
                      <ul class="nav nav-tabs" id="myTab" role="tablist">
                        <li class="nav-item">
                          <a class="nav-link active" @click="tab = 1" id="tab1-tab" data-toggle="tab" :href="'#tab1-'+index" role="tab" aria-controls="tab1" aria-selected="true">
                            Solicitudes
                          </a>
                        </li>
                        <li class="nav-item">
                          <a class="nav-link" @click="tab = 2" id="tab2-tab" data-toggle="tab" :href="'#tab2-'+index" role="tab" aria-controls="tab2" aria-selected="false">
                            Historial
                          </a>
                        </li>
                        <li class="nav-item">
                          <a class="nav-link" @click="tab = 3" id="tab3-tab" data-toggle="tab" :href="'#tab3-'+index" role="tab" aria-controls="tab3" aria-selected="false">
                            Personalización
                          </a>
                        </li>
                      </ul>
                      <div class="tab-content" id="myTabContent">

                        <div class="tab-pane fade show active p-2" :id="'tab1-'+index" role="tabpanel" aria-labelledby="tab1-tab">
                          <div class="col-md-8 ant-col-md-offset-2">
                            <requests :requests="t.vacationabsencesrequest" :supervisors="t.supervisors" :idDetail="getIdDetails(t.vacationabsencesdetail)"/>

                            <!-- <table class="table table-bordered custom-bg">
                              <thead>
                                <tr>
                                  <th scope="col">Tipo de ausencia</th>
                                  <th scope="col">Días solicitados</th>
                                  <th scope="col">Inicio</th>
                                  <th scope="col">Fin</th>
                                  <th scope="col">Esstatus</th>
                                </tr>
                              </thead>
                              <tbody>
                                <template v-for="(r, index) in request">
                                  <tr :key="r.id">
                                    <td style="cursor:pointer;" @click="openCursor(index)">
                                      <span v-show="request[index].open == false" >
                                        <i class="fa fa-angle-right"></i>
                                      </span>
                                      <span v-show="request[index].open == true" >
                                        <i class="fa fa-angle-down"></i>
                                      </span>
                                      {{ getTipoName(r.type) }}
                                    </td>
                                    <td>{{ r.days }}</td>
                                    <td>{{ r.date_start }}</td>
                                    <td>{{ r.date_end }}</td>
                                    <td><span :class="'btn-status-'+r.status">{{ getSingleStatus(r.status) }}</span></td>
                                  </tr>
                                  <tr :key="'child'+index" v-show="r.open">
                                    <td colspan="5">
                                      <div class="card">
                                          <div class="card-body">
                                            <div class="row">
                                              <div class="col-md-12">
                                                <h6>Comentario:</h6>
                                                <p class="square">
                                                  {{ r.comment }}
                                                </p>
                                              </div>
                                              <div class="col-md-12 mt-2" v-if="r.additional_comment">
                                                <h6>Información adicional:</h6>
                                                <p class="square">
                                                  {{ r.additional_comment }}
                                                </p>
                                              </div>
                                              <div class="col-md-12 mt-2">
                                                <div class="row">
                                                  <div class="col-md-6">
                                                    <h6>Supervisor(a): <span style="font-weight: bold">{{ getSupervisor(r)}}</span></h6>
                                                  </div>
                                                  <div class="col-md-6">
                                                    <h6>Socio(a) responsable: <span></span></h6>
                                                  </div>
                                                </div>
                                              </div>
                                              <div class="col-md-12 mt-2">
                                                <h6>Notas del socio responsable:</h6>
                                                <p class="square">
                                                  {{ r.supervisor_note }}
                                                </p>
                                              </div>
                                            </div>
                                          </div>
                                      </div>
                                    </td>
                                  </tr>
                                </template>
                              </tbody>
                            </table> -->
                          </div>
                        </div>

                        <div class="tab-pane fade p-2" :id="'tab2-'+index" role="tabpanel" aria-labelledby="tab2-tab">
                          <div class="col-md-8 ant-col-md-offset-2">
                            <vacation-history v-if="tab == 2 && t.id == idNav"  :userId="t.id" :year="year" :origin="'Admin'"></vacation-history>
                          </div>
                        </div>

                        <div class="tab-pane fade p-2" :id="'tab3-'+index" role="tabpanel" aria-labelledby="tab3-tab">
                          <vacation-personalization @loadDataGral="getDataGral" :year="year" :userId="t.id" :datein="t.date_in" :vacationabsencesdetail="getDataByYear(t.vacationabsencesdetail)"></vacation-personalization>
                          <div class="card">
                            <div class="card-body">
                              <!-- <div v-if="needActive(t.vacationabsencesdetail)" class="row d-flex align-items-center justify-content-around">
                                <div>
                                  <h5>Días Generados: <b>{{ dayGeneratedByCurrentYear(t.vacationabsencesdetail) }}</b></h5>
                                </div>
                                <div>
                                  <button @click="activeVacationByYear(t.vacationabsencesdetail)" style="border-radius: 10px; cursor: pointer; background-color: #01408D; color: white; padding: 10px 20px; border: none;">
                                    Activar Vacaciones
                                  </button>
                                </div>
                              </div> -->

                              <!-- <div v-else> -->
<!--                                <small>{{t.vacationabsencesdetail}}</small>-->
                                <!-- <div class="row">
                                  <div class="col-md-12"> -->
                                    <!-- <h5 style="color: #01408D">Días generados: <b>{{ dayGeneratedByCurrentYear(t.vacationabsencesdetail) }}</b></h5> -->
                                  <!-- </div>
                                </div>
                                <div class="row d-flex align-items-center">
                                  <div class="col-md-3">
                                    <h6>Vacaciones Fijas</h6>
                                  </div>
                                  <div class="col-md-3">
                                    <input type="text" class="form-control" :value="getPermanentHolyDaysByYear(t.vacationabsencesdetail)" readonly disabled>
                                  </div>
                                  <div class="col-md-3">
                                    <h6>Inicio de cuenta</h6>
                                  </div>
                                  <div class="col-md-3">
                                    <input type="text" class="form-control" :value="year+'-01-01'" readonly disabled>
                                  </div>
                                </div>
                                <div class="row d-flex align-items-center mt-3">
                                  <div class="col-md-3">
                                    <h6>Vacaciones Flexibles</h6>
                                  </div>
                                  <div class="col-md-3">
                                    <input type="text" class="form-control" :value="getFlexibleHolyDaysByYear(t.vacationabsencesdetail)" readonly disabled>
                                  </div>
                                  <div class="col-md-3">
                                    <h6>Vigencia</h6>
                                  </div>
                                  <div class="col-md-3">
                                    <input type="text" class="form-control" :value="getExpirationByYear(t.vacationabsencesdetail)" readonly disabled>
                                  </div>
                                </div>
                                <div class="row">
                                  <div class="col-md-12 d-flex align-items-center">
                                    <div style="cursor: pointer" v-if="editingVacation" class="btn-circle-vacation d-flex justify-content-center align-items-center mr-2">
                                      +
                                    </div>
                                    <h5 style="color: #01408D">Días de reposición</h5>
                                  </div>
                                </div>
                                <div class="row">
                                  <div class="col-md-12 mt-2 pl-4 pr-4">
                                    <div class="row pl-4 pr-4 d-flex align-items-center" style="background-color: #01408D;">
                                      <div class="col-md-4">
                                        <h6 style="color: white">Total: </h6>
                                      </div>
                                      <div class="col-md-8">
                                        <h6 style="color: white">{{ calculateReplacementDaysByYear(t.vacationabsencesdetail) }} Días</h6>
                                      </div>
                                    </div>
                                  </div>
                                </div>
                                <div class="row d-flex justify-content-end mt-2 mr-2">
                                  <button class="btn btn-vacation ml-2" @click="cancelEditVacation" v-if="editingVacation">Cancelar</button>
                                  <button class="btn btn-vacation ml-2" v-if="editingVacation">Guardar Cambios</button>
                                  <button class="btn btn-vacation ml-2" @click="editVacation" v-else>Editar</button>
                                </div>
                              </div>
                               -->
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </td>
                </template>
              </template>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
    <a-modal v-model="visible" :title="typeDayAdd == 1 ? 'Agregar días' : typeDayAdd == 2 ? 'Restar días' : ''" @ok="handleOk">
      <div v-if="!loadingModal">
        <br />
        <label>Días</label>
        <div class="input-group">
          <input type="text" class="form-control" aria-label="Text input with dropdown button" v-model="daysr" />
          <div class="input-group-append">
            <button class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false"> Tipo ... </button>
            <br />
            <div class="dropdown-menu">
              <button class="dropdown-item" @click="typeDayAdd = 1">Sumar días</button>
              <button class="dropdown-item" @click="typeDayAdd = 2">Restar días</button>
            </div>
          </div>
        </div>
        <label>Tipo </label>
        <select class="form-control" v-model="typedayadd">
          <option value="1">Reposición</option>
          <option value="2">Dias de enfermedad</option>
        </select>
        <label>Nota</label>
        <input type="text" class="form-control" v-model="noter" />
      </div>
      <div class="loading" v-show="loadingModal"></div>
    </a-modal>
    <a-modal v-model="visibleModalNewYear" width="920px" ok-text="Añadir año" @ok="handleOkModalNewYear">
      <div class="row">
        <div class="col-md-3">
          <label>Año:</label>
          <input type="text" v-model="anioNew" class="form-control">
        </div>
        <div class="col-md-3">
          <label>Días de vacaciones fijas:</label>
          <input type="text" v-model="dayFiNew" class="form-control">
        </div>
        <div class="col-md-3">
          <label>Días de vacaciones flexibles:</label>
          <input type="text" v-model="dayFlNew" class="form-control">
        </div>
        <div class="col-md-3">
          <label>Total:</label>
          <input type="text" :value="Number(dayFiNew) + Number(dayFlNew)" class="form-control" disabled>
        </div>
      </div>
      <div class="row" style="height:300px;overflow:overlay;">
        <div class="col-md-12" style="margin-top: 2%;">
          <h5>Usuarios contemplados</h5>
        </div>
        <div class="col-md-12" style="margin-top: 1%;">
          <template v-for="d in data">
            <div :key="d.id" style="display: grid;grid-template-columns: 40% 60%;">
              <div>
                <label class="container-check" >{{d.name}}
                  <input type="checkbox" v-model="d.active"/>
                  <span class="checkmark"></span>
                </label>
              </div>
              <div>
                <label><b>Ingreso:</b> {{getDateFormated(d.date_in)}}</label>
              </div>
            </div>
          </template>
        </div>
      </div>
   </a-modal>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import VacationHistory from "@/views/vacation/VacationHistory.vue";
import VacationPersonalization from "@/views/vacation/VacationPersonalization.vue";
import Requests from "@/views/Requests.vue";
import { Modal } from "ant-design-vue";
import axios from "axios";
import moment from "moment/moment";

@Component({
  components: { VacationHistory, VacationPersonalization, Requests },
})
export default class Welcome extends Vue {
  userName = "";
  check = "";
  tab = 1;
  checkboxOrgCm = true;
  checkboxOrgCu = true;
  checkboxOrgFi = true;
  checkboxOrgSu = false;

  data: Array<any> = [];
  history: any = [];
  request: Array<any> = [];
  dataRequest: any = null;

  year = moment().year();
  yeatHistory = moment().year();
  vacationabsencesreplacementdays = [];

  navOption = 1;
  idNav = 0;
  classBtnRequest: any = -1;

  edit = 0;

  initialStatusVacationFi = 0;
  initialStatusVacationFl = 0;
  initialStatusVacationDg = 0;
  initialStatusVacationGe = 0;

  initialVacationFi = 0;
  initialVacationFl = 0;

  visible = false;
  loadingRequest = false;
  loadingModal = false;
  visibleModalNewYear = false;

  typeDayAdd = 0; //1 suma, 2 resta
  detailId = 0;
  daysr = "";
  noter = "";
  typedayadd = "";

  role: any = 0;
  permission: any = false;

  dateNow: any = "";

  indexDetail: any = 0;

  editingVacation = false;
  yearActive: any = [];

  dayFiNew: any = 12;
  dayFlNew: any = 13;
  anioNew: any = 0;
  years = moment().year();

  getDateFormated(date: any){
    const dateoriginal: Date = new Date(date);
    // Obtener los componentes de la fecha (día, mes y año)
    const dia: number = dateoriginal.getDate();
    const mes: number = dateoriginal.getMonth() + 1; // Sumar 1 porque los meses van de 0 a 11
    const anio: number = dateoriginal.getFullYear();

    // Agregar ceros a la izquierda si es necesario
    const diaFormateado: string = dia < 10 ? '0' + dia : dia.toString();
    const mesFormateado: string = mes < 10 ? '0' + mes : mes.toString();

    // Crear la nueva cadena de fecha en el formato deseado
    const fechaEnNuevoFormato = `${diaFormateado}/${mesFormateado}/${anio}`;

    return fechaEnNuevoFormato;
  }

  async getYearsActives(){
    const response = await axios.get("/api/get-years-actives");
    this.yearActive = response.data;
  }

  getIdDetails(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.years ){
        return data[i].id;
      }
    }
    return 0;
  }

  getIdDetailsStatus(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.years ){
        return data[i].active;
      }
    }
    return 0;
  }

  addNewYear(year){
    this.anioNew = year + 1;
    this.visibleModalNewYear = true;
  }

  async handleOkModalNewYear(){
    const resultados = this.data
    .filter(({ active }) => active === 1)
    .map(({ id, active }) => ({ id, active }));

    // const response = await axios.get("/api/complete-new-year/" + this.dayFiNew + '&' + this.dayFlNew + '&' + this.anioNew);
    await axios.post("/api/complete-new-year/",{'dayFiNew' : this.dayFiNew, 'dayFlNew' : this.dayFlNew, 'anioNew' : this.anioNew, 'data' : resultados })

    this.getUsersActive();
    this.getYearsActives();
    this.visibleModalNewYear = false;
    this.showAlert("Registros agregados correctamente");
  }

  getStatus(item: any){
    // fecha de vacaciones es mayor a hoy y estatus 3: (Presente)
    // fecha de vacaciones es status 1 y es mayor a hoy: (Solicitud Recibida)
    // fecha de vacaciones esta entre hoy

    const dateFilter = item.vacationabsencesrequest.filter( (value: any) => {
      const fechaComparar = moment(value.date_end);
      if(fechaComparar.isSameOrAfter(moment(), "day") && value.status != 3) return value;
    });

    if(!dateFilter.length) return "Presente";

    let status = "Presente";
    item.class = "present";
    const fechaActual = moment();
    for (let i = 0; i < dateFilter.length; i++) {
        const dates = dateFilter[i];
        const dateStart = moment(dates.date_start);
        const dateEnd = moment(dates.date_end);

        if(fechaActual.isSameOrAfter(dateStart, "day") && fechaActual.isSameOrBefore(dateEnd, "day") && dates.status == 2){
          item.class = "ausent";
          return "Ausente";
        }
    }
    for (let i = 0; i < dateFilter.length; i++) {
      const dates = dateFilter[i];
      if(dates.status == 1){
        item.class = "request";
         status = " Solicitud Recibida";
         break;
      }
    }

    return status;
  }

  getClass(item: any){
    // fecha de vacaciones es mayor a hoy y estatus 3: (Presente)
    // fecha de vacaciones es status 1 y es mayor a hoy: (Solicitud Recibida)
    // fecha de vacaciones esta entre hoy
    const dateFilter = item.vacationabsencesrequest.filter( (value: any) => {
      const fechaComparar = moment(value.date_end);
      if(fechaComparar.isSameOrAfter(moment(), "day") && value.status != 3) return value;
    });

    if(!dateFilter.length) return "span-present";

    let status = "span-present";
    const fechaActual = moment();
    for (let i = 0; i < dateFilter.length; i++) {
        const dates = dateFilter[i];
        const dateStart = moment(dates.date_start);
        const dateEnd = moment(dates.date_end);

        if(fechaActual.isSameOrAfter(dateStart, "day") && fechaActual.isSameOrBefore(dateEnd, "day") && dates.status == 2){
          return "span-absent";
        }
    }

    for (let i = 0; i < dateFilter.length; i++) {
      const dates = dateFilter[i];
      if(dates.status == 1){
         status = "span-request";
         break;
      }
    }

    return status;
  }

  getDataByYear(data: any){
    for (let i = 0; i < data.length; i++){
       if (data[i].year == this.year ){
         return data[i];
       }
     }
   return null;
  }

  // needActive(data: any) {
  //   for (let i = 0; i < data.length; i++){
  //       if (!data[i].active && data[i].year == this.year ){
  //         return true;
  //       }
  //   }
  //   return false;
  // }

  // dayGeneratedByCurrentYear(data: any){
  //   for (let i = 0; i < data.length; i++){
  //     if (data[i].year == this.year ){
  //       this.vacationabsencesreplacementdays = data[i].vacationabsencesreplacementdays;
  //       return Number(data[i].flexible_holidays) - Number(data[i].days_taken);
  //     }
  //   }
  //   return  0;
  // }

  // calculateReplacementDaysByYear(data: any){
  //   for (let i = 0; i < data.length; i++){
  //     if (data[i].year == this.year ){
  //       let total =0;
  //       for (let j = 0; j < data[i].vacationabsencesreplacementdays.length; j++){
  //          if (data[i].vacationabsencesreplacementdays[j].type == 1){
  //            total += Number(data[i].vacationabsencesreplacementdays[j].days)
  //          }else if (data[i].vacationabsencesreplacementdays[j].type == 2){
  //            total -= Number(data[i].vacationabsencesreplacementdays[j].days)
  //          }
  //       }
  //       return total;
  //     }
  //   }
  //   return  0;
  // }

  getSingleStatus(status: number) {
    /*const fechaActual = moment();
    const fechaInicial = moment(item.date_start);
    const fechaFinal = moment(item.date_end);
    if(fechaActual.isSameOrAfter(fechaInicial, "day") && fechaActual.isSameOrBefore(fechaFinal, "day") && item.status == 2){
      return "Ausente";
    }
    if (item.status == 1){
      return "Solicitud Recibida"
    }
    return "Presente";*/

    if (status == 2){
      return "Aprobado";
    }
    if (status == 3) {
      return "Rechazado";
    }
    return "En Revisión";
  }

  async activeVacations(id: any, status: any){
    const request = await axios.get("/api/active-desactive-vacations/" + id + '&' + status);
    this.getDataGral();
  }
  // async activeVacationByYear(data: any){
  //   await axios.get("/api/active-vacations/" + this.year+"/user_id/"+data[0].user_id);
  //   this.getUsersActive();
  // }

  getPermanentHolyDaysByYear(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.year ){
        return Number(data[i].permanent_holidays) + " días";
      }
    }
    return  "0 días";
  }

  getFlexibleHolyDaysByYear(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.year ){
        return Number(data[i].flexible_holidays) + " días";
      }
    }
    return  "0 días";
  }

  getExpirationByYear(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.year ){
        return data[i].expiration_date;
      }
    }
    return  "0 días";
  }

  async deleteDayReplacement(number: any) {
    const request = await axios.get("/api/delete-replacement-days/" + number);
    this.getUsersActive();
  }

  async addQuitHolidaysFixed(number: any) {
    const request = await axios.get("/api/add-quit-holidays-fixed/" + number);
    this.getUsersActive();
  }

  async addQuitDaysGenerated(number: any) {
    const request = await axios.get("/api/add-quit-days-generated/" + number);
    this.getUsersActive();
  }

  async completeDays(data: any) {
    const request = await axios.get("/api/complete-days/" + data.id);
    this.getUsersActive();
  }

  setInitialStatus(data: any) {
    this.initialVacationFi = parseFloat(data[this.indexDetail]["permanent_holidays"]);
    this.initialVacationFl = parseFloat(data[this.indexDetail]["flexible_holidays"]);
  }

  fechaSince(data: any) {
    const year = new Date();
    // const date = new Date();
    // const date_in = new Date(data);
    //
    if (data > year.getFullYear() + "-04-01") {
      return [false, data];
    } else {
      return [true, year.getFullYear() + "-04-01"];
    }
  }

  getPermission(data: any) {
    const roles = JSON.parse(this["$store"].state.user.roles_time);
    const array = roles.map(function (x) {
      return x.id;
    });
    let iguales = 0;

    for (let i = 0; i < data.length; i++) {
      for (let j = 0; j < array.length; j++) {
        if (data[i] == array[j]) iguales++;
      }
    }
    if (iguales > 0) {
      this.permission = true;
    } else {
      this.permission = false;
    }
  }

  calculateTotalDaysTable(detail: any) {
    return  parseFloat(detail.flexible_holidays) - parseFloat(detail.days_taken) ;
  }
  calculateTotalDays(detail: any) {
    const total = parseFloat(detail.flexible_holidays) - parseFloat(detail.days_taken);
    // if (
    //   // eslint-disable-next-line no-prototype-builtins
    //   detail.hasOwnProperty("vacationabsencesreplacementdays") &&
    //   Array.isArray(detail.vacationabsencesreplacementdays)
    // ) {
    //   detail.vacationabsencesreplacementdays.forEach((item: any) => {
    //     if (item.type == 1) {
    //       total += parseFloat(item.days);
    //     } else if (item.type == 2) {
    //       total -= parseFloat(item.days);
    //     }
    //   });
    // } else {
    //   console.log("La propiedad no existe o no es un array");
    //   console.log(detail);
    // }
    //
    // if(total > 15){
    //   console.log("detail");
    //   console.log(detail);
    // }

    return total;
  }

  async handleOk() {
    if (this.noter == "") {
      this.showAlert("Se necesita registrar un comentario");
      return;
    }
    if (parseFloat(this.daysr) <= 0) {
      this.showAlert("Los días no pueden ser menor o igual a 0");
      return;
    }
    if (this.typeDayAdd == 0) {
      this.showAlert("Seleccione el tipo de operación");
      return;
    }
    if (isNaN(parseFloat(this.daysr))) {
      this.showAlert("Solo se permiten valores numericos para el numero de días");
      return;
    }
    this.loadingModal = true;
    const result: any = await axios.post("/api/save-detail-holiday", {
      id: this.detailId,
      type: this.typeDayAdd,
      day: this.daysr,
      note: this.noter,
      typeday: this.typedayadd,
    });

    if (result.data.status) {
      this.getUsersActive();
      this.visible = false;
      this.daysr = "";
      this.noter = "";
      this.typedayadd = "";
      this.loadingModal = false;
    } else {
      this.showAlert(result.data.error);
      this.loadingModal = false;
    }
  }

  async getRequest(id: any) {
    this.getPermission([2, 4, 5]);
    this.loadingRequest = true;
    const request = await axios.get("/api/user-request-vacation/" + id + "&" + this.year);
    this.request = [];
    request.data.forEach( (item: any) => {
      item.open = false;
      this.request.push(item);
    });
    this.loadingRequest = false;
  }

  async getHistory(id: any) {
    const history = await axios.get(
      "/api/user-history-vacation/" + id + "&" + this.yeatHistory
    );
    this.history = history.data;
  }

  async getUsersActive() {
    this.data = [];
    const response = await axios.get("/api/get-list-users-active");
    this.data = response.data;
  }

  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title,
      content: "",
      okText: "Aceptar",
    });
  }

  async requestHoliday(status: any) {
    if (
      this.dataRequest.supervisor_note == null ||
      this.dataRequest.supervisor_note == ""
    ) {
      this.showAlert("El campo Notas de supervisor(a) es requerido");
      return;
    }
    this.loadingRequest = true;
    const result: any = await axios.post("/api/admin-request-holiday", {
      id: this.dataRequest.id,
      supervisorNote: this.dataRequest.supervisor_note,
      status: status,
    });
    if (result.data) {
      this.dataRequest = result.data.data;
      this.loadingRequest = false;
    }
    const success = Modal.success;
    this.getUsersActive();
    success({
      title: status == 2 ? "Aprobado" : status == 3 ? "Rechazado" : "",
      content: "",
      okText: "Aceptar",
    });
  }

  editVacation(){
    this.editingVacation = true;
  }

  cancelEditVacation(){
    this.editingVacation = false;
  }

  addNewRow(){
    const item = {

    };
  }

  async saveDaysUpdate(data: any) {
    this.loadingRequest = true;
    const result: any = await axios.post("/api/save-days-update", data[this.indexDetail]);
    if (result.data) {
      this.loadingRequest = false;
      this.edit = 0;
    }
    const success = Modal.success;
    success({
      title: "información actualizada",
      content: "",
      okText: "Aceptar",
    });
  }

  getDataGral() {
    this.userName = this.$store.state.user.name;
    this.getPermission([2]);
    this.check = this.$store.state.user.pronombre;
    this.getUsersActive();
    this.getYearsActives();
    this.role = this.$store.state.user.role_time_id;
    this.dateNow = new Date().toISOString().slice(0, 10);
    if (this.year == 2022) {
      this.indexDetail = 0;
    } else if (this.year == 2023) {
      this.indexDetail = 1;
    } else if (this.year == 2024) {
      this.indexDetail = 2;
    }
  }

  async mounted() {
    this.getDataGral();
    // this.getPermission();
  }

  /*getStatusName(status: number ) {

  }*/

  getTipoName(tipo: number) {
    switch (tipo) {
      case 1:
        return "Vacaciones";
      case 2:
        return "Vacaciones (medio turno)";
      case 3:
        return "Enfermedad";
      case 4:
        return "Otro";
      case 5:
        return "Días no pagados";
      default:
        return "";
    }
  }

  openCursor(index: number){
    this.request[index].open = !this.request[index].open;
  }

  getSupervisor(item: any){
    const data = JSON.parse(item.supervisor_id);
    if (data.length){
      return data[0].name;
    }
    return "";
  }
}
</script>
<style media="screen">
.custom-h5 {
  font-size: 2em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  font-weight: 600;
}

.fh {
  font-size: 0.9em;
}

.table-info-new {
  background: #f4f7f9;
}

.grid-inputs-one-admin {
  display: grid;
  grid-template-columns: 25% 25% 50%;
  gap: 5px;
}
</style>
<style media="screen">
.btn-w-request {
  width: 90%;
  font-size: 10px;
}
.list-org {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.grid-check {
  display: grid;
  grid-template-columns: 80% 20%;
}

@media (max-width: 655px) {
  .list-org {
    display: grid;
    justify-content: left;
  }
}

.container-check {
  display: block;
  position: relative;
  padding-left: 35px;
  margin-bottom: 12px;
  cursor: pointer;
  /* font-size: 22px; */
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
/* Hide the browser's default checkbox */
.container-check input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 15px;
  width: 15px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.container-check:hover input ~ .checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.container-check input:checked ~ .checkmark {
  background-color: #2196f3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.container-check input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container-check .checkmark:after {
  left: 5px;
  top: 2px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 1px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

.grid-dates {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.5%;
  border-top: 1px solid #000;
  border-bottom: 1px solid #000;
  margin-top: 1%;
}

.card-days {
  text-align: left;
  display: grid;
  grid-template-columns: 10% 90%;
  height: min-content;
  border-top: 4px solid #6236ff;
  margin-top: 4%;
}

.card-days-two {
  border-top: 4px solid #6236ff;
  margin-top: 4%;
  text-align: left;
  display: grid;
  grid-template-columns: 78% 15%;
  height: min-content;
}

.card-days-three {
  text-align: left;
  display: grid;
  grid-template-columns: 1fr;
  height: min-content;
  border-top: 4px solid #6236ff;
  margin-top: 4%;
}

.card-three-sub-one {
  text-align: left;
  display: grid;
  grid-template-columns: 10% 90%;
  height: min-content;
}

.card-three-sub-four {
  text-align: left;
  display: grid;
  grid-template-columns: 90% 10%;
  height: min-content;
}

.card-three-sub-two {
  text-align: left;
  display: grid;
  grid-template-columns: 8% 92%;
  height: min-content;
  background: #ebe8f6;
}

.card-three-sub-two-second {
  text-align: left;
  display: grid;
  grid-template-columns: 8% 92%;
  height: min-content;
  background: #ebe8f6;
}

.card-three-sub-three {
  text-align: left;
  display: grid;
  grid-template-columns: 6% 94%;
  height: min-content;
  background: #ebe8f6;
}

.text-footer {
  display: flex;
  flex-direction: column;
  /* justify-content: flex-end; */
  align-items: flex-end;
}

.m0 {
  margin-bottom: 0;
  margin-top: 2%;
  font-size: 1em;
}

.mtb0 {
  margin-bottom: 0;
  margin-top: 0;
}

.color-black {
  color: #000000;
}
/* Stule in contetn */

span.square {
  background: transparent;
  border-radius: 5px;
  border-bottom: 1px solid #fff;
  display: inline-block;
  height: 10px;
  width: 10px;
  cursor: pointer;
}
.arrow {
  position: relative;
}
.arrow.up:after {
  position: absolute;
  top: 7px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent #000000 transparent;
  border-radius: 2px;
}

.arrow.up:before {
  position: absolute;
  top: 11px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

.arrow.down:after {
  position: absolute;
  top: 10px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: #000000 transparent transparent transparent;
  border-radius: 2px;
}

.arrow.down:before {
  position: absolute;
  top: 12px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

/* style gral */
span.square-gral {
  background: transparent;
  border-radius: 4px;
  border-bottom: 1px solid transparent;
  display: inline-block;
  height: 8px;
  width: 8px;
  cursor: pointer;
}
.arrow-gral {
  position: relative;
}
.arrow-gral.up-gral:after {
  position: absolute;
  top: 5px;
  left: 5px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent rgb(61, 112, 179) transparent;
  border-radius: 2px;
}

.arrow-gral.up-gral:before {
  position: absolute;
  top: 9px;
  left: 5px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

.arrow-gral.down-gral:after {
  position: absolute;
  top: 8px;
  left: 5px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: rgb(61, 112, 179) transparent transparent transparent;
  border-radius: 2px;
}

.arrow-gral.down-gral:before {
  position: absolute;
  top: 10px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

.grid-buttons-up-down {
  display: flex;
  flex-direction: column;
}

.grid-buttons-plus {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.btn-plus {
  border-radius: 80%;
  height: 80%;
  border: 1px solid black;
  background: transparent;
  padding: 0;
  padding-left: 12%;
  padding-right: 12%;
  padding-bottom: 2%;
  padding-top: 2%;
  margin-left: 4%;
  font-size: 4rem;
}

.circle {
  width: 60%;
  height: 60%;
  border: 1px solid #000;
  border-radius: 100%;
  -moz-border-radius: 100%;
  -webkit-border-radius: 100%;
  text-align: center;
  font: small-caption;
  font-weight: bold;
  cursor: pointer;
}

/*  */
.loadinghistory {
  display: inline-block;
  width: 80px;
  height: 80px;
  border: 5px solid rgb(26 108 97 / 56%);
  border-radius: 50%;
  border-top-color: #fff;
  animation: spinh 1s ease-in-out infinite;
  -webkit-animation: spinh 1s ease-in-out infinite;
  position: inherit;
  top: 50%;
  left: 56%;
}

@keyframes spinh {
  to {
    -webkit-transform: rotate(360deg);
  }
}

@-webkit-keyframes spinh {
  to {
    -webkit-transform: rotate(360deg);
  }
}

/* Span buttons */
.span-present {
  border: 1px solid #accf60;
  padding-top: 1.2%;
  padding-bottom: 1.2%;
  padding-left: 6%;
  padding-right: 6%;
  border-radius: 20px;
  color: #accf60;
  background: #f4f8e9;
}

.span-absent {
  border: 1px solid #b0a1fa;
  padding-top: 1.2%;
  padding-bottom: 1.2%;
  padding-left: 6%;
  padding-right: 6%;
  border-radius: 20px;
  color: #836bf9;
  background: #f2f0fd ;
}

.span-request {
  border: 1px solid #db7e97;
  padding-top: 1.2%;
  padding-bottom: 1.2%;
  padding-left: 6%;
  padding-right: 6%;
  border-radius: 20px;
  color: #d63776;
  background: #f9e9ec;
}

.span-revision {
  border: 1px solid #c0c1c2;
  padding-top: 1.2%;
  padding-bottom: 1.2%;
  padding-left: 6%;
  padding-right: 6%;
  border-radius: 20px;
  color: #989da5;
  background: #eef0f2;
}

.span-finalizado {
  border: 1px solid #b0a1fa;
  padding-top: 1.2%;
  padding-bottom: 1.2%;
  padding-left: 6%;
  padding-right: 6%;
  border-radius: 20px;
  color: #836bf9;
  background: #f2f0fd;
}

.buttons-grid-sr {
  border: 1px solid #6236ff;
  text-align: center;
  align-self: end;
  background: #6236ff;
  border-radius: 15px;
  font-size: 14px;
  font-weight: 800;
  color: white;
  cursor: pointer;
  margin: 0 0px 0 -30%;
}

.button-options {

    margin-right: 2%;
    height: 30px;
    align-self: initial;
    padding-bottom: 9%;
}

.custom-bg th {
  background-color: #A3C9FF;
  color: #3d70b3/* Este es el color azul cielo */
}
.custom-bg td {
  font-weight: bold;
}

.custom-bg tbody tr {
  background-color: #f0f6ff; /* Este es el color azul cielo */
}

.btn-status-1 {
  border: 1px solid gray;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: gray;
  background: #E5E5E5;
}
.btn-status-2{
  border: 1px solid #accf60;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #accf60;
  background: #f4f8e9;
}
.btn-status-3{
  border: 1px solid #db7e97;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #d63776;
  background: #f9e9ec;
}

p.square {
  border: 1px solid silver;
  padding: 5px;
  font-size: 1em;
}

.btn-vacation{
  border-radius: 20px;
  background-color: #3C018D;
  color: white;
  padding: 5px 20px;
  border: none;
}

.btn-circle-vacation{
  border-radius: 50px;
  background-color: #01408D;
  color: white;
  width: 25px;
  height: 25px;
  font-size: 18px;
  font-weight: bold;
  padding: 0;
  line-height: 0px;
}
.table thead th {
    font-size: 0.85em;
}
.table td, .table th {
    padding: 1% 0.5%;
}


.checkbox-container {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

input[type="checkbox"] {
  appearance: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  width: 20px;
  height: 20px;
  border: 2px solid #333;
  border-radius: 4px;
  margin-right: 8px;
  cursor: pointer;
}

input[type="checkbox"]:checked {
  background-color: #4CAF50;
  border-color: #4CAF50;
}

label {
  cursor: pointer;
}
</style>
