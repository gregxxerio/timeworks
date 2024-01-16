<template>
  <div class="row w-100 p-0">
    <div class="col-md-12 text-left">
      <div class="row d-flex align-items-center">
        <arrow-up-icon v-if="!isOpened" @on-click="toggleOpened" />
        <arrow-down-icon v-if="isOpened" @on-click="toggleOpened" />
        <b style="margin-left: 10px">Repositorio Documental</b>
      </div>
      <div class="row mt-2" v-if="isOpened">
        <div id="accordion">
          <div class="card">
            <div class="card-header" id="headingOne">
              <a
                class="item collapsed"
                data-toggle="collapse"
                data-target="#collapseOne"
                aria-expanded="true"
                aria-controls="collapseOne"
              >
                Politica de registros de tiempo
              </a>
            </div>

            <div
              id="collapseOne"
              class="collapse show"
              aria-labelledby="headingOne"
              data-parent="#accordion"
            >
              <div class="card-body">
                <ul>
                  <li>
                    <a
                      target="_blank"
                      href="/pdf/PolíticaRegistroTiempos_C230_Proyectos.pdf"
                      ><arrow-icon /> Proyectos C230</a
                    >
                  </li>
                  <li>
                    <a
                      target="_blank"
                      href="/pdf/PolíticaRegistroTiempos_IDEA_Proyectos.pdf"
                    >
                      <arrow-icon /> Proyectos IDEA</a
                    >
                  </li>
                  <li>
                    <a
                      target="_blank"
                      href="/pdf/PolíticaRegistroTiempos_C230_Administrativos.pdf"
                    >
                      <arrow-icon /> Administrativos C230</a
                    >
                  </li>
                  <li>
                    <a
                      target="_blank"
                      href="/pdf/PolíticaRegistroTiempos_IDEA_Administrativos.pdf"
                      ><arrow-icon /> Administrativos IDEA</a
                    >
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <div class="card">
            <div class="card-header" id="headingTwo">
              <a
                class="item collapsed"
                data-toggle="collapse"
                data-target="#collapseTwo"
                aria-expanded="false"
                aria-controls="collapseTwo"
              >
                Catalogos de códigos
              </a>
            </div>
            <div
              id="collapseTwo"
              class="collapse"
              aria-labelledby="headingTwo"
              data-parent="#accordion"
            >
              <div class="card-body">
                <ul>
                  <li>
                    <a
                      target="_blank"
                      href="https://docs.google.com/spreadsheets/d/1JVyopkCjXnaiFEgrb1T7Y6cnyrFC5NqY06X-2-012Mc/edit?usp=sharing"
                      ><arrow-icon /> Catálogo</a
                    >
                  </li>
                </ul>
              </div>
            </div>
          </div>
          <div class="card">
            <div class="card-header" id="headingThree">
              <a
                class="item collapsed"
                data-toggle="collapse"
                data-target="#collapseThree"
                aria-expanded="false"
                aria-controls="collapseThree"
              >
                Manuales
              </a>
            </div>
            <div
              id="collapseThree"
              class="collapse"
              aria-labelledby="headingThree"
              data-parent="#accordion"
            >
              <div class="card-body">
                <ul>
                  <li>
                    <a target="_blank" href="/pdf/Manual de uso TimeWorks- Junio 2021.pdf">
                      <arrow-icon /> Manual de usuario 2021
                    </a>
                  </li>
                  <li>
                    <a target="_blank" href="/pdf/Timeworks- Manual reporte parcial.pdf">
                      <arrow-icon /> Manual de reporte parcial
                    </a>
                  </li>
                  <li>
                    <a target="_blank" href="/pdf/Manual Token.pdf">
                      <arrow-icon /> Manual token
                    </a>
                  </li>
                  <li>
                    <a target="_blank" href="#">
                      <arrow-icon /> Manual vacaciones- user general
                    </a>
                  </li>
                  <li>
                    <a target="_blank" href="/pdf/TimeWorks- Socios.pdf">
                      <arrow-icon /> Manual vacaciones- socios responsables
                    </a>
                  </li>
                  <li>
                    <a target="_blank" href="/capacitacion">
                      <arrow-icon /> Capacitación
                    </a>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import ArrowIcon from "./ArrowIcon.vue";
import { Component, Vue } from "vue-property-decorator";
import ArrowUpIcon from "@/views/components/home/ArrowUpIcon.vue";
import ArrowDownIcon from "@/views/components/home/ArrowDownIcon.vue";
@Component({
  components: {
    ArrowIcon,
    ArrowUpIcon,
    ArrowDownIcon,
  },
})
export default class DocsItem extends Vue {
  isOpened = true;
  toggleOpened() {
    this.isOpened = !this.isOpened;
  }
}
</script>
<style scoped>
ul {
  padding: 0 !important;
}
ul li {
  list-style: none;
  padding: 0.1rem;
}
li a {
  color: #50504f;
  display: flex;
  align-items: center;
}

.card{
  border-radius: 10px;
}

.card-header{
  border: 0px;
}

.card-body {
  border: 0px;
}

a.item{
  background-color: transparent !important;
}

.card-header{
  background-color: transparent !important;
}
</style>
