<script lang="ts">
import { Line, mixins } from 'vue-chartjs';
import { Component, Mixins, Prop } from "vue-property-decorator";

@Component({})
export default class LineChart extends Mixins(Line, mixins.reactiveProp) {
  @Prop() options!: Chart.ChartOptions;
  
  mounted () {
    this.renderChart(this.chartData, this.options)
  }
}
</script>
