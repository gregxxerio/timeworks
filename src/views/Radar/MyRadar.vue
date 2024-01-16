<template>
  <div class="container-home">
    <!-- <div class="col-12"> -->
    <div class="card">
      <h1 class="title-home">Mi Radar C19</h1>
      <div class="" style="display:flex; justify-content: space-between;gap: 4%;">
        <a class="btn btn-outline-btn-vacation" @click="step = 1" :class="step == 1 ? 'active' : ''"><i></i> Autoevaluación <i></i></a>
        <a class="btn btn-outline-btn-vacation" @click="step = 2" :class="step == 2 ? 'active' : ''"><i></i> Infografía del proceso <i></i></a>
        <a class="btn btn-outline-btn-vacation" @click="step = 3" :class="step == 3 ? 'active' : ''"><i></i> Información <i></i></a>
        <a class="btn btn-outline-btn-vacation" @click="step = 4" :class="step == 4 ? 'active' : ''"><i></i> Contactos <i></i></a>

      </div>
      <div class="card-body">
        <div class="card border-shadow">
          <div class="card-header-radar">
            <p style="font-size; 18px; font-weight: bold;padding-top: 1%;color: #5886c7;">{{ step == 1 ? 'Autoevaluación' : step == 2 ? 'Infografía del proceso' : step == 3 ? 'Información' : step == 4 ? 'Contactos' : ''}}</p>
          </div>
          <div class="card-body">

            <div style="text-align: justify;" v-show="step == 1 && withData == 0">
              <h4 class="title_one mb-4">I. ¿Tienes alguno de estos síntomas graves? </h4>
              <ul class="title_two mb-4">
                <li>Piel o labios pálidos, grises o azules, según el tono de la piel.</li>
                <li>Dolor o presión severa y constante en el pecho.</li>
                <li>Dificultad para respirar (como jadear por aire, no poder caminar o hablar sin recuperar el aliento, sonido
                  silbante severo durante la respiración, fosas nasales dilatadas, gruñidos o estómago moviéndose hacia
                  adentro y hacia afuera profunda y rápidamente mientras respira).</li>
                  <li>Desorientación.</li>
                  <li>Inconsciente o muy difícil de despertar.</li>
                  <li>Dificultad para hablar (nueva o que empeora).</li>
                  <li>Convulsiones nuevas o que empeoran.</li>
                  <li>Signos de presión arterial baja (demasiado débil para estar de pie, mareos, aturdimiento, sensación de frío,
                    piel pálida y húmeda).</li>
                    <li>Deshidratación (labios y boca secos, no orinar mucho, ojos hundidos). </li>
                  </ul>
                  <div style="display:flex;">
                    <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                      <input type="checkbox" class="custom-control-input" @change="partOne('one', $event)" id="exampleCheck1" v-model="evaluationPartOne.one">
                      <label class="custom-control-label" style="font-size: 16px;" for="exampleCheck1"> Si</label>
                    </div>
                    <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                      <input type="checkbox" class="custom-control-input" @change="partOne('two', $event)" id="exampleCheck2" v-model="evaluationPartOne.two">
                      <label class="custom-control-label" style="font-size: 16px;" for="exampleCheck2"> No</label>
                    </div>
                  </div>
                  <!-- Fin de parte uno -->
                  <hr color="#989898" class="mb-4">
                  <!-- Parte dos -->
                  <h4 class="title_one mb-3">II. En las últimas dos semanas, ¿has estado en contacto con alguna persona que
                    haya sido diagnosticada con COVID-19 o que sea un caso sospechoso?</h4>
                    <label class="title_two mb-4">Marcar <b class="bold-title">“no”</b> si, aunque tuviste contacto, ya se ha descartado que tu seas un caso sospechoso o positivo.</label>
                    <label class="title_two"><b class="bold-title">Nota: Has estado en contacto si:</b></label>
                    <ul class="title_two">
                      <li> has estado a menos de dos metros de una persona con COVID-19 durante un total de 15 minutos o más.</li>
                      <li> has atendido en casa a una persona enferma de COVID-19.</li>
                      <li> has tenido contacto físico directo (abrazos o besos) con alguien que tiene COVID-19.</li>
                      <li> has compartido utensilios para comer o beber con alguien que tiene COVID-19.</li>
                      <li> has estornudado o tosido en compañía de alguien que tiene COVID-19.</li>
                    </ul>

                    <div style="display:flex;">
                      <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                        <input type="checkbox" class="custom-control-input" :disabled="two" @change="partTwo('one', $event)" id="customRadioInline3" v-model="evaluationPartTwo.one">
                        <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline3"> Si</label>
                      </div>
                      <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                        <input type="checkbox" class="custom-control-input" :disabled="two" @change="partTwo('two', $event)" id="customRadioInline4" v-model="evaluationPartTwo.two">
                        <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline4"> No</label>
                      </div>
                    </div>
                    <!-- Fin de parte dos -->
                    <hr color="#989898" class="mb-4">
                    <!-- Parte tres -->
                    <h4 class="title_one mb-4">III. Las siguientes preguntas contéstalas con base en tus síntomas durante las
                      últimas 10 horas</h4>

                      <label class="title_two mb-2">1. ¿Has tenido fiebre? (Temperatura igual o mayor a 37.5°C)</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question1Yes',$event)" id="customRadioInline5" name="customRadioInline5" class="custom-control-input" v-model="evaluationForm.question1Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline5">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question1No', $event)" id="customRadioInline6" name="customRadioInline6" class="custom-control-input" v-model="evaluationForm.question1No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline6">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">2. ¿Has tenido dolor de cabeza?</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question2Yes', $event)" id="customRadioInline7" name="customRadioInline7" class="custom-control-input" v-model="evaluationForm.question2Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline7">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question2No', $event)" id="customRadioInline8" name="customRadioInline8" class="custom-control-input" v-model="evaluationForm.question2No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline8">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">3. ¿Has tenido tos?</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question3Yes', $event)" id="customRadioInline9" name="customRadioInline9" class="custom-control-input" v-model="evaluationForm.question3Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline9">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question3No', $event)" id="customRadioInline10" name="customRadioInline10" class="custom-control-input" v-model="evaluationForm.question3No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline10">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">4. ¿Has tenido fatiga inusual?</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question4Yes', $event)" id="customRadioInline11" name="customRadioInline11" class="custom-control-input" v-model="evaluationForm.question4Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline11">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question4No', $event)" id="customRadioInline12" name="customRadioInline12" class="custom-control-input" v-model="evaluationForm.question4No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline12">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">5. ¿Has tenido dolor de garganta?</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question5Yes', $event)" id="customRadioInline13" name="customRadioInline13" class="custom-control-input" v-model="evaluationForm.question5Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline13">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question5No', $event)" id="customRadioInline14" name="customRadioInline14" class="custom-control-input" v-model="evaluationForm.question5No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline14">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">6. ¿Has tenido congestión o secreción nasal?</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question6Yes', $event)" id="customRadioInline15" name="customRadioInline15" class="custom-control-input" v-model="evaluationForm.question6Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline15">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question6No', $event)" id="customRadioInline16" name="customRadioInline16" class="custom-control-input" v-model="evaluationForm.question6No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline16">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">7. ¿Has tenido dolores musculares o corporales?</label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question7Yes', $event)" id="customRadioInline17" name="customRadioInline17" class="custom-control-input" v-model="evaluationForm.question7Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline17">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question7No', $event)" id="customRadioInline18" name="customRadioInline18" class="custom-control-input" v-model="evaluationForm.question7No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline18">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">8. ¿Has tenido diarrea o vómito? </label>
                      <br>
                      <div style="display:flex;">
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question8Yes', $event)" id="customRadioInline19" name="customRadioInline19" class="custom-control-input" v-model="evaluationForm.question8Yes">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline19">Sí</label>
                        </div>
                        <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                          <input type="checkbox" :disabled="three" @change="partThree('question8No', $event)" id="customRadioInline20" name="customRadioInline20" class="custom-control-input" v-model="evaluationForm.question8No">
                          <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline20">No</label>
                        </div>
                      </div>
                      <br>

                      <label class="title_two mb-2">9. ¿Has perdido el olfato o el gusto? (De forma nueva, y no como efecto a largo plazo de haber
                        tenido COVID).</label>
                        <br>
                        <div style="display:flex;">
                          <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                            <input type="checkbox" :disabled="three" @change="partThree('question9Yes', $event)" id="customRadioInline21" name="customRadioInline21" class="custom-control-input" v-model="evaluationForm.question9Yes">
                            <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline21">Sí</label>
                          </div>
                          <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                            <input type="checkbox" :disabled="three" @change="partThree('question9No', $event)" id="customRadioInline22" name="customRadioInline22" class="custom-control-input" v-model="evaluationForm.question9No">
                            <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline22">No</label>
                          </div>
                        </div>

                        <!-- Fin de parte tres -->
                        <template >

                          <hr color="#989898" class="mb-4">

                          <h4 class="title_one mb-4">IV. ¿Tienes comentarios o sugerencias por compartirnos?</h4>
                          <div style="display: flex;">

                            <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                              <input type="checkbox"  @change="partFour('question40Yes')" id="customRadioInline40" name="customRadioInline40" class="custom-control-input" v-model="evaluationFormFour.fourYes">
                              <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline40">Sí</label>
                            </div>
                            <div class="custom-control form-control-lg custom-checkbox" style="padding; 4%;">
                              <input type="checkbox"  @change="partFour('question41No')" id="customRadioInline41" name="customRadioInline41" class="custom-control-input" v-model="evaluationFormFour.fourNo">
                              <label class="custom-control-label" style="font-size: 16px;" for="customRadioInline41">No</label>
                            </div>
                          </div>
                          <br>
                          <textarea name="name" class="form-control" rows="4" cols="4" v-model="text" :disabled="natext"></textarea>
                        </template>


                        <hr color="#989898" class="mb-4">

                        <div class="form-group">
                          <center>
                            <button type="button" class="btn btn-outline-success btn-md" @click="result">Ver resultado</button>
                          </center>
                        </div>

                      </div>

                      <div v-show="withData == 1 && resultEvaluate.result == 'Rojo' && step == 1">
                        <div class="row" style="padding;0;margin:-1%;">
                          <div class="col-md-12 " style="background-color:#f8f8f8; box-shadow: 7px 10px 18px -15px;border-left:70px solid #e02020;" >
                            <div class="row">
                              <h1>&nbsp;</h1>
                              <div class="col-md-12 mb-5">
                                <h3
                                style="font-family: 'IBM Semi Bold', sans-serif;
                                color: #e02020;">Tu resultado para hoy es semáforo rojo.
                              </h3>
                            </div>
                            <div class="col-md-12 mb-2">
                              <h4
                              style="font-family: 'IBM Condensed Regular', sans-serif;
                              color: #213239;">Busca ayuda médica inmediatamente.
                            </h4>
                          </div>
                          <div class="col-md-12 mb-2">
                            <h4
                            style="font-family: 'IBM Condensed Bold', sans-serif;
                            color: #1b3233a;">Si tenías que acudir a la oficina avisa a Victoria que no te presentarás porque tu resultado fue semáforo rojo.
                          </h4>
                          <button type="button" class="btn btn-outline-warning"
                          style="font-family: 'IBM Plex Sans Condensed', sans-serif;" @click="step = 4">
                          Ver teléfonos de emergencia
                        </button>
                        <button type="button" class="btn btn-outline-warning"
                        style="font-family: 'IBM Plex Sans Condensed', sans-serif;" @click="step = 4">
                        Ver contactos de oficina
                      </button>
                    </div>
                  </div>

                  <div class="row">
                    <div class="col-md-12" >

                      <ul class="ul-font" style="padding-top:20px;text-align: justify;">
                        <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                        Al asistir a una evaluación médica lleva mascarilla, mantente al
                        menos a un metro de distancia de las demás personas y no toques
                        las superficies con las manos.
                      </li>
                      <li
                      style="font-family: 'IBM Condensed Regular', sans-serif;
                      color: #1b3233a;">Te recomendamos someterte a una prueba diagnóstica y en caso de
                      que lo hagas, te pedimos que compartas tus resultados con la
                      Coordinación de Talento.
                    </li>
                    <li
                    style="font-family: 'IBM Condensed Regular', sans-serif;
                    color: #1b3233a;">Sigue las instrucciones sobre los cuidados que te dé tu proveedor de
                    atención médica y departamento de salud local. Las autoridades de
                    salud locales te darán instrucciones sobre cómo vigilar tus síntomas
                    y notificar la información.
                  </li>
                  <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;">Sigue contestando esta evaluación diariamente, en caso de que te
                  sea posible.
                </li>
                <li
                style="font-family: 'IBM Condensed Regular', sans-serif;
                color: #1b3233a;">La <span style="color: #E02020; font-weight: bold;">Coordinación de Talento</span> te estará buscando para darle
                seguimiento a tu caso, pero cualquier cambio significativo que
                descubras en tus síntomas, por favor avisa a Victoria y a Mirna y, en
                caso de que no te sea posible, dile a tu contacto de emergencia que
                lo haga por ti.
              </li>
              <li
              style="font-family: 'IBM Condensed Regular', sans-serif;
              color: #1b3233a;">
              Te invitamos a revisar la sección
              <span style="color: #E02020; font-weight: bold;">Información</span> para consultar las
              fuentes de información oficiales sobre el tema.
            </li>
          </ul>

        </div>
      </div>
    </div>

  </div>
</div>
<div v-show="withData == 1 && resultEvaluate.result == 'Naranja' && step == 1">
  <div class="col-md-12" style="background-color:#f8f8f8;box-shadow: 7px 10px 18px -15px;border-left:70px solid #fa6400;" >
      <div class="row">
        <h1>&nbsp;</h1>
        <div class="col-md-12 mb-2">
            <h4
              style="font-family: 'IBM Semi Bold', sans-serif;
                  color: #fa6400;">Tu resultado para hoy es semáforo naranja.
            </h4>
        </div>
        <div class="col-md-12 mb-2">
            <h4
              style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #213239;">Quédate en casa. Podrías ser un caso sospechoso.
            </h4>
        </div>
        <div class="col-md-12 mb-2">
            <h4
              style="font-family: 'IBM Condensed Bold', sans-serif;
                color: #1b3233a;">De acuerdo con este cuestionario no eres caso sospechoso, pero debido al síntoma que presentaste de “escurrimiento nasal” te recomendamos trabajar desde casa.
            </h4>
            <button type="button" class="btn btn-outline-warning"   @click="step = 4"                  style="font-family: 'IBM Plex Sans Condensed', sans-serif;" >Ver contactos de emergencia
            </button>
            <button type="button" class="btn btn-outline-warning"   @click="step = 4"                  style="font-family: 'IBM Plex Sans Condensed', sans-serif;">Ver contactos de oficina
            </button>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12">
            <ul class="ul-font" style="padding-top:20px;text-align:justify;">
              <li
                style="font-family: 'IBM Condensed Regular', sans-serif;
                color: #1b3233a;">
                     Si tenías que acudir a la oficina avisa a tu Supervisor(a) y a la
                     <span style="color: #FA6400; font-weight: bold;" @click="step = 4">Coordinación de Talento</span> (contactos de oficina) que no te
                     presentarás porque tu resultado fue semáforo naranja.
              </li>
              <br>
              <li
                style="font-family: 'IBM Condensed Regular', sans-serif;
                color: #1b3233a;">Si tienes síntomas leves, como tos o fiebre leves, generalmente no es
                    necesario que busques atención médica. Autoaíslate en casa
                    durante los siguientes 14 días y monitorea tus síntomas. A
                    continuación te compartimos las orientaciones nacionales sobre el
                    autoaislamiento:
                    <ul class="ul-font" style="text-align:justify;">
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Ocupa una habitación individual amplia y bien ventilada con retrete y lavabo.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Si esto no es posible, coloca las camas al menos a un metro de distancia.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Mantente al menos a un metro de distancia de los demás,
                          incluso de los miembros de tu familia.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Controla tus síntomas diariamente.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Aíslate durante 14 días, incluso si te sientes bien.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Si tienes dificultades para respirar, ponte en contacto
                          inmediatamente con tu dispensador de atención de salud.
                          Llama por teléfono primero si es posible.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Permanece positivo(a) y con energía manteniendo el contacto
                          con tus seres queridos por teléfono o internet y haciendo
                          ejercicio en casa.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Si decides acudir a una evaluación médica lleva cubrebocas,
                          mantente al menos a un metro de distancia de las demás
                          personas y no toques las superficies con las manos.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Si decides hacer una prueba diagnóstica, te pedimos que
                          compartas tus resultados con la Coordinación de Talento.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                        Sigue contestando esta evaluación diariamente.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          La Coordinación de Talento te estará buscando para darle
                          seguimiento a tu caso, pero cualquier cambio significativo que
                          descubras en tus síntomas, por favor avisa a Victoria y a Mirna.
                      </li>
                      <li
                        style="font-family: 'IBM Condensed Regular', sans-serif;
                        color: #1b3233a;">
                          Te invitamos a consultar la sección
                        <span style="color: #FA6400; font-weight: bold;">Información</span>
                          para revisar las fuentes de información oficiales sobre el tema.
                      </li>
                </ul>
            </li>
          </ul>
        </div>
      </div>
    </div>
</div>

<div v-show="withData == 1 && resultEvaluate.result == 'Verde' && step == 1">
  <div class="col-md-12" style="background-color:#f8f8f8;box-shadow: 7px 10px 18px -15px;border-left:70px solid #4bb774;" >
                    <div class="row">
                      <h1>&nbsp;</h1>
                      <div class="col-md-12">
                          <h4
                           style="font-family: 'IBM Semi Bold', sans-serif;
                          color: #4bb774;">Tu resultado para hoy es
                          semáforo verde.</h4>
                      </div>
                      <div class="col-md-12">
                          <h4
                           style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #213239;">Puedes presentarte en la oficina (si te corresponde ir).</h4>
                      </div>
                      <div class="col-md-12">
                          <h4
                           style="font-family: 'IBM Condensed Bold', sans-serif;
                          color: #1b3233a;">Si tienes que acudir a la oficina, te pedimos considerar las siguientes recomendaciones:</h4>
                      </div>
                    </div>
                    <div class="row">
                      <br>
                      <label>&nbsp;</label>
                      <div class="col-md-12 mb-2">
                          <h4
                           style="font-family: 'IBM Condensed Bold', sans-serif;
                          color: #1b3233a;">Antes de salir de casa no olvides:</h4>
                          <ul class="ul-font" style="padding-top:20px;text-align: justify;">
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">
                              Utilizar tu cubrebocas correctamente (cubriendo boca y nariz).
                          </li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Llevar en tus pertenencias gel antibacterial a base de alcohol (con
                          60-80% de alcohol).</li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Si tienes que recorrer distancias cortas, opta por ir a pie o en
                          bicicleta.</li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Evita el transporte público en las horas pico. Si no te es posible
                          recuerda utilizar tu cubrebocas correctamente, conservar la sana
                          distancia y evitar ingerir alimentos o bebidas durante todo el
                          trayecto.</li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Si vas a ocupar tu automóvil personal sigue estos consejos de
                          limpieza:
                              <ul class="ul-font">
                                   <br>
                                  <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Antes de aplicar cualquier desinfectante, primero haz una
                                  prueba para asegurarte que el interior del auto no se dañará,
                                  recuerda usar un paño suave.</li>
                                  <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Si prefieres usar agua y jabón, también es una opción, sin
                                  embargo, para eso evita los productos que digan “libre de
                                  detergentes” ya que no serán eficaces -evita el amoniaco-.</li>
                                  <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Recuerda lavar el paño que estas usando después de limpiar
                                  cada parte como la palanca de velocidades, el freno de mano, la
                                  pantalla de la consola, el estéreo, los botones de las ventanillas
                                  y espejos, el cinturón de seguridad, las perillas del aire
                                  acondicionado y los porta-vasos.</li>
                                  <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Te recomendamos abrir la ventana unos 8 centímetros para
                                  disminuir significativamente las concentraciones de
                                  contaminantes en el aire en el interior del transporte.</li>
                              </ul>
                          </li>
                        </ul>
                        <br>
                        <h4
                         style="font-family: 'IBM Condensed Bold', sans-serif;
                          color: #1b3233a;
                          ">Al llegar a la oficina:</h4>
                        <ul class="ul-font" style="text-align: justify;">
                            <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Se te tomará la temperatura. De acuerdo con los resultados habrá
                              dos escenarios:
                              <ul class="ul-font">
                                  <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">En caso de que esta sea igual o mayor a la 37.5°C tendrás que
                                  regresar a casa y recibirás una notificación en slack con las
                                  recomendaciones del semáforo naranja, o bien, puedes
                                  consultarlas

                                      <a href="">aquí.</a>

                                  </li>
                                  <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">En caso de que sea menor a 37.5°C se te otorgará el ingreso a la
                                  oficina. Además, te pedimos seguir y respetar las medidas de
                                  protección establecidas.</li>
                              </ul>
                            </li>
                        </ul>
                        <br>
                        <h4
                         style="font-family: 'IBM Condensed Bold', sans-serif;
                          color: #1b3233a;
                          ">Si tienes que trabajar desde tu casa no olvides:</h4>
                        <ul class="ul-font" style="text-align:justify;">
                            <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Lavarte las manos cada media hora con agua y jabón durante 40 - 60
                              segundos.</li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Evitar tocar tus ojos, nariz y boca con las manos sin lavar.</li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Tener ventilado tu espacio de trabajo.</li>
                          <li  style="font-family: 'IBM Condensed Regular', sans-serif;
                          color: #1b3233a;
                          ">Desinfectar constantemente tu espacio y herramientas de trabajo.</li>
                        </ul>
                      </div>
                    </div>
                  </div>
</div>
<div v-show="withData == 1 && resultEvaluate.result == 'Amarillo' && step == 1">
  <div class="col-md-12" style="background-color:#f8f8f8;box-shadow: 7px 10px 18px -15px;border-left:70px solid #f7b500;" >
          <div class="row">
            <h1>&nbsp;</h1>
            <div class="col-md-12 mb-2">
                <h4
                  style="font-family: 'IBM Semi Bold', sans-serif;
                  color: #f7b500;">
                    Tu resultado para hoy es semáforo amarillo.
                </h4>
            </div>
            <div class="col-md-12 mb-2">
                <h4
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #213239;">
                    Te recomendamos quedarte en casa.
                </h4>
            </div>
            <div class="col-md-12 mb-2">
                <h4
                  style="font-family: 'IBM Condensed Bold', sans-serif;
                  color: #1b3233a;">
                    De acuerdo con este cuestionario no eres caso sospechoso, pero debido al síntoma que presentaste de “escurrimiento nasal” te recomendamos trabajar desde casa.
                </h4>
                <button type="button" class="btn btn-outline-warning"
                  style="font-family: 'IBM Plex Sans Condensed', sans-serif;" @click="step = 4;">
                  Ver contactos de oficina
                </button>
            </div>
          </div>
          <div class="row">
            <div class="col-md-12" >
                <ul class="ul-font" style="padding-top:20px;text-align: justify;">
                <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;
                  ">
                    Si tenías que acudir a la oficina avisa a tu Supervisor(a) y a la
                    <span style="color: #F7B500; font-weight: bold;" >Coordinación de Talento</span> (contactos de oficina) que no te
                    presentarás porque tu resultado fue semáforo amarillo.
                </li>
                <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;
                  ">
                    Monitorea tus síntomas durante el día.
                </li>
                <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;
                  ">
                    Si decides acudir a una evaluación médica lleva cubrebocas,
                    mantente al menos a un metro de distancia de las demás personas y
                    no toques las superficies con las manos.
                </li>
                <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;
                  ">
                    Sigue contestando esta evaluación diariamente.
                </li>
                <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;
                  ">
                    La Coordinación de Talento te estará buscando para darle
                    seguimiento a tu caso, pero cualquier cambio significativo que
                    descubras en tus síntomas, por favor avisa a Victoria y a Mirna.
                </li>
                <li
                  style="font-family: 'IBM Condensed Regular', sans-serif;
                  color: #1b3233a;
                  ">
                    Te invitamos a consultar la sección
                  <span style="color: #F7B500; font-weight: bold;" >Información</span>
                    para revisar las fuentes de información oficiales sobre el tema.
                </li>
              </ul>
            </div>
          </div>
        </div>
</div>

<div class="" v-show="step == 2">
  <infographic ></infographic>

</div>

<div class="" v-show="step == 3">
  <informative-section ></informative-section>

</div>

<div class="" v-show="step == 4">
  <contact ></contact>

</div>


</div>
</div>
</div>
</div>
<!-- </div> -->
</div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import SideBar from '../../components/SideBar.vue';
import axios from "axios";
import { Modal } from 'ant-design-vue';
import { message } from 'ant-design-vue';
import Infographic from "@/views/Radar/Infographic.vue";
import InformativeSection from "@/views/Radar/InformativeSection.vue";
import Contact from "@/views/Radar/Contact.vue";
import moment from "moment"
import proxy from "@/helpers/proxy";

@Component({
  components:{
    SideBar, Infographic, InformativeSection, Contact
  }
})
export default class MyRadar extends Vue {
  step: any = 0;
  text: any = '';
  natext: any = true;
  withData: any = false;
  visibleOrange = false;
  visibleYellow = false;
  visibleLoading = false;
  resultEvaluateresult: any = '';
  resultEvaluateresultYellow: any = '';

  evaluationPartOne: any = {
    one : false,
    two : false,
  }
  evaluationPartTwo: any = {
    one : false,
    two : false,
  }
  evaluationForm: any =  {
    question1Yes: 0,
    question2Yes: 0,
    question3Yes: 0,
    question4Yes: 0,
    question5Yes: 0,
    question6Yes: 0,
    question7Yes: 0,
    question8Yes: 0,
    question9Yes: 0,
    question1No: 0,
    question2No: 0,
    question3No: 0,
    question4No: 0,
    question5No: 0,
    question6No: 0,
    question7No: 0,
    question8No: 0,
    question9No: 0,
  }

  two = true;
  three = true;
  evaluationFormFour: any = {
    fourYes: 0,
    fourNo: 0,
  }
  resultEvaluate: any = {
    result : ''
  };

  created() {
          this.confirmEvalaute();
        }

        async confirmEvalaute(){
            const date  = moment().format();
            const newDate = date.split('T')
            const confirm = await axios.get('/api/evaluation-validate/'+newDate[0]);

            if (confirm.data) {
              this.showConfirm(confirm.data);
            }
          }

          showConfirm(data: any){
            this.resultEvaluate.result = (data == 1 ? 'Rojo' : data == 2 ? 'Naranja' : data == 3 ? 'Amarillo' : data == 4 ? 'Verde' : data == 5 ? 'Verde' : '');
            this.withData = 1;
            message.error('No es posible realizar la evaluación debido a que ya se tiene un registro del día de hoy');
          }

  partOne(data: string, event: { target: HTMLInputElement }){

    if (data === 'one') {
      if (this.evaluationPartOne.two == true) {
        this.evaluationPartOne.two = false;
      }
      const confirm = Modal.confirm;
      confirm({
        title: '¿Estás seguro de su respuesta?',
        content: '',
        okText: 'OK',
        cancelText: 'Cancelar',
        onOk: () => {
          // this.visibleLoading = true;
          this.resultEvaluate.result = 'Rojo';
          axios.post('/api/user-evaluation-result',this.resultEvaluate).then(response => {
            this.withData = 1;
            // this.visibleLoading = false;
          }).catch(e => {
            this.withData = 1;
            this.resultEvaluate.result = 'Rojo';
            // this.visibleLoading = true;
          });
        },
        onCancel: () => {
          this.evaluationPartOne.one = false;
        }
      });

    }else if (data === 'two'){

      if (event.target.checked == true) {
        this.two = false;
      }else{
        this.two = true;
      }
      if (this.evaluationPartOne.one == true) {
        this.evaluationPartOne.one = false;
      }
    }
  }

  partTwo(data: string, event: { target: HTMLInputElement }){
    if (data === 'one') {
      if (this.evaluationPartTwo.two == true) {
        this.evaluationPartTwo.two = false;
      }
      const confirm = Modal.confirm;
      confirm({
        title: '¿Estás seguro de su respuesta?',
        content: '',
        okText: 'OK',
        cancelText: 'Cancelar',
        onOk: () => {
          this.resultEvaluate.result  = 'Naranja';
          axios.post('/api/user-evaluation-result',this.resultEvaluate).then(response =>{
            // this.$router.replace("/orange-status");
            this.withData = 1;
            this.visibleOrange = true;
          }).catch(e => {
            this.resultEvaluate.result  = 'Naranja';
            this.withData = 1;
            this.visibleOrange = true;
          });
        },
        onCancel: () => {console.log('cancel');
      }
    });
  }else if (data === 'two'){
    if (event.target.checked == true) {
      this.three = false;
    }else{
      this.three = true;
    }

    if (this.evaluationPartTwo.one == true) {
      this.evaluationPartTwo.one = false;
    }
  }
}

partThree(data: string, event: { target: HTMLInputElement }){
  // console.log(event,'evento');

  // const scale_one = 0;
  // const scale_two = 0;
  // const scale_three = 0;
  // const scale_four = 0;
  // const scale_five = 0;
  if (data === 'question1No') {
    if (this.evaluationForm.question1Yes == true) {
      this.evaluationForm.question1Yes = false;
    }
    this.resultEvaluateresult = (this.resultEvaluateresult).replace("1", "","gi");
  }else if (data === 'question1Yes'){
    if (this.evaluationForm.question1No == true) {
      this.evaluationForm.question1No = false;
    }
    if (!event.target.checked && this.resultEvaluateresult != '') {
      this.resultEvaluateresult = (this.resultEvaluateresult).replace("1", "","gi");
    }
    if (event.target.checked) {
      this.resultEvaluateresult += '1';
    }
  }

  if (data === 'question2No') {
    if (this.evaluationForm.question2Yes == true) {
      this.evaluationForm.question2Yes = false;
    }
  }else if (data === 'question2Yes'){
    if (this.evaluationForm.question2No == true) {
      this.evaluationForm.question2No = false;
    }
  }

  if (data === 'question3No') {
    if (this.evaluationForm.question3Yes == true) {
      this.evaluationForm.question3Yes = false;
    }
    this.resultEvaluateresult = this.resultEvaluateresult.replace("2", "");
  }else if (data === 'question3Yes'){
    if (this.evaluationForm.question3No == true) {
      this.evaluationForm.question3No = false;
    }
    if (!event.target.checked && this.resultEvaluateresult != '') {
      this.resultEvaluateresult = this.resultEvaluateresult.replace("2", "");
    }
    if (event.target.checked) {
      this.resultEvaluateresult += '2';
    }
  }

  if (data === 'question4No') {
    if (this.evaluationForm.question4Yes == true) {
      this.evaluationForm.question4Yes = false;
    }
  }else if (data === 'question4Yes'){
    if (this.evaluationForm.question4No == true) {
      this.evaluationForm.question4No = false;
    }
  }

  if (data === 'question5No') {
    if (this.evaluationForm.question5Yes == true) {
      this.evaluationForm.question5Yes = false;
    }
  }else if (data === 'question5Yes'){
    if (this.evaluationForm.question5No == true) {
      this.evaluationForm.question5No = false;
    }
  }

  if (data === 'question6No') {
    if (this.evaluationForm.question6Yes == true) {
      this.evaluationForm.question6Yes = false;
    }
    this.resultEvaluateresultYellow = this.resultEvaluateresultYellow.replace("6", "");

  }else if (data === 'question6Yes'){
    if (this.evaluationForm.question6No == true) {
      this.evaluationForm.question6No = false;
    }
    if (!event.target.checked && this.resultEvaluateresultYellow != '') {
      this.resultEvaluateresultYellow = this.resultEvaluateresultYellow.replace("6", "");
    }
    if (event.target.checked) {
      this.resultEvaluateresultYellow += '6';
    }
  }

  if (data === 'question7No') {
    if (this.evaluationForm.question7Yes == true) {
      this.evaluationForm.question7Yes = false;
    }
  }else if (data === 'question7Yes'){
    if (this.evaluationForm.question7No == true) {
      this.evaluationForm.question7No = false;
    }
  }

  if (data === 'question8No') {
    if (this.evaluationForm.question8Yes == true) {
      this.evaluationForm.question8Yes = false;
    }
  }else if (data === 'question8Yes'){
    if (this.evaluationForm.question8No == true) {
      this.evaluationForm.question8No = false;
    }
  }

  if (data === 'question9No') {
    if (this.evaluationForm.question9Yes == true) {
      this.evaluationForm.question9Yes = false;
    }
    this.resultEvaluateresult = this.resultEvaluateresult.replace("3", "");
  }else if (data === 'question9Yes'){
    if (this.evaluationForm.question9No == true) {
      this.evaluationForm.question9No = false;
    }
    if (!event.target.checked && this.resultEvaluateresult != '') {
      this.resultEvaluateresult = this.resultEvaluateresult.replace("3", "");
    }
    if(event.target.checked) {
      this.resultEvaluateresult += '3';
    }
  }
}

partFour(data: string){
  if (data === 'question40Yes') {
    this.natext = false;
    this.evaluationFormFour.fourNo = false;
  }else if(data === 'question41No'){
    this.natext = true;
    this.evaluationFormFour.fourYes = false;
  }
}

showErrors(){
  console.log("errors")
}

async result(){
  let inCorrectAnswers = 0;
  // let scaleone = 0;
  // let scaletwo = 0;
  // let scalethree = 0;
  //Se realiza un contador de las respuestas existentes
  Object.values(this.evaluationForm).forEach(answers => {
    if (answers == false) {
      inCorrectAnswers++
    }
  });

  //se evalua que existan todas las respuestas contestadas de lo contrario lanza un mensaje de advertencia
  if(inCorrectAnswers < 9 ){
    this.showErrors();

  }else if(inCorrectAnswers < 9 && (this.resultEvaluateresult === '' && this.resultEvaluateresult === '')){
    this.showErrors();
  }else if(inCorrectAnswers > 8 && this.resultEvaluateresultYellow === '' && this.resultEvaluateresult === '' && (this.evaluationFormFour.fourNo == 0 && this.evaluationFormFour.fourYes == 0 && this.text === '')){
    this.showErrors();
  }else if(inCorrectAnswers > 8 && this.resultEvaluateresultYellow === '' && this.resultEvaluateresult === '' && (this.evaluationFormFour.fourNo == 0 && this.evaluationFormFour.fourYes == 1 && this.text === '')){
    this.showErrors();
  }else {

    // scaleone = this.evaluationForm.question1Yes  + this.evaluationForm.question3Yes + this.evaluationForm.question9Yes ;
    // scaletwo = this.evaluationForm.question6Yes;
    // scalethree = this.evaluationForm.question2Yes + this.evaluationForm.question4Yes + this.evaluationForm.question5Yes + this.evaluationForm.question7Yes + this.evaluationForm.question8Yes;
    //Naranja
    if (this.resultEvaluateresult != '' ) {
      this.resultEvaluate.result  = 'Naranja';
      await axios.post('/api/user-evaluation-result',this.resultEvaluate).then(response => {
        this.withData = 1;
        this.visibleOrange = true;

        if (this.text != '') {
          this.visibleLoading = true;
          axios.post(proxy.api.domain + '/api/send-mail-green',{
            'text' : this.text
          }).then(response => {
            if (response.status) {
              this.visibleYellow = false;
              this.withData = 1;
              // this.$router.replace("/green-status");
            }else{
              this.withData = 1;
              // this.$router.replace("/green-status");
            }
            this.visibleLoading = false;
          }).catch(e => {
            this.visibleLoading = false;
            this.withData = 1;
            // this.$router.replace("/green-status");
          });
        }else{
          this.withData = 1;
          // this.$router.replace("/green-status");
        }

      }).catch(e => {
        this.resultEvaluate.result  = 'Naranja';
        this.withData = 1;
      });
    }
    //Amarrillo
    if (this.resultEvaluateresultYellow != '' && this.resultEvaluateresult === '') {
      this.resultEvaluate.result  = 'Amarillo';
      await axios.post('/api/user-evaluation-result',this.resultEvaluate).then(response => {
        this.withData = 1;
        this.visibleYellow = true;

        if (this.text != '') {
          this.visibleLoading = true;
          axios.post(proxy.api.domain + '/api/send-mail-green',{
            'text' : this.text
          }).then(response => {
            if (response.status) {
              this.visibleYellow = false;
              this.withData = 1;
              // this.$router.replace("/green-status");
            }else{
              this.withData = 1;
              // this.$router.replace("/green-status");
            }
            this.visibleLoading = false;
          }).catch(e => {
            this.visibleLoading = false;
            this.withData = 1;
            // this.$router.replace("/green-status");
          });
        }else{
          this.withData = 1;
          // this.$router.replace("/green-status");
        }
        
      }).catch(e => {
        this.resultEvaluate.result  = 'Amarillo';
        this.withData = 1;
      });
    }
    //Verde
    if (this.resultEvaluateresultYellow === '' && this.resultEvaluateresult === '') {
      this.resultEvaluate.result = 'Verde';
      await axios.post('/api/user-evaluation-result',this.resultEvaluate).then(response => {

        if (this.text != '') {
          this.visibleLoading = true;
          axios.post(proxy.api.domain + '/api/send-mail-green',{
            'text' : this.text
          }).then(response => {
            if (response.status) {
              this.visibleYellow = false;
              this.withData = 1;
              // this.$router.replace("/green-status");
            }else{
              this.withData = 1;
              // this.$router.replace("/green-status");
            }
            this.visibleLoading = false;
          }).catch(e => {
            this.visibleLoading = false;
            this.withData = 1;
            // this.$router.replace("/green-status");
          });
        }else{
          this.withData = 1;
          // this.$router.replace("/green-status");
        }

      }).catch(e =>{
        this.withData = 1;
        // this.$router.replace("/green-status");
        //
      });

    }
    // if (scaleone > 1 && scaletwo > 0) {
    //   this.resultEvaluate.result  = 'Naranja';
    //   await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //   this.$router.replace("/orange-status");
    // }else if(scaleone == 1 && scalethree > 0){
    //   this.resultEvaluate.result  = 'Naranja';
    //   await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //   this.$router.replace("/orange-status");
    // }else if(scaleone == 0 && scaletwo == 1 && scalethree >= 0){
    //   this.resultEvaluate.result  = 'Amarillo';
    //   await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //   this.$router.replace("/yellow-status");
    // }else if(scaleone == 0 && scaletwo == 0 && scalethree > 0){
    //   this.resultEvaluate.result = 'Verde';
    //   await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //   this.$router.replace("/green-status");
    // }else if(scaleone == 0 && scaletwo == 0 && scalethree == 0 ){
    //   this.resultEvaluate.result = 'Verde';
    //   await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //   this.$router.replace("/green-status");
    // }

    // if (this.evaluationForm.question7 == "true") {
    //   //return rojo
    //   this.resultEvaluate.result = 'Rojo';
    //   await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //   // api/user-evaluation-result
    //   this.$router.replace("/red-status");
    // }else{
    //   if (this.evaluationForm.question1 == "true") {
    //     //return  naranja
    //     this.resultEvaluate.result  = 'Naranja';
    //     await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //     this.$router.replace("/orange-status");
    //   } else {
    //     if ((this.mediumRisk() >= 2 || this.mediumRisk() == 1) && this.otherQuestions() >= 1) {
    //       // return  naranja
    //       this.resultEvaluate.result  = 'Naranja';
    //       await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //       this.$router.replace("/orange-status");
    //     } else{
    //
    //       if (this.evaluationForm.question8 == "true" ) {
    //         // return amarillo
    //         this.resultEvaluate.result  = 'Amarillo';
    //         await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //         this.$router.replace("/yellow-status");
    //       } else {
    //         this.resultEvaluate.result = 'Verde';
    //         await axios.post('/api/user-evaluation-result',this.resultEvaluate);
    //         //return verde
    //         this.$router.replace("/green-status");
    //       }
    //     }
    //   }
    // }
  }


}


}
</script>

<style scoped>
.bg-orange-300{
  background-color: #E64A19 !important;
  color: white !important;
}
.text-red{
  color: red !important;
}

.text-weekend {
  color: #6b0392 !important;
  font-weight: bold;
}

.btn-purple{
  background-color: #643FED;
  color: white;
  border-radius: 50px !important;
}
.circle {
  border-radius: 50%;
  width: 18px;
  height: 18px;
  padding: 6px;
  border: 2px solid #8B9298;
  color: #8B9298;
  text-align: center;
  font: 12px Arial, sans-serif;
  margin-right: 10px;
  margin-left: 10px;
}
.circle-muted {
  border-radius: 50%;
  width: 36px;
  height: 36px;
  padding: 6px;
  background: #fff;
  border: 2px solid #d1d1d1;
  color: #d1d1d1;
  text-align: center;
  font: 18px Arial, sans-serif;
  margin-left: 8px;
}

.arrow:after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 0;
  border-left: 17px solid white;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
}
.arrow {
  background-color: #006560;
  width: 100%;
  height: 26px;
  display: inline-block;
  position: relative;
}

.arrow p {
  color: white;
}

.arrow:before {
  color: #006560;
  border-left: 13px solid;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
  display: inline-block;
  content: '';
  position: absolute;
  right: -13px;
}

.arrow-gray {
  background-color: #DDDDDE;
  width: 100%;
  height: 26px;
  display: inline-block;
  position: relative;
  font-weight: bold;
}

.arrow-gray:before {
  color: #DDDDDE;
  border-left: 13px solid;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
  display: inline-block;
  content: '';
  position: absolute;
  right: -13px;
}

.arrow-gray:after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 0;
  border-left: 17px solid white;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
}

.arrow-green {
  height: 26px;
  position: relative;
  background: #8FC02B;
  font-weight: bold;
}

.arrow-green p{
  color: white;
}

.arrow-green:after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 0;
  border-left: 17px solid white;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
}

.arrow-green:before {
  color: #8FC02B;
  border-left: 13px solid;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
  display: inline-block;
  content: '';
  position: absolute;
  right: -13px;
}

.arrow-green-square {
  height: 26px;
  position: relative;
  background: #8FC02B;
  font-weight: bold;
}

.arrow-green-square p{
  color: white;
}

.arrow-green-square:after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 0;
  border-left: 0px solid white;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
}

.arrow-green-square:before {
  color: #8FC02B;
  border-left: 13px solid;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
  display: inline-block;
  content: '';
  position: absolute;
  right: -13px;
}


.arrow-square:after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 0;
  border-left: 0px solid white;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
}
.arrow-square {
  background-color: #006560;
  width: 100%;
  height: 26px;
  display: inline-block;
  position: relative;
}

.arrow-square p {
  color: white;
}

.arrow-square:before {
  color: #006560;
  border-left: 13px solid;
  border-top: 13px solid transparent;
  border-bottom: 13px solid transparent;
  display: inline-block;
  content: '';
  position: absolute;
  right: -13px;
}

.tableFixHead          { overflow: auto; height: 400px; }
.tableFixHead thead th { position: sticky; top: 0; z-index: 1; }

/* Just common table stuff. Really. */
table  { border-collapse: collapse; width: 100%; }
th, td { padding: 8px 16px; }
th     { background:#eee; }

.paragraphs-home{
  font-size: 0.6em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  text-align: left;
  font-weight: 600;
}

.container-home {
  background: #FFFFFF;
  width: 100%;
  padding-right: 4%;
  padding-left: 4%;
  padding-top: 2%;
  padding-bottom: 2%;
  margin-right: auto;
  margin-left: auto;
}

.title {
  text-align: left;
}

.font-size-title {
  /* padding-left: 1.5%; */
  font-size: 0.9em;
  font-weight: 500;
}

.font-button {
  font-size: 0.6em;
}

.btn-custom-green{
  border-color: #8FC02B !important;
  color: #8FC02B !important;
}

.btn-custom-green:hover{
  background-color: white;
}

.text-blue{
  color: #643FED !important; ;
}


.title_one {
  font-family: 'IBM Condensed Bold', sans-serif;
  color: #1b323a;
  font-size: 24px;
}

.title {
  font-family: 'IBM Semi Bold', sans-serif;
  color: #221A54;
  font-size: 36px;
  padding-top:20px;
}

.title_two {
  font-family: 'IBM Condensed Regular', sans-serif;
  color: #1b323a;
  font-size: 18px;
}

.bold-title {
  font-family: 'IBM Condensed Bold', sans-serif;
}
.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border-left-color: #09f;

  animation: spin 1s ease infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
.title_one {
  font-size: 18px;
}
.title_two {
  font-size: 14px;
}
/* .custom-radio .custom-control-label::before {
border-radius: 0;
background: #fff;
border: 1px solid;
top: 0;
}

.custom-radio .custom-control-input:checked~.custom-control-label::before {
background: #dee2e6;
border-radius: 1px solid;
} */


/* Hide the browser's default checkbox */
.container input {
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
  height: 25px;
  width: 25px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.container:hover input ~ .checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.container input:checked ~ .checkmark {
  background-color: #2196F3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.container input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container .checkmark:after {
  left: 9px;
  top: 5px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}
</style>
