<template>
    <div class="layout">
        <Row>
            <Col span="24">
                模块：<Input placeholder="请输入" style="width: 200px"/>
                <Button type="primary" shape="circle" icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br>
        <Row>
            <Col span="24">
                <Button type="info" icon="md-add" @click="addMethod">新建</Button>
                <Button type="success" icon="ios-build" @click="updateMethod">修改</Button>
                <Button type="error" icon="ios-trash" @click="deleteMethod">删除</Button>
            </Col>
        </Row>
        <br>
        <Row>
            <Col span="22">
                <Table border ref="selection" :columns="columns" :data="modules"
                       @on-selection-change=getSelections></Table>
            </Col>
        </Row>
        <br>
        <Row>
            <Col span="22" align="center">
                <Page :total="totalCount" :page-size="pageSize" :current="pageNo" show-total @on-change="changePage"/>
            </Col>
        </Row>

        <Modal
                v-model="addModule"
                title="新建模块"
                width="40%"
                @on-ok="addOk"
                @on-cancel="addCancel">
            <Row>
                <Form ref="addForm" :label-width="90" :model="addData">
                    <Row>
                        <Col span="20">
                            <FormItem label="父模块" prop="parentId">
                                <Select v-model="addData.parentId" placeholder="请选择父模块"  @on-change=" changeParent">
                                    <Option v-for="item in moduleList" :value="item.id">{{ item.name}}</Option>
                                </Select>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="16">
                            <FormItem label="模块编号" prop="id">
                                <Input placeholder="请输入模块名" v-model="addData.id" ></Input>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="16">
                            <FormItem label="模块名" prop="name">
                                <Input placeholder="请输入模块名" v-model="addData.name" ></Input>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="16">
                            <FormItem label="状态" prop="state">
                                <RadioGroup v-model="addData.state" >
                                    <Radio :label="0">启用</Radio>
                                    <Radio :label="1">停用</Radio>
                                </RadioGroup>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="24">
                            <FormItem label="链接" prop="url">
                                <Input placeholder="请输入链接" style="width: 300px" v-model="addData.url"></Input>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="24">
                            <FormItem label="说明" prop="remark">
                                <Input type="textarea" :rows="2" style="width: 300px"
                                       :autosize="{minRows: 2,maxRows: 5}"
                                       placeholder="请输入备注说明" v-model="addData.remark"></Input>
                            </FormItem>
                        </Col>
                    </Row>
                </Form>
            </Row>
        </Modal>

        <Modal
                v-model="updateModule"
                title="编辑模块"
                width="40%"
                @on-ok="updateOk"
                @on-cancel="updateCancel">
            <Row>
                <Form ref="updateForm" :label-width="90" :model="updateData">
                    <Row>
                        <Col span="20">
                            <FormItem label="父模块" prop="parentId">
                                <Select v-model="updateData.parentId" placeholder="请选择父模块"  @on-change=" updateChangeParent">
                                    <Option v-for="item in moduleList" :value="item.id">{{ item.name}}</Option>
                                </Select>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="16">
                            <FormItem label="模块编号" prop="id">
                                <Input placeholder="请输入模块名" v-model="updateData.id" style="width: 300px"></Input>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="16">
                            <FormItem label="模块名" prop="name">
                                <Input placeholder="请输入模块名" v-model="updateData.name" style="width: 300px"></Input>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="16">
                            <FormItem label="状态" prop="state">
                                <RadioGroup v-model="updateData.state"  >
                                    <Radio :label="0">启用</Radio>
                                    <Radio :label="1">停用</Radio>
                                </RadioGroup>
                            </FormItem>
                        </Col>
                        <Col span="24">
                            <FormItem label="链接" prop="url">
                                <Input placeholder="请输入链接" style="width: 300px" v-model="updateData.url"></Input>
                            </FormItem>
                        </Col>
                    </Row>
                    <Row>
                        <Col span="24">
                            <FormItem label="说明" prop="remark">
                                <Input type="textarea" :rows="2" style="width: 300px"
                                       :autosize="{minRows: 2,maxRows: 5}"
                                       placeholder="请输入备注说明" v-model="updateData.remark"></Input>
                            </FormItem>
                        </Col>
                    </Row>
                </Form>
            </Row>
        </Modal>
    </div>
</template>

<script>
    import utils from '../../libs/util';
    import Cookies from 'js-cookie';
    // 编写vue.js代码
    export default {
        data () {
            return {
                addData: {
                    id:null,
                    name: '',
                    icon: '',
                    url: '',
                    parentId: 0,
                    state: '',
                    remark: ''
                },
                updateData: {
                    id:null,
                    name: '',
                    icon: '',
                    url: '',
                    parentId: 0,
                    state: '',
                    remark: ''
                },
                state: 2,
                addModule: false,    //添加模块对话框
                updateModule: false, //修改模块对话框
                totalCount: 0,       //总数据数
                pageSize: 2,         //分页大小
                pageNo: 1,           //当前页码
                modules: [],    //分页数据集合
                moduleList: [], //父模块集合
                selections:[],  //已选择数据
                ids:[],         //已选数据的id集合
                columns: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '模块编号',
                        key: 'id',
                    },
                    {
                        title: '模块名',
                        key: 'name'
                    },
                    {
                        title: '链接',
                        key: 'url',
                    },
                    {
                        title: '父模块ID',
                        render (h,params){
                            if (params.row.parentId === null){return "无"}
                            else {
                                return h("div",+ params.row.parentId);
                            }
                        }
                    },
                    {
                        title: '状态',
                        key: 'state',
                        render: (h, params) => {
                            if (params.row.state === 0) {
                                return h('div', '可用');
                            } else {
                                return h('div', '不可用');
                            }
                        }
                    },
                    {
                        title: '说明',
                        key: 'remark',
                    },
                ]
            };
        },
        // 编写各种事件方法
        methods: {
            addMethod () {
                this.addModule = true;
                this.getModuleList();
                this.$refs.addForm.resetFields();
            },
            addOk () {
                this.addData.createBy = Cookies.set('user');
                utils.ajax({
                    url: '/module/add',
                    method: 'post',
                    data: this.addData
                }).then((resp) => {
                    if (resp.data === 'ok') {
                        this.$Message.success('添加成功');
                    } else {
                        this.$Message.error('添加失败！请重新输入');
                    }
                    this.$refs['addForm'].resetFields();
                    this.changePage(1);
                });
            },
            addCancel () {
                this.$refs['addForm'].resetFields();
                this.$Message.info('取消添加');
            },
            updateMethod () {
                if (this.ids.length !== 1) {
                    this.$Message.error('更新操作能且仅能选择一个部门');
                } else {
                    this.updateModule = true;
                    this.moduleList=this.getModuleList();
                    this.updateData = this.selections[0];
                }
            },
            updateOk () {
                this.updateData.updateBy = Cookies.get("user");
                utils.ajax({
                    url: '/module/update',
                    method: 'post',
                    data: this.updateData
                }).then((resp) => {
                    if (resp.data === 'ok') {
                        this.$Message.success('修改成功');
                    } else {
                        this.$Message.error('修改失败！请重新输入');
                    }
                    this.changePage(1);
                });
            },
            updateCancel () {
                this.$Message.info('取消更新');
            },
            deleteMethod () {
                if (this.selections.length === 0) {
                    this.$Message.error('至少选择一个模块！');
                } else {
                    utils.ajax({
                        url: '/module/delete',
                        method: 'post',
                        data: this.ids,
                    }).then((resp) => {
                        if (resp.data === 'ok') {
                            this.$Message.success('删除成功');
                        } else {
                            this.$Message.error('删除失败');
                        }
                        this.ids = [];
                        this.changePage(1);
                    });
                }
            },
            changePage (current) {
                utils.ajax({
                    url: '/module/showPage',
                    method: 'get',
                    params: {
                        pageSize: this.pageSize,
                        pageNo: current,
                    }
                }).then((resp) => {
                    this.modules = resp.data.list;
                    this.totalCount = resp.data.totalCount;
                    this.pageNo = current;
                    this.ids=[];
                });
            },
            getSelections (selections) {
                this.ids = [];
                this.selections = selections;
                for (let i = 0; i < this.selections.length; i++) {
                    this.ids.push(this.selections[i].id);
                }
            },
            getModuleList () {
                utils.ajax({
                    url: '/module/getModulesOneList',
                    method: 'get',
                }).then((resp) => {
                    this.moduleList = resp.data;
                });
            },
            changeParent(id){
                var checkedModule='';
                for (let i = 0; i < this.moduleList.length; i++) {
                    if (this.moduleList[i].id === id){
                        checkedModule = this.moduleList[i];
                        break;
                    }
                }
                this.addData.url = checkedModule.url;
            },
            updateChangeParent(id){
                for (let i = 0; i < this.moduleList.length; i++) {
                    if (this.moduleList[i].id === id){
                        var checkedModule = this.moduleList[i];
                        break;
                    }
                }
                this.updateData.url = checkedModule.url;
                console.log(this.updateData.url);
            },
        },
        // 编写页面加载时执行的代码
        mounted () {
            this.changePage(1);
        }
    };
</script>

<style scoped>

</style>
