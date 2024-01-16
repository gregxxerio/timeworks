<template>
  <div class="container">
    <div class="row">
      <h1><button class="btn" @click="solicitarURL">Conectar con Calendarios</button></h1>
    </div>
    <div v-if="calendarsConnecteds.length" class="row">
      <div class="col-md-12">
        <h4>Cuentas Conectadas</h4>
      </div>
      <div class="col-md-12">
        <table class="table table-bordered">
          <thead>
            <tr>
              <th class="text-center">Calendario</th>
              <th class="text-center">Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(calendar, index) in calendarsConnecteds" :key="index">
              <td>{{ calendar.calendar }}</td>
              <td>
                <button
                  class="btn btn-sm btn-primary"
                  @click="listEvents(calendar.calendar)"
                >
                  Listar Eventos
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div v-if="calendars.length" class="row mt-2">
      <div class="col-md-12">
        <h4>Cuentas a conectar</h4>
      </div>
      <div class="col-md-12">
        <table class="table table-bordered">
          <thead>
            <tr>
              <th class="text-center">Calendario a almacenar</th>
              <th class="text-center">Seleccionar</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(calendar, index) in calendars" :key="index">
              <td>{{ calendar.name }}</td>
              <td>
                <label class="cl-checkbox">
                  <input v-model="calendar.selected" type="checkbox" />
                  <span></span>
                </label>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="w-100 text-right">
        <button class="btn btn-success" @click="saveCalendarWithTokens">
          Guardar Calendarios
        </button>
      </div>
    </div>
    <div v-if="events.length" class="row mt-2">
      <div class="col-md-12">
        <h4>Lista de eventos</h4>
      </div>
      <div class="col-md-12">
        <table class="table table-bordered">
          <thead>
            <tr>
              <th class="text-center">Evento</th>
              <th class="text-center">Inicio</th>
              <th class="text-center">Fin</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(event, index) in events" :key="index">
              <td>{{ event.summary }}</td>
              <td>{{ event.start }}</td>
              <td>{{ event.end }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import proxy from "@/helpers/proxy";
axios.defaults.url = proxy.api.domain;
import { Component, Vue } from "vue-property-decorator";
@Component
export default class Calendar extends Vue {
  data: any = null;
  calendars = [];
  calendarsConnecteds = [];
  events = [];

  created() {
    this.getCalendars();
    if (this.$route.query.access_token) {
      this.getDataToken();
    }
  }

  getCalendars() {
    console.log("get calendars");
    axios.get("/api/google-calendar/calendars").then((response) => {
      this.calendarsConnecteds = response.data;
    });
  }

  async solicitarURL() {
    const response: any = await axios.get("/api/google-calendar/oauth");
    const url = response.data;
    window.location.replace(url);
  }

  async saveCalendarWithTokens() {
    const calendars = this.calendars
      .filter((item) => item.selected)
      .map((item) => item.name);

    if (calendars.length == 0) alert("selecciona al menos un calendario");

    const data = {
      calendars,
      ...this.data,
    };

    const response: any = await axios.post("/api/google-calendar/token", data);

    this.calendars = [];

    this.getCalendars();

    this.$router.replace({ query: null });
  }

  getDataToken() {
    this.data = {
      accessToken: this.$route.query.access_token,
    };
    this.calendars = String(this.$route.query.calendars)
      .split(",")
      .map((item) => {
        return {
          name: item,
          selected: false,
        };
      }); //[...this.calendars, ...String(this.$route.query.calendars).split(',')]
  }

  async listEvents(calendar) {
    const response: any = await axios.post("/api/google-calendar/list-events", {
      calendar,
    });

    this.events = response.data;
  }
}
</script>

<style scoped>
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
