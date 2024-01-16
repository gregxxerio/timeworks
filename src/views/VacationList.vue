<template>
  <div class="container-home-dos" style="background: #FFFFFF;">

    <div class="table-responsive">
      <table class="table table-hover">
        <thead>
          <tr class="table-info-new">
            <th  scope="col" class="fh">
              <div style="color: #3d70b3;">
                Nombre del colaborador
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Organización </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Fecha de ingreso</th>
            <th scope="col" class="fh" style="color: #3d70b3;">
              <div style="display:grid;    grid-template-columns: 28% 90%;align-items: baseline;">
<!--                <div class="grid-buttons-up-down">
                  <span class="icon square-gral arrow-gral up-gral" @click="addQuitDaysGenerated(1)"></span>
                  <span class="icon square-gral arrow-gral down-gral" @click="addQuitDaysGenerated(2)"></span>
                </div>-->
                <div>
                  Días generados
                </div>
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">
              <div style="display:grid;    grid-template-columns: 28% 90%;align-items: baseline;">
<!--                <div class="grid-buttons-up-down">
                  <span class="icon square-gral arrow-gral up-gral" @click="addQuitHolidaysFixed(1)"></span>
                  <span class="icon square-gral arrow-gral down-gral" @click="addQuitHolidaysFixed(2)"></span>
                </div>-->
                <div>
                  Vacaciones fijas
                </div>
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Vacaciones flexibles tomadas</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Días disponibles</th>
            <th width="15%" scope="col" class="fh" style="color: #3d70b3; ">Estatus</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="data.length == 0">
            <tr>
              <td colspan="8">
                <div class="loadinghistory">
                </div>
                <h2>Cargando...</h2>
              </td>
            </tr>
          </template>
          <template v-for="(t, index) in data" >
            <tr :key="t.id" class="text-al border-n" :style="t.id == idNav ? 'background: #f0f6ff;' : ''">
              <template v-if="t.organizacion != null">

                <td class="fw" style="padding:0.5rem !important;display: flex;align-items: center;">
                  <button v-show="t.id != idNav" class="btn btn-sm" @click="idNav = t.id;dataRequest = null;request = [];navOption = 2; getRequest(t.id)" style="background-color: transparent !important;padding: .25rem .3rem !important;"><i class="fa fa-angle-down"></i></button>
                  <button v-show="t.id == idNav" class="btn btn-sm" @click="idNav = 0;" style="background-color: transparent !important;padding: .25rem .4rem !important;"><i class="fa fa-angle-up"></i></button>
                  {{t.name}}
                </td>
                <td>{{t.organizacion}}</td>
                <td>{{t.date_in}}</td>
                <template v-if="(t.vacationabsencesdetail).length != 0">
                  <template v-for="(detail, index) in t.vacationabsencesdetail">
                    <template v-if="detail.year == year">
                      <td style="text-align: center;" :key="'one' + index">{{parseFloat(detail.flexible_holidays) + parseFloat(detail.permanent_holidays) }}</td>
                      <td style="text-align: center;" :key="'two' + index">{{parseFloat(detail.permanent_holidays)}}</td>
                      <td style="text-align: center;" :key="'three' + index">{{parseFloat(detail.days_taken)}}</td>
                      <td style="text-align: center;" :key="'four' + index" class="fw">{{parseFloat(detail.flexible_holidays) }}</td>
                    </template>
                  </template>
                </template>
                <template v-else>
                  <td ></td>
                  <td ></td>
                  <td ></td>
                  <td ></td>
                </template>
                <td>
                  <template v-if="(t.vacationabsencesrequest).length != 0">
                    <button style="width: max-content" :class="getClass(t)"> {{ getStatus(t) }} </button>
                    <!--<template>
                      <template v-if="sendStatus(t.vacationabsencesrequest)[1] > 0">
                        <button style="width:max-content;" class="span-finalizado">
                          {{  getStatus(t) }}
                        </button>
                      </template>
                      <template v-else>
                        <template v-if="sendStatus(t.vacationabsencesrequest)[0] > 0">
                          <button style="width:max-content;" class="span-revision">
                            Solicitud recibida
                          </button>
                        </template>
                        <template v-else>
                          <button style="width:max-content;" class="span-aprobado">
                            Presente
                          </button>
                        </template>
                      </template>
                    </template> -->
<!--
                    <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 1">
                      <button style="width:max-content;" class="span-revision">
                        Solicitud recibida
                      </button>
                    </template>
                    <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 2">
                      <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['date_start'] >= dateNow && t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['date_end'] <= dateNow">
                        <button style="width:max-content;" class="span-finalizado">
                          Ausencia
                        </button>
                      </template>
                      <template v-else>
                        <button style="width:max-content;" class="span-aprobado">
                          Presente
                        </button>
                      </template>
                    </template>
                    <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 3">
                      <button style="width:max-content;" class="span-aprobado">
                        Presente
                      </button>
                    </template> -->
                  </template>
                  <template v-else>
                    <button style="width:max-content;" class="span-aprobado">
                      Presente
                    </button>
                  </template>
                </td>
            </template>
          </tr>
          <!-- Chid -->
          <tr v-show="t.id == idNav" :key="'child' + index" style="background: #f0f6ff;">
            <template v-if="t.organizacion != null">
              <!-- Filter companies -->

              <td colspan="8" style="border: 1px solid red;">
                <ul class="nav nav-pills mb-3" id="pills-tab" role="tablist">

                  <li class="nav-item" role="presentation">
                    <a class="nav-link" :class="navOption == 2 ? 'active' : ''" @click="navOption = 2;getRequest(t.id)" data-toggle="pill" href="#pills-profile" role="tab" aria-controls="pills-profile" aria-selected="true">Solicitudes</a>
                  </li>
                  <li class="nav-item" role="presentation">
                    <a class="nav-link" :class="navOption == 1 ? 'active' : ''" @click="navOption = 1;" data-toggle="pill" href="#pills-home" role="tab" aria-controls="pills-home" aria-selected="false">Historial</a>
                  </li>
                  <!-- <li class="nav-item" role="presentation">
                    <a class="nav-link" :class="navOption == 3 ? 'active' : ''" @click="navOption = 3;setInitialStatus(t.vacationabsencesdetail);" data-toggle="pill" href="#pills-contact" role="tab" aria-controls="pills-contact" aria-selected="false">Editar</a>
                  </li> -->
                </ul>
                <div class="tab-content" id="pills-tabContent">
                  <!-- Historial -->
                  <div class="tab-pane fade" :class="navOption == 1 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-home-tab">
                    <vacation-history v-if="navOption == 1 && t.id == idNav" :userId="t.id" :year="years" :socio="socio" :origin="'Admin'"></vacation-history>
                  </div>
                  <!-- End historial -->
                  <!-- Solicitudes -->
                  <div class="tab-pane fade" :class="navOption == 2 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-profile-tab">
                    <div class="card card-shadow">
                      <div class="card-body">
                        <div v-show="loadingRequest">
                          <div class="loading">
                          </div>
                          <h2>Cargando...</h2>
                        </div>
                        <div v-show="!loadingRequest">
                          <div style="display: grid;grid-template-columns: 1fr 1fr 1fr 1fr;height: 50%;">
                            <div v-for="(r, index) in request" :key="r.id">

                              <template v-if="r.status == 1">
                                <button type="button" @click="dataRequest = r;classBtnRequest = index;" class="btn btn-outline-success btn-w-request btn-pd fw" :class="classBtnRequest == index ? 'active' : ''">
                                  Solicitud {{r.type == 1 ? 'Vacaciones' : r.type == 2 ? 'Vacaciones (medio turno)' : r.type == 3 ? 'Enfermedad' : r.type == 4 ? 'Otro' : r.type == 5 ? 'Días no pagados' : ''}}<br>
                                  ({{r.date_start}} / {{r.date_end}})
                                  <!-- {{dateNow}} -->
                                </button>
                              </template>
                              <template v-if="r.status == 2">
                                <button type="button" @click="dataRequest = r;classBtnRequest = index;" class="btn btn-outline-secondary btn-w-request btn-pd fw" :class="classBtnRequest == index ? 'active' : ''">
                                  Solicitud {{r.type == 1 ? 'Vacaciones' : r.type == 2 ? 'Vacaciones (medio turno)' : r.type == 3 ? 'Enfermedad' : r.type == 4 ? 'Otro' : r.type == 5 ? 'Días no pagados' : ''}}<br>
                                  ({{r.date_start}} / {{r.date_end}})
                                </button>
                              </template>
                              <template v-if="r.status == 3">
                                <button type="button" @click="dataRequest = r;classBtnRequest = index;" class="btn btn-outline-danger btn-w-request btn-pd fw" :class="classBtnRequest == index ? 'active' : ''">
                                  Solicitud {{r.type == 1 ? 'Vacaciones' : r.type == 2 ? 'Vacaciones (medio turno)' : r.type == 3 ? 'Enfermedad' : r.type == 4 ? 'Otro' : r.type == 5 ? 'Días no pagados' : ''}}<br>
                                  ({{r.date_start}} / {{r.date_end}})
                                </button>
                              </template>
                            </div>
                          </div>
                          <template v-if="dataRequest != null">
                            <hr class="hr">
                            <p class="p-admin pd-text" style="color: #6C757D !important;">Supervisor(a) :</p>
                            <template v-for="(supervisor, indexsu) in JSON.parse(dataRequest.supervisor_id)">
                              <p style="color: #000;" class="pd-text text-al text-f fw"  :key="indexsu">
                                {{supervisor.name}}
                              </p>
                            </template>
                            <hr class="hr">
                            <div class="grid-inputs-one-admin">
                              <div class="form-group text-al">
                                <label class="fw">Tipo de ausencia</label>
                                <select disabled v-model="dataRequest.type" class="form-control">
                                  <option value="0">Selecciona una opción</option>
                                  <option value="1">Vacaciones</option>
                                  <option value="2">Vacaciones (medio turno)</option>
                                  <option value="3">Enfermedad</option>
                                  <option value="4">Otro</option>
                                  <option value="5">Días no pagados</option>
                                </select>
                                <template v-if="dataRequest.type == 4">
                                  <input disabled v-model="dataRequest.commentother" type="text" class="form-control" placeholder="Escriba un comentario" style="margin-top: 2%;">
                                </template>
                              </div>
                              <div class="form-group text-al">
                                <label class="fw">Días solicitados</label>
                                <input disabled type="text" v-model="dataRequest.days" class="form-control">
                              </div>
                              <div class="form-group text-al">
                                <label class="fw">Rango de fechas</label>
                                <div class="grid-inputs-two">
                                  <div class="input-group mb-3">
                                    <div class="input-group-prepend">
                                      <span class="input-group-text" style="font-size:small;">Inicio:</span>
                                    </div>
                                    <input disabled v-model="dataRequest.date_start" type="date" class="form-control" >
                                  </div>
                                  <div class="input-group mb-3">
                                    <div class="input-group-prepend">
                                      <span class="input-group-text" style="font-size:small;">Fin:</span>
                                    </div>
                                    <input disabled v-model="dataRequest.date_end" type="date" class="form-control" >
                                  </div>
                                </div>
                              </div>

                            </div>
                            <div class="form-group text-al">
                              <label class="fw">Comentario compartido</label>
                              <textarea disabled v-model="dataRequest.comment" class="form-control"
                              rows="3"></textarea>
                            </div>
                            <div class="form-group text-al">
                              <label class="fw">Notas de supervisor(a)</label>
                              <textarea v-model="dataRequest.supervisor_note" class="form-control"
                              rows="3"></textarea>
                            </div>

                            <div class="grid-inputs-two m-lr" style="gap: 4%;" v-if="dataRequest.status == 1">
                              <button v-if="permission && socio" @click="requestHoliday(2)" type="button" class="btn btn-outline-success btn-rounded">
                                <img src="images/Solicitar.png">
                                Aprobar
                              </button>
                              <button v-if="permission && socio" @click="requestHoliday(3)" type="button" class="btn btn-outline-danger btn-rounded">
                                <img src="images/cancelar.png">
                                Rechazar
                              </button>
                            </div>
                            <div class="grid-inputs-two m-lr" style="gap: 4%;" v-else>
                              <template v-if="dataRequest.status == 2 && socio">
                                <button type="button" class="btn btn-outline-success">Aprobado</button>
                              </template>
                              <template v-if="dataRequest.status == 3 && socio">
                                <button type="button" class="btn btn-outline-danger">Rechazado</button>
                              </template>
                            </div>
                          </template>
                        </div>
                      </div>
                    </div>

                  </div>
                  <!-- End Solicitudes -->
                  <!-- Detalles -->
                  <div class="tab-pane fade" :class="navOption == 3 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-contact-tab">
                    <div class="card card-shadow">
                      <div class="card-body">
                        <div class="loading" v-show="loadingRequest">

                        </div>
                        <div v-show="!loadingRequest">
                          <div class="grid-buttons" >
                            <button type="button" class="btn btn-outline-info btn-w btn-pd fw" @click="year = 2022;" :class="year == 2022 ? 'active' : ''">{{year}}</button>
                            <!-- <button type="button" class="btn btn-outline-info btn-center btn-w btn-pd fw" @click="year = 2023;" :class="year == 2023 ? 'active' : ''">2023</button> -->
                            <!-- <button type="button" class="btn btn-outline-info btn-right btn-w btn-pd fw" @click="year = 2024;" :class="year == 2024 ? 'active' : ''">2024</button> -->
                          </div>
                          <div style="text-align: right;" v-if="t.vacationabsencesdetail.length == 0">
                            <button type="button" @click="completeDays(t)" class="btn btn-info" name="button">Completar días</button>
                          </div>
                          <div class="grid-dates">

                            <div class="card-days-three">
                              <template v-for="(detail, index) in t.vacationabsencesdetail">
                                <div v-if="detail.active == 1" :key="'ph' + index">

                                  <!-- {{detail}} -->
                                  <div class="card-three-sub-one">
                                    <div class="grid-buttons-up-down">
                                      <span class="icon square arrow up"
                                      @click="edit += 1; initialStatusVacationFl += 0.5;
                                      detail.flexible_holidays = parseFloat(detail.permanent_holidays) == 0 ? parseFloat(detail.flexible_holidays) : parseFloat(detail.flexible_holidays) + 0.5;
                                      detail.days_generated = parseFloat(detail.days_generated) == 0 ? 0 : parseFloat(detail.days_generated) + 0.5;
                                      ">
                                    </span>
                                    <span class="icon square arrow down"
                                    @click="edit += 1; initialStatusVacationFl -= 0.5;
                                    detail.flexible_holidays = parseFloat(detail.flexible_holidays) == 0 ? parseFloat(detail.flexible_holidays) : parseFloat(detail.flexible_holidays) - 0.5;
                                    detail.days_generated = parseFloat(detail.days_generated) == 0 ? 0 : parseFloat(detail.days_generated) - 0.5;
                                    ">
                                  </span>
                                </div>

                                <div>
                                  <p class="m0 fw color-black">
                                    <template v-for="(detail, index) in t.vacationabsencesdetail">
                                      <div v-if="detail.active == 1" :key="index">
                                        {{(parseFloat(detail.permanent_holidays) + parseFloat(detail.flexible_holidays)) }} días generados
                                      </div>
                                    </template>
                                  </p>
                                  <p class="m0 fw">Desde: {{fechaSince(t.date_in)[0] == true ? fechaSince(t.date_in)[1] : fechaSince(t.date_in)[1] }} </p>
                                </div>
                              </div>
                              <div class="card-three-sub-two">
                                <p>&nbsp;</p>
                                <div class="card-three-sub-three">
                                  <div class="grid-buttons-up-down">
                                    <span class="icon square arrow up"
                                    @click="edit += 1; initialStatusVacationFi += 0.5;
                                    detail.permanent_holidays = parseFloat(detail.flexible_holidays) == 0 ? parseFloat(detail.permanent_holidays) : parseFloat(detail.permanent_holidays) + 0.5;
                                    detail.flexible_holidays = parseFloat(detail.flexible_holidays) == 0 ? 0 : parseFloat(detail.flexible_holidays) - 0.5;">
                                  </span>
                                  <span class="icon square arrow down" @click="edit += 1; initialStatusVacationFi -= 0.5;
                                  detail.permanent_holidays = parseFloat(detail.permanent_holidays) == 0 ? parseFloat(detail.permanent_holidays) : parseFloat(detail.permanent_holidays) - 0.5;
                                  detail.flexible_holidays = parseFloat(detail.permanent_holidays) == 0 ?  parseFloat(initialVacationFi + initialVacationFl) : parseFloat(detail.flexible_holidays) + 0.5"></span>
                                </div>
                                <p class="m0 fw color-black">
                                  {{parseFloat(detail.permanent_holidays)}} vacaciones fijas
                                </p>
                              </div>
                              <p>&nbsp;</p>
                              <div class="card-three-sub-three">
                                <div class="grid-buttons-up-down">
                                  <span class="icon square arrow up"
                                  @click="edit += 1; initialStatusVacationFl += 0.5;
                                  detail.flexible_holidays = parseFloat(detail.permanent_holidays) == 0 ? parseFloat(detail.flexible_holidays) : parseFloat(detail.flexible_holidays) + 0.5;
                                  detail.permanent_holidays = parseFloat(detail.permanent_holidays) == 0 ? 0 : parseFloat(detail.permanent_holidays) - 0.5;">
                                </span>
                                <span class="icon square arrow down"
                                @click="edit += 1; initialStatusVacationFl -= 0.5;
                                detail.flexible_holidays = parseFloat(detail.flexible_holidays) == 0 ? parseFloat(detail.flexible_holidays) : parseFloat(detail.flexible_holidays) - 0.5;
                                detail.permanent_holidays = parseFloat(detail.flexible_holidays) == 0 ?  parseFloat(initialVacationFi + initialVacationFl) : parseFloat(detail.permanent_holidays) + 0.5;">
                              </span>
                            </div>
                            <p class="m0 fw color-black">
                              {{parseFloat(detail.flexible_holidays )}} vacaciones flexibles
                            </p>
                          </div>
                        </div>

                      </div>
                    </template>
                  </div>
                  <div class="card-days-three">
                    <template v-for="(detail, index) in t.vacationabsencesdetail">
                      <div v-if="detail.active == 1" :key="index">
                        <div class="card-three-sub-four" style="margin-bottom: 6.6%;">
                          <p class="m0 fw color-black">
                            {{parseFloat(detail.days_replacement )}} días reposición
                          </p>
                          <div class="buttons-grid-sr" @click="visible = true;detailId = detail.id;">
                            + / -
                          </div>


                        </div>
                        <div class="card-three-sub-two">
                          <table>
                            <tr>
                              <th style="color:#6236ff;">Días</th>
                              <th style="color:#6236ff;">Notas</th>
                              <th style="color:#6236ff;">Tipo</th>
                              <th style="color:#6236ff;"></th>
                            </tr>
                            <tr v-for="vard in detail.vacationabsencesreplacementdays"  :key="vard.id">
                              <td>{{(vard.type == 1 ? '+' : vard.type == 2 ? '-' : '')  + parseFloat(vard.days)}}</td>
                              <td>{{vard.note}}</td>
                              <td>{{vard.type_day == 1 ? 'Reposición' : vard.type_day == 2 ? 'Días de enfermedad' : ''}}</td>
                              <td>
                                <span style="cursor:pointer;" @click="deleteDayReplacement(vard.id)">
                                  <i class="fa fa-trash" aria-hidden="true"></i>
                                </span>
                              </td>
                            </tr>
                          </table>
                        </div>
                      </div>
                    </template>
                  </div>

                  <br>
                </div>
                <div class="text-footer">
                  <div>
                    <p class="mtb0 fw color-black" style="font-size: 1.2rem;">
                      <template v-for="(detail, index) in t.vacationabsencesdetail">
                        <div v-if="detail.active == 1" :key="index">
                          {{parseFloat(detail.flexible_holidays) - parseFloat(detail.days_taken) }} días disponibles
                        </div>
                      </template>
                    </p>
                    <p class="mtb0 fw">Válidos hasta el 31/12/23</p>
                  </div>
                  <div class="grid-buttons-plus" v-show="edit > 0">

                    <button type="button" class="btn btn-outline-primary" style="height: 50%; align-self: center; margin-left: 2%;" name="button" @click="saveDaysUpdate(t.vacationabsencesdetail)">Guardar cambios</button>
                    <button type="button" class="btn btn-outline-danger" style="height: 50%; align-self: center; margin-left: 1%;"
                    @click="t.vacationabsencesdetail[0].flexible_holidays = initialVacationFl;t.vacationabsencesdetail[0].permanent_holidays =  initialVacationFi; edit = 0;" name="button">
                    Cancelar
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- End detalles -->
    </div>
  </td>
</template>
</tr>
<!-- End child -->
</template>
</tbody>
</table>
</div>

<a-modal v-model="visible" :title="typeDayAdd == 1 ? 'Agregar días': typeDayAdd == 2 ? 'Restar días' : ''" @ok="handleOk">
  <div v-if="!loadingModal">
    <br>
    <label>Días</label>
    <div class="input-group">

      <input type="text" class="form-control" aria-label="Text input with dropdown button" v-model="daysr">
      <div class="input-group-append">
        <button class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">Tipo ...</button>
        <br>
        <div class="dropdown-menu">
          <button class="dropdown-item"  @click="typeDayAdd=1;">Sumar días</button>
          <button class="dropdown-item"  @click="typeDayAdd=2;">Restar días</button>
        </div>
      </div>
    </div>
    <label>Tipo </label>
    <select class="form-control" v-model="typedayadd">
      <option value="1">Reposición</option>
      <option value="2">Dias de enfermedad</option>
    </select>
    <label>Nota</label>
    <input type="text" class="form-control" v-model="noter">
  </div>
  <div class="loading" v-show="loadingModal">
  </div>
</a-modal>
  <a-modal v-model="visibleMessage" @ok="handleOkMessage" style="text-align:center;">
    <h4 style="color:rgb(95 56 56);font-weight:400;">Advertencia !!!</h4>
    <p style="color:rgb(95 56 56);font-weight:400;"><i class="fas fa-bell"></i> Realmente desea rechazar la solicitud ?</p>
  </a-modal>
  </div>

</template>
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import axios from "axios";
import {Modal} from "ant-design-vue";
import VacationHistory from "@/views/vacation/VacationHistory.vue";
import moment from "moment";

@Component({
  components: {VacationHistory }
})

export default class Welcome extends Vue {
  @Prop({ default: 0 })
  private userId!: number;
  @Prop({ default: 0 })
  private years!: number;
  @Prop({ default: '' })
  private origin!: string;

  userName = "";
  check = "";

  checkboxOrgCm = true;
  checkboxOrgCu = false;
  checkboxOrgFi = false;
  checkboxOrgSu = false;

  data: Array<any> = [];
  history: any = [];
  request: Array<any> = [];
  dataRequest: any = null;

  year =  moment().year();
  yeatHistory =  moment().year();

  navOption = 2;
  idNav = 0;
  classBtnRequest: any = -1;

  edit = 0;

  initialStatusVacationFi = 0;
  initialStatusVacationFl = 0;
  initialStatusVacationDg = 0;
  initialStatusVacationGe = 0;

  initialVacationFi = 0;
  initialVacationFl = 0;

  visible = false;
  visibleMessage = false;
  loadingRequest = false;
  loadingModal = false;

  typeDayAdd = 0;//1 suma, 2 resta
  detailId = 0;
  daysr = '';
  noter = '';
  typedayadd = '';

  role: any = 0;
  permission: any = false;

  dateNow: any = '';

  socio: any = false;


  async mounted() {
    this.userName = this.$store.state.user.name;
    this.check = this.$store.state.user.pronombre;
    this.getUsersActive();
    this.getIsSupervisor();
    this.role = this.$store.state.user.role_time_id;
    this.dateNow = new Date().toISOString().slice(0, 10);
  }

  getStatus(item: any){
    console.log(item,'soos');

    // fecha de vacaciones es mayor a hoy y estatus 3: (Presente)
    // fecha de vacaciones es status 1 y es mayor a hoy: (Solicitud Recibida)
    // fecha de vacaciones esta entre hoy
    const dateFilter = item.vacationabsencesrequest.filter( (value: any) => {
      const fechaComparar = moment(value.date_end);
      if(fechaComparar.isSameOrAfter(moment(), "day") && value.status != 3) return value;
    });

    if(!dateFilter.length) return "Presente";

    let status = "Presente";
    item.class = "present";
    const fechaActual = moment();
    for (let i = 0; i < dateFilter.length; i++) {
        const dates = dateFilter[i];
        const dateStart = moment(dates.date_start);
        const dateEnd = moment(dates.date_end);

        if(fechaActual.isSameOrAfter(dateStart, "day") && fechaActual.isSameOrBefore(dateEnd, "day") && dates.status == 2){
          item.class = "ausent";
          return "Ausente";
        }
    }

    for (let i = 0; i < dateFilter.length; i++) {
      const dates = dateFilter[i];
      if(dates.status == 1){
        item.class = "request";
         status = " Solicitud Recibida";
         break;
      }
    }

    return status;
  }

  getClass(item: any){
    // fecha de vacaciones es mayor a hoy y estatus 3: (Presente)
    // fecha de vacaciones es status 1 y es mayor a hoy: (Solicitud Recibida)
    // fecha de vacaciones esta entre hoy
    const dateFilter = item.vacationabsencesrequest.filter( (value: any) => {
      const fechaComparar = moment(value.date_end);
      if(fechaComparar.isSameOrAfter(moment(), "day") && value.status != 3) return value;
    });

    if(!dateFilter.length) return "span-present";

    let status = "span-present";
    const fechaActual = moment();
    for (let i = 0; i < dateFilter.length; i++) {
        const dates = dateFilter[i];
        const dateStart = moment(dates.date_start);
        const dateEnd = moment(dates.date_end);

        if(fechaActual.isSameOrAfter(dateStart, "day") && fechaActual.isSameOrBefore(dateEnd, "day") && dates.status == 2){
          return "span-absent";
        }
    }

    for (let i = 0; i < dateFilter.length; i++) {
      const dates = dateFilter[i];
      if(dates.status == 1){
         status = "span-request";
         break;
      }
    }

    return status;
  }

  sendStatus(data){
    let recibido = 0;
    let ausente = 0;

    const dateTime2 = moment(new Date()).format("YYYY-MM-DD");

    data.forEach(element => {
      if (element.status == 1) {
        recibido += 1;
      }
      if (dateTime2 >= element.date_start && dateTime2 <= element.date_end && element.status == 2) {
         ausente = 1;
      }
    });
    return [recibido, ausente];

  }
  async getIsSupervisor(){
    const countIsSupervisor = await axios.get('/api/get-is-supervisor/' + this.$store.state.user.id);
    this.socio = countIsSupervisor!.data.socio;
  }

  async getUsersActive(){
    this.data = [];
    const response = await axios.get('/api/get-list-users-supervisor');

    this.data = response.data;
  }

  async deleteDayReplacement(number: any){
    const request = await axios.get('/api/delete-replacement-days/' + number);
    this.getUsersActive();
  }

  async addQuitHolidaysFixed(number: any){
    const request = await axios.get('/api/add-quit-holidays-fixed/' + number);
    this.getUsersActive();
  }

  async addQuitDaysGenerated(number: any){
    const request = await axios.get('/api/add-quit-days-generated/' + number);
    this.getUsersActive();
  }

  async completeDays(data: any){
    const request = await axios.get('/api/complete-days/' + data.id);
    this.getUsersActive();
  }

  setInitialStatus(data: any){
    this.initialVacationFi = parseFloat(data[0]['permanent_holidays']);
    this.initialVacationFl = parseFloat(data[0]['flexible_holidays']);
  }

  fechaSince(data: any){
    const year = new Date();
    if (data > year.getFullYear() + '-04-01') {
      return [false, data];
    }else{
      return [true, year.getFullYear() + '-04-01'];
    }
  }

  getPermission(data: any){
    const roles = JSON.parse(this['$store'].state.user.roles_time);
    const array = roles.map(function(x) {
      return x.id;
    });
    let iguales=0;

    for(let i=0; i < data.length; i++)
    {
      for(let j=0; j < array.length; j++)
      {
        if(data[i]==array[j])
        iguales++;
      }
    }
    if (iguales > 0) {
      this.permission = true;
    }else {
      this.permission = false;
    }
  }

  async handleOk() {

    if (this.noter == '') {
      this.showAlert('Se necesita registrar un comentario');
      return;
    }
    if (parseFloat(this.daysr) <= 0) {
      this.showAlert('Los días no pueden ser menor o igual a 0');
      return;
    }
    if (this.typeDayAdd == 0) {
      this.showAlert('Seleccione el tipo de operación');
      return;
    }
    if (isNaN(parseFloat(this.daysr))) {
      this.showAlert('Solo se permiten valores numericos para el numero de días');
      return
    }
    this.loadingModal = true;
    const result: any = await axios.post('/api/save-detail-holiday',
    { id : this.detailId,
      type : this.typeDayAdd,
      day : this.daysr,
      note : this.noter,
      typeday : this.typedayadd
    });

    if (result.data.status) {
      this.getUsersActive();
      this.visible = false;
      this.daysr = '';
      this.noter = '';
      this.typedayadd = '';
      this.loadingModal = false;
    }else{
      this.showAlert(result.data.error);
      this.loadingModal = false;
    }
  }

  async getRequest(id: any) {
    this.getPermission([2,4,5]);
    this.loadingRequest = true;

    const request = await axios.get('/api/user-request-vacation/' + id + '&' + this.year);
    this.request = request.data;
    this.loadingRequest = false;

  }

  async getHistory(id: any) {
    const history = await axios.get('/api/user-history-vacation/' + id + '&' + this.yeatHistory);
    this.history = history.data;
  }



  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title ,
      content: '',
      okText: 'Aceptar',
    });
  }

  /**
  ** 2.- Aprobado
  ** 3.- Rechazado
  **/
  async requestHoliday(status: any) {
    if (this.dataRequest.supervisor_note == null || this.dataRequest.supervisor_note == "") {
      this.showAlert("El campo Notas de supervisor(a) es requerido");
      return;
    }
    if (status == 3) {
      this.visibleMessage = true;
    }else{
      this.loadingRequest = true;
      const result: any = await axios.post('/api/admin-request-holiday',
      { id : this.dataRequest.id,
        supervisorNote : this.dataRequest.supervisor_note,
        status : status
      });
      if (result.data) {
        this.dataRequest = result.data.data;
        this.loadingRequest = false;
      }
      const success = Modal.success;
      this.getUsersActive();
      success({
        title:  status == 2 ? 'Aprobado' : status == 3 ? 'Rechazado' : '',
        content: '',
        okText: 'Aceptar',
      });
      window.location.reload();
    }
  }

  async handleOkMessage(){
    this.visibleMessage = false;
    this.loadingRequest = true;
    const result: any = await axios.post('/api/admin-request-holiday',
    { id : this.dataRequest.id,
      supervisorNote : this.dataRequest.supervisor_note,
      status : 3
    });
    if (result.data) {
      this.dataRequest = result.data.data;
      this.loadingRequest = false;
    }
    const success = Modal.success;
    this.getUsersActive();
    success({
      title:  'Rechazado',
      content: '',
      okText: 'Aceptar',
    });
    window.location.reload();
  }

  async saveDaysUpdate(data: any){
    this.loadingRequest = true;
    const result: any = await axios.post('/api/save-days-update', data[0]);
    if (result.data) {
      this.loadingRequest = false;
    }
    const success = Modal.success;
    success({
      title:  'información actualizada',
      content: '',
      okText: 'Aceptar',
    });
  }


}
</script>
<style media="screen">
.table-responsive {
  width: 100%;
  overflow-x: scroll;
}
.img-w {
  background-image: url("/assets/images/Image.png");
  height: 90vh;
  background-repeat: no-repeat;
  background-position: center top;
  padding-bottom: 10%;
}

.custom-h1{
  font-size: 2.5em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  font-weight: 600;
}

.pd {
  padding-left: 6%;
}

.pd-text {
  margin-top: -2%;
}

/* .height-card {
  height: 115%;
} */

.bg-admin {
  background-image: url("/assets/images/Image.png");
  background-repeat: no-repeat;
  background-position: 24em top;
  /* height: 96vh; */
}

@media only screen and (max-width: 600px) {
  .table-responsive {
    overflow-x: auto;
  }
}

@media (min-width: 1025px) and (max-width: 1200px) {
  .bg-admin {
    background-position: 18em top;
    /* height: 74vh; */
  }
}

@media (min-width: 769px) and (max-width: 1024px) {
  .bg-admin {
    background-position: 6em top;
    /* height: 74vh; */
  }

  .grid-vacation {
    grid-template-columns: 1fr !important;
  }

  .grid-one-v {
    margin-left: 0% !important;
  }

  .grid-two-v {
    margin-left: 0% !important;
    margin-right: 0% !important;
    padding-top: 2% !important;
  }
}

@media (max-width: 768px) {
  .bg-admin {
    background-position: -6em top;
    /* height: 74vh; */
  }

  .grid-vacation {
    grid-template-columns: 1fr !important;
  }

  .grid-one-v {
    margin-left: 0% !important;
  }

  .grid-two-v {
    margin-left: 0% !important;
    margin-right: 0% !important;
    padding-top: 2% !important;
  }
}

@media (min-width: 481px) and (max-width: 1024px) {
  .grid-buttons-vacation {
    grid-template-columns: 1fr 1fr !important;
    gap: 2%;
    padding: 4% 4%;
  }
}

@media (max-width: 481px) {
  .grid-inputs-one {
    display: grid;
    grid-template-columns: 1fr !important;
    gap : 5px;
  }
}

.grid-buttons-vacation {
  display: grid;
  grid-template-columns: 1fr;
}

.grid-vacation {
  display: grid;
  grid-template-columns: 26% 74%;
  height: 80%;
}

.grid-one-v {
  margin-left: 20%;
}

.grid-two-v {
  margin-left: 4%;
  margin-right: 20%;
}

.grid-inputs-one {
  display: grid;
  grid-template-columns: 32% 68%;
  gap : 5px;
}

.grid-inputs-two {
  display: grid;
  grid-template-columns: 1fr 1fr;
}

.grid-buttons{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  height: 50%;
}

.hr {
  margin-top: 2%;
  margin-bottom: 2%;
  background: #000000;
}

.p-admin {
  font-size: 1em;
  font-weight: 600;
  color: #27282a;
  text-align: left;
}

.btn-outline-btn-vacation {
  margin-top: 4%;
  font-size: 85%;
  border-radius: 20px;
  height: auto;
  width: 100%;
  text-align: center;
  background-color: transparent;
  background-image: none;
  border-color: #310981;
  color: #310981 !important;
  font-weight: 600;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.btn-outline-btn-vacation.active{
  background-color: #310981 !important;
  color: #FFFFFF !important;
}

.btn-outline-btn-vacation:hover {
  background-color: #310981;
  color: #FFFFFF !important;
}

.btn-outline-btn-vacation:checked {
  background-color: pink;
}

.title-one-card-vacation {
  display: grid;
  grid-template-columns:  95% 10%;
}

.p-title-one-card-vacation {
  background: #310981;
  margin: 0;
  padding: 1%;
  text-align: left;
  font-weight: 600;
  color: #fff;
}

.triangulo_der {
  width: 0;
  /* height: 0; */
  border-top: 20px solid transparent;
  border-left: 14px solid #310981;
  border-bottom: 20px solid transparent;
}

.text-al {
  text-align: left;
}

.text-f {
  font-size : 0.9em;
}

.fw {
  font-weight: 600;
}

.m-lr {
  margin-left: 6%;
  margin-right: 6%;
}

.btn-rounded {
  border-radius: 20px !important;
  padding-top: 0px !important;
  padding-bottom: 0px !important;
}

.btn-w {
  width: 100%;
}

.btn-pd {
  padding-top: 0px !important;
  padding-bottom: 0px !important;
}

.btn-left {
  border-top-left-radius: 10px;
  border-bottom-left-radius: 10px;
  border-top-right-radius: 0px;
  border-bottom-right-radius: 0px;
}
.btn-center {
  border-radius: 0;
}
.btn-right {
  border-top-left-radius: 0px;
  border-bottom-left-radius: 0px;
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
}

tr td {
  border: 0px !important;
}

/*  */
.loadinghistory {
  display: inline-block;
  width: 80px;
  height: 80px;
  border: 5px solid rgb(26 108 97 / 56%);
  border-radius: 50%;
  border-top-color: #fff;
  animation: spinh 1s ease-in-out infinite;
  -webkit-animation: spinh 1s ease-in-out infinite;
  position: inherit;
  top: 50%;
  left: 56%;
}

@keyframes spinh {
  to {
    -webkit-transform: rotate(360deg);
  }
}

@-webkit-keyframes spinh {
  to {
    -webkit-transform: rotate(360deg);
  }
}
</style>
