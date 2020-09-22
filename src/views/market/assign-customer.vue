<template>
    <div class="layout">
        <!--编写Iview组件-->
        <br/>
        <Row>
            <Col span="22">
                <Button type="primary" icon="ios-add" @click="openAddModel">新增客户</Button>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="22">
                <h3>导入客户名单列表：</h3>
            </Col>
        </Row>
        <br/>
        <!--数据展示表单-->
        <Row>
            <Col span="22">
                <Table border :columns="columns2" :data="rosterData"></Table>
            </Col>
        </Row>
        <br/>
        <Row>
            <Col span="22">
                <h3>新增客户列表：</h3>
            </Col>
        </Row>
        <br/>
        <!--数据展示表单-->
        <Row>
            <Col span="22">
                <Table border :columns="columns3" :data="customerData" @on-selection-change="checkRow"></Table>
            </Col>
        </Row>
        <br/>

        <!--整单分配弹框-->
        <Modal
                v-model="allAssignModal"
                title="整单分配客户"
                width="1300"
                @on-cancel="allAssignModal_cancel"
                :closable="false">
            <Row>
                <Col span="24">
                    <Button style="margin-left: 20px" type="success" @click="assignToDept">分配到部门</Button>
                    <Button style="margin-left: 20px" type="success" @click="assignToEmp">分配到员工</Button>
                </Col>
            </Row>
            <!--        选择栏-->
            <Row style="margin-top: 18px">
                <Col span="24">
                    <Form ref="allotDate" :model="allotAllDate" :label-width="150" inline>
                        <FormItem label="所属公司："  prop="companyId">
                            <Select v-model="allotAllDate.companyId" placeholder="请选择公司" @on-change="showDeptList">
                                <Option v-for="item in companyList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem label="所属部门："  prop="deptId">
                            <Select v-model="allotAllDate.deptId" placeholder="请选择部门" @on-change="showEmpList">
                                <Option v-for="item in deptList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem label="部门员工：" v-show="allotEmp" prop="empId">
                            <Select v-model="allotAllDate.empId" placeholder="请选择员工">
                                <Option v-for="item in empList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem  style="margin-left: -40px">
                            <Button type="error" style="margin-right: 10px" @click="toAllCustomer"  >确认分配</Button>
                        </FormItem>
                    </Form>
                </Col>
            </Row>
            <br/>
            <!--表格-->
            <Row>
                <Col span="24">
                    <Table border :columns="columns4" :data="data1" @on-selection-change="selectAll"></Table>
                </Col>
            </Row>
            <br/>
            <!--        分页条-->
            <Row>
                <Col span="20" align="center">
                    <Page :total="total" :current="current" :page-size="pageSize" @on-change="changePage"/>
                </Col>
            </Row>
        </Modal>

        <!--自增名单分配弹框-->
        <Modal
                v-model="assignModal"
                title="整单分配客户"
                width="1300"
                @on-cancel="assignModal_cancel"
                :closable="false">
            <Row>
                <Col span="24">
                    <Button style="margin-left: 20px" type="success" @click="assignSingleToDept">分配到部门</Button>
                    <Button style="margin-left: 20px" type="success" @click="assignSingleToEmp">分配到员工</Button>
                </Col>
            </Row>
            <!--        选择栏-->
            <Row style="margin-top: 18px">
                <Col span="24">
                    <Form ref="allotDate" :model="allotSingleDate" :label-width="150" inline>
                        <FormItem label="所属公司："  prop="companyId">
                            <Select v-model="allotSingleDate.companyId" placeholder="请选择公司" @on-change="showDeptList">
                                <Option v-for="item in companyList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem label="所属部门："  prop="deptId">
                            <Select v-model="allotSingleDate.deptId" placeholder="请选择部门" @on-change="showEmpList">
                                <Option v-for="item in deptList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem label="部门员工：" v-show="allotSingleEmp" prop="empId">
                            <Select v-model="allotSingleDate.empId" placeholder="请选择员工">
                                <Option v-for="item in empList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem  style="margin-left: -40px">
                            <Button type="error" style="margin-right: 10px" @click="toSingleCustomer"  >确认分配</Button>
                        </FormItem>
                    </Form>
                </Col>
            </Row>
            <br/>
        </Modal>

        <!--勾选分配弹框-->
        <Modal
                v-model="selectAssignModal"
                title="勾选分配客户"
                width="1300"
                @on-cancel="selectAssignModal_cancel"
                :closable="false">
            <Row>
                <Col span="24">
                    <Button style="margin-left: 20px" type="success">分配到员工</Button>
                </Col>
            </Row>
            <!-- 选择栏-->
            <Row style="margin-top: 18px">
                <Col span="24">
                    <Form ref="allotDate" :model="allotDate" :label-width="150" inline>
                        <FormItem label="所属公司："  prop="companyId">
                            <Select v-model="allotDate.companyId" placeholder="请选择公司" @on-change="showDeptList">
                                <Option v-for="item in companyList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem label="所属部门："  prop="deptId">
                            <Select v-model="allotDate.deptId" placeholder="请选择部门" @on-change="showEmpList">
                                <Option v-for="item in deptList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem label="部门员工："  prop="empId">
                            <Select v-model="allotDate.empId" placeholder="请选择员工">
                                <Option v-for="item in empList" :value="item.id">{{item.name}}</Option>
                            </Select>
                        </FormItem>
                        <FormItem  style="margin-left: -40px">
                            <Button type="error" style="margin-right: 10px" @click="toCustomer"  >确认分配</Button>
                        </FormItem>
                    </Form>
                </Col>
            </Row>
            <br/>
            <!--表格-->
            <Row>
                <Col span="24">
                    <Table border :columns="columns4" :data="data1" @on-selection-change="selectAll"></Table>
                </Col>
            </Row>
            <br/>
            <!--        分页条-->
            <Row>
                <Col span="20" align="center">
                    <Page :total="total" :current="current" :page-size="pageSize" @on-change="changePage"/>
                </Col>
            </Row>
        </Modal>

        <!--新增弹框-->
        <Modal
                v-model="addModal"
                title="新增客户"
                width="800"
                @on-ok="add_ok"
                @on-cancel="add_cancel"
                :closable="false">
            <Form ref="updateForm" :model="addFormData" :rules="ruleValidate" :label-width="110">
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
                            <Select v-model="addFormData.enterpriseNature" placeholder="请选择推荐贷款机构">
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
                                <Option value=1>一次性还款</Option>
                                <Option value=2>分期</Option>
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

        <!--编辑弹框-->
        <Modal
                v-model="updateModal"
                title="编辑客户"
                width="800"
                @on-ok="update_ok"
                @on-cancel="update_cancel"
                :closable="false">
            <Form ref="updateForm" :model="updateFormData" :rules="ruleValidate" :label-width="110">
                <row><h3>基本信息：</h3></row><br/>
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
                <br/><row><h3>辅助信息：</h3></row><br/>
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
                            <Select v-model="updateFormData.enterpriseNature" placeholder="请选择推荐贷款机构">
                                <Option :value=1>银行</Option>
                                <Option :value=2>小贷</Option>
                                <Option :value=3>私人</Option>
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
                                <Option value=1>一次性还款</Option>
                                <Option value=2>分期</Option>
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
    </div>

</template>

<script>
    // 编写vue.js代码
    import util from '../../libs/util';
    import Cookies from 'js-cookie';

    export default {
        data () {
            return {
                allotSingleEmp:false,
                allotEmp: false,
                // 组件中使用的各种属性
                addFormData: {
                    'name': '',
                    'gender': '',
                    'age': '0',
                    'phone': '',
                    'phoneRemark': '',
                    'loanType': '',
                    'otherInfo': '',
                    'updateTime': '',
                    'customerAssetTitile': '',
                    'customerAsset': '',
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
                    'debtTotal': '0',
                    'referrerId': '0',
                    'customerLevel': '',
                    'attentionLevel': '',
                    'dataPercent': '0',
                    'visibility': '0',
                    'callCount': '0',
                    'visitCount': '0',
                    'addType': '0',
                    'signState': '',
                    'countdown': '0',
                    'turnDetail': '',
                    'publicView': '0',
                    'isFollow': '0',
                    'fllowDate': '',
                    'state': '0',
                    'addPersonId': '0',
                    'addPersonName': '',
                    'createDate': '',
                    'createTime': '',
                    'customerRosterName': '',
                    'releaseDate': '',
                    'releaseTime': '',
                    'receiveDate': '',
                    'releaseType': '0',
                    'releaseId': '0',
                    'dept': {
                        'id': 0,
                    },
                    'emp': {
                        'id': 0,
                    },
                    'customerSource': {
                        'id': 0,
                    },
                    'house': {
                        'id': 0,
                    },
                    'car': {
                        'id': 0,
                    },
                    'creditCard': {
                        'id': 0,
                    },
                    'companyName': '',
                    'companyId': '0',
                    'lastFollowId': '0',
                    'lastRemindContent': '',
                    'lastFollowType': '0',
                    'lastRemindTime': '',
                    'lastWarrantFollowId': '0',
                    'lastWarrantRemindContent': '',
                    'lastWarrantFollowType': '0',
                    'lastWarrantRemindTime': ''
                },
                updateFormData: {
                    'name': '',
                    'gender': '',
                    'age': '0',
                    'phone': '',
                    'phoneRemark': '',
                    'loanType': '',
                    'otherInfo': '',
                    'updateTime': '',
                    'customerAssetTitile': '',
                    'customerAsset': '',
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
                    'debtTotal': '0',
                    'referrerId': '0',
                    'customerLevel': '',
                    'attentionLevel': '',
                    'dataPercent': '0',
                    'visibility': '0',
                    'callCount': '0',
                    'visitCount': '0',
                    'addType': '0',
                    'signState': '',
                    'countdown': '0',
                    'turnDetail': '',
                    'publicView': '0',
                    'isFollow': '0',
                    'fllowDate': '',
                    'state': '0',
                    'addPersonId': '0',
                    'addPersonName': '',
                    'createDate': '',
                    'createTime': '',
                    'customerRosterName': '',
                    'releaseDate': '',
                    'releaseTime': '',
                    'receiveDate': '',
                    'releaseType': '0',
                    'releaseId': '0',
                    'companyId': '0',
                    'companyName': '',
                    'lastFollowId': '0',
                    'lastRemindContent': '',
                    'lastFollowType': '0',
                    'lastRemindTime': '',
                    'lastWarrantFollowId': '0',
                    'lastWarrantRemindContent': '',
                    'lastWarrantFollowType': '0',
                    'lastWarrantRemindTime': ''
                },
                columns4: [
                    {
                        type: 'selection',
                        width: 60,
                        align: 'center'
                    },
                    {
                        title: '客户姓名',
                        key: 'name',
                        align: 'center'
                    },
                    {
                        title: '客户手机',
                        key: 'phone',
                        align: 'center'
                    },
                    {
                        title: '关注等级',
                        key: 'attentionLevel',
                        align: 'center',
                    },
                    {
                        title: '客户等级',
                        key: 'customerLevel',
                        align: 'center',
                    }, {
                        title: '客户来源',
                        key: 'customerSource',
                        align: 'center',
                        render: (h, params) => {
                            return h('div', params.row.customerSource.sourceName);
                        }
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
                                            this.openUpdateModal(params.index);
                                        }
                                    }
                                }, '编辑查看')
                            ]);
                        }
                    }
                ],
                //分页显示页面数据
                data1: [],
                //自增名单分配弹框属性
                assignModal: false,
                //新增弹框属性
                addAssign: false,
                //更新弹框属性
                updateModal: false,
                //勾选分配弹框中选择数据
                allotSingleDate: {
                    companyId: '',
                    deptId: '',
                    empId: ''
                },
                //勾选分配弹框中选择数据
                allotDate: {
                    companyId: '',
                    deptId: '',
                    empId: ''
                },
                //整单分配弹框中选择数据
                allotAllDate: {
                    companyId: '',
                    deptId: '',
                    empId: ''
                },
                twoData: {
                    companyId: '',
                    deptId: '',
                },
                ids: [],
                // 新增页面校验规则/更新页面校验规则
                addRule: {
                    name: [
                        {required: true, message: '员工姓名不能为空', trigger: 'blur'}
                    ],
                    company: [
                        {required: true, message: '所属公司不能为空', trigger: 'blur'}
                    ],
                    state: [
                        {required: true, message: '状态不能为空', trigger: 'blur'}
                    ]
                },
                // 更新页面数据
                updateDate: {},
                // 分页对象数据
                total: 0,
                current: 1,
                pageSize: 5,
                //存储员工列表，部门列表和公司列表属性
                empList: [],
                deptList: [],
                companyList: [],
                selects: [],
                allotDate: {
                    companyId: '',
                    deptId: '',
                    empId: ''
                },
                selectAssignModal:false,
                allAssignModal:false,
                panel_num:1,
                addModal:false,
                customerSourceList: {},
                // 客户分配空间栏显示属性
                conpanySelect: false,
                empSelect: false,
                // 客户分配数据栏数据
                allctateCustomer: {
                    company: {
                        id: ''
                    },
                    dept: {
                        id: ''
                    },
                    emp: {
                        id: ''
                    }
                },
                // 存储勾选框选中的id
                ids: [],
                // 公司集合
                companyList: [],
                // 公司集合
                deptList: [],
                // 公司集合
                empList: [],
                // 组件中使用的各种属性
                columns2: [
                    {
                        title: '序号',
                        type: 'index',
                        width: 70,
                        align: 'center'
                    },
                    {
                        title: '名单名称',
                        key: 'name',
                        width: 200,
                        align: 'center'
                    },
                    {
                        title: '导入日期',
                        key: 'datetime',
                        width: 180,
                        align: 'center',
                    },
                    {
                        title: '导入数量',
                        key: 'number',
                        align: 'center'
                    },
                    {
                        title: '已分配数量',
                        key: 'allocated',
                        align: 'center'
                    },
                    {
                        title: '未分配数量',
                        key: 'unallocated',
                        align: 'center'
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 200,
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
                                            this.openSelectAssignModal(params.row.id);
                                        }
                                    }
                                }, '勾选分配'),
                                h('Button', {
                                    props: {
                                        type: 'success',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.openAllAssignModal(params.row.id);
                                        }
                                    }
                                }, '整单分配')
                            ]);
                        }
                    }

                ],
                columns3: [
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
                        width: 110,
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
                        align: 'center'
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
                                            this.openAssignModal(params.row.id);
                                        }
                                    }
                                }, '分配')
                            ]);
                        }
                    }

                ],
                // 分页显示页面数据
                rosterData: [],
                customerData: [],
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
                updateFormData: {
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
                ruleValidate: {
                    name: [
                        {required: true, message: '姓名不能为空', trigger: 'blur'}
                    ]
                },
                // 分页对象数据
                total: 0,
                current: 1,
                pageSize: 2,
                id:'',
            };
        },
        // 弹窗提交事件方法
        methods: {
            //自增名单分配对话框分配给部门按钮
            assignSingleToDept(){
                this.allotSingleDate=false;
            },
            //自增名单分配对话框分配给员工按钮
            assignSingleToEmp(){
                this.allotSingleEmp=true;
            },
            //整单分配对话框分配给部门按钮
            assignToDept(){
                this.allotEmp=false;
            },
            //整单分配对话框分配给员工按钮
            assignToEmp(){
                this.allotEmp=true;
            },
            //打开整单分配对话框
            openAllAssignModal(id){
                this.id=id;
                this.allAssignModal=true;
                this.getCompanyList();
                this.changePage(1);
            },
            //打开勾选分配对话框
            openSelectAssignModal(id){
                this.id=id;
                this.selectAssignModal=true;
                this.getCompanyList();
                this.changePage(1);
            },
            //打开新增对话框
            openAddModel(){
                this.getCustomerSourceList();
                this.addModal = true
            }, // 新增页面确认按钮点击方法
            update_ok: function () {
                var username = Cookies.get('user');
                util.ajax({
                    url: '/customer/updateCustomer',
                    method: 'post',
                    data: this.updateFormData,
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('编辑成功');
                    };
                    this.getCustomerSourceList();
                    // 清空表单
                    this.$refs['addForm'].resetFields();
                    this.changePage(this.current)
                });
            },
            update_cancel () {
                this.$Message.info('取消编辑');
            },
            // 新增页面确认按钮点击方法
            add_ok: function () {
                var username = Cookies.get('user');
                util.ajax({
                    url: '/customer/market/addCustomer/'+username,
                    method: 'post',
                    data: this.addFormData,
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('新增成功');
                    };
                    this.getCustomerSourceList();
                    // 清空表单
                    this.$refs['addForm'].resetFields();
                });
            },
            allAssignModal_cancel() {
                this.$Message.info('取消整单分配');
            },
            selectAssignModal_cancel() {
                this.$Message.info('取消勾选分配');
            },
            assignModal_cancel() {
                this.$Message.info('取消分配');
            },
            add_cancel () {
                this.$Message.info('取消新增');
            },
            // 获取导入名单客户
            getRosterData () {
                var username = Cookies.get('user');
                util.ajax({
                    url: '/customer/rosters/' + username,
                    method: 'get'
                }).then((resp) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    this.rosterData = resp.data;
                });
            },
            //获取新增客户列表
            getCustomerData () {
                var username = Cookies.get('user');
                util.ajax({
                    url: '/customer/getAdd/' + username,
                    method: 'get'
                }).then((resp) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    this.customerData = resp.data;
                });
            },
            // 勾选框选择事件
            checkRow (selecteds) {
                this.ids = [];
                for (var i = 0; i < selecteds.length; i++) {
                    this.ids.push(selecteds[i].id);
                }
                ;
            },
            // 获取客户来源集合
            getCustomerSourceList () {
                var account = Cookies.get('user');
                util.ajax({
                    url: '/customerSource/customerSourceList',
                    method: 'get'
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    this.customerSourceList = result.data;
                });
            },
            toSingleCustomer(){
                var companyId = this.allotSingleDate.companyId;
                var deptId = this.allotSingleDate.deptId;
                //判断分配给员工or部门
                if (this.allotEmp) {
                    var empId = this.allotSingleDate.empId;
                    if(empId!=null){
                        util.ajax({
                            url: '/customer/assignSingleEmp/' + companyId + '/' + deptId + '/' + empId+ '/' +this.id,
                            method: 'get',
                        }).then((resp) => {
                            if (resp.data == 'ok') {
                                this.$Message.success('分配成功');
                                // 清空表单组件
                                this.$refs['allotDate'].resetFields();
                                this.changePage(this.current);
                            } else {
                                this.$Message.error('分配失败');
                            }
                        });
                    }else {
                        this.$Message.error("请选择要分配的员工")
                    }
                } else {
                    if(deptId!=null){
                        util.ajax({
                            url: '/customer/assignSingleDept/' + companyId + '/' + deptId+ '/' +this.id,
                            method: 'get',
                        }).then((resp) => {
                            if (resp.data == 'ok') {
                                this.$Message.success('分配成功');
                                this.$refs['allotDate'].resetFields();
                                this.changePage(this.current);
                            } else {
                                this.$Message.error('分配失败');
                            }
                        });
                    }else {
                        this.$Message.error("请选择要分配的部门")
                    }
                }
            },
            //整单分配弹框确定分配
            toAllCustomer(){
                var companyId = this.allotAllDate.companyId;
                var deptId = this.allotAllDate.deptId;
                //判断分配给员工or部门
                if (this.allotSingleEmp) {
                    var empId = this.allotAllDate.empId;
                    if(empId!=null){
                        util.ajax({
                            url: '/customer/assignAllEmp/' + companyId + '/' + deptId + '/' + empId,
                            method: 'get',
                        }).then((resp) => {
                            if (resp.data == 'ok') {
                                this.$Message.success('分配成功');
                                // 清空表单组件
                                this.$refs['allotDate'].resetFields();
                                this.changePage(this.current);
                            } else {
                                this.$Message.error('分配失败');
                            }
                        });
                    }else {
                        this.$Message.error("请选择要分配的员工")
                    }
                } else {
                    if(deptId!=null){
                        util.ajax({
                            url: '/customer/assignAllDept/' + companyId + '/' + deptId,
                            method: 'get',
                        }).then((resp) => {
                            if (resp.data == 'ok') {
                                this.$Message.success('分配成功');
                                this.$refs['allotDate'].resetFields();
                                this.changePage(this.current);
                            } else {
                                this.$Message.error('分配失败');
                            }
                        });
                    }else {
                        this.$Message.error("请选择要分配的部门")
                    }
                }
            },
            //勾选分配弹框确定分配
            toCustomer () {
                var companyId = this.allotDate.companyId;
                var deptId = this.allotDate.deptId;
                var empId = this.allotDate.empId;
                if(this.ids.length>0){
                    if(empId!=null) {
                        util.ajax({
                            url: '/customer/assignSelectEmp/' + companyId + '/' + deptId + '/' + empId,
                            method: 'post',
                            data: this.ids
                        }).then((resp) => {
                            if (resp.data == 'ok') {
                                this.$Message.success('分配成功');
                                // 清空表单组件
                                this.$refs['allotDate'].resetFields();
                                this.changePage(this.current);
                                this.ids=[];
                            } else {
                                this.$Message.error('分配失败');
                            }
                        });
                    }else{
                        this.$Message.error("请选择要分配的员工")
                    }
                }else {
                    this.$Message.error("请至少勾选一项分配")
                }
            },
            //储存勾选的id
            selectAll (selection) {
                this.ids = [];
                for (let i = 0; i < selection.length; i++) {
                    this.ids.push(selection[i].id);
                }
            },
            //勾选分配分页加载事件
            changePage (current) {
                var username = Cookies.get('user');
                //加载订单未分配的顾客
                util.ajax({
                    url:'/customer/getSelectPage',
                    method:'get',
                    params: {
                        customerRosterId:this.id,
                        username:username,
                        pageSize:this.pageSize,
                        current:current,
                    }
                }).then((resp)=>{
                    //获取响应的数据对象：resp.data
                    //给页码组件属性赋值
                    this.data1 = resp.data.list;
                    this.total = resp.data.totalCount;
                    this.current = resp.data.pageNo;
                    this.pageSize = resp.data.pageSize;
                })
            },
            openAssignModal(index){
                this.assignModal = true;
                this.getCompanyList();
                this.id = index;
            },
            // 点击“编辑查看”按钮事件方法
            openUpdateModal(index) {
                this.updateModal = true;
                this.updateFormData = this.data1[index];
                this.getCustomerSourceList();
            },
            //请求获取部门列表
            showDeptList (id) {
                util.ajax({
                    url: '/dept/getByCompany/' + id,
                    method: 'get',
                }).then((result) => {//请求成功后的操作
                    this.deptList = result.data;
                });
            },
            //请求获取公司列表
            getCompanyList () {
                util.ajax({
                    url: '/company/getAll',
                    method: 'get',
                }).then((result) => {//请求成功后的操作
                    this.companyList = result.data;
                });
            },
            //员工列表
            showEmpList (id) {
                util.ajax({
                    url: '/emp/getByDept/' + id,
                    method: 'get',
                }).then((result) => {//请求成功后的操作
                    this.empList = result.data;
                });
            },
        },
        mounted () {
            // 页面初始化方法
            this.getRosterData();
            this.getCustomerData();
        }
    };
</script>

<style scoped>

</style>
