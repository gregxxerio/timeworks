<template>
  <!-- Header section -->
  <!-- <nav class="navbar sticky-top navbar-custom flex-md-nowrap p-0">
    <a class="navbar-brand mx-auto" href="/welcome">
        <img src="assets/images/LOGO BLANCO.png" alt="Logo" height="40">
    </a>
    <ul class="navbar-nav px-3">
      <li class="nav-item text-nowrap">
        <b class="mr-2 text-white">{{ user }}</b> <button class="btn " @click="logout" style="background: transparent !important;"><img  src="@/assets/logout.png"  alt=""/></button>
      </li>
    </ul>
  </nav> -->
  <nav class="nav-top">
  <div class="wrapper">
  <ul class="nav-links desktop-item">
    <ul>
      &nbsp;
    </ul>
  </ul>
    <div class="logo">
      <a class="navbar-brand mx-auto" href="/welcome">
          <img src="assets/images/LOGO BLANCO.png" alt="Logo" height="40">
      </a>
    </div>
    <input type="radio" name="slider" id="menu-btn">
    <input type="radio" name="slider" id="close-btn">
    <ul class="nav-links desktop-item">
      <li>
        <b class="mr-2 text-white">{{ user }}</b>
        <button  @click="logout" style="background: transparent !important;border: none; cursor: pointer;"><img  src="@/assets/logout.png"  alt=""/></button>
      </li>
    </ul>
    <ul class="nav-links mobile-item">
      <label for="close-btn" class="btn close-btn"><i class="fas fa-times"></i></label>
      <li>
        <a class="flex-nav-bar-mobile" href="/home">
          <img width="30px" src="assets/images/Menu-Envio.png"  alt="Envío de informe"/>
          <span> Envío de informe</span>
        </a>
      </li>
      <li>
        <a class="flex-nav-bar-mobile" href="/query-user">
          <img width="30px" src="assets/images/Menu-Revision.png"  alt="Revision de registros"/>
          <span> Revisión de registros</span>
        </a>
      </li>
      <li>
        <a class="flex-nav-bar-mobile" href="/vacation">
          <img width="30px" src="assets/images/Menu-Vacaciones.png"  alt="vacaciones"/>
          <span> Vacaciones y ausencias</span>
        </a>
      </li>
      <li>
        <a class="flex-nav-bar-mobile" href="/my-radar">
          <img src="assets/images/Radar- morado.png" style="width: 30px"  alt="mi radar"/>
          <span> Mi Radar C19</span>
        </a>
      </li>
      <li>
        <a v-if="countIsSupervisor > 0" class="flex-nav-bar-mobile d-flex flex-column parent-circle" href="/user-supervisor">
          <img src="assets/images/personal_supervisado.png" style="width: 30px"  alt="personal supervisado"/>
          <span> Personal supervisado
          <span class="circle" v-show="socio">{{ countRequest }}</span>
          </span>
        </a>
      </li>
      <li v-if="permission > 1">
        <a class="flex-nav-bar-mobile" href="/administration">
          <img src="assets/images/Menu-Admin.png" style="width: 30px" alt="administración"/>
          <span> Administración</span>

        </a>
      </li>
      <li>
        <a @click="logout" href="#" class="flex-nav-bar-mobile">
          <img  src="@/assets/logout.png" width="30px"  alt=""/>
          <span> Cerrar Sesión</span>
        </a>
        <!-- <button  @click="logout" style="background: transparent !important;border: none; cursor: pointer;"></button> -->
      </li>
    </ul>
    <label for="menu-btn" class="btn menu-btn"><i class="fas fa-bars"></i></label>
  </div>
</nav>
</template>
<script lang="ts">
import { Component, Mixins } from "vue-property-decorator";
import MenuMixin from "@/mixin/MenuMixin";
import SideBar from "../components/SideBar.vue";
import axios from "axios";

@Component({
  components: {
    SideBar,
  },
})
export default class NavBar extends Mixins(MenuMixin) {
  permission: any = null;
  user = "";
  countIsSupervisor: any = 0;
  countRequest: any = 0;
  socio: any = false;
  supervisor: any = false;
  hiddenItem = false;

  checkIfHavePermissions() {
    const roles = JSON.parse(this["$store"].state.user.roles_time);
    const array = roles.map(function (x) {
      return x.id;
    });
    this.permission = array.reduce((a, b) => a + b);
  }

  goToUsePolicy() {
    this["$router"].replace("/use-policy");
  }

  async logout() {
    await this["$store"].dispatch("logout");
    this["$router"].replace("/login");
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

  async mounted() {
    await this["$store"].dispatch("getUser").then(() => {
      this.userName = this["$store"].state.user.role_time_id;
      this.user = this["$store"].state.user.name;
    });
    this.checkIfHavePermissions();
    this.showAdministrationMenu = this.checkIfUserCanSeeAdministratorMenu();
    this.getIsSupervisor();
  }
}
</script>
<style>
/* .navbar-custom {
  height: 70px;
  background: rgb(2,0,36);
  background: linear-gradient(90deg, rgba(2,0,36,1) 0%, rgba(93,9,121,1) 65%);
  width: 95%;
  margin: 0 auto; */

nav.nav-top{
  position: relative;
  z-index: 1;
  border-bottom-right-radius: 25px;
  border-bottom-left-radius: 25px;
  width: 95%;
  margin: 0 auto;
  background: linear-gradient(90deg,#020024,#5d0979 65%);
}
nav .wrapper{
  position: relative;
  max-width: 1300px;
  padding: 0px 30px;
  height: 70px;
  line-height: 70px;
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.wrapper .logo a{
  color: #f2f2f2;
  font-size: 30px;
  font-weight: 600;
  text-decoration: none;
}
.wrapper .nav-links{
  display: inline-flex;
}
.nav-links li{
  list-style: none;
}
.nav-links li a{
  color: #f2f2f2;
  text-decoration: none;
  font-size: 18px;
  font-weight: 500;
  padding: 9px 15px;
  border-radius: 5px;
  transition: all 0.3s ease;
}
.nav-links li a:hover{
  background: #5d0979;
}
.nav-links.mobile-item{
  display: none;
}
.nav-links.desktop-item{
  display: block;
}
.nav-links .drop-menu{
  position: absolute;
  background: #242526;
  width: 180px;
  line-height: 45px;
  top: 85px;
  opacity: 0;
  visibility: hidden;
  box-shadow: 0 6px 10px rgba(0,0,0,0.15);
}
.nav-links li:hover .drop-menu,
.nav-links li:hover .mega-box{
  transition: all 0.3s ease;
  top: 70px;
  opacity: 1;
  visibility: visible;
}
.drop-menu li a{
  width: 100%;
  display: block;
  padding: 0 0 0 15px;
  font-weight: 400;
  border-radius: 0px;
}
.mega-box{
  position: absolute;
  left: 0;
  width: 100%;
  padding: 0 30px;
  top: 85px;
  opacity: 0;
  visibility: hidden;
}
.mega-box .content{
  background: #242526;
  padding: 25px 20px;
  display: flex;
  width: 100%;
  justify-content: space-between;
  box-shadow: 0 6px 10px rgba(0,0,0,0.15);
}
.mega-box .content .row{
  width: calc(25% - 30px);
  line-height: 45px;
}
.content .row img{
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.content .row header{
  color: #f2f2f2;
  font-size: 20px;
  font-weight: 500;
}
.content .row .mega-links{
  margin-left: -40px;
  border-left: 1px solid rgba(255,255,255,0.09);
}
.row .mega-links li{
  padding: 0 20px;
}
.row .mega-links li a{
  padding: 0px;
  padding: 0 20px;
  color: #d9d9d9;
  font-size: 17px;
  display: block;
}
.row .mega-links li a:hover{
  color: #f2f2f2;
}
.wrapper .btn{
  color: #fff;
  font-size: 20px;
  cursor: pointer;
  display: none;
}
.wrapper .btn.close-btn{
  position: absolute;
  right: 30px;
  top: 10px;
}

@media screen and (max-width: 970px) {
  .wrapper .btn{
    display: block;
  }
  .wrapper .nav-links{
    position: fixed;
    height: 100vh;
    width: 100%;
    max-width: 350px;
    top: 0;
    left: -100%;
    /* background: #242526; */
    background: linear-gradient(90deg,#020024,#5d0979 65%);
    display: block;
    padding: 50px 10px;
    line-height: 50px;
    overflow-y: auto;
    box-shadow: 0px 15px 15px rgba(0,0,0,0.18);
    transition: all 0.3s ease;
  }
  /* custom scroll bar */
  ::-webkit-scrollbar {
    width: 10px;
  }
  ::-webkit-scrollbar-track {
    background: #242526;
  }
  ::-webkit-scrollbar-thumb {
    background: #3A3B3C;
  }
  #menu-btn:checked ~ .nav-links{
    left: 0%;
  }
  #menu-btn:checked ~ .btn.menu-btn{
    display: none;
  }
  #close-btn:checked ~ .btn.menu-btn{
    display: block;
  }
  .nav-links li{
    margin: 15px 10px;
  }
  .nav-links li a{
    padding: 0 20px;
    display: block;
    font-size: 20px;
  }
  .nav-links .drop-menu{
    position: static;
    opacity: 1;
    top: 65px;
    visibility: visible;
    padding-left: 20px;
    width: 100%;
    max-height: 0px;
    overflow: hidden;
    box-shadow: none;
    transition: all 0.3s ease;
  }
  #showDrop:checked ~ .drop-menu,
  #showMega:checked ~ .mega-box{
    max-height: 100%;
  }
  .nav-links .desktop-item{
    display: none;
  }
  .nav-links .mobile-item{
    display: block;
    color: #f2f2f2;
    font-size: 20px;
    font-weight: 500;
    padding-left: 20px;
    cursor: pointer;
    border-radius: 5px;
    transition: all 0.3s ease;
  }
  .nav-links .mobile-item:hover{
    background: #3A3B3C;
  }
  .drop-menu li{
    margin: 0;
  }
  .drop-menu li a{
    border-radius: 5px;
    font-size: 18px;
  }
  .mega-box{
    position: static;
    top: 65px;
    opacity: 1;
    visibility: visible;
    padding: 0 20px;
    max-height: 0px;
    overflow: hidden;
    transition: all 0.3s ease;
  }
  .mega-box .content{
    box-shadow: none;
    flex-direction: column;
    padding: 20px 20px 0 20px;
  }
  .mega-box .content .row{
    width: 100%;
    margin-bottom: 15px;
    border-top: 1px solid rgba(255,255,255,0.08);
  }
  .mega-box .content .row:nth-child(1),
  .mega-box .content .row:nth-child(2){
    border-top: 0px;
  }
  .content .row .mega-links{
    border-left: 0px;
    padding-left: 15px;
  }
  .row .mega-links li{
    margin: 0;
  }
  .content .row header{
    font-size: 19px;
  }
}
nav input{
  display: none;
}

.body-text{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  text-align: center;
  padding: 0 30px;
}
.body-text div{
  font-size: 45px;
  font-weight: 600;
}

.flex-nav-bar-mobile {
  display: grid !important;
    grid-template-columns: 20% 80%;
    align-items: center;
}
</style>
