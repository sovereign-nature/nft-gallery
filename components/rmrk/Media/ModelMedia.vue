<template>
  <div class="view-model__wrapper">
    <model-viewer
      class="view-model__component"
      :src="src"
      auto-rotate
      camera-controls
      ar
      ar-modes="webxr scene-viewer quick-look"
      shadow-intensity="1"
    >
      <button
        id="ar-button"
        slot="ar-button"
      >
        Activate AR
      </button>
    </model-viewer>
  </div>
</template>

<script lang="ts" >
import { Component, Prop, Vue } from 'nuxt-property-decorator'
import '@google/model-viewer'

@Component({})
export default class ViewModel extends Vue {
  @Prop() public src!: string;

  // get src() {
  //   return 'https://kristina-simakova.github.io/ar-webview/assets/RocketShip_1393.gltf';  // }

  get poster() {
    return ''
  }
}
</script>

<style scoped>
.view-model__wrapper {
  height: 100%;
  width: 100%;
}

.view-model__component {
  height: 100%;
  width: 100%;
  min-width: 300px;
  min-height: 592px;
}
</style>
