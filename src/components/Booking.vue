<template>
    <div class="m-3">
        <div class=" mb-4">
            <div class="card">
                <div class="card-body text-white mailbox-widget pb-0" style="background-color: #567890;">
                    <h2 class="text-white pb-3">Bookings</h2>

                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="mb-2">
                    <input type="text" v-model="search" placeholder="Search by patient name" style="margin-right: 5px;">
                    <button class="btn btn-primary" @click="openPostModal">Create Booking</button>
                </div>
                <div class="hidden-on-medium-small overflow-x:auto">
                    <table class="table table-striped">
                        <thead>
                            <tr>
                                <th>Patient</th>
                                <th>Dentist</th>
                                <th>Date</th>
                                <th>Time</th>
                                <th>Message</th>
                                <th>Status</th>
                                <th>Manage</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="booking in filteredBookings" :key="booking._id">
                                <td>{{ booking.patientName }}</td>
                                <td>{{ booking.dentistName }}</td>
                                <td>{{ booking.date }}</td>
                                <td>{{ booking.time }}</td>
                                <td>{{ booking.message }}</td>
                                <td>{{ booking.status }}</td>
                                <td> <button class="btn btn-primary"
                                        @click="cancelAndReOpenBooking(booking)">Re-Open</button>
                                </td>
                                <td> <button class="btn btn-danger" @click="cancelBooking(booking)">Cancel</button> </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
                <div class="hidden-on-large">
                    <div class="booking-cards" v-for="booking in filteredBookings" :key="booking._id">
                        <div class="booking-card">
                            <p><strong>Patient:</strong> {{ booking.patientName }}</p>
                            <p><strong>Dentist:</strong> {{ booking.dentistName }}</p>
                            <p><strong>Date:</strong> {{ booking.date }}</p>
                            <p><strong>Time:</strong> {{ booking.time }}</p>
                            <p><strong>Message:</strong> {{ booking.message }}</p>
                            <p><strong>Status:</strong> {{ booking.status }}</p>
                            <button class="btn btn-primary" @click="cancelAndReOpenBooking(booking)">Re-Open</button>
                            <button class="btn btn-danger" @click="cancelBooking(booking)">Cancel</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div v-if="showPostModal" class="modal" style="display:block;">
        <div class="modal-content">
            <span class="close" @click="closePostModal">&times;</span>
            <h1>Post Booking Time Slot</h1>
            <form @submit.prevent="submitPost">
                <div>
                    <label>Date:</label>
                    <input v-model="newBooking.date" type="date" />
                </div>
                <div>
                    <label>Time:</label>
                    <input v-model="newBooking.time" type="time" />
                </div>
                <button class="btn btn-primary" type="submit">Post</button>
            </form>
        </div>
    </div>
    <div v-if="showSuccessModal" class="modal" style="display:block;">
        <div class="modal-content">
            <span class="close" @click="closeSuccessModal">&times;</span>
            <h1>Successfully posted a booking</h1>
        </div>
    </div>
</template>
  
<script>
import axios from "axios";
const API_GATEWAY = import.meta.env.VITE_API_GATEWAY;

export default {
    name: 'booking',
    mounted() {
        this.getAllBookings();
    },
    data() {
        return {
            bookings: [],
            search: '',
            showPostModal: false,
            showSuccessModal: false,
            newBooking: {
                date: '',
                time: '',
                status: 'AVAILABLE'
            }
        }
    },
    computed: {
        filteredBookings() {
            return this.bookings.filter(booking => booking.patientName.toLowerCase().includes(this.search.toLowerCase()));
        }
    },
    methods: {
        openPostModal() {
            this.showPostModal = true;
        },
        closePostModal() {
            this.showPostModal = false;
        },
        openSuccessModal() {
            this.showSuccessModal = true;
        },
        closeSuccessModal() {
            this.showSuccessModal = false;
        },
        submitPost() {
            const dentistID = localStorage.getItem('dentistID');
            const dentistName = localStorage.getItem('dentistName');
            this.newBooking.dentistID = dentistID;
            this.newBooking.dentistName = dentistName;
            axios.post(`http://${API_GATEWAY}:80/api/v1/bookings/`, {
                patientName: '',
                dentistName: this.newBooking.dentistName,
                dentistID: this.newBooking.dentistID,
                date: this.newBooking.date,
                time: this.newBooking.time,
                status: 'AVAILABLE'
            }).then(() => {
                this.showPostModal = false;
                this.openSuccessModal();
                this.getAllBookings();
            })
                .catch((error) => {
                    console.error(error);
                });
        },
        getAllBookings() {
            let dentistID = localStorage.getItem('dentistID');
            axios.get(`http://${API_GATEWAY}:80/api/v1/bookings/dentist/${dentistID}`)
                .then((response) => {
                    this.bookings = response.data;
                })
                .catch((error) => {
                    console.error(error);
                });
        },
        cancelAndReOpenBooking(booking) {
            axios.patch(`http://${API_GATEWAY}:80/api/v1/bookings/${booking._id}`, {
                status: 'CANCELED'

            })
                .then(() => {
                    axios.post(`http://${API_GATEWAY}:80/api/v1/bookings/`, {
                        patientName: '',
                        dentistName: booking.dentistName,
                        dentistID: booking.dentistID,
                        date: booking.date,
                        time: booking.time,
                        status: 'AVAILABLE'
                    })
                        .then(() => {
                            this.getAllBookings();
                        })


                })
                .catch((error) => {
                    console.error(error);
                });
        },
        cancelBooking(booking) {
            axios.patch(`http://${API_GATEWAY}:80/api/v1/bookings/${booking._id}`, {
                status: 'CANCELED'
            })
                .then(() => {
                    this.getAllBookings();
                })
                .catch((error) => {
                    console.error(error);
                });

        }
    },

}

</script>
<style scoped>
.button-space {
    margin-right: 15px;
}

/* Define card styles */
.booking-cards {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
}

.booking-card {
    width: calc(50% - 20px);
    /* Adjust the width as needed for medium screens */
    background-color: #fff;
    border: 1px solid #ccc;
    padding: 20px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

/* Media query for small screens */
@media (max-width: 767px) {
    .booking-card {
        width: 100%;
    }
}

/* Define the media query to hide the div on medium and small screens */
@media (max-width: 991px) {
    .hidden-on-medium-small {
        display: none;
    }
}

/* Define the media query to hide the div on medium and small screens */
@media (min-width: 991px) {
    .hidden-on-large {
        display: none;
    }
}
</style>
