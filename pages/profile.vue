<template>
  <v-container>
    <v-row justify="center" align="center">
      <v-col cols="12">
        <v-parallax
          dark
          height="200"
          :src="
            this.orderData && orderData.cover_picture
              ? orderData.cover_picture.url
              : 'https://cdn.vuetifyjs.com/images/parallax/material2.jpg'
          "
        >
          <v-row
            :class="{
              invisible:
                this.orderData &&
                this.orderData.cover_picture &&
                this.orderData.cover_picture.url,
            }"
            align="center"
            justify="center"
          >
            <v-col class="text-center" cols="12">
              <h1 class="text-h4 font-weight-thin mb-4">{{this.userName ? this.userName : 'Anonymous'}}</h1>
              <v-btn
                @click="logoutDialog = true"
                class="mt-2 mb-2"
                dark
                small
                color="secondary"
              >
                Log Out
              </v-btn>
            </v-col>
          </v-row>


          <v-dialog v-model="logoutDialog" width="500">
            <v-card>
              <v-card-title class="text-h5">
                Log Out Confirmation
              </v-card-title>

              <v-card-text> Are you sure you want to log out? </v-card-text>

              <v-divider></v-divider>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="primary" text @click="logoutNow()">
                  Logout
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>


          <v-dialog v-model="orderDialog" width="500">
            <v-card>
              <v-card-title class="text-h5"> Add Order </v-card-title>

              <v-card-text>
                <template>
                  <v-text-field
                    v-model="newOrder.consigneeName"
                    label="Consignee Name">
                  </v-text-field>
                  <v-text-field
                    v-model="newOrder.consigneeCity"
                    label="Consignee City">
                  </v-text-field>
                  <v-text-field
                    v-model="newOrder.consigneeNumber"
                    label="Consignee Number">
                  </v-text-field>
                  <v-text-field
                    v-model="newOrder.height"
                    label="Height"
                    single-line
                    type="number">
                  </v-text-field>
                  <v-text-field
                    v-model="newOrder.weight"
                    label="Weight"
                    single-line
                    type="number">
                  </v-text-field>
                  <template>
                    <v-radio-group v-model="newOrder.paymentType" row>
                      <v-radio label="Cod" :value="'cod'"></v-radio>
                    </v-radio-group>
                  </template>
                </template>

              </v-card-text>

              <v-divider></v-divider>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="primary" text @click="changeEducationData()">
                  Save Changes
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>


          <v-snackbar v-model="snackbar">
            {{ errorMessage }}

            <template v-slot:action="{ attrs }">
              <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
                Close
              </v-btn>
            </template>
          </v-snackbar>
        </v-parallax>
      </v-col>
    </v-row>

    <v-row>
      <client-only>
        <v-col cols="12" xs="12">
          <v-row>
            <v-col cols="12" xs="12">
              <v-skeleton-loader
                v-if="dataLoading"
                class="mx-auto"
                type="card"
              ></v-skeleton-loader>
              <v-card elevation="3" v-else>
                <v-card-text class="text-center font-weight-bold">
                  <v-icon>mdi-menu</v-icon>
                  Order
                </v-card-text>
                <div class="container p-5">
                  <div class="row align-items-start">
                  <table class="table" id="orders">
                    <thead>
                      <tr>
                        <th>Tracking Number</th>
                        <th>Consignee Name</th>
                        <th>Consignee City</th>
                        <th>Consignee Number</th>
                        <th>Height</th>
                        <th>Weight</th>
                        <th>Payment Type</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="order in orderData" :key="order.TrackingNumber">
                        <td>{{order.TrackingNumber}}</td>
                        <td>{{order.ConsigneeName}}</td>
                        <td>{{order.ConsigneeCity}}</td>
                        <td>{{order.ConsigneeNumber}}</td>
                        <td>{{order.Height}}</td>
                        <td>{{order.Weight}}</td>
                        <td>{{order.PaymentType}}</td>
                      </tr>
                      
                    </tbody>
                  </table>
                  </div>
                </div>
                <!-- <template>
                  <v-data-table
                    v-model:items-per-page="itemsPerPage"
                    :headers="headers"
                    :items="orderData"
                    class="elevation-1"
                  ></v-data-table>
                </template> -->
                <v-btn
                  @click="orderDialog = true"
                  class="mx-2 mb-2 floating-btn"
                  style="right: 0"
                  fab
                  dark
                  small
                  color="primary"
                >
                  <v-icon dark> mdi-pencil </v-icon>
                </v-btn>
              </v-card>
            </v-col>
          </v-row>
        </v-col>
      </client-only>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

export default {
  name: 'ProfilePage',
  data() {
    return {
      orderData: [],
      userName: '',
      dataLoading: false,
      orderDialog: false,
      logoutDialog: false,
      itemsPerPage: 5,
      headers: [
          {
            title: 'Dessert (100g serving)',
            align: 'start',
            sortable: false,
            key: 'name',
          },
          { title: 'Calories', align: 'end', key: 'calories' },
          { title: 'Fat (g)', align: 'end', key: 'fat' },
          { title: 'Carbs (g)', align: 'end', key: 'carbs' },
      ],
      newOrder: {
        consigneeName: '',
        consigneeAddress: '',
        consigneeCity: '',
        consigneeCountry: '',
        consigneePostalCode: '',
        consigneeProvince: '',
        consigneeNumber: '',
        height: undefined,
        weight: undefined,
        length: undefined,
        width: undefined,
        paymentType: ''
      },
      moment: moment,

      //error message
      errorMessage: '',
      snackbar: false,

      graduationDateInput: false,

      //messages
      messages: [],
    }
  },
  created() {
    if (process.client) {
      this.getOrderData()
      if (localStorage.getItem('session') === null) {
        this.$router.push({
          name: 'signin',
        })
      }
    }
  },
  computed: {
  },
  methods: {

    async logoutNow() { 
      localStorage.removeItem('session')
      this.logoutDialog = false
      this.$router.push({
        name: 'signin',
      })
      
    },

    async getOrderData() {
      let token = localStorage.getItem('session')
      const profile = localStorage.getItem('username')
      this.userName = profile

      const config = {
        headers: { Authorization: token, Accept: 'application/json' },
      }

      this.dataLoading = true

      try {
        let res = await axios.get('/api/orders', config)
        console.log('isi apa? ', res.data.data)
        this.orderData = res.data.data
        this.dataLoading = false
      } catch (error) {
        this.errorMessage = error.response.data.error.errors[0]
        this.snackbar = true
        this.localStorage.removeItem('access_token')
        this.$router.push({
          name: 'signin',
        })
      } finally {
      }
    },

    async changeEducationData() {
      let token = localStorage.getItem('session')
      const config = {
        headers: { Authorization: token, Accept: 'application/json' },
      }

      let data = {
        "consigneeName": this.newOrder.consigneeName,
        "consigneeAddress": this.newOrder.consigneeAddress ? this.newOrder.consigneeAddress : "Something something street 22",
        "consigneeCity": this.newOrder.consigneeAddress ? this.newOrder.consigneeAddress : "Singapore",
        "consigneeCountry": this.newOrder.consigneeCountry ? this.newOrder.consigneeCountry : "SG",
        "consigneePostalCode": this.newOrder.consigneePostalCode ? this.newOrder.consigneePostalCode : "12345",
        "consigneeProvince": this.newOrder.consigneeProvince ? this.newOrder.consigneeProvince : "Singapore",
        "consigneeNumber": this.newOrder.consigneeNumber ? this.newOrder.consigneeNumber : "12345678",
        "height": this.newOrder.height ? parseInt(this.newOrder.height) : 1.2,
        "weight": this.newOrder.height ? parseInt(this.newOrder.height) : 2.2,
        "length": this.newOrder.length ? parseInt(this.newOrder.length) : 4.5,
        "width": this.newOrder.width ? parseInt(this.newOrder.width) : 1,
        "paymentType": this.newOrder.paymentType
      }

      try {
        let res = await axios.post('/api/orders', data, config)
      } catch (error) {
        this.errorMessage = error.response.data.error.errors[0]
        this.snackbar = true
      } finally {
        this.getOrderData()
        this.orderDialog = false
      }
    },

  },
}
</script>

<style lang="scss">
#no-background-hover {
  background-color: transparent !important;
}

.invisible {
  visibility: hidden !important;
}

.floating-btn {
  position: absolute;
  bottom: 0;
}

.remove-img-btn {
  cursor: pointer;
  position: absolute;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.5);
  color: #fff;
}

#orders {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

#orders td, #orders th {
  border: 1px solid #ddd;
  padding: 8px;
}

#orders tr:nth-child(even){background-color: #f2f2f2; color: black;}

#orders tr:hover {background-color: #ddd;}

#orders th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #04AA6D;
  color: white;
}
</style>
