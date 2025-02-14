<template>
  <div
    class="
      is-flex
      is-flex-direction-column
      min-h-full
    "
  >
    <Navbar v-if="isNavbarVisible" />
    <main class="is-flex-grow-1 mt-6">
      <router-view />
    </main>
    <Footer />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
import { NotificationProgrammatic as Notification } from 'buefy'
import { cryptoWaitReady } from '@polkadot/util-crypto'
import keyring from '@polkadot/ui-keyring'
import isShareMode from '@/utils/isShareMode'
import correctFormat from '@/utils/ss58Format'
import checkIndexer from '@/queries/checkIndexer.graphql'

@Component<Dashboard>({
  metaInfo() {
    return {
      title: 'KodaDot - Kusama NFT Market Explorer',
      titleTemplate: '%s | Low Carbon NFTs',
      meta: [
        { property: 'og:type', content: 'website' },
        // { property: 'og:url', content: 'https://nft.kodadot.xyz'},
        { property: 'og:locale', content: 'en_US' },
        { property: 'twitter:card', content: 'summary_large_image' },
        { property: 'twitter:site', content: '@KodaDot' }
      ]
    }
  },
})
export default class Dashboard extends Vue {
  get chainProperties() {
    return this.$store.getters.getChainProperties
  }

  get ss58Format(): number {
    return this.chainProperties?.ss58Format
  }

  public async loadKeyring(): Promise<void> {
    const isDevelopment = process.env.VUE_APP_KEYRING === 'true'
    keyring.loadAll({
      ss58Format: correctFormat(this.ss58Format),
      type: 'sr25519',
      isDevelopment
    })
  }

  public async mountWasmCrypto(): Promise<void> {
    await cryptoWaitReady()
    console.log('wasmCrypto loaded')
    this.loadKeyring()
    this.$store.commit('keyringLoaded')
  }

  public mounted(): void {
    this.mountWasmCrypto()
    this.fetchIndexer()
    this.checkVersion()
  }

  private async fetchIndexer() {
    try {
      const indexer = this.$apollo.query({
        query: checkIndexer
      })

      const {
        data: { _meta: data }
      } = await indexer

      console.log(
        `
    %cIndexer:
    Health: ${data?.indexerHealthy ? '❤️' : '💀'}
    Last: ${new Date(Number(data?.lastProcessedTimestamp))}
    `,
        'background: #222; color: #bada55; padding: 0.3em'
      )
      this.$store.dispatch('upateIndexerStatus', data)
    } catch (error) {
      const type = {
        indefinite: true,
        position: 'is-top-right',
        message: `
        <p class="title is-3">Indexer Error</p>
    <p class="subtitle">Indexer is not working properly
    <a target="_blank" rel="noopener noreferrer"  href="https://explorer.subquery.network/subquery/vikiival/magick">and you can check the status here</a></p>
    <p class="subtitle">If you think this should't happen, report us by
      <a target="_blank" rel="noopener noreferrer"  href="https://github.com/kodadot/nft-gallery/issues/new?assignees=&labels=bug&template=bug_report.md&title=">creating bug issue with steps to reproduce and screenshot.</a></p>
        `,
        type: 'is-danger',
        hasIcon: true
      }
      this.$buefy.snackbar.open(type as any)
      // this.$router.push({ name: 'error' });
      console.warn('Do something', error)
    }
  }

  private async checkVersion() {
    //@ts-ignore
    const workbox = await window.$workbox;
    if (workbox) {
      workbox.addEventListener('installed', (event) => {
        console.log(
          'App is being served from cache by a service worker.\n' +
            'For more details, visit https://pwa.nuxtjs.org/'
        )

        if (event.isUpdate) {
          console.log('New content is available; please refresh.')
          const notif = Notification.open({
            message: 'New version is ready. Close to upgrade.',
            queue: false,
            type: 'is-info is-dark',
            position: 'is-top-left',
            indefinite: true,
            hasIcon: true,
          })

          notif.$on('close', () => {
            window.sessionStorage.clear()
            window.localStorage.clear()
            window.location.reload()
          })
        }
      });
    }
  }

  get isNavbarVisible() {
    return !isShareMode
  }
}
</script>
