<template>
  <!-- style="background-color: white!important;" -->
  <!-- <nav class="sidebar" style="border: 3px solid blue;"> -->
    <div class="sidebar-sticky">
      <ul class="nav flex-column">
        <li class="nav-item">
          <a class="nav-link d-flex flex-column justify-content-center align-items-center text-center" href="/home">
            <span data-feather="home"></span>
            <img width="40px" src="assets/images/Menu-Envio.png"  alt="Envío de informe"/>
            Envío de informe
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link d-flex flex-column justify-content-center align-items-center text-center" href="/query-user">
            <span data-feather="home"></span>
            <img width="35px" src="assets/images/Menu-Revision.png"  alt="Revision de registros"/>
            Revisión de registros
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link d-flex flex-column justify-content-center align-items-center text-center" href="/vacation">
            <span data-feather="home"></span>
            <img width="30px" src="assets/images/Menu-Vacaciones.png"  alt="vacaciones"/>
            Vacaciones y ausencias
          </a>
        </li>
        <li class="nav-item">
          <a class="nav-link d-flex flex-column justify-content-center align-items-center text-center" href="/my-radar">
            <img width="20px" src="assets/images/Radar- morado.png" style="width: 42px"  alt="mi radar"/>
            <span data-feather="home"></span>
            Mi Radar C19
          </a>
        </li>
        <li v-if="countIsSupervisor > 0" class="nav-item ">
          <a class="nav-link d-flex flex-column justify-content-center align-items-center text-center parent-circle" href="/user-supervisor">
            <span data-feather="home"></span>
            <img width="28px" src="assets/images/personal_supervisado.png" style="width: 42px"  alt="personal supervisado"/>
            Personal supervisado
            <span class="circle" v-show="socio">{{ countRequest }}</span>
          </a>
        </li>
        <li  v-if="permission > 1" class="nav-item">
          <a class="nav-link d-flex flex-column justify-content-center align-items-center text-center" href="/administration">
            <span data-feather="home"></span>
            <img width="35px" src="assets/images/Menu-Admin.png"  alt="administración"/>
            Administración
          </a>
        </li>
      </ul>
    </div>
  <!-- </nav> -->
</template>

<script lang="ts">
import { Component, Mixins } from "vue-property-decorator";
import MenuMixin from "@/mixin/MenuMixin";
import axios from "axios";
@Component
export default class SideBar extends Mixins(MenuMixin) {
  permission: any = null;
  countIsSupervisor: any = 0;
  countRequest: any = 0;
  socio: any = false;
  supervisor: any = false;
  hiddenItem = false;


  goToUsePolicy() {
    this["$router"].replace("/use-policy");
  }

  checkIfHavePermissions() {
    const roles = JSON.parse(this["$store"].state.user.roles_time);
    const array = roles.map(function (x) {
      return x.id;
    });
    this.permission = array.reduce((a, b) => a + b);
  }

  async logout() {
    await this["$store"].dispatch("logout");
    this["$router"].replace("/login");
  }

  async mounted() {
    await this["$store"].dispatch("getUser").then(() => {
      this.userName = this["$store"].state.user.role_time_id;
    });
    this.checkIfHavePermissions();
    this.showAdministrationMenu = this.checkIfUserCanSeeAdministratorMenu();
    this.getIsSupervisor();
    this.validsEmails();
  }

  async getIsSupervisor() {
    const countIsSupervisor = await axios.get(
      "/api/get-is-supervisor/" + this.$store.state.user.id
    );
    this.countIsSupervisor = countIsSupervisor!.data.count;
    this.countRequest = countIsSupervisor!.data.request;
    this.socio = countIsSupervisor!.data.socio;
    this.supervisor = countIsSupervisor!.data.supervisor;
  }

  validsEmails() {
    console.log("valid");
    //const email = this.$store.state.user.email;
    //this.hiddenItem =  ['hmedina@c-230.com'].includes(email)
  }
}
</script>
<style scoped>
@media screen and (max-width: 970px) {
  nav.sidebar {
    display: none !important;
  }
}
.sidebar {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  z-index: 0; /* Behind the navbar */
  padding: 0;
  box-shadow: inset -1px 0 0 rgba(0, 0, 0, .1);
}

.sidebar-sticky {
  position: -webkit-sticky;
  position: sticky;
  top: 80px; /* Height of navbar */
  height: calc(100vh - 80px);
  padding-top: .5rem;
  overflow-x: hidden;
  overflow-y: auto; /* Scrollable contents if viewport is shorter than content. */
}

a.nav-link{
  font-weight: bold;
  color: #6c757d;
}

.parent-circle{
  position: relative;
}
span.circle {
  position: absolute;
  top: 5px;
  right: 27%;
  width: 20px;
  height: 20px;
  font-size: 0.7rem;
  background: #6236ff;
  color: white;
  border-radius: 50px;
}

.nav-item {
  /* width: fit-content; */
  display: contents;
}
</style>
