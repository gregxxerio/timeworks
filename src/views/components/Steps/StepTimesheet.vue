<script lang="ts">
import {Vue, Component} from 'vue-property-decorator';
import TimeSheetHome from "@/views/TimeSheetHome.vue";

@Component({
  components: {TimeSheetHome}
})
export default class StepTimesheet extends Vue {

}
</script>

<template>
  <div>
    <label class="step"></label>
    <div>
      <TimeSheetHome />
    </div>
  </div>
</template>

<style scoped>

</style>