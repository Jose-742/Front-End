<template>
     <div class="grid">
        <div class="col-12 lg:col-12 xl:col-12">
            <div class="card mb-0  align-items-center">
                <h1 for="chart">Gráfico de projetos</h1>
                <div class="card flex justify-content-center">
                     <Chart id="chart" type="doughnut" :data="chartData" :options="chartOptions" class="w-full md:w-30rem" />
                </div>
            </div>
        </div>
    </div>
</template>
<script setup>
//script pagina home
import { ref, onMounted } from "vue";
let projetos = []
let contAvaliado = 0;
let contEnviado = 0;
onMounted(() => {
    fetchProjetos();   
});
async function fetchProjetos() {
    const req = await fetch('https://jose2550.c41.integrator.host/backend/projeto');
    const data = await req.json();
    projetos = data;
    for(let i=0; i<projetos.length; i++){
        if(projetos[i].status === "AVALIADO"){contAvaliado++}else{contEnviado++}
    }
    chartData.value = setChartData();
  }
const chartData = ref();
const chartOptions = ref({
    cutout: '60%'
});
const setChartData = () => {
    const documentStyle = getComputedStyle(document.body);
    return {
        labels: ['Eviado', 'Avaliado'],
        datasets: [
            {
                data: [contEnviado,contAvaliado],
                backgroundColor: [documentStyle.getPropertyValue('--blue-500'), documentStyle.getPropertyValue('--green-500')],
                hoverBackgroundColor: [documentStyle.getPropertyValue('--blue-400'), documentStyle.getPropertyValue('--green-400')]
            }
        ]
    };
};
</script>