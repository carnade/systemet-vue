<template>
  <v-card>
    <v-card-title>
      Vinlista
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="wines"
      :search="search"
      :footer-props="{
        'items-per-page-options': [10, 20, 30, 40, 50]
      }"
      :items-per-page="20"
      class="elevation-3"
      pagination.sync="pagination"
      item-key="id"
      loading="true"
      loading-text="Hämtar viner..."
      multi-sort
      sort-by="lastestPriceChangeDate"
      sort-desc
    >
      <!--       hide-default-footer -->
      <template #item.name="{item}">{{item.name}} {{item.nameExtra}}</template>
      <template #item.vintage="{value}">{{value != 0 ? value: "-"}}</template>
      <template #item.price="{item}">
        <PriceSlot :item="item" />
      </template>
      <template #item.lastestPriceChangePercent="{value}">{{value && (value*100).toFixed(1) + '%'}}</template>
      <template #item.lastestPriceChangeDate="{value}">{{value && value.split('T')[0]}}</template>
      <template #item.sellStart="{value}">{{value && value.split('T')[0]}}</template>
      <!--      <template #item.grapes="{value}">{{getGrapes(value)}}</template>-->
    </v-data-table>
  </v-card>
</template>
<!-- 
    search="search"
-->
<script>
import PriceSlot from "./slots/PriceSlot";
export default {
  name: "DataTable",
  components: {
    PriceSlot
  },

  data: () => ({
    search: "",
    listSize: [10, 25, 50, 100],
    headers: [
      {
        text: "Namn",
        value: "name",
        align: "left",
        sortable: false
      },
      { text: "Typ", value: "category" },
      { text: "Land", value: "country" },
      { text: "Producent", value: "producer" },
      { text: "Årgång", value: "vintage" },
      { text: "Pris", value: "price" },
      { text: "Ändring %", value: "lastestPriceChangePercent" },
      { text: "Ändring datum", value: "lastestPriceChangeDate" },
      { text: "Volym", value: "volume" },
      //      { text: "Alkohol", value: "alcoholPercentage" },
      { text: "Sortiment", value: "assortmentText" },
      { text: "Säljstart", value: "sellStart" },
      { text: "Vivino", value: "scoreVivino" },
      { text: "", value: "_id" },
      { text: "druvor", value: "grapeList" }
    ],
    wines: []
  }),
  computed: {
    computedHeaders() {
      return this.headers.filter(header => header.text !== "id");
    }
  },
  created() {
    const baseURI = "http://localhost:3000/item/wines";
    //const baseURI = 'https://jsonplaceholder.typicode.com/todos/1';
    this.$http
      .get(baseURI)
      .then(result => {
        this.wines = result.data;
        this.wines.forEach(wine => {
          wine.grapeList = wine.grapes.map(grape => grape.name).join(", ");
        });
        console.log(this.wines);
      })
      .catch(error => {
        console.log(error);
      });
  },
  methods: {
    getGrapes: grapes => {
      return grapes.map(grape => grape.name).join(", ");
    }
  }
};
</script>

<style></style>
