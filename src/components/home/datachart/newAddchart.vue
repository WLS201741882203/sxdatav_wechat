<template>
  <v-chart  :options="polar" style="width:80vw;height:40vh"/>
</template>

<script>
export default {
    name: "newAddchart",
  data () {
    return {
      polar: {
        title : {
            text: '2009-12-30至今新增人口',
            subtext: '总计人数：',
            x:'center',
            textStyle:{
            color: '#00ffb2'
            }
        },
        tooltip : {
            trigger: 'item',
            position: 'bottom',
            formatter: "{a} <br/>{b} : {c} ({d}%)"
        },
        legend: {
        // orient: 'vertical',
        bottom: 3,
        data: ['新增人口','之前人口']
         },
        series: [
            {
                name: '新增人口分析',
                type: 'pie',
                radius : '50%',
                center: ['50%', '50%'],
                data:[
                    {value:35, name:'新增人口'},
                    {value:310, name:'之前人口'}
                ],
                avoidLabelOverLap: true,
                label: {
                normal: {
                    textStyle: {
                        color: '#05d2e2'
                    }
                }
                },
                itemStyle: {
                    emphasis: {
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
      getdataSex:function () {
      this.axios.get('http://110.53.162.165:5050/api/count/pmBySex').then(function(res) {
        console.log(res.data)
              this.polar.series.data[0].value=res.data.data.man
              this.polar.series.data[1].value=res.data.data.woman
              this.polar.series.data[2].value=res.data.data.msun

        // 请求成功函数  
                },function(){
                  console.log('请求失败处理');
              });
    }
  },
  mounted() { //函数初始化
      this.getdataSex()
  }
  
}
</script>

