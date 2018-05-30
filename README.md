# ------架构治理平台开发方案-----
## 表结构设计
### 数据采集频率(准实时、少于N次/天)
### 采集一次数据量
### 表理论每日增量
### 单表最大数据量级
### 数据保留时间
### 建表方式(新建、复用单表或日表等)
## 查询功能模块设计
### 查询时间方式(特定时间选择or可选择时间区间)
### 查询耗时要求
### 是否有最大查询时间跨度限制
### 是否支持结果导出
### 是否存在环比变化计算
### 环比变化计算参照时间是否可选择
### 是否有曲线图展示模块
#### echarts规范/近7天、近30天曲线(同理)
##### [Echarts官网](http://echarts.baidu.com/examples/ "Title")
>   
    var myChart = echarts.init(Page.findId('archiIndexViewMax')[0]);
            myChart.showLoading({
                text: '读取数据中...' //loading，是在读取数据的时候显示
            });            
			var option = {
				title : {
			        text: '指标情况',
			        subtext: '数据采集截止时间：XX月XX日XX:XX'
			    },
			    tooltip : {
			        trigger: 'axis'
			    },
			    legend: {
			        data:['营业库A','营业库B','营业库C','营业库D','渠道资源库']
			    },
			    toolbox: {
			        show : true,
			        feature : {
	                    restore: { //重置
	                        show: true
	                    },
	                    dataZoom: { //数据缩放视图
	                        show: true
	                    },
	                    saveAsImage: {//保存图片
	                        show: true
	                    },
	                    magicType: {//动态类型切换
	                        type: ['bar', 'line']
	                    },
				calculable : true,
			    xAxis : [
			        {
			            type : 'category',
			            boundaryGap: false,
			            data : ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月']
			        }
			    ],
			    yAxis : [
			        {
			            type : 'value'
			        }
			    ],
			    series : [
			        {
			            name:'营业库A',
			            type:'line',
			            data:[0, 0, 0, 0, 16970, 14747, 4012, 0, 0, 0, 0, 0],
			            markLine : {
                			data : [{type : 'average', name: '平均值'}]
            			}
			        },
			        {
			            name:'营业库B',
			            type:'line',
			            data:[0, 0, 0, 0, 18045, 15594, 4012, 0, 0, 0, 0, 0],
			            markLine : {
                			data : [{type : 'average', name: '平均值'}]
            			}
			        },
			        {
			            name:'营业库C',
			            type:'line',
			            data:[0, 0, 0, 0, 17468, 15024, 4012, 0, 0, 0, 0, 0],
			            markLine : {
                			data : [{type : 'average', name: '平均值'}]
            			}
			        },
			        {
			            name:'营业库D',
			            type:'line',
			            data:[0, 0, 0, 0, 17909, 15358, 4012, 0, 0, 0, 0, 0],
			            markLine : {
                			data : [{type : 'average', name: '平均值'}]
            			}
			        },
			        {
			            name:'渠道资源库',
			            type:'line',
			            data:[0, 0, 0, 0, 19932, 19793, 4012, 0, 0, 0, 0, 0],
			            markLine : {
                			data : [{type : 'average', name: '平均值'}]
            			}
			        }
			    ]
			};
			if(json && json.data) {
				option.legend.data = json.data.legend;
				option.series = json.data.series;
				option.xAxis[0].data = json.data.xAxis;
				option.title.subtext=cache.deadline;
			}
			//加载前数据刷新
			myChart.clear();
			myChart.setOption(option);	
			myChart.hideLoading();//隐藏loading

			window.onresize = myChart.resize;
			Page.findId('archiIndexViewMax').resize(function(){
                myChart.resize();             
            });
		
		
### extend
### 文件上传、下载

