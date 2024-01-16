<template>
  <div class="section p-4">
    <h1 class="section-title">
      Administración
      <template v-if="$route.fullPath === '/administration?detail'">

        <b>  {{type === '' ? ''
            : type === types.reportsgeneration ? '> Reportes recibidos'
                : type === types.users ? '> Revisión detallada'
                    : type === types.vacation ? '> Vacaciones y ausencias'
                        : type === types.timesheet ? '> TimeSheets'
                            : type === types.general ? '> Revisión General'
                                : type === types.radar ? '> Radar C19'
                                    : '' }}</b>
      </template>
    </h1>

    <div class="card" style="border: 0; background-color: transparent">
      <div class="grid-cards-admin" v-show="type === '' || $route.fullPath != '/administration?detail'">
        <div class="card card-shadow" @click="setType(types.reportsgeneration)" style="cursor: pointer;">
          <div class="card-body" >
            <hr class="hr">
            <div class="card-contain">
              <p class="p-admin">INFORMES RECIBIDOS</p>
              <img src="assets/images/Informes recibidos.png" class="img-admin"/>

            </div>
          </div>
        </div>
        <div class="card card-shadow" @click="setType(types.users)" style="cursor: pointer;">
          <div class="card-body" >
            <hr class="hr">
            <div class="card-contain">
              <p class="p-admin">REVISIÓN DETALLADA</p>
              <img src="assets/images/detallada.png" class="img-admin"/>

            </div>
          </div>
        </div>
        <div class="card card-shadow" @click="setType(types.general)" style="cursor: pointer;">
          <div class="card-body" >
            <hr class="hr">
            <div class="card-contain">
              <p class="p-admin">REVISIÓN GENERAL</p>
              <img src="assets/images/general.png" class="img-admin"/>
            </div>
          </div>
        </div>
        <div v-show="permission" class="card card-shadow" @click="setType(types.vacation)" style="cursor: pointer;">
          <div class="card-body" >
            <hr class="hr">
            <div class="card-contain">
              <p class="p-admin" >VACACIONES Y AUSENCIAS</p>
              <img src="assets/images/Vacaciones.png" class="img-admin"/>

            </div>
          </div>
        </div>
        <div class="card card-shadow" @click="setType(types.timesheet)" style="cursor: pointer;">
          <div class="card-body" >
            <hr class="hr">
            <div class="card-contain">
              <p class="p-admin">TIMESHEETS</p>
              <img src="assets/images/TimeSheets.png" class="img-admin"/>
            </div>
          </div>
        </div>
        <div class="card card-shadow" @click="setType(types.radar)" style="cursor: pointer;">
          <div class="card-body" >
            <hr class="hr">
            <div class="card-contain">
              <p class="p-admin">RADAR C19</p>
              <img src="assets/images/Radar- naranja.png" class="img-admin"/>
            </div>
          </div>
        </div>
      </div>
      <template>
        <VueWindowDimensions>
          <template slot-scope="{ dimensions }">
            <div v-if="dimensions" style="width: 100%" class="card-body card-shadow mt-4 data-query" v-show="type != '' && type != types.reportsgeneration && $route.fullPath === '/administration?detail'">
              <general-registers v-if="type === types.general"></general-registers>
              <project-registers v-if="type === types.projects"></project-registers>
              <users-registers v-if="type === types.users"></users-registers>
              <reports-register v-if="type === types.reports"></reports-register>
              <vacation v-if="type === types.vacation"></vacation>
              <general-review v-if="type === types.generalreview"/>
              <timesheet v-if="type === types.timesheet" />
              <radar v-if="type === types.radar" />
            </div>
          </template>
        </VueWindowDimensions>
      </template>
      <div v-show="$route.fullPath === '/administration?detail'">
        <reports-generation v-if="type === types.reportsgeneration"></reports-generation>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component,  Vue} from "vue-property-decorator";
import GeneralRegisters from "@/views/GeneralRegisters.vue";
import UsersRegisters from "@/views/UserRegisters.vue";
import ProjectRegisters from "@/views/ProjectRegisters.vue";
import ReportsRegister from "@/views/ReportsRegister.vue";
import ReportsGeneration from "@/views/ReportsGeneration.vue";
import Vacation from "@/views/AdminVacation.vue";
import GeneralReview from "@/views/GeneralReview.vue";
import VueWindowDimensions from 'vue-window-dimensions'
import Timesheet from "@/views/Timesheet.vue";
import Radar from "@/views/Radar/Radar.vue";

@Component({
  components: {
    Timesheet,
    GeneralReview,
    Radar,
    ReportsRegister, ProjectRegisters, GeneralRegisters, UsersRegisters, ReportsGeneration, Vacation, VueWindowDimensions }
})
export default class Administration extends Vue{
  private types = {
    general : "general",
    users : "users",
    projects : "projects",
    reports: "reports",
    reportsgeneration: "reportsgeneration",
    vacation: "vacation",
    timesheet: "timesheet",
    generalreview: 'generalreview',
    radar: 'radar'
  };
  private type = '';
  role: any = 0;
  permission: any = false;


  getPermission(data: any){
    const roles = JSON.parse(this['$store'].state.user.roles_time);
    const array = roles.map(function(x) {
      return x.id;
    });
    let iguales=0;

    for(let i=0; i < data.length; i++)
    {
      for(let j=0; j < array.length; j++)
      {
        if(data[i]==array[j])
          iguales++;
      }
    }
    if (iguales > 0) {
      this.permission = true;
    }else {
      this.permission = false;
    }
  }

  goToReportsGeneration(){
    this['$router'].replace("/reports-generation").catch(()=>{ console.log("error") });
  }

  private setType(type: string){
    console.log(type);
    this['$router'].replace("/administration?detail").catch(()=>{ console.log("error")});
    this.type =  type;
  }

  async mounted() {
    this.role = this.$store.state.user.role_time_id;
    this.getPermission([2,4,5]);
  }
}
</script>

<style scoped>

/* .data-query {
  width: calc(40% + 3em) !important;
  margin-left : -2%;
} */

.btn-custom-warning{
  border: 2px solid #F5B133 !important;
  color: #F5B133 !important;
  border-radius: 10px !important;
  font-weight: bold;
  padding-top: 5px;
  padding-bottom: 3px;
  margin: 2px;
}

.btn-custom-warning:hover {
  background-color: #434B54;
}

.grid-cards-admin {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap : 1em;
}

.hr {
  margin-top: 2%;
  margin-bottom: 2%;
  background: #000000;
}

.p-admin {
  font-size: 1em;
  font-weight: 600;
  color: #27282a;
}

.img-admin {
  width: 5.2rem;
  padding: 2%;
}

.card-contain {
  display: flex;
  /* justify-content: space-between; */
  flex-direction: column;
  text-align : start;
  height: 100%;
}

.p-lr {
  padding-left: 4%;
  padding-right: 4%;
}

a {
  color: #6C757D !important;
}

a:hover {
  color: #6C757D !important;
}

.bg-admin {
  background-image: url("/assets/images/Image.png");
  background-repeat: no-repeat;
  background-position: 24em top;
  height: 76vh;
}

.title-admin {
  font-weight: 500;
}

.title-admin:hover {
  text-decoration: underline;
}

@media (min-width: 1025px) and (max-width: 1200px) {
  .bg-admin {
    background-position: 18em top;
    height: 74vh;
  }
}

@media (min-width: 769px) and (max-width: 1024px) {
  .bg-admin {
    background-position: 6em top;
    height: 74vh;
  }
}

@media (max-width: 768px) {
  .bg-admin {
    background-position: -6em top;
    height: 74vh;
  }

  .grid-cards-admin {
    grid-template-columns: repeat(2, 1fr);
  }
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
</style>
