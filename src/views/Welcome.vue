<template>
  <div class="container-fluid">
    <h2 class="mt-4">¡Te damos la bienvenida!</h2>
    <div v-if="!check" :class="{ 'col-md-12': !check, 'col-md-8': check }">
      <div class="img-w">
        <use-policy v-if="!check" @check-policy="checkPolicy" />
      </div>
    </div>
    <div v-show="check" class="col-md-5 mt-4 user-card">
      <div class="row">
        <div class="col-md-12" v-if="user">
          <div class="row w-100">
            <div class="col-md-4" style="text-align: start;">
              <img
                  :src="user.avatar"
                  alt="Avatar"
                  width="80"
                  height="80"
                  class="img-avatar"
              />
            </div>
            <div
                class="col-md-8 text-left"
                style="display: flex; flex-direction: column"
            >
              <b>{{ userName }}</b>
              <b>{{ JSON.parse(user.roles_time)[0].name }}</b>
              <div>{{ user.jornada }} ({{ user.hours_daily }} horas)</div>
            </div>

          </div>
        </div>
        <div class="col-md-12 mt-2" style="margin: 0; padding: 0">
          <div style="border: 1px solid #606060; margin: 0; width: 90%"></div>
        </div>
        <div class="col-md-12 mt-2">
          <token-item />
        </div>
        <div class="col-md-12 mt-2" style="margin: 0; padding: 0">
          <div style="border: 1px solid #606060; margin: 0; width: 90%"></div>
        </div>
        <div class="col-md-12 mt-2">
          <docs-item />
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import UsePolicy from "./UsePolicy.vue";
import DocsItem from "./components/home/DocsItem.vue";
import TokenItem from "./components/home/TokenItem.vue";
import { Modal } from "ant-design-vue";

@Component({
  components: {
    UsePolicy,
    DocsItem,
    TokenItem,
  },
})
export default class Welcome extends Vue {
  userName = "";
  pro = "";
  check = false;
  user: any = null;

  async mounted() {
    this.userName = this.$store.state.user.name;
    this.pro = this.$store.state.user.pronombre;
    this.check = this.$store.state.agreedToPrivacy;
    this.user = this.$store.state.user;
    this.checkAcceptCookie();
  }

  checkPolicy(value: boolean) {
    this.check = value;
  }

  checkAcceptCookie(){
    // eslint-disable-next-line no-useless-escape
    const cookieValue = document.cookie.replace(/(?:(?:^|.*;\s*)checkToken\s*\=\s*([^;]*).*$)|^.*$/, "$1");
    const checked = cookieValue === "true";
    if(checked == false){
      const confirm = Modal.confirm;
        confirm({
          title: "Actualización en tokens" ,
          content: 'Debido a las actualizaciones de seguridad de Google, se han implementado nuevos procesos de lectura y actualización en los tokens para calendarios. Te solicitamos borrar el token anterior, volver a integrar y seleccionar la lista o listas que deseas leer.',
          okText: 'Ver instrucciones',
          cancelText: 'No volver a mostrar',
          onOk() {
            // URL del archivo PDF
            const pdfUrl = '/pdf/manual_token.pdf';

            // Abrir la URL en una nueva pestaña
            window.open(pdfUrl, '_blank');
          },
          onCancel() {
            // Definir la variable booleana que se desea guardar
            const checked = true;
            // Calcular la fecha de expiración de la cookie
            const expirationDate = new Date();
            expirationDate.setMonth(expirationDate.getMonth() + 6);
            // Guardar la variable booleana en una cookie con la fecha de expiración
            document.cookie = "checkToken=" + checked + "; expires=" + expirationDate.toUTCString() + "; path=/";

          },
      });
    }
  }
}
</script>
<style media="screen" scoped>

.container-fluid{
  background-image: url("/assets/images/home_1.png") !important;
  min-height: calc(100vh - 110px);
  background-repeat: no-repeat;
  background-size: 50% auto;
  background-position: right center;
}
.user-card {
  border: 1px solid silver;
  padding-top: 10px;
  padding-left: 30px;
  padding-bottom: 10px;
  margin-bottom: 20px;
  border-radius: 10px;
  box-shadow: -2px -1px 8px 0px rgba(73, 73, 73, 0.86);
  -webkit-box-shadow: -2px -1px 8px 0px rgba(73, 73, 73, 0.86);
  -moz-box-shadow: -2px -1px 8px 0px rgba(73, 73, 73, 0.86);
  background-color: white;
}

.img-avatar {
  border: 1px solid #c9c9c9;
  border-radius: 50px;
}

#accordion .card {
  margin-bottom: 5px !important;
}
.card-header {
  border-top: 1px solid silver;
  border-left: 1px solid silver;
  border-right: 1px solid silver;
  border-bottom: 1px solid silver;
  background-color: transparent !important;
  text-align: left;
}

.card-header .item {
  background-color: transparent !important;
}

.card-body {
  border: 1px solid silver;
  background-color: transparent !important;
  border-top: 0px !important;
  text-align: left;
  padding: 0.8rem;
}
.img-w {
  background-image: url("/assets/images/Image.png");
  height: 80vh;
  background-repeat: no-repeat;
  background-position: center top;
  padding-bottom: 10%;
}

.custom-h5 {
  font-size: 2em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  font-weight: 600;
}

.ant-modal{
  width: 600px !important;
}
.ant-modal-confirm-title{
  font-weight: bold !important;
  font-size: 2em !important;
}
.ant-modal-confirm-content{
  font-size: 1.1em !important;
}
</style>
