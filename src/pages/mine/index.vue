<template>
  <div class="container">
    <div class="tab_content">

      <!-- iview 全局提示组件 -->
      <i-message id="message"/>
      <!-- 加载中组件 -->
      <i-spin size="large" fix v-if="spinShow"></i-spin>

      <!-- 弹窗 -->
      <i-modal title="温馨提示" :visible="modalVisible" @ok="handleOk" @cancel="handleClose">
        <view>{{modalMessage}}</view>
      </i-modal>

      <div class="transition tab_mine">
        <div>
          <i-cell-group>
            <i-cell title="个人信息" is-link link-type="navigateTo" url="/pages/userInfo/main"></i-cell>
            <i-cell title="修改密码" is-link link-type="navigateTo" url="/pages/changePwd/main"></i-cell>
            <i-cell i-class="logout" title="退出当前账号" @tap="logout"></i-cell>
          </i-cell-group>
        </div>
      </div>
    </div>
    <!-- <div class="tab_bar">
      <i-tab-bar fixed :current="current" color="#f759ab" @change="handleChangeTabBar($event)">
        <i-tab-bar-item key="index" icon="financial_fill" current-icon="financial_fill" title="接车"></i-tab-bar-item>
        <i-tab-bar-item key="order" icon="createtask" current-icon="createtask_fill" title="工单"></i-tab-bar-item>
        <i-tab-bar-item key="workplace" icon="shop_fill" current-icon="shop_fill" title="工作台"></i-tab-bar-item>
        <i-tab-bar-item key="mine" icon="mine" current-icon="mine_fill" title="我的"></i-tab-bar-item>
      </i-tab-bar>
    </div> -->
  </div>
</template>

<script>
  import globe from '../../utils/globe'
  import {isSuperAdmin} from '../../utils/permission'
  import api from '../../api/api'
  import { mapGetters } from 'vuex'
  export default {
    data () {
      return {
        current: 'mine',
        modalSuccess: true,
        modalVisible: false,
        modalMessage: '',
        spinShow: true
      }
    },
    mounted() {
      this.spinShow = false
    },
    computed: {
      ...mapGetters([
        'userInfo'
      ])
    },
    // 上拉加载，拉到底部触发
    onReachBottom() {
      // 到这底部在这里需要做什么事情
    },
    methods: {
      // 登出
      logout() {
        const that = this
        //删除cookie并跳到登录页
        const params = {company: this.userInfo.company, username: this.userInfo.username}
        //请求后端
        this.spinShow = true
        this.$http.get(api.loginout,params,true).then((response) => {
          if(response.success){
            //成功后删除cookie
            this.$store.dispatch('setUserInfo', {}).then(() => {
              globe.message(response.errorMsg,'success')
              //跳转到登录页
              setTimeout(() => {
                that.spinShow = false
                wx.redirectTo({
                    url: '../../pages/login/main'
                })
              }, 1000)
            })
          }else{
            globe.message(response.errorMsg,'warning')
            that.spinShow = false
          }
        }, (response) => {
          //Error
        })
      },
      // 切换tab-bar
      handleChangeTabBar (data) {
        this.current = data.mp.detail.key
        wx.switchTab({
          url: '../../pages/'+this.current+'/main'
        })
      },
      // 弹窗确定按钮
      handleOk(){
        if(this.modalSuccess){
          this.modalVisible = false
        }else{
          this.modalVisible = false
        }
      },
      // 弹窗关闭按钮
      handleClose(){
        if(this.modalSuccess){
          this.modalVisible = false
        }else{
          this.modalVisible = false
        }
      }
    }
  }
</script>

<style>

.i-noticebar-content-wrap{
  text-align: center!important;
}
.grid-item{
  height: 185rpx;
}
.grid-label{
  margin-top: 0!important;
}
.logout{
  text-align: center;
  color: brown;
}
.navigator-hover {
  color:#777;
}
.placeholder{
  color: #777;
}
</style>

<style lang="scss" scoped>
.transition{
  transition: $--fade-transition;
}
.tab_content{
  width: 100%;
}
.tab_bar{
  width: 100vw;
}
.tab_list{
  background-color: $--background-color-base;
  padding: 10px;
  margin-bottom: 14px;
  .pay-btn{
    padding: 3px 6px;
    border: 1px solid $--color-text-secondary;
    color: $--color-success;
  }
  ul {
    li{
      height: 34px;
      line-height: 34px;
      color: $--color-text-primary;
      .label{
        display: inline-block;
        text-align: right;
        margin-right: 6px;
        color: $--color-text-secondary;
      }
      .value{
        display: inline-block;
        color: $--color-text-secondary;
      }
    }
    .line{
      height: 3rpx;
      background-color: $--color-text-secondary;
    }
  }
}
.menu_list{
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
  align-content: center;
  width: 90%;
  margin: 20px auto;
  background-color: #f2f4fb;
  border: 1px solid #f2f4fb;
  border-radius: 8px;
  height: 100%;
  .menu{
    padding: 10px;
    text-align: center;
    .iconfont{
      font-size: 32px;
      color: #98ccd3;
    }
  }
}
.item{
  width: 90%;
  margin: 6px auto;
  height: 28px;
  line-height: 28px;
  border: 1px solid #ccc;
  overflow: hidden;
  padding: 3px 6px;
  .label{
    float: left;
    width: 30%;
    display: block;
  }
  .input{
    display: inline-block;
    width: 70%;
    height: 100%;
    line-height: 30px;
  }
}
.button_wrap{
  width: 90%;
  margin: 20px auto;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  .item_button{
    background: #ccc;
    border-radius: 50%;
    height: 50px;
    width: 50px;
    line-height: 50px;
    cursor: pointer;
    text-align: center;
  }
}
.productForm{
  width: 100%;
  margin: 0 auto;
  overflow: hidden;
  .productForm_button{
    text-align: center;
  }
  .repairList{
    overflow: hidden;
    margin: 0 auto;
    margin-top: 10px;
    .item_button{
      overflow: hidden;
      height: 50px;
      line-height: 50px;
      width: 100%;
      text-align: center;
      display: flex;
      justify-content: space-between;
      align-items: center;
      .button{
        width: 80px;
        margin: 0 auto;
        height: 30px;
        line-height: 30px;
        border: 1px solid #d2d2d2;
        border-radius: 6px;
      }
    }
  }
}
.billingForm{
  width: 100%;
  margin: 0 auto;
  overflow: hidden;
  .billingWrap{
    margin: 0 auto;
    .billing_card{
      margin-top: 10px;
    }
  }
}
.step_button{
  width: 100%;
  margin: 0 auto;
  text-align: center;
}

</style>
