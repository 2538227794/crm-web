<template>
    <div class="layout">
        <!--编写Iview组件-->
        <br/>
        <!--搜索栏-->
        <Row>
            <Col span="20">
                部门名称： <Input  placeholder="请输入部门名称" clearable style="width: 200px;margin-right:10px"/>
                <Button type="primary" icon="ios-search">搜索</Button>
            </Col>
        </Row>
        <br/>
        <!--控件栏-->
        <Row>
            <Col span="8">
                <Button type="info" icon="md-add" @click="addValidCustomer">新增</Button>
            </Col>
        </Row>
        <br/>
        <!--数据展示表单-->
        <Row>
            <Col span="23">
                <Table border :columns="columns4" :data="data1"></Table>
            </Col>
        </Row>
        <br/>
        <!-- 分页条-->
        <Row>
            <Col span="23" align="center">
                <Page :total="total" :current="current" :page-size="pageSize" align="center" @on-change="getpage"/>
            </Col>
        </Row>
        <!--新增弹框-->
        <Modal
                v-model="addModal"
                title="新增客户"
                width="800"
                @on-ok="add_ok"
                @on-cancel="add_cancel"
                :closable="false">
            <Form ref="addForm" :model="addFormData" :rules="addRule" :label-width="110">
                <row><h3>基本信息：</h3></row><br/>
                <row>
                    <Col span="11">
                        <FormItem label="客户姓名" prop="name">
                            <Input v-model="addFormData.name" placeholder="请输入客户姓名"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="客户性别" prop="gender">
                            <RadioGroup v-model="addFormData.gender">
                                <Radio :label=0>男</Radio>
                                <Radio :label=1>女</Radio>
                            </RadioGroup>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="客户年龄" prop="age">
                            <Input v-model="addFormData.age" placeholder="请输入客户年龄"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="客户电话" prop="phone">
                            <Input v-model="addFormData.phone" placeholder="请输入客户电话"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="客户来源" prop="customerSource">
                            <Select v-model="addFormData.customerSource.id" placeholder="请选择客户来源">
                                <Option v-for="item in customerSourceList" :value="item.id">{{item.sourceName}}</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="贷款类型" prop="loanType">
                            <Select v-model="addFormData.loanType" placeholder="请选择贷款类型">
                                <Option :value=1>信用贷款</Option>
                                <Option :value=2>抵押贷款</Option>
                                <Option :value=3>商业贷款</Option>
                                <Option :value=4>房贷</Option>
                                <Option :value=5>车贷</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="电话备注" prop="phoneRemark">
                            <Input v-model="addFormData.phoneRemark" placeholder="请输入客户电话备注信息"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="其它信息" prop="otherInfo">
                            <Input v-model="addFormData.otherInfo" placeholder="请输入其它备注信息"></Input>
                        </FormItem>
                    </Col>
                </row>
                <br/><row><h3>辅助信息：</h3></row><br/>
                <row>
                    <Col span="11">
                        <FormItem label="每月收入" prop="monthIncome">
                            <Select v-model="addFormData.monthIncome" placeholder="请选择客户来源">
                                <Option :value=1>3000以下</Option>
                                <Option :value=2>3000-5000</Option>
                                <Option :value=3>5000-8000</Option>
                                <Option :value=4>8000-15000</Option>
                                <Option :value=5>15000以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="收入体现" prop="embodiment">
                            <Select v-model="addFormData.embodiment" placeholder="请选择收入体现">
                                <Option :value=1>现金</Option>
                                <Option :value=2>转账</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="社保状态" prop="socialInsurance">
                            <Select v-model="addFormData.socialInsurance" placeholder="请选择客户来源">
                                <Option :value=1>个人缴纳</Option>
                                <Option :value=2>公司缴纳一年以内</Option>
                                <Option :value=3>公司缴纳三年以内</Option>
                                <Option :value=4>公司缴纳三年以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="个人征信" prop="credit">
                            <Select v-model="addFormData.credit" placeholder="请选择收入体现">
                                <Option :value=1>良好</Option>
                                <Option :value=2>两年以内少数逾期</Option>
                                <Option :value=3>五年以内少数逾期</Option>
                                <Option :value=4>两年以内严重逾期</Option>
                                <Option :value=5>五年以内严重逾期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="单位性质" prop="enterpriseNature">
                            <Select v-model="addFormData.enterpriseNature" placeholder="请选择单位性质">
                                <Option :value=1>企事业单位</Option>
                                <Option :value=2>外资企业</Option>
                                <Option :value=3>上市公司</Option>
                                <Option :value=4>私营企业</Option>
                                <Option :value=5>个体</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="工作时间" prop="workYear">
                            <Select v-model="addFormData.workYear" placeholder="请选择收入体现">
                                <Option :value=1>1年以内</Option>
                                <Option :value=2>1-3年</Option>
                                <Option :value=3>3-5年</Option>
                                <Option :value=4>5年以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="需求资金" prop="requiredMoney">
                            <Input v-model="addFormData.requiredMoney" placeholder="请输入借款金额"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款年限" prop="repaymentLimit">
                            <Select v-model="addFormData.repaymentLimit" placeholder="请选择借款周期">
                                <Option :value=1>1年以内</Option>
                                <Option :value=2>1-3年</Option>
                                <Option :value=3>3-5年</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="推荐机构" prop="enterpriseNature">
                            <Select v-model="addFormData.lendingInstitution" placeholder="请选择推荐贷款机构">
                                <Option :value=1>银行</Option>
                                <Option :value=2>小贷</Option>
                                <Option :value=3>私人</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="客户户籍" prop="census">
                            <Input v-model="addFormData.census" placeholder="请输入客户户籍"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="婚姻状况" prop="marriage">
                            <Select v-model="addFormData.marriage" placeholder="请选择客户婚姻状况">
                                <Option :value=1>已婚</Option>
                                <Option :value=2>未婚</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="债券到期时间" prop="bondExpireDate">
                            <DatePicker type="date" placeholder="请选择日期" v-model="addFormData.bondExpireDate"></DatePicker>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="贷款用途" prop="fundUse">
                            <Select v-model="addFormData.fundUse" placeholder="请选择贷款用途">
                                <Option :value=1>经商</Option>
                                <Option :value=2>购房</Option>
                                <Option :value=3>装修</Option>
                                <Option :value=4>生活消费</Option>
                                <Option :value=5>其它</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款方式" prop="repaymentType">
                            <Select v-model="addFormData.repaymentType" placeholder="请选择还款方式">
                                <Option :value=1>一次性还款</Option>
                                <Option :value=2>分期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="22">
                        <FormItem label="客户性格" prop="customerCharacter">
                            <Input v-model="addFormData.customerCharacter" type="textarea" :autosize="{minRows: 2,maxRows: 5}"
                                   placeholder="请输入客户性格说明"></Input>
                        </FormItem>
                    </Col>
                </row>
            </Form>
        </Modal>
        <!--修改弹框-->
        <Modal
                v-model="updateModal"
                title="更新客户信息"
                width="1000"
                header-hide
                footer-hide>
            <Form ref="updateForm" :model="updateFormData" :rules="addRule" :label-width="90">
                <row><h3 align="center">基本信息</h3></row><br/>
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
                                <Option :value=1>信用贷款</Option>
                                <Option :value=2>抵押贷款</Option>
                                <Option :value=3>商业贷款</Option>
                                <Option :value=4>房贷</Option>
                                <Option :value=5>车贷</Option>
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
                <row><h3 align="center">辅助信息</h3></row><br/>
                <row>
                    <Col span="11">
                        <FormItem label="每月收入" prop="monthIncome">
                            <Select v-model="updateFormData.monthIncome" placeholder="请选择客户来源">
                                <Option :value=1>3000以下</Option>
                                <Option :value=2>3000-5000</Option>
                                <Option :value=3>5000-8000</Option>
                                <Option :value=4>8000-15000</Option>
                                <Option :value=5>15000以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="收入体现" prop="embodiment">
                            <Select v-model="updateFormData.embodiment" placeholder="请选择收入体现">
                                <Option :value=1>现金</Option>
                                <Option :value=2>转账</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="社保状态" prop="socialInsurance">
                            <Select v-model="updateFormData.socialInsurance" placeholder="请选择客户来源">
                                <Option :value=1>个人缴纳</Option>
                                <Option :value=2>公司缴纳一年以内</Option>
                                <Option :value=3>公司缴纳三年以内</Option>
                                <Option :value=4>公司缴纳三年以上</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="个人征信" prop="credit">
                            <Select v-model="updateFormData.credit" placeholder="请选择收入体现">
                                <Option :value=1>良好</Option>
                                <Option :value=2>两年以内少数逾期</Option>
                                <Option :value=3>五年以内少数逾期</Option>
                                <Option :value=4>两年以内严重逾期</Option>
                                <Option :value=5>五年以内严重逾期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="单位性质" prop="enterpriseNature">
                            <Select v-model="updateFormData.enterpriseNature" placeholder="请选择单位性质">
                                <Option :value=1>事业单位</Option>
                                <Option :value=2>外资企业</Option>
                                <Option :value=3>上市公司</Option>
                                <Option :value=4>私营企业</Option>
                                <Option :value=5>个体</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="工作时间" prop="workYear">
                            <Select v-model="updateFormData.workYear" placeholder="请选择收入体现">
                                <Option :value=1>1年以内</Option>
                                <Option :value=2>1-3年</Option>
                                <Option :value=3>3-5年</Option>
                                <Option :value=4>5年以上</Option>
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
                                <Option :value=1>1年以内</Option>
                                <Option :value=2>1-3年</Option>
                                <Option :value=3>3-5年</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="推荐机构" prop="enterpriseNature">
                            <Select v-model="updateFormData.lendingInstitution" placeholder="请选择推荐贷款机构">
                                <Option :value=1>银行</Option>
                                <Option :value=2>小贷</Option>
                                <Option :value=2>私人</Option>
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
                                <Option :value=1>已婚</Option>
                                <Option :value=2>未婚</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="债券到期时间" prop="bondExpireDate">
                            <DatePicker type="date" placeholder="请选择日期" v-model="updateFormData.bondExpireDate"></DatePicker>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="贷款用途" prop="fundUse">
                            <Select v-model="updateFormData.fundUse" placeholder="请选择贷款用途">
                                <Option :value=1>经商</Option>
                                <Option :value=2>购房</Option>
                                <Option :value=3>装修</Option>
                                <Option :value=4>生活消费</Option>
                                <Option :value=5>其它</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="还款方式" prop="repaymentType">
                            <Select v-model="updateFormData.repaymentType" placeholder="请选择还款方式">
                                <Option :value=1>一次性还款</Option>
                                <Option :value=2>分期</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row><br/>
                <Row>
                    <Col span="24" align="center">
                        <Button type="info" style="margin-right: 5px" @click="updateValidCustomer">更改</Button>
                        <Button type="info" style="margin-left: 5px" @click="update_clear">清空</Button>
                    </Col>
                </Row><br/><br/>
            </Form>
            <row><h2 align="center">资产信息</h2></row><br/>
            <row><h3 align="center">房产信息</h3></row><br/>
            <row>
                <Col span="24">
                    <Table border :columns="columnsHouse" :data="dataHouse"></Table>
                </Col>
            </row><br/>
            <Row>
                <Col span="24" align="center">
                    <Button type="info" @click="changeHouse">编辑房产信息</Button>
                </Col>
            </Row><br/>
            <row><h3 align="center">车辆信息</h3></row><br/>
            <row>
                <Col span="24">
                    <Table border :columns="columnsCar" :data="dataCar"></Table>
                </Col>
            </row><br/>
            <Row>
                <Col span="24" align="center">
                    <Button type="info" @click="changeCar">编辑车辆信息</Button>
                </Col>
            </Row><br/>
            <row><h3 align="center">信用卡信息</h3></row><br/>
            <row>
                <Col span="24">
                    <Table border :columns="columnsCard" :data="dataCard"></Table>
                </Col>
            </row>
            <Row>
                <Col span="24" align="center">
                    <Button type="info" @click="changeCard">编辑信用卡信息</Button>
                </Col>
            </Row>
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
                // 房产信息
                dataHouse: [],
                // 车辆信息
                dataCar: [],
                // 信用卡信息
                dataCard: [],
                // 客户来源
                customerSourceList: {},
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
                        title: '客户姓名',
                        key: 'name',
                        width: 95,
                        align: 'center'
                    },
                    {
                        title: '客户电话',
                        key: 'phone',
                        align: 'center'
                    },
                    {
                        title: '贷款类型',
                        key: 'loanType',
                        width: 95,
                        align: 'center',
                        render: (h, params) => {
                            if(params.row.loanType==1){
                                return h('div', "信用贷款");
                            }else if(params.row.loanType==2){
                                return h('div', "抵押贷款");
                            }else if(params.row.loanType==3){
                                return h('div', "商业贷款");
                            }else if(params.row.loanType==4){
                                return h('div', "房贷");
                            }else if(params.row.loanType==5){
                                return h('div', "车贷");
                            }else {
                                return h('div', "");
                            }
                        }
                    },
                    {
                        title: '通话次数',
                        key: 'callCount',
                        width: 95,
                        align: 'center'
                    },
                    {
                        title: '上门次数',
                        key: 'visitCount',
                        width: 95,
                        align: 'center'
                    },
                    {
                        title: '签单状态',
                        key: 'signState',
                        width: 95,
                        align: 'center'
                    },
                    {
                        title: '自留',
                        width: 80,
                        align: 'center',
                        render: (h, params) => {
                            var visibility = params.row.visibility;
                            if (visibility === 0) {
                                return h('strong', '是');
                            } else {
                                return h('strong', '否');
                            }
                        }
                    },
                    {
                        title: '关注等级',
                        key: 'attentionLevel',
                        width: 95,
                        align: 'center'
                    },
                    {
                        title: '倒计时',
                        key: 'countdown',
                        width: 85,
                        align: 'center'
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 100,
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
                                            this.updateCustomer(params.index);
                                        }
                                    }
                                }, '编辑')
                            ]);
                        }
                    }

                ],
                // 房产组件中的属性
                columnsHouse: [
                    {
                        type: 'index',
                        title: '序号',
                        width: 70,
                        align: 'center'
                    },
                    {
                        title: '国土性质',
                        align: 'center',
                        render: (h, params) => {
                            var landNature = params.row.landNature;
                            if (landNature === 1) {
                                return h('div', '国有');
                            } if (landNature === 2) {
                                return h('div', '集体');
                            } else {
                                return h('div', '');
                            }
                        }
                    },
                    {
                        title: '建造年代',
                        key: 'constructDate',
                        align: 'center'
                    },
                    {
                        title: '面积',
                        width: 100,
                        key: 'area',
                        align: 'center'
                    },
                    {
                        title: '单价',
                        key: 'price',
                        width: 100,
                        align: 'center'
                    },
                    {
                        title: '装修情况',
                        align: 'center',
                        render: (h, params) => {
                            var decorateCase = params.row.decorateCase;
                            if (decorateCase === 1) {
                                return h('div', '精装');
                            } else if (decorateCase === 2) {
                                return h('div', '简装');
                            } else if (decorateCase === 3){
                                return h('div', '清水');
                            }else {
                                return h('div', '清水');
                            }
                        }
                    },
                    {
                        title: '使用情况',
                        align: 'center',
                        render: (h, params) => {
                            var signState = params.row.signState;
                            if (signState === 1) {
                                return h('div', '自用');
                            } else if (signState === 2) {
                                return h('div', '出租');
                            }else {
                                return h('div', '');
                            }
                        }
                    },
                    {
                        title: '类型',
                        key: 'type',
                        align: 'center'
                    }],
                // 车辆组件中的属性
                columnsCar: [
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
                        title: '够买时间',
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
                    }
                ],
                // 信用卡组件中的属性
                columnsCard: [
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
                    }
                ],
                // 分页显示页面数据
                data1: [],
                // 更新弹框属性
                updateModal: false,
                // 新增弹框属性
                addModal: false,
                // 校验规则
                addRule: {
                    name: [
                        {required: true, message: '姓名不能为空', trigger: 'blur'}
                    ]
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
                // 新增页面数据
                addFormData: {
                    'name': '',
                    'gender': '',
                    'age': '',
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
                pageSize: 5
            };
        },
        // 弹窗提交事件方法
        methods: {
            // 新增页面确认按钮点击方法
            add_ok: function () {
                var username=Cookies.get("user");
                util.ajax({
                    url: '/customer/addValidCustomer/'+username,
                    method: 'post',
                    data: this.addFormData,
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('新增成功');
                    };
                    this.getpage(this.current);
                    // 清空表单
                    this.$refs['addForm'].resetFields();
                });
            },
            add_cancel () {
                this.$Message.info('取消新增');
            },
            // 更新页面更新按钮点击方法
            updateValidCustomer: function () {
                util.ajax({
                    url: '/customer/updateCustomer',
                    method: 'post',
                    data: this.updateFormData,
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('更新成功');
                    };
                    this.getpage(this.current);
                });
            },
            update_clear () {
                // 清空表单
                this.$refs['updateForm'].resetFields();
            },
            // 分页显示页面更新按钮点击方法
            updateCustomer (index) {
                // index参数就是点击按钮所在的表格行索引
                this.updateModal = true;
                // 获取页面默认显示数据
                this.updateFormData = this.data1[index];
                // 获取客户来源
                this.getCustomerSourceList();
                // 获取当前客户的房产信息
                this.dataHouse = this.data1[index].houses;
                // 获取当前客户的车辆信息
                this.dataCar = this.data1[index].cars;
                // 获取当前客户的信用卡信息
                this.dataCard = this.data1[index].creditCards;
            },
            // 有效客户分页显示方法
            getpage (current) {
                var pageSize = this.pageSize;
                var account = Cookies.get('user');
                util.ajax({
                    url: '/customer/validCustomer/' + pageSize + '/' + current + '/' + account,
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
            addValidCustomer () {
                this.addModal = true;
                this.getCustomerSourceList();
            },
            // 获取客户来源集合
            getCustomerSourceList () {
                util.ajax({
                    url: '/customerSource/customerSourceList',
                    method: 'get'
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    this.customerSourceList = result.data;
                });
            },
            // 模拟登陆
            loginEmp () {
                util.ajax({
                    url: '/personnel/emp/login',
                    method: 'get',
                    withCredentials: true
                }).then((result) => {});
            },
            // 跳转至房产信息修改页面
            changeHouse () {
                this.$router.push({path: '/business/house/' + this.updateFormData.id});
            },
            // 跳转至车辆信息修改页面
            changeCar () {
                this.$router.push({path: '/business/car/' + this.updateFormData.id});
            },
            // 跳转至信用卡信息修改页面
            changeCard () {
                this.$router.push({path: '/business/card/' + this.updateFormData.id});
            }
        },
        mounted () {
            // 页面初始化方法
            this.getpage(1);
            this.loginEmp();
        }
    };
</script>

<style scoped>

</style>
