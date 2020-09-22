<template>
    <div class="layout">
        <Row>
            <Col span="24">
                员工姓名：
                <Input v-model="empName" placeholder="请输入员工姓名" clearable style="width: 200px"/>
                <Button type="primary"  icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="24">
                <Button type="info" ghost  @click="openAddModal">新增</Button>
                <Button type="error" ghost @click="updateUsableStateOne">冻结</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="23">
                <Table border :columns="columns" :data="data1" @on-selection-change="selectedEmp"></Table>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="23" style="text-align: center">
                <Page :total="total" :current="current" :page-size="pageSize" @on-change="getPage"/>
            </Col>
        </Row>
        <!--新增对话框-->
        <Modal
                v-model="addModal"
                title="新增员工"
                width="70%"
                @on-ok="addOk"
                @on-cancel="addCancel">
            <Form ref="addForm" :model="addEmp" :rules="ruleValidate" :label-width="110">
                <Row>
                    <Col span="12">
                        <FormItem label="所属公司" prop="company.id">
                            <Select v-model="addEmp.company.id" placeholder="请选择员工所属公司" @on-change="getAddDeptList" >
                                <Option v-for="item in companyList" :value="item.id">{{ item.name}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="所属部门" prop="dept.id">
                            <Select v-model="addEmp.dept.id" placeholder="请选择员工所属部门" >
                                <Option v-for="item in deptList" :value="item.id" >{{ item.name}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="姓名" prop="name">
                            <Input v-model="addEmp.name" placeholder="请输入员工姓名"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="员工账户" prop="account">
                            <Input v-model="addEmp.account" placeholder="请输入员工账户"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="身份证" prop="idCard" >
                            <Input  v-model="addEmp.idCard" placeholder="请输入员工身份证号码"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="性别" prop="gender">
                            <RadioGroup v-model="addEmp.gender">
                                <Radio label="男">男</Radio>
                                <Radio label="女">女</Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="手机号码" prop="mobile">
                            <Input type="number"  v-model="addEmp.mobile"  placeholder="请输入员工手机号码"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="座机" prop="tel">
                            <Input v-model="addEmp.tel" placeholder="请输入员工工位座机号码"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="短号" prop="corent">
                            <Input v-model="addEmp.corent" placeholder="请输入员工短号"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="家庭住址" prop="address">
                            <Input v-model="addEmp.address" placeholder="请输入员工家庭住址"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="Date" prop="date">
                            <DatePicker type="date" placeholder="请选择员工入职时间" v-model="addEmp.entryDate"></DatePicker>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="E-mail" prop="email">
                            <Input v-model="addEmp.email" placeholder="Enter your e-mail"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="紧急联系人姓名" prop="contactName">
                            <Input v-model="addEmp.contactName" placeholder="请输入员工紧急联系人姓名"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="紧急联系人电话" prop="contactPhone">
                            <Input type="number" v-model="addEmp.contactPhone" placeholder="请输入员工紧急联系人电话"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="关系" prop="relation">
                            <Input v-model="addEmp.relation" placeholder="请输入员工与紧急联系人的关系"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="Desc" prop="remark">
                            <Input v-model="addEmp.remark" type="textarea"></Input>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <!--编辑对话框-->
        <Modal
                v-model="updateModal"
                title="员工信息编辑"
                width="70%"
                @on-ok="updateOk"
                @on-cancel="updateCancel">
            <Form ref="updateForm" :model="updateEmp" :rules="ruleValidate" :label-width="110">
                <Row>
                    <Col span="12">
                        <FormItem label="所属公司" prop="company.id">
                            <Select v-model="updateEmp.company.id" placeholder="请选择员工所属公司">
                                <Option v-for="item in companyList" :value="item.id">{{ item.name}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="所属部门" prop="dept.id">
                            <Select v-model="updateEmp.dept.id" placeholder="请选择员工所属部门">
                                <Option v-for="item in deptList" :value="item.id" >{{ item.name}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="姓名" prop="name">
                            <Input v-model="updateEmp.name" placeholder="请输入员工姓名"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="员工账户" prop="account">
                            <Input v-model="updateEmp.account" placeholder="请输入员工账户"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="工号" prop="jobNo">
                            <Input v-model="updateEmp.jobNo" placeholder="请输入员工工号"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="身份证" prop="idCard" >
                            <Input  v-model="updateEmp.idCard" placeholder="请输入员工身份证号码"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="状态" prop="state">
                            <RadioGroup v-model="updateEmp.state">
                                <Radio :label="0">在职</Radio>
                                <Radio :label="1">离职</Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="性别" prop="gender">
                            <RadioGroup v-model="updateEmp.gender">
                                <Radio label="男">男</Radio>
                                <Radio label="女">女</Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="手机号码" prop="mobile">
                            <Input type="number"  v-model="updateEmp.mobile"  placeholder="请输入员工手机号码"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="座机" prop="tel">
                            <Input v-model="updateEmp.tel" placeholder="请输入员工工位座机号码"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="短号" prop="corent">
                            <Input v-model="updateEmp.corent" placeholder="请输入员工短号"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="家庭住址" prop="address">
                            <Input v-model="updateEmp.address" placeholder="请输入员工家庭住址"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="Date" prop="date">
                            <DatePicker type="date" placeholder="请选择员工入职时间" v-model="updateEmp.entryDate"></DatePicker>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="E-mail" prop="email">
                            <Input v-model="updateEmp.email" placeholder="Enter your e-mail"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="紧急联系人姓名" prop="contactName">
                            <Input v-model="updateEmp.contactName" placeholder="请输入员工紧急联系人姓名"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="紧急联系人电话" prop="contactPhone">
                            <Input type="number" v-model="updateEmp.contactPhone" placeholder="请输入员工紧急联系人电话"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="12">
                        <FormItem label="关系" prop="relation">
                            <Input v-model="updateEmp.relation" placeholder="请输入员工与紧急联系人的关系"></Input>
                        </FormItem>
                    </Col>
                    <Col span="12">
                        <FormItem label="Desc" prop="remark">
                            <Input v-model="updateEmp.remark" type="textarea"></Input>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <!--分配角色-->
        <Modal
                v-model="roleAssignModal"
                title="员工角色分配"
                width="50%"
                @on-ok="roleAssignOk"
                @on-cancel="roleAssignCancel">
            <Form ref="assignRoleForm" :model="assignRole" :label-width="110">
                <Row>
                    <Col span="12">
                        <FormItem label="员工姓名"  prop="name">
                            <Input v-model="assignRole.name" disabled></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="24">
                        <FormItem label="角色">
                            <CheckboxGroup v-model="assignRole.ids" @on-change="selectRole">
                                <Checkbox v-for="item in roleList" :value="item.id" :label="item.id">{{item.remark}}</Checkbox>
                            </CheckboxGroup>
                        </FormItem>
                    </Col>

                </Row>
            </Form>
        </Modal>
    </div>
</template>

<script>
    //引入工具js
    import util from '../../libs/util';
    import Cookies from 'js-cookie';
    // 编写vue.js代码
    export default {
        data () {
            //身份证号码校验
            const idCardCheck = (rule, value, callback) => {
                let reg=/^[1-9]\d{5}(18|19|20|(3\d))\d{2}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;
                if (reg.test(value) == false) {
                    callback(new Error('请输入正确的身份证号码'));
                }
                callback();
            };
            //手机号校验
            const mobileCheck = (rule, value, callback) => {
                let reg=/^1(3|4|5|7|8)\d{9}$/;
                if (reg.test(value) == false) {
                    callback(new Error('请输入正确的手机号码'));
                }
                callback();
            };
            return {
                //搜索框关联数据
                empName: '',
                //分页数据
                total: 0,
                current: 1,
                pageSize: 2,
                //储存角色
                roleList:[],
                //存储公司
                companyList: [],
                //储存部门
                deptList:[],
                //分配员工对象
                assignRole:{
                    id:'',
                    name:'',
                    ids:[],
                },
                //新增员工对象
                addEmp: {
                    name: '',
                    mail: '',
                    usableState:'',
                    company: {
                        id:'',
                        name:''
                        },
                    dept: {
                        id:'',
                        name:''
                    },
                    idCard:'',
                    gender: '',
                    mobile:'',
                    tel:'',
                    corent:'',
                    address:'',
                    entryDate:'',
                    email:'',
                    contactName:'',
                    contactPhone:'',
                    relation:'',
                    remark: '',
                    mailState:'',
                },
                //编辑员工对象
                updateEmp: {
                    name: '',
                    state:'',
                    mail: '',
                    usableState:'',
                    mailState:'',
                    company: {
                        id:'',
                        name:''
                    },
                    dept: {
                        id:'',
                        name:''
                    },
                    idCard:'',
                    gender: '',
                    mobile:'',
                    tel:'',
                    corent:'',
                    address:'',
                    entryDate:'',
                    email:'',
                    contactName:'',
                    contactPhone:'',
                    relation:'',
                    remark: '',
                    jobNo:'',
                },
                //表单校验
                ruleValidate: {
                    //员工姓名
                    name: [
                        { required: true, message: '员工姓名不能为空', trigger: 'blur' }
                    ],
                    //员工账户
                    account: [
                        { required: true, message: '员工账户不能为空', trigger: 'blur' }
                    ],
                    //身份证号
                    idCard: [
                        {validator:idCardCheck, trigger: 'blur' }
                    ],
                    //手机号
                    mobile: [
                        {validator:mobileCheck, trigger: 'blur' }
                    ],
                    //紧急联系人电话
                    contactPhone: [
                        {validator:mobileCheck, trigger: 'blur' }
                    ],
                    //入职时间
                    entryDate: [
                        { required: true, type: 'date', message: '请选择入职时间', trigger: 'change' }
                    ],
                    //邮箱格式
                    email: [
                        { type: 'email', message: '请输入正确邮箱格式', trigger: 'blur' }
                    ],
                    //备注
                    desc: [
                        { type: 'string', trigger: 'blur' }
                    ]
                },
                //编辑对话框属性
                updateModal:false,
                //新增对话框默认属性
                addModal: false,
                //角色分配对话框默认属性
                roleAssignModal:false,
                //表格列属性
                columns: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        type: 'index',
                        title: '序号',
                    },
                    {
                        title: '公司',
                        key: 'company',
                        render: (h, params) => {
                            return h('div', params.row.company.name);
                        }
                    },
                    {
                        title: '部门',
                        key: 'dept',
                        render: (h, params) => {
                            return h('div', params.row.dept.name);
                        }
                    },
                    {
                        title: '姓名',
                        key: 'name'
                    },
                    {
                        title: '性别',
                        key: 'gender'
                    },
                    {
                        title: '手机',
                        key: 'mobile'
                    },
                    {
                        title: '状态',
                        key: 'state',
                        render: (h, params) => {
                            if (params.row.state === 0) {
                                return h('div', [
                                    h('strong', '在职')
                                ]);
                            } else {
                                return h('div', [
                                    h('strong', '离职')
                                ]);
                            }
                        }
                    },
                    {
                        title: '入职邮件',
                        render: (h, params) => {
                            if (params.row.mailState === 0) {
                                return h('div', [
                                    h('strong', '已发送')
                                ]);
                            } else if (params.row.mailState === 1){
                                return h('div', [
                                    h('strong', '未发送')
                                ]);
                            }else if (params.row.mailState === 2){
                                return h('div', [
                                    h('strong', '发送成功')
                                ]);
                            }else if (params.row.mailState === 3){
                                return h('div', [
                                    h('strong', '发送失败')
                                ]);
                            }
                        }
                    },
                    {
                        title: '操作',
                        key: 'operation',
                        width: 180,
                        align: 'center',
                        render: (h, params) => {
                            if(params.row.usableState==0){
                                return h('div', [
                                    h('Button', {
                                        props: {
                                            type: 'primary',
                                            size: 'small'
                                        },
                                        style: {
                                            marginRight: '10px'
                                        },
                                        on: {
                                            click: () => {
                                                this.openUpdateModal(params.index);
                                            }
                                        }
                                    }, '编辑'),
                                    h('Button', {
                                        props: {
                                            type: 'success',
                                            size: 'small'
                                        },
                                        on: {
                                            click: () => {
                                                this.openRoleAssignModal(params.index);

                                            }
                                        }
                                    }, '分配角色')
                                ]);
                            }else if(params.row.usableState==1){
                                return h('div', [
                                    h('Button', {
                                        props: {
                                            type: 'error',
                                            size: 'small'
                                        },
                                        style: {
                                            marginRight: '10px'
                                        },
                                        on: {
                                            click: () => {
                                                this.updateUsableStateTwo(params.row.id);
                                            }
                                        }
                                    }, '解冻'),
                                ]);
                            }
                        }
                    }
                ],
                //表格数据
                data1: [],
                //储存选择勾选对象的id
                ids:[],
            };
        },
        // 编写各种事件方法
        methods: {
            // 获取所有角色集合
            getRoleList () {
                util.ajax({
                    url: '/role/list',
                    method: 'get'
                }).then((result) => {
                    this.roleList = result.data;
                });
            },
            //打开角色分配对话框按钮
            openRoleAssignModal(index){
                this.roleAssignModal=true;
                this.assignRole.id=this.data1[index].id;
                this.assignRole.name=this.data1[index].name;
                this.assignRole.ids = [];//还原
                for (let i = 0; i < this.data1[index].roleList.length; i++) {
                    this.assignRole.ids.push(this.data1[index].roleList[i].id);
                }
                this.getRoleList();
            },
            //打开编辑会话框
            openUpdateModal(index){
                this.updateModal=true;
                var updateEmp=this.data1[index];
                this.getUpdateDeptList(updateEmp);

            },
            //选择复选框获得当前行对象的id
            selectedEmp(emps){
                this.ids=[];
                for (let i = 0; i < emps.length; i++) {
                    this.ids.push(emps[i].id);
                }
            },
            //冻结
            updateUsableStateOne(){
                if(this.ids.length>0){
                    util.ajax({
                        url:"/emp/updateUsableState/1",
                        method:"post",
                        data: this.ids,
                    }).then((resp)=>{
                        if(resp.data==="ok"){
                            this.$Message.info('冻结成功');
                            this.getPage(this.current)
                        }else{
                            this.$Message.info('冻结失败');
                        }
                    })
                }else {
                    this.$Message.info('请至少选择一项冻结');
                }
            },
            //解冻
            updateUsableStateTwo(id){
                this.ids=[],
                this.ids.push(id),
                    util.ajax({
                        url:"/emp/updateUsableState/0",
                        method:"post",
                        data: this.ids,
                    }).then((resp)=>{
                        //根据不同操作给出不同提示
                        if(resp.data==="ok"){
                            this.$Message.info('解冻成功');
                            this.getPage(this.current)
                        }else{
                            this.$Message.info('解冻成功');
                        }
                    })
            },
            //获取新增部门列表
            getAddDeptList(id){
                util.ajax({
                    url:'/dept/getByCompany/'+id,
                    method:'get',
                }).then((resp)=>{
                    this.deptList=resp.data;
                    this.updateEmp=updateEmp;
                });
            },
            //获取修改部门列表
            getUpdateDeptList(updateEmp){
                util.ajax({
                    url:'/dept/getByCompany/'+updateEmp.company.id,
                    method:'get',
                }).then((resp)=>{
                    this.deptList=resp.data;
                    this.updateEmp=updateEmp;
                });
            },
            //获取公司列表
            getCompanyList(){
                util.ajax({
                    url: '/company/getAll',
                    method: 'get',
                }).then((resp) => {
                    this.companyList = resp.data;
                });
            },
            //打开新增对话框事件
            openAddModal(){
                this.addModal= true;
            },
            //角色选择
            selectRole(Roles){
                this.selectedRoles=Roles;
            },
            //新增对话框确定按钮事件
            addOk () {
                var username = Cookies.get('user');
                this.addEmp.createBy = username;
                this.$refs['addForm'].validate((valid) => {
                    if (valid) {
                        util.ajax({
                            url: '/emp/add',
                            method: 'post',
                            data: this.addEmp,
                        }).then((result) => {
                            if (result.data === 'ok') {
                                this.$Message.info('新增成功');
                            }else{
                                this.$Message.info('新增失败');
                            }
                            //刷新页面
                            this.getPage(this.current);
                            // 清空表单
                            //this.$refs['addForm'].resetFields();
                        });
                    } else {
                        this.$Message.info('表单校验失败');
                        // 清空表单
                        //this.$refs['addForm'].resetFields();
                    }
                });
            },
            //新增对话框取消按钮事件
            addCancel () {
                this.$Message.info('取消新增');
                this.$refs['addForm'].resetFields();
            },
            //编辑对话框确定按钮事件
            updateOk () {
                var username = Cookies.get('user');
                this.addEmp.updateBy = username;
                this.$refs['addForm'].validate((valid) => {
                    if (valid) {
                        util.ajax({
                            url: '/emp/update',
                            method: 'post',
                            data: this.updateEmp,
                        }).then((result) => {
                            if (result.data === 'ok') {
                                this.$Message.info('修改成功');
                            } else {
                                this.$Message.info('修改失败');
                            }
                            //刷新页面
                            this.getPage(this.current);
                            // 清空表单
                            this.$refs['addForm'].resetFields();
                        });
                    } else {
                        this.$Message.info('表单校验失败');
                        // 清空表单
                        this.$refs['addForm'].resetFields();
                    }
                });
            },
            //编辑对话框取消按钮事件
            updateCancel () {
                this.$Message.info('取消修改');
            },
            //角色分配对话框确定按钮事件
            roleAssignOk () {
                util.ajax({
                    url:"/emp/assginRole/"+this.assignRole.id,
                    method:'post',
                    data:this.assignRole.ids
                }).then((resp)=>{
                    if (resp.data=="ok"){
                        this.$Message.info('角色分配成功');
                        this.getPage(this.current);
                    } else {
                        this.$Message.info('角色分配失败');
                    }
                })
            },
            //角色分配对话框取消按钮事件
            roleAssignCancel () {
                this.$Message.info('取消角色分配');
            },
            //获取每页对象
            getPage (current) {
                util.ajax({
                    url: '/emp/getPages',
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
                    this.ids=[];
                });
            }
        },
        // 编写页面加载时执行的代码
        mounted () {
            this.getPage(1);
            this.getCompanyList();
        }
    };
</script>

<style scoped>

</style>
