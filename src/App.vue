<template>
  <div id="app" class="container">
    <div>
      Obs: The COVID Tracking Project has ended all data collection as of March
      7, 2021.
    </div>
    <div class="row">
      <div class="col text-center">
        <h1>COVID-19 Data Visualization</h1>
      </div>
    </div>
    <div class="row mt-5" v-if="arrCasesBr.length > 0">
      <div class="col">
        <LineChart
          :chartData="arrCasesBr"
          :options="chartOptions"
          label="Casos do Brasil"
        />
      </div>
    </div>
    <div class="row mt-5" v-if="arrPositive.length > 0">
      <div class="col">
        <h2>Positive</h2>
        <LineChart
          :chartData="arrPositive"
          :options="chartOptions"
          label="Positive"
        />
      </div>
    </div>
    <div class="row mt-5" v-if="arrHospitalized.length > 0">
      <div class="col">
        <h2>Hospitalized</h2>
        <LineChart
          :chartData="arrHospitalized"
          :options="chartOptions"
          label="Positive"
        />
      </div>
    </div>
    <div class="row mt-5" v-if="arrInIcu.length > 0">
      <div class="col">
        <h2>In ICU</h2>
        <LineChart
          :chartData="arrInIcu"
          :options="chartOptions"
          label="In ICU"
        />
      </div>
    </div>
    <div class="row mt-5" v-if="arrOnVentilators.length > 0">
      <div class="col">
        <h2>On Ventilators</h2>
        <LineChart
          :chartData="arrOnVentilators"
          :options="chartOptions"
          label="On Ventilators"
        />
      </div>
    </div>
    <div class="row mt-5" v-if="arrRecovered.length > 0">
      <div class="col">
        <h2>Positive</h2>
        <LineChart
          :chartData="arrRecovered"
          :options="chartOptions"
          label="Recovered"
        />
      </div>
    </div>
    <div class="row mt-5" v-if="arrDeaths.length > 0">
      <div class="col">
        <h2>Positive</h2>
        <LineChart
          :chartData="arrDeaths"
          :options="chartOptions"
          label="Deaths"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import LineChart from "./components/LineChart.vue";

export default {
  name: "App",
  components: {
    LineChart,
  },
  data() {
    return {
      arrCasesBr: [],
      arrDeathBr: [],
      arrPositive: [],
      arrHospitalized: [],
      arrInIcu: [],
      arrOnVentilators: [],
      arrRecovered: [],
      arrDeaths: [],
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  methods: {
    async covidUSA() {
      const { data } = await axios.get(
        "https://api.covidtracking.com/v1/us/daily.json"
      );
      data.forEach((d) => {
        const date = moment(d.date, "YYYYMMDD").format("DD/MM/YY");
        const {
          positive,
          hospitalizedCurrently,
          inIcuCurrently,
          onVentilatorCurrently,
          recovered,
          death,
        } = d;
        this.arrPositive.push({ date, total: positive });
        this.arrHospitalized.push({ date, total: hospitalizedCurrently });
        this.arrInIcu.push({ date, total: inIcuCurrently });
        this.arrOnVentilators.push({ date, total: onVentilatorCurrently });
        this.arrRecovered.push({ date, total: recovered });
        this.arrDeaths.push({ date, total: death });
      });
    },
    async covidBR() {
      const response = await axios.get(
        "https://covid19-brazil-api.now.sh/api/report/v1"
      );
      const data = response.data.data;
      data.forEach((d) => {
        const { cases, deaths, uf } = d;
        this.arrCasesBr.push({ date: uf, total: cases });
        this.arrDeathBr.push({ date: uf, total: deaths });
      });
    },
  },
  async created() {
    this.covidBR();
    this.covidUSA();
  },
};
</script>

<style>
</style>
