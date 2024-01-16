
<template>
  <div >
    <div class="container-fluid vh-100 desktop-login" style="overflow: hidden;">
      <div class="row h-100">
        <div class="col-md-4 d-flex flex-column justify-content-center align-items-center">
          <!-- Aquí puedes agregar tus campos de formulario -->
          <div class="row text-center pb-4">
            <img style="width: 40%; margin: 0 auto" src="assets/images/LOGO MORADO.png" alt="">
          </div>
          <div style="text-align: center;" v-if="error">
            <small id="emailHelp" class="form-text text-danger mb-3"><strong>El email o la contraseña son incorrectos.</strong></small>
          </div>
          <div class="form-group">
            <label class="text-dark"><strong>Correo electrónico</strong></label>
            <input  v-on:keyup.enter="singIn" v-model="_data.email" autofocus type="email" class="form-control">
          </div>
          <div class="form-group">
            <label class="text-dark"><strong>Contraseña</strong></label>
            <input v-on:keyup.enter="singIn" @input="deleteSpace()" v-model="_data.password" type="password" class="form-control">
          </div>
          <div class="form-group text-center">
            <template v-if="loading">
              <div id="loading"></div>
            </template>
            <template v-else>
              <button type="button" @click="singIn" class="btn btn-info pl-5 pr-5" style="background-color: #006560; border-radius: 10px; border-color: transparent">Iniciar sesión</button>
            </template>
          </div>
          <div class="form-group text-center">
            <a style="color: black" href="http://pgc.c-230.com/password/reset" class="input-font"><strong>¿Olvidaste tu contraseña?</strong></a>
          </div>
        </div>
        <div class="col-md-8 bg-image" />
      </div>
    </div>
    <div class="bg-image mobile-login" >

      <div class="background">
        <div class="shape"></div>
        <div class="shape"></div>
      </div>
      <form>
        <div class="row text-center pb-4">
          <img style="width: 40%; margin: 0 auto" src="assets/images/LOGO MORADO.png" alt="">
        </div>
        <div style="text-align: center;" v-if="error">
          <small id="emailHelp" class="form-text text-danger mb-3"><strong>El email o la contraseña son incorrectos.</strong></small>
        </div>
        <div class="form-group">
          <label class="text-dark"><strong>Correo electrónico</strong></label>
          <input  v-on:keyup.enter="singIn" v-model="_data.email" autofocus type="email" class="form-control">
        </div>
        <div class="form-group">
          <label class="text-dark"><strong>Contraseña</strong></label>
          <input v-on:keyup.enter="singIn" @input="deleteSpace()" v-model="_data.password" type="password" class="form-control">
        </div>
        <div class="form-group text-center">
          <template v-if="loading">
            <div id="loading"></div>
          </template>
          <template v-else>
            <button type="button" @click="singIn" class="btn btn-info pl-5 pr-5" style="background-color: #006560; border-radius: 10px; border-color: transparent">Iniciar sesión</button>
          </template>
        </div>
        <div class="form-group text-center">
          <a style="color: black" href="http://pgc.c-230.com/password/reset" class="input-font"><strong>¿Olvidaste tu contraseña?</strong></a>
        </div>

      </form>
    </div>
  </div>


</template>

<script lang="ts">
import { Component, Vue,  } from "vue-property-decorator";
import { FormLogin } from ".";
import axios from "axios";
import Proxy from "../../helpers/proxy";
axios.defaults.baseURL = Proxy.api.domain;
@Component
export default class Login extends Vue {
  error = false;
  loading = false;
  private _data!: FormLogin;
  API_Domain = Proxy.api.domain;

  deleteSpace(){
  this._data.password = (this._data.password).replace(/\s+/g, '')
  }

  async singIn() {
    try {
      this.loading = true;
      await this['$store'].dispatch("login", this._data);
      // this['$router'].replace("/use-policy");
      this['$router'].replace("/welcome");
      this.loading = false;
    } catch (error) {
      console.log("hay un error");
      this.loading = false;
      this.error = true;
    }
  }
  async created() {
    this._data.email = "";
    this._data.password = "";
  }
}
</script>

<style scoped>
.bg-image {
  height: 100vh;
  /* Establecer la imagen de fondo */
  position: relative;
  width: 100%;
  /* height: 100%; */
  /* linear-gradient(90deg, rgba(2,0,36,1) 0%, rgba(93,9,121,1) 65%) */
  background-color: rgba(93,9,121,1);
  background-image: url("/assets/images/home_1.png");
  background-repeat: no-repeat;
  background-size: 75% auto;
  background-position: center;
}
/*
.bg-image::after{
  content: "";
  position: absolute;

} */

.form-group{
  width: 50%;
}

.desktop-login {
  display: block;
}

.mobile-login {
  display: none;
}

@media screen and (max-width: 970px) {
  .form-group{
    width: 100%;
  }

  .desktop-login {
    display: none;
  }

  .mobile-login {
    display: block;
  }

}

.background{
    width: auto;
    height: auto;
    position: absolute;
    transform: translate(-50%,-50%);
    left: 50%;
    top: 50%;
}

form{
    height: 520px;
    width: 20rem;
    background-color: rgba(255,255,255,0.70);
    position: absolute;
    transform: translate(-50%,-50%);
    top: 50%;
    left: 50%;
    border-radius: 10px;
    /* backdrop-filter: blur(20px); */
    border: 2px solid rgba(255,255,255,0.1);
    box-shadow: 0 0 40px rgba(8,7,16,0.6);
    padding: 50px 35px;
}
form *{
    letter-spacing: 0.5px;
    outline: none;
    border: none;
}




::placeholder{
    color: #e5e5e5;
}



</style>
