
<template>
  <div id="chartdiv">

  </div>
</template>

<script>
/* eslint-disable @typescript-eslint/no-unsafe-assignment */
/* eslint-disable @typescript-eslint/no-unsafe-member-access */
/* eslint-disable @typescript-eslint/no-unsafe-return */
import { defineComponent } from 'vue'
import * as core from '@amcharts/amcharts4/core'
import * as maps from '@amcharts/amcharts4/maps'
import charType from '@amcharts/amcharts4-geodata/worldLow'
import animatedTheme from '@amcharts/amcharts4/themes/animated'
import { COUNTRIES } from '../utils/countries'

core.useTheme(animatedTheme)

// let chart = core.create('chartdiv', maps.MapChart)

export default defineComponent({
  name: 'MapComponent',
  props: {

  },
  data () {
    return {
    }
  },
  methods: {
  },
  mounted () {
    const chart = core.create('chartdiv', maps.MapChart)
    chart.geodata = charType
    chart.panBehavior = 'rotateLong'
    chart.projection.d3Projection = maps.d3geo.geoGnomonic()

    const polygonSeries = chart.series.push(new maps.MapPolygonSeries())
    polygonSeries.north = 90

    polygonSeries.useGeodata = true

    // Configure series
    const polygonTemplate = polygonSeries.mapPolygons.template
    polygonTemplate.tooltipText = '{name}: {populate}'
    polygonTemplate.fill = core.color('#74B266')
    polygonTemplate.propertyFields.id = 'id'

    // Create hover state and set alternative fill color
    const hs = polygonTemplate.states.create('hover')
    hs.properties.fill = core.color('#367B25')

    // Add grid
    const grid = chart.series.push(new maps.GraticuleSeries())

    polygonSeries.events.on('inited', function () {
      chart.series.values[0].data.map((polygon) => {
        const country = COUNTRIES.find(resp => resp.id === polygon.id)
        if (country) {
          polygon.populate = country.populate
        }
        return polygon
      })
    })

    grid.toBack()
  },
  computed: {
  }
})
</script>

<style>
  #chartdiv {
    width: 100%;
    height: 700px;
  }
</style>
