<script lang="ts">
import {Vue, Component} from 'vue-property-decorator';
import MonthlyClosure from "@/views/MonthlyClosure.vue";

@Component({
  components: {MonthlyClosure}
})
export default class StepSendData extends Vue {

  acceptTerms = false;

  private mounted() {
    this.$watch('acceptTerms', () => {
      this.$emit('onAcceptTerms', this.acceptTerms);
    });
  }

  updateValues(data: any){
    this.$emit('onUpdateTotalHours', data);
  }


}
</script>

<template>
  <div>
    <label class="step">
      Verifica que no cuentes con registros en códigos no reconocidos, que el tiempo registrado corresponda a la distribución prevista
(distribución por proyecto / actividad, distribución por organización).
      <!-- Las siguientes gráficas desglosan los registros importados por categoría y código de proyecto. Si no hay errores o ajustes, validar la consistencia de los registros y reporte. -->
    </label>

    <monthly-closure @update-values="updateValues"></monthly-closure>

    <div>
      <div class="custom-control custom-checkbox">
        <input
            type="checkbox"
            class="custom-control-input"
            id="customCheck1"
            v-model="acceptTerms"
        />
        <label class="custom-control-label check-policy" for="customCheck1">
          <strong style="font-size: 0.9em"
          >Manifiesto que las actividades y tiempos registrados en mis
            calendarios e importados en esta aplicación son consistentes con lo
            realizado durante el período que se reporta</strong
          >
        </label>
      </div>
    </div>

  </div>
</template>

<style scoped>

</style>
