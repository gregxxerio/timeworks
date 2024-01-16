<template>
  <div>
    <loading :active="loadingData" :is-full-page="true"></loading>
    <div class="row p-2 d-flex align-items-center">
      <div class="d-flex align-items-center">
        <span class="mr-1">Organización</span>
        <v-select @input="onOrganizationSelectedChanged"
                  v-model="organizationSelected" taggable multiple
                  label="organizacion"
                  :options="organizations"
                  placeholder="Selecciona una opcion"/>
      </div>
      <div class="d-flex align-items-center ml-3">
        <span class="mr-1">Actividades: </span>
        <v-select
                  v-model="projectsSelected" taggable multiple
                  label="codigo"
                  :options="projects"
                  placeholder="Selecciona una opcion"/>
      </div>
      <div class="d-flex align-items-center ml-3">
        <span class="mr-1">Fechas:</span>
        <a-range-picker  @change="dateRangeSelected" />
      </div>
      <div class="ml-3">
        <button v-if="!isLoading" class="btn-apply" @click.prevent="getDataByProjectDateAndOrg">Aplicar</button>
        <rotate-square2 size="20px" v-else></rotate-square2>
      </div>
    </div>

<!--    <div class="row">
      <rotate-square2 v-if="isLoading"></rotate-square2>
    </div>-->

<!--    <highcharts v-show="data.length" id="myChart"  :options="chartOptions" ref="chart"></highcharts>-->

    <div class="row p-3">
      <div class="col-6" >
        <div class="row">
          <div class="col-12 text-left" style="border-bottom: 1px solid gray">
            <b>RESUMEN GENERAL</b>
          </div>
          <div v-if="!!response" class="col-12 pt-2 text-muted text-left">
            TOTAL DE HORAS: {{ response.totalHours }}
          </div>
          <div class="col-12 pt-1">
            <highcharts v-if="horizontalChart!=null" :options="horizontalChart"></highcharts>
          </div>

        </div>
        <div class="row p-2 mt-2" v-if="horizontalChart!=null" style="background-color: #E6E2EB">
          <div class="col-6">
            <div class="d-flex align-items-center">
              <div><span class="dot dot-pro"></span></div> <div>Proyectos</div>
            </div>

          </div>
          <div class="col-6">
            <div class="d-flex align-items-center">
              <div><span class="dot dot-ad"></span></div> <div>Administración de proyectos</div>
            </div>
          </div>
          <div class="col-6 mt-1">
            <div class="d-flex align-items-center">
              <div><span class="dot dot-over"></span></div> <div>Overhead</div>
            </div>
          </div>
          <div class="col-6 mt-1">
            <div class="d-flex align-items-center">
              <div><span class="dot dot-off"></span></div> <div>No Laborado</div>
            </div>
          </div>
        </div>
        <div class="row mt-2 p-3 text-center justify-content-center" v-if="horizontalChart!=null">
          <button class="btn btn-outline-success btn-custom" @click="exportToExcel">Descargar Excel <img src="/images/Excel.png" alt=""></button>
        </div>
      </div>
      <div class="col-6">
        <div class="col-12" v-if="pieChart!=null">
            <highcharts  :options="pieChart"></highcharts>
        </div>
        <div class="col-12" v-if="dataCollaborator.length">
          <div class="tableFixHead" style="margin-top: 10px">
            <table id="customers" class="table-wrapper">
              <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col">Colaborador</th>
                <th scope="col">Porcentaje</th>
                <th scope="col">Tiempo</th>
                <th></th>
              </tr>
              </thead>
              <tbody>
              <tr v-for="(item, index) in dataCollaborator" :key="index">
                <td ></td>
                <td class="text-left">{{ item.name }}</td>
                <td class="text-left">{{ calculatePercentage(item.total)}}%</td>
                <td class="text-left">{{ item.total }}</td>
                <td class="text-left">
                  <div class="dropdown">
                    <button class="btn btn-default custom-button dropdown-toggle" type="button" id="dropdownMenu1" data-toggle="dropdown" aria-haspopup="true" aria-expanded="true">
                      ...
                      <span class="caret"></span>
                    </button>
                    <ul class="dropdown-menu" aria-labelledby="dropdownMenu1">
                      <li class="no-padding"><a @click="exportToExcelSingleCollaborator(index)">Descargar</a></li>
                    </ul>
                  </div>
                </td>
              </tr>
              </tbody>
            </table>
          </div>
          <div class="row mt-2 p-3 text-center justify-content-center">
            <button class="btn btn-outline-success btn-custom" @click="exportToExcelCollaborator">Descargar Excel <img src="/images/Excel.png" alt=""></button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {Component, Vue} from "vue-property-decorator";
import moment from "moment";
import axios from "axios";
import XLSX from 'xlsx';
import {RotateSquare2} from "vue-loading-spinner";
import vSelect from 'vue-select'
import 'vue-select/dist/vue-select.css';
import GeneralRegisterPie3D from "@/views/charts/GeneralRegisterPie3D.vue";

@Component({
  components:{
    GeneralRegisterPie3D,
    RotateSquare2, vSelect
  }
})
export default class GeneralRegisters extends Vue{
  users: any = [];
  data: any = [];
  startDate = '';
  endDate = '';
  isLoading = false;
  loadingData = false;
  series: Array<any> = [];
  projects: Array<any> = [];
  userSelected: any = null;
  projectsSelected: any = null;
  organizationSelected: any = null;
  organizations: Array<any> = [];
  response: any = null;
  monthName = '';
  horizontalChart: any = null;
  circularChart: any = null;
  chartOptions: any = null;
  pieChart: any = null;
  loadingSecond  = false;
  sumTotal = 0;
  dataCollaborator: Array<any> = [];

  async created(){
    // const response = await axios.post('/api/user-list');
    // this.users = response.data
    const response = await axios.get('/api/get/data/organizations');
    this.organizations = response.data
  }


  onProjectsSelectedChanged(){
    this.getDataByProjectDateAndOrg()
  }

  async onOrganizationSelectedChanged(){
    const orgs = this.organizationSelected.map( (item: any) => item.organizacion)
    const response = await axios.post('/api/get/data/projects/organizations', { organizations: orgs});
    this.projects = response.data
  }

  /*get chartOptions() {
    return {
      chart: {
        type: 'bar'
      },
      title: {
        text: 'Horas reportadas en el período, por colaborador y actividad'
      },
      xAxis: {
        categories: this.projects
      },
      yAxis: {
        min: 0,
        title: {
          text: ''
        }
      },
      legend: {
        reversed: true
      },
      plotOptions: {
        series: {
          stacking: 'normal'
        }
      },
      series: this.series
    };
  }*/

  dateRangeSelected(date: any){
    if (!date.length) return;
    this.startDate = moment(date[0]._d).format('YYYY-MM-DD');
    this.endDate = moment(date[1]._d).format('YYYY-MM-DD');
  }

  onUserChanged(index: any){
    this.userSelected =  (typeof index === 'number') ? this.users[index] : null;
    this.getRegistersByDateRange();
  }

  async getDataByProjectDateAndOrg(){
    if (this.organizationSelected == null || this.startDate == '' || this.organizationSelected.length == 0) return;
    try {
      this.isLoading = true;
      const params = {
        start : this.startDate,
        end : this.endDate,
        organizations: this.organizationSelected.map( (item: any) => item.organizacion),
        projects: (this.projectsSelected) ? this.projectsSelected.map( (item: any) => item.codigo) : null,
      };

      const response = await axios.post('/api/get/data/revision-detail-custom', params);

      this.response = response.data;
      this.buildHorizontalChart();

      this.isLoading = false
    }
    catch (e) {
      console.log(e);
      this.isLoading = false;
    }
  }

  calculatePercentage(number: number){
    const total = (number * 100 ) / this.sumTotal;
    return total.toFixed(2);
  }

  buildHorizontalChart(){
    if (this.response == null) return;

    const horizontalChartData: any = [];

    const horizontalChartColors: any = [];

    // eslint-disable-next-line @typescript-eslint/no-this-alias
    const me = this;

    this.response.nowData.forEach(function (item: any) {
      item.codes.forEach((code: any) => {
        horizontalChartData.push({
          name: code.code,
          y: code.hours,
          p: parseFloat((Math.round(Number(code.hours) * 100) / Number(me.response.totalHours)).toString()).toFixed(2) //this.calcularPorcentaje(Number(code.hours), Number(response.data.totalHours))
        });

        horizontalChartColors.push(item.color);
      });
    });

    this.horizontalChart = {
      chart: {
        type: 'bar'
      },
      title: {
        text: null
      },
      subtitle: {
        text: null
      },
      accessibility: {
        announceNewData: {
          enabled: true
        }
      },
      xAxis: {
        type: 'category'
      },
      yAxis: {
        title: {
          text: ''
        }

      },
      legend: {
        enabled: false
      },
      plotOptions: {
        series: {
          borderWidth: 0,
          dataLabels: {
            enabled: true,
            format: '{point.y} ({point.p}%)'
          },
          cursor: 'pointer',
          point: {
            events: {
              click: (e: any) => {
                this.openModalFromCode(e.point.name);
              }
            }
          }
        },
      },
      series: [
        {
          name: "SubActividades",
          colorByPoint: true,
          data: horizontalChartData
        }
      ],
      colors: horizontalChartColors
    };
  }


  async openModalFromCode(code: string){
    const params = {
      start : this.startDate,
      end : this.endDate,
      code: code
    };

    this.loadingData = true;

    const response = await axios.post('/api/get/data/revision-detail-user', params);

    this.sumTotal = response.data.total;
    this.dataCollaborator = response.data.data;

    this.createPie3DChart(code);

    this.loadingData = false;
  }

  createPie3DChart(code: any){


    const data = [];

    this.dataCollaborator.forEach((item: any) => {
      data.push([item.name, item.total])
    });

    this.pieChart = {
      chart: {
        type: 'pie',
        options3d: {
          enabled: true,
          alpha: 45
        }
      },
      title: {
        text: 'Desglose de '+code
      },
      subtitle: {
        text: 'Total de horas: '+this.sumTotal+" hrs."
      },
      plotOptions: {
        pie: {
          innerSize: 100,
          depth: 45
        }
      },
      series: [{
        name: 'Horas',
        data: data
      }]
    };
  }

  async getRegistersByDateRange(){
    if (!this.startDate) return;
    try{
      this.data = [];

      const params = { start : this.startDate, end : this.endDate };

      if (this.userSelected){
        params['user_id'] = this.userSelected.id
      }

      this.isLoading = true;
      const result = await axios.post('/api/general-registers-list', params);
      this.data = result.data.result;

      const resultCharts = await axios.post('/api/general-registers-chart', params);
      this.projects = resultCharts.data.projects;
      this.series = resultCharts.data.series;

      this.isLoading = false;
    }catch (e) {
      console.log(e);
      this.isLoading = false;
    }
  }

  exportToExcelSingleCollaborator(index){
    const dataItems = this.dataCollaborator[index].data.map(function (item: any) {
      return {
        'colaborador' : item.name,
        'fecha' : item.start_date,
        'codigo_proyecto' : item.project,
        'descripcion' : item.detail,
        'horas' : item.hours
      }
    });

    const headers = ['colaborador', 'fecha', 'codigo_proyecto', 'descripcion', 'horas'];
    const data = XLSX.utils.json_to_sheet(dataItems, {
      header: headers
    })
    data['A1'].v = 'Colaborador'
    data['B1'].v = 'Fecha'
    data['C1'].v = 'Código del Proyecto'
    data['D1'].v = 'Descripción'
    data['E1'].v = 'Tiempo Estimado (hrs)'

    const workbook = XLSX.utils.book_new()
    const workbookNname = "registros";
    XLSX.utils.book_append_sheet(workbook, data, workbookNname);
    const fileName = `registros_generales_del_${this.startDate}_al_${this.endDate}.xlsx`;
    XLSX.writeFile(workbook, fileName);
  }

  exportToExcelCollaborator() {
    const dataItemsPrev = this.dataCollaborator.reduce(function (previousValue: any, currentValue: any) {
      return previousValue.concat(currentValue.data)
    }, []);

    const dataItems = dataItemsPrev.map(function (item: any) {
      return {
        'colaborador' : item.name,
        'fecha' : item.start_date,
        'codigo_proyecto' : item.project,
        'descripcion' : item.detail,
        'horas' : item.hours
      }
    });

    const headers = ['colaborador', 'fecha', 'codigo_proyecto', 'descripcion', 'horas'];
    const data = XLSX.utils.json_to_sheet(dataItems, {
      header: headers
    })
    data['A1'].v = 'Colaborador'
    data['B1'].v = 'Fecha'
    data['C1'].v = 'Código del Proyecto'
    data['D1'].v = 'Descripción'
    data['E1'].v = 'Tiempo Estimado (hrs)'

    const workbook = XLSX.utils.book_new()
    const workbookNname = "registros";
    XLSX.utils.book_append_sheet(workbook, data, workbookNname);
    const fileName = `registros_generales_del_${this.startDate}_al_${this.endDate}.xlsx`;
    XLSX.writeFile(workbook, fileName);

  }

  exportToExcel(){

    const dataItems = this.response.data.map(function (item: any) {
      return {
        'colaborador' : item.user.name,
        'fecha' : item.start_date,
        'codigo_proyecto' : item.project,
        'descripcion' : item.detail,
        'horas' : item.hours
      }
    });

    const headers = ['colaborador', 'fecha', 'codigo_proyecto', 'descripcion', 'horas'];
    const data = XLSX.utils.json_to_sheet(dataItems, {
      header: headers
    })
    data['A1'].v = 'Colaborador'
    data['B1'].v = 'Fecha'
    data['C1'].v = 'Código del Proyecto'
    data['D1'].v = 'Descripción'
    data['E1'].v = 'Tiempo Estimado (hrs)'

    const workbook = XLSX.utils.book_new()
    const workbookNname = "registros";
    XLSX.utils.book_append_sheet(workbook, data, workbookNname);
    const fileName = `registros_generales_del_${this.startDate}_al_${this.endDate}.xlsx`;
    XLSX.writeFile(workbook, fileName);
  }

}
</script>

<style scoped>
  #myChart{
    height: 800px !important;
  }

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

  .v-select{
    min-width: 200px !important;
    width: 200px !important;
  }

  .ant-calendar-picker{
    width: 250px !important;
  }

  .btn-custom{
    font-size: 12px;
    border-radius: 25px;
    padding-left: 20px;
    padding-right: 20px;
  }
  .btn-custom img{
    width: 20px;
  }

  .custom-button{
    padding: 0;
    background: none;
  }

  li.no-padding{
    padding-left: 10px;
  }

  .tableFixHead          { overflow: auto; height: 250px; }
  .tableFixHead thead th { position: sticky; top: 0; z-index: 1; }

  /* Just common table stuff. Really. */
  table  { border-collapse: collapse; width: 100%; }
  th, td { padding: 8px 16px; }
  th     { background:#eee; }
</style>
