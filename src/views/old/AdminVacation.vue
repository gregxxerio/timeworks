<template>
  <!-- <div> -->
  <div class="container-fluid" style="
      background: #ffffff;
      padding-right: 0px !important;
      padding-left: 0px !important;
    ">
    <div class="grid-buttons">
      <button type="button" @click="
          year = 2022;
          indexDetail = 0;
          idNav = 0;
        " class="btn btn-outline-info btn-left btn-w btn-pd fw" :class="year == 2022 ? 'active' : ''"> 2022 </button>
      <button type="button" @click="
          year = 2023;
          indexDetail = 1;
          idNav = 0;
        " class="btn btn-outline-info btn-center btn-w btn-pd fw" :class="year == 2023 ? 'active' : ''"> 2023 </button>
      <button type="button" @click="
          year = 2024;
          indexDetail = 2;
          idNav = 0;
        " class="btn btn-outline-info btn-right btn-w btn-pd fw" :class="year == 2024 ? 'active' : ''"> 2024 </button>
    </div>
    <br />
    <div class="grid-check">
      <div class="list-org">
        <label class="container-check fw"> Organizaciones: </label>
        <label class="container-check">C230 Consultores <input v-model="checkboxOrgCm" type="checkbox" />
          <span class="checkmark"></span>
        </label>
        <label class="container-check">Fundación IDEA <input v-model="checkboxOrgFi" type="checkbox" />
          <span class="checkmark"></span>
        </label>
        <label class="container-check">C230 Consulting <input v-model="checkboxOrgCu" type="checkbox" />
          <span class="checkmark"></span>
        </label>
        <label class="container-check">Supernova <input v-model="checkboxOrgSu" type="checkbox" />
          <span class="checkmark"></span>
        </label>
      </div>
      <div class="empty-space"></div>
    </div>
    <div class="table-responsive">
      <table class="table table-hover">
        <thead>
          <tr class="table-info-new">
            <th scope="col" class="fh">
              <div style="color: #3d70b3">Nombre del colaborador</div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3">Organización</th>
            <th scope="col" class="fh" style="color: #3d70b3;width:12%;">Fecha de ingreso</th>
            <th scope="col" class="fh" style="color: #3d70b3">
              <div style="
                  display: grid;
                  grid-template-columns: 28% 90%;
                  align-items: baseline;
                ">
                <div class="grid-buttons-up-down">
                  <span class="icon square-gral arrow-gral up-gral" @click="addQuitDaysGenerated(1)"></span>
                  <span class="icon square-gral arrow-gral down-gral" @click="addQuitDaysGenerated(2)"></span>
                </div>
                <div>Días generados</div>
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3">
              <div style="
                  display: grid;
                  grid-template-columns: 28% 90%;
                  align-items: baseline;
                ">
                <div class="grid-buttons-up-down">
                  <span class="icon square-gral arrow-gral up-gral" @click="addQuitHolidaysFixed(1)"></span>
                  <span class="icon square-gral arrow-gral down-gral" @click="addQuitHolidaysFixed(2)"></span>
                </div>
                <div>Vacaciones fijas</div>
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3"> Vacaciones flexibles tomadas </th>
            <th scope="col" class="fh" style="color: #3d70b3">Días disponibles</th>
            <th width="15%" scope="col" class="fh" style="color: #3d70b3">Estatus</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="data.length == 0">
            <tr>
              <td colspan="8" style="text-align:center;">
                <div class="loadinghistory"></div>
                <h2>Cargando...</h2>
              </td>
            </tr>
          </template>
          <template v-for="(t, index) in data">
            <tr :key="t.id" class="text-al border-n" :style="t.id == idNav ? 'background: #f0f6ff;' : ''">
              <template v-if="t.organizacion != null">
                <template v-if="
                    (checkboxOrgCm == true &&
                      t.organizacion.search('C230 Consultores') >= 0) ||
                    (checkboxOrgFi == true && t.organizacion.search('IDEA') >= 0) ||
                    (checkboxOrgCu == true &&
                      t.organizacion.search('C230 Consulting') >= 0) ||
                    (checkboxOrgSu == true && t.organizacion.search('Supernova') >= 0)
                  ">
                  <td class="fw" style="padding: 0.5rem !important; display: flex; align-items: center">
                    <button v-show="t.id != idNav" class="btn btn-sm" @click="
                        idNav = t.id;
                        dataRequest = null;
                        request = [];
                        navOption = 2;
                        getRequest(t.id);
                      " style="
                        background-color: transparent !important;
                        padding: 0.25rem 0.3rem !important;
                      ">
                      <i class="fa fa-angle-down"></i>
                    </button>
                    <button v-show="t.id == idNav" class="btn btn-sm" @click="idNav = 0" style="
                        background-color: transparent !important;
                        padding: 0.25rem 0.4rem !important;
                      ">
                      <i class="fa fa-angle-up"></i>
                    </button>
                    {{ t.name }}
                  </td>
                  <td>{{ t.organizacion }}</td>
                  <td>{{ t.date_in }}</td>
                  <template v-if="t.vacationabsencesdetail.length != 0">
                    <template v-for="(detail, index) in t.vacationabsencesdetail">
                      <template v-if="detail.year == year">
                        <td style="text-align: center" :key="'one' + index" :style="(detail.active == 0) ? 'opacity:50%;':''">
                          <!-- {{detail}} -->
                          {{ parseFloat(detail.flexible_holidays) +
                            parseFloat(detail.permanent_holidays)
                          }}
                        </td>
                        <td style="text-align: center" :key="'two' + index" :style="(detail.active == 0) ? 'opacity:50%;':''">
                          {{ parseFloat(detail.permanent_holidays) }}
                        </td>
                        <td style="text-align: center" :key="'three' + index" :style="(detail.active == 0) ? 'opacity:50%;':''">
                          {{ parseFloat(detail.days_taken) }}
                        </td>
                        <td style="text-align: center" :key="'four' + index" :style="(detail.active == 0) ? 'opacity:50%;':''" class="fw">
                          {{ calculateTotalDaysTable(detail) }}
                        </td>
                      </template>
                    </template>
                  </template>
                  <template v-else>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td></td>
                  </template>
                  <td>
                    <button style="width: max-content" :class="getClass(t)"> {{ getStatus(t) }} </button>
                    <!-- Revisar funsionalidad -->
                    <!-- <template v-if="(t.vacationabsencesrequest).length != 0 "><template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 1"><button style="width:max-content;" class="span-revision">
                        Solicitud recibida {{t.vacationabsencesrequest.}}
                      </button></template><template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 2"><template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['date_start'] >= dateNow && t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['date_end'] <= dateNow"><button style="width:max-content;" class="span-finalizado">
                          Ausencia
                        </button></template><template v-else><button style="width:max-content;" class="span-aprobado">
                          Presente
                        </button></template></template><template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 3"><button style="width:max-content;" class="span-aprobado">
                        Presente
                      </button></template></template><template v-else><button style="width:max-content;" class="span-aprobado">
                      Presente
                    </button></template> -->
                  </td>
                </template>
              </template>
            </tr>
            <!-- Chid -->
            <tr v-show="t.id == idNav" :key="'child' + index" style="background: #f0f6ff">
              <template v-if="t.organizacion != null">
                <!-- Filter companies -->
                <template v-if="
                    (checkboxOrgCm == true &&
                      t.organizacion.search('C230 Consultores') >= 0) ||
                    (checkboxOrgFi == true && t.organizacion.search('IDEA') >= 0) ||
                    (checkboxOrgCu == true &&
                      t.organizacion.search('C230 Consulting') >= 0) ||
                    (checkboxOrgSu == true && t.organizacion.search('Supernova') >= 0)
                  ">
                  <td colspan="8" style="border: 1px solid red">
                    <ul class="nav nav-pills mb-3" id="pills-tab" role="tablist">
                      <li class="nav-item" role="presentation">
                        <a class="nav-link" :class="navOption == 2 ? 'active' : ''" @click="
                            navOption = 2;
                            getRequest(t.id);
                          " data-toggle="pill" href="#pills-profile" role="tab" aria-controls="pills-profile" aria-selected="true">Solicitudes</a>
                      </li>
                      <li class="nav-item" role="presentation">
                        <a class="nav-link" :class="navOption == 1 ? 'active' : ''" @click="navOption = 1" data-toggle="pill" href="#pills-home" role="tab" aria-controls="pills-home" aria-selected="false">Historial</a>
                      </li>
                      <li class="nav-item" role="presentation">
                        <a class="nav-link" :class="navOption == 3 ? 'active' : ''" @click="
                            navOption = 3;
                            setInitialStatus(t.vacationabsencesdetail);
                          " data-toggle="pill" href="#pills-contact" role="tab" aria-controls="pills-contact" aria-selected="false">Editar</a>
                      </li>
                    </ul>
                    <div class="tab-content" id="pills-tabContent">
                      <!-- Historial -->
                      <div class="tab-pane fade" :class="navOption == 1 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-home-tab">
                        <vacation-history v-if="navOption == 1 && t.id == idNav" :userId="t.id" :year="year" :origin="'Admin'"></vacation-history>
                      </div>
                      <!-- End historial -->
                      <!-- Solicitudes -->
                      <div class="tab-pane fade" :class="navOption == 2 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-profile-tab">
                        <div class="card card-shadow">
                          <div class="card-body">
                            <div v-show="loadingRequest" style="text-align:center;">
                              <div class="loading"></div>
                              <h2>Cargando...</h2>
                            </div>
                            <div v-show="!loadingRequest">
                              <div style="
                                  display: grid;
                                  grid-template-columns: 1fr 1fr 1fr 1fr;
                                  height: 50%;
                                ">
                                <div v-for="(r, index) in request" :key="r.id">
                                  <template v-if="r.status == 1">
                                    <button type="button" @click="
                                        dataRequest = r;
                                        classBtnRequest = index;
                                      " class="btn btn-outline-success btn-w-request btn-pd fw" :class="classBtnRequest == index ? 'active' : ''"> Solicitud {{ r.type == 1
                                          ? "Vacaciones"
                                          : r.type == 2
                                          ? "Vacaciones (medio turno)"
                                          : r.type == 3
                                          ? "Enfermedad"
                                          : r.type == 4
                                          ? "Otro"
                                          : r.type == 5
                                          ? "Días no pagados"
                                          : ""
                                      }}
                                      <br /> ({{ r.date_start }} / {{ r.date_end }})
                                      <!-- {{dateNow}} -->
                                    </button>
                                  </template>
                                  <template v-if="r.status == 2">
                                    <button type="button" @click="
                                        dataRequest = r;
                                        classBtnRequest = index;
                                      " class="btn btn-outline-secondary btn-w-request btn-pd fw" :class="classBtnRequest == index ? 'active' : ''"> Solicitud {{ r.type == 1
                                          ? "Vacaciones"
                                          : r.type == 2
                                          ? "Vacaciones (medio turno)"
                                          : r.type == 3
                                          ? "Enfermedad"
                                          : r.type == 4
                                          ? "Otro"
                                          : r.type == 5
                                          ? "Días no pagados"
                                          : ""
                                      }}
                                      <br /> ({{ r.date_start }} / {{ r.date_end }}) </button>
                                  </template>
                                  <template v-if="r.status == 3">
                                    <button type="button" @click="
                                        dataRequest = r;
                                        classBtnRequest = index;
                                      " class="btn btn-outline-danger btn-w-request btn-pd fw" :class="classBtnRequest == index ? 'active' : ''"> Solicitud {{ r.type == 1
                                          ? "Vacaciones"
                                          : r.type == 2
                                          ? "Vacaciones (medio turno)"
                                          : r.type == 3
                                          ? "Enfermedad"
                                          : r.type == 4
                                          ? "Otro"
                                          : r.type == 5
                                          ? "Días no pagados"
                                          : ""
                                      }}
                                      <br /> ({{ r.date_start }} / {{ r.date_end }}) </button>
                                  </template>
                                </div>
                              </div>
                              <template v-if="dataRequest != null">
                                <hr class="hr" />
                                <p class="p-admin pd-text" style="color: #6c757d !important"> Supervisor(a) : </p>
                                <template v-for="(supervisor, indexsu) in JSON.parse(
                                    dataRequest.supervisor_id
                                  )">
                                  <p style="color: #000" class="pd-text text-al text-f fw" :key="indexsu">
                                    {{ supervisor.name }}
                                  </p>
                                </template>
                                <hr class="hr" />
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
                                      <input disabled v-model="dataRequest.commentother" type="text" class="form-control" placeholder="Escriba un comentario" style="margin-top: 2%" />
                                    </template>
                                  </div>
                                  <div class="form-group text-al">
                                    <label class="fw">Días solicitados</label>
                                    <input disabled type="text" v-model="dataRequest.days" class="form-control" />
                                  </div>
                                  <div class="form-group text-al">
                                    <label class="fw">Rango de fechas</label>
                                    <div class="grid-inputs-two">
                                      <div class="input-group mb-3">
                                        <div class="input-group-prepend">
                                          <span class="input-group-text" style="font-size: small">Inicio:</span>
                                        </div>
                                        <input disabled v-model="dataRequest.date_start" type="date" class="form-control" />
                                      </div>
                                      <div class="input-group mb-3">
                                        <div class="input-group-prepend">
                                          <span class="input-group-text" style="font-size: small">Fin:</span>
                                        </div>
                                        <input disabled v-model="dataRequest.date_end" type="date" class="form-control" />
                                      </div>
                                    </div>
                                  </div>
                                </div>
                                <div class="form-group text-al">
                                  <label class="fw">Comentario compartido</label>
                                  <textarea disabled v-model="dataRequest.comment" class="form-control" rows="3"></textarea>
                                </div>
                                <div class="form-group text-al">
                                  <label class="fw">Notas de supervisor(a)</label>
                                  <textarea v-model="dataRequest.supervisor_note" class="form-control" rows="3"></textarea>
                                </div>
                                <div class="grid-inputs-two m-lr" style="gap: 4%" v-if="dataRequest.status == 1">
                                  <button v-if="permission" @click="requestHoliday(2)" type="button" class="btn btn-outline-success btn-rounded">
                                    <img src="images/Solicitar.png" /> Aprobar </button>
                                  <button v-if="permission" @click="requestHoliday(3)" type="button" class="btn btn-outline-danger btn-rounded">
                                    <img src="images/cancelar.png" /> Rechazar </button>
                                </div>
                                <div class="grid-inputs-two m-lr" style="gap: 4%" v-else>
                                  <template v-if="dataRequest.status == 2">
                                    <button type="button" class="btn btn-outline-success"> Aprobado </button>
                                  </template>
                                  <template v-if="dataRequest.status == 3">
                                    <button type="button" class="btn btn-outline-danger"> Rechazado </button>
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
                            <div class="loading" v-show="loadingRequest"></div>
                            <div v-show="!loadingRequest">
                              <div class="grid-buttons">
                                <button type="button" class="btn btn-outline-info btn-w btn-pd fw" :class="year == year ? 'active' : ''">
                                  {{ year }}
                                </button>
                                <!-- <button type="button" class="btn btn-outline-info btn-center btn-w btn-pd fw" @click="year = 2023;" :class="year == 2023 ? 'active' : ''">2023</button> -->
                                <!-- <button type="button" class="btn btn-outline-info btn-right btn-w btn-pd fw" @click="year = 2024;" :class="year == 2024 ? 'active' : ''">2024</button> -->
                              </div>
                              <div style="text-align: right" v-if="t.vacationabsencesdetail.length == 0">
                                <button type="button" @click="completeDays(t)" class="btn btn-info" name="button"> Completar días </button>
                              </div>
                              <div class="grid-dates">
                                <div class="card-days-three">
                                  <template v-for="(detail, index) in t.vacationabsencesdetail">
                                    <div v-if="detail.year == year" :key="'ph' + index">
                                      <!-- {{detail}} -->
                                      <div class="card-three-sub-one">
                                        <div class="grid-buttons-up-down">
                                          <span class="icon square arrow up" @click="
                                              edit += 1;
                                              initialStatusVacationFl += 0.5;
                                              detail.flexible_holidays =
                                                parseFloat(detail.permanent_holidays) == 0
                                                  ? parseFloat(detail.flexible_holidays)
                                                  : parseFloat(detail.flexible_holidays) +
                                                    0.5;
                                              detail.days_generated =
                                                parseFloat(detail.days_generated) == 0
                                                  ? 0
                                                  : parseFloat(detail.days_generated) + 1;
                                            "></span>
                                          <span class="icon square arrow down" @click="
                                              edit += 1;
                                              initialStatusVacationFl -= 0.5;
                                              detail.flexible_holidays =
                                                parseFloat(detail.flexible_holidays) == 0
                                                  ? parseFloat(detail.flexible_holidays)
                                                  : parseFloat(detail.flexible_holidays) -
                                                    0.5;
                                              detail.days_generated =
                                                parseFloat(detail.days_generated) == 0
                                                  ? 0
                                                  : parseFloat(detail.days_generated) -
                                                    0.5;
                                            "></span>
                                        </div>
                                        <div>
                                          <p class="m0 fw color-black">
                                            <template v-for="(
                                                detail, index
                                              ) in t.vacationabsencesdetail">
                                              <div v-if="detail.year == year" :key="index">
                                                {{ parseFloat(detail.permanent_holidays) +
                                                  parseFloat(detail.flexible_holidays)
                                                }} días generados
                                              </div>
                                            </template>
                                          </p>
                                          <p class="m0 fw"> Desde: {{ fechaSince(t.date_in)[0] == true
                                                ? fechaSince(t.date_in)[1]
                                                : fechaSince(t.date_in)[1]
                                            }}
                                          </p>
                                        </div>
                                      </div>
                                      <div class="card-three-sub-two">
                                        <p>&nbsp;</p>
                                        <div class="card-three-sub-three">
                                          <div class="grid-buttons-up-down">
                                            <span class="icon square arrow up" @click="
                                                edit += 1;
                                                initialStatusVacationFi += 0.5;
                                                detail.permanent_holidays =
                                                  parseFloat(detail.flexible_holidays) ==
                                                  0
                                                    ? parseFloat(
                                                        detail.permanent_holidays
                                                      )
                                                    : parseFloat(
                                                        detail.permanent_holidays
                                                      ) + 0.5;
                                                detail.flexible_holidays =
                                                  parseFloat(detail.flexible_holidays) ==
                                                  0
                                                    ? 0
                                                    : parseFloat(
                                                        detail.flexible_holidays
                                                      ) - 0.5;
                                              "></span>
                                            <span class="icon square arrow down" @click="
                                                edit += 1;
                                                initialStatusVacationFi -= 0.5;
                                                detail.permanent_holidays =
                                                  parseFloat(detail.permanent_holidays) ==
                                                  0
                                                    ? parseFloat(
                                                        detail.permanent_holidays
                                                      )
                                                    : parseFloat(
                                                        detail.permanent_holidays
                                                      ) - 0.5;
                                                detail.flexible_holidays =
                                                  parseFloat(detail.permanent_holidays) ==
                                                  0
                                                    ? parseFloat(
                                                        initialVacationFi +
                                                          initialVacationFl
                                                      )
                                                    : parseFloat(
                                                        detail.flexible_holidays
                                                      ) + 0.5;
                                              "></span>
                                          </div>
                                          <p class="m0 fw color-black">
                                            {{ parseFloat(detail.permanent_holidays) }} vacaciones fijas
                                          </p>
                                        </div>
                                        <p>&nbsp;</p>
                                        <div class="card-three-sub-three">
                                          <div class="grid-buttons-up-down">
                                            <span class="icon square arrow up" @click="
                                                edit += 1;
                                                initialStatusVacationFl += 0.5;
                                                detail.flexible_holidays =
                                                  parseFloat(detail.permanent_holidays) ==
                                                  0
                                                    ? parseFloat(detail.flexible_holidays)
                                                    : parseFloat(
                                                        detail.flexible_holidays
                                                      ) + 0.5;
                                                detail.permanent_holidays =
                                                  parseFloat(detail.permanent_holidays) ==
                                                  0
                                                    ? 0
                                                    : parseFloat(
                                                        detail.permanent_holidays
                                                      ) - 0.5;
                                              "></span>
                                            <span class="icon square arrow down" @click="
                                                edit += 1;
                                                initialStatusVacationFl -= 0.5;
                                                detail.flexible_holidays =
                                                  parseFloat(detail.flexible_holidays) ==
                                                  0
                                                    ? parseFloat(detail.flexible_holidays)
                                                    : parseFloat(
                                                        detail.flexible_holidays
                                                      ) - 0.5;
                                                detail.permanent_holidays =
                                                  parseFloat(detail.flexible_holidays) ==
                                                  0
                                                    ? parseFloat(
                                                        initialVacationFi +
                                                          initialVacationFl
                                                      )
                                                    : parseFloat(
                                                        detail.permanent_holidays
                                                      ) + 0.5;
                                              "></span>
                                          </div>
                                          <p class="m0 fw color-black">
                                            {{ parseFloat(detail.flexible_holidays) }} vacaciones flexibles
                                          </p>
                                        </div>
                                      </div>
                                    </div>
                                  </template>
                                </div>
                                <div class="card-days-three">
                                  <template v-for="(detail, index) in t.vacationabsencesdetail">
                                    <div v-if="detail.year == year" :key="index">
                                      <div class="card-three-sub-four" style="margin-bottom: 6.6%">
                                        <p class="m0 fw color-black">
                                          {{ parseFloat(detail.days_replacement) }} días reposición
                                        </p>
                                        <div class="buttons-grid-sr" @click="
                                            visible = true;
                                            detailId = detail.id;
                                          "> + / - </div>
                                      </div>
                                      <div class="card-three-sub-two">
                                        <table style="width:350px;">
                                          <tr>
                                            <th style="color: #6236ff">Días</th>
                                            <th style="color: #6236ff">Notas</th>
                                            <th style="color: #6236ff">Tipo</th>
                                            <th style="color: #6236ff"></th>
                                          </tr>
                                          <tr v-for="vard in detail.vacationabsencesreplacementdays" :key="vard.id">
                                            <td>
                                              {{ vard.type == 1
                                                  ? "+"
                                                  : vard.type == 2
                                                  ? "-"
                                                  : ""
                                              }}
                                              {{ parseFloat(vard.days) }}
                                            </td>
                                            <td>{{ vard.note }}</td>
                                            <td>
                                              {{ vard.type_day == 1
                                                  ? "Reposición"
                                                  : vard.type_day == 2
                                                  ? "Días de enfermedad"
                                                  : ""
                                              }}
                                            </td>
                                            <td>
                                              <span style="cursor: pointer" @click="deleteDayReplacement(vard.id)">
                                                <i class="fa fa-trash" aria-hidden="true"></i>
                                              </span>
                                            </td>
                                          </tr>
                                        </table>
                                      </div>
                                    </div>
                                  </template>
                                </div>
                                <br />
                              </div>
                              <div class="text-footer">
                                <div>
                                  <template v-for="(detail, index) in t.vacationabsencesdetail">
                                    <div v-if="detail.year == year" :key="index" style="display:flex;margin-top: 1%;">
                                    <button :class="(detail.active == 1) ? 'btn btn-outline-danger' : 'btn btn-outline-success'"
                                    :style="(detail.active == 1) ? 'margin-left: -38%':'margin-left: -32%'"
                                     class="button-options"
                                     @click="activeVacations(detail.id, (detail.active == 1) ? 0 : 1);
                                     t.vacationabsencesdetail[indexDetail].active = ((detail.active == 1) ? 0 : 1);">
                                     {{(detail.active == 1) ? 'Desactivar' : 'Activar'}}

                                   </button>
                                      <p class="mtb0 fw color-black" style="font-size: 1.2rem">
                                        {{ calculateTotalDays(detail) }} días disponibles
                                      </p>
                                    </div>
                                  </template>
                                  <p class="mtb0 fw"> Válidos hasta el 31/12/{{ String(year).slice(2) }}
                                  </p>
                                </div>
                                <div class="grid-buttons-plus" v-show="edit > 0">
                                  <button type="button" class="btn btn-outline-primary" style="
                                      height: 50%;
                                      align-self: center;
                                      margin-left: 2%;
                                    " name="button" @click="saveDaysUpdate(t.vacationabsencesdetail)"> Guardar cambios </button>
                                  <button type="button" class="btn btn-outline-danger" style="
                                      height: 50%;
                                      align-self: center;
                                      margin-left: 1%;
                                    " @click="
                                      t.vacationabsencesdetail[
                                        indexDetail
                                      ].flexible_holidays = initialVacationFl;
                                      t.vacationabsencesdetail[
                                        indexDetail
                                      ].permanent_holidays = initialVacationFi;
                                      edit = 0;
                                    " name="button"> Cancelar </button>
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
                <!--End of filter-->
              </template>
            </tr>
            <!-- End child -->
          </template>
        </tbody>
      </table>
    </div>
    <a-modal v-model="visible" :title="typeDayAdd == 1 ? 'Agregar días' : typeDayAdd == 2 ? 'Restar días' : ''" @ok="handleOk">
      <div v-if="!loadingModal">
        <br />
        <label>Días</label>
        <div class="input-group">
          <input type="text" class="form-control" aria-label="Text input with dropdown button" v-model="daysr" />
          <div class="input-group-append">
            <button class="btn btn-outline-secondary dropdown-toggle" type="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false"> Tipo ... </button>
            <br />
            <div class="dropdown-menu">
              <button class="dropdown-item" @click="typeDayAdd = 1">Sumar días</button>
              <button class="dropdown-item" @click="typeDayAdd = 2">Restar días</button>
            </div>
          </div>
        </div>
        <label>Tipo </label>
        <select class="form-control" v-model="typedayadd">
          <option value="1">Reposición</option>
          <option value="2">Dias de enfermedad</option>
        </select>
        <label>Nota</label>
        <input type="text" class="form-control" v-model="noter" />
      </div>
      <div class="loading" v-show="loadingModal"></div>
    </a-modal>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import VacationHistory from "@/views/vacation/VacationHistory.vue";
import { Modal } from "ant-design-vue";
import axios from "axios";
import moment from "moment/moment";

@Component({
  components: { VacationHistory },
})
export default class Welcome extends Vue {
  userName = "";
  check = "";

  checkboxOrgCm = true;
  checkboxOrgCu = true;
  checkboxOrgFi = true;
  checkboxOrgSu = false;

  data: Array<any> = [];
  history: any = [];
  request: Array<any> = [];
  dataRequest: any = null;

  year = moment().year();
  yeatHistory = moment().year();

  navOption = 1;
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
  loadingRequest = false;
  loadingModal = false;

  typeDayAdd = 0; //1 suma, 2 resta
  detailId = 0;
  daysr = "";
  noter = "";
  typedayadd = "";

  role: any = 0;
  permission: any = false;

  dateNow: any = "";

  indexDetail: any = 0;

  getStatus(item: any){
    // fecha de vacaciones es mayor a hoy y estatus 3: (Presente) 
    // fecha de vacaciones es status 1 y es mayor a hoy: (Solicitud Recibida)
    // fecha de vacaciones esta entre hoy 
    let debug = false;
    if(item.email == 'emontiel@fundsacionidea.org.mx'){
      debug = true;
    }
    const dateFilter = item.vacationabsencesrequest.filter( (value: any) => {
      const fechaComparar = moment(value.date_end);
      if(fechaComparar.isSameOrAfter(moment(), "day") && value.status != 3) return value;
    });

    if(debug){
      console.log('dateFilter');
      console.log(dateFilter);
    }

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
          if(debug){
            console.log('cumple condicion');
          }
          return "Ausente";
        }else{
          if(debug){
              console.log("No cumple condicion");
              console.log({
                '>=' : fechaActual.isSameOrAfter(dateStart, "day"),
                '<=' : fechaActual.isSameOrBefore(dateStart, "day"),
                'dates.status == 2':  dates.status == 2
              });
            }
        }
    }

    if(debug){
    console.log('entra aqui')
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

  async activeVacations(id: any, status: any){
    const request = await axios.get("/api/active-desactive-vacations/" + id + '&' + status);
  }

  async deleteDayReplacement(number: any) {
    const request = await axios.get("/api/delete-replacement-days/" + number);
    this.getUsersActive();
  }

  async addQuitHolidaysFixed(number: any) {
    const request = await axios.get("/api/add-quit-holidays-fixed/" + number);
    this.getUsersActive();
  }

  async addQuitDaysGenerated(number: any) {
    const request = await axios.get("/api/add-quit-days-generated/" + number);
    this.getUsersActive();
  }

  async completeDays(data: any) {
    const request = await axios.get("/api/complete-days/" + data.id);
    this.getUsersActive();
  }

  setInitialStatus(data: any) {
    this.initialVacationFi = parseFloat(data[this.indexDetail]["permanent_holidays"]);
    this.initialVacationFl = parseFloat(data[this.indexDetail]["flexible_holidays"]);
  }

  fechaSince(data: any) {
    const year = new Date();
    // const date = new Date();
    // const date_in = new Date(data);
    //
    if (data > year.getFullYear() + "-04-01") {
      return [false, data];
    } else {
      return [true, year.getFullYear() + "-04-01"];
    }
  }

  getPermission(data: any) {
    const roles = JSON.parse(this["$store"].state.user.roles_time);
    const array = roles.map(function (x) {
      return x.id;
    });
    let iguales = 0;

    for (let i = 0; i < data.length; i++) {
      for (let j = 0; j < array.length; j++) {
        if (data[i] == array[j]) iguales++;
      }
    }
    if (iguales > 0) {
      this.permission = true;
    } else {
      this.permission = false;
    }
  }

  calculateTotalDaysTable(detail: any) {
    return  parseFloat(detail.flexible_holidays) - parseFloat(detail.days_taken) ;
  }
  calculateTotalDays(detail: any) {
    const total = parseFloat(detail.flexible_holidays) - parseFloat(detail.days_taken);
    // if (
    //   // eslint-disable-next-line no-prototype-builtins
    //   detail.hasOwnProperty("vacationabsencesreplacementdays") &&
    //   Array.isArray(detail.vacationabsencesreplacementdays)
    // ) {
    //   detail.vacationabsencesreplacementdays.forEach((item: any) => {
    //     if (item.type == 1) {
    //       total += parseFloat(item.days);
    //     } else if (item.type == 2) {
    //       total -= parseFloat(item.days);
    //     }
    //   });
    // } else {
    //   console.log("La propiedad no existe o no es un array");
    //   console.log(detail);
    // }
    //
    // if(total > 15){
    //   console.log("detail");
    //   console.log(detail);
    // }

    return total;
  }

  async handleOk() {
    if (this.noter == "") {
      this.showAlert("Se necesita registrar un comentario");
      return;
    }
    if (parseFloat(this.daysr) <= 0) {
      this.showAlert("Los días no pueden ser menor o igual a 0");
      return;
    }
    if (this.typeDayAdd == 0) {
      this.showAlert("Seleccione el tipo de operación");
      return;
    }
    if (isNaN(parseFloat(this.daysr))) {
      this.showAlert("Solo se permiten valores numericos para el numero de días");
      return;
    }
    this.loadingModal = true;
    const result: any = await axios.post("/api/save-detail-holiday", {
      id: this.detailId,
      type: this.typeDayAdd,
      day: this.daysr,
      note: this.noter,
      typeday: this.typedayadd,
    });

    if (result.data.status) {
      this.getUsersActive();
      this.visible = false;
      this.daysr = "";
      this.noter = "";
      this.typedayadd = "";
      this.loadingModal = false;
    } else {
      this.showAlert(result.data.error);
      this.loadingModal = false;
    }
  }

  async getRequest(id: any) {
    this.getPermission([2, 4, 5]);
    this.loadingRequest = true;
    const request = await axios.get("/api/user-request-vacation/" + id + "&" + this.year);
    this.request = request.data;
    this.loadingRequest = false;
  }

  async getHistory(id: any) {
    const history = await axios.get(
      "/api/user-history-vacation/" + id + "&" + this.yeatHistory
    );
    this.history = history.data;
  }

  async getUsersActive() {
    this.data = [];
    const response = await axios.get("/api/get-list-users-active");
    this.data = response.data;
  }

  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title,
      content: "",
      okText: "Aceptar",
    });
  }

  async requestHoliday(status: any) {
    if (
      this.dataRequest.supervisor_note == null ||
      this.dataRequest.supervisor_note == ""
    ) {
      this.showAlert("El campo Notas de supervisor(a) es requerido");
      return;
    }
    this.loadingRequest = true;
    const result: any = await axios.post("/api/admin-request-holiday", {
      id: this.dataRequest.id,
      supervisorNote: this.dataRequest.supervisor_note,
      status: status,
    });
    if (result.data) {
      this.dataRequest = result.data.data;
      this.loadingRequest = false;
    }
    const success = Modal.success;
    this.getUsersActive();
    success({
      title: status == 2 ? "Aprobado" : status == 3 ? "Rechazado" : "",
      content: "",
      okText: "Aceptar",
    });
  }

  async saveDaysUpdate(data: any) {
    this.loadingRequest = true;
    const result: any = await axios.post("/api/save-days-update", data[this.indexDetail]);
    if (result.data) {
      this.loadingRequest = false;
      this.edit = 0;
    }
    const success = Modal.success;
    success({
      title: "información actualizada",
      content: "",
      okText: "Aceptar",
    });
  }

  async mounted() {
    this.userName = this.$store.state.user.name;
    this.check = this.$store.state.user.pronombre;
    this.getUsersActive();
    this.role = this.$store.state.user.role_time_id;
    this.dateNow = new Date().toISOString().slice(0, 10);
    if (this.year == 2022) {
      this.indexDetail = 0;
    } else if (this.year == 2023) {
      this.indexDetail = 1;
    } else if (this.year == 2024) {
      this.indexDetail = 2;
    }
    // this.getPermission();
  }
}
</script>
<style media="screen">
.custom-h5 {
  font-size: 2em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  font-weight: 600;
}

.fh {
  font-size: 0.9em;
}

.table-info-new {
  background: #f4f7f9;
}

.grid-inputs-one-admin {
  display: grid;
  grid-template-columns: 25% 25% 50%;
  gap: 5px;
}
</style>
<style media="screen">
.btn-w-request {
  width: 90%;
  font-size: 10px;
}
.list-org {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.grid-check {
  display: grid;
  grid-template-columns: 80% 20%;
}

@media (max-width: 655px) {
  .list-org {
    display: grid;
    justify-content: left;
  }
}

.container-check {
  display: block;
  position: relative;
  padding-left: 35px;
  margin-bottom: 12px;
  cursor: pointer;
  /* font-size: 22px; */
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
/* Hide the browser's default checkbox */
.container-check input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 15px;
  width: 15px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.container-check:hover input ~ .checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.container-check input:checked ~ .checkmark {
  background-color: #2196f3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.container-check input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container-check .checkmark:after {
  left: 5px;
  top: 2px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 1px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

.grid-dates {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.5%;
  border-top: 1px solid #000;
  border-bottom: 1px solid #000;
  margin-top: 1%;
}

.card-days {
  text-align: left;
  display: grid;
  grid-template-columns: 10% 90%;
  height: min-content;
  border-top: 4px solid #6236ff;
  margin-top: 4%;
}

.card-days-two {
  border-top: 4px solid #6236ff;
  margin-top: 4%;
  text-align: left;
  display: grid;
  grid-template-columns: 78% 15%;
  height: min-content;
}

.card-days-three {
  text-align: left;
  display: grid;
  grid-template-columns: 1fr;
  height: min-content;
  border-top: 4px solid #6236ff;
  margin-top: 4%;
}

.card-three-sub-one {
  text-align: left;
  display: grid;
  grid-template-columns: 10% 90%;
  height: min-content;
}

.card-three-sub-four {
  text-align: left;
  display: grid;
  grid-template-columns: 90% 10%;
  height: min-content;
}

.card-three-sub-two {
  text-align: left;
  display: grid;
  grid-template-columns: 8% 92%;
  height: min-content;
  background: #ebe8f6;
}

.card-three-sub-two-second {
  text-align: left;
  display: grid;
  grid-template-columns: 8% 92%;
  height: min-content;
  background: #ebe8f6;
}

.card-three-sub-three {
  text-align: left;
  display: grid;
  grid-template-columns: 6% 94%;
  height: min-content;
  background: #ebe8f6;
}

.text-footer {
  display: flex;
  flex-direction: column;
  /* justify-content: flex-end; */
  align-items: flex-end;
}

.m0 {
  margin-bottom: 0;
  margin-top: 2%;
  font-size: 1em;
}

.mtb0 {
  margin-bottom: 0;
  margin-top: 0;
}

.color-black {
  color: #000000;
}
/* Stule in contetn */

span.square {
  background: transparent;
  border-radius: 5px;
  border-bottom: 1px solid #fff;
  display: inline-block;
  height: 10px;
  width: 10px;
  cursor: pointer;
}
.arrow {
  position: relative;
}
.arrow.up:after {
  position: absolute;
  top: 7px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent #000000 transparent;
  border-radius: 2px;
}

.arrow.up:before {
  position: absolute;
  top: 11px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

.arrow.down:after {
  position: absolute;
  top: 10px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: #000000 transparent transparent transparent;
  border-radius: 2px;
}

.arrow.down:before {
  position: absolute;
  top: 12px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

/* style gral */
span.square-gral {
  background: transparent;
  border-radius: 4px;
  border-bottom: 1px solid transparent;
  display: inline-block;
  height: 8px;
  width: 8px;
  cursor: pointer;
}
.arrow-gral {
  position: relative;
}
.arrow-gral.up-gral:after {
  position: absolute;
  top: 5px;
  left: 5px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent rgb(61, 112, 179) transparent;
  border-radius: 2px;
}

.arrow-gral.up-gral:before {
  position: absolute;
  top: 9px;
  left: 5px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

.arrow-gral.down-gral:after {
  position: absolute;
  top: 8px;
  left: 5px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: rgb(61, 112, 179) transparent transparent transparent;
  border-radius: 2px;
}

.arrow-gral.down-gral:before {
  position: absolute;
  top: 10px;
  left: 7px;
  width: 0;
  height: 0;
  content: "";
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: transparent transparent transparent transparent;
  border-radius: 2px;
}

.grid-buttons-up-down {
  display: flex;
  flex-direction: column;
}

.grid-buttons-plus {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.btn-plus {
  border-radius: 80%;
  height: 80%;
  border: 1px solid black;
  background: transparent;
  padding: 0;
  padding-left: 12%;
  padding-right: 12%;
  padding-bottom: 2%;
  padding-top: 2%;
  margin-left: 4%;
  font-size: 4rem;
}

.circle {
  width: 60%;
  height: 60%;
  border: 1px solid #000;
  border-radius: 100%;
  -moz-border-radius: 100%;
  -webkit-border-radius: 100%;
  text-align: center;
  font: small-caption;
  font-weight: bold;
  cursor: pointer;
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

/* Span buttons */
.span-present {
  border: 1px solid #accf60;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #accf60;
  background: #f4f8e9;
}

.span-absent {
  border: 1px solid #b0a1fa;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #836bf9;
  background: #f2f0fd ;
}

.span-request {
  border: 1px solid #db7e97;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #d63776;
  background: #f9e9ec;
}

.span-revision {
  border: 1px solid #c0c1c2;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #989da5;
  background: #eef0f2;
}

.span-finalizado {
  border: 1px solid #b0a1fa;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #836bf9;
  background: #f2f0fd;
}

.buttons-grid-sr {
  border: 1px solid #6236ff;
  text-align: center;
  align-self: end;
  background: #6236ff;
  border-radius: 15px;
  font-size: 14px;
  font-weight: 800;
  color: white;
  cursor: pointer;
  margin: 0 0px 0 -30%;
}

.button-options {

    margin-right: 2%;
    height: 30px;
    align-self: initial;
    padding-bottom: 9%;
}
</style>
