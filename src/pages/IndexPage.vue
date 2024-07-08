<template>

  <q-page class="flex flex-center">
    <div class="row justify-center">
      <q-card flat bordered class="weather-card q-ml-md">
        <q-card-section class="q-pa-none">
          <div class="weather-content">
            <div class="time-location">
              <div class="text-subtitle1"><strong>Talakag, Bukidnon</strong></div>
            </div>
            <div class="weather-info">
              <q-icon color="primary" name="cloud" size="56px" />
              <div class="temperature">{{ current_temperature }}°C</div>
              <div class="text-overline"><strong>Current Temperature</strong></div>
            </div>
          </div>
        </q-card-section>

        <q-separator />

        <q-card-section class="q-pa-none">
          <div class="weather-content">
            <div class="weather-info">
              <div class="temperature">{{ humidity_temp }}%</div>
              <div class="text-overline"><strong>Humidity</strong>

              </div>
            </div>
          </div>
        </q-card-section>
      </q-card>


      <q-card flat  class="temp-card q-ml-md">
        <q-card-section class="q-pa-none">
            <div class="temp-info">
              <q-list bordered separator>
                <q-separator spaced />
                <q-card-section><q-item-label header class="row justify-center"><strong>Warehouse Running Temperature</strong></q-item-label></q-card-section>

                <q-item v-for="(record, index) in temperatureData" :key="index" v-ripple>

                  <q-item-section>
                    <q-item-label class="text-primary">Humidity: {{ record.humidity }}</q-item-label>
                    <q-item-label class="text-red"> Temperature: {{ record.temperature }}</q-item-label>
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
      <q-card flat bordered class="temp-card2 q-ml-md">
        <canvas id="humidityTemperatureChart"></canvas>
      </q-card>



    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';
import Chart from 'chart.js/auto'; // Import Chart.js


export default defineComponent({
  name: 'IndexPage',
  setup() {
    const current_temperature = ref(null);
    const max_temp = ref(null);
    const humidity_temp = ref(null);
    const temperatureData = ref([]);

    let chart = null; // Variable to hold the chart instance

    const fetchTemperatureData = async () => {
      try {
        const response = await axios.get('http://localhost:1234/temperature');
        temperatureData.value = response.data;

        // Debug log to check if data is fetched correctly
        console.log('Fetched temperature data:', temperatureData.value);

        createChart(); // Create or update the chart after fetching data
      } catch (error) {
        console.error('Error fetching temperature data', error);
      }
    };

    onMounted(() => {
      fetchTemperatureData();

      const apiKey = "";
      const location = "Talakag, Bukidnon";
      const units = "metric"; // Change units to metric for Celsius
      const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${location}&units=${units}&appid=${apiKey}`;

      axios.get(apiUrl)
        .then(response => {
          current_temperature.value = response.data.main.temp;
          humidity_temp.value = response.data.main.humidity;
        })
        .catch(error => {
          console.error("Error fetching weather data:", error);
        });
    });

    // Function to create or update the chart
    const createChart = () => {
      const ctx = document.getElementById('humidityTemperatureChart');

      if (!ctx) {
        // If the canvas element is not found, try again later
        console.error('Canvas element not found');
        return;
      }

      if (chart !== null) {
        chart.destroy();
      }


      chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: temperatureData.value.map(record => record.id),
          datasets: [
            {
              label: 'Humidity',
              data: temperatureData.value.map(record => parseFloat(record.humidity)),
              borderColor: 'blue',
              fill: false,
            },
            {
              label: 'Temperature',
              data: temperatureData.value.map(record => parseFloat(record.temperature)),
              borderColor: 'red',
              fill: false,
            },
          ],
        },
        options: {
          scales: {
            y: {
              beginAtZero: true,
            },
          },
        },
      });
    };

    // Fetch data and update the chart every 5 seconds
    setInterval(fetchTemperatureData, 20000);

    return {
      current_temperature,
      max_temp,
      humidity_temp,
      temperatureData,
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
.temp-card2 {
  width: 600px;
  border-radius: 16px;
  background-color: #fff;
}
.temp-info {
  /* max-height and overflow-y for scrollable content */
  max-height: 400px;
  overflow-y: auto;
}
.weather-widget {
  background-color: #f5f5f5;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
.weather-card {
  width: 300px;
  height: 400px;
  border-radius: 16px;
  background-color: #fff;
}
.weather-header {
  display: flex;
  justify-content: space-between;
  padding: 16px;
}
.weather-content {
  text-align: center;
  padding: 16px;
}
.time-location {
  margin-bottom: 16px;
}
.time {
  font-size: 24px;
  font-weight: bold;
}
.location {
  color: #888;
}
.weather-info {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.temperature {
  font-size: 48px;
  font-weight: bold;
  margin-top: 8px;
}
.weather-footer {
  display: flex;
  justify-content: space-around;
}
</style>
