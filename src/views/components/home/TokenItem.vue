<template>
  <div class="row w-100">
    <div class="col-md-12 text-left">
      <div class="row d-flex align-items-center">
        <arrow-up-icon v-if="!isOpened" @on-click="toggleOpened" />
        <arrow-down-icon v-if="isOpened" @on-click="toggleOpened" />
        <b style="margin-left: 10px">Tokens</b>
      </div>
      <div class="row" v-if="isOpened">
        <div class="col-md-12 mt-2" v-if="!showButtonCalendar">
          <div
            class="row r-square mb-2"
            v-for="(group, index) in calendarsConnecteds"
            :key="index"
          >
            <div class="col-md-12 mt-1">
              <div class="row pl-3 pr-3 d-flex justify-content-between">
                <b>{{ group.primary.summary }}</b>
                <svg
                    style="cursor: pointer"
                  width="18px"
                  height="18px"
                  viewBox="-2 0 24 24"
                  id="meteor-icon-kit__regular-trash"
                  fill="none"
                  xmlns="http://www.w3.org/2000/svg"
                  @click.prevent="removeCalendars(index)"
                >
                  <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                  <g
                    id="SVGRepo_tracerCarrier"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  ></g>
                  <g id="SVGRepo_iconCarrier">
                    <path
                      fill-rule="evenodd"
                      clip-rule="evenodd"
                      d="M3 21C3 21.5523 3.44772 22 4 22H16C16.5523 22 17 21.5523 17 21V9H3V21zM15 3H17C18.6569 3 20 4.34315 20 6V8C20 8.55229 19.5523 9 19 9V21C19 22.6569 17.6569 24 16 24H4C2.34315 24 1 22.6569 1 21V9C0.44772 9 0 8.55229 0 8V6C0 4.34315 1.34315 3 3 3H5C5 1.34315 6.34315 0 8 0H12C13.6569 0 15 1.34315 15 3zM5 13C5 12.4477 5.44772 12 6 12C6.55228 12 7 12.4477 7 13V18C7 18.5523 6.55228 19 6 19C5.44772 19 5 18.5523 5 18V13zM9 13C9 12.4477 9.4477 12 10 12C10.5523 12 11 12.4477 11 13V18C11 18.5523 10.5523 19 10 19C9.4477 19 9 18.5523 9 18V13zM13 13C13 12.4477 13.4477 12 14 12C14.5523 12 15 12.4477 15 13V18C15 18.5523 14.5523 19 14 19C13.4477 19 13 18.5523 13 18V13zM8 2C7.44772 2 7 2.44772 7 3H13C13 2.44772 12.5523 2 12 2H8zM2 7H18V6C18 5.44772 17.5523 5 17 5H3C2.44772 5 2 5.44772 2 6V7z"
                      fill="#b5b5b5"
                    ></path>
                  </g>
                </svg>
              </div>
            </div>
            <div class="col-md-12 mt-1">
              <div
                class="flex-item w-100"
                v-for="(item, index2) in group.data"
                :key="index2"
              >
                <div>
                  <label class="cl-checkbox">
                    <input
                      v-model="item.active"
                      type="checkbox"
                      @change="onChangeCheck(index, index2)"
                    />
                    <span></span>
                  </label>
                </div>
                <span>{{ item.summary }}</span>
              </div>
            </div>
          </div>
        </div>

        <div class="col-md-12 mt-3">
          <button
            @click.prevent="getOauthUrl"
            type="button"
            class="btn btn-outline-primary btn-purple btn-rounded btn-sm p-2"
            style="display: flex; background-color: white"
          >
            <span style="margin-right: 5px">Añadir calendarios</span>
            <svg
              width="18"
              viewBox="0 0 24 24"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
              <g
                id="SVGRepo_tracerCarrier"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></g>
              <g id="SVGRepo_iconCarrier">
                <path
                  fill-rule="evenodd"
                  clip-rule="evenodd"
                  d="M17 2C17 1.44772 16.5523 1 16 1C15.4477 1 15 1.44772 15 2V3H9V2C9 1.44772 8.55228 1 8 1C7.44772 1 7 1.44772 7 2V3H5C3.34315 3 2 4.34315 2 6V20C2 21.6569 3.34315 23 5 23H19C20.6569 23 22 21.6569 22 20V6C22 4.34315 20.6569 3 19 3H17V2ZM20 9V6C20 5.44772 19.5523 5 19 5H17V6C17 6.55228 16.5523 7 16 7C15.4477 7 15 6.55228 15 6V5H9V6C9 6.55228 8.55228 7 8 7C7.44772 7 7 6.55228 7 6V5H5C4.44772 5 4 5.44772 4 6V9H20ZM4 11H20V20C20 20.5523 19.5523 21 19 21H5C4.44772 21 4 20.5523 4 20V11Z"
                  fill="#6621a6"
                ></path>
              </g>
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";
import ArrowUpIcon from "@/views/components/home/ArrowUpIcon.vue";
import ArrowDownIcon from "@/views/components/home/ArrowDownIcon.vue";
import { Modal } from "ant-design-vue";

declare const gapi: any;

@Component({
  components: { ArrowUpIcon, ArrowDownIcon },
})
export default class TokenItem extends Vue {
  CLIENT_ID = "438182473804-lold6v82v1m26dht1dr3j58e0tmbjg3g.apps.googleusercontent.com";
  API_KEY = "AIzaSyC2eQA62ImF7GlMuscwl26zg2X0_dBkIcY";
  DISCOVERY_DOCS = ["https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest"];
  SCOPES = "https://www.googleapis.com/auth/calendar";
  authorized = false;
  user: any = null;
  calendarsConnecteds = [];
  data: any = null;
  calendars = [];
  isOpened = true;
  api: any = null;

  async mounted() {
    this.user = await this.$store.state.user;

    this.api = gapi;
    await this.handleClientLoad();

    if (this.$route.query.access_token) {
      this.getDataToken();
    } else {
      this.getCalendars();
    }

    if(this.$route.query.code == "403"){
      const confirm = Modal.confirm;
      confirm({
          title: "Error al obtener el token del calendario" ,
          content: 'Necesitar seleccionar el permiso de lectura de tu calendario',
          okText: 'Aceptar',
          cancelText: 'Mostrar instrucciones',
          onCancel() {
            const pdfUrl = '/pdf/manual_token.pdf';
            // Abrir la URL en una nueva pestaña
            window.open(pdfUrl, '_blank');
          },
      })
    }
  }

  handleClientLoad() {
    this.api.load("client:auth2", this.initClient);
  }

  async initClient() {
    await this.api.client.init({
      apiKey: this.API_KEY,
      clientId: this.CLIENT_ID,
      discoveryDocs: this.DISCOVERY_DOCS,
      scope: this.SCOPES,
      accessType: 'offline',
      state:'aldair',
      prompt: 'consent'
    });

    this.api.auth2.getAuthInstance().isSignedIn.listen(this.statusSession);
    this.statusSession(this.api.auth2.getAuthInstance().isSignedIn.get());
  }

  statusSession(isSignedIn: any) {
    this.authorized = isSignedIn;
  }

  async removeCalendars(index: number) {
    const group = this.calendarsConnecteds[index].primary.group;

    await axios.post("/api/google-calendar/token/remove", { group });

    this.getCalendars();
  }

  toggleOpened() {
    this.isOpened = !this.isOpened;
  }

  getDataToken() {
    this.data = {
      accessToken: this.$route.query.access_token,
    };

    this.calendars = JSON.parse(String(this.$route.query.calendars));

    this.saveCalendarWithTokens();
  }

  async saveCalendarWithTokens() {
    const data = {
      calendars: this.calendars,
      ...this.data,
    };

    const response = await axios.post("/api/google-calendar/token", data);

    this.calendars = [];

    this.getCalendars();

    this.$router.replace({ query: null });
  }

  getCalendars() {
    axios.get("/api/google-calendar/calendars").then((response) => {
      this.calendarsConnecteds = response.data;
    });
  }

  get showButtonCalendar() {
    return this.calendarsConnecteds.length == 0 && this.calendars.length === 0;
  }

  async getOauthUrl() {
    /*if (this.authorized) {
      await this.handleSignOutClick();
    }
    const authInstance = this.api.auth2.getAuthInstance();
    authInstance.signIn().then(async () => {
      this.authorized = true;
      const token = authInstance.currentUser.get().getAuthResponse();
    });*/
    const response = await axios.get("/api/google-calendar/oauth");
    window.open(response.data, '_blank');
  }

  handleSignOutClick() {
    Promise.resolve(this.api.auth2.getAuthInstance().signOut());
  }

  onChangeCheck(index, index2) {
    const item = this.calendarsConnecteds[index].data[index2];

    axios.post("/api/google-calendar/calendar/status", {
      calendar: item.calendar,
      active: item.active,
    });
  }
}
</script>
<style scoped>
.r-square {
  border: 1px solid silver;
  border-radius: 10px;
}
.flex-item {
  display: flex;
}

.calendar {
  border: 1px solid silver;
  display: flex;
  padding: 5px 0px 0px 5px;
  border-radius: 5px;
}

.btn-purple {
  border: 1px solid #6621a6;
  color: #6621a6;
}
.btn-purple:hover {
  background-color: #6621a6;
  border: 1px solid #6621a6;
  color: white;
}

.cl-checkbox {
  position: relative;
  display: inline-block;
}

/* Input */
.cl-checkbox > input {
  appearance: none;
  -moz-appearance: none;
  -webkit-appearance: none;
  z-index: -1;
  position: absolute;
  left: -10px;
  top: -8px;
  display: block;
  margin: 0;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  background-color: rgba(0, 0, 0, 0.6);
  box-shadow: none;
  outline: none;
  opacity: 0;
  transform: scale(1);
  pointer-events: none;
  transition: opacity 0.3s, transform 0.2s;
}

/* Span */
.cl-checkbox > span {
  display: inline-block;
  width: 100%;
  cursor: pointer;
}

/* Box */
.cl-checkbox > span::before {
  content: "";
  display: inline-block;
  box-sizing: border-box;
  margin: 3px 11px 3px 1px;
  border: solid 2px;
  /* Safari */
  border-color: rgba(0, 0, 0, 0.6);
  border-radius: 2px;
  width: 18px;
  height: 18px;
  vertical-align: top;
  transition: border-color 0.2s, background-color 0.2s;
}

/* Checkmark */
.cl-checkbox > span::after {
  content: "";
  display: block;
  position: absolute;
  top: 3px;
  left: 1px;
  width: 10px;
  height: 5px;
  border: solid 2px transparent;
  border-right: none;
  border-top: none;
  transform: translate(3px, 4px) rotate(-45deg);
}

/* Checked, Indeterminate */
.cl-checkbox > input:checked,
.cl-checkbox > input:indeterminate {
  background-color: #018786;
}

.cl-checkbox > input:checked + span::before,
.cl-checkbox > input:indeterminate + span::before {
  border-color: #018786;
  background-color: #018786;
}

.cl-checkbox > input:checked + span::after,
.cl-checkbox > input:indeterminate + span::after {
  border-color: #fff;
}

.cl-checkbox > input:indeterminate + span::after {
  border-left: none;
  transform: translate(4px, 3px);
}

/* Hover, Focus */
.cl-checkbox:hover > input {
  opacity: 0.04;
}

.cl-checkbox > input:focus {
  opacity: 0.12;
}

.cl-checkbox:hover > input:focus {
  opacity: 0.16;
}

/* Active */
.cl-checkbox > input:active {
  opacity: 1;
  transform: scale(0);
  transition: transform 0s, opacity 0s;
}

.cl-checkbox > input:active + span::before {
  border-color: #85b8b7;
}

.cl-checkbox > input:checked:active + span::before {
  border-color: transparent;
  background-color: rgba(0, 0, 0, 0.6);
}

/* Disabled */
.cl-checkbox > input:disabled {
  opacity: 0;
}

.cl-checkbox > input:disabled + span {
  color: rgba(0, 0, 0, 0.38);
  cursor: initial;
}

.cl-checkbox > input:disabled + span::before {
  border-color: currentColor;
}

.cl-checkbox > input:checked:disabled + span::before,
.cl-checkbox > input:indeterminate:disabled + span::before {
  border-color: transparent;
  background-color: currentColor;
}
</style>
