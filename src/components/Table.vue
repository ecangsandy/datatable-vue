<template>
  <div class="">
    <h1 style="text-align: center">Datatable with 3rd Party API</h1>
    <v-row align="center" class="list px-3 mx-auto">
      <v-col cols="12" sm="8">
        <v-text-field
          v-model="searchTitle"
          label="Search by Title"
        ></v-text-field>
      </v-col>
      <v-col cols="12" sm="4">
        <v-btn
          small
          @click="
            page = 1;
            readDataFromAPI();
          "
        >
          Search
        </v-btn>
      </v-col>
      <v-col cols="12" sm="12">
        <v-data-table
          :headers="headers"
          disable-pagination
          :hide-default-footer="true"
          :items="passengers"
          class="elevation-1"
        >
        </v-data-table>
      </v-col>
      <v-row class="justify-end">
        <v-col cols="5">
          <v-container class="max-width">
            <v-pagination
              v-model="page"
              :length="totalPages"
              total-visible="5"
              next-icon="mdi-menu-right"
              prev-icon="mdi-menu-left"
              @input="handlePageChange"
              class="my-4"
            ></v-pagination>
          </v-container>
        </v-col>
      </v-row>
    </v-row>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "DatatableComponent",
  data() {
    return {
      searchTitle: "",
      page: 1,
      totalPages: 0,
      totalPassengers: 0,
      numberOfPages: 0,
      passengers: [],
      loading: true,
      options: {},
      headers: [
        { text: "Kode Kecamatan", value: "kode_kecamatan" },
        { text: "Nama Kecamatan", value: "nama_kecamatan" },
        { text: "Nama Desa", value: "nama_desa" },
      ],
    };
  },
  //this one will populate new data set when user changes current page.
  watch: {
    options: {
      handler() {
        this.readDataFromAPI();
      },
    },
    deep: true,
  },
  methods: {
    //Reading data from API method.
    getRequestParams(searchTitle, page, pageSize) {
      let params = {};
      if (searchTitle) {
        params["title"] = searchTitle;
      }
      // params["title"] = 'pager';
      if (page) {
        params["page"] = page - 1;
      }
      // params["page"] = 10;
      // if (pageSize) {
      //   params["size"] = pageSize;
      // }
      return params;
    },
    readDataFromAPI() {
      const params = this.getRequestParams(this.searchTitle, this.page);
      this.loading = true;
      const { page, itemsPerPage } = this.options;
      let pageNumber = page - 1;
      axios
        .get("http://127.0.0.1:8000/api/data", { params })
        .then((response) => {
          console.log(response);
          //Then injecting the result to datatable parameters.
          this.loading = false;
          this.totalPages = response.data.last_page;
          this.passengers = response.data.data;
          this.totalPassengers = response.data.total;
          this.numberOfPages = response.data.last_page;
        });
    },
    handlePageChange(value) {
      console.log(value);
      this.page = value;
      this.readDataFromAPI();
    },
  },
  //this will trigger in the onReady State
  mounted() {
    this.readDataFromAPI();
  },
};
</script>
