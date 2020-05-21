<template>
  <span>
    {{item.price}}
    <span v-if="this.item.lastestPriceChange">
      (
      <span v-bind:class="classObject">{{this.item.lastestPriceChange}}</span>
      <v-icon
        small
        :color="getColor(this.item.lastestPriceChange)"
      >{{getArrow(this.item.lastestPriceChange)}}</v-icon>)
    </span>
  </span>
</template>

<script>
export default {
  methods: {
    getColor(priceChange) {
      return priceChange > 0 ? "green" : "red";
    },
    getArrow(priceChange) {
      return priceChange > 0 ? "mdi-arrow-up-thick" : "mdi-arrow-down-thick";
    }
  },
  computed: {
    classObject: function() {
      return {
        "green--text": this.item.lastestPriceChange > 0,
        "red--text": this.item.lastestPriceChange < 0
      };
    }
  },
  props: ["item"]
};
</script>

<style>
</style>