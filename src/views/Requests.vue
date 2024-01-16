<template >
  <div class="card card-request">
  <div class="table-responsive" >
    <table class="table table-hover table-bordered ">
      <thead>
        <tr class="table-info">
          <th scope="col" style="color: #3d70b3;">Tipo de ausencia</th>
          <th scope="col" style="color: #3d70b3;">Días solicitados</th>
          <th scope="col" style="color: #3d70b3;">Inicio</th>
          <th scope="col" style="color: #3d70b3;">Fin</th>
          <th scope="col" width="24%" style="color: #3d70b3;">Estatus</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="(r, index) in requests" >
          <tr :key="r.id" v-if="r.vacation_absences_detail_id == idDetail">
            <!-- -->
            <td style="cursor:pointer;" >
              <button class="btn btn-sm" v-show="open != index" @click="open = index;getUserSameDays(r.date_start, r.date_end)" style="background-color: transparent !important;padding: .25rem .3rem !important;">
                <i class="fa fa-caret-right"></i>
              </button>
              <button class="btn btn-sm" v-show="open == index" @click="open = null;" style="background-color: transparent !important;padding: .25rem .3rem !important;">
                <i class="fa fa-caret-down"></i>
              </button>
              {{ getTipoName(r.type) }}
            </td>
            <td style="text-align: center;">{{ Number(r.days) }}</td>
            <td>{{ r.date_start }}</td>
            <td>{{ r.date_end }}</td>
            <td><span :class="'btn-status-'+r.status">{{ getSingleStatus(r.status) }}</span></td>
          </tr>
          <tr :key="'child'+index" v-show="open == index && open != null">
            <td colspan="5">
              <div class="card">
                  <div class="card-body">
                    <div class="row">
                      <div class="col-md-12">
                        <h6 class="title-form-style">Comentario:</h6>
                        <p class="square">
                          {{ r.comment }}
                        </p>
                      </div>
                      <div class="col-md-12 mt-2" v-if="r.additional_comment != null">
                        <h6 class="title-form-style">Información adicional:</h6>
                        <p class="square">
                          {{ r.additional_comment }}
                        </p>
                      </div>
                      <div class="col-md-12 mt-2">
                        <div class="row">
                          <div class="col-md-6">
                            <h6 class="title-form-style">Supervisor(a):
                              <span style="font-weight: bold;color: #000;" v-for="su in supervisors"  :key="su.id">
                                {{su.name}}
                              </span>
                            </h6>
                          </div>
                          <div class="col-md-6">
                            <h6 class="title-form-style">Socio(a) responsable:
                              <span style="font-weight: bold; color: #000;">
                                {{getResponsiblePartner(r)}}
                              </span>
                            </h6>
                          </div>
                        </div>
                      </div>
                      <div class="col-md-12 mt-2">
                        <h6 class="title-form-style">Notas del socio responsable:</h6>
                        <p class="square">
                          {{ r.supervisor_note }}
                        </p>
                      </div>
                      <div class="col-md-12 mt-2">
                        <h6 class="title-form-style">Otros usuarios ausentes:</h6>
                        <p class="square" style="color: #000;">
                          <span v-for="sd in sameDays" :key="sd.id">
                              {{sd.name}}, {{sd.fecha}} <br>
                          </span>
                        </p>
                      </div>
                    </div>
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

@Component({
  // components: { VacationHistory, VacationPersonalization },
})

export default class Welcome extends Vue {
  @Prop({ default: null })
  private requests!: object;
  @Prop({ default: 0 })
  private years!: number;
  @Prop({ default: 0 })
  private idDetail!: number;
  @Prop({ default: null })
  private supervisors!: object;

  open = null;
  sameDays = [];

  getTipoName(tipo: number) {
    switch (tipo) {
      case 1:
        return "Vacaciones";
      case 2:
        return "Vacaciones (medio turno)";
      case 3:
        return "Enfermedad";
        case 6:
          return "Enfermedad (medio turno)";
      case 4:
        return "Otro";
      case 5:
        return "Días no pagados";
      default:
        return "";
    }
  }

  getSingleStatus(status: number) {
    if (status == 2){
      return "Aprobado";
    }
    if (status == 3) {
      return "Rechazado";
    }
    return "En Revisión";
  }

  getResponsiblePartner(data: any){
    const json = JSON.parse(data.supervisor_id);
    return json[0].name;
  }

  async getUserSameDays(start: any, end: any){
    const response = await axios.get('/api/get-user-same-days/' + start + '&' + end);
    this.sameDays = response.data;
  }

}
</script>
<style media="screen">
  .card-request {
    box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    padding: 2% 6%;
  }
  .title-form-style {
    color:rgb(108, 107, 108);
    font-weight: 600;
  }
</style>
