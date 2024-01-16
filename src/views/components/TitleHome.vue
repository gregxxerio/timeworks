<script lang="ts">
import {Vue, Component, Prop } from 'vue-property-decorator';

@Component({})
export default class TitleHome extends Vue {
  @Prop({ default: 1 }) private step!: number;
  @Prop({ default: 2 }) private steps!: number;

  stepsLabels = {
    1 : '01 Importación de calendarios y desglose de registros',
    2 : '02 Organización de registros para Timesheets',
    3 : '03 Cierre del informe mensual'
  };

  stepsLabelsList = {
    1 : '01 Importación de calendarios y desglose de registros',
    3 : '02 Cierre del informe mensual'
  };

}
</script>

<template>
  <div class="home d-flex flex-column">
    <template v-if="$store.state.user.hasTimesheet">
      <span>{{ stepsLabels[step] }}</span>
    </template>
    <template v-else>
      <span>{{ stepsLabelsList[step] }}</span>
    </template>
      <div class="steps mt-2 d-flex justify-content-between">
          <div class="flex-fill" :class="{ 'step-active' : step == 1 , 'step-past' : step > 1}"></div>
          <div v-if="steps == 3" class="flex-fill" :class="{ 'step-active' : step == 2, 'step-past' : step > 2}"></div>
          <div class="flex-fill" :class="{ 'step-active' : step == 3, 'step-past' : step > 3}"></div>
      </div>
  </div>
</template>

<style scoped lang="scss">

  div.home span{
    font-size: 1.5em;
    color: #3A4343;
    font-weight: bold;
  }

  div.steps{
    border-bottom: 6px solid silver;
    position: relative;
    gap: 12px;
  }


  .step-past{
    position: relative;
    display: block;
    bottom: 0px;
    margin-bottom: -8px;
    border: 5px solid #8fc02b;
  }

  .step-active{
    position: relative;
    display: block;
    bottom: 0px;
    margin-bottom: -8px;
    border: 5px solid #006560;
  }



</style>
