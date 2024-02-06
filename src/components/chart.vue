

<template>
    <div>
      <div>
        <apexchart width="800" type="bar" :options="options" :series="currentSeries"></apexchart>
      </div>
      <div class="flex justify-center mt-4">
        <button @click="previousGroup" :disabled="currentPage === 0" class="mr-2 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">Anterior</button>
        <button @click="nextGroup" :disabled="currentPage === totalGroups - 1" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">Próximo</button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import VueApexCharts from 'vue3-apexcharts';
  
  const pageSize = 10;
  const currentPage = ref(0);
  const totalGroups = ref(0);
  const allSeriesData = ref([]);
  
  const options = ref({
    chart: {
      id: 'vuechart-example',
      type: 'bar'
    },
    xaxis: {
      categories: [], 
      
    },
    yaxis: {
      show: false, 
      labels: {
        formatter: function(value) {
          return value.toLocaleString('pt-BR');
        }
      }
    },
    plotOptions: {
      bar: {
        horizontal: true, 
        barHeight: '80%' 
      },
    },
    tooltip: {
      y: {
        formatter: function(value) {
          return value.toLocaleString('pt-BR');
        }
      }
    },
    grid: {
      row: {
        colors: ['#f3f4f5', 'transparent'],
        opacity: 0.5 
      }
    }
  });
  
  const currentSeries = ref([]);
  
  const fetchData = async () => {
    try {
      const response = await fetch('https://covid19-brazil-api.now.sh/api/report/v1/countries');
      const data = await response.json();
      const countries = data.data;
  
      allSeriesData.value = countries.map(country => ({
        name: country.country,
        data: [country.confirmed.toLocaleString("pt-Br")]
      }));
  
      totalGroups.value = Math.ceil(allSeriesData.value.length / pageSize);
      updateCurrentSeries();
    } catch (error) {
      console.error('Erro ao carregar dados da API:', error);
    }
  };
  
  const updateCurrentSeries = () => {
    const startIndex = currentPage.value * pageSize;
    const endIndex = startIndex + pageSize;
    currentSeries.value = allSeriesData.value.slice(startIndex, endIndex);
  };
  
  const previousGroup = () => {
    currentPage.value--;
    updateCurrentSeries();
  };
  
  const nextGroup = () => {
    currentPage.value++;
    updateCurrentSeries();
  };
  
  onMounted(fetchData);
  </script>
  
  <style scoped>
  .overflow-scroll {
    overflow-x: auto;
  }
  
  .flex {
    display: flex;
  }
  
  .justify-center {
    justify-content: center;
  }
  
  .mr-2 {
    margin-right: 0.5rem;
  }
  
  .px-4 {
    padding-left: 1rem;
    padding-right: 1rem;
  }
  
  .py-2 {
    padding-top: 0.5rem;
    padding-bottom: 0.5rem;
  }
  
  .bg-blue-500 {
    background-color: #3b82f6;
  }
  
  .text-white {
    color: #fff;
  }
  
  .rounded {
    border-radius: 0.375rem;
  }
  
  .hover\:bg-blue-600:hover {
    background-color: #2563eb;
  }
  
  .mt-4 {
    margin-top: 1rem;
  }
  </style>
  
  <script>
  import VueApexCharts from 'vue3-apexcharts';
  
  export default {
    components: {
      apexchart: VueApexCharts
    },
    setup() {
      return {
        currentPage,
        totalGroups,
        currentSeries,
        previousGroup,
        nextGroup
      };
    }
  }
  </script>
  

  
 
 
