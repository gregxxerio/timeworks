<script lang="ts">
import {Vue, Component} from 'vue-property-decorator';
import TimeSheetHomeNew from "@/views/TimeSheetHomeNew.vue";

@Component({
  components: {TimeSheetHomeNew}
})
export default class StepTimesheet extends Vue {

}
</script>

<template>
  <div>
    <label class="step"></label>
    <div>
      <TimeSheetHomeNew />
    </div>
  </div>
</template>

<style scoped>

</style>
