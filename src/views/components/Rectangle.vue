<template>
  <div class="rectangle d-flex">
    <div class="flex-fill side left-side" :class="{ active: isActiveLocal }" @click="toggleActiveState(true)">
      Mensual
    </div>
    <div class="flex-fill side right-side" :class="{ active: !isActiveLocal }" @click="toggleActiveState(false)">
      Parcial
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator';

@Component
export default class Rectangle extends Vue {
  @Prop({ default: true }) private isActive!: boolean;
  @Prop({ default: '#91c12e' }) private activeColor!: string;
  @Prop({ default: '#ddddde' }) private inactiveColor!: string;

  private isActiveLocal: boolean = this.isActive;

  private toggleActiveState(status: boolean) {
    this.isActiveLocal = status;
    this.$emit('state-changed', this.isActiveLocal);
  }

}
</script>

<style scoped>
  div.rectangle {
    border: 1px solid silver;
  }

  .side{
    text-align: center;
    padding: 3px;
    cursor: pointer;
    background-color: #ddddde;
    color: #6c757d;
    font-weight: bold;
  }

  div.rectangle .active{
    color: white;
    background-color: #8fc02b;
  }

  @media screen and (max-width: 476px) {
    .rectangle.d-flex{
      flex-direction: column;
    }
  }

</style>
