<template>
    <div class="layout">
        <!--编写Iview组件-->
        <br/>
        <!--控件栏-->
        <Row>
            <Col span="8">
                <Button type="info" icon="md-add" @click="addCard">新增</Button>
                <Button type="error" icon="md-trash" @click="deleteCard">删除</Button>
                <Button type="success" icon="md-arrow-round-back" @click="goBack">返回</Button>
            </Col>
        </Row>
        <br/>
        <!--数据展示表单-->
        <Row>
            <Col span="20">
                <Table border :columns="columns4" :data="data1" @on-selection-change="checkRow"></Table>
            </Col>
        </Row>
        <br/>
        <!-- 分页条-->
        <Row>
            <Col span="20" align="center">
                <Page :total="total" :current="current" :page-size="pageSize" align="center" @on-change="getpage"/>
            </Col>
        </Row>
        <!--新增弹框-->
        <Modal
                v-model="addModal"
                width="800"
                title="新增信用卡"
                @on-ok="add_ok"
                @on-cancel="add_cancel"
                :closable="false">
            <Form ref="addForm" :model="addFormData" :rules="addRule" :label-width="90">
                <row>
                    <Col span="11">
                        <FormItem label="发卡银行" prop="bank">
                            <Input v-model="addFormData.bank" placeholder="请输入发卡银行"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="信用额度" prop="creditLimit">
                            <Input v-model="addFormData.creditLimit" placeholder="请输入信用额度"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="逾期情况" prop="overdueCase">
                            <Select v-model="addFormData.overdueCase" placeholder="请选择逾期情况">
                                <Option :value=1>良好</Option>
                                <Option :value=2>两年以内少数逾期</Option>
                                <Option :value=3>五年以内少数逾期</Option>
                                <Option :value=4>两年以内严重逾期</Option>
                                <Option :value=5>五年以内严重逾期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="发卡日期" prop="publishCardDate">
                            <DatePicker type="date" v-model="addFormData.publishCardDate" placeholder="请选择发卡日期"></DatePicker>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="卡号" prop="cardNumber">
                            <Input v-model="addFormData.cardNumber" placeholder="请输入信用卡卡号"></Input>
                        </FormItem>
                    </Col>
                </row>
            </Form>
        </Modal>
        <!--修改弹框-->
        <Modal
                v-model="updateModal"
                title="更新信用卡"
                width="800"
                @on-ok="update_ok"
                @on-cancel="update_cancel"
                :closable="false">
            <Form ref="updateForm" :model="updateFormData" :rules="addRule" :label-width="90">
                <row>
                    <Col span="11">
                        <FormItem label="发卡银行" prop="bank">
                            <Input v-model="updateFormData.bank" placeholder="请输入发卡银行"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="信用额度" prop="creditLimit">
                            <Input v-model="updateFormData.creditLimit" placeholder="请输入信用额度"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="逾期情况" prop="overdueCase">
                            <Select v-model="updateFormData.overdueCase" placeholder="请选择逾期情况">
                                <Option :value=1>良好</Option>
                                <Option :value=2>两年以内少数逾期</Option>
                                <Option :value=3>五年以内少数逾期</Option>
                                <Option :value=4>两年以内严重逾期</Option>
                                <Option :value=5>五年以内严重逾期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="发卡日期" prop="publishCardDate">
                            <DatePicker type="date" v-model="updateFormData.publishCardDate" placeholder="请选择发卡日期"></DatePicker>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="卡号" prop="cardNumber">
                            <Input v-model="updateFormData.cardNumber" placeholder="请输入信用卡卡号"></Input>
                        </FormItem>
                    </Col>
                </row>
            </Form>
        </Modal>
    </div>
</template>

<script>
    // 编写vue.js代码
    import util from '../../libs/util';
    export default {
        data () {
            return {
                // 当前客户的id
                customerId: '',
                // 储存需要删除的信用卡的id
                ids: [],
                // 组件中使用的各种属性
                columns4: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        type: 'index',
                        title: '序号',
                        width: 70,
                        align: 'center'
                    },
                    {
                        title: '发卡银行',
                        key: 'bank',
                        align: 'center'
                    },
                    {
                        title: '信用额度',
                        key: 'creditLimit',
                        align: 'center'
                    },
                    {
                        title: '逾期情况',
                        key: 'overdueCase',
                        align: 'center'
                    },
                    {
                        title: '发卡日期',
                        key: 'publishCardDate',
                        align: 'center'
                    },
                    {
                        title: '卡号',
                        width: 130,
                        key: 'cardNumber',
                        align: 'center'
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 120,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'success',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.updateCard(params.index);
                                        }
                                    }
                                }, '更新')
                            ]);
                        }
                    }

                ],
                // 分页显示页面数据
                data1: [],
                // 新增弹框属性
                addModal: false,
                // 更新弹框属性
                updateModal: false,
                // 更新页面数据
                updateFormData: {
                    bank: '',
                    creditLimit: '',
                    overdueCase: '',
                    publishCardDate: '',
                    cardNumber: '',
                    customerId:'',
                },
                // 新增页面数据
                addFormData: {
                    bank: '',
                    creditLimit: '',
                    overdueCase: '',
                    publishCardDate: '',
                    cardNumber: '',
                    customerId:'',
                },
                // 新增页面校验规则/更新页面校验规则
                addRule: {
                    brand: [
                        {required: true, message: '发卡银行不能为空', trigger: 'blur'}
                    ]
                },
                // 分页对象数据
                total: 0,
                current: 1,
                pageSize: 5
            };
        },
        // 弹窗提交事件方法
        methods: {
            goBack () {
                this.$router.push({path: '/business/valid-customer'});
            },
            // 新增页面确认按钮点击方法
            add_ok(){
                this.addFormData.customerId= this.customerId;
                var params = this.addFormData;
                util.ajax({
                    url: '/card/addCard',
                    method: 'post',
                    data: params
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('新增成功');
                    };
                    this.getpage(this.current);
                    // 清空表单
                    this.$refs['adds'].resetFields();
                });
            },
            add_cancel () {
                this.$Message.info('取消新增');
            },
            // 更新页面确认按钮点击方法
            update_ok() {
                var params = this.updateFormData;
                util.ajax({
                    url: '/card/updateCard',
                    method: 'post',
                    data: params
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('更新成功');
                        this.getpage(this.current);
                    } else {
                        this.$Message.info('更新失败');
                    }
                });
            },
            update_cancel () {
                this.$Message.info('取消更新');
            },
            // 修改数据回显
            updateCard (index) {
                // index参数就是点击按钮所在的表格行索引
                this.updateModal = true;
                this.updateFormData = this.data1[index];
            },
            // 分页显示方法
            getpage (current) {
                var pageSize = this.pageSize;
                var id = this.customerId;
                util.ajax({
                    url: '/card/page/' + id + '/' + current + '/' + pageSize,
                    method: 'get'
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    this.data1 = result.data.list;
                    // 设置分页条数据属性
                    this.total = result.data.totalCount;
                    this.current = result.data.pageNo;
                    this.pageSize = result.data.pageSize;
                });
            },
            // 新增按钮方法
            addCard () {
                this.addModal = true;
            },
            // 获取需要冻结的信用卡集合
            checkRow (selecteds) {
                this.ids = [];
                for (var i = 0; i < selecteds.length; i++) {
                    this.ids.push(selecteds[i].id);
                }
            },
            // 批量删除
            deleteCard () {
                // 判断是否选中信用卡
                if (this.ids.length > 0) {
                    util.ajax({
                        url: '/card/deleteCard',
                        method: 'post',
                        data: this.ids
                    }).then((result) => {
                        // 请求成功后的操作
                        if (result.data === 'ok') {
                            this.$Message.info('删除成功');
                            this.getpage(this.current);
                        } else {
                            this.$Message.info('删除失败');
                        }
                    });
                } else {
                    this.$Message.info('请选择需要删除的信用卡');
                }
            }
        },
        mounted () {
            // 获取页面跳转传递的参数
            this.customerId = this.$route.params.id;
            // 页面初始化方法
            if (this.customerId === undefined || this.customerId === null) {
                this.$Message.error('请通过有效客户编辑弹窗，进行房产信息编辑');
                this.$router.push({path: '/business/valid-customer'});
            } else {
                // 页面初始化方法
                this.getpage(1);
            }
        }
    };
</script>

<style scoped>

</style>
