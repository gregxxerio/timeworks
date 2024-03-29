<template>
  <div class="row p-3">
    <div class="table-responsive" v-if="dataWeek.length > 0">
      <table class="table">
        <thead>
        <tr class="br-th">
          <th scope="col" class="text-center">Semana</th>
          <th scope="col" class="text-center">Fecha</th>
          <th scope="col" class="text-center">Hora</th>
          <th scope="col" class="text-center">Detalle</th>
          <th scope="col" class="text-center">Codigo</th>
          <th scope="col" class="text-center">Mail</th>
          <th scope="col" class="text-center">Tiempo</th>
        </tr>
        </thead>
        <tbody>
        <template v-for="(week, index) in dataWeek">
          <tr :key="index" class="br-th">
            <td>
              <strong>{{ week.weekNumber }}</strong>
              <button
                  v-show="!dataWeek[index].open"
                  class="btn btn-sm"
                  @click="dataWeek[index].open = true"
                  style="background-color: transparent !important"
              >
                <i class="fa fa-angle-down"></i>
              </button>
              <button
                  v-show="dataWeek[index].open"
                  class="btn btn-sm"
                  @click="dataWeek[index].open = false"
                  style="background-color: transparent !important"
              >
                <i class="fa fa-angle-up"></i>
              </button>
            </td>
            <td>
              <strong>{{ week.startWeek }} - {{ week.endWeek }}</strong>
            </td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
            <td>
              <strong>{{ week.total }}</strong>
              <template v-if="!registerInWeekend">
                <i
                    v-if="Number(week.total) != Number(week.hoursRequired)"
                    class="fa fa-exclamation-triangle"
                    style="color: orangered; font-size: 1.5em; padding-left: 10px"
                    aria-hidden="true"
                >
                </i>
                <i
                    v-else
                    class="fa fa-check-circle"
                    style="color: green; font-size: 1.5em; padding-left: 10px"
                    aria-hidden="true"
                >
                </i>
              </template>
            </td>
          </tr>
          <tr
              v-show="dataWeek[index].open"
              v-for="(day, j) in week.data"
              :key="j + '-' + index"
          >
            <td style="border-top: none !important"></td>
            <td style="color: #a2a3ae">
              {{ formatDay(day.day) }}
              <button
                  v-show="!dataWeek[index].data[j].open"
                  class="btn btn-sm"
                  @click="dataWeek[index].data[j].open = true"
                  style="background-color: transparent !important"
              >
                <i class="fa fa-angle-down"></i>
              </button>
              <button
                  v-show="dataWeek[index].data[j].open"
                  class="btn btn-sm"
                  @click="dataWeek[index].data[j].open = false"
                  style="background-color: transparent !important"
              >
                <i class="fa fa-angle-up"></i>
              </button>
            </td>
            <td>
              <p
                  v-show="dataWeek[index].data[j].open"
                  v-for="(item, o) in day.data"
                  :key="o"
              >
                {{ formatHour(item) }}
              </p>
            </td>
            <td>
              <p
                  v-show="dataWeek[index].data[j].open"
                  v-for="(item, k) in day.data"
                  :key="k"
              >
                {{ item.detail }}
              </p>
            </td>
            <td>
              <p
                  v-show="dataWeek[index].data[j].open"
                  v-for="(item, l) in day.data"
                  :key="l"
              >
                {{ item.project }}
              </p>
            </td>
            <td>
              <p v-show="dataWeek[index].data[j].open"
                 v-for="(item, m) in day.data"
                 :key="m"
              >
                {{ item.email }}
              </p>
            </td>
            <td style="color: #a2a3ae">{{ day.total }}</td>
          </tr>
        </template>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import moment from "moment";
import axios from "axios";
import { RotateSquare2 } from "vue-loading-spinner";
import collect from "collect.js";
import { Modal } from "ant-design-vue";
import store from "@/store";

@Component({
  components: {
    RotateSquare2,
  },
})
export default class MonthlyReportUser extends Vue {
  data: any = null;
  totalHours: any = null;
  dates: any = null;
  year: any = null;
  month: any = null;
  finished = false;
  isLoading = false;
  registerInWeekend = false;
  validWorkWeeks = null;
  dataWeek: Array<any> = [];
  hoursRequiredByDay = 8;

  private mounted() {
    this.$watch(
        () => this.$store.state.data,
        () => {
          this.processData();
        }
    );
    this.$watch(
        () => this.$store.state.registerInWeekend,
        () => {
          this.processData();
        }
    );
  }

  async created() {
    await this.processData();
  }

  private isWeekend(dateStr: string): boolean {
    const date = moment(dateStr, "DD/MM/YYYY HH:mm");
    const dayOfWeek = date.day(); // Obtener el día de la semana (0: Domingo, 1: Lunes, ..., 6: Sábado)
    return dayOfWeek === 0 || dayOfWeek === 6; // Comprobar si es sábado (6) o domingo (0)
  }

  async processData() {
    this.dataWeek = [];

    this.$nextTick(async () => {
      this.data = this.$store.state.data;

      this.registerInWeekend = this.$store.state.registerInWeekend;

      this.hoursRequiredByDay = this.getHoursDailyByEmail();

      if (this.data.length == 0) {
        this.showAlert("Necesitas importar eventos de tu calendario para continuar");
        return;
      }

      const firstDate = moment(this.data[0].dateEnd, "DD/MM/YYYY").format("YYYY-MM-DD");

      try {
        const response = await axios.post("/api/weeks-days", {
          date: firstDate,
          registerInWeekend: this.registerInWeekend
        });
        const dataNew: Array<any> = [];
        this.validWorkWeeks = response.data.validWorkWeeks;
        response.data.data.forEach((item: any, index: number) => {
          dataNew.push({weekNumber: index, data: item, total: 0, open: false});
        });

        //vamos a agregar cada registro subido a su semana correspondiente
        this.data.forEach((register: any) => {
          //obtenemos la fecha del registro del dia que inicio
          const dateTemp = moment(register.dateStart, "DD/MM/YYYY HH:mm");
          //buscamos en que semana se encuentra el registro
          const indexWeekFound = dataNew.findIndex((week: any, i: number) => {
            //obtenemos el dia de  inicio de la semana
            const startWeek = moment(week.data[0].day).subtract(1, "days");
            //obtenemos el dia final de la semana
            const endWeek = moment(week.data[week.data.length - 1].day).add(1, "days");

            //obtenemos las horas requeridas por semana
            let hoursRequired = (endWeek.diff(startWeek, "days") - 1) * this.hoursRequiredByDay;
            hoursRequired = Math.round((hoursRequired + Number.EPSILON) * 100) / 100;
            dataNew[i].hoursRequired = hoursRequired;

            const result = dateTemp.isAfter(startWeek) && dateTemp.isBefore(endWeek);

            //le asignamos el inicio de la semana y el final
            dataNew[i].startWeek = moment(week.data[0].day).format("DD/MM/YYYY");
            dataNew[i].endWeek = moment(week.data[week.data.length - 1].day).format(
                "DD/MM/YYYY"
            );
            return result;
          });
          //si se encuentra en una semana entonces buscamos en que dia de esa semana se encuentra
          if (indexWeekFound !== -1) {
            const weekDatesToAdd = dataNew[indexWeekFound].data;

            const indexDayFound = weekDatesToAdd.findIndex(function (item: any) {
              return dateTemp.format("YYYY-MM-DD") == item.day;
            });
            //si encontro a que dia pertenece entonces agregar a su registro
            if (indexDayFound !== -1) {
              dataNew[indexWeekFound].data[indexDayFound].data.push(register);
            }
          }
        });

        //sumar todos los registros por dia y por semana
        dataNew.forEach(function (week: any, i: number) {
          week.data.forEach(function (day: any, j: number) {
            dataNew[i].data[j].total = collect(day.data).sum("hours");
          });
          dataNew[i].total = collect(dataNew[i].data).sum("total");
          dataNew[i].weekNumber += 1;
        });

        this.dataWeek = dataNew;

        const inWeekIncomplete = this.dataWeek.findIndex(function (item) {
          return item.hoursRequired != item.total;
        });

        if (!this.registerInWeekend) {
          if (inWeekIncomplete !== -1) {
            /*this.showAlert(
                "Alguna(s) semana(s) no cumple(n) con la sumatoria de horas definida."
            );*/
            this.$emit("continueToNext", false);
          } else {
            this.$emit("continueToNext", true);
          }
        } else {
          let totalCount = 0;

          Object.keys(this.validWorkWeeks).forEach(key => {
            totalCount += this.validWorkWeeks[key].length;
          });

          const totalHoursInWeek = totalCount * this.hoursRequiredByDay;

          const totalRegister = this.dataWeek.reduce((accumulator, currentValue) => {
            return accumulator + currentValue.total;
          }, 0)
          if (totalRegister !== totalHoursInWeek) {
            this.showAlert(
                "Alguna(s) semana(s) no cumple(n) con la sumatoria de horas definida."
            );
            this.$emit("continueToNext", false);
          } else {
            this.$emit("continueToNext", true);
          }
        }
      } catch (error) {
        console.error(error);
      }
    });
  }

  formatDay(day: any) {
    return moment(day).format("DD/MM/YYYY");
  }

  formatHour(day: any) {
    return (
        moment(day.dateStart, "DD/MM/YYYY HH:mm").format("HH:mm") +
        "-" +
        moment(day.dateEnd, "DD/MM/YYYY HH:mm").format("HH:mm")
    );
  }

  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title,
      content: "",
      okText: "Aceptar",
    });
  }

  getHoursDailyByEmail() {
    return Number(this.$store.state.user.hours_daily);
    /* const user: any = store.state.user;
    if (user !== null){
      const email = user.email;
      const find =  this.hoursRequiredByUsers.find( (value: any) => {
        return value.email == email;
      });
      if (find) return find.hours;
    }
    return  this.defaultHoursByDay;*/
  }

  getMonthSpanishName(month: any) {
    const months = [
      {
        en: "January",
        es: "Enero",
      },
      {
        en: "February",
        es: "Febrero",
      },
      {
        en: "March",
        es: "Marzo",
      },
      {
        en: "April",
        es: "Abril",
      },
      {
        en: "May",
        es: "Mayo",
      },
      {
        en: "June",
        es: "Junio",
      },
      {
        en: "July",
        es: "Julio",
      },
      {
        en: "August",
        es: "Agosto",
      },
      {
        en: "September",
        es: "Septiembre",
      },
      {
        en: "October",
        es: "Octubre",
      },
      {
        en: "November",
        es: "Noviembre",
      },
      {
        en: "December",
        es: "Diciembre",
      },
    ];

    const found = months.find((element: any) => element.en == month);

    return found ? found.es : month;
  }
}
</script>

<style scoped>
  td p {
    font-size: 0.8em !important;
  }
  .br-th {
    border-top: 1px solid #585858 !important;
    border-bottom: 0px solid red !important;
  }
</style>
