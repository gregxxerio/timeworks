<template>
  <div class="container-home-dos" style="background: #FFFFFF;">
    <div v-if="loadingRequestR" style="text-align: center;">
      <div class="loadinghistory">
      </div>
      <h2>Cargando...</h2>
    </div>
    <div class="table-responsive" v-else>
      <table class="table table-hover">
        <thead>
          <tr class="table-info">
            <th width="25%" scope="col" class="fh" style="color: #3d70b3;">Nombre del colaborador</th>
            <th width="20%" scope="col" class="fh" style="color: #3d70b3;">Tipo de ausencia</th>
            <th width="10%" scope="col" class="fh" style="color: #3d70b3;">Días solicitados </th>
            <th width="15%" scope="col" class="fh" style="color: #3d70b3;">Inicio</th>
            <th width="15%" scope="col" class="fh" style="color: #3d70b3;">Fin</th>
            <th width="20%" scope="col" class="fh" style="color: #3d70b3;">Respuesta</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(d, indexPrincipal) in data">
            <tr :class="opacity" v-show="openSquare != ('open' + d.id)" :key="indexPrincipal" :style="open == indexPrincipal ? 'background: #f0f6ff;' : ''">
              <td>
                <button class="btn btn-sm" v-show="open != indexPrincipal" @click="open = indexPrincipal;getUserSameDays(d.date_start, d.date_end)" style="background-color: transparent !important;padding: .25rem .3rem !important;">
                  <i class="fa fa-caret-right"></i>
                </button>
                <button class="btn btn-sm" v-show="open == indexPrincipal" @click="open = null;" style="background-color: transparent !important;padding: .25rem .3rem !important;">
                  <i class="fa fa-caret-down"></i>
                </button>
                {{d.name}}
              </td>
              <td>{{getTipoName(d.type)}}</td>
              <td>{{Number(d.days)}}</td>
              <td>{{d.date_start}}</td>
              <td>{{d.date_end}}</td>
              <td>
                <span class="new-check-circle" @click="openSquareResponse(d.id, 2)"></span>
                &nbsp;
                <span class="new-times-circle" @click="openSquareResponse(d.id, 3)"></span>
              </td>
            </tr>
            <tr :class="opacity" v-show="open == indexPrincipal && open != null && openSquare != ('open' + d.id)" :key="'child' + indexPrincipal" :style="open == indexPrincipal ? 'background: #f0f6ff;' : ''">
              <td colspan="6">
                <div class="card" style="background: transparent;border: none;">
                  <div class="card-body">
                    <div class="row">
                      <div class="col-md-12">
                        <h6>Comentario:</h6>
                        <p class="square">
                          {{ d.comment }}
                        </p>
                      </div>
                      <div class="col-md-12" v-if="d.additional_comment != null">
                        <h6>Información adicional:</h6>
                        <p class="square">
                          {{ d.additional_comment }}
                        </p>
                      </div>
                      <div class="col-md-12">
                        <div class="row">
                          <div class="col-md-6">
                            <h6>Supervisor(a):
                              <span style="font-weight: bold">
                                {{d.supervisor_name_s}}
                              </span>
                            </h6>
                          </div>
                          <div class="col-md-6">
                            <h6>Socio(a) responsable:
                              <span style="font-weight: bold">
                                {{getResponsiblePartner(d)}}
                              </span>
                            </h6>
                          </div>
                        </div>
                      </div>
                      <div class="col-md-12">
                        <h6>Otros usuarios ausentes:</h6>
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
            <tr v-show="openSquare == ('open' + d.id)" :key="'childTwo' + indexPrincipal">
              <td colspan="6">
                <div class="card">
                  <div class="content-header">
                    <span class="icono-x" @click="closeSquareResponse()"></span>
                  </div>
                  <div class="content-form">
                    <h6 style="color:rgb(91, 134, 189);">Respuesta para {{d.name}}:</h6>
                    <textarea name="name" v-model="supervisorNote"  class="form-control" rows="4" cols="80" placeholder="Escribir respuesta"></textarea>
                    <button class="bottom-request-vr" type="button" name="button" @click="sendResponse(d.id)">
                       Enviar Respuesta
                     </button>
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
import {Modal} from "ant-design-vue";

@Component({
  // components: {Requests, VacationHistory }
})

export default class Welcome extends Vue {
  @Prop({ default: 0 })
  private userId!: number;

  data: Array<any> = [];
  open = null;
  openSquare = 'close';
  sameDays = [];
  opacity = 'opacity';
  supervisorNote = '';
  type = 0;
  loadingRequestR = false;

  async mounted() {
    this.getData(this.userId);
  }

  async getData(id: any){
    this.loadingRequestR = true;
    const response = await axios.get('/api/get-list-request-new/' + id);
    this.data = response.data;
    this.loadingRequestR = false;
    this.$emit('count-request', response.data.length);
  }

  getTipoName(tipo: number) {
    switch (tipo) {
      case 1:
        return "Vacaciones";
      case 2:
        return "Vacaciones (medio turno)";
      case 3:
        return "Enfermedad";
        case 6:
          return "Enfermedad  (medio turno)";
      case 4:
        return "Otro";
      case 5:
        return "Días no pagados";
      default:
        return "";
    }
  }

  async getUserSameDays(start: any, end: any){
    const response = await axios.get('/api/get-user-same-days/' + start + '&' + end);
    this.sameDays = response.data;
  }

  getResponsiblePartner(data: any){
    const json = JSON.parse(data.supervisor_id);
    return json[0].name;
  }


  openSquareResponse(open: any, type: any) {
    this.openSquare = 'open' + open;
    this.opacity = 'opacity-active';
    this.type = type;
  }

  closeSquareResponse(){
    this.opacity = 'opacity';
    this.openSquare = 'close';
    this.type = 0;
  }

  sendResponse(idrequest: any){
    if (this.supervisorNote == null || this.supervisorNote == "") {
      this.showAlert("La respuesta del supervisor(a) es requerido");
      return;
    }
    const confirm = Modal.confirm;
    confirm({
      title: '¿Estás seguro(a) de '+ ((this.type == 2) ? 'aprobar' : 'rechazar') + ' la solicitud?',
      content: '',
      okText: 'OK',
      cancelText: 'Cancelar',
      onOk: () => {
        this.loadingRequestR = true;
        const resultado = axios.post('/api/admin-request-holiday',
        { id : idrequest,
          supervisorNote : this.supervisorNote,
          status : this.type
        });
          this.supervisorNote = '';
          this.opacity = 'opacity';
          this.openSquare = 'close';
        this.loadingRequestR = false;
        const success = Modal.success;
        this.getData(this.userId);
        success({
          title:  this.type == 2 ? 'Aprobado' : this.type == 3 ? 'Rechazado' : '',
          content: '',
          okText: 'Aceptar',
        });
      },
      onCancel: () => {
        console.log('e<');
      }
    });
  }

  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title ,
      content: '',
      okText: 'Aceptar',
    });
  }

}
</script>
<style media="screen">
.new-times-circle {
  margin: 0 2%;
  cursor: pointer;
       display: inline-block;
       width: 24px;
       height: 24px;
       border: 1px solid #d9534f; /* Color del círculo (rojo en este caso) */
       background-color: transparent; /* Color del círculo (rojo en este caso) */
       border-radius: 50%; /* Para hacer un círculo */
       color: #d9534f; /* Color del icono (blanco en este caso) */
       text-align: center;
       font-size: 16px;
       line-height: 24px;
   }

   .new-times-circle:before {
       content: "\00D7"; /* Código Unicode para la X */
       font-family: Arial, sans-serif; /* Puedes ajustar la fuente según tus preferencias */
   }

   .new-check-circle {
     margin: 0 2%;
     cursor: pointer;
    display: inline-block;
    width: 24px;
    height: 24px;
    background-color: transparent; /* Color del círculo (verde en este caso) */
    border: 1px solid #5cb85c;
    border-radius: 50%; /* Para hacer un círculo */
    color: #5cb85c; /* Color del icono (blanco en este caso) */
    text-align: center;
    font-size: 16px;
    line-height: 24px;
  }

.new-check-circle:before {
    content: "\2713"; /* Código Unicode para la marca de verificación */
    font-family: Arial, sans-serif; /* Puedes ajustar la fuente según tus preferencias */
}


.opacity {
  opacity: 1;
}

.opacity-active {
  opacity: 0.2;
}

.icono-x {
    display: inline-block;
    width: 20px;
    height: 20px;
    position: relative;
    cursor: pointer;
}

.icono-x::before,
.icono-x::after {
    content: "";
    position: absolute;
    height: 2px;
    width: 100%;
    background-color: #333; /* Color de la línea de la "x" */
    top: 50%;
    transform: translateY(-50%);
}

.icono-x::before {
    transform: rotate(45deg);
}

.icono-x::after {
    transform: rotate(-45deg);
}

.content-header {
  text-align: end;
    width: 94%;
    padding: 1% 0%;
}

.content-form {
  padding: 1% 6% 2% 6%;
}

.bottom-request-vr {
  margin-top: 1%;
  background: #310981 !important;
  padding: 0.4% 4% !important;
  color: #fff !important;
  /* border: 1px solid #310981 !important; */
  border: none;
  border-radius: 10px;
  position:relative;
  left: 75%;
  cursor: pointer;
  display: flex;
  flex-direction: row;
  /* width: 28%; */
  align-items: center;
  justify-content: center;
}

.bottom-request-vr:hover {
  border: none !important;
}

</style>
