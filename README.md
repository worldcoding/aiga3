# -------------架构治理平台开发方案-----------
摘要：
       近日，随着架构治理平台的开发和使用发现，平台陆续出现很多改造和优化的工作。造成的返工的原因一方面是项目需初期只顾及功能的实现，没有考虑到长久的宏观上的考虑，比如，以八大军规为例，数据库中部分表的数据量累计过大（数百万级别），导致页面出现卡顿延迟现象，另一方面是前期开发需求不明确，没有一定的开发需求标准，为了今后提高大家的工作效率避免多次重复性返工，现展开一下初步的开发需求的标准进行统一研讨指定。
## 一、表结构设计
### 数据采集频率(准实时、少于N次/天)
### 采集一次数据量
### 表理论每日增量
### 单表最大数据量级
### 数据保留时间
### 建表方式(新建、复用单表或日表等)
## 二、查询功能模块设计
### 查询时间方式(特定时间选择or可选择时间区间)
### 查询耗时要求
### 是否有最大查询时间跨度限制
### 是否支持结果导出
项目对于Excel导出功能已经封装到ExcelExportController中，
> 	
	public HSSFWorkbook export(List<Map> list) {  
        HSSFWorkbook wb = new HSSFWorkbook();  
        List<HSSFSheet> sheetList = new ArrayList<HSSFSheet>();
        List<HSSFRow> rowList = new ArrayList<HSSFRow>();
        int[] num = new int[9];
        for(String excelSheetBase : excelSheet) {
        	HSSFSheet sheet = wb.createSheet(excelSheetBase);
        	sheetList.add(sheet);
        	HSSFRow row = sheet.createRow((int) 0);
        	rowList.add(row);
        }
        HSSFCellStyle style = wb.createCellStyle();  
        style.setAlignment(HSSFCellStyle.ALIGN_CENTER);  
        for (int i = 0; i < excelHeader.length; i++) {
        	for(int j=0; j < rowList.size(); j++) {
        		HSSFCell cell = rowList.get(j).createCell(i);
	            cell.setCellValue(excelHeader[i]);  
	            cell.setCellStyle(style); 
	            if("1,2,3,4,8".contains(String.valueOf(i))) {
	            	sheetList.get(j).setColumnWidth(i, 12 * 256);
	            } else if(i==5) {
	            	sheetList.get(j).setColumnWidth(i, 60 * 256);  
	            } else {
	            	sheetList.get(j).setColumnWidth(i, 20 * 256);
	            }	         
        	}
         // sheet
        }
        for (Map data : list) {  
        	int index = Integer.parseInt(String.valueOf(data.get("idThird")))/10000000;
        	index--;
        	HSSFRow row =sheetList.get(index).createRow(++num[index]);  
        	row.createCell(0).setCellValue(String.valueOf(data.get("name")).replace("null", ""));
        	row.createCell(1).setCellValue(String.valueOf(data.get("idThird")).replace("null", ""));
        	row.createCell(2).setCellValue(String.valueOf(data.get("firName")).replace("null", ""));
        	row.createCell(3).setCellValue(String.valueOf(data.get("secName")).replace("null", ""));
        	row.createCell(4).setCellValue(String.valueOf(data.get("belongLevel")).replace("null", ""));
        	row.createCell(5).setCellValue(String.valueOf(data.get("systemFunction")).replace("null", ""));
        	row.createCell(6).setCellValue(String.valueOf(data.get("department")).replace("null", ""));
        	row.createCell(7).setCellValue(String.valueOf(data.get("projectInfo")).replace("null", "")); 
        	row.createCell(8).setCellValue(String.valueOf(data.get("designInfo")).replace("null", ""));
        	row.createCell(9).setCellValue(String.valueOf(data.get("codeName")).replace("null", ""));
        }  
        return wb;  
    } 

	public @ResponseBody void sysMessageExport(HttpServletRequest request, HttpServletResponse response) throws IOException {
		List<Map> findData = architectureThirdSv.excelExport(0L,"");
		Collections.sort(findData, new sysExportComparator());
        HSSFWorkbook wb = export(findData);  
        response.setContentType("application/vnd.ms-excel");  
        Date nowtime = new Date();
        DateFormat format=new SimpleDateFormat("yyyyMMdd");
        String time=format.format(nowtime);
        response.setHeader("Content-disposition", "attachment;filename="+new String("系统基线_".getBytes(),"iso-8859-1")+time+".xls");  
        OutputStream ouputStream = response.getOutputStream();  
        wb.write(ouputStream);  
        ouputStream.flush();  
        ouputStream.close(); 
	}
### 是否存在环比变化计算
### 环比变化计算参照时间是否可选择
### 是否有曲线图展示模块
#### echarts规范/近7天、近30天曲线(同理)
1、项目引入图形化数据展示可以使用Echats组件， [Echarts官网](http://echarts.baidu.com/examples/ "Title")
> require("lib/ztree/3.5.28/js/jquery.ztree.excheck.min.js");

2、然后在绘图前我们需要为 ECharts 准备一个具备高宽的 DOM 容器。

> <body>
    <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
</body>

3、然后就可以通过 echarts.init 方法初始化一个 echarts 实例并通过 setOption 方法生成一个简单的柱状图，下面是完整代码。

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

