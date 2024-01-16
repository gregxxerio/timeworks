<template>
  <div>
    <label class="step" style="margin-top:-2%;">
    Organiza los registros importados por objetivo y actividad según el proyecto que requiere reportes TimeSheets.
    </label>
    <div class="row p-3 cf-style">
      <!-- {{data}} -->
      <template v-for="(item, index) in data"  >
        <div class="card cfp-style" :style="'max-width:'+screenWidth+';'" :key="index" v-if="item.activities.length != 0">
          <div class="card-body">
            <h2>{{item.project.code}}</h2>
            <div class="table-responsive">
              <table class="table">
                <thead style="background-color: #f4f7f9">
                  <tr>
                    <th scope="col" style="text-align: left;" width="25%">Descripción</th>
                    <th scope="col" style="text-align: left;" width="10%">Fecha</th>
                    <th scope="col" style="text-align: left;">Hora</th>
                    <th scope="col" style="text-align: left;">Tiempo registrado</th>
                    <th scope="col" style="text-align: left;">Objetivo</th>
                    <th scope="col" style="text-align: left;">Actividad</th>
                  </tr>
                </thead>
                <tbody >
                  <template v-for="(time, index_child_one) in item.times">
                    <tr :key="index_child_one" style="border: none;">
                      <td style="text-align: left;">{{time.detail}}</td>
                      <td style="text-align: left;">{{ getDateFormated(time.start_date)}}</td>
                      <td style="text-align: left;">{{ getHoursFormated(time.start_time, time.end_time)}}</td>
                      <td style="text-align: left;">{{ time.hours }}</td>
                      <td>
                        <div class="select">
                          <select v-model="time.entries[0]['activities_timesheets_objetive_id']" @change="handleSelectChange">
                            <option v-for="(activity, index_activity_one) in item.activities" :key="index_activity_one" :value="activity.id">
                              {{ activity.name }}
                            </option>
                          </select>
                        </div>
                      </td>
                      <td>
                        <div class="select">
                          <select>
                            <option value="option1">Opción 1</option>
                            <option value="option2">Opción 2</option>
                            <option value="option3">Opción 3</option>
                          </select>
                        </div>
                      </td>
                    </tr>
                  </template>
                </tbody>
              </table>
            </div>
            <button class="btn btn-purple-ut" @click="saveData(item.times)" v-show="changes > 0" style="margin-top: 2%;">
              Guardar Cambios <i class="fa fa-save"></i>
            </button>
          </div>
        </div>

      </template>


    </div>
  </div>

  </template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";
import XLSX from "xlsx";
import { message } from 'ant-design-vue';

@Component
export default class UsePolicy extends Vue {
  data: any = null;
  screenWidth: any = '300px';
  changes: any = 0;


  async created() {
    this.screenWidth = (window.innerWidth - 85) + 'px';

    try {
      //array de registros importados
      const data = this.$store.state.data;

      await axios.post('/api/store/temporarily', {data, noProcesar: true});
      const response = await axios.get('/api/get/data/timesheetnew/temporarily');
      this.data = response.data;

    } catch (e) {
      console.log(e.message)
    }
  }

  async saveData(data: any){
    try {
      await axios.post('api/save-data-temporarily-activities',{data});
      message.success('Registros guardados correctamente');
    } catch (error) {
      message.error('Ha ocurrido un error');
    }
  }

  handleSelectChange(){
    this.changes += 1;
  }

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


  getHoursFormated(start: any, end: any){
    // Extraer horas y minutos de la hora de inicio
    const [horaInicioSinSegundos, minutosInicio] = start.split(":");

    // Extraer horas y minutos de la hora de fin
    const [horaFinSinSegundos, minutosFin] = end.split(":");

    // Combinar las horas y minutos en el formato deseado
    const horasCombinadas = `${horaInicioSinSegundos}:${minutosInicio}-${horaFinSinSegundos}:${minutosFin}`;

    return horasCombinadas;
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

.cf-style {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.cfp-style {
  padding: 2% 0%;
}

.select {
    position: relative;
    display: inline-block;
    width: 200px;
    height: 40px;
    line-height: 40px;
    background-color: #f0f0f0;
    border-radius: 5px;
}

.select select {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    padding: 0 20px;
    font-size: 14px;
    color: #666;
    background-color: transparent;
    border: none;
    border-radius: 5px;
    appearance: none;
    cursor: pointer;
}

.select select:focus {
    outline: none;
}

.select:after {
    position: absolute;
    top: 14px;
    right: 20px;
    width: 0;
    height: 0;
    padding: 0;
    content: '';
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-top: 5px solid #666;
    pointer-events: none;
}

.btn-purple-ut {
  background-color: white;
  color: #643fed;
  border-radius: 50px !important;
  border: 1px solid #643fed;
}

.btn-purple-ut:hover {
  background-color: #643fed;
  color: white;
  border-radius: 50px !important;
}
</style>
