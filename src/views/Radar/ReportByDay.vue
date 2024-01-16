<template>

    <div class="page-wrapper" >
      <div class="container-fluid" >
        <div class="row">
          <div class="col-md-12" style="background: #d8d8d8">
            <br />

            <div class="row">
              <div class="col-md-12">
                <label style="
                color: #221a54;
                line-height: 19px;
                font-size: 22px;
                ">Reporte del {{date}}</label>
                <br>
              </div>
              <div class="col-md-5">
                <br>
                <input type="date" class="form-control" v-model="date" @change="initialData" @click="filter = 0">
              </div>
              <!-- <div class="col-md-12" v-show="userName == 'Héctor Medina Vega'">
                <input type="date" class="form-control" v-model="date_uno">
                <input type="date" class="form-control" v-model="date_dos">
                <button type="button" @click="initialDataFixed()">Calcular</button>
              </div> -->
            </div>
            <h2><!-- Infome {{currentMonth}} --></h2>
          </div>
          <div class="col-md-12">
            <div class="table-responsive">
              <table class="table">
                <thead>
                  <th
                  style="
                  border-bottom-color: #221a54;
                  border-width: 3px;

                  color: #221a54;
                  line-height: 19px;
                  font-size: 19px;
                  border-bottom-style: solid;
                  "
                  >
                  Nombre
                </th>
                <th
                style="
                font-size: 14px;
                border-bottom-style: solid;
                border-width: 1px;
                "
                >
                Autocheck &nbsp;
                <button class="img-filter">
                  <img
                  src="/assets/Icons/filtro.png"
                  class="img-fluid"
                  style="width: 20px; margin-left: 3px"
                  />

                </button>

                <nav class="menu" >
                  <ul>
                    <li>
                      <button @click="filter = 6">
                        <img
                        src="/assets/Icons/sin registro.png"
                        class="img-fluid"
                        style="width: 16px; margin-left: -5px"
                        />&nbsp;Sin registro
                      </button>
                    </li>
                    <li>
                      <button @click="filter = 4">
                        <img
                        src="/assets/Icons/verde.png"
                        class="img-fluid"
                        style="width: 16px; margin-left: -5px"
                        />&nbsp;Verde (ingreso)
                      </button>
                    </li>
                    <li>
                      <button @click="filter = 3">
                        <img
                        src="/assets/Icons/amarillo.png"
                        class="img-fluid"
                        style="width: 16px; margin-left: -5px"
                        />&nbsp;Amarillo
                      </button>
                    </li>
                    <li>
                      <button @click="filter = 2">
                        <img
                        src="/assets/Icons/naranja.png"
                        class="img-fluid"
                        style="width: 16px; margin-left: -5px"
                        />&nbsp;Naranja
                      </button>
                    </li>
                    <li>
                      <button @click="filter = 1">
                        <img
                        src="/assets/Icons/rojo.png"
                        class="img-fluid"
                        style="width: 16px; margin-left: -5px"
                        />&nbsp;Rojo
                      </button>
                    </li>


                  </ul>
                </nav>


              </th>

              <!-- <th
              style="
              font-size: 14px;
              border-bottom-style: solid;
              border-width: 1px;
              "
              >
              Check recepción
            </th> -->
            <!-- <th
            style="
            font-size: 14px;
            border-bottom-style: solid;
            border-width: 1px;
            "
            >
            Ingreso
          </th> -->
          <th
            style="
              font-size: 14px;
              border-bottom-style: solid;
              border-width: 1px;
            "
          >
            Reset
          </th>
        </thead>
        <tbody>
          <tr v-for="(info, index) in infoByDay" :key="index">
            <template v-if="!(date > info.date_down)">

            <template v-if="filter == 0">
              <td
              style="font-size: 15px; color: #2d3740"
              >
              {{info.name}}
              <br>
            </td>
            <center>
              <td>
                <template v-if="info.evaluation_name == 'sin registro'">
                  <img
                  src="/assets/Icons/sin registro.png"
                  class="img-fluid"
                  style="width: 20px; margin-left: -5px"
                  />
                </template>
                <template v-if="info.evaluation_name == 'Rojo'">
                  <img
                  src="/assets/Icons/rojo.png"
                  class="img-fluid"
                  style="width: 20px; margin-left: -5px"
                  />
                </template>
                <template
                v-else-if="info.evaluation_name == 'Naranja'"
                >
                <img
                src="/assets/Icons/naranja.png"
                class="img-fluid"
                style="width: 20px; margin-left: -5px"
                />
              </template>
              <template
              v-else-if="info.evaluation_name == 'Amarillo'"
              >
              <img
              src="/assets/Icons/amarillo.png"
              class="img-fluid"
              style="width: 20px; margin-left: -5px"
              />
            </template>
            <template v-else-if="info.evaluation_name == 'Verde'">
              <img
              src="/assets/Icons/verde pre.png"
              class="img-fluid"
              style="width: 20px; margin-left: -5px"
              />
            </template>
          </td>
        </center>
        <!-- <td class="text-center">
          <template v-if="info.evaluation_results_confirm_id == 5">
            <a-switch :style="info.evaluation_results_confirm_id == 5 ? 'background-color:#4bb774 !important;' : info.evaluation_results_confirm_id == 2 ? 'background-color:#fa6400 !important;' : ''"
            @change="onChange(info.id, 'swtchButton2')"
            v-model="swtchButton2"
            readonly
            />
          </template>
          <template v-else>
            <a-switch :style="info.evaluation_results_confirm_id == 5 ? 'background-color:#4bb774 !important;' : info.evaluation_results_confirm_id == 2 ? 'background-color:#fa6400 !important;' : ''"
            @change="onChange(info.id, 'swtchButton')"
            v-model="swtchButton"
            />
          </template>
        </td>
        <td class="text-center">
          <template v-if="info.evaluation_results_confirm_id == 5">
            <img
            src="/assets/Icons/verde.png"
            class="img-fluid"
            style="width: 16px; margin-left: -5px"
            />
          </template>
          <template v-if="info.evaluation_results_confirm_id == 2">
            <img
            src="/assets/Icons/naranja.png"
            class="img-fluid"
            style="width: 16px; margin-left: -5px"
            />
          </template>
          <template v-if="!info.evaluation_results_confirm_id">
            <img
            src="/assets/Icons/sin registro.png"
            class="img-fluid"
            style="width: 16px; margin-left: -5px"
            />
          </template>
        </td> -->
        <td class="text-center">

                <img
                @click="resetStatus(info.id)"
                 src="/assets/Icons/reset.png"
                 class="img-fluid"
                 style="width: 16px; margin-left: -5px; cursor: pointer;"/>

             </td>
      </template>
      <template v-if="filter == info.evaluation_results_id">
        <td
        style="font-size: 15px; color: #2d3740"
        >
        {{ info.user.name }}
      </td>
      <center>
        <td>
          <template v-if="info.evaluation_name == 'sin registro'">
            <img
            src="/assets/Icons/sin registro.png"
            class="img-fluid"
            style="width: 20px; margin-left: -5px"
            />
          </template>
          <template v-if="info.evaluation_name == 'Rojo'">
            <img
            src="/assets/Icons/rojo.png"
            class="img-fluid"
            style="width: 20px; margin-left: -5px"
            />
          </template>
          <template
          v-else-if="info.evaluation_name == 'Naranja'"
          >
          <img
          src="/assets/Icons/naranja.png"
          class="img-fluid"
          style="width: 20px; margin-left: -5px"
          />
        </template>
        <template
        v-else-if="info.evaluation_name == 'Amarillo'"
        >
        <img
        src="/assets/Icons/amarillo.png"
        class="img-fluid"
        style="width: 20px; margin-left: -5px"
        />
      </template>
      <template v-else-if="info.evaluation_name == 'Verde'">
        <img
        src="/assets/Icons/verde pre.png"
        class="img-fluid"
        style="width: 20px; margin-left: -5px"
        />
      </template>
    </td>
  </center>
  <td >
    <center v-if="info.evaluation_results_confirm_id == 5">
      <a-switch :style="info.evaluation_results_confirm_id == 5 ? 'background-color:#4bb774 !important;' : info.evaluation_results_confirm_id == 2 ? 'background-color:#fa6400 !important;' : ''"
      @change="onChange(info.id, 'swtchButton2')"
      v-model="swtchButton2"
      />
    </center>
    <center v-else>
      <a-switch :style="info.evaluation_results_confirm_id == 5 ? 'background-color:#4bb774 !important;' : info.evaluation_results_confirm_id == 2 ? 'background-color:#fa6400 !important;' : ''"
      @change="onChange(info.id, 'swtchButton')"
      v-model="swtchButton"
      />
    </center>
  </td>
  <td >
    <center v-if="info.evaluation_results_confirm_id == 5">
      <img
      src="/assets/Icons/verde.png"
      class="img-fluid"
      style="width: 16px; margin-left: -5px"
      />
    </center>
    <center v-if="info.evaluation_results_confirm_id == 2">
      <img
      src="/assets/Icons/naranja.png"
      class="img-fluid"
      style="width: 16px; margin-left: -5px"
      />
    </center>
    <center v-if="!info.evaluation_results_confirm_id">
      <img
      src="/assets/Icons/sin registro.png"
      class="img-fluid"
      style="width: 16px; margin-left: -5px"
      />
    </center>
  </td>
  <td class="text-center">

          <img
          @click="resetStatus(info.id)"
           src="/assets/Icons/reset.png"
           class="img-fluid"
           style="width: 16px; margin-left: -5px; cursor: pointer;"/>

       </td>
     </template>
</template>

</tr>
<tr>
  <td>&nbsp;</td>
</tr>
<tr>
  <td>&nbsp;</td>
</tr>
<tr>
  <td>&nbsp;</td>
</tr>
</tbody>
</table>
            </div>
          </div>
          <!-- <div class="col-md-3"></div> -->
        </div>
      </div>
    </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import SideBar from "@/components/SideBar.vue";
import axios from "axios";

@Component({
  components: {
    SideBar,
  },
})
export default class ReportByDay extends Vue {
  infoByDay: Array<any> = [];
  users: Array<any> = [];
  swtchButton: any = false;
  swtchButton2: any = true;
  interval: any = null;
  menuShow = false;
  date: any = '00-00-0000';
  date_uno: any = '00-00-0000';
  date_dos: any = '00-00-0000';
  filter: any = 0;
  created() {
    this.initialData();
    this.intervalFun();
  }
  get userName(){
    return this.$store.state.user.name
  }
  async resetStatus(id: any){

    await axios.post( "/api/reset", {data:id});
    this.initialData();
  }
  clickBody(){
    this.menuShow = false;
  }
  showMenu(control: any) {

    if (!this.menuShow) {
      this.menuShow = true;
    } else {
      this.menuShow = false;
    }
  }

  async onChange(id: number, swith: string) {
    let status = false;

    if (swith == "swtchButton") {
      status = this.swtchButton;
    } else {
      status = this.swtchButton2;
    }

    const updatedEvaluation: any = {
      user: id,
      status: status,
    };
    this.swtchButton = false;
    this.swtchButton2 = true;
    await axios.post( "/api/confirm", updatedEvaluation);

    this.initialData();
  }

  async initialData() {
    this.filter = 0;
    const infoByDay = await axios.get(
       "/api/info-day/" + this.date
    );

    this.infoByDay = infoByDay.data;
  }

  async initialDataFixed() {
    console.log(this.date_uno);

    const infoByDay = await axios.get(
       "/api/fixed-register/" + this.date_uno + '&' + this.date_dos
    );
    console.log(infoByDay);

  }
  intervalFun() {
    this.interval = setInterval(() => {
      // this.initialDataFixed();
    }, 20000);
  }
}
</script>
<style media="screen">
.menu {
  visibility: hidden;
  position: absolute;
  background: #262626;
  border-radius: 7px;
  border: 1px solid #262626;
}

button.img-filter + .menu:active,
button.img-filter:focus + .menu {
  visibility: visible;
  outline: none;
  box-shadow: none;
}

.img-filter {
  border: none;
  background: transparent;
  outline: none !important;
   box-shadow: none;
}

.menu ul {
  list-style: none;
  margin: .2rem 0;
  padding: .2rem 1.2rem .2rem .4rem;
}

.menu ul li {
  padding: 0;
}

.menu ul li button {
  padding: .2rem .1rem;
width: 100%;
font-size: 0.9rem;
-webkit-appearance: button;
-moz-appearance: button;
text-transform: none;
font-family: inherit;
/* font-size: 100%; */
line-height: 1.15;
margin: 0;
overflow: visible;
border: 0;
text-align: left;
/* font-family: Arial,Verdana,sans-serif; */
/* outline: 0; */
cursor: pointer;
background: 0 0;
color: #afc0c1;
outline: none;
 box-shadow: none;
}
</style>
