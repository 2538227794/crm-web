<style lang="less">
    @import './login.less';
</style>

<template>
    <div class="login" @keydown.enter="handleSubmit">
        <div class="login-con">
            <Card :bordered="false">
                <p slot="title">
                    <Icon type="log-in"></Icon>
                    欢迎登录
                </p>
                <div class="form-con">
                    <Form ref="loginForm" :model="form" :rules="rules">
                        <FormItem prop="userName">
                            <Input v-model="form.userName" placeholder="请输入用户名">
                                <span slot="prepend">
                                    <Icon :size="16" type="person"></Icon>
                                </span>
                            </Input>
                        </FormItem>
                        <FormItem prop="password">
                            <Input type="password" v-model="form.password" placeholder="请输入密码">
                                <span slot="prepend">
                                    <Icon :size="14" type="locked"></Icon>
                                </span>
                            </Input>
                        </FormItem>
                        <FormItem>
                            <Button @click="handleSubmit" type="primary" long>登录</Button>
                        </FormItem>
                    </Form>
                    <p class="login-tip">输入任意用户名和密码即可</p>
                </div>
            </Card>
        </div>
    </div>
</template>

<script>
import Cookies from 'js-cookie';
import util from '../libs/util'
export default {
    data () {
        return {
            form: {
                userName: 'iview_admin',
                password: ''
            },
            rules: {
                userName: [
                    { required: true, message: '账号不能为空', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '密码不能为空', trigger: 'blur' }
                ]
            }
        };
    },
    methods: {
        handleSubmit () {
            this.$refs.loginForm.validate((valid) => {
                if (valid) {
                    Cookies.set('user', this.form.userName);
                    Cookies.set('password', this.form.password);

                    this.$store.commit('setAvator', 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3448484253,3685836170&fm=27&gp=0.jpg');
                    if (this.form.userName === 'iview_admin') {
                        Cookies.set('access', 0);
                    } else {
                        Cookies.set('access', 1);
                    }
                    this.$router.push({
                        name: 'home_index'
                    });
                }
                //权限验证登录
                // if (valid) {
                //     var qs = require('qs');
                //     util.ajax({
                //         url: "oauth/token",
                //         method: "post",
                //         data: qs.stringify({
                //             "username": this.form.userName,
                //             "password": this.form.password,
                //             "grant_type": 'password',
                //             "client_id": 'pass',
                //             "client_secret": '123',
                //             "scope": 'all',
                //         }),
                //     }).then((resp) => {//请求成功后端操作
                //         if(resp.data.access_token != null){
                //             this.$Message.info("登录成功");
                //             //将当前用户的信息以及token存入本地cookie中
                //             Cookies.set('user', this.form.userName);
                //             //Cookies.set('password', this.form.password);
                //             Cookies.set('token', resp.data.access_token);
                //             Cookies.set('access', 0);
                //             this.$store.commit('setAvator', 'https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=3448484253,3685836170&fm=27&gp=0.jpg');
                //             this.$router.push({
                //                 name: 'home_index'
                //             });
                //         }
                //     }).catch(resp => {
                //         this.$Message.error("账号密码错误");
                //     });
                // }
            });
        }
    }
};
</script>

<style>

</style>
