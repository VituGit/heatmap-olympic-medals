<template>
  <div>
    <header>
      <h1>Medalhas Olimpicas Heatmap</h1>
        <h2><label for="medalType">Selecione o tipo de medalha</label></h2>
        <select id="medalType" v-model="selectedMedalType" class="select">
          <option value="total_medals">Total</option>
          <option value="gold_medals">🥇Ouro</option>
          <option value="silver_medals">🥈Prata</option>
          <option value="bronze_medals">🥉Bronze</option>
        </select>
    </header>
    <main>
      <div class="container">
        <div class="legend">
          <div class="labels">
            <div>80</div>
            <div>60</div>
            <div>40</div>
            <div>20</div>
            <div>0</div>
          </div>
          <div class="gradient"></div>
        </div>
        <div class="heatmap">
          <div class="cell" v-for="country in countries" :key="country.id"
            :style="getHeatmapStyle(country[selectedMedalType])">
            <div class="content">
              {{ country.id }}<br>{{ country[selectedMedalType] }}
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>


<script>
export default {
  data() {
    return {
      countries: [],
      svgWidth: 800,
      svgHeight: 600,
      rectSize: 80,
      selectedMedalType: 'total_medals'
    };
  },
  created() {
    this.fetchCountries();
  },
  methods: {
    async fetchCountries() {
      try {
        const response = await fetch('https://apis.codante.io/olympic-games/countries');
        const result = await response.json();
        this.countries = result.data;
      } catch (error) {
        console.error('Error fetching countries:', error);
      }
    },
    getHeatmapStyle(value) {
      const maxMedals = 90;
      const color = this.getColorForValue(value, maxMedals);
      return {
        backgroundColor: color,
        color: '#000',
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'center',
        textAlign: 'center',
        fontWeight: 'bold'
      };
    },
    getMedalCount(country) {
      switch (this.selectedMedalType) {
        case 'gold_medals':
          return country.gold_medals;
        case 'silver_medals':
          return country.silver_medals;
        case 'bronze_medals':
          return country.bronze_medals;
        default:
          return country.total_medals;
      }
    },
    getColorForValue(value, max) {
      const percentage = value / max;
      const r = Math.floor(255 * (1 - percentage));
      const g = Math.floor(255 * percentage);
      const b = 0;
      return `rgba(${r}, ${g}, ${b}, 0.6)`;
    }
  }
};
</script>

<style>
body {
  margin: 0;
  background-color: #b6b6b6;
}

header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background-color: #373738;
  color: white;
  padding: 4px 0;
}

main {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 80vh;
  margin-top: 100px;
}

h1, h2 {
  margin-left: 10px;
  color: white;
}

.select {
  width: 150px;
  padding: 5px;
  font-size: 16px;
  margin-left: 10px; 
  background-color: #8d8d8d;
}
.container {
  display: flex;
  align-items: flex-start;
  justify-content: center;
}

.heatmap {
  display: grid;
  grid-template-columns: repeat(10, 1fr);
  gap: 0px;
}

.cell {
  font-weight: bold;
  color: white;
  width: 80px;
  height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  border: 1px solid #b6b6b6;
}

.legend {
  display: flex;
  align-items: center;
  margin-left: 10px;
}

.gradient {
  width: 20px;
  height: 410px;
  background: linear-gradient(to top, rgba(252, 2, 0, 0.6), rgba(136, 119, 0, 0.6), rgba(34, 206, 0, 0.6), rgba(31, 223, 0, 0.6));
  margin-right: 4px;
}

.labels {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 400px;
  margin-right: 4px;
}

.labels div {
  text-align: center;
  font-size: 12px;
  color: #000;
}
</style>