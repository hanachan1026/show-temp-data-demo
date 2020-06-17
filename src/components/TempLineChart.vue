<template>
  <div class="small">
    <line-chart :chart-data="datacollection" :options="options"></line-chart>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import LineChart from "@/components/LineChart.vue";
import { Component } from "vue-property-decorator";
import { db } from "@/scripts/firebase";

@Component({
  components: {
    LineChart
  }
})
export default class TempLineChart extends Vue {
  datacollection: object = {};
  options: Chart.ChartOptions = {
    scales: {
      yAxes: [
        {
          ticks: {
            beginAtZero: false
          },
          gridLines: {
            display: true
          }
        }
      ],
      xAxes: [
        {
          gridLines: {
            display: true
          }
        }
      ]
    },
    legend: {
      display: true
    },
    responsive: true,
    maintainAspectRatio: false,
  };

  async created() {
    await this.listenToData();
  }

  async mounted() {
    const data = await this.fetchData();
    this.fillData(data);
  }

  async fetchData() {
    const dataRef = db.collection("data");
    const queryInfo = dataRef.orderBy("datetime");
    const snapshot = await queryInfo.get();
    const x: string[] = [];
    const y: number[] = [];

    snapshot.docs.forEach(doc => {
      const item = doc.data();
      const time = item.datetime;
      const temp = item.temp;
      let datetime;

      if (time !== undefined && temp !== undefined) {
        datetime = new Date(time * 1000).toLocaleTimeString('en-GB');

        x.push(datetime);
        y.push(temp);
      }
    });

    return [x, y];
  }

  listenToData() {
    const dataRef = db.collection("data");
    const queryInfo = dataRef.orderBy("datetime");
    const x: string[] = [];
    const y: number[] = [];

    queryInfo.onSnapshot(querySnapshot => {
      console.log('update');
      querySnapshot.docs.forEach(doc => {
        const item = doc.data();
        const time = item.datetime;
        const temp = item.temp;
        let datetime;

        if (time !== undefined && temp !== undefined) {
          datetime = new Date(time * 1000).toLocaleTimeString('en-GB');

          x.push(datetime);
          y.push(temp);
        }
      });

      this.datacollection = {
        labels: x,
        datasets: [
          {
            label: "Demo data",
            backgroundColor: "rgb(255, 99, 132)",
            fill: false,
            lineTension: 0,
            data: y,
          },
        ]
      };
    });
  }

  async fillData(data: (string[] | number[])[]) {
    const [x, y] = data;
    this.datacollection = {
      labels: x,
      datasets: [
        {
          label: "Demo data",
          backgroundColor: "rgb(255, 99, 132)",
          fill: false,
          lineTension: 0,
          data: y,
        },
      ]
    };
  }
}
</script>

<style>
.small {
  max-width: 600px;
  margin: 150px auto;
}
</style>
