<script setup>
import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css';
import { es } from "date-fns/locale";
import moment from 'moment';
import VueApexCharts from 'vue3-apexcharts';

moment.lang('es', {
  months: 'Enero_Febrero_Marzo_Abril_Mayo_Junio_Julio_Agosto_Septiembre_Octubre_Noviembre_Diciembre'.split('_'),
  monthsShort: 'Ene._Feb._Mar_Abr._May_Jun_Jul._Ago_Sept._Oct._Nov._Dec.'.split('_'),
  weekdays: 'Domingo_Lunes_Martes_Miercoles_Jueves_Viernes_Sabado'.split('_'),
  weekdaysShort: 'Dom._Lun._Mar._Mier._Jue._Vier._Sab.'.split('_'),
  weekdaysMin: 'Do_Lu_Ma_Mi_Ju_Vi_Sa'.split('_')
}
);

const empresa = ref(['AGROVISION'])
const itemsEmpresa = ref(['AGROVISION', 'ARENA VERDE'])

const horas = ref(0)
const minutos = ref(0)
const segundos = ref(0)
const ampm = ref('')
const dia = ref(0)
const nameFech = ref('')
const mes = ref(0)
const year = ref(0)
const tabs = ref('item-general')
const isActive = ref(true)
const fechaInicio = ref([])
const tabGrupoPlanta = ref(0)
const tabGrupoFruto = ref(0)
const tabGrupoSuelo = ref(0)
const tabGrupoRiego = ref(0)
const refVueApexChart = ref()
const fechaHora = ref()
const eventFechas = ref(1)
const tamanoComponente = ref(6)
const dataCrecimientoAcomulado = ref([])
const dataCrecimientoDiario = ref([])
const dataContraccionDiaria = ref([])


const categoria = ref(['AGV-A9-E07C02-T04A'])
const itemsCategoria = ref(['AGV-A9-E07C02-T04A'])


const actualizarHora = () => {
  fechaHora.value = moment().format('dddd [,] DD [de] MMMM [del] YYYY LTS')
}

const eventoSemana = () => {
  tabGrupoPlanta.value = 0
  // refVueApexChart.value.updateOptions({
  //   chart: {
  //     width: '100%',
  //     height: 300
  //   }
  // });
  tamanoComponente.value = 6
  eventFechas.value = 1 //evento 1 fechas semanal
  isActive.value = true;
  fechaGrupo(eventFechas.value)
}

const fechaGrupo = (event) => {
  var fechaActual = moment().format('DD-MM-YYYY')
  var fechas = []
  dataCrecimientoAcomulado.value = []
  dataCrecimientoDiario.value = []
  dataContraccionDiaria.value = []

  if (event == 1) {

    for (let i = 1; i <= 7; i++) {
      var fechaAnterior = moment(fechaActual, 'DD-MM-YYYY').subtract(i, 'days')

      fechas.push(fechaAnterior.format('MMM DD'))
    }

    dataCrecimientoAcomulado.value = [0, 0.018, 0.033, 0.048, 0.077, 0.095, 0.106]
    dataCrecimientoDiario.value = [0, 0.018, 0.015, 0.015, 0.029, 0.018, 0.011]
    dataContraccionDiaria.value = [0.024, 0.026, 0.037, 0.029, 0.04, 0.042, 0.029]

  } else if (event == 2) {
    for (let i = 1; i <= 30; i++) {
      var fechaAnterior = moment(fechaActual, 'DD-MM-YYYY').subtract(i, 'days')

      fechas.push(fechaAnterior.format('MMM DD'))
    }

    dataCrecimientoAcomulado.value = [-0.002, 0.026, null, 0.053, 0.062, 0.077, 0.09, 0.101, 0.112, 0.117, 0.134, 0.15, 0.163,
      0.176, 0.196, 0.211, 0.218, 0.231, 0.262, 0.268, 0.293, 0.319, 0.319, 0.337, 0.352, 0.367, 0.396, 0.414, 0.425, 0.451]
    dataCrecimientoDiario.value = [-0.03, 0.029, null, null, 0.009, 0.015, 0.013, 0.011, 0.011, 0.004, 0.018, 0.015, 0.013,
      0.013, 0.02, 0.015, 0.007, 0.013, 0.031, 0.007, 0.024, 0.026, 0, 0.018, 0.015, 0.015, 0.029, 0.018, 0.011, 0.026]
  }

  return fechas.reverse();
}

const eventoMes = () => {
  tabGrupoPlanta.value = null
  // refVueApexChart.value.updateOptions({
  //   chart: {
  //     width: '100%',
  //     height: 300
  //   }
  // });

  tamanoComponente.value = 12

  isActive.value = false;
  eventFechas.value = 2

  fechaGrupo(eventFechas.value)
}

const chartConfig = computed(() => {

  return [
    {
      title: 'Crecimiento acumulado',
      icon: 'tabler-plant-2',
      chartOptions: {
        chart: {
          width: '100%',
          height: 350,
          type: 'line',
          zoom: {
            enabled: false
          },
          toolbar: { show: false },
        },
        colors: ['#6dca94'],
        stroke: { curve: 'straight' },
        markers: {
          size: 6,
          strokeWidth: 6,
          strokeOpacity: 1,
          colors: ['#336e03'],
          strokeColors: ['#fff'],
        },
        dataLabels: {
          enabled: false
        },
        grid: {
          padding: { top: -10 },
          borderColor: "#E5E7E9",
          xaxis: {
            lines: { show: true },
          },
          yaxis: {
            lines: { show: true },
          },
        },
        xaxis: {
          categories: fechaGrupo(eventFechas.value),
          labels: {
            style: {
              colors: '#8b90b2',
              fontSize: '11px',
              fontFamily: 'Public Sans',
            },
          },
        },

        tooltip: {
          custom(data) {
            return `<div class='bar-chart pa-2'>
              <span>${data.series[data.seriesIndex][data.dataPointIndex]}mm</span>
            </div>`
          },
        },
        yaxis: {
          labels: {
            style: {
              colors: '#8b90b2',
              fontSize: '11px',
              fontFamily: 'Public Sans',
            },
            formatter: function (value) {
              // Personaliza el formato de la etiqueta del eje Y
              if (value != null || value != undefined) {
                return `${value.toFixed(3)} mm`
              } else {
                return ''
              }
            },
          },
        },
        fill: {
          type: 'gradient',
          gradient: {
            shade: 'dark',
            gradientToColors: ['#82E0AA'],
            shadeIntensity: 1,
            type: 'horizontal',
            opacityFrom: 1,
            opacityTo: 1,
            stops: [0, 100, 100, 100]
          },
        }
      },
      series: [
        {
          data: dataCrecimientoAcomulado
        }
      ]
    },
    {
      title: 'Crecimiento diario',
      icon: 'tabler-plant',
      chartOptions: {
        chart: {
          type: 'bar',
          height: 350,
          toolbar: { show: false },
        },
        annotations: {
          yaxis: [
            {
              y: 0,
              strokeDashArray: 9,
              borderColor: "#99A3A4",
              fillColor: "#c2c2c2",
              opacity: 0.8,
              offsetX: 0,
              offsetY: 0
            },
            {
              y: -0.028,
              strokeDashArray: 9,
              borderColor: "#FF4560",
              fillColor: "#FF4560",
              opacity: 0.8,
              offsetX: 0,
              offsetY: 0,
              label: {
                borderColor: "#FF4560",
                style: {
                  color: "#fff",
                  background: "#FF4560"
                },
                text: "Estrés"
              }
            }
          ]
        },
        colors: ['#6dca94'],
        legend: {
          show: false
        },
        plotOptions: {
          bar: {
            dataLabels: {
              position: "top"
            },
            horizontal: false,
            columnWidth: '25%',
            // endingShape: 'rounded',
            borderRadius: 18,
            startingShape: 'rounded',
            // colors: {
            //   backgroundBarRadius: 15,
            //   backgroundBarOpacity: 0.8,
            //   backgroundBarColors: ["#F2F2F2"],
            // },
          },
        },
        dataLabels: {
          enabled: true,
          formatter: (val) => {
            if (val != null || val != undefined) {
              return `${val} mm`
            } else {
              return 'N/A'
            }
          },
          offsetY: -20,
          style: {
            fontSize: '15px',
            colors: ['#8b90b2'],
            fontWeight: '600',
            fontFamily: 'Public Sans',
          },
        },
        stroke: {
          show: true,
          width: 2,
          colors: ['transparent']
        },
        xaxis: {
          categories: fechaGrupo(eventFechas.value),
          axisBorder: {
            show: true,
          },
          labels: {
            style: {
              colors: '#8b90b2',
              fontSize: '13px',
              fontFamily: 'Public Sans',
            },
          },
        },
        yaxis: {
          labels: {
            formatter(value) {
              if (value != null || value != undefined) {
                return `${value.toFixed(3)} mm`;
              } else {
                return ''
              }
            },
            style: {
              fontSize: '13px',
              colors: '#8b90b2',
              fontFamily: 'Public Sans',
            }
          },
        },
        grid: {
          show: true,
          strokeDashArray: 5,
          borderColor: "#99A3A4",
          yaxis: {
            lines: {
              show: false
            }
          },
          xaxis: {
            lines: {
              show: true
            }
          }
        },
        fill: {
          opacity: 1
        },
        tooltip: {
          enabled: false,
        }
      },
      series: [
        {
          data: dataCrecimientoDiario.value
        }
      ]
    },
    {
      title: 'Contracción diaria',
      icon: 'tabler-tree',
      chartOptions: {
        chart: {
          type: 'bar',
          height: 350,
          toolbar: { show: false },
        },
        colors: ['#6dca94'],
        legend: {
          show: false
        },
        plotOptions: {
          bar: {
            dataLabels: {
              position: "top"
            },
            horizontal: false,
            columnWidth: '25%',
            // endingShape: 'rounded',
            borderRadius: 18,
            startingShape: 'rounded',
          },
        },
        dataLabels: {
          enabled: true,
          formatter: (val) => {
            return `${val} mm`
          },
          offsetY: -20,
          style: {
            fontSize: '15px',
            colors: ['#8b90b2'],
            fontWeight: '600',
            fontFamily: 'Public Sans',
          },
        },
        stroke: {
          show: true,
          width: 2,
          colors: ['transparent']
        },
        xaxis: {
          categories: fechaGrupo(eventFechas.value),
          axisBorder: {
            show: true,
          },
          labels: {
            style: {
              colors: '#8b90b2',
              fontSize: '13px',
              fontFamily: 'Public Sans',
            },
          },
        },
        yaxis: {
          labels: {
            formatter(value) {
              return `${value.toFixed(3)} mm`
            },
            style: {
              fontSize: '13px',
              colors: '#8b90b2',
              fontFamily: 'Public Sans',
            }
          },
        },
        grid: {
          show: true,
          strokeDashArray: 5,
          borderColor: "#99A3A4",
          yaxis: {
            lines: {
              show: false
            }
          },
          xaxis: {
            lines: {
              show: true
            }
          }
        },
        fill: {
          opacity: 1
        },
        tooltip: {
          enabled: false,
        }
      },
      series: [
        {
          data: dataContraccionDiaria.value
        }
      ]
    }

  ]
})

const chartConfigFruto = computed(() => {
  return [
    {
      title: 'Tamaño de fruto',
      icon: 'tabler-cherry',
      chartOptions: {
      },
      series: [{}]
    }
  ]
})

const chartConfigSuelo = computed(() => {
  return [
    {
      title: 'Humedad del Suelo',
      icon: 'tabler-droplet-cog',
      chartOptions: {
      },
      series: [{}]
    }
  ]
})

const chartConfigRiego = computed(() => {
  return [
    {
      title: 'Riego',
      icon: 'tabler-droplets',
      chartOptions: {
      },
      series: [{}]
    }
  ]
})

onMounted(() => {
  setInterval(actualizarHora, 1000);
});
watch(() => {

})

</script>
<template>
  <div style="position: relative; margin: 0; padding: 0;">
    <div class="">
      <header class="header-secundary">
        <div class="header-content-container">
          <div class="d-flex  align-center justify-space-between flex-column flex-sm-row">
            <div class="d-flex align-center">
              <div>
                <svg class="icon-header" xmlns="http://www.w3.org/2000/svg" id="_레이어_1_사본" data-name="레이어 1 사본"
                  viewBox="0 0 100 100">
                  <defs>

                  </defs>
                  <path class="cls-1"
                    d="m30.1962891,58.828125c-.4467773,0-.8793945-.2402344-1.1040039-.6621094-.3520508-.6601562-.6542969-1.3583984-.8984375-2.0761719-1.2094727-3.5478516-.965332-7.3544922.6884766-10.7182617,1.6533203-3.3642578,4.5180664-5.8833008,8.065918-7.0927734,3.5473633-1.2099609,7.3540039-.9658203,10.7182617.6884766,3.3647461,1.6533203,5.8833008,4.5180664,7.0932617,8.065918.4365234,1.2792969.6865234,2.6098633.7421875,3.9541016.0283203.6894531-.5078125,1.2724609-1.1972656,1.3007812-.6865234.0390625-1.2714844-.5078125-1.3007812-1.1972656-.0458984-1.1040039-.2509766-2.1977539-.6103516-3.2504883-.9951172-2.9165039-3.0649414-5.2705078-5.8295898-6.6298828-2.7651367-1.3583984-5.8935547-1.559082-8.809082-.5654297-2.9160156.9941406-5.2700195,3.0644531-6.6293945,5.8291016-1.3588867,2.7646484-1.5595703,5.8930664-.5654297,8.809082.2011719.5908203.449707,1.1650391.7382812,1.7070312.324707.609375.09375,1.3662109-.5151367,1.6914062-.1875.0996094-.3886719.1464844-.5869141.1464844Z" />
                  <path class="cls-1"
                    d="m55.3212891,77.8222656c-2.15625,0-4.2988281-.5039062-6.2856445-1.5019531-.6171875-.3105469-.8657227-1.0615234-.5561523-1.6787109.3100586-.6162109,1.0620117-.8632812,1.6782227-.5556641,2.7875977,1.3994141,5.9467773,1.6162109,8.8999023.6103516,6.0195312-2.0517578,9.2470703-8.6191406,7.1943359-14.6386719-2.0517578-6.0185547-8.6210938-9.2460938-14.6386719-7.1943359-3.3100586,1.1279297-5.9008789,3.6611328-7.1079102,6.9492188-.237793.6474609-.9560547.9785156-1.6040039.7431641-.6479492-.2382812-.9804688-.9560547-.7426758-1.6044922,1.4677734-4,4.6201172-7.0810547,8.6479492-8.4545898,7.3251953-2.496582,15.3144531,1.4301758,17.8125,8.7543945,1.2089844,3.5478516.9648438,7.3544922-.6884766,10.71875s-4.5185547,5.8837891-8.0664062,7.09375c-1.4873047.5068359-3.0185547.7587891-4.5429688.7587891Z" />
                  <path class="cls-1"
                    d="m34.9951172,87.2333984c-6.6025391,0-12.7807617-4.15625-15.0268555-10.7441406-1.3671875-4.0107422-1.0908203-8.3134766.777832-12.1152344,1.8686523-3.8027344,5.1064453-6.6494141,9.1166992-8.0166016,8.277832-2.8222656,17.309082,1.6171875,20.1318359,9.8945312h0c2.8217773,8.2783203-1.6171875,17.3095703-9.8950195,20.1328125-1.6894531.5751953-3.4111328.8486328-5.1044922.8486328Zm-.0244141-29.2255859c-1.4272461,0-2.8769531.2304688-4.3012695.7167969-3.3779297,1.1513672-6.1054688,3.5488281-7.6796875,6.7519531s-1.8071289,6.8271484-.6552734,10.2060547c2.3774414,6.9736328,9.9858398,10.7148438,16.9584961,8.3349609,6.9731445-2.3769531,10.7124023-9.9853516,8.3354492-16.9589844-1.8925781-5.5488281-7.097168-9.0507812-12.6577148-9.0507812Z" />
                  <path class="cls-1"
                    d="m31.543457,81.2441406c-.2700195,0-.5454102-.0410156-.8178711-.125-1.1347656-.3505859-1.8789062-1.3447266-1.8959961-2.5332031l-.015625-1.078125c-.0009766-.0839844-.0551758-.15625-.1347656-.1806641l-1.0307617-.3183594c-1.1352539-.3505859-1.8793945-1.3447266-1.8964844-2.5322266s.6982422-2.2021484,1.8222656-2.5859375l1.0214844-.3476562c.0791016-.0273438.1308594-.1005859.1293945-.1835938l-.015625-1.0791016c-.0170898-1.1884766.6987305-2.203125,1.8227539-2.5869141,1.1259766-.3818359,2.3105469-.015625,3.0219727.9345703l.6474609.8642578c.0498047.0664062.1362305.0927734.2128906.0664062l1.0244141-.3486328c1.121582-.3818359,2.3085938-.0175781,3.0209961.9335938.7119141.9511719.7299805,2.1923828.0454102,3.1640625l-.621582.8818359c-.0478516.0683594-.0463867.1582031.003418.2246094l.6469727.8642578c.7119141.9501953.7294922,2.1923828.0454102,3.1630859s-1.8588867,1.3701172-2.9941406,1.0205078l-1.0307617-.3183594c-.0791016-.0234375-.1655273.0039062-.2138672.0722656l-.6210938.8818359c-.5200195.7373047-1.3227539,1.1464844-2.1762695,1.1464844Zm-.128418-11.1982422c-.0219727,0-.0473633.0048828-.0761719.0146484-.1313477.0449219-.1303711.1386719-.1293945.1835938l.015625,1.078125c.0170898,1.1699219-.715332,2.2099609-1.8227539,2.5869141l-1.0209961.3476562c-.0429688.0146484-.1318359.0449219-.1298828.1835938s.0917969.1660156.1347656.1796875l1.03125.3183594c1.1171875.3457031,1.8789062,1.3632812,1.8964844,2.5322266l.015625,1.0791016c.0004883.0458984.0019531.1396484.1342773.1806641.1298828.0400391.1865234-.0351562.2119141-.0722656l.6220703-.8828125c.675293-.9560547,1.8803711-1.3613281,2.9951172-1.0205078l1.0302734.3183594c.0429688.0126953.1323242.0419922.2128906-.0722656.0800781-.1132812.0239258-.1884766-.0029297-.2246094l-.6474609-.8642578c-.7006836-.9365234-.71875-2.2070312-.0454102-3.1630859l.621582-.8818359c.0263672-.0371094.0800781-.1132812-.0029297-.2246094-.0839844-.1113281-.1723633-.0810547-.2133789-.0664062l-1.0239258.3486328c-1.1040039.3759766-2.3193359.0029297-3.0214844-.9345703l-.6469727-.8642578h0c-.0209961-.0283203-.0600586-.0800781-.1381836-.0800781Z" />
                  <g>
                    <path class="cls-2"
                      d="m60.0256367,60.1880305l.5661892.7561302c.3273414.4371555.898945.6138142,1.4158586.4375825l.8940846-.3048208c1.155854-.394066,2.1406966.9211646,1.4372173,1.9193646l-.5441605.772135c-.3146055.4464086-.3059827,1.0446265.0213587,1.481782l.5661892.7561302c.7319575.9775094-.2145679,2.320579-1.3812996,1.9599911l-.9024988-.2789245c-.5217783-.1612598-1.0880527.0318005-1.4026583.4782091l-.5441605.772135c-.7034793.9982-2.2733069.5130321-2.2909074-.7080234l-.0136145-.9445198c-.0078712-.5460726-.3664709-1.0249727-.8882491-1.1862325l-.9024988-.2789245c-1.1667317-.3605878-1.190413-2.0035077-.034559-2.3975737l.8940846-.3048208c.5169136-.1762317.8615613-.6652685.8536901-1.2113411l-.0136145-.9445198c-.0176005-1.2210555,1.5375912-1.751268,2.2695487-.7737586Z" />
                    <path class="cls-1"
                      d="m59.1425781,70.7265625c-.2519531,0-.5078125-.0380859-.7626953-.1162109-1.0585938-.3271484-1.7529297-1.2548828-1.7685547-2.3632812l-.0136719-.9433594-.9091797-.2890625c-1.0605469-.328125-1.7548828-1.2558594-1.7705078-2.3642578-.015625-1.1074219.6523438-2.0537109,1.7001953-2.4111328l.8935547-.3046875-.0058594-.9541016c-.015625-1.1083984.6513672-2.0556641,1.7011719-2.4130859,1.0478516-.3574219,2.1552734-.0136719,2.8193359.8720703l.5654297.7548828.90625-.3007812c1.0527344-.3554688,2.15625-.0146484,2.8193359.8710938.6650391.8867188.6816406,2.0458984.0429688,2.9511719l-.5429688.7724609.5654297.7675781c.6650391.8886719.6806641,2.046875.0429688,2.9521484-.6386719.90625-1.734375,1.2773438-2.7939453.953125l-.9023438-.2792969-.5566406.7763672c-.484375.6875-1.2333984,1.0683594-2.0302734,1.0683594Zm-.1308594-9.8037109l.0078125.9648438c.015625,1.0917969-.6679688,2.0615234-1.7011719,2.4130859l-.8925781.3046875.9013672.2988281c1.0439453.3222656,1.7548828,1.2714844,1.7705078,2.3632812l.0136719.9433594.5644531-.765625c.6259766-.8916016,1.7460938-1.2744141,2.7919922-.9521484l.9023438.2792969-.5537109-.7724609c-.6542969-.8730469-.671875-2.0595703-.0429688-2.9511719l.5429688-.7724609-.9052734.2890625c-1.0361328.3496094-2.1660156.0009766-2.8193359-.8710938l-.5664062-.7558594c0-.0009766-.0009766-.0009766-.0009766-.0009766-.0078125-.0107422-.0117188-.0146484-.0126953-.0146484Z" />
                  </g>
                  <g>
                    <path class="cls-2"
                      d="m37.8200185,43.7551688l.5661892.7561302c.3273414.4371555.898945.6138142,1.4158586.4375825l.8940846-.3048208c1.155854-.394066,2.1406966.9211646,1.4372173,1.9193646l-.5441605.772135c-.3146055.4464086-.3059827,1.0446265.0213587,1.481782l.5661892.7561302c.7319575.9775094-.2145679,2.320579-1.3812996,1.9599911l-.9024988-.2789245c-.5217783-.1612598-1.0880527.0318005-1.4026583.4782091l-.5441605.772135c-.7034793.9982-2.2733069.5130321-2.2909074-.7080234l-.0136145-.9445198c-.0078712-.5460726-.3664709-1.0249727-.8882491-1.1862325l-.9024988-.2789245c-1.1667317-.3605878-1.190413-2.0035077-.034559-2.3975737l.8940846-.3048208c.5169136-.1762317.8615613-.6652685.8536901-1.2113411l-.0136145-.9445198c-.0176005-1.2210555,1.5375912-1.751268,2.2695487-.7737586Z" />
                    <path class="cls-1"
                      d="m36.9375,54.2939453c-.2519531,0-.5087891-.0380859-.7631836-.1171875-1.0585938-.3271484-1.7529297-1.2539062-1.769043-2.3623047l-.0136719-.9438477-.909668-.2890625c-1.0585938-.3276367-1.753418-1.2548828-1.769043-2.362793-.0161133-1.1079102.6513672-2.0546875,1.7001953-2.4121094l.8935547-.3051758-.0063477-.9545898c-.015625-1.1079102.6513672-2.0546875,1.7001953-2.4121094,1.0478516-.359375,2.1547852-.0166016,2.8198242.8710938l.5664062.7558594.90625-.3007812c1.0463867-.3588867,2.1552734-.015625,2.8198242.871582.6640625.887207.6806641,2.0454102.0419922,2.9511719l-.543457.7719727.565918.7680664c.6645508.8876953.6811523,2.0463867.0419922,2.9526367-.6386719.9052734-1.7363281,1.2783203-2.793457.9511719l-.9013672-.2783203-.5561523.7753906c-.4853516.6884766-1.234375,1.0693359-2.0307617,1.0693359Zm-.1308594-9.8037109l.0073242.965332c.015625,1.0917969-.6679688,2.0615234-1.7011719,2.4125977l-.8925781.3046875.9023438.2988281c1.0424805.3222656,1.753418,1.2719727,1.769043,2.362793l.0136719.9438477.5629883-.765625c.628418-.8911133,1.7529297-1.2729492,2.793457-.9521484l.9023438.2783203-.5541992-.7719727c-.6542969-.8740234-.6708984-2.0605469-.0419922-2.9521484l.543457-.7714844-.9057617.2885742c-1.0327148.3530273-2.1665039.0019531-2.8198242-.871582l-.5664062-.7553711v-.0004883c-.0078125-.0102539-.0126953-.0141602-.0126953-.0141602Z" />
                  </g>
                  <path class="cls-1"
                    d="m45.8808594,40.690918c-.3681641,0-.7329102-.1616211-.9794922-.4726562-.137207-.171875-3.3388672-4.2714844-2.3618164-9.0810547.6411133-3.1557617,2.8925781-5.8041992,6.6914062-7.8725586.0878906-.0478516.1811523-.0854492.2783203-.1108398.4248047-.1196289,2.559082-.8530273,1.972168-3.3383789-.1201172-.5117188.0917969-1.0439453.53125-1.3325195.4414062-.2871094,1.0136719-.2695312,1.4335938.0439453.0898438.0673828,8.9228516,6.8354492,5.265625,18.5629883-.2060547.6591797-.9042969,1.0273438-1.5654297.8212891-.6591797-.2055664-1.0263672-.90625-.8212891-1.5654297,2.1044922-6.7475586-.5498047-11.5341797-2.5478516-13.9716797-.640625,1.7060547-2.1689453,2.7592773-3.4707031,3.1518555-3.0473633,1.6865234-4.8359375,3.7416992-5.3168945,6.1088867-.7480469,3.6811523,1.8427734,6.9956055,1.8686523,7.0288086.4296875.5405273.3393555,1.3266602-.2011719,1.7558594-.2294922.1826172-.5039062.2714844-.7763672.2714844Z" />
                  <path class="cls-1"
                    d="m61.1064453,53.4248047c-.1542969,0-.3085938-.0029297-.4658203-.0078125-.6894531-.0214844-1.2314453-.5986328-1.2089844-1.2890625.0214844-.6894531.5947266-1.234375,1.2890625-1.2094727,2.4462891.0805664,4.3720703-.6025391,5.6738281-2.0253906,2.7988281-3.0595703,2.1601562-8.6206055,2.1542969-8.6767578-.171875-2.9921875.6914062-4.956543,1.6005859-6.1748047-3.6894531-.9086914-6.7539062-.5556641-9.1220703,1.0605469-5.2724609,3.5957031-5.6787109,12.3413086-5.6826172,12.4291992-.0273438.6899414-.6367188,1.2255859-1.296875,1.2001953-.6894531-.0268555-1.2275391-.6064453-1.2011719-1.2954102.015625-.409668.4501953-10.0776367,6.7626953-14.3925781,3.4794922-2.378418,7.984375-2.6328125,13.3847656-.7607422.4746094.1645508.8046875.5966797.8378906,1.0976562.0341797.5004883-.2353516.9731445-.6835938,1.1992188-.0800781.0439453-2.34375,1.324707-2.1113281,5.4135742.0263672.1899414.7841797,6.6591797-2.7890625,10.5776367-1.7285156,1.8950195-4.1298828,2.8540039-7.1416016,2.8540039Z" />
                  <path class="cls-1"
                    d="m57.8330078,36.7539062c-.2109375,0-.4238281-.0532227-.6191406-.1645508-.5439453-.3105469-.7734375-.9755859-.5380859-1.5556641.3574219-.8789062,8.8945312-21.5288086,22.3115234-22.2680664.3730469-.0268555.7324219.1259766.9853516.3984375.2529297.2729492.3710938.644043.3222656,1.0131836,0,.0024414-.1279297,1.1713867.4521484,2.3017578.1621094.3154297.1806641.6850586.0546875,1.0161133s-.3876953.59375-.7177734.7211914c-.1064453.0410156-10.7314453,4.2583008-21.2568359,18.0458984-.2431641.3198242-.6152344.4916992-.9941406.4916992Zm20.0087891-21.3457031c-5.5878906.9677734-10.2910156,6.0878906-13.6025391,10.9770508,6.2050781-6.0161133,11.5371094-8.925293,13.8017578-9.9995117-.0966797-.3466797-.1591797-.6767578-.1992188-.9775391Z" />
                  <path class="cls-1"
                    d="m55.1933594,50.2158203c-.1171875,0-.2363281-.0166016-.3544922-.0517578-.6621094-.1958008-1.0400391-.8901367-.8447266-1.5517578.1289062-.434082,3.2285156-10.6484375,10.8662109-11.6806641.6806641-.0976562,1.3134766.3867188,1.4052734,1.0712891.0927734.684082-.3867188,1.3139648-1.0712891,1.40625-6.0302734.8149414-8.7753906,9.8212891-8.8027344,9.9121094-.1611328.5429688-.6591797.8945312-1.1982422.8945312Z" />
                  <path class="cls-1"
                    d="m52.9052734,44.9379883c-.0410156,0-.0820312-.0019531-.1240234-.0058594-.6875-.0678711-1.1894531-.6791992-1.1220703-1.3662109.4238281-4.3183594.3466797-11.0083008-1.1489258-12.3603516-.512207-.4628906-.5517578-1.253418-.0888672-1.765625.4624023-.5131836,1.2524414-.5522461,1.7651367-.0888672,2.9570312,2.6723633,2.1523438,12.5136719,1.9609375,14.4594727-.0634766.6450195-.6074219,1.1274414-1.2421875,1.1274414Z" />
                </svg>
              </div>

              <div>
                <span>AGV-A9-E07C02-T04A</span>
              </div>
            </div>

            <div class="flex-grow-2" style="width: 50%;">
              <VRow class="">
                <VCol cols="12" md="6" sm="6">
                  <VAutocomplete v-model="empresa" variant="outlined" label="Empresa" :items="itemsEmpresa" />
                </VCol>
                <VCol cols="12" md="6" sm="6">
                  <VAutocomplete v-model="categoria" variant="outlined" label="Categoria" :items="itemsCategoria" />
                </VCol>
              </VRow>
            </div>
          </div>
          <VDivider class="mt-1 pb-3" />
          <div class="d-flex  align-center justify-space-between flex-column flex-sm-row pb-3">
            <div class="d-flex  align-center flex-column flex-sm-row">
              <div class="px-3">
                <label class="ml-2 fecha-hora" for="">{{ fechaHora }}</label>
              </div>

              <VDivider vertical />

              <div class="px-3">
                <div class="d-flex  align-center">
                  <VIcon icon="tabler-cloud-storm" class="mr-3"></VIcon>
                  <span>0%</span>
                </div>
              </div>

              <VDivider vertical />

              <div class="px-3">
                <div class="d-flex  align-center">
                  <VIcon icon="tabler-cloud-rain" class="mr-3"></VIcon>
                  <span>0mm</span>
                </div>
              </div>

              <VDivider vertical />

              <div class="px-3">
                <div class="d-flex  align-center">
                  <VIcon icon="tabler-temperature" class="mr-3"></VIcon>
                  <span>21⁰-29⁰</span>
                </div>
              </div>

              <VDivider vertical />

              <div class="px-3">
                <div class="d-flex  align-center">
                  <VIcon icon="tabler-wind" class="mr-3"></VIcon>
                  <span>9.5km/hr</span>
                </div>
              </div>

            </div>

            <div class="d-flex  align-center flex-column flex-sm-row">

              <div class="px-3">
                <span>Arándano</span>
              </div>
              <VDivider vertical />

              <div class="px-3">
                <span>Arena</span>
              </div>
              <VDivider vertical />
              <div class="px-3">
                <VMenu activator="parent" open-on-hover location="bottom" :close-on-content-click="false">
                  <template #activator="{ props }">
                    <span v-bind="props" style="cursor: pointer;">Cultivo</span>
                  </template>

                  <VCard max-width="3500">
                    <VCardItem class="px-6 py-3">
                      <h3>Etapa fenológica</h3>
                    </VCardItem>
                    <VDivider />
                    <VCardText class="">
                      <VTimeline side="end" align="start" truncate-line="both" density="compact"
                        class="v-timeline-density-compact v-timeline-icon-only">
                        <VTimelineItem>
                          <template #icon>
                            <VIcon size="20" icon="tabler-flower" color="#5D6D7E" />
                          </template>

                          <div class="d-flex align-center gap-2">
                            <div>
                              <span>Floración</span>
                            </div>
                            <div class="datePicker">
                              <VueDatePicker prepend-inner-icon="tabler-calendar-check" v-model="fechaInicio"
                                :format-locale="es" format="dd/MM/yyyy" :enable-time-picker="false" />
                            </div>
                          </div>
                        </VTimelineItem>

                        <VTimelineItem>
                          <template #icon>
                            <VIcon size="20" icon="tabler-cherry" color="#5D6D7E" />
                          </template>

                          <div>
                            <span>Cuaja de frutos</span>
                          </div>
                        </VTimelineItem>

                        <VTimelineItem>
                          <template #icon>
                            <VIcon size="20" icon="tabler-brand-asana" color="#5D6D7E" />
                          </template>

                          <div>
                            <span>Desarrollo de frutos</span>
                          </div>
                        </VTimelineItem>

                        <VTimelineItem>
                          <template #icon>
                            <VIcon size="20" icon="tabler-apple" color="#5D6D7E" />
                          </template>

                          <div>
                            <span>Maduración de fruto</span>
                          </div>
                        </VTimelineItem>

                        <VTimelineItem class="active-timeline">
                          <template #icon>
                            <VIcon class="active-timeline" size="20" icon="tabler-seeding" color="#5D6D7E" />
                          </template>

                          <div>
                            <span>Cosecha</span>
                          </div>
                        </VTimelineItem>

                        <VTimelineItem>
                          <template #icon>
                            <VIcon size="20" icon="tabler-plant" color="#5D6D7E" />
                          </template>

                          <div>
                            <span>Postcosecha</span>
                          </div>
                        </VTimelineItem>

                      </VTimeline>
                    </VCardText>
                    <!-- <VDivider /> -->
                  </VCard>
                </VMenu>
              </div>
              <VDivider vertical />

              <div class="px-3">
                <span>2.64 Hectáreas</span>
              </div>
              <VDivider vertical />

              <div class="px-3">
                <span>3 Años</span>
              </div>
            </div>
          </div>
        </div>
      </header>

      <section class="body-section">
        <VTabs v-model="tabs">
          <VTab value="item-general">Vista General</VTab>
          <VTab value="item-reco">Recomendaciones</VTab>
          <VTab value="item-grafico">Gráficos</VTab>
        </VTabs>
        <VWindow v-model="tabs">
          <VWindowItem value="item-general">
            <div class="d-flex align-center justify-space-between px-5 py-3">
              <div></div>
              <div class="container-btn-cuestiom">
                <VBtn @click="eventoSemana()" variant="text" class="btn-cuestiom" :class="isActive ? 'selected' : ''">
                  Semana
                </VBtn>

                <VBtn @click="eventoMes()" variant="text" class="btn-cuestiom" :class="!isActive ? 'selected' : ''">
                  Mes
                </VBtn>

              </div>
            </div>

            <VRow class="px-5 py-5">
              <VCol md="3" cols="12">
                <VCard style="border-block-start: 0.23rem solid rgba(109, 202, 148, var(--v-disabled-opacity));">
                  <VCardText class="py-5 px-9 d-flex align-center justify-space-between ">
                    <div>
                      <h4 class="text-h6 text-uppercase mb-3" style="font-weight: 600;">Crecimiento de la Planta</h4>

                      <div class="d-flex align-center gap-2 mb-2 pb-1 flex-wrap">
                        <h5 class="text-h3 font-weight-medium">0.121mm</h5>
                        <VChip label color="success" class="text-uppercase" style="font-size: 10.5px;">
                          Total del campo
                        </VChip>
                      </div>
                      <span class="text-xm text-uppercase" style="font-size: 12px;">Sobre el promedio</span>
                    </div>

                    <div>
                      <VIcon size="60" icon="tabler-trees" color="#6dca94"></VIcon>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>

              <VCol md="3" cols="12">
                <VCard style="border-block-start: 0.23rem solid rgba(115,103,240, var(--v-disabled-opacity));">
                  <VCardText class="py-5 px-9 d-flex align-center justify-space-between ">
                    <div>
                      <h4 class="text-h6 text-uppercase mb-3" style="font-weight: 600;">Crecimiento de la Fruta</h4>
                      <div class="d-flex align-center gap-2 mb-2 pb-1 flex-wrap">
                        <h5 class="text-h3 font-weight-medium">0.0mm</h5>
                        <VChip label color="success" class="text-uppercase" style="font-size: 10.5px;">
                          Total del campo
                        </VChip>
                      </div>
                      <span class="text-xm text-uppercase" style="font-size: 12px;">Similar al promedio</span>
                    </div>

                    <div>
                      <VIcon size="60" icon="tabler-apple" color="primary"></VIcon>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>

              <VCol md="3" cols="12">
                <VCard style="border-block-start: 0.23rem solid rgba(133, 193, 233, var(--v-disabled-opacity));">
                  <VCardText class="py-5 px-9 d-flex align-center justify-space-between ">
                    <div>
                      <h4 class="text-h6 text-uppercase mb-3" style="font-weight: 600;">Humedad del Suelo</h4>
                      <div class="d-flex align-center gap-2 mb-2 pb-1 flex-wrap">
                        <h5 class="text-h3 font-weight-medium"> 0.0% </h5>
                        <VChip class="text-uppercase" label color="success" style="font-size: 10.5px;">
                          Estable
                        </VChip>
                      </div>
                      <span class="text-xm text-uppercase" style="font-size: 12px;">Promedio Total</span>
                    </div>

                    <div>
                      <VIcon size="60" icon="tabler-droplet-cog" color="#85C1E9"></VIcon>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>

              <VCol md="3" cols="12">
                <VCard style="border-block-start: 0.23rem solid rgba(133, 193, 233, var(--v-disabled-opacity));">
                  <VCardText class="py-5 px-9 d-flex align-center justify-space-between ">
                    <div>
                      <h4 class="text-h6 text-uppercase mb-3" style="font-weight: 600;">Riego</h4>
                      <div class="d-flex align-center gap-2 mb-2 pb-1 flex-wrap">
                        <h5 class="text-h3 font-weight-medium">0.0mm</h5>
                        <VChip label color="success" class="text-uppercase" style="font-size: 10.5px;">
                          Total del campo
                        </VChip>
                      </div>
                      <span class="text-xm text-uppercase" style="font-size: 12px;">Similar al promedio</span>
                    </div>

                    <div>
                      <VIcon size="60" icon="tabler-droplets" color="#85C1E9"></VIcon>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>

            </VRow>

            <!--Semana--->
            <VRow class="px-5 py-0">
              <VCol :md="tamanoComponente" cols="12">
                <VCard>
                  <VCardItem class="pb-sm-0">
                    <VCardTitle>Planta</VCardTitle>
                    <VCardSubtitle>Crecimiento de tallo </VCardSubtitle>

                    <!-- <template #append>
                      <div class="mt-n4 me-n2">
                        <MoreBtn :menu-list="moreList" />
                      </div>
                    </template> -->
                  </VCardItem>

                  <VCardText class="mt-5">
                    <VSlideGroup v-model="tabGrupoPlanta" show-arrows mandatory>
                      <VSlideGroupItem v-for="(items, index) in chartConfig" :key="items.title"
                        v-slot="{ isSelected, toggle }" :value="index">

                        <div style="block-size: 94px; inline-size: 130px;"
                          :style="isSelected ? 'border-color:rgb(var(--v-theme-primary)) !important' : ''"
                          :class="isSelected ? 'border' : 'border border-dashed'"
                          class="d-flex flex-column justify-center align-center cursor-pointer rounded px-5 py-2 me-6"
                          @click="toggle">

                          <VAvatar rounded size="38" :color="isSelected ? 'primary' : 'secondary'" variant="tonal"
                            class="mb-2">
                            <VIcon :icon="items.icon" />
                          </VAvatar>

                          <p class="mb-0 font-weight-medium text-center">
                            {{ items.title }}
                          </p>

                        </div>
                      </VSlideGroupItem>
                    </VSlideGroup>

                    <VueApexCharts ref="refVueApexChart" :key="tabGrupoPlanta"
                      :options="chartConfig[Number(tabGrupoPlanta)].chartOptions"
                      :series="chartConfig[Number(tabGrupoPlanta)].series" height="300" class="mt-3 w-100" />
                  </VCardText>
                </VCard>
              </VCol>

              <VCol :md="tamanoComponente" cols="12">
                <VCard>
                  <VCardItem class="pb-sm-0">
                    <VCardTitle>Fruto</VCardTitle>
                    <VCardSubtitle>Crecimiento del fruto </VCardSubtitle>

                    <!-- <template #append>
                      <div class="mt-n4 me-n2">
                        <MoreBtn :menu-list="moreList" />
                      </div>
                    </template> -->
                  </VCardItem>

                  <VCardText class="mt-5">
                    <VSlideGroup v-model="tabGrupoFruto" show-arrows mandatory>
                      <VSlideGroupItem v-for="(items, index) in chartConfigFruto" :key="items.title"
                        v-slot="{ isSelected, toggle }" :value="index">

                        <div style="block-size: 94px; inline-size: 130px;"
                          :style="isSelected ? 'border-color:rgb(var(--v-theme-primary)) !important' : ''"
                          :class="isSelected ? 'border' : 'border border-dashed'"
                          class="d-flex flex-column justify-center align-center cursor-pointer rounded px-5 py-2 me-6"
                          @click="toggle">

                          <VAvatar rounded size="38" :color="isSelected ? 'primary' : 'secondary'" variant="tonal"
                            class="mb-2">
                            <VIcon :icon="items.icon" />
                          </VAvatar>

                          <p class="mb-0 font-weight-medium text-center">
                            {{ items.title }}
                          </p>

                        </div>
                      </VSlideGroupItem>
                    </VSlideGroup>

                    <div :key="tabGrupoFruto" class="d-flex justify-center align-center flex-column container-no-data">
                      <VIcon icon="tabler-database-cog" size="90"></VIcon>
                      <span class="text-uppercase">En desarrollo</span>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>

              <VCol :md="tamanoComponente" cols="12">
                <VCard>
                  <VCardItem class="pb-sm-0">
                    <VCardTitle>Suelo</VCardTitle>
                    <VCardSubtitle>Resumen general</VCardSubtitle>

                    <!-- <template #append>
                      <div class="mt-n4 me-n2">
                        <MoreBtn :menu-list="moreList" />
                      </div>
                    </template> -->
                  </VCardItem>

                  <VCardText class="mt-5">
                    <VSlideGroup v-model="tabGrupoSuelo" show-arrows mandatory>
                      <VSlideGroupItem v-for="(items, index) in chartConfigSuelo" :key="items.title"
                        v-slot="{ isSelected, toggle }" :value="index">

                        <div style="block-size: 94px; inline-size: 130px;"
                          :style="isSelected ? 'border-color:rgb(var(--v-theme-primary)) !important' : ''"
                          :class="isSelected ? 'border' : 'border border-dashed'"
                          class="d-flex flex-column justify-center align-center cursor-pointer rounded px-5 py-2 me-6"
                          @click="toggle">

                          <VAvatar rounded size="38" :color="isSelected ? 'primary' : 'secondary'" variant="tonal"
                            class="mb-2">
                            <VIcon :icon="items.icon" />
                          </VAvatar>

                          <p class="mb-0 font-weight-medium text-center">
                            {{ items.title }}
                          </p>

                        </div>
                      </VSlideGroupItem>
                    </VSlideGroup>

                    <div :key="tabGrupoSuelo" class="d-flex justify-center align-center flex-column container-no-data">
                      <VIcon icon="tabler-database-cog" size="90"></VIcon>
                      <span class="text-uppercase">En desarrollo</span>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>

              <VCol :md="tamanoComponente" cols="12">
                <VCard>
                  <VCardItem class="pb-sm-0">
                    <VCardTitle>Riego</VCardTitle>
                    <VCardSubtitle>Resumen general</VCardSubtitle>

                    <!-- <template #append>
                      <div class="mt-n4 me-n2">
                        <MoreBtn :menu-list="moreList" />
                      </div>
                    </template> -->
                  </VCardItem>

                  <VCardText class="mt-5">
                    <VSlideGroup v-model="tabGrupoRiego" show-arrows mandatory>
                      <VSlideGroupItem v-for="(items, index) in chartConfigRiego" :key="items.title"
                        v-slot="{ isSelected, toggle }" :value="index">

                        <div style="block-size: 94px; inline-size: 130px;"
                          :style="isSelected ? 'border-color:rgb(var(--v-theme-primary)) !important' : ''"
                          :class="isSelected ? 'border' : 'border border-dashed'"
                          class="d-flex flex-column justify-center align-center cursor-pointer rounded px-5 py-2 me-6"
                          @click="toggle">

                          <VAvatar rounded size="38" :color="isSelected ? 'primary' : 'secondary'" variant="tonal"
                            class="mb-2">
                            <VIcon :icon="items.icon" />
                          </VAvatar>

                          <p class="mb-0 font-weight-medium text-center">
                            {{ items.title }}
                          </p>

                        </div>
                      </VSlideGroupItem>
                    </VSlideGroup>

                    <div :key="tabGrupoSuelo" class="d-flex justify-center align-center flex-column container-no-data">
                      <VIcon icon="tabler-database-cog" size="90"></VIcon>
                      <span class="text-uppercase">En desarrollo</span>
                    </div>
                  </VCardText>
                </VCard>
              </VCol>
            </VRow>

          </VWindowItem>
          <VWindowItem value="item-reco">

          </VWindowItem>
          <VWindowItem value="item-grafico">

          </VWindowItem>
        </VWindow>
      </section>
    </div>
  </div>
</template>
