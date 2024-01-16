<template>
<div>
  <div class="" style="display:flex; justify-content: space-between;gap: 2%;margin: -4% 0 0 0;">
    <a class="btn btn-outline-btn-vacation" @click="step = 1" :class="step == 1 ? 'active' : ''"><i></i> Informe Mensual <i></i></a>
    <a class="btn btn-outline-btn-vacation" @click="step = 2" :class="step == 2 ? 'active' : ''"><i></i> Informe por día <i></i></a>
  </div>

  <template v-if="step == 1">
    <GeneralReport/>
  </template>
  <template v-if="step == 2">
    <br>
    <ReportByDay/>
  </template>
</div>


</template>
<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import SideBar from '../../components/SideBar.vue';
import GeneralReport from "@/views/Radar/GeneralReport.vue";
import ReportByDay from "@/views/Radar/ReportByDay.vue";

@Component({
  components: {
    SideBar, GeneralReport,
    ReportByDay
  },
})
export default class Radar extends Vue {
step: any = 0;


}


</script>
