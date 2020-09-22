<template>
    <div class="layout">
        <!--编写Iview组件-->
        <br/>
        <Row>
            <Col span="24">
                部门名称： <Input placeholder="请输入部门名称" clearable style="width: 200px;margin-right:10px"/>
                <Button type="primary" icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br/>
        <!--数据展示表单-->
        <Row>
            <Col span="20">
                <Table border :columns="columns4" :data="data1"></Table>
            </Col>
        </Row>
        <br/>
        <!-- 分页条-->
        <Row>
            <Col span="20" align="center">
                <Page :total="total" :current="current" :page-size="pageSize" align="center" @on-change="getpage"/>
            </Col>
        </Row>
        <!--修改弹框-->
        <Modal
                v-model="updateModal"
                title="更新客户信息"
                width="800"
                @on-ok="update_ok"
                @on-cancel="update_cancel"
                :closable="false">
            <Form ref="updateForm" :model="updateFormData" :rules="addRule" :label-width="110">
                <row>
                    <Col span="11">
                        <FormItem label="客户姓名" prop="name">
                            <Input v-model="updateFormData.name" placeholder="请输入客户姓名"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="客户性别" prop="gender">
                            <RadioGroup v-model="updateFormData.gender">
                                <Radio :label=0>男</Radio>
                                <Radio :label=1>女</Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="客户年龄" prop="age">
                            <Input v-model="updateFormData.age" placeholder="请输入客户年龄"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="客户电话" prop="phone">
                            <Input v-model="updateFormData.phone" placeholder="请输入客户电话"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="客户来源" prop="customerSource">
                            <Select v-model="updateFormData.customerSource.id" placeholder="请选择客户来源">
                                <Option v-for="item in customerSourceList" :value="item.id">{{item.sourceName}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="贷款类型" prop="loanType">
                            <Select v-model="updateFormData.loanType" placeholder="请选择贷款类型">
                                <Option value="信用贷款">信用贷款</Option>
                                <Option value="抵押贷款">抵押贷款</Option>
                                <Option value="商业贷款">商业贷款</Option>
                                <Option value="房贷">房贷</Option>
                                <Option value="车贷">车贷</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="电话备注" prop="phoneRemark">
                            <Input v-model="updateFormData.phoneRemark" placeholder="请输入客户电话备注信息"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="其它信息" prop="otherInfo">
                            <Input v-model="updateFormData.otherInfo" placeholder="请输入其它备注信息"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="每月收入" prop="monthIncome">
                            <Select v-model="updateFormData.monthIncome" placeholder="请选择客户来源">
                                <Option value="3000以下">3000以下</Option>
                                <Option value="3000-5000">3000-5000</Option>
                                <Option value="5000-8000">5000-8000</Option>
                                <Option value="8000-15000">8000-15000</Option>
                                <Option value="15000以上">15000以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="收入体现" prop="embodiment">
                            <Select v-model="updateFormData.embodiment" placeholder="请选择收入体现">
                                <Option value="现金">现金</Option>
                                <Option value="转账">转账</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="社保状态" prop="socialInsurance">
                            <Select v-model="updateFormData.socialInsurance" placeholder="请选择客户来源">
                                <Option value="个人缴纳">个人缴纳</Option>
                                <Option value="公司缴纳一年以内">公司缴纳一年以内</Option>
                                <Option value="公司缴纳三年以内">公司缴纳三年以内</Option>
                                <Option value="公司缴纳三年以上">公司缴纳三年以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="个人征信" prop="credit">
                            <Select v-model="updateFormData.credit" placeholder="请选择收入体现">
                                <Option value="良好">良好</Option>
                                <Option value="两年以内少数逾期">两年以内少数逾期</Option>
                                <Option value="五年以内少数逾期">五年以内少数逾期</Option>
                                <Option value="两年以内严重逾期">两年以内严重逾期</Option>
                                <Option value="五年以内严重逾期">五年以内严重逾期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="单位性质" prop="enterpriseNature">
                            <Select v-model="updateFormData.enterpriseNature" placeholder="请选择单位性质">
                                <Option value="企事业单位">企事业单位</Option>
                                <Option value="外资企业">外资企业</Option>
                                <Option value="上市公司">上市公司</Option>
                                <Option value="私营企业">私营企业</Option>
                                <Option value="个体">个体</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="工作时间" prop="workYear">
                            <Select v-model="updateFormData.workYear" placeholder="请选择收入体现">
                                <Option value="1年以内">1年以内</Option>
                                <Option value="1-3年">1-3年</Option>
                                <Option value="3-5年">3-5年</Option>
                                <Option value="5年以上">5年以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="需求资金" prop="requiredMoney">
                            <Input v-model="updateFormData.requiredMoney" placeholder="请输入借款金额"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款年限" prop="repaymentLimit">
                            <Select v-model="updateFormData.repaymentLimit" placeholder="请选择借款周期">
                                <Option value="1年以内">1年以内</Option>
                                <Option value="1-3年">1-3年</Option>
                                <Option value="3-5年">3-5年</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="推荐机构" prop="enterpriseNature">
                            <Select v-model="updateFormData.enterpriseNature" placeholder="请选择推荐贷款机构">
                                <Option value="银行">银行</Option>
                                <Option value="小贷">小贷</Option>
                                <Option value="私人">私人</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="客户户籍" prop="census">
                            <Input v-model="updateFormData.census" placeholder="请输入客户户籍"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="婚姻状况" prop="marriage">
                            <Select v-model="updateFormData.marriage" placeholder="请选择客户婚姻状况">
                                <Option value="已婚">已婚</Option>
                                <Option value="未婚">未婚</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="债券到期时间" prop="bondExpireDate">
                            <DatePicker type="date" placeholder="请选择日期"
                                        v-model="updateFormData.bondExpireDate"></DatePicker>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="贷款用途" prop="fundUse">
                            <Select v-model="updateFormData.fundUse" placeholder="请选择贷款用途">
                                <Option value="经商">经商</Option>
                                <Option value="购房">购房</Option>
                                <Option value="装修">装修</Option>
                                <Option value="生活消费">生活消费</Option>
                                <Option value="其它">其它</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款方式" prop="repaymentType">
                            <Select v-model="updateFormData.repaymentType" placeholder="请选择还款方式">
                                <Option value="一次性还款">一次性还款</Option>
                                <Option value="分期">分期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="22">
                        <FormItem label="客户性格" prop="customerCharacter">
                            <Input v-model="updateFormData.customerCharacter" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入客户性格说明"></Input>
                        </FormItem>
                    </Col>
                </row>
            </Form>
        </Modal>
        <!--无效弹框-->
        <Modal
                v-model="deleteModal"
                title="设为无效客户"
                width="800"
                @on-ok="delete_ok"
                @on-cancel="delete_cancel"
                :closable="false">
            <Form ref="deleteForm" :model="deleteFormData" :rules="addRule" :label-width="120">
                <row>
                    <Col span="24">
                        <FormItem label="电话说明：" prop="phoneRemark">
                            <Input v-model="deleteFormData.phoneRemark" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入电话无效说明"></Input>
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
    import Cookies from 'js-cookie';

    export default {
        data () {
            return {
                // 客户来源数据集合
                customerSourceList: [],
                // 组件中使用的各种属性
                columns4: [
                    {
                        type: 'index',
                        title: '序号',
                        width: 80,
                        align: 'center'
                    },
                    {
                        title: '客户姓名',
                        key: 'name',
                        width: 120,
                        align: 'center'
                    },
                    {
                        title: '客户电话',
                        key: 'phone',
                        width: 140,
                        align: 'center'
                    },
                    {
                        title: '客户来源',
                        key: 'customerSource',
                        width: 140,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', params.row.customerSource.sourceName);
                        }
                    },
                    {
                        title: '录入时间',
                        key: 'createDate',
                        width: 140,
                        align: 'center'
                    },
                    {
                        title: '客户备注',
                        key: 'otherInfo',
                        align: 'center'
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 140,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'success',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '10px'
                                    },
                                    on: {
                                        click: () => {
                                            this.updateCustomer(params.index);
                                        }
                                    }
                                }, '修改'),
                                h('Button', {
                                    props: {
                                        type: 'success',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.deleteCustomer(params.index);
                                        }
                                    }
                                }, '无效')
                            ]);
                        }
                    }

                ],
                // 分页显示页面数据
                data1: [],
                // 无效弹框属性
                deleteModal: false,
                // 更新弹框属性
                updateModal: false,
                // 设置无效弹框界面数据
                deleteFormData: {
                    id: '',
                    phoneRemark: ''
                },
                // 更新页面数据
                updateFormData: {
                    'name': '',
                    'gender': '',
                    'age': 0,
                    'phone': '',
                    'phoneRemark': '',
                    'loanType': '',
                    'customerSource': {
                        'id': '',
                        'sourceName': ''
                    },
                    'otherInfo': '',
                    'monthIncome': '',
                    'embodiment': '',
                    'socialInsurance': '',
                    'credit': '',
                    'enterpriseNature': '',
                    'workYear': '',
                    'requiredMoney': '',
                    'repaymentLimit': '',
                    'useDate': '',
                    'lendingInstitution': '',
                    'census': '',
                    'marriage': '',
                    'bondExpireDate': '',
                    'repaymentType': '',
                    'fundUse': '',
                    'customerCharacter': '',
                    'debtTotal': 0.0
                },
                // 分页对象数据
                total: 0,
                current: 1,
                pageSize: 5,
                // 校验规则
                addRule: {}
            };
        },
        // 弹窗提交事件方法
        methods: {
            // 更新页面确认按钮点击方法
            update_ok: function () {
                var newCustomer = this.updateFormData;
                util.ajax({
                    url: '/customer/update',
                    method: 'post',
                    data: newCustomer
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data == 'ok') {
                        this.$Message.success('操作成功');
                        this.getpage(this.current);
                    }
                });
            },
            update_cancel () {
                this.$Message.info('取消更新');
            },
            // 无效页面确认按钮点击方法
            delete_ok: function () {
                var customer = this.deleteFormData;
                util.ajax({
                    url: '/customer/setInvalid',
                    method: 'post',
                    data: customer
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data == 'ok') {
                        this.$Message.success('操作成功');
                        this.$refs['deleteForm'].resetFields();
                        this.getpage(this.current);
                    }
                });
            },
            delete_cancel () {
                this.$Message.info('取消操作');
            },
            // 分页显示页面更新按钮点击方法
            updateCustomer (index) {
                // index参数就是点击按钮所在的表格行索引
                this.updateModal = true;
                this.getCustomerSourceList();
                this.updateFormData = this.data1[index];
            },
            // 分页显示页面无效按钮点击方法
            deleteCustomer (index) {
                // index参数就是点击按钮所在的表格行索引
                this.deleteModal = true;
                this.deleteFormData = this.data1[index];
            },
            // 分页显示方法
            getpage (current) {
                var pageSize = this.pageSize;
                var account = Cookies.get('user');
                util.ajax({
                    url: '/customer/page/' + pageSize + '/' + current + '/' + account,
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
            // 获取客户来源列表
            getCustomerSourceList () {
                util.ajax({
                    url: '/customerSource/customerSourceList',
                    method: 'get'
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    this.customerSourceList = result.data;
                });
            }
        },
        mounted () {
            // 页面初始化方法
            this.getpage(1);
        }
    };
</script>

<style scoped>

</style>
