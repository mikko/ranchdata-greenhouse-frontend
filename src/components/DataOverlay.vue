<template>
  <div class="overlay">
    <div class="data-row" roof>
      <div class="data-label">Kattoluukku</div>
      <div class="data-value">{{ roofText }}</div>
    </div>
    <div class="data-row indoor-temperature">
      <div class="data-label">Sisälämpötila</div>
      <div class="data-value">{{ indoorTemp }}&#176;C</div>
    </div>
    <div class="data-row outdoor-temperature">
      <div class="data-label">Ulkolämpötila</div>
      <div class="data-value">{{ outdoorTemp }}&#176;C</div>
    </div>
    <div class="data-row moisture">
      <div class="data-label">Ilmankosteus</div>
      <div class="data-value">{{ moisture }}%</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DataOverlay',
  props: {
    moisture: Number,
    indoorTemp: Number,
    outdoorTemp: Number,
    roofOpen: Boolean,
  },
  computed: {
    roofText: function() { return this.roofOpen ? 'Auki': 'Kiinni' },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .overlay {
    font-size: 1.2rem;
    color: white;
    text-align: center;
    display: flex;
    flex-direction: column;
    height: 80%;
  }
  .data-row {
    flex: 1;
    display: flex;
    flex-direction: column;
    margin: 8vh 0;
  }
  .data-label, .data-value {
    flex: 1;
  }

</style>
