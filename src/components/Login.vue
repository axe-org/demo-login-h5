<template>
  <div style="width: 100%;height: 100%;">
    <mt-field label="帐号" placeholder="请输入帐号" v-model="form.account" :attr="{ maxlength: 20 }"></mt-field>
    <mt-field label="密码" placeholder="请输入密码" type="password" v-model="form.password" :attr="{ maxlength: 20 }"></mt-field>
    <mt-button type="primary" @click.native="logIn">登录</mt-button>
    <mt-button type="primary" @click.native="register" plain>注册帐号</mt-button>
  </div>
</template>

<script>
import axe from 'axe4js'
import { Toast, Indicator } from 'mint-ui'
export default {
  name: 'Login',
  data () {
    return {
      form: {
        account: '',
        password: ''
      }
    }
  },
  methods: {
    logIn () {
      if (this.form.account.trim() === '') {
        return Toast('请输入帐号！！！')
      }
      if (this.form.password.trim() === '') {
        return Toast('请输入密码！！！')
      }
      Indicator.open({
        text: '登录中...',
        spinnerType: 'fading-circle'
      })
      setTimeout(() => {
        Indicator.close()
        Toast('登录成功！')
        setTimeout(() => {
          let data = axe.data.create()
          // model 类型效果展示。
          let userInfo = {
            account: this.form.account,
            level: 11,
            detailInfo: {
              firstName: 'hello',
              lastName: 'world',
              gender: 'FTM'
            },
            tagList: ['a', 'b', 'c']
          }
          data.setModel('userInfo', userInfo)
          // 共享数据分享数据
          axe.data.sharedData.setModel('userInfo', userInfo)
          // 事件通知，且分享数据
          data.setBoolean('login', true)
          axe.event.postEvent('LoginStatusChange', data)
          // 回调的处理。 处理回调前，还是要先检测一下调用时是否设置了回调， 可以通过接口文档说明一个页面是否强制要求回调。
          axe.router.getRouteInfo((routerInfo) => {
            if (routerInfo.needCallback) {
              // 回调传递数据, 需要注意h5里面的回调，是默认关闭当前页面的，所以一定要完成其他处理后，再进行回调。
              axe.router.callback(data)
            } else {
              // 如果没有回调。 当前界面完成，也是要关闭页面的， 这时调用原生页面关闭接口。
              axe.setupWebViewJavascriptBridge((bridge) => {
                bridge.callHandler('close_page')
              })
            }
          })
        }, 1000)
      }, 2000)
    },
    register () {
      this.$router.push('/register')
    }
  },
  mounted () {
    axe.router.getRouteInfo((routerInfo) => {
      if (routerInfo.payload) {
        let account = routerInfo.payload.get('account')
        if (account) {
          this.form.account = account
        }
      }
    })
  },
  beforeRouteEnter: (to, from, next) => {
    next(vm => {
      axe.setupWebViewJavascriptBridge((bridge) => {
        bridge.callHandler('set_title', '登录')
      })
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
