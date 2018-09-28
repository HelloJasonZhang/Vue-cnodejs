<template>
    <div class="login-page">
        <nv-head page-type="登录">
        </nv-head>
        <section class="page-body">
  <!--           <div class="label">
                <input class="txt" type="text" placeholder="Access Token" v-model="token" maxlength="36">
            </div> -->
            <div class="label">
                <input class="txt" type="text" placeholder="用户名" v-model="loginname" maxlength="36">
            </div>
            <div class="label">
                <input class="txt" type="password" placeholder="密码" v-model="pass" maxlength="36">
            </div>                        
            <div class="label">
                <a class="button" @click="logon">登录</a>
            </div>
        </section>
    </div>
</template>

<script>
    import $ from 'webpack-zepto';
    import nvHead from '../components/header.vue';
    import utils from '../libs/utils.js';
    export default {
        data() {
            return {
                token: '',
                loginname: '',
                pass: ''
            };
        },
        methods: {
            logon() {
                if (this.loginname === '') {
                    this.$alert('用户名错误,不能为空');
                    return false;
                }

                if (this.pass === '') {
                    this.$alert('密码错误,不能为空');
                    return false;
                }
                $.ajax({
                    type: 'POST',
                    url: utils.BE_URL + '/login?loginname=' + this.loginname + '&pass=' + this.pass,
                    data: {
                        accesstoken: this.token
                    },
                    dataType: 'json',
                    success: (res) => {
                        let user = {
                            loginname: res.loginname,
                            avatar_url: res.avatar_url,
                            userId: res.id,
                            token: res.token
                        };
                        window.window.sessionStorage.user = JSON.stringify(user);
                        this.$store.dispatch('setUserInfo', user);
                        let redirect = decodeURIComponent(this.$route.query.redirect || '/');
                        this.$router.push({
                            path: redirect
                        });
                    },
                    error: (res) => {
                        var error = JSON.parse(res.responseText);
                        this.$alert(error.error_msg);
                    }
                });
            }
        },
        components: {
            nvHead
        }
    };
</script>
<style lang="scss">
    .page-body {
        padding: 50px 15px;
        min-height: 400px;
        background-color: #fff;
        .label {
            display: inline-block;
            width: 100%;
            margin-top: 15px;
            position: relative;
            left: 0;
            top: 0;
            .txt {
                padding: 12px 0;
                border: none;
                border-bottom: 1px solid #4fc08d;
                background-color: transparent;
                width: 100%;
                font-size: 14px;
                color: #313131;
            }
            .button {
                display: inline-block;
                width: 99%;
                height: 42px;
                line-height: 42px;
                border-radius: 3px;
                color: #fff;
                font-size: 16px;
                background-color: #4fc08d;
                border: none;
                border-bottom: 2px solid #3aa373;
                text-align: center;
                vertical-align: middle;
            }
            .file {
                position: absolute;
                top: 0;
                left: 0;
                height: 42px;
                width: 48%;
                outline: medium none;
                opacity: 0;
            }
        }
    }
</style>
