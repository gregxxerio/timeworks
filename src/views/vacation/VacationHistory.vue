<template>
  <div class="card card-request">
    <div v-show="loading" style="text-align: center;">
      <div class="loadinghistory" >
      </div>
      <h2>Cargando ...</h2>
    </div>
    <div v-show="!loading">
      <template v-if="origin == 'user'">
        <div class="row d-flex flex-row justify-content-between">
          <button type="button" class="btn btn-outline-info flex-grow-1 history" @click="year = 2022;getHistory();" :class="year == 2022 ? 'active' : ''">2022</button>
          <button type="button" class="btn btn-outline-info flex-grow-1 history" @click="year = 2023;getHistory();" :class="year == 2023 ? 'active' : ''">2023</button>
          <button type="button" class="btn btn-outline-info flex-grow-1 history" @click="year = 2024;getHistory();" :class="year == 2024 ? 'active' : ''">2024</button>
        </div>
      </template>
      <template v-if="origin == 'Admin'">
        <div class="row d-flex flex-row justify-content-between">
          <button type="button" class="btn btn-outline-info flex-grow-1 history" @click="year = 2022;getHistory();" :class="year == 2022 ? 'active' : ''">2022</button>
          <button type="button" class="btn btn-outline-info flex-grow-1 history" @click="year = 2023;getHistory();" :class="year == 2023 ? 'active' : ''">2023</button>
          <button type="button" class="btn btn-outline-info flex-grow-1 history" @click="year = 2024;getHistory();" :class="year == 2024 ? 'active' : ''">2024</button>
        </div>
        <br>
      </template>
      <div class="table-responsive">
        <table class="table table-hover">
          <thead>
            <tr class="table-info">
              <th scope="col" style="color: #3d70b3;">Tipo de ausencia </th>
              <th scope="col" style="color: #3d70b3;">Fecha </th>
              <th width="20%" scope="col" style="color: #3d70b3;">Estatus</th>
            </tr>
          </thead>
          <tbody>
            <tr class="text-al border-n" v-for="t in history"  :key="t.id">
              <td class="fw">{{t.type == 1 ? 'Vacaciones' : t.type == 2 ? 'Vacaciones (medio turno)' : t.type == 3 ? 'Enfermedad' : t.type == 4 ? 'Otro - ' + t.comment :
                 t.type == 5 ? 'Días no pagados' : t.type == 6 ? 'Enfermedad (medio turno)' : ''}}</td>
              <td class="fw">{{t.fecha}}</td>
              <td>
                <div  class="dropdown" v-show="t.status >= 1 && t.status <= 2">
                  <button class="dropdown-toggle" :class="t.status == 1 ? 'span-revision' : t.status == 2 ? 'span-aprobado' : t.status == 3 ? 'span-rechazado' : t.status == 4 ? 'span-finalizado':''" type="button" id="dropdownMenuButton" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    {{t.status == 1 ? 'En revisión' : t.status == 2 ? 'Aprobada' : t.status == 3 ? 'Rechazada' : t.status == 4 ? 'Finalizado' : ''}}
                  </button>
                  <div v-show="origin == 'Admin'" class="dropdown-menu" aria-labelledby="dropdownMenuButton" >
                    <a class="dropdown-item" @click="deleteHistory(t)" href="#">Rechazar</a>
                  </div>
                </div>
                <div v-show="t.status > 2">
                  <span style="white-space: nowrap;" :class="t.status == 1 ? 'span-revision' : t.status == 2 ? 'span-aprobado' : t.status == 3 ? 'span-rechazado' : t.status == 4 ? 'span-finalizado':''">
                    {{t.status == 1 ? 'En revisión' : t.status == 2 ? 'Aprobada' : t.status == 3 ? 'Rechazada' : t.status == 4 ? 'Finalizado' : ''}}
                  </span>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <a-modal v-model="visibleMessage" @ok="handleOkMessage" style="text-align:center;">
      <h4 style="color:rgb(95 56 56);font-weight:400;">Advertencia !!!</h4>
      <p style="color:rgb(95 56 56);font-weight:400;"><i class="fas fa-bell"></i> Realmente desea rechazar la solicitud ?</p>
    </a-modal>
  </div>
</template>
<script lang="ts">
import { Component, Prop ,Vue } from 'vue-property-decorator';
import axios from "axios";
import { Modal } from 'ant-design-vue';

@Component

export default class Welcome extends Vue {
  @Prop({ default: 0 })
  private userId!: number;
  @Prop({ default: 0 })
  private year!: number;
  @Prop({ default: '' })
  private origin!: string;
  @Prop({ default: false })
  private socio!: boolean;

  userName = "";
  check = "";
  loading = false;
  visibleMessage = false;
  dataDelete: any = '';
  history: any = [];

  deleteHistory(data: any){
    this.visibleMessage = true;
    this.dataDelete = data;
  }

  async handleOkMessage(){
    this.visibleMessage = false;
    this.loading = true;
    const history = await axios.get('/api/delete-reject-vacation/' + this.dataDelete.id);
    this.getHistory();
  }

  async getHistory() {
    this.loading = true;
    const history = await axios.get('/api/user-history-vacation/' + this.userId + '&' + this.year);
    this.history = history.data;
    this.loading = false;
  }

  async mounted() {
    this.userName = this.$store.state.user.name;
    this.check = this.$store.state.user.pronombre;
    this.getHistory();
  }
}
</script>
<style media="screen">
.img-w {
  background-image: url("/assets/images/Image.png");
  height: 80vh;
  background-repeat: no-repeat;
  background-position: center top;
  padding-bottom: 10%;
}

.custom-h5{
  font-size: 2em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  font-weight: 600;
}

.btn-outline-info {
  border-color: #3d70b2;
  color: #3d70b2;
}

.btn-outline-info.active {
  background: #3d70b2 !important;
  border-color: #3d70b2;
}

.btn-outline-info:hover {
  background: #3d70b2 !important;
  border-color: #3d70b2;
}

.table-info-new {
  background: #f4f7f9;
}

/* Span buttons */
.span-aprobado {
  border: 1px solid #accf60;
  padding-top: 0.5%;
  padding-bottom: 0.5%;
  padding-left: 10%;
  padding-right: 10%;
  border-radius: 20px;
  color: #accf60;
  background: #f4f8e9;
}

.span-rechazado {
  border: 1px solid #db7e97;
  padding-top: 0.5%;
  padding-bottom: 0.5%;
  padding-left: 10%;
  padding-right: 10%;
  border-radius: 20px;
  color: #d63776;
  background: #f9e9ec;
}

.span-revision {
  border: 1px solid #c0c1c2;
  padding-top: 0.5%;
  padding-bottom: 0.5%;
  padding-left: 10%;
  padding-right: 10%;
  border-radius: 20px;
  color: #989da5;
  background: #eef0f2;
}

.span-finalizado {
  border: 1px solid #b0a1fa;
  padding-top: 0.5%;
  padding-bottom: 0.5%;
  padding-left: 10%;
  padding-right: 10%;
  border-radius: 20px;
  color: #836bf9;
  background: #f2f0fd;
}

.btn.btn-outline-info.flex-grow-1.history {
  padding: 0%;
}
</style>
