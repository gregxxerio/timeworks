<template>
  <svg
    fill="#000000"
    width="18px"
    height="18px"
    viewBox="0 0 24.00 24.00"
    xmlns="http://www.w3.org/2000/svg"
    style="cursor: pointer"
    @click="onClick"
  >
    <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
    <g
      id="SVGRepo_tracerCarrier"
      stroke-linecap="round"
      stroke-linejoin="round"
      stroke="#CCCCCC"
      stroke-width="0.288"
    ></g>
    <g id="SVGRepo_iconCarrier">
      <path
        d="M3 19h18a1.002 1.002 0 0 0 .823-1.569l-9-13c-.373-.539-1.271-.539-1.645 0l-9 13A.999.999 0 0 0 3 19z"
      ></path>
    </g>
  </svg>
</template>

<script lang="ts">
import { Component, Vue, Emit } from "vue-property-decorator";
@Component({
  components: {},
})
export default class ArrowUpIcon extends Vue {
  @Emit("on-click")
  onClick() {
    return true;
  }
}
</script>
