<template>
  <div>
  <div class="page-wrapper" style="margin-left: 0px" >
    <div class="container-fluid" style="text-align: left;">
      <div class="row">
        <div class="col-md-6">
          <p style="font-size: 18px; color: #000;font-weight: 500;">NÚMEROS DE EMERGENCIA</p>
          <div class="row">
            <div class="col-md-6">
              <ul>
                <li><b>Línea de emergencia nacional </b> <br>911 <br><br> </li>
                <li><b>Número de atención ciudadana del Sector Salud </b> <br>800 00 44 800 <br><br> </li>
                <li><b>Atención a mujeres en embarazo, parto, puerperio o lactancia </b> <br>800 628 3762 <br><br> </li>
              </ul>
            </div>
            <div class="col-md-6">
              <ul>
                <li><b>Ciudad de México: </b> <br>555 658 1111 <br><br> </li>
                <li><b>Estado de México: </b> <br>800 900 3200 <br><br> </li>
                <li><b>O envía un SMS: </b> <br>covid19 al 51515 <br><br> </li>
              </ul>
            </div>
          </div>
        </div>
        <div class="col-md-6" style="border-left: 1px solid;">
          <p style="font-size: 18px; color: #000;font-weight: 500;">CONTACTOS OFICINA</p>

            <ul>
              <li>
                <span style="font-weight: bold;">Victoria Cuatecontzi </span><br>
                  <span style="color: black" >Celular </span><span class="p-bold"> 55 8041 0844</span><br>
                  <span style="color: black">Correo </span><span class="p-bold"> vcuatecontzi@c-230.com</span><br><br>
              </li>
              <li>
                <span style="font-weight: bold;">Estefany Montiel </span><br>
        <span style="color: black">Celular </span><span class="p-bold"> 55 6967 2723</span><br>
        <span style="color: black">Correo </span><span class="p-bold"> emontiel@fundacionidea.org.mx</span><br><br>
              </li>
              <li>
                <span style="font-weight: bold;">Mónica Padrón </span><br>
           <span style="color: black">Celular </span><span class="p-bold"> 55 4144 7036</span><br>
           <span style="color: black">Correo </span><span class="p-bold"> mpadron@c-230.com</span><br><br>
              </li>
              <li>
                <span style="font-weight: bold;">Karla Aguirre </span><br>
<span style="color: black">Celular </span><span class="p-bold"> 55 2765 1196</span><br>
<span style="color: black">Correo </span><span class="p-bold"> kaguirre@c-230.com</span><br><br>
              </li>
            </ul>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import SideBar from '../../components/SideBar.vue';
@Component({
  components: {
    SideBar
  },
})
export default class Contact extends Vue {

  }


</script>
