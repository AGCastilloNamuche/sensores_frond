<script setup>
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

const eventFechas = ref(1)
const tamanoComponente = ref(6)
const dataCrecimientoAcomulado = ref([])
const dataCrecimientoDiario = ref([])
const dataContraccionDiaria = ref([])

const tabGrupoPlanta = ref(0)
const tabGrupoFruto = ref(0)
const tabGrupoSuelo = ref(0)
const tabGrupoRiego = ref(0)
const refVueApexChart = ref()
const isActive = ref(true)

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
        dataCrecimientoDiario.value = [-0.002, 0.029, null, null, 0.009, 0.015, 0.013, 0.011, 0.011, 0.004, 0.018, 0.015, 0.013,
            0.013, 0.02, 0.015, 0.007, 0.013, 0.031, 0.007, 0.024, 0.026, 0, 0.018, 0.015, 0.015, 0.029, 0.018, 0.011, 0.026, -0.03]

        dataContraccionDiaria.value = [null, null, null, 0.26, 0.26, 0.029, 0.024, 0.026, 0.022, 0.026, 0.026, 0.026, 0.022,
            0.018, 0.035, 0.011, 0.022, 0.024, 0.018, 0.026, 0.026, 0.04, 0.04, 0.031, 0.024, 0.029, 0.026, 0.022, 0.024, 0.033]
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
                        if (val != null || val != undefined) {
                            return `${val} mm`
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
                                return `${value.toFixed(3)} mm`
                            } else {
                                return 'N/A'
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
</script>
<template>
    <div>
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
                                <h5 class="text-h3 font-weight-medium">{{
                                    dataCrecimientoAcomulado[dataCrecimientoAcomulado.length
                                        - 1]
                                }}mm</h5>
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

        <!--semana y mensual--->
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
    </div>
</template>