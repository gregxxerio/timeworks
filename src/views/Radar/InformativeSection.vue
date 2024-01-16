<template>
  <!-- <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div> -->

    <div class="page-wrapper" style="margin-left: 0px">
      <br>
      <br>
        <div class="container-fluid">
            <div class="row">
              <div class="col-md-1">

              </div>
            <div class="col-md-6">
              <div class="card">
                  <div class="card-body" style="text-align: left;">
                      <h5 style="text-align: left;" class="card-title">Gobierno de México</h5>

                      <p style="text-align: left;">Página del gobierno de México en la que encontrarás artículos científicos, datos
                      abiertos, cursos de capacitación sobre la COVID-19, infografías, entre otros.</p>
                      <a href="https://coronavirus.gob.mx/" style="width: auto;" target="_blank" class="badge badge-success">coronavirus.gob.mx</a>
                  </div>
              </div>
              <div class="card">
                  <div class="card-body" style="text-align: left;">
                      <h5 style="text-align: left;" class="card-title">Centros para el Control y la Prevención de Enfermedades (CDC)</h5>

                      <p style="text-align: left;">Página de los Centros para el Control y la Prevención de Enfermedades de Estados
                          Unidos en donde encontrarás cuáles son los síntomas de la COVID-19, medidas de
                          prevención, medidas de cuidado, entre otros.</p>
                      <a   target="_blank" style="width: auto;" href="https://espanol.cdc.gov/coronavirus/2019-ncov/index.html?CDC_AA_refVal=https%3A%2F%2Fwww.cdc.gov%2Fcoronavirus%2F2019-ncov%2Findex-sp.html" class="badge badge-success">cdc.gov/coronavirus/2019-ncov/index-sp.html</a>
                  </div>
              </div>
              <div class="card">
                  <div class="card-body" style="text-align: left;">
                      <h5 style="text-align: left;" class="card-title">Organización Mundial de la Salud (OMS).
                          Preguntas y respuestas sobre la enfermedad por coronavirus (COVID-19)</h5>

                      <p style="text-align: left;">Página oficial de la Organización Mundial de la Salud que te proporciona una sección de
                          preguntas y respuestas, consejos para la población acerca de los rumores, consejos
                          para viajes, entre otras.</p>
                      <a  target="_blank" style="width: auto;" href="https://www.who.int/es/emergencies/diseases/novel-coronavirus-2019/advice-for-public/q-a-coronaviruses" class="badge badge-success">who.int/es/emergencies/diseases/novel-coronavirus-2019/advice-for-public/q-a-
coronaviruses</a>
                  </div>
              </div>
            </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class InformativeSection extends Vue {
  created() {
    console.log('Informative');

  }
}

</script>
