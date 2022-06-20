<template>
  <div id="unfolded">
    <div class="overlay-container">
      <h4>Embedding an Unfolded Map using the Map SDK with Vue.js</h4>
      <div v-if="!isLoaded" id="loader"></div>
      <div v-if="isLoaded" id="button-container">
        <button
          class="btn btn-primary btn-lg btn-square"
          v-on:click="goTo('sf')"
        >
          SAN FRANCISCO
        </button>
        <button
          class="btn btn-primary btn-lg btn-square"
          v-on:click="goTo('la')"
        >
          LOS ANGELES
        </button>
        <button
          class="btn btn-primary btn-lg btn-square"
          v-on:click="goTo('ny')"
        >
          NEW YORK
        </button>
        <button
          class="btn btn-primary btn-lg btn-square"
          v-on:click="getLayers()"
        >
          GET LAYERS
        </button>
        <div id="sdk-layers" v-if="layers.length">
          <label class="toggle-label">Toggle layer visibility</label>
          <div class="checkbox-group" v-for="layer in layers">
            <label class="switch">
              <input
                type="checkbox"
                :id="layer.id"
                :checked="layer.isVisible"
                @change="setLayerVisibility(layer.id)"
              />
              <span class="slider round"></span>
            </label>
            <label class="switch-label">
              <div>{{ layer.label }}</div>
            </label>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'unfolded',
  data() {
    return {
      lights: true,
      unfoldedMap: null,
      isLoaded: false,
      layers: [],
    };
  },

  created() {
    this.unfoldedMap = new UnfoldedMap({
      mapUUID: '80c800cc-5805-4416-94a5-bd8105cab7f7',
      embed: true,
      width: '100%',
      height: '100%',
      onLoad: () => {
        console.log(
          '%cUnfoldedMapSDK: %cMap has loaded...',
          'color: violet;',
          'color: yellow;'
        );
        this.isLoaded = true;
      },
    });
  },

  methods: {
    getLayers: function () {
      this.unfoldedMap.getLayers().then((layers) => {
        console.log(layers);
        this.layers = layers;
      });
    },
    setLayerVisibility: function (id) {
      const index = this.layers.findIndex((layer) => layer.id === id),
        layer = this.layers[index],
        layerId = layer.id,
        isVisible = !layer.isVisible;
      this.unfoldedMap
        .setLayerVisibility(layerId, isVisible)
        .then((layer) => (this.layers[index] = layer));
    },
    goTo: function (location) {
      let viewStateConfig = {};
      switch (location) {
        case 'sf':
          viewStateConfig = {
            longitude: -122.4194,
            latitude: 37.7749,
            zoom: 6,
          };
          break;
        case 'la':
          viewStateConfig = {
            longitude: -118.243683,
            latitude: 34.052235,
            zoom: 6,
          };
          break;
        case 'ny':
          viewStateConfig = {
            longitude: -73.935242,
            latitude: 40.73061,
            zoom: 6,
          };
          break;
      }
      this._setViewState(viewStateConfig);
    },
    _setViewState: function (config) {
      this.unfoldedMap.setViewState(config).then((data) => console.log(data));
    },
  },
};
</script>
