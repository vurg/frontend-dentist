<template>
  <div class="container-fluid">
    <div class="row flex-nowrap">

      <div class="col-auto col-md-3 col-xl-2 px-sm-2 px-0 backgroud">
        <div class="d-flex flex-column align-items-center align-items-sm-start px-3 pt-2 text-white min-vh-100">
          <div class="d-flex align-items-center pb-3 mb-md-0 me-md-auto text-white text-decoration-none">
            <span class="fs-4 d-none d-sm-inline">
              <h4>Dentist Dashboard </h4>
            </span>
          </div>


          <ul class="nav nav-pills flex-column mb-sm-auto mb-0 align-items-center align-items-sm-start" id="menu">
            <!--
              <li class="nav-item">
                <a href="/" class="nav-link align-middle px-0">
                  <i class="fs-4 bi-house"></i> <span class="ms-1 d-none d-sm-inline">Home</span>
                </a>
              </li>
            -->

            <li>
              <a @click="showContent('booking')" href="#" class="nav-link px-0 align-middle">
                <i class="fs-4 bi-chat-dots"></i> <span class="ms-1 d-none d-sm-inline">Booking</span>
              </a>
            </li>
            <!--
              <li>
                <a  @click="showContent('profile')" href="#" class="nav-link px-0 align-middle">
                  <i class="fs-4 bi-person-circle"></i> <span class="ms-1 d-none d-sm-inline">Profile</span>
                </a>
              </li>
              -->
            <li>
              <a @click="logout" href="#" class="nav-link px-0 align-middle">
                <i class="fs-4 bi-box-arrow-right"></i> <span class="ms-1 d-none d-sm-inline">Sign out</span>
              </a>
            </li>


          </ul>
        </div>

      </div>
      <div class="col py-3">

        <div v-if="currentContent === 'booking'">
          <Booking />
        </div>

        <!--
        <div v-if="currentContent === 'profile'">
          <Profile />
        </div>
      -->

      </div>

    </div>

  </div>
</template>
  
<script>
import { ref } from 'vue';
import Booking from './Booking.vue';
import { getToken, logout } from '@/utils/auth';




export default {
  components: {
    Booking,
  },
  setup() {
    const currentContent = ref('booking');

    const showContent = (contentName) => {
      currentContent.value = contentName;
    };

    const token = getToken();
    const isUserLoggedIn = !!token;  // check if the user is logged with token

    return {
      currentContent,
      showContent,
      logout,
      isUserLoggedIn
    };
  },
  methods: {

    logout() {
      logout();
    }

  }
}


</script>
  
<style>
.backgroud {
  background-color: #122430;
}

.backgroud a {
  color: white;
}
</style>
  