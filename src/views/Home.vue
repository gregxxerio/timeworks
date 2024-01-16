<template>
  <div class="section phome" style="position: relative; z-index: 0;">
    <h1 class="section-title" @click="type = '';">Envío de informe</h1>
    <div class="card">
        <div class="card-header">
          <TitleHome :steps="steps" :step="step" />
        </div>
        <div class="card-body p-4" v-if="showComponents">
            <StepImport v-show="step == 1" @continueToNext="continueToNext" @continueToNextProcesedata="continueToNextProcesedata"/>
            <!-- <StepTimesheet v-if="step == 2"/> -->
            <StepTimesheetNew v-if="step == 2"/>
            <StepSendData v-if="step == 3"  @onAcceptTerms="onAcceptTerms" @onUpdateTotalHours="onUpdateTotalHours" />
        </div>
    </div>
    <div class="steep-buttons mt-3">
      <div class="flex-buttons-home">
        <div class="flex-buttons-home-izq">
          <button class="btn btn-purple" v-if="step > 1" @click="regresar">
            <i class="fa fa-arrow-left"></i> Regresar
          </button>
        </div>
        <div class="flex-buttons-home-der justify-content-end text-right">
          <button class="btn btn-purple" v-if="continueNextStepProcesedata && step!= 3" @click="continuar">
            Continuar <i class="fa fa-arrow-right"></i>
          </button>
          <div v-if="continueNextStep && step == 3">
            <rotate-square2 v-if="isLoading"></rotate-square2>
            <button v-else class="btn btn-purple" @click="storeRegisters">
              Enviar informe <i class="fa fa-paper-plane"></i>
            </button>
          </div>

        </div>
      </div>
    </div>
    <CustomModal :is-open="openModal" />
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from "vue-property-decorator";
import SideBar from "../components/SideBar.vue";
import Proxy from "../helpers/proxy";
import moment from "moment-timezone";
import axios from "axios";
import { RotateSquare2 } from "vue-loading-spinner";
import collect from "collect.js";
import { Modal } from "ant-design-vue";
import XLSX from "xlsx";
import MonthlyReportUser from "@/views/MonthlyReportUser.vue";
import MonthlyClosure from "@/views/MonthlyClosure.vue";
import TimeSheetHome from "@/views/TimeSheetHome.vue";
import TitleHome from "@/views/components/TitleHome.vue";
import StepImport from "@/views/components/Steps/StepImport.vue";
import StepTimesheet from "@/views/components/Steps/StepTimesheet.vue";
import StepTimesheetNew from "@/views/components/Steps/StepTimesheetNew.vue";
import StepSendData from "@/views/components/Steps/StepSendData.vue";
import CustomModal from "@/views/components/CustomModal.vue";

axios.defaults.url = Proxy.api.domain;
declare const gapi: any;

const eventBuss = new Vue({});
@Component({
  components: {
    CustomModal,
    StepSendData,
    StepTimesheet,
    StepTimesheetNew,
    StepImport,
    TitleHome,
    TimeSheetHome,
    MonthlyClosure,
    MonthlyReportUser,
    SideBar,
    RotateSquare2,
  },
})
export default class Index extends Vue {
  step = 1;
  steps = 2;
  hasTimesheet = false;
  continueNextStep = false;
  continueNextStepProcesedata = false;
  isLoading = false;
  checkForSending = false;
  hoursValues: any = null;
  openModal = false;
  showComponents = true;
  user: any = null;

  async created() {
    this.hasTimesheet = this["$store"].state.user.hasTimesheet;
    this.steps = this.hasTimesheet ? 3 : 2;
    this.user = this["$store"].state.user;
  }

  continueToNext(val: boolean) {
    this.continueNextStep = val || this.user.partial_report;
  }

  continueToNextProcesedata(val: boolean) {
    this.continueNextStepProcesedata = val || this.user.partial_report;
  }

  onAcceptTerms(val: boolean){
    this.checkForSending = val;
  }
  continuar() {
    if (this.step == 1) {
      this.step += this.hasTimesheet ? 1 : 2;
    }else{
      this.step +=  1;
    }
    axios.post("/api/logs", {
      text: "Click en continuar a paso "+this.step,
      description: JSON.stringify({ step : this.step }),
    });
  }

  regresar(){
    if (this.step == 3) {
      this.step -= this.hasTimesheet ? 1 : 2;
    }else{
      this.step -= 1;
    }
  }

  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title,
      content: "",
      okText: "Aceptar",
    });
  }

  onUpdateTotalHours(data: any){
    this.hoursValues = data;
  }

  async storeRegisters() {
    try {
      if (!this.checkForSending) {
        this.showAlert("Antes de enviar, debes aceptar las condiciones");
        return;
      }
      const dates: any = this.$store.state.data;

      axios.post("/api/logs", {
        text: "Click En enviar informe",
        description: JSON.stringify(this.hoursValues),
      });


      let result: any = await axios.post("/api/check-register-is-same-date", {
        data: dates,
      });

      if (result.data.exists) {
        const confirm = await this.$swal({
          title: "Ya tienes registros en esta fecha. ¿Deseas remplazarlos?",
          showCancelButton: true,
          confirmButtonText: `Aceptar`,
          cancelButtonText: `Cancelar`,
        });
        if (!confirm.isConfirmed) {
          axios.post("/api/logs", {
            text: "No acepto remplazar registros existentes",
            description: "",
          });
          return true;
        }
      }

      this.isLoading = true;

      result = await axios.post("/api/events/store", {
        data: dates,
        ...this.hoursValues
      });

      this.openModal = true;

      await axios.post("/api/logs", {
        text: "Envio de informe correctamente",
        description: " ",
      });

      this.isLoading = false;
      this.showComponents = false;
      this.$nextTick( ()=> {
        this.step = 1;
        this.showComponents = true;
      })
    } catch (e) {
      console.log(e);
      const success = Modal.error;
      this.isLoading = false;
      await axios.post("/api/logs", {
        text: "Envio de informe falló",
        description: JSON.stringify(e),
      });
      success({
        title: "La información no se ha podido guardar, intentelo más tarde o comuniquese con el administrador",
        content: "",
        okText: "Aceptar",
      });
    }
  }

}
</script>

<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.0.1/css/font-awesome.css"
  integrity="sha512-bf5lgyUrZOfPh94XyXFK4+2062lAMQFAfxUTVkOAHZ7R3XQ0tY+TUSkbqt8sjFsq0hVMKvGT/1P39c+vKwesTQ=="
  crossorigin="anonymous"
  referrerpolicy="no-referrer"
/>

<style>
  .card-header{
    border: 0 solid !important;
  }
  .card-body{
    border: 0 solid !important;
  }

  label.step {
    font-size: 1.2em;
    color: #50504f;
    text-align: left;
    vertical-align: middle;
    text-transform: none;
  }

  .btn-purple {
    background-color: #643fed;
    color: white;
    border-radius: 50px !important;
  }

  .switch input[type="checkbox"]:checked + .slider {
    background-color: #8fc02b !important;
  }

  .slider {
    width: 42px !important;
    height: 20px !important;
  }

  .slider::after {
    width: 14px !important;
    height: 14px !important;
  }

  .section.phome {
    padding: 1.5rem;
  }

  @media screen and (max-width: 970px) {
    .section.phome {
      padding: 0;
    }
  }

  .flex-buttons-home {
    display: flex;
                justify-content: space-between;
                align-items: center;
  }

  .flex-buttons-home-izq,
       .flex-buttons-home-der {
           padding: 10px 20px;
       }

</style>
