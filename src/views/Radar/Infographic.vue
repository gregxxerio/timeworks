<template>
  <div>
  <div class="page-wrapper" style="margin-left: 0px" @click="clickBody()">
    <br />
    <br />
    <div class="container-fluid">
      <div class="row">
        <div class="col-lg-12 col-md-12">
          <div class="d-flex justify-content-center">
            <img
              src="assets/images/Infografía usuario-a.jpg"
              class="img-fluid"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>
<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import SideBar from '../../components/SideBar.vue';
@Component({
  components: {
    SideBar
  },
})
export default class Infographic extends Vue {

  }


</script>
