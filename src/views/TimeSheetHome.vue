<template>
  <div class="row p-3">
    <div class="table-responsive">
      <table class="table">
        <thead style="background-color: #f4f7f9">
        <tr>
          <th scope="col" style="text-align: center">Proyecto</th>
          <th scope="col" style="text-align: center">Horas registradas</th>
          <th scope="col" style="text-align: center">Porcentaje registrado</th>
          <th scope="col" style="text-align: center">Previsto</th>
          <!-- <th scope="col" style="text-align: center">Estatus</th> -->
          <th scope="col" style="text-align: center">Excel</th>
        </tr>
        </thead>
        <tbody v-if="data != null">
          <template v-for="(item, index) in data.data" >
            <tr :key="index" style="border: none">
              <td style="text-align: left;padding-left: 3%; font-weight: bold;">{{ item.code }}</td>
              <td style="text-align: center; ">{{ (item.hours + ((item.percentageTotal / 100) * data.horasNoLaborado)).toFixed(2) }}</td>
              <td style="text-align: center; ">{{ item.percentageTotal  }}%</td>
              <td style="text-align: center; ">{{ item.percentageRequired }}%</td>
              <!-- <td style="text-align: center; ">
              <img v-if="item.percentageTotal >= item.percentageRequired" src="/images/ok.png" alt="Ok">
              <img v-else src="/images/danger.png" alt="Danger">
            </td> -->

            <td style="text-align: center;">
              <img @click="downloadXML(data.data[index])" v-if="item.percentageTotal > 0" style="cursor: pointer"  src="/images/Excel.png" alt="Descargar Excel">
            </td>
          </tr>
          <tr :key="index" style="border: none;">
            <td style="text-align: left;padding-left: 3%; ">Horas laboradas </td>
            <td style="text-align: center;">{{item.hours}}</td>
            <td style="text-align: center;">{{ ((item.hours / (item.hours + ((item.percentageTotal / 100) * data.horasNoLaborado)) ) * item.percentageTotal).toFixed(2)  }}%</td>
            <td style="text-align: center;">-</td>
            <td style="text-align: center;"></td>
          </tr>
          <tr :key="index">
            <td style="text-align: left;padding-left: 3%; ">Horas no laboradas
            </td>
            <td style="text-align: center;">{{((item.percentageTotal / 100) * data.horasNoLaborado).toFixed(2)}}</td>
            <td style="text-align: center;">{{((((item.percentageTotal / 100) * data.horasNoLaborado) / (item.hours + ((item.percentageTotal / 100) * data.horasNoLaborado)) ) * item.percentageTotal).toFixed(2)}}%</td>
            <td style="text-align: center;">-</td>
            <td style="text-align: center;">-</td>
          </tr>
        </template>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";
import XLSX from "xlsx";
@Component
export default class UsePolicy extends Vue {
  data: any = null;

  async created() {

    try {
      //array de registros importados
      const data = this.$store.state.data;
      await axios.post('/api/store/temporarily', {data, noProcesar: true});
      const response = await axios.get('/api/get/data/timesheet/temporarily');
      this.data = response.data;
    } catch (e) {
      console.log(e.message)
    }
  }

  downloadXML(item: any) {
    const array = [];
    for (const index in item.data) {
      array.push(item.data[index]);
    }

    const headers = ['project', 'detail', 'hours',  'start_date'];
    const excel = XLSX.utils.json_to_sheet(array, {
      header: headers
    })

    excel['A1'].v = 'Proyecto'
    excel['B1'].v = 'Detalle'
    excel['C1'].v = 'Horas'
    excel['D1'].v = 'Fecha'

    const workbook = XLSX.utils.book_new()
    const workbookNname = "registros";
    const userName = this['$store'].state.user.name.replace(/ /g, "_");
    XLSX.utils.book_append_sheet(workbook, excel, workbookNname);
    const fileName = `${userName}.xlsx`;
    XLSX.writeFile(workbook, fileName);

  }

}
</script>
<style scoped>

tr {
  border-bottom: 1pt solid #cccccc;
}
.icon.icon-blue, .icons-blue .icon {
    background-color: blue;
    width: 16px;
    height: 16px;
    display: block;
}
td.tool:hover:after {
    content: "Horas no laboradas incluye el 10 de porcentaje proporcional vacaciones y días de enfermedad.";
    display: block;
    position: relative;
    top: -16px;
    /* right: -16px; */
    width: 350px;
    background: lightblue;
}
</style>
