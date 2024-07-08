<template>
  <q-page class="flex flex-center">
    <div class="row justify-center">
      <q-btn color="primary" label="Generate Report" @click="generateReport"/>

      <q-card flat  class="temp-card q-ml-md">
        <q-card-section class="q-pa-none">
            <div class="temp-info">
              <q-list bordered separator>
                <q-separator spaced />
                <q-card-section><q-item-label header class="row justify-center"><strong>Report:</strong></q-item-label></q-card-section>

                <q-item v-for="(record, index) in dailyReportData" :key="index" v-ripple>

                  <q-item-section>
                    <q-item-label class="text-primary">highest_humidity: {{ record.highest_humidity }}</q-item-label>
                    <q-item-label class="text-red"> highest_temperature: {{ record.highest_temperature }}</q-item-label>

                    <q-item-label class="text-primary">lowest_humidity: {{ record.lowest_humidity }}</q-item-label>
                    <q-item-label class="text-red"> lowest_temperature: {{ record.lowest_temperature }}</q-item-label>
                  </q-item-section>

                  <q-item-section side top>
                    <q-item-label caption>id: {{record.id}}</q-item-label>
                    <q-icon name="ac_unit" color="primary" />
                  </q-item-section>
                </q-item>
              </q-list>
            </div>
        </q-card-section>
      </q-card>
      <!-- reports -->

    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';
import Chart from 'chart.js/auto'; // Import Chart.js


export default defineComponent({
  name: 'Report',
  setup() {
    const temperatureData = ref([]);
    const dailyReportData = ref([]);

    const generateReport = async () => {
      try {
        const response = await axios.get('http://localhost:1234/generate_report');
        dailyReportData.value = response.data;
      } catch (error) {
        console.error('Error fetching report', error);
      }
    };

    return {
      generateReport,
      dailyReportData
    };
  },
});

</script>



<style scoped>
.temp-card {
  width: 350px;
  border-radius: 16px;
  background-color: #fff;
}
.temp-info {
  /* max-height and overflow-y for scrollable content */
  max-height: 400px;
  overflow-y: auto;
}
</style>
