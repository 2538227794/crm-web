<template>
    <div class="layout">
        <Row>
            <Col span="24">
                角色：
                <Input v-model="roleName" placeholder="请输入角色" clearable style="width: 200px"/>
                <Button type="primary" shape="circle" icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="24">
                <Button type="primary" icon="md-add" @click="openAddModal">新增</Button>
                <Button type="success" icon="md-build" @click="openUpdateModal">修改</Button>
                <Button type="error" icon="md-trash" @click="deleteRoles">删除</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="20">
                <Table border ref="selection" :columns="columns4" :data="data1" @on-selection-change="selectRole"></Table>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="20" style="text-align: center">
                <Page :total="total" :current="current" :page-size="pageSize" @on-change="getPage"/>
            </Col>
        </Row>
        <Modal
                v-model="addModal"
                title="新增角色"
                @on-ok="addOk"
                width="30%"
                @on-cancel="addCancel">
            <Form ref="addForm" :model="addRole" :rules="ruleValidate" :label-width="80">
                <Row>
                    <Col span="16">
                        <FormItem label="角色名字" prop="name">
                            <Input v-model="addRole.name" placeholder="请输入角色名"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="20">
                        <FormItem label="备注" prop="remark">
                            <Input v-model="addRole.remark" placeholder="请输入备注"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="16">
                        <FormItem label="所属公司" prop="company.id">
                            <Select v-model="addRole.company.id" placeholder="请选择员工所属公司" >
                                <Option v-for="item in companyList" :value="item.id">{{ item.name}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="24">
                        <FormItem label="模块组" prop="moduleList">
                            <ul id="treeDemoAdd" class="ztree"></ul>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <Modal
                v-model="updateModal"
                title="修改角色"
                width="30%"
                @on-ok="updateOk"
                @on-cancel="updateCancel">
            <Form ref="updateForm" :model="updateRole" :rules="ruleValidate" :label-width="80">
                <Row>
                    <Col span="16">
                        <FormItem label="角色名字" prop="name">
                            <Input v-model="updateRole.name" placeholder="请输入角色名"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="20">
                        <FormItem label="备注" prop="remark">
                            <Input v-model="updateRole.remark" placeholder="请输入备注"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="16">
                        <FormItem label="所属公司" prop="company.id">
                            <Select v-model="updateRole.company.id" placeholder="请选择员工所属公司" >
                                <Option v-for="item in companyList" :value="item.id">{{ item.name}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="24">
                        <FormItem label="模块组" prop="moduleList">
                            <ul id="treeDemoUpdate" class="ztree"></ul>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
    </div>
</template>

<script>
    //导入ztree插件
    import '@ztree/ztree_v3/js/jquery-1.4.4.min'
    import '@ztree/ztree_v3/js/jquery.ztree.core.min'
    import '@ztree/ztree_v3/js/jquery.ztree.excheck'
    import '@ztree/ztree_v3/css/zTreeStyle/zTreeStyle.css'
    import util from '../../libs/util';
    import Cookies from 'js-cookie';
    // 编写vue.js代码
    export default {
        data () {
            return {
                zTreeObjAdd:{},
                zTreeObjUpdate:{},
                // zTree 的参数配置
                settingAdd : {
                    check: {
                        enable: true
                    },
                    data: {
                        simpleData: {
                            enable: true
                        }
                    }
                },
                settingUpdate : {
                    check: {
                        enable: true
                    },
                    data: {
                        simpleData: {
                            enable: true
                        }
                    }
                },
                // zTree 的数据属性
                zNodes : [],
                zNodesAdd : [],
                zNodesUpdate : [],
                //存储公司
                companyList: [],
                //新增角色对象
                addRole: {
                    id:'',
                    name: '',
                    moduleList:[
                        {
                            id:'',
                        }
                    ],
                    remark:'',
                    company:{
                        id:'',
                        name:''
                    },
                    createBy:'',
                },
                //修改角色对象
                updateRole: {
                    id:'',
                    name: '',
                    moduleList:[
                        {
                            id:'',
                        }
                    ],
                    remark:'',
                    company:{
                        id:'',
                        name:''
                    },
                    createBy:'',
                },
                //表单校验
                ruleValidate: {
                    name: [
                        { required: true, message: 'The name cannot be empty', trigger: 'blur' }
                    ],
                },
                //修改对话框默认属性
                updateModal:false,
                //新增对话框默认属性
                addModal:false,
                columns4: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '角色名字',
                        key: 'name'
                    },
                    {
                        title: '备注',
                        key: 'remark'
                    },
                    {
                        title: '公司',
                        render: (h, params) => {
                            return h('div', params.row.company.name)
                        }
                    }
                ],
                data1: [],
                //搜索框关联数据
                roleName: '',
                //分页数据
                total: 0,
                current: 1,
                pageSize: 2,
                //存储勾选的角色
                selectedRoles: [],
                //储存分割后的模块id
                module:[],
            };
        },
        // 编写各种事件方法
        methods: {
            //打开修改窗口
            openUpdateModal () {
                //判断是否只有一个复选框被选中
                if (this.selectedRoles.length === 1) {
                    this.updateModal = true;
                    //返回当前选中对象数据
                    this.updateRole = this.selectedRoles[0];
                    this.getCompanyList();
                    this.getZtreeNodeList();
                } else {
                    this.$Message.info('修改必须选中并且只选择一项');
                }
            },
            //删除按钮事件
            deleteRoles(){
                if (this.selectedRoles.length >= 1) {
                    var ids = [];
                    for (let i = 0; i < this.selectedRoles.length; i++) {
                        ids.push(this.selectedRoles[i].id);
                    }
                    util.ajax({
                        url: '/role/delete',
                        method: 'post',
                        data: ids,
                    }).then((resp) => {
                        if (resp.data == 'ok') {
                            this.$Message.info('删除成功');
                            this.getPage(this.current);
                        } else {
                            this.$Message.info('删除失败');
                        }
                    });
                } else {
                    this.$Message.info('删除必须至少选择一项');
                }
            },
            //表格勾选
            selectRole(selection){
                this.selectedRoles = selection;
            },
            //打开新增窗口
            openAddModal(){
                this.addModal=true;
                this.getCompanyList();
                this.getZtreeNodeList();
            },
            //获取ztree节点集合(也就是获取所有模块菜单)
            getZtreeNodeList(){
                util.ajax({
                    url:'/module/ztreeNodes',
                    method:'get',
                }).then((result)=>{//请求成功后的操作
                    this.zNodes = result.data;
                    for (let i = 0; i < this.zNodes.length; i++) {
                        for (let j = 0; j < this.updateRole.moduleList.length; j++) {
                            if(this.zNodes[i].id==this.updateRole.moduleList[j].id){
                                this.zNodes[i].checked=true;
                            }
                        }
                    }
                    this.zNodesAdd=this.zNodes;
                    this.zNodesUpdate=this.zNodes;
                    //初始化加载新增ztree
                    this.zTreeObjAdd = $.fn.zTree.init($("#treeDemoAdd"), this.settingAdd, this.zNodesAdd);
                    //初始化加载修改ztree
                    this.zTreeObjUpdate = $.fn.zTree.init($("#treeDemoUpdate"), this.settingUpdate, this.zNodesUpdate);
                })
            },
            //获取公司列表
            getCompanyList(){
                util.ajax({
                    url: '/company/getAll',
                    method: 'get',
                }).then((resp) => {
                    this.companyList= resp.data;
                });
            },
            //获取每页对象
            getPage (current) {
                util.ajax({
                    url: '/role/getPage',
                    method: 'get',
                    params: {
                        pageSize: this.pageSize,
                        current: current
                    }
                }).then((resp) => {
                    //获取响应的数据对象
                    //给分页组件属性赋值
                    this.data1 = resp.data.list;
                    this.pageSize = resp.data.pageSize;
                    this.total = resp.data.totalCount;
                    this.current = resp.data.pageNo;
                    //重置复选框数据
                    this.selectedRoles=[];
                });
            },
            //新增对话框确定按钮
            addOk () {
                //获取新增角色对象的属性值
                var username = Cookies.get('user');
                var role=this.addRole;
                role.createBy = username;
                //获取勾选的所有ztree节点
                var nodes = this.zTreeObjAdd.getCheckedNodes(true);
                for (let i = 0; i < nodes.length; i++) {
                    var ztreeVo = nodes[i];
                    console.log(ztreeVo.id);
                    role.moduleList[i]={id:ztreeVo.id};
                }
                this.$refs['addForm'].validate((valid) => {
                    if (valid) {
                        util.ajax({
                            url:"/role/add",
                            method:'post',
                            data:role
                        }).then((resp)=>{
                            if(resp.data=="ok"){
                                this.$Message.info('新增成功');
                            }else {
                                this.$Message.info('新增失败');
                            }
                            //刷新页面
                            this.getPage(this.current);
                            // 清空表单
                            this.$refs['addForm'].resetFields();
                        })
                    } else {
                        this.$Message.info('表单校验失败');
                        // 清空表单
                        this.$refs['addForm'].resetFields();
                    }
                });
            },
            //新增对话框取消按钮
            addCancel () {
                this.$Message.info('取消新增');
                this.module=[];
            },
            //修改对话框确定按钮
            updateOk () {
                var username = Cookies.get('user');
                var role=this.updateRole;
                role.updateBy = username;
                //获取勾选的所有ztree节点
                var nodes = this.zTreeObjUpdate.getCheckedNodes(true);
                for (let i = 0; i < nodes.length; i++) {
                    var ztreeVo = nodes[i];
                    role.moduleList[i]={id:ztreeVo.id};
                }
                this.$refs['updateForm'].validate((valid) => {
                    if (valid) {
                        util.ajax({
                            url:"/role/update",
                            method:'post',
                            data:role
                        }).then((resp)=>{
                            if(resp.data=="ok"){
                                this.$Message.info('修改成功');
                            }else {
                                this.$Message.info('修改失败');
                            }
                            //刷新页面
                            this.getPage(this.current);
                            // 清空表单
                            this.module=[];
                            this.$refs['updateForm'].resetFields();
                        })
                    } else {
                        this.$Message.info('表单校验失败');
                        // 清空表单
                        this.module=[];
                        this.$refs['updateForm'].resetFields();
                    }
                });
            },
            //修改对话框取消按钮
            updateCancel () {
                this.module=[];
                this.$Message.info('取消修改');
            }
        },
        // 编写页面加载时执行的代码
        mounted () {
            this.getPage(1);
        }
    };
</script>

<style scoped>

</style>
