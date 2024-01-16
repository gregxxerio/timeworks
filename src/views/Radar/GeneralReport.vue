<template>
  <div>
    <div class="page-wrapper" @click="clickBody()">
      <a-modal
        v-model="visibleLoading"
        :style="{ color: '#6c757d' }"
        :ok-button-props="{ props: { disabled: true } }"
        :cancel-button-props="{ props: { disabled: true } }"
        :footer="null"
      >
        <div class="container-fluid">
          <div class="row">
            <div class="col-md-12" style="text-align: -webkit-center;">
              <h4>Espere un momento</h4>
              <h4>Cargando resultados...</h4>
              <div class="spinner"></div>
            </div >
          </div>
        </div>
      </a-modal>
      <div class="container-fluid">
        <hr>
        <div class="row" >
          <div class="col-md-12" >
            <h4 class="float-sm-left">Informe
            </h4>
          </div>
          <div class="col-md-3" style="background:#D8D8D8;">
            <month-picker-input @change="onMonthChanged"  :input-pre-filled="true" :default-month="month + 1" :default-year="year" lang="es"></month-picker-input>

            <!-- <div class="input-group" style="height: 100%;">
  <span class="input-group-text" style="background: #ffffff;padding-top:5px;">
    &nbsp;&nbsp;
    <a @click="pastMonth()">
    <img src="/assets/Icons/left.png" style="width : 1rem;">
    </a>
    &nbsp;&nbsp;
  </span>
  <label style="padding-top:5px;">&nbsp;&nbsp;{{currentMonth}}&nbsp;&nbsp; </label>
  <span class="input-group-text" style="background: #ffffff;padding-top:5px;">
    &nbsp;&nbsp;
    <a @click="nextMonth()">
    <img src="/assets/Icons/rigth.png" style="width : 1rem;">
    </a>
    &nbsp;&nbsp;
  </span>
</div> -->
          </div>
          <div class="col-md-9" style="background:#D8D8D8;">
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

            <button class="btn btn-outline float-sm-right" @click="generateExcel">
              <!-- <export-json-excel class="export-excel" :data="data" :fields="fields" name="Reporte" > -->
                <img src="/assets/Icons/descargar.png" style="width : 1rem;">
              <!-- </export-json-excel> -->
            </button>
            <div class="float-sm-right">

              <button class="btn btn-outline float-sm-right img-filter">
                <img
                src="/assets/Icons/filtro.png"
                class="img-fluid"
                style="width: 20px; margin-left: 3px"
                />

              </button>
              <nav class="menu" >
                <ul>
                  <li>
                    <button @click="filters = 6">
                      <img
                      src="/assets/Icons/sin registro.png"
                      class="img-fluid"
                      style="width: 16px; margin-left: -5px"
                      />&nbsp;Sin registro
                    </button>
                  </li>
                  <li>
                    <button @click="filters = 4">
                      <img
                      src="/assets/Icons/verde.png"
                      class="img-fluid"
                      style="width: 16px; margin-left: -5px"
                      />&nbsp;Verde (ingreso)
                    </button>
                  </li>
                  <li>
                    <button @click="filters = 3">
                      <img
                      src="/assets/Icons/amarillo.png"
                      class="img-fluid"
                      style="width: 16px; margin-left: -5px"
                      />&nbsp;Amarillo
                    </button>
                  </li>
                  <li>
                    <button @click="filters = 2">
                      <img
                      src="/assets/Icons/naranja.png"
                      class="img-fluid"
                      style="width: 16px; margin-left: -5px"
                      />&nbsp;Naranja
                    </button>
                  </li>
                  <li>
                    <button @click="filters = 1">
                      <img
                      src="/assets/Icons/rojo.png"
                      class="img-fluid"
                      style="width: 16px; margin-left: -5px"
                      />&nbsp;Rojo
                    </button>
                  </li>
                  <li>
                    <button @click="filters = 0">
                      <img
                      src="/assets/Icons/reset.png"
                      class="img-fluid"
                      style="width: 16px; margin-left: -5px"
                      />&nbsp;Todos
                    </button>
                  </li>



                </ul>
              </nav>
            </div>

            <!-- <button class="btn btn-outline float-sm-right">
              <img src="/assets/Icons/filtro.png" style="width : 1rem;">

            </button> -->


          </div>

          <div class="col-md-12">

            <div class="table">
              <div class="table-scroll">
                <table class="table-main">
                  <thead>
                    <tr>
                      <th class="fix-col" style="
                      border-color: #221A54;
                      border-width: 3px;
                      color: #221A54;
                      line-height: 19px;
                      font-size: 21px;
                      border-bottom-style: solid;
                      "> Nombre</th>
                      <th v-for="(day, index) in countDay" :key='index' @click="a()"> {{day}}</th>
                    </tr>
                  </thead>
                  <tbody>

                    <tr v-for="(info, index) in infoByMoth" :key='index'>
                      <template v-if="info.data != '[]'">
                        <td class="fix-col" style="font-size: 15px; color: #2D3740; height: 65px;"> {{info.name}}</td>
                        <td style="height: 65px;" v-for="(result, index2) in JSON.parse(info.data)" :key='index2'>
                          <template v-if="result.evaluation.name != 'vacio'">
                            <template v-if="result.evaluation.name == 'Rojo'">
                              <img src="/assets/Icons/rojo.png" style="width : 1rem;"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''">
                            </template >
                            <template v-else-if="result.evaluation.name == 'Naranja'">
                              <img src="/assets/Icons/naranja.png" style="width : 1rem;"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''">
                            </template>
                            <template v-else-if="result.evaluation.name == 'Amarillo'">
                              <img src="/assets/Icons/amarillo.png" style="width : 1rem;"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''">
                            </template>
                            <template v-else-if="result.evaluation.name == 'sin registro'">
                              <img src="/assets/Icons/sin registro.png" style="width : 1rem;"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''" >
                            </template>
                            <template v-else-if="result.evaluation.name == 'Verde'">
                              <img src="/assets/Icons/verde pre.png" style="width : 1rem;" v-if="!result.evaluation_results_confirm_id"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''">
                              <img src="/assets/Icons/naranja.png" style="width : 1rem;" v-if="result.evaluation_results_confirm_id == 2"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''">
                              <img src="/assets/Icons/verde.png" style="width : 1rem;"   v-if="result.evaluation_results_confirm_id == 5"
                              :style="filters == 0 ? '' :
                              filters != result.evaluation_results_id ? 'filter: invert(0.4) sepia(1);':
                              filters == result.evaluation_results_id ? '' : ''">
                            </template>
                          </template>
                          <template v-else>
                            <p>&nbsp;<br> </p>
                          </template>
                      </td>
                    </template>

                    </tr>
                  </tbody>
                </table>
              </div>
            </div>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import SideBar from "@/components/SideBar.vue";
import axios from "axios"
import moment from "moment"
import {MonthPickerInput} from "vue-month-picker";

// tumut y pozarica,
// petrofac

// axios.defaults.url = Proxy.api.domain;
@Component({
  components: {
    SideBar,MonthPickerInput
  },
})
export default class GeneralReports extends Vue {
  initialInfo: Array<any>=[];
  users: Array<any>=[];
  infoByMoth: Array<any>=[];
  reprotStructure: Array<any>=[];
  daatanew: any;
  newdaytest: Array<any>=[];
  countDay: any;
  coutnPost= 0;
  currentMonth: any;
  menuShow= false;
  data: Array<any>=[];
  fields: Array<any>=[];
  monthinitial: any = 0;
  yearinitial: any = 0;
  visibleLoading: any = false;
  filters: any = 0;
  month = moment().month();
  year =  moment().year();

  created() {


    this.initialData();
    this.days();
    this.geetcurrentMonth();
    // EventBuss.$on('controlMenu', function(this: any, control: any) {
    //   if (control) {
    //     this.showMenu(control)
    //   }
    // }.bind(this));
    // EventBuss.$on('filterReports', function(this: any, date: any) {
    //   if (date) {
    //     this.coutnPost++;
    //     this.filter(date);
    //     this.daysFilter(date)
    //     this.filtergeetcurrentMonth(date)
    //   }
    // }.bind(this));
  }

  // async
   initialData(){
    const date = moment().toISOString();
    const newData = date.split('T')
    const yearMoht = newData[0].split('-');
    const mergeMothYear = yearMoht[0]+'-'+yearMoht[1];
    this.monthinitial = yearMoht[1];
    this.yearinitial = yearMoht[0];
    this.data = [];
    this.fields = [];
    this.visibleLoading = true;

    // const infoByMonth = await

    axios.get('/api/info-moth/'+mergeMothYear).then(response => {
      this.infoByMoth = response.data;
      this.visibleLoading = false;
    });
    // axios.get('/api/info-moth-data/'+mergeMothYear).then(response => {
    //     this.data.push(JSON.parse(response.data.data));
    //   this.fields = response.data.dataFields;
    // });
  }
   filter(){
    this.data = [];
    this.fields = [];
    this.visibleLoading = true;

    const mergeMothYear = this.yearinitial+'-'+this.monthinitial;
      axios.get('/api/info-moth/'+mergeMothYear).then(response => {
        this.infoByMoth = response.data;
        this.visibleLoading = false;
      });
    // axios.get('/api/info-moth-data/'+mergeMothYear).then(response => {
    //
    //   // response.data.data.forEach(function(item) => {
    //     this.data.push(JSON.parse(response.data.data));
    //   // });
    //   this.fields = response.data.dataFields;
    // });
  }
  daysFilter(date: any){

    const dateparse = new Date(Date.parse(date));

    const lastday = new Date(dateparse.getFullYear(), dateparse.getMonth() + 1, 0);
    const day = lastday.getDate();
    const days = [];
    for (let index = 1; index < day+1; index++) {
      const numberOfDay = index;
      days.push(numberOfDay);
    }
    this.countDay = days;
  }


  days() {
    const date = new Date();
    const lastday = new Date(date.getFullYear(), date.getMonth() + 1, 0);
    const day = lastday.getDate();
    const days = [];
    for (let index = 1; index < day+1; index++) {
      const numberOfDay = index;
      days.push(numberOfDay);
    }
    this.countDay = days;
  }
  geetcurrentMonth(){
    const date = new Date();
    const month = [];

    month[0] = "Enero";
    month[1] = "Febrero";
    month[2] = "Marzo";
    month[3] = "Abril";
    month[4] = "Mayo";
    month[5] = "Junio";
    month[6] = "Julio";
    month[7] = "Agosto";
    month[8] = "Septiembre";
    month[9] = "Octubre";
    month[10] = "Noviembre";
    month[11] = "Diciembre";

    const monthCurrent = month[date.getMonth()];
    const year = date.getFullYear();
    this.currentMonth = monthCurrent + ' ' + year;
  }

  onMonthChanged (date: any) {
    this.year = date.year;
    this.month = date.monthIndex - 1;
    this.currentMonth =((this.month +1) < 10 ? '0'+(this.month +1) : this.month +1 ) + ' ' + this.year;
    this.yearinitial = this.year;
    this.monthinitial = ((this.month +1) < 10 ? '0'+(this.month +1) : this.month +1 );
    this.daysFilter(this.yearinitial + '-' + this.monthinitial + '-03');
    this.filter();

  }

  filtergeetcurrentMonth(date: any){
    const dateParse = new Date(Date.parse(date));
    const month = [];

    month[0] = "Enero";
    month[1] = "Febrero";
    month[2] = "Marzo";
    month[3] = "Abril";
    month[4] = "Mayo";
    month[5] = "Junio";
    month[6] = "Julio";
    month[7] = "Agosto";
    month[8] = "Septiembre";
    month[9] = "Octubre";
    month[10] = "Noviembre";
    month[11] = "Diciembre";

    const monthCurrent = month[dateParse.getMonth()];
    const year = dateParse.getFullYear();
    this.currentMonth = monthCurrent + ' ' + year;
  }
  filtergeetcurrentMonthTwo(year: any, month: any){

    switch (month) {

      case '01': this.currentMonth =  'Enero ' + year;   break;
      case '02': this.currentMonth =  'Febrero ' + year;   break;
      case '03': this.currentMonth =  'Marzo ' + year;   break;
      case '04': this.currentMonth =  'Abril ' + year;   break;
      case '05': this.currentMonth =  'Mayo ' + year;   break;
      case '06': this.currentMonth =  'Junio ' + year;   break;
      case '07': this.currentMonth =  'Julio ' + year;   break;
      case '08': this.currentMonth =  'Agosto ' + year;   break;
      case '09': this.currentMonth =  'Septiembre ' + year;   break;
      case '10': this.currentMonth =  'Octubre ' + year;   break;
      case '11': this.currentMonth =  'Noviembre ' + year;   break;
      case '12': this.currentMonth =  'Diciembre ' + year;   break;
    }
  }
  clickBody(){
    this.menuShow = false;
  }
  a(){
    console.log('s');

  }
  nextMonth(){

    if (parseInt(this.monthinitial) == 12) {
      this.yearinitial = parseInt(this.yearinitial) + 1;
      this.monthinitial = 1;
    }else{
      this.monthinitial = parseInt(this.monthinitial) + 1;
    }

    this.monthinitial = this.monthinitial < 10 ? '0'+this.monthinitial : this.monthinitial;
    this.filtergeetcurrentMonthTwo(this.yearinitial, String(this.monthinitial));
    this.filter();
  }
  pastMonth(){
    if (parseInt(this.monthinitial) == 1) {
      this.yearinitial = parseInt(this.yearinitial) - 1;
      this.monthinitial = 12;
    }else{
      this.monthinitial = parseInt(this.monthinitial) - 1;
    }

    this.monthinitial = this.monthinitial < 10 ? '0'+this.monthinitial : this.monthinitial;
    this.filtergeetcurrentMonthTwo(this.yearinitial, String(this.monthinitial));
    this.filter();
  }

  generateExcel(){
    console.log(this.infoByMoth,'ibm');

  }
}
</script>
<style >
.table-main {
  border: none;
  /* border-right: solid 1px rgb(75, 90, 102); */
  border-collapse: separate;
  border-spacing: 0;
  font: normal 13px Arial, sans-serif;
}

.table-main thead th {
  /* background-color: rgb(203, 220, 233); */
  border: none;
  /* color: #336B6B; */
  padding: 10px;
  text-align: left;
  text-shadow: 1px 1px 1px #fff;
  /* white-space: nowrap; */
}

.table-main tbody td {
  border-bottom: solid 1px rgb(75, 90, 102);
  color: #333;
  padding: 10px;
  text-shadow: 1px 1px 1px #fff;
  /* white-space: nowrap; */
}

.table {
  position: relative;
  /* border: solid 2px; */
}

.table-scroll {
  margin-left: 240px;
  overflow-x: scroll;
  overflow-y: visible;
  padding-bottom: 5px;
  /* width: 500px; */
  /* border: solid 2px; */
}

.table-main .fix-col {
  /* border-left: solid 1px rgb(75, 90, 102); */
  /* border-right: solid 1px rgb(75, 90, 102); */
  /* border-bottom: : solid 0px */
  left: 0;
  /* word-break: break-all; */
  position: absolute;
  top: auto;
  width: 240px;
}
.export-excel {
  background:#D8D8D8;
  border: 0px;
}
export-excel:hover {
  background:#D8D8D8;
  border: 0px;
}

.page-wrapper{
  margin-left: 5px !important;
}

.menu {
  visibility: hidden;
  position: absolute;
  background: #262626;
  border-radius: 7px;
  border: 1px solid #262626;
  z-index: 22;
}

button.img-filter + .menu:active,
button.img-filter:focus + .menu {
  visibility: visible;
  outline: none;
  box-shadow: none;
}

.img-filter {
  border: none;
  background: transparent;
  outline: none !important;
   box-shadow: none;
}

.menu ul {
  list-style: none;
  margin: .2rem 0;
  padding: .2rem 1.2rem .2rem .4rem;
}

.menu ul li {
  padding: 0;
}

.menu ul li button {
  padding: .2rem .1rem;
width: 100%;
font-size: 0.9rem;
-webkit-appearance: button;
-moz-appearance: button;
text-transform: none;
font-family: inherit;
/* font-size: 100%; */
line-height: 1.15;
margin: 0;
overflow: visible;
border: 0;
text-align: left;
/* font-family: Arial,Verdana,sans-serif; */
/* outline: 0; */
cursor: pointer;
background: 0 0;
color: #afc0c1;
outline: none;
 box-shadow: none;
}
</style>
