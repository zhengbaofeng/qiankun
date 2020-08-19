<template>
  <div id="container">
    <header>
      <nav>
        <ol>
          <li v-for="app of apps" :key="app.name">
            <a @click="goto(app.name, `${app.activeRule}`)">{{app.name}}</a>
          </li>
        </ol>
      </nav>
    </header>
    <div id="appContainer" />
  </div>
</template>

<script>
import { registerMicroApps, runAfterFirstMounted, setDefaultMountApp, start } from 'qiankun'

export default {
  name: 'master',
  data () {
    return {
      apps: [
        { name: 'foo-app', entry: '//localhost:8081', container: '#appContainer', activeRule: '/foo-app' }
      ]
    }
  },
  created () {
    if (!window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__) {
      this.initQiankun()
    } else {
      // fix hot-reload
      location.reload()
    }
  },
  methods: {
    goto (title, href) {
      window.history.pushState({}, title, href)
    },
    initQiankun () {
      registerMicroApps(
        this.apps,
        {
          beforeLoad: [
            app => {
              // eslint-disable-next-line no-console
              console.log('挂载前', app)
            }
          ],
          beforeMount: [
            app => {
              // eslint-disable-next-line no-console
              console.log('挂载后', app)
            }
          ],
          afterUnmount: [
            app => {
              // eslint-disable-next-line no-console
              console.log('卸载后', app)
            }
          ]
        }
      )
      //  设置主程序默认加载子程序
      setDefaultMountApp('/foo-app')

      runAfterFirstMounted(() => {
        // eslint-disable-next-line no-console
        console.info('first app mounted')
      })

      start({ prefetch: true })
    }
  }
}
</script>

<style scoped>
  a {
    color: #42b983;
    cursor: pointer;
  }
  .appContainer {
    margin-top: 50px;
  }
</style>
