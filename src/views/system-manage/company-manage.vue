<template>
    <div class="layout">
        <Row>
            <Col span="24">
                公司名称： <Input v-model="companyName" placeholder="请输入公司名称" clearable style="width: 200px;margin-right:10px"/>
                <Button type="primary" icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br>
        <Row>
            <Col span="24">
                <Button type="success" icon="md-add" @click="openAddModel">新增</Button>
                <Button type="warning" icon="md-hammer" @click="openUpdateModel">修改</Button>
                <Button type="error" icon="md-trash" @click="deletecompanys">删除</Button>
            </Col>
        </Row>
        <br>
        <Row>
            <Col span="20">
                <Table border :columns="columns4" :data="data1" @on-selection-change="selectcompany"></Table>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="20" align="center">
                <Page :total="total" :current="current" :page-size="pageSize" @on-change="changePage"/>
            </Col>
        </Row>

        <Modal
                v-model="addModel"
                title="新增公司"
                @on-ok="addOk"
                @on-cancel="addCancel" width="50%">
            <Form ref="addForm" :model="company" :label-width="100">
                <Row>
                    <Col span="12">
                        <FormItem label="公司名称：" prop="name">
                            <Input v-model="company.name" placeholder="请输入公司名称"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="20">
                        <FormItem label="备注：" prop="remark">
                            <Input v-model="company.remark" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入备注说明"></Input>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <Modal
                v-model="updateModel"
                title="修改公司"
                @on-ok="updateOk"
                @on-cancel="updateCancel" width="50%">
            <Form ref="updateForm" :model="company" :label-width="100">
                <Row>
                    <Col span="12">
                        <FormItem label="公司名称：" prop="name">
                            <Input v-model="company.name" placeholder="请输入公司名称"></Input>
                        </FormItem>
                    </Col>
                </Row>

                <Row>
                    <Col span="12">
                        <FormItem label="公司状态：" prop="state">
                            <RadioGroup v-model="company.state" >
                                <Radio :label="0">可用</Radio>
                                <Radio :label="1">不可用</Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="20">
                        <FormItem label="备注：" prop="remark">
                            <Input v-model="company.remark" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入备注说明"></Input>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>


    </div>
</template>

<script>
    // 引入工具js
    import util from '@/libs/util';
    import Cookies from 'js-cookie';
    // 编写vue.js代码
    export default {
        data () {
            return {
                selectedcompanys:[],//存储勾选的公司
                company: {//用于增加
                    name: '',
                    remark: '',
                    state:0,
                    createBy:'',
                    updateBy:''
                },
                addModel: false,//控制对话框显示隐藏
                updateModel:false,
                //分页数据 --start
                total: 0,
                current: 1,
                pageSize: 2,
                //分页数据 --end
                companyName: '',//搜索框关联数据
                columns4: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '公司名称',
                        key: 'name'
                    },
                    {
                        title: '状态',
                        key: 'state',
                        render: (h, params) => {
                            if (params.row.state === 0) {
                                return h('div', [
                                    h('strong', '可用')
                                ]);
                            } else {
                                return h('div', [
                                    h('strong', '不可用')
                                ]);
                            }
                        }
                    }
                ],//表格标题
                data1: []//表格显示数据属性
            };
        },
        // 编写各种事件方法
        methods: {
            //新增按钮事件
            openAddModel () {
                //打开对话框
                this.addModel = true;
            },
            //修改按钮事件
            openUpdateModel () {
                let companys = this.selectedcompanys;
                if(this.selectedcompanys.length!=1){
                    this.$Message.error("修改时能且只能勾选一条数据");
                }else{
                    this.company = companys[0];
                    //打开对话框
                    this.updateModel = true;
                }
            },
            //分页加载事件
            changePage (pageNo) {
                util.ajax({
                    url: '/company/page',
                    method: 'get',
                    params: {
                        pageSize: this.pageSize,
                        current: pageNo
                    }
                }).then((resp) => {
                    console.log(resp);
                    //获取响应的数据对象：resp.data
                    //给页码组件属性赋值
                    this.data1 = resp.data.list;
                    this.total = resp.data.totalCount;
                    this.current = resp.data.pageNo;
                    this.pageSize = resp.data.pageSize;
                    //重置复选框数据
                    this.selectedcompanys=[];
                });
            },
            // 点击新增对话框确定按钮事件
            addOk () {
                // 设置创建者
                var username = Cookies.get('user');
                this.company.createBy = username;
                console.log(this.company);
                util.ajax({
                    // 工具中配置了baseUrl，所有url只需要写后面的相对路径
                    url: '/company/add',
                    method: 'post',
                    data: this.company
                }).then((resp) => {
                    if(resp.data == 'ok'){
                        this.$Message.success('新增成功');
                        //刷新页面
                        this.changePage(this.current);
                    }else{
                        this.$Message.error('新增失败');
                    }
                    this.$refs['addForm'].resetFields();
                });
            },
            // 点击更新对话框确定按钮事件
            updateOk () {
                // 设置创建者
                var username = Cookies.get('user');
                this.company.updateBy = username;
                util.ajax({
                    // 工具中配置了baseUrl，所有url只需要写后面的相对路径
                    url: '/company/update',
                    method: 'post',
                    data: this.company
                }).then((resp) => {
                    if(resp.data == 'ok'){
                        this.$Message.success('更新成功');
                        //刷新页面
                        this.changePage(this.current);
                    }else{
                        this.$Message.error('更新失败');
                    }
                    this.$refs['updateForm'].resetFields();
                });
            },
            // 点击新增对话框取消按钮事件
            addCancel () {
                this.$refs['addForm'].resetFields();
                this.$Message.info("取消新增")
            },
            // 点击更新对话框取消按钮事件
            updateCancel () {
                this.$Message.info("取消更新")
            },
            //清空表单方法
            handleReset (name) {
                this.$refs[name].resetFields();
            },
            //删除公司方法
            deletecompanys(){
                var username = Cookies.get('user');
                this.company.updateBy = username;
                //判断当前勾选项
                var companys = this.selectedcompanys;
                if(companys == null || companys.length < 1){
                    this.$Message.error('删除必须至少选中一项');
                }else{
                    //获取勾选对象id
                    var ids = [];
                    for (let i = 0; i < companys.length; i++) {
                        ids.push(companys[i].id);
                    }
                    //调用axios请求后端删除
                    util.ajax({
                        url: '/company/delete/'+username,
                        method: 'post',
                        data:ids
                    }).then((resp) => {
                        if(resp.data == 'ok'){
                            this.$Message.success('删除成功');
                            //刷新页面
                            this.changePage(this.current);
                            //TODO: 清空表单
                        }else{
                            this.$Message.error('删除失败');
                        }
                    });
                }
            },
            //表格勾选事件方法
            selectcompany(selection){
                //selection:勾选的公司对象数组
                this.selectedcompanys = selection;
            }
        },
        // 编写页面加载时执行的代码
        mounted () {
            this.changePage(1);
        }
    };
</script>

<style scoped>

</style>
