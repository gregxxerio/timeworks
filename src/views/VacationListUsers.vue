<template>
  <div class="container-home-dos" style="background: #FFFFFF;">
    <div v-if="loadingRequestListUser" style="text-align: center;">
      <div class="loadinghistory">
      </div>
      <h2>Cargando...</h2>
    </div>
    <div class="table-responsive" v-else>
      <table>
        <thead>
          <tr class="table-info">
            <th width="25%" scope="col" class="fh" style="color: #3d70b3;">Nombre del colaborador<br>Cargo</th>
            <th width="20%" scope="col" class="fh" style="color: #3d70b3;">Supervisora o supervisor</th>
            <th scope="col" class="fh" style="color: #3d70b3;">Organización </th>
            <th width="14%" scope="col" class="fh" style="color: #3d70b3;">Fecha de ingreso</th>
            <th scope="col" class="fh" style="color: #3d70b3;">Vacaciones flexibles tomadas</th>
            <th scope="col" class="fh" style="color: #3d70b3;">Días disponibles</th>
            <th width="25%" scope="col" class="fh" style="color: #3d70b3;">Estatus</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(d, indexPrincipal) in data">
            <tr :key="d.id" :style="idNav == d.id ? 'background: #f0f6ff;' : ''">
              <td class="fw" style="padding:0.5rem !important;display: flex;align-items: center;">
                <button v-show="d.id != idNav" @click="idNav = d.id;fixedNavPills = 1;" class="btn btn-sm"
                style="background-color: transparent !important;padding: .25rem .3rem !important;">
                <i class="fa fa-caret-right"></i></button>
                <button v-show="d.id == idNav" @click="idNav = 0;" class="btn btn-sm"
                style="background-color: transparent !important;padding: .25rem .4rem !important;">
                <i class="fa fa-caret-down"></i></button>
                {{d.name}}
              </td>
              <td v-for="s in d.supervisors"  :key="s.id">
                {{s.name}}<br>
              </td>
              <td>{{d.organizacion}}</td>
              <td>{{d.date_in}}</td>
              <td>{{Number(getDaysTaken(d.vacationabsencesdetail))}}</td>
              <td>{{Number(getDaysAvailable(d.vacationabsencesdetail))}}</td>
              <td>
                <template v-if="getStatus(d.vacationabsencesrequest, getIdDetails(d.vacationabsencesdetail)) == 'presente'">
                  <span class="span-present">Presente</span>
                </template>
                <template v-if="getStatus(d.vacationabsencesrequest, getIdDetails(d.vacationabsencesdetail)) == 'ausente'">
                  <span class="span-absent">Ausente</span>
                </template>
                <template v-if="getStatus(d.vacationabsencesrequest, getIdDetails(d.vacationabsencesdetail)) == 'recibida'">
                  <span class="span-request">Solicitud Recibida</span>
                </template>
              </td>
            </tr>
            <tr v-show="d.id == idNav" :key="'child' + indexPrincipal" :style="idNav == d.id ? 'background: #f0f6ff;' : ''">
              <td colspan="8">
                <ul class="nav nav-pills user-data" id="pills-tab" role="tablist">
                  <li class="nav-item">
                    <a :class="(fixedNavPills == 1) ? 'nav-link active' : 'nav-link'" @click="fixedNavPills = 1;" id="pills-home-tab" data-toggle="pill" href="#pills-home" role="tab" aria-controls="pills-home" aria-selected="true">Solicitudes</a>
                  </li>
                  <li class="nav-item">
                    <a :class="(fixedNavPills == 2) ? 'nav-link active' : 'nav-link'" @click="fixedNavPills = 2;"  id="pills-profile-tab" data-toggle="pill" href="#pills-profile" role="tab" aria-controls="pills-profile" aria-selected="false">Historial</a>
                  </li>
                </ul>
                <div class="tab-content" id="pills-tabContent">
                  <div v-show="fixedNavPills == 1" class="tab-pane fade show active" id="pills-home" role="tabpanel" aria-labelledby="pills-home-tab">
                    <requests :requests="d.vacationabsencesrequest" :supervisors="d.supervisors" :years="years" :idDetail="getIdDetails(d.vacationabsencesdetail)"/>
                  </div>
                  <div v-show="fixedNavPills == 2" class="tab-pane fade show active" id="pills-profile" role="tabpanel" aria-labelledby="pills-profile-tab">
                    <vacation-history v-if="idNav == d.id" :userId="d.id" :year="years" :origin="'Admin'"></vacation-history>
                  </div>
                </div>
              </td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import axios from "axios";
import Requests from "@/views/Requests.vue";
import VacationHistory from "@/views/vacation/VacationHistory.vue";

@Component({
  components: {Requests, VacationHistory }
})

export default class Welcome extends Vue {
  @Prop({ default: 0 })
  private userId!: number;
  @Prop({ default: 0 })
  private years!: number;

  data: Array<any> = [];
  historyUser: Array<any> = [];

  idNav = 0;
  fixedNavPills = 1;
  loadingRequestListUser = false;

  async mounted() {
    this.getUsersActive();
  }

  async getUsersActive(){
    this.loadingRequestListUser = true;
    const response = await axios.get('/api/get-list-users-supervisor-new');
    this.data = response.data;
    this.loadingRequestListUser = false;
    this.$emit('count-data', response.data.length);
  }

  getDaysAvailable(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.years ){
        return data[i].flexible_holidays - data[i].days_taken;
      }
    }
    return 0;
  }

  getDaysTaken(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.years ){
        return data[i].days_taken;
      }
    }
    return 0;
  }

  getIdDetails(data: any){
    for (let i = 0; i < data.length; i++){
      if (data[i].year == this.years ){
        return data[i].id;
      }
    }
    return 0;
  }

  getStatus(data: any, id: any){
    const fechaActual = new Date();

      for (const solicitud of data) {
          if (solicitud.status === 1 && solicitud.vacation_absences_detail_id === id) {
              return 'recibida';
          }

          const fechaInicio = new Date(solicitud.date_start);
          const fechaFin = new Date(solicitud.date_end);

          if (solicitud.status === 3 && fechaActual >= fechaInicio && fechaActual <= fechaFin && solicitud.vacation_absences_detail_id === id) {
              return 'ausente';
          }
      }

      return 'presente';
  }

}
</script>
<style media="screen">
#pills-home {
  padding: 0.5% 4%;
}
#pills-profile {
  padding: 0.5% 4%;
}
.nav-pills.user-data .nav-item .nav-link.active{
  background: transparent;
  font-weight: bold;
  color: #01408D;
}
.nav-pills.user-data .nav-item .nav-link{
  font-weight: 400;
  color: #01408D;
}
p {
  color: #000;
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
</style>
