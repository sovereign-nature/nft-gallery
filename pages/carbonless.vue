<template>
  <Carbonless />
</template>

<script lang="ts" >
import { Component, Vue } from 'nuxt-property-decorator'
import Carbonless from '@/components/landing/Carbonless.vue'

@Component({
  components: {
    Carbonless
  }
})

export default class CarbonlessPage extends Vue {
}
</script>
