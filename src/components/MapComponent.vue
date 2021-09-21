
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
    // En este primer bloque se crea la grafica
    const chart = core.create('chartdiv', maps.MapChart)
    chart.geodata = charType
    chart.panBehavior = 'rotateLong'
    chart.projection.d3Projection = maps.d3geo.geoGnomonic()

    const polygonSeries = chart.series.push(new maps.MapPolygonSeries())
    polygonSeries.north = 90

    polygonSeries.useGeodata = true

    // Aqui se configura la serie de datos, aqui se pone lo que se va a mostrar en el tooltip y el color
    const polygonTemplate = polygonSeries.mapPolygons.template
    polygonTemplate.tooltipText = '{name}: {populate}'
    polygonTemplate.fill = core.color('#74B266')

    // Aqui se crea el evento over para todos los poligonos de la grafica
    const hs = polygonTemplate.states.create('hover')
    hs.properties.fill = core.color('#367B25')

    // Esto no tengo idea de para que es
    const grid = chart.series.push(new maps.GraticuleSeries())

    // Aqui se le añade el campo adicional a cada dato, en este caso se añade la poblacion,
    // el nombre de los paises ya viene por defecto en la libreria
    polygonSeries.events.on('inited', function () {
      chart.series.values[0].data.map((polygon) => {
        const country = COUNTRIES.find(resp => resp.id === polygon.id)
        if (country) {
          polygon.populate = country.populate
        }
        return polygon
      })
    })

    // Por ultimo actualizamos la grafica para que surtan efectos los cambios
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
