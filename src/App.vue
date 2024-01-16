<template>
  <a-config-provider :locale="locale">
    <div id="app">
      <div v-if="isLogin">
        <router-view/>
      </div>
      <div v-if="!isLogin" style="background: #f2f4f8;">
        <NavBar />
        <div class="container-fluid">
          <!-- class="row" -->
          <div class="grid-app">
            <!-- col-md-1 -->
            <nav class="sidebar grid-app-child-one" style="position: relative;top: -30%;height: 130%;">
              <SideBar />
            </nav>
            <!-- col-md-11 -->
            <div class="grid-app-child-two">
              <main role="main" class="ml-sm-auto  pt-3 px-4" style="background-color: #f2f4f8; min-height: calc(100vh - 70px)">
                <router-view/>
              </main>
            </div>
          </div>

        </div>
      </div>

</div>
</a-config-provider>

</template>
<script lang="ts">
import locale from 'ant-design-vue/es/locale/es_ES';
import { Component, Vue } from 'vue-property-decorator';
import SideBar from './components/SideBar.vue';
import SideBarDecorator from './components/SideBarDecorator.vue';
import NavBar from "@/components/NavBar.vue";
import Banner from "@/components/Banner.vue";
import BackToTop from 'vue-backtotop'
import axios from "axios";

@Component({
  components: {
    Banner,
    NavBar,
    SideBar,
    SideBarDecorator,
    BackToTop
  }
})
export default class App extends Vue {

  config = {
    size : 30,
    bottom: 50,
    src :  "/images/volver_arriba.png",
    bgColor : "transparent",
  };


  userName = "";
  // goToUsePolicy(){
  //     this['$router'].replace("/use-policy");
  // }

  // async logout(){
  //   await this['$store'].dispatch('logout');
  //   this['$router'].replace("/login");
  // }

  async mounted() {
    this['$store'].dispatch("getUser").then(() => {
      this.userName = this['$store'].state.user.name.replace(/ .*/,'');
    });

  }

  locale = locale;

  created() {
    console.log(locale);
  }


  get isLogin(){
    const path = this['$route'].fullPath;
    if(path === '/login') return true;
    else return false;
  }

  beforeCreate() {
    this.$store.commit('INITIAL_STORE');
  }


}
</script>

<style>

.container-fluid {
  padding-left: 0;
}

.grid-app {
  display: grid;
  grid-template-columns: 10% 90%;
}

.grid-app-child-one {
  background: #fff;
}

@media only screen and (min-width: 1121px) and (max-width: 1220px)  {
  .grid-app {
    grid-template-columns: 12% 88%;
  }
}

@media only screen and (min-width: 1021px) and (max-width: 1120px)  {
  .grid-app {
    grid-template-columns: 12% 88%;
  }
}

@media only screen and (min-width: 970px) and (max-width: 1020px)  {
  .grid-app {
    grid-template-columns: 14% 86%;
  }
}

@media screen and (max-width: 970px) {
  .grid-app {
    grid-template-columns: 1fr;
  }
  .grid-app-child-one {
    display: none;
  }
}

.title-home {
  font-family: IBM Plex Sans Condensed;
  font-size: 2em;
  color: #6c757d;
  text-align: left;
  vertical-align: top;
  font-weight: 700;
  text-transform: none;
}

div.section{
  border: 0px solid red;
  min-height: calc(100vh - 110px);
}

h1.section-title{
  color: #6c757d;
  font-weight: bold;
  font-size: 2em;
  font-family: IBM Plex Sans Condensed;
}
</style>
