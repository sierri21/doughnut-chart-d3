<template>
  <div id="app">
    <doughnut-chart :dataset="dataset"/>
  </div>
</template>

<script>
import DoughnutChart from './components/doughnut-chart.vue'

export default {
  name: 'App',
  components: {
    DoughnutChart
  },
  data: () => ({
    dataset: {
      name: 'Income',
          currency: 'USDT',
        data: [
      { name: 'Portfolio', value: 70, color: '#15C7A7' },
      { name: 'Hedging', value: 15, color: '#1393EA' },
      { name: 'Intraday trading', value: 15, color: '#DCA654' }
    ],
        text1: '60% in USDT',
        centerText: 'Target annual',
        text2: 'Income'
    }
  })
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
