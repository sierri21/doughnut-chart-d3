<template>
  <div class="chart" />
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'DoughnutChart',
  props: {
    dataset: {
      type: Object,
      default: () => {}
    }
  },
  data: () => {
    return {
      width: 500,
      height: 250,
      radius: 86
    }
  },
  mounted () {
    this.drawChart()
  },
  methods: {
    drawChart () {
      const pieFunc = d3.pie().value(d => d.value)
      const data = pieFunc(this.dataset.data)

      const midAngle = d => d.startAngle + (d.endAngle - d.startAngle) / 2
      const outerArc = d3.arc().innerRadius(this.radius * 1.3).outerRadius(this.radius * 1.3)
      const innerArc = d3.arc().innerRadius(68).outerRadius(this.radius + 2)

      const svg = d3.create('svg')
          .attr('viewBox', [0, 0, this.width, this.height])
          .attr('width', this.width)
          .attr('height', this.height)

      // eslint-disable-next-line no-unused-vars
      const chart = svg
          .append('g')
          .attr('class', 'pie')
          .selectAll('path')
          .data(data)
          .join('path')
          .attr('transform', 'translate(' + this.width / 2 + ',' + this.height / 2 + ')')
          .attr('fill', d => d.data.color)
          .attr(
              'd',
              d3
                  .arc()
                  .innerRadius(68)
                  .outerRadius(this.radius)
          )
          .transition()
          .duration(1000)
          .attrTween('d', (d) => {
            const start = {
              startAngle: 0,
              endAngle: 0
            }
            const interpolate = d3.interpolate(start, d)
            return t => innerArc(interpolate(t))
          })

      // eslint-disable-next-line no-unused-vars
      const labels = svg
          .append('g')
          .attr('class', 'labels')
          .selectAll('foreignObject')
          .data(data)
          .join('foreignObject')
          .attr('width', 130)
          .attr('height', 57)
          // .attr( 'dy', '0.44rem')
          .html((d) => {
            if (midAngle(d) > Math.PI) {
              return '<div style="text-align: right">' + d.data.name + ': ' + d.data.value + '%' + '</div>'
            }
            return '<div style="text-align: left">' + d.data.name + ': ' + d.data.value + '%' + '</div>'
          })
          .attr('transform', (d) => {
            const pos = outerArc.centroid(d)
            const decidingSide = (midAngle(d) < Math.PI ? 1.1 : -1.1)
            pos[0] = ((this.radius) * 1.3 * decidingSide)
            if (midAngle(d) > Math.PI) {
              pos[0] -= 125
            }
            pos[0] += this.width / 2
            pos[1] += this.height / 2
            pos[1] -= 10
            return 'translate(' + pos + ')'
          })
          .attr('text-align', (d) => {
            return midAngle(d) < Math.PI ? 'start' : 'end'
          })
          .attr('opacity', 0)
          .transition()
          .duration(1000)
          .delay(1000)
          .styleTween('text-anchor', (d) => {
            return t => midAngle(d) < Math.PI ? 'start' : 'end'
          })
          .attr('opacity', 1)
          .attr('fill', 'rgba(37,37,37,0.8)')
      // eslint-disable-next-line no-unused-vars
      const lines = svg
          .append('g')
          .attr('class', 'lines')
          .selectAll('polyline')
          .data(data)
          .attr('opacity', 0)
          .join('polyline')
          .attr('points', (d) => {
            const pos = outerArc.centroid(d)
            pos[0] = this.radius * 1.3 * (midAngle(d) < Math.PI ? 1 : -1)
            pos[0] += this.width / 2
            pos[1] += this.height / 2
            return [
              [innerArc.centroid(d)[0] + this.width / 2, innerArc.centroid(d)[1] + this.height / 2],
              [outerArc.centroid(d)[0] + this.width / 2, outerArc.centroid(d)[1] + this.height / 2],
              pos]
          })
          .attr('fill', 'none')
          .transition()
          .duration(1000)
          .delay(1000)
          .attr('opacity', 1)
          .attr('stroke', '#ABABAB')
          .attr('stroke-width', '1px')

      const centeredText = svg
          .append('g')
          .attr('class', 'centerText')
          .append('text')

      centeredText
          .append('tspan')
          .attr('text-anchor', 'middle')
          .attr('font-size', 15)
          .attr('font-weight', 500)
          .text(this.dataset.text1)
          .attr('x', this.width / 2)
          .attr('y', this.height / 2 - 9)
      centeredText
          .append('tspan')
          .attr('text-anchor', 'middle')
          .attr('font-size', 14)
          .text(this.dataset.centerText)
          .attr('x', this.width / 2)
          .attr('y', this.height / 2 + 10)
      centeredText
          .append('tspan')
          .attr('text-anchor', 'middle')
          .attr('font-size', 14)
          .text(this.dataset.text2)
          .attr('x', this.width / 2)
          .attr('y', this.height / 2 + 28)
      centeredText
          .attr('opacity', 0)
          .transition()
          .delay(1000)
          .duration(1000)
          .attr('opacity', 1)
          .attr('fill', 'rgba(37,37,37,0.8)')

      document.querySelector('.chart').appendChild(svg.node())
    }
  }
}
</script>

<style scoped>

</style>
