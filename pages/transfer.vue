<template>
  <Transfer />
</template>

<script lang="ts" >
import { Component, Vue } from 'nuxt-property-decorator'
import Transfer from '@/components/transfer/Transfer.vue'

@Component({
  components: {
    Transfer
  }
})

export default class TransferPage extends Vue {
}
</script>
