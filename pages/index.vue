<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline">
          Welcome to Farjistik!
        </v-card-title>
        <v-card-text>
          <p>
            Farjistik is a software solution designed for efficient order administration management. It provides a centralized platform for order placement, fulfillment, and tracking, with real-time visibility and user-friendly features. With Farjistik, businesses can streamline and automate their order management processes for improved efficiency and accuracy.
          </p>
          <hr class="my-3" />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" nuxt to="/signin"> Sign In </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'IndexPage',
}
</script>
