<template>
    <div class="container">
        <!-- iview 全局提示组件 -->
        <i-message id="message"/>
        <!-- 加载中组件 -->
        <i-spin size="large" fix v-if="spinShow"></i-spin>
        <div class="login_box">
            <div class="login_logo">
                <p class="logo"><h3>万车达</h3></p>
            </div>
            <div class="login_form">
                <form action="javascript:;">
                    <div class="form-item form_username">
                        <i class="iconfont icon-user"></i>
                        <input v-model="form.username" class="form-input" type="text" @keyup.enter.native="login" placeholder="请输入用户名">
                    </div>
                    <div class="form-item form_password mt40">
                        <i class="iconfont icon-password"></i>
                        <input v-model="form.password" class="form-input" type="password"  @keyup.enter.native="login" placeholder="请输入密码">
                    </div>
                    <div class="form_btn mt60" hover-class="hoverClass">
                        <div @tap="login">登 录</div>
                    </div>
                    <div class="form_other">
                        <span class="text-left">立即注册</span>
                        <span class="text-right">忘记密码?</span>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import globe from '../../utils/globe'
import api from '../../api/api'
import CryptoJS from 'crypto-js/core'
import MD5 from 'crypto-js/md5'
export default {
    data () {
        return {
            spinShow: true,
            form: {
                username: '',
                password: ''
            }
        }
    },
    onLoad () {
        this.spinShow = false
    },
    methods: {
        // 登录
        login(){
            const that = this
            const username = this.form.username
            const password = this.form.password
            const hashPwd = CryptoJS.MD5(password).toString()
            if(!username){
                globe.message('请输入用户名','warning')
            }else if(!password){
                globe.message('请输入密码','warning')
            }else{
                this.spinShow = true
                this.$http.get(api.login, {username,password: hashPwd}).then(res => {
                    //console.log(res)
                    if(res.success){
                        globe.message(res.errorMsg,'success')
                        const userinfo = res.data.entity
                        this.$store.dispatch('setUserInfo', userinfo)
                        setTimeout(() => {
                            that.spinShow = false
                            wx.switchTab({
                                url: '../../pages/index/main'
                            })
                        }, 1000)
                    }else{
                        globe.message(res.errorMsg,'warning')
                        this.spinShow = false
                    }
                }).catch(err => {
                    globe.message('网络请求错误，请稍后重试!','error')
                })
            }
        }
    },
}
</script>

<style lang="scss" scoped>
    .hoverClass{
        background-color: $--link-hover-color;
        box-shadow:0 0 10rpx $--link-hover-color;
    }
    .form-item{
        display: flex;
        height: 84rpx;
        line-height: 84rpx;
        border-bottom: 4rpx solid $--color-text-placeholder;
    }
    .iconfont{
        font-size: 60rpx;
        color: $--color-text-placeholder;
    }
    .form-input{
        flex-grow: 1;
        align-self: center;
        margin: 30rpx 0;
        font-size: 32rpx;
    }
    .login_box{
        height: 100vh;
        width: 50vh;
        .login_logo{
            height: 200rpx;
            line-height: 200rpx;
            text-align: center;
            font-family: Arial,Helvetica,sans-serif;
            font-size: 60rpx;
            color: $--color-primary;
        }
        .login_form{
            width: 100%;
            .form_btn{
                width: 100%;
                height: 80rpx;
                line-height: 80rpx;
                border-radius: 12rpx;
                text-align: center;
                background-color: $--color-primary;
                box-shadow:0 0 10rpx $--color-primary;
                div{
                    color: $--color-white;
                    font-size: 36rpx;
                }
            }
            .form_btn:hover{
                background-color: $--link-hover-color;
                box-shadow:0 0 10rpx $--link-hover-color;
            }
            .form_other{
                display: flex;
                flex-direction: row;
                height: 100rpx;
                line-height: 100rpx;
                span{
                    display: block;
                    width: 50%;
                    font-size: 32rpx;
                    color: $--color-text-placeholder;
                }
            }
        }
    }
</style>
