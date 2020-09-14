<template>
  <v-card>
    <v-card-title>
      Vinlista
      <v-spacer></v-spacer>
      <v-text-field
        v-model="searchText"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="wines"
      :search="searchTable"
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
      <template slot="expand" slot-scope="props">
        <v-card flat>
          <v-card-text>
            <td>{{props.name}}</td>
          </v-card-text>
        </v-card>
      </template>
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
    searchTable: "",
    searchText: "",
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
      { text: "Importör", value: "producer" },
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
        this.wines = this.wines.filter(w => w.lastestPriceChangePercent < 0);
        this.wines = this.wines.filter(w => {
          let d1 = new Date(w.lastestPriceChangeDate);
          let d2 = new Date("2020-08-30");
          return d1 > d2;
        });
        this.wines = this.wines.filter(w => !w.outOfStock);

        this.wines.forEach(wine => {
          wine.grapeList = wine.grapes.map(grape => grape.name).join(", ");
        });
      })
      .catch(error => {
        console.log(error);
      });
  },
  watch: {
    searchText(val) {
      if (!val) {
        return;
      }
      this.searchDebounced(val);
    }
  },
  methods: {
    searchDebounced(val) {
      // cancel pending call
      clearTimeout(this._timerId);

      // delay new call 500ms
      this._timerId = setTimeout(() => {
        this.searchTable = val;
      }, 500);
    }
  }
};
</script>

<style></style>
