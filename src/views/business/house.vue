<template>
    <div class="layout">
        <!--编写Iview组件-->
        <br/>
        <!--控件栏-->
        <Row>
            <Col span="8">
                <Button type="info" icon="md-add" @click="addHouse">新增</Button>
                <Button type="error" icon="md-trash" @click="deleteHouse">删除</Button>
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
                title="新增房产"
                @on-ok="add_ok"
                @on-cancel="add_cancel"
                :closable="false">
            <Form ref="addForm" :model="addFormData" :rules="addRule" :label-width="120">
                <row>
                    <Col span="11">
                        <FormItem label="住房位置(区)" prop="placeArea">
                            <Input v-model="addFormData.placeArea" placeholder="请输入住房位置(区)"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="住房位置(街道)" prop="placeStreet">
                            <Input v-model="addFormData.placeStreet" placeholder="请输入住房位置(街道)"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="住房位置(详情)" prop="placeDetail">
                            <Input v-model="addFormData.placeDetail" placeholder="请输入住房位置(详情)"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="住房位置(楼盘)" prop="placePremise">
                            <Input v-model="addFormData.placePremise" placeholder="请输入住房位置(楼盘)"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="国土性质" prop="landNature">
                            <Select v-model="addFormData.landNature" placeholder="请选择国土性质">
                                <Option :value=1>集体</Option>
                                <Option :value=2>国有</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="建造年代" prop="constructDate">
                            <Input v-model="addFormData.constructDate" placeholder="请输入建造年代"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="面积" prop="area">
                            <Input v-model="addFormData.area" placeholder="请输入房产面积"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="单价" prop="price">
                            <Input v-model="addFormData.price" placeholder="请输入房产单价"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="月租金" prop="rental">
                            <Input v-model="addFormData.rental" placeholder="请输入月租金"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="类型" prop="type">
                            <Select v-model="addFormData.type" placeholder="请选择房产类型">
                                <Option :value=0>住宅</Option>
                                <Option :value=1>公寓</Option>
                                <Option :value=2>别墅</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="装修情况" prop="decorateCase">
                            <Select v-model="addFormData.decorateCase" placeholder="请选择装修情况">
                                <Option :value=1>精装</Option>
                                <Option :value=2>简装</Option>
                                <Option :value=3>清水</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="使用情况" prop="useCase">
                            <Select v-model="addFormData.useCase" placeholder="请选择房产使用情况">
                                <Option :value=1>自用</Option>
                                <Option :value=2>出租</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
            </Form>
        </Modal>
        <!--修改弹框-->
        <Modal
                v-model="updateModal"
                title="更新房产"
                width="800"
                @on-ok="update_ok"
                @on-cancel="update_cancel"
                :closable="false">
            <Form ref="updateForm" :model="updateFormData" :rules="addRule" :label-width="120">
                <row>
                    <Col span="11">
                        <FormItem label="住房位置(区)" prop="placeArea">
                            <Input v-model="updateFormData.placeArea" placeholder="请输入住房位置(区)"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="住房位置(街道)" prop="placeStreet">
                            <Input v-model="updateFormData.placeStreet" placeholder="请输入住房位置(街道)"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="住房位置(详情)" prop="placeDetail">
                            <Input v-model="updateFormData.placeDetail" placeholder="请输入住房位置(详情)"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="住房位置(楼盘)" prop="placePremise">
                            <Input v-model="updateFormData.placePremise" placeholder="请输入住房位置(楼盘)"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="国土性质" prop="landNature">
                            <Select v-model="updateFormData.landNature" placeholder="请选择国土性质">
                                <Option :value=1>集体</Option>
                                <Option :value=2>国有</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="建造年代" prop="constructDate">
                            <Input v-model="updateFormData.constructDate" placeholder="请输入建造年代"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="面积" prop="area">
                            <Input v-model="updateFormData.area" placeholder="请输入房产面积"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="单价" prop="price">
                            <Input v-model="updateFormData.price" placeholder="请输入房产单价"></Input>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="月租金" prop="rental">
                            <Input v-model="updateFormData.rental" placeholder="请输入月租金"></Input>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="类型" prop="type">
                            <Select v-model="updateFormData.type" placeholder="请选择房产类型">
                                <Option :value=1>住宅</Option>
                                <Option :value=2>公寓</Option>
                                <Option :value=3>别墅</Option>
                            </Select>
                        </FormItem>
                    </Col>
                </row>
                <row>
                    <Col span="11">
                        <FormItem label="装修情况" prop="decorateCase">
                            <Select v-model="updateFormData.decorateCase" placeholder="请选择装修情况">
                                <Option :value=1>精装</Option>
                                <Option :value=2>简装</Option>
                                <Option :value=3>清水</Option>
                            </Select>
                        </FormItem>
                    </Col>
                    <Col span="2"></Col>
                    <Col span="11">
                        <FormItem label="使用情况" prop="useCase">
                            <Select v-model="updateFormData.useCase" placeholder="请选择房产使用情况">
                                <Option :value=1>自用</Option>
                                <Option :value=2>出租</Option>
                            </Select>
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
                        title: '国土性质',
                        align: 'center',
                        render: (h, params) => {
                            var landNature = params.row.landNature;
                            if (landNature === 1) {
                                return h('div', '国有');
                            } else if (landNature === 2) {
                                return h('div', '集体');
                            }else {
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
                                return h('div', '');
                            }
                        }
                    },
                    {
                        title: '使用情况',
                        align: 'center',
                        render: (h, params) => {
                            var signState = params.row.useCase;
                            if (signState === 1) {
                                return h('div', '自用');
                            } else if(signState===2){
                                return h('div', '出租');
                            }else {
                                return h('div', '');
                            }
                        }
                    },
                    {
                        title: '类型',
                        align: 'center',
                        render: (h, params) => {
                            var type = params.row.type;
                            if (type === 1) {
                                return h('div', '住宅');
                            } else if(type===2){
                                return h('div', '公寓');
                            }else if(type===3){
                                return h('div', '别墅');
                            }else {
                                return h('div', '');
                            }
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
                                            this.updateHouse(params.index);
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
                    refundDate: '',
                    customerId:'',
                },
                // 新增页面数据
                addFormData: {
                    placeArea: '',
                    placeStreet: '',
                    placeDetail: '',
                    placePremise: '',
                    landNature: '',
                    constructDate: '',
                    area: '',
                    price: '',
                    decorateCase: '',
                    useCase: '',
                    rental: '',
                    type: 0,
                    customerId:'',
                },
                // 校验规则
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
            add_ok() {
                this.addFormData.customerId= this.customerId;
                var params = this.addFormData;
                util.ajax({
                    url: '/house/addHouse',
                    method: 'post',
                    data: params
                }).then((result) => {
                    // 请求成功后的操作
                    // 将返回数据集合赋值给表格数据属性
                    if (result.data === 'ok') {
                        this.$Message.info('新增成功');
                    }
                    ;
                    this.getpage(this.current);
                    // 清空表单
                    this.$refs['addFrom'].resetFields();
                });
            },
            add_cancel () {
                this.$Message.info('取消新增');
            },
            // 更新页面确认按钮点击方法
            update_ok(){
                var params = this.updateFormData;
                util.ajax({
                    url: '/house/updateHouse',
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
            updateHouse (index) {
                // index参数就是点击按钮所在的表格行索引
                this.updateModal = true;
                this.updateFormData = this.data1[index];
            },
            // 分页显示方法
            getpage (current) {
                var pageSize = this.pageSize;
                var id = this.customerId;
                util.ajax({
                    url: '/house/page/'+id+'/'+ current + '/'+ pageSize ,
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
            addHouse () {
                this.addModal = true;
            },
            // 获取需要冻结的房产集合
            checkRow (selecteds) {
                this.ids = [];
                for (var i = 0; i < selecteds.length; i++) {
                    this.ids.push(selecteds[i].id);
                }
            },
            // 批量删除
            deleteHouse () {
                // 判断是否选中房产
                if (this.ids.length > 0) {
                    util.ajax({
                        url: '/house/deleteHouse',
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
                    this.$Message.info('请选择需要删除的房产');
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
