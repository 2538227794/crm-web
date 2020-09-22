<template>
    <div class="layout">
        <Row>
            <Col span="24">
                部门名称：
                <Input v-model="deptName" placeholder="请输入部门" clearable style="width: 200px"/>
                <Button type="primary" shape="circle" icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="24">
                <Button type="primary" icon="md-add" @click="openAddModal">新增</Button>
                <Button type="success" icon="md-build" @click="openUpdateModal">修改</Button>
                <Button type="error" icon="md-trash" @click="deleteDepts">删除</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="20">
                <Table border ref="selection" :columns="columns4" :data="data1"
                       @on-selection-change="selectDept"></Table>
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
                title="新增部门"
                @on-ok="addOk"
                @on-cancel="addCancel"
                width="40%">
            <Form ref="addForm" :model="addDept" :label-width="100">
                <Row>
                    <Col span="14">
                        <FormItem label="部门名称：" prop="name">
                            <Input v-model="addDept.name" placeholder="请输入部门名称"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="14">
                        <FormItem label="所属公司：" prop="company.id">
                            <Select v-model="addDept.company.id">
                                <Option v-for="item in companyList" :value="item.id" :key="item.id">{{ item.name }}
                                </Option>
                            </Select>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="22">
                        <FormItem label="备注：" prop="remark">
                            <Input v-model="addDept.remark" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入备注说明"></Input>
                        </FormItem>
                    </Col>
                </Row>
            </Form>
        </Modal>
        <Modal
                v-model="updateModal"
                title="修改部门"
                @on-ok="updateOk"
                @on-cancel="updateCancel"
                width="40%">
            <Form ref="updateForm" :model="updateDept" :label-width="100">
                <Row>
                    <Col span="14">
                        <FormItem label="部门名称：" prop="name">
                            <Input v-model="updateDept.name" placeholder="请输入部门名称"></Input>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="14">
                        <FormItem label="所属公司：" prop="company.id">
                            <Select v-model="updateDept.company.id" >
                                <Option v-for="item in companyList" :value="item.id" :key="item.id">{{ item.name }}
                                </Option>
                            </Select>
                        </FormItem>
                    </Col>
                </Row>
                <Row>
                    <Col span="22">
                        <FormItem label="备注：" prop="remark">
                            <Input v-model="updateDept.remark" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入备注说明"></Input>
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
            return {
                //修改对话框默认属性false
                updateModal: false,
                //存储公司
                companyList: [],
                //新增部门对象
                addDept: {
                    name: '',
                    remark: '',
                    company: {
                        id: '',
                        name: ''
                    },
                    createBy: ''
                },
                //修改部门对象
                updateDept: {
                    name: '',
                    remark: '',
                    company: {
                        id: '',
                        name: ''
                    },
                    createBy: ''
                },
                //新增对话框默认属性false
                addModal: false,
                //分页数据
                total: 0,
                current: 1,
                pageSize: 2,
                //搜索框关联数据
                deptName: '',
                //表格标题
                columns4: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '部门名称',
                        key: 'name'
                    },
                    {
                        title: '所属公司',
                        key: 'company',
                        render: (h, params) => {
                            return h('div', params.row.company.name);
                        }
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
                                    h('strong', '可用')
                                ]);
                            }
                        }
                    }
                ],
                //表格数据
                data1: [],
                //存储勾选的部门
                selectedDepts: []
            };
        },
        // 编写各种事件方法
        methods: {
            //修改确定
            updateOk() {
                //部门名称不能为空
                if(this.updateDept.name!=null&&this.updateDept.name!=""){
                    this.updateDept.updateBy = Cookies.get('user');
                    util.ajax({
                        url: 'dept/update',
                        method: 'post',
                        data: this.updateDept
                    }).then((resp) => {
                        if (resp.data == 'ok') {
                            this.$Message.info('修改成功');
                            this.getPage(this.current);
                            this.handleReset('updateForm');
                        } else {
                            this.$Message.info('修改失败');
                        }
                    });
                }else {
                    this.$Message.info('修改失败部门名称不能为空');
                }
            },
            //修改取消
            updateCancel () {
                this.$Message.info('取消修改');
            },
            //打开修改窗口
            openUpdateModal () {
                //判断是否只有一个复选框被选中
                if (this.selectedDepts.length === 1) {
                    this.updateModal = true;
                    //返回当前选中对象数据
                    this.updateDept = this.selectedDepts[0];
                    this.getCompanyList();
                } else {
                    this.$Message.info('修改必须选中并且只选择一项');
                }
            },
            //表格勾选
            selectDept (selection) {
                this.selectedDepts = selection;
            },
            //删除部门
            deleteDepts () {
                if (this.selectedDepts.length >= 1) {
                    var ids = [];
                    for (let i = 0; i < this.selectedDepts.length; i++) {
                        ids.push(this.selectedDepts[i].id);
                    }
                    util.ajax({
                        url: '/dept/delete',
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
            //清空表单
            handleReset (name) {
                this.$refs[name].resetFields();
            },
            //打开新增窗口
            openAddModal () {
                this.addModal = true;
                this.getCompanyList();
            },
            //获取公司对象
            getCompanyList () {
                util.ajax({
                    url: '/company/getAll',
                    method: 'get',
                }).then((resp) => {
                    this.companyList = resp.data;
                });
            },
            //新增确定
            addOk () {
                //部门名称不能为空
                if(this.addDept.name!=null&&this.addDept.name!=""){
                    this.addDept.createBy = Cookies.get('user');
                    util.ajax({
                        url: 'dept/add',
                        method: 'post',
                        data: this.addDept
                    }).then((resp) => {
                        if (resp.data == 'ok') {
                            this.$Message.info('新增成功');
                            this.getPage(this.current);
                            this.handleReset('addForm');
                        } else {
                            this.$Message.info('新增失败');
                        }
                    });
                }else {
                    this.$Message.info('新增失败部门名称不能为空');
                }
            },
            //新增取消
            addCancel () {
                this.$Message.info('取消新增');
            },
            //获取每页对象
            getPage (current) {
                util.ajax({
                    url: '/dept/getPages',
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
                    this.selectedDepts=[];
                });
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
