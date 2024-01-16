<template>
  <div class="container-home new-style-ch">
    <h3 style="color: rgb(108, 117, 125);font-weight: 500;">Personal supervisado</h3>
    <div class="card">
      <div class="card-body">
        <ul class="nav nav-tabs user-super" id="myTab" role="tablist">
          <li class="nav-item">
            <a class="nav-link nav-link-new-style active" id="home-tab" data-toggle="tab" href="#home" role="tab" aria-controls="home" aria-selected="true">
              <span class="text-nav-one">Mi equipo</span>
              <br>
              <span class="text-nav-two">{{countPesonal}} personas</span>
            </a>
          </li>
          <li class="nav-item">
            <a class="nav-link nav-link-new-style" id="profile-tab" data-toggle="tab" href="#profile" role="tab" aria-controls="profile" aria-selected="false">
              <span class="text-nav-one">Solicitudes recibidas</span>
              <br>
              <span class="text-nav-two">{{countRequests}} solicitudes</span>
            </a>
          </li>
        </ul>
        <div class="tab-content" id="myTabContent">
          <div class="tab-pane fade show active" id="home" role="tabpanel" aria-labelledby="home-tab">
            <vacation-list-users v-if="user" :userId="user.id" :years="year" @count-data="countData" />
          </div>
          <div class="tab-pane fade" id="profile" role="tabpanel" aria-labelledby="profile-tab">
            <vacation-request v-if="user" :userId="user.id" @count-request="countRequest"/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import VacationList from "@/views/VacationList.vue";
import VacationListUsers from "@/views/VacationListUsers.vue";
import VacationRequest from "@/views/VacationRequest.vue";
import moment from "moment";
@Component({
  components: { VacationList, VacationListUsers, VacationRequest }
})
export default class UserSupervisor extends Vue {
  user: any = null;
  year = moment().year();
  countPesonal = 0;
  countRequests = 0;

  async mounted() {
    this.user = this.$store.state.user;
  }

  countData(mensaje: any) {
    this.countPesonal = mensaje;
  }

  countRequest(mensaje: any){
    this.countRequests = mensaje;
  }
}
</script>
<style>

.new-style-ch {
  background: transparent;
}

.nav-link-new-style {
  padding: 0.4rem 2rem;
}

.nav-tabs .user-super {
  border-bottom: 1px solid;
}

.nav-tabs .user-super .nav-item.show .nav-link, .nav-tabs .nav-link.active {
  border-color: #000 #000 #fff;
}

.user-super.nav-item.nav-link.active span {
  color: rgb(98, 54, 254);
  font-weight: 600;
}

.user-super.nav-item.nav-link span {
  color: #000;
  font-weight: 600;
}

.text-nav-one {
  font-size: 1em !important;
  font-weight: 400 !important;
}

.text-nav-two {
  font-size: 1.2em !important;
  font-weight: bold !important;
}

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
