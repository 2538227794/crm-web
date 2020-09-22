<template>
    <div class="layout">
        <p style="margin-top: 120px"></p>
        <Row>
            <Col span="16" offset="4">
                <a href="http://localhost:8088/crm/download/中泰和润批量导入客户模板.xls"
                   style="font-size: 16pt;font-weight:bolder ">
                    <Icon type="md-cloud-download" size="24"  style="margin-right: 6px"/>点击下载导入模板</a>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="16" offset="4">
                <Form ref="importForm" :model="data1" :label-width="100">
                    <Row>
                        <Col span="12">
                            <FormItem label="导入方式：" prop="importType">
                                <Select v-model="data1.importType" >
                                    <Option :value="1">自行分配</Option>
                                    <Option :value="9">放入公共池</Option>
                                </Select>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="12">
                            <FormItem label="名单名称：" prop="customerRosterName">
                                <Input placeholder="请输入导入的客户名单名称" v-model="data1.customerRosterName"/>
                            </FormItem>
                        </Col>
                    </Row>
                </Form>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="16" offset="4">
                <Upload type="drag"
                        :before-upload="handleUpload"
                        :on-success="handleSuccess"
                        :max-size="1024*10"
                        :data="data1"
                        :on-exceeded-size="handleMaxSize"
                        action="http://localhost:8088/crm/customer/importCustomer">
                    <div style="padding: 20px 0">
                        <Icon type="ios-cloud-upload" size="52" style="color: #3399ff"></Icon>
                        <p style="font-weight: bolder">请选择Excel文件导入，文件后缀xls或xlsx</p>
                    </div>
                </Upload>
            </Col>
        </Row>
    </div>
</template>

<script>
    import util from '@/libs/util';
    import Cookies from 'js-cookie';
    // 编写vue.js代码
    export default {
        data () {
            return {
                // 组件中使用的各种属性
                data1:{
                    customerRosterName:"",
                    username:Cookies.get("user"),
                    importType:1,
                },
            };
        },
        // 编写各种事件方法
        methods: {
            handleUpload (file) {
                if(this.data1.customerRosterName == ""){
                    this.$Message.error("客户名单名称不能为空");
                    return false;
                }
            },
            handleSuccess (res, file) {
                console.log(file);
                if(res.status){
                    this.$Message.success("上传导入成功");
                }else{
                    this.$Message.success(res.msg);
                }
            },
            handleFormatError (file) {
                this.$Notice.warning({
                    title: '文件格式不正确',
                    desc: '选择的 ' + file.name + '文件格式不正确，请选择xls或xlsx文件。'
                });
            },
            handleMaxSize (file) {
                this.$Notice.warning({
                    title: '超出文件大小限制',
                    desc: '选择的' + file.name + '文件太大, 最大不能超过10MB.'
                });
            },
        },
        // 编写页面加载时执行的代码
        mounted () {
        }
    };
</script>

<style scoped>

</style>
