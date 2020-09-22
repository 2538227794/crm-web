<template>
    <div class="layout">
        <!--编写Iview组件-->
        <br/>
        <!--控件栏-->
        <Row>
            <Col span="8">
                <Button type="info" icon="md-add" @click="addCar">新增</Button>
                <Button type="error" icon="md-trash" @click="deleteCar">删除</Button>
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
                title="新增汽车"
                @on-ok="add_ok"
                @on-cancel="add_cancel"
                :closable="false">
            <Form ref="addForm" :model="addFormData" :rules="addRule" :label-width="90">
                <row>
                    <Col span="11">
                        <FormItem label="品牌" prop="brand">
                            <Input v-model="addFormData.brand" placeholder="请输入汽车品牌"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="购入价格" prop="price">
                            <Input v-model="addFormData.price" placeholder="请输入购买价格"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="购买时间" prop="buyDate">
                            <DatePicker type="date" v-model="addFormData.buyDate" placeholder="请输入购买时间"></DatePicker>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="贷款情况" prop="loanStatus">
                            <Input v-model="addFormData.loanStatus" placeholder="请输入贷款情况"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="月供" prop="refundMonth">
                            <Input v-model="addFormData.refundMonth" placeholder="请输入月供金额"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款时间" prop="refundDate">
                            <Input v-model="addFormData.refundDate" placeholder="请输入还款时间"></Input>
                        </FormItem>
                    </Col>
                </row>
            </Form>
        </Modal>
        <!--修改弹框-->
        <Modal
                v-model="updateModal"
                title="更新部门"
                width="800"
                @on-ok="update_ok"
                @on-cancel="update_cancel"
                :closable="false">
            <Form ref="updateForm" :model="updateFormData" :rules="addRule" :label-width="90">
                <row>
                    <Col span="11">
                        <FormItem label="品牌" prop="brand">
                            <Input v-model="updateFormData.brand" placeholder="请输入汽车品牌"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="购入价格" prop="price">
                            <Input v-model="updateFormData.price" placeholder="请输入购买价格"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="购买时间" prop="buyDate">
                            <Input v-model="updateFormData.buyDate" placeholder="请输入购买时间"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="贷款情况" prop="loanStatus">
                            <Input v-model="updateFormData.loanStatus" placeholder="请输入贷款情况"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="月供" prop="refundMonth">
                            <Input v-model="updateFormData.refundMonth" placeholder="请输入月供金额"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款时间" prop="refundDate">
                            <Input v-model="updateFormData.refundDate" placeholder="请输入还款时间"></Input>
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
                // 储存需要删除的汽车的id
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
                        title: '汽车品牌',
                        key: 'brand',
                        align: 'center'
                    },
                    {
                        title: '购入价格',
                        key: 'price',
                        align: 'center'
                    },
                    {
                        title: '购买时间',
                        key: 'buyDate',
                        align: 'center'
                    },
                    {
                        title: '贷款情况',
                        key: 'loanStatus',
                        align: 'center'
                    },
                    {
                        title: '月供',
                        key: 'refundMonth',
                        align: 'center'
                    },
                    {
                        title: '还款时间',
                        key: 'refundDate',
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
                                            this.updateCar(params.index);
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
                    brand: '',
                    price: '',
                    buyDate: '',
                    loanStatus: '',
                    refundMonth: '',
                    refundDate: ''
                },
                // 新增页面数据
                addFormData: {
                    brand: '',
                    price: '',
                    buyDate: '',
                    loanStatus: '',
                    refundMonth: '',
                    refundDate: ''
                },
                // 新增页面校验规则/更新页面校验规则
                addRule: {
                    brand: [
                        {required: true, message: '品牌不能为空', trigger: 'blur'}
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
            add_ok: function () {
                this.addFormData.customerId= this.customerId;
                var params = this.addFormData;
                util.ajax({
                    url: '/car/addCar',
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
            update_ok: function () {
                var params = this.updateFormData;
                util.ajax({
                    url: '/car/updateCar',
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
            // 分页显示页面更新按钮点击方法
            updateCar (index) {
                // index参数就是点击按钮所在的表格行索引
                this.updateModal = true;
                this.updateFormData = this.data1[index];
            },
            // 分页显示方法
            getpage (current) {
                var pageSize = this.pageSize;
                var id = this.customerId;
                util.ajax({
                    url: '/car/page/' + id + '/' + current + '/' + pageSize,
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
            addCar () {
                this.addModal = true;
            },
            // 获取需要冻结的汽车集合
            checkRow (selecteds) {
                this.ids = [];
                for (var i = 0; i < selecteds.length; i++) {
                    this.ids.push(selecteds[i].id);
                }
            },
            // 批量删除
            deleteCar () {
                // 判断是否选中汽车
                if (this.ids.length > 0) {
                    util.ajax({
                        url: '/car/deleteCar',
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
                    this.$Message.info('请选择需要删除的车辆');
                }
            }
        },
        mounted () {
            // 获取页面跳转传递的参数
            this.customerId = this.$route.params.id;
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
