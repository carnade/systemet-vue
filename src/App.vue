<template>
  <v-app>
    <v-content>
      <AppBar />
      <DataTable />
    </v-content>
  </v-app>
</template >

<script>
import AppBar from './components/AppBar';
import DataTable from './components/DataTable';

export default {
  name: 'App',

  components: {
    AppBar,
    DataTable
  },

  data: () => ({

    //
  }),
};
</script>
