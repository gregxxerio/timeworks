<template>
  <div class="card">
    <div class="card-body">
      <div v-if="loadingRequestVP" style="text-align: center;">
        <div class="loadinghistory"></div>
        <h2>Cargando...</h2>
      </div>
      <div v-else>
        <div v-if="needActive(vacationabsencesdetail)" class="row d-flex align-items-center justify-content-around">
          <div>
            <h5>Días Generados: <b>0</b></h5>
          </div>
          <div>
            <button @click="activeVacationByYear(vacationabsencesdetail)" style="border-radius: 10px; cursor: pointer; background-color: #01408D; color: white; padding: 10px 20px; border: none;">
              Activar Vacaciones
            </button>
          </div>
        </div>
        <div v-else>
          <div class="row">
            <div class="col-md-12">
              <h5 style="color: #01408D">Días generados</h5>
            </div>
          </div>
          <div class="row d-flex align-items-center">
            <div class="col-md-3">
              <h6>Vacaciones Fijas</h6>
            </div>
            <div class="col-md-3">
              <input type="text" class="form-control" :value="Number(vacationabsencesdetail.permanent_holidays) + ' días' " v-if="!editingVacation" disabled>
              <input type="number" class="form-control" v-model="vacationabsencesdetail.permanent_holidays" v-else>
            </div>
            <div class="col-md-3">
              <h6>Inicio de cuenta</h6>
            </div>
            <div class="col-md-3">
              <input type="date" class="form-control" v-model="datein" disabled readonly>
            </div>
          </div>
          <div class="row d-flex align-items-center mt-3">
            <div class="col-md-3">
              <h6>Vacaciones Flexibles</h6>
            </div>
            <div class="col-md-3">
              <input type="text" class="form-control" :value="Number(vacationabsencesdetail.flexible_holidays) + ' días'" readonly disabled v-if="!editingVacation">
              <input type="number" class="form-control" v-model="vacationabsencesdetail.flexible_holidays" v-else>

            </div>
            <div class="col-md-3">
              <h6>Vigencia</h6>
            </div>
            <div class="col-md-3">
              <input type="date" class="form-control" v-model="vacationabsencesdetail.expiration_date" :disabled="!editingVacation">
            </div>
          </div>
          <div class="row">
            <div class="col-md-12 d-flex align-items-center">
              <div style="cursor: pointer" v-if="editingVacation" @click="addDaysReposition(vacationabsencesdetail.id)" class="btn-circle-vacation d-flex justify-content-center align-items-center mr-2">
                +
              </div>
              <h5 style="color: #01408D">Días de reposición  <b>{{ Number(getReplacemnetDay(vacationabsencesdetail.vacationabsencesreplacementdays))}}</b></h5>
            </div>
          </div>
          <div v-for="(vard, index) in vacationabsencesdetail.vacationabsencesreplacementdays"  :key="vard.id">
            <div class="row d-flex align-items-center mt-3">
              <template v-if="editingVacation">
                <div class="col-md-4">
                  <div class="input-group">
                    <div class="input-group-prepend" style="cursor:pointer;" @click="deleteDayReplacement(vacationabsencesdetail, index, vard.id)">
                      <span class="input-group-text" id="basic-addon1"><i class="fas fa-trash"></i></span>
                    </div>
                    <input type="text" class="form-control" v-model="vard.note" placeholder="Escriba aqui su nota">
                  </div>
                </div>
                <div class="col-md-3">
                  <div class="input-group">
                    <div class="input-group-prepend">
                      <select class="custom-select" v-model="vard.type" id="inputGroupSelect01">
                        <option value="1">Sumar días</option>
                        <option value="2">Restar días</option>
                      </select>
                    </div>
                    <input type="number" class="form-control" v-model="vard.days" >
                  </div>
                </div>
                <div class="col-md-2">
                  <h6>Vigencia</h6>
                </div>
                <div class="col-md-3">
                  <input type="date" class="form-control" v-model="vard.validity">
                </div>
              </template>
              <template v-else>
                <div class="col-md-3">
                  <h6>{{vard.note}}</h6>
                </div>
                <div class="col-md-3">
                  <input type="text" class="form-control" :value="((vard.type == 1) ? '+ ' : '- ') + vard.days" readonly disabled>
                </div>
                <div class="col-md-3">
                  <h6>Vigencia</h6>
                </div>
                <div class="col-md-3">
                  <div v-if="verificarValidez(vard.validity)">

                  </div>
                  <div class="">

                  </div>
                  <input type="date" class="form-control" :value="vard.validity" readonly disabled>
                </div>
              </template>
            </div>
          </div>
          <div class="row">
            <div class="col-md-12 mt-2 pl-4 pr-4">
              <div class="row pl-4 pr-4 d-flex align-items-center" style="background-color: #01408D;">
                <div class="col-md-4">
                  <h6 style="color: white">Total de días flexibles: </h6>
                </div>
                <div class="col-md-8">
                  <h6 style="color: white">{{ calculateReplacementDaysByYear(vacationabsencesdetail) + (Number(getReplacemnetDay(vacationabsencesdetail.vacationabsencesreplacementdays))) }} Días</h6>
                </div>
              </div>
            </div>
          </div>
          <div class="row d-flex justify-content-end mt-2 mr-2">
            <!-- <button class="btn btn-vacation ml-2" ></button> -->
            <button class="btn btn-vacation ml-2" @click="cancelEditVacation" v-if="editingVacation">Cancelar</button>
            <button class="btn btn-vacation ml-2" @click="saveChangesVacation(vacationabsencesdetail.vacationabsencesreplacementdays)" v-if="editingVacation">Guardar Cambios</button>
            <button class="btn btn-vacation ml-2" @click="editVacation" v-else>Editar</button>
          </div>

        </div>
      </div>

    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop ,Vue, Emit } from 'vue-property-decorator';
import axios from "axios";
import moment from "moment/moment";
import { Modal } from 'ant-design-vue';


@Component
export default class VacationPersonalization extends Vue {
  @Prop({ default: null })
  private vacationabsencesdetail!: object;

  @Prop({ default: moment().year() })
  private year!: number;

  @Prop({ default: 0 })
  private userId!: number;

  @Prop({ default: '' })
  private datein!: string;


  vacationabsencesreplacementdays = [];

  editingVacation = false;
  loadingRequestVP = false;
  data = [];

  async activeVacationByYear(data: any){
    // await axios.get("/api/active-vacation-user/" + this.year+"&"+this.userId);
    await axios.get("/api/complete-days/" + this.userId);
    this.getUsersActive();
  }

  async getUsersActive() {
    const response = await axios.get("/api/get-list-users-active");
    this.$emit('loadDataGral');
    this.data = response.data;
  }

  needActive(data: any) {
    if (data != null) {
          return false;
    }
    return true;
  }


  calculateReplacementDaysByYear(data: any){
        let total =0;
        // total = Number(data.flexible_holidays) + Number(data.permanent_holidays)
        total = Number(data.flexible_holidays);
        return total;
  }

  editVacation(){
    this.editingVacation = true;
  }

  cancelEditVacation(){
    this.editingVacation = false;
  }

  addDaysReposition(id: any){
        (this.vacationabsencesdetail['vacationabsencesreplacementdays']).push({
          id : 0,
          'vacation_absences_detail_id' : id,
          days : "",
          type : "1",
          note : "",
          validity: null
        });
  }

  successMsm(msm: any){
    const success = Modal.success;
    success({
      title: msm,
      content: "",
      okText: "Aceptar",
    });
  }

 deleteDayReplacement(data: any, index: any, id: any ){
    const confirm = Modal.confirm;
    confirm({
      title: '¿Estás seguro(a) de Eliminar?',
      content: '',
      okText: 'OK',
      cancelText: 'Cancelar',
      onOk: () => {
            if (this.vacationabsencesdetail['vacationabsencesreplacementdays'][index].id == 0) {
               (this.vacationabsencesdetail['vacationabsencesreplacementdays']).splice(index, 1);
            }
            else{
              this.loadingRequestVP = true;
              const request = axios.get("/api/delete-replacement-days-new/" + id);
              (this.vacationabsencesdetail['vacationabsencesreplacementdays']).splice(index, 1);
              this.loadingRequestVP = false;
              this.successMsm("Registro eliminado");
            }
      },
      onCancel: () => {
        console.log('e<');
      }
    });


  }

  async saveChangesVacation(data: any){
    this.loadingRequestVP = true;
    const result: any = await axios.post("/api/admin-save-changes-holiday", this.vacationabsencesdetail );
    if (result.data) {
      this.vacationabsencesdetail = result.data;
      this.loadingRequestVP = false;
      this.editingVacation = false;
      this.successMsm("información actualizada");
    }

  }

  getReplacemnetDay(data: any){
    if (data.length != 0) {
      let day = 0;
      data.forEach(element => {
        if (element.type == 1) {
          day += Number(element.days);
        }else{
          day -= Number(element.days);
        }
      });
      return day;
    }
    return 0;

  }



}

</script>
