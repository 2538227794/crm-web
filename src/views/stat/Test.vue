<template>
    <div class="layout">
        <Row>
            <Col span="6">
                <DatePicker transfer="transfer" v-model="monthTime" type="month" placeholder="选择月份" style="width: 200px"></DatePicker>
            </Col>
            <Col span="6">
                <Button type="success" ghost  shape="circle" @click="getStatisticByMonth">查找</Button>
            </Col>
            <Col span="6">
                <h1>{{formatTime}}</h1>
            </Col>
        </Row>
        <br>
        <card>
            <v-chart :options="option" style="width: 1127px;height:480px;"/>
        </card>

    </div>
</template>

<script>
    //使用图表需要的依赖
    //下面是单个图表依赖(推荐)
    // import 'echarts/lib/chart/bar';
    // import 'echarts/lib/component/tooltip';
    //下面是所有图表依赖
    import 'echarts';
    //注册组件需要的依赖
    import ECharts from 'vue-echarts';
    import Cookies from 'js-cookie';
    // 参考全局组件注册代码
    // Vue.component('v-chart', ECharts)
    import utils from '../../libs/util'
    export default {
        //局部组件注册
        components: {
            'v-chart': ECharts
        },
        data() {
            return {
                monthTime:'',
                formatTime:'',
                option: {
                    title: {
                        text: '月份客户统计',
                        left: 'center'
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: '{a} <br/>{b} : {c} ({d}%)'
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        data: []//动态后端返回
                    },
                    series: [
                        {
                            name: '访问来源',
                            type: 'pie',
                            radius: '55%',
                            center: ['50%', '60%'],
                            data: [],
                            emphasis: {
                                itemStyle: {
                                    shadowBlur: 10,
                                    shadowOffsetX: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                }
                            }
                        }
                    ]
                }

            }
        },
        methods: {
            getStatisticByMonth(){
                let account = Cookies.get("user");
                utils.ajax({
                    url:'/stat/monthData/'+account+'/'+this.monthTime,
                    method:'get',
                }).then((resp) => {
                    console.log(resp.data);
                    this.option.series[0].data = resp.data;
                })
            }
        },
        mounted() {
            this.monthTime = new Date();
            this.getStatisticByMonth();
        }
    }
</script>

<style scoped>

</style>
