<template >
  <div >
    <button class="btn btn-light" @click="printThis">
      <i class="fas fa-image"></i>
    </button>
    <div class="diviser" style="overflow: auto;" ref="printcontent">
      <div class="graf">
        <template v-for="(option, index) in charts" >

          <div class="bar" :key="index" :style="'left:'+((index*12)*2.55)+'%;height:'+option.porcentaje+'%;'">
            <template v-for="(t, indexd) in option.series[0].data"  >

              <div class="div-hover" :key="indexd" :style="'background:'+option.colors[indexd]+';width:100%;height:'+t.y+'%;'">
                    <p v-if="((option.porcentaje * t.y) / 100) > 3.4" class="text-hover" :style="(option.myOwnTitle == 'IDEA') ? 'color:#fff' : 'color:#000000'" style="font-size: 12px;">{{t.name}}: {{ fixValue(t) }}%</p>
                    <div v-if="((option.porcentaje * t.y) / 100) < 3.4" class="line" :style="'border: 1px solid'+option.colors[indexd]+';'" style="top: -40px;left: 140px;width: 15px;transform: rotate(-5deg);position: absolute;z-index: -1;"></div>
              </div>

            </template>
            <p style="margin-bottom: 0em;font-size: 12px;"><b>{{option.myOwnTitle}}</b></p>
            <p style="margin-bottom: 0em;font-size: 12px;">Total de horas: <b>{{option.totalHoras}} hrs.</b></p>
            <p style="margin-bottom: 0em;font-size: 12px;">Porcentaje: <b>{{option.porcentaje}}%</b></p>

          </div>
          <div class="bar-hide" :key="index" :style="'left:'+(((index*12)*2.5) + 18.5)+'%;height:'+(option.porcentaje)+'%;'">
            <template v-for="(t, indexd) in option.series[0].data"  >

              <div :key="indexd" :style="'background:transparent;width:100%;margin-top:-9%;height:'+( (((option.porcentaje * t.y) / 100) < 3.4) ? '-1px;' : t.y + '%;')">
                <template  v-if="((option.porcentaje * t.y) / 100) < 3.4">
                    <p  class="text-hover" :style="'color:'+option.colors[indexd]+';'" style="background:#fcfcfc;font-size: 12px;color:#000;">{{t}}: {{ fixValue(t) }}%</p>
                </template>
              </div>
            </template>


          </div>
        </template>
        <ul style="list-style: none;" class="v-meter">
          <li class="top"><div>100</div></li>
          <li class="normal"><div>80</div></li>
          <li class="normal"><div>60</div></li>
          <li class="normal"><div>40</div></li>
          <li class="normal"><div>20</div></li>
          <li class="bottom"><div>0</div></li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop ,Vue } from 'vue-property-decorator';
import html2canvas from 'html2canvas';

@Component

export default class Welcome extends Vue {
  @Prop({ default: {} })
  private charts!: object;

  fixValue(t){
    // (t.p).toFixed(1)
    const p = Number(t.p);
    if (!isNaN(p)) {
      return p.toFixed(1);
    }
    return null;
  }

  async printThis() {
    console.log("printing..");
    const el: HTMLElement = this.$refs.printcontent as HTMLElement;

    // const options = {
    //   type: "dataURL"
    // };
    const printCanvas = await html2canvas(el);

    const link = document.createElement("a");
    link.setAttribute("download", "download.png");
    link.setAttribute(
      "href",
      printCanvas
      .toDataURL("image/png")
      .replace("image/png", "image/octet-stream")
    );
    link.click();

    console.log("done");
  }
}
</script>
<style>
.diviser {
  padding-top: 40px;
  padding-left: 40px;
  padding-right: 40px;
  padding-bottom: 100px;
}

.graf {
  position: relative;
  height: 400px;
  width: 900px;
  background-color: #fcfcfc;
  border: none;
  margin: 0;
  z-index: 0;
}

.v-meter {
  position: relative;
  top: 0;
  left: 0px;
  width: 850px;
  height: 400px;
  z-index: 100;
}

.v-meter .normal {
  position: relative;
  left: 32px;
  height: 79.5px;
  border-bottom: 1px solid #979797;
  opacity: 0.7;
}

.v-meter .top {
  position: relative;
  left: 32px;
  height: 0px;
  top: -3px;
  border-bottom: 1px solid #979797;
  opacity: 0.7;

}

.v-meter .bottom {
  position: relative;
  left: 32px;
  height: 79px;
  border-bottom: 1px solid #979797;
  opacity: 0.7;

}


.v-meter .normal::before {
  content: '';
  position: absolute;
  top: 1px;
  left: -5px;
  height: 100%;
  border-bottom: 1px solid #979797;
  /* transform: skewY(45deg); */
  width: 30px;
}




.v-meter .normal div {
  position: absolute;
  width: 80px;
  font-size: 16px;
  left: -88px;
  padding-top: 60px;
  color: #979797;
  font-weight: bold;
  text-align: right;
}

.v-meter .top::before {
  content: '';
  position: absolute;
  top: 1px;
  left: -5px;
  height: 100%;
  border-bottom: 1px solid #979797;
  /* transform: skewY(45deg); */
  width: 30px;
}

.v-meter .top div {
  position: absolute;
  width: 80px;
  font-size: 16px;
  left: -85px;
  padding-top: 0px;
  margin-top: -6px;
  color: #979797;
  font-weight: bold;
  text-align: right;
}



.v-meter .bottom::before {
  content: '';
  position: absolute;
  top: 1px;
  left: -5px;
  height: 100%;
  border-bottom: 1px solid #979797;
  /* transform: skewY(45deg); */
  width: 30px;
}

.v-meter .bottom div {
  position: absolute;
  width: 80px;
  font-size: 16px;
  left: -88px;
  padding-top: 62px;
  color: #979797;
  font-weight: bold;
  text-align: right;
}

.bar {
  width: 146px;
  /* height: 32%; */
  background-color: #328036;
  position: absolute;
  left: 10%;
  bottom: 0px;
  z-index: 1000;
  margin: 0 40px;
  display: flex;
  flex-flow: wrap;
}

.bar-hide {
  width: 140px;
  /* height: 32%; */
  background-color: transparent;
  position: absolute;
  left: 10%;
  bottom: 0px;
  z-index: 1000;
  margin: 0 40px;
  display: flex;
  flex-flow: wrap;
}

.text-hover:hover {
  font-weight: bold;
}

.div-hover {
  position: relative;
}

/* .line {
} */



</style>
