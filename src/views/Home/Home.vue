<template>
    <el-row class="home" :gutter="20">
        <el-col :span="8">
            <el-card shadow="hover" style="height:290px">
            <div class="user">
                <img :src="userImg" alt=""/>
                <div class="userino">
                    <p class="name">Christy</p>
                    <p class="access">超级管理员</p>
                </div>
            </div>
            <div class="loin-info">
                <p>上次登录时间:<span>2019-11-2</span></p>
                <p>上次登录地点:<span>pk</span></p>
            </div>
            </el-card>
            <el-card shadow="hover" style="height:522px;margin-top:40px">
                <el-table :data="tableData">
                    <el-table-column show-overflow-tooltip v-for="(val,key) in tableLabel" :key="key" :prop="key" :label="val">

                    </el-table-column>
                </el-table>
            </el-card>
        </el-col>
        <el-col :span="16">
            <div class="num">
                 <el-card shadow="hover" v-for="item in countData" :key="item.name" style="width:32%;margin-bottom:20px" :body-style="{ display:'flex',padding:0 }">
                <i class="icon" :class="`el-icon-${item.icon}`" :style="{background:item.color}"></i>
                <div class="detail">
                    <p class="num">{{item.value}}</p>
                    <p class="txt">{{item.name}}</p>
                </div>
                </el-card>
            </div>
                <el-card shadow="hover">
               <echart style="height:280px" :chartData="echartData.order"></echart>
                </el-card>
            <div class="graph">
                <el-card shadow="hover">
                 <echart style="height:260px" :chartData="echartData.user"></echart>
                </el-card>
                <el-card shadow="hover">
                 <echart style="height:260px" :chartData="echartData.video"></echart>
                </el-card>
            </div>
        </el-col>
    </el-row>
</template>

<script>
import Echart from '../../components/EChart'
export default {
    components:{
        Echart
    },
    data(){
        return{
            userImg:require('../../assets/images/user.png'),
            countData:[
                {
                    name:'今日支付订单',
                    value:'$1234',
                    icon:'success',
                    color:'#2ec7c9'
                },
                {
                    name:'明天支付订单',
                    value:'$7878',
                    icon:'success',
                    color:'#2ec7c9'
                },
                {
                    name:'后天支付订单',
                    value:'$5677',
                    icon:'success',
                    color:'#2ec7c9'
                },
                {
                    name:'昨日支付订单',
                    value:'$43546',
                    icon:'success',
                    color:'#2ec7c9'
                },
                {
                    name:'下午支付订单',
                    value:'$1234',
                    icon:'success',
                    color:'#2ec7c9'
                },
                {
                    name:'晚上支付订单',
                    value:'$1234',
                    icon:'success',
                    color:'#2ec7c9'
                }
            ],
            tableData:[],
            tableLabel:{
                name:'课程',
                todayBuy:'今日购买',
                monthBuy:'本月购买',
                totalBuy:'总购买'
            },
            echartData:{
                order:{
                    xData:[],//x轴数据
                    series:[]
                },
                user:{
                    xData:[],
                    series:[]
                },
                video:{
                    series:[]
                }
            }
        }
    },
    methods:{
        getTableData(){
             this.$http.get('/home/getData').then(res =>{
             res = res.data
             this.tableData = res.data.tableData
             console.log(res.data)

            //  订单折线图
            const order = res.data.orderData
            // 定义x轴上xData数据
            this.echartData.order.xData = order.date
            // 如何取出键对值
            // 第一步取出series中name部分 键名
            let keyArray = Object.keys(order.data[0]) //取出各项的键名
            console.log('keyArray',keyArray)
            // 取出键名对应的键值
             keyArray.forEach(key => {
                this.echartData.order.series.push({
                    name:key === 'wechat' ? '小程序' : key,
                    data:order.data.map(item => item[key]), //利用map方法取出键名对应的键值
                    type:'line'
                })
            })
            // 用户柱状图
             this.echartData.user.xData = res.data.userData.map(item => item.date)
            this.echartData.user.series.push({
                name:'新增用户',
                data:res.data.userData.map(item =>item.new),
                type:'bar'
            })
            this.echartData.user.series.push({
                name:'活跃用户',
                data:res.data.userData.map(item =>item.active),
                type:'bar',
                barGap:0
            })
            //视频饼图
            this.echartData.video.series.push({
                data:res.data.videoData,
                type:'pie'
            })
        })
        }
    },
    created(){
       this.getTableData()
    }
}
</script>

<style lang="less" scoped>
@import '~@/assets/css/home.css';
</style>