<template>
  <div
    id="app"
    class="
      container
      d-flex
      flex-column
      justify-content-center
      align-items-center
      mt-5
    "
  >
  <h1>Unsere Employees!</h1>
    <div class="alert alert-danger" v-if="updateAlert">
      Update vorhanden, bitte neustarten!
    </div>

    <img src="../public/employees.jpg" class="w-50" alt="" />
    <ButtonGet @get="fetchData"></ButtonGet>
    <CardView :employees="employees" @del="delEmployee"></CardView>
  </div>
</template>

<script>
import ButtonGet from '@/components/ButtonGet.vue';
import CardView from '@/components/CardView.vue';
import axios from 'axios';

export default {
  name: 'app',
  components: {
    ButtonGet,
    CardView,
  },
  data() {
    return {
      employees: [],
      updateAlert: false,
      serverAddress: process.env.VUE_APP_SERVER,
      offline: true
    };
  },
  created() {
    document.addEventListener('swUpdated', this.updateAvailable, {
      once: true,
    });
    window.addEventListener('online', () => (this.offline = false));
    window.addEventListener('offline', () => (this.offline = true));
  },
  methods: {
    async fetchData() {
      try {
        const { data } = await axios({
          url: this.serverAddress + '/employees',
        });
        this.employees = data;
        console.log('Data fetched');
      } catch (error) {
        console.log(error);
      }
    },
    async delEmployee(e) {
      try {
        await axios({
          url: this.serverAddress + '/employees/' + e.id,
          method: 'DELETE',
        });
        console.log('Deleted');
      } catch (error) {
        console.log(error);
      }
      this.fetchData();
    },
    updateAvailable() {
      this.updateAlert = true;
      if(confirm('There is an update available. Please refresh')) {
        window.location.reload();
      }
    },
  },
};
</script>

<style></style>
