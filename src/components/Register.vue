<template>
  <div style="width: 100%;height: 100%;">
    <mt-field label="帐号" placeholder="请输入帐号" v-model="form.account" :attr="{ maxlength: 20 }"></mt-field>
    <mt-field label="姓" placeholder="请输入姓" v-model="form.lastName" :attr="{ maxlength: 20 }"></mt-field>
    <mt-field label="名" placeholder="请输入名" v-model="form.firstName" :attr="{ maxlength: 20 }"></mt-field>
    <mt-radio title="性别" v-model="form.gender" :options="['男', '女', '中']"/>
    <mt-field label="密码" placeholder="请输入密码" v-model="form.password" :attr="{ maxlength: 20 }"></mt-field>
    <mt-button type="primary" @click="register">注册</mt-button>
  </div>
</template>

<script>
import axe from 'axe4js'
import { Toast, Indicator } from 'mint-ui'
export default {
  name: 'Register',
  data () {
    return {
      form: {
        account: '',
        password: '',
        gender: '男',
        lastName: '',
        firstName: ''
      },
      sheetVisible: false
    }
  },
  methods: {
    register () {
      if (this.form.account.trim() === '') {
        return Toast('请输入帐号！！！')
      }
      if (this.form.firstName.trim() === '') {
        return Toast('请输入名！！！')
      }
      if (this.form.lastName.trim() === '') {
        return Toast('请输入姓！！！')
      }
      if (this.form.password.trim() === '') {
        return Toast('请输入密码！！！')
      }
      Indicator.open({
        text: '注册中...',
        spinnerType: 'fading-circle'
      })
      setTimeout(() => {
        Indicator.close()
        Toast('注册成功！')
        setTimeout(() => {
          let data = axe.data.create()
          // model 类型效果展示。
          let userInfo = {
            account: this.form.account,
            level: 1,
            detailInfo: {
              firstName: this.form.firstName,
              lastName: this.form.lastName,
              gender: this.form.gender
            },
            tagList: []
          }
          data.setModel('userInfo', userInfo)
          // 共享数据分享数据
          axe.data.sharedData.setModel('userInfo', userInfo)
          // 事件通知，且分享数据
          data.setBoolean('login', true)
          axe.event.postEvent('LoginStatusChange', data)
          axe.event.postEvent('RegistAccountSuccess', data)
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
    }
  },
  beforeRouteEnter: (to, from, next) => {
    next(vm => {
      axe.setupWebViewJavascriptBridge((bridge) => {
        bridge.callHandler('set_title', '注册')
      })
    })
  }
}
</script>
