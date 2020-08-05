---
此文档记录平安电子保单转base64的接口开发过程
---

## 对接接口请求参数信息

| **序号** | **名称**             | **代码**         | **类型** | **长度** | **非空** | **备注**                                                     |
| -------- | -------------------- | ---------------- | -------- | -------- | -------- | ------------------------------------------------------------ |
| 1        | 附件类型             | fileType         | String   | 2        | Y        | 影像类型：  01电子发票  02电子保单  03电子批单           保证唯一性 |
| 2        | 附件base64串         | fileBase64String | String   |          | Y        | 附件base64串                                                 |
| 3        | 单证类型             | documentType     | String   | 2        | Y        | 单证类型：  01：保单 02：批改单                              |
| 4        | 单证号               | documentNo       | String   | 20       | Y        |                                                              |
| 5        | 交强险标志base64数据 | trafficMark      | String   |          | CY       | 交强险强标数据，当为交强险时需要传                           |

可以跟据CarPlatformRpcTaskService.taskSendGZJBPlatformInfoPDF贵州机构的电子批、保单进行修改

## 贵州机构电子批、保单

### taskSendGZJBPlatformInfoPDF

- 获取开始手机和结束时间，作为参数传入`JBPlatformConnectTaskPDF`

```java
	public void taskSendGZJBPlatformInfoPDF() {
		log.warn("开始上传贵州警保电子保、批单...");
		try{
			Date endDate = DateUtils.trunc(new Date(), DateUtils.DAY);
			Date startDate = DateUtils.addDays(endDate, -1);
			gzCarJBPlatformConnect.JBPlatformConnectTaskPDF(startDate, endDate);
		} catch (Exception e) {
			e.printStackTrace();
		}
		log.warn("上传贵州警保电子保、批单结束...");
	}
```

### JBPlatformConnectTaskPDF

- 判断日期参数非空

- 上传pdf，

  ```java
  // PDF文件上传
  logger.error("PDF文件上传方法开始执行*************开始时间：{}，结束时间：{}", startDate,endDate);
  //接收PDFConnect返回的一组GZCarJBPlatformConnectLog
  List<GZCarJBPlatformConnectLog> savePDFConnectLog = this.PDFConnect( startDate, endDate );
  //List非空
  if (!ObjectUtils.isEmpty(savePDFConnectLog)) {
      //遍历List
  	for (GZCarJBPlatformConnectLog gzCarJBPlatformConnectLog : savePDFConnectLog) {
  		//设置id，开始时间。结束时间，表类型等
          gzCarJBPlatformConnectLog.setId(UUID.randomUUID().toString(). toUpperCase().replace("-", ""));
      	gzCarJBPlatformConnectLog.setStartDate(startDate);
  		gzCarJBPlatformConnectLog.setEndDate(endDate);
  		gzCarJBPlatformConnectLog.setPolicyType("0");// 0是原始表   1是批单表
          //保存表
  		gzCarJBPlatformConnectLogService.save(gzCarJBPlatformConnectLog);
  		}
  	logger.error("PDF文件上传方法执行完毕************");
  }
  ```

- 补送pdf

  ```java
  // 失败PDF根据时间补送
  logger.error("PDF补送方法开始执行*************");
  //找到所有失败的pdf发送，
  List<GZCarJBPlatformConnectLog> gZCarJBPlatformConnectLogList = gzCarJBPlatformConnectLogService				. findLog_GZCarJBPlatformConnectLog(DateUtils.addDays(endDate, -4), DateUtils.addDays(endDate, -1));
  
  if (!ObjectUtils.isEmpty(gZCarJBPlatformConnectLogList)) {
  	for (GZCarJBPlatformConnectLog gzCarJBPlatformConnectLog : gZCarJBPlatformConnectLogList) {
          //获取相关信息
  		String policyNo = gzCarJBPlatformConnectLog.getPolicyNo();
  		Date startDate1 = gzCarJBPlatformConnectLog.getStartDate();
  		Date endDate1 = gzCarJBPlatformConnectLog.getEndDate();
          //若非空
  		if (!ObjectUtils.isEmpty(policyNo)&&!ObjectUtils.isEmpty(startDate1) &&!ObjectUtils.isEmpty(endDate1)) {
  			logger.error("正在执行*************保单号：{}",policyNo);
              //PDFConnectSingle生成新GZCarJBPlatformConnectLog
  			GZCarJBPlatformConnectLog pdfConnectSingle = PDFConnectSingle( gzCarJBPlatformConnectLog,policyNo, startDate, endDate);
  			if (!ObjectUtils.isEmpty(pdfConnectSingle)) {
                  //set参数并补送
  				pdfConnectSingle.setStartDate(startDate1);
  				pdfConnectSingle.setEndDate(endDate1);
  				gzCarJBPlatformConnectLogService.update(pdfConnectSingle);
  			}
  		}
  	}
  logger.error("PDF补送方法执行结束*************");
  }
  ```

### PDFConnect获取电子保单

- 参数：开始时间和结束时间，两个Date
- 

### GZCarJBPlatformConnectLog

- 对应数据库中的表

- 数据准备

  ```java
  logger.error("进入获取电子保单的PDFConnect方法......");
  Date sendTime = new Date();
  AjaxResult ajaxResult = null;
  String filepath = null;
  String type = null;
  String policyNo = null; // 保单号
  String riskCode = null;
  String comCode = null;
  String endorseNo = null; // 批单号
  List<GZCarJBPlatformConnectLog> GZJBList = new ArrayList< GZCarJBPlatformConnectLog
  // 找贵州机构有打印流水号的电子保单信息
  List<PrpCmain> list = prpCmainService.findByUnderWriteEndDateForPDF( startDate, endDate);
  ```

- 如果list非空

  ```java
  if (!ObjectUtils.isEmpty(list)) {
  	for (PrpCmain prpCmain : list) {
          //遍历list（prpcmian）
  		policyNo = prpCmain.getPolicyNo();
  		riskCode = prpCmain.getRiskCode();
  		comCode = prpCmain.getComCode();
  		type="1";
  		GZCarJBPlatformPDFVo json =  new GZCarJBPlatformPDFVo();
  		GZCarJBPlatformPDFInfo pdf_info = new GZCarJBPlatformPDFInfo();
  		GZCarJBPlatformConnectLog gzCarJBPlatformConnectLog = new GZCarJBPlatformConnectLog();
  		if (!ObjectUtils.isEmpty(policyNo)) {
  			pdf_info.setBxdh(policyNo);
  			try {
  				// 电子保单下载全路径(服务器路径)
  				filepath = AppConfig.get("PDF_GZJBPATH") + File.separator + policyNo +".pdf";//PDF_GZJBPATH=/home/weblogic/image/FileManger2019/gzjbPDF
  				logger.error("正在下载电子保单......");
  				ajaxResult = getElecDownedMessage(policyNo,type,riskCode,comCode);//电子保单下载
  				if ("200".equals(Long.toString(ajaxResult.getStatus()))) {
  					// 电子保单上傳
  					logger.error("电子保单开始上传......保单号：{}",policyNo);
  					GZCarJBPlatformConnectLog policyPDFLog = localToUploadPDF(gzCarJBPlatformConnectLog,sendTime,policyNo,filepath,json, pdf_info);
  					policyPDFLog.setPolicyFlag("0");
  					if (!ObjectUtils.isEmpty(policyPDFLog)) {
  						GZJBList.add(policyPDFLog);
  					}
  				}else {
  					gzCarJBPlatformConnectLog.setErrorMessage("没有下载到【"+policyNo+"】PDF文件！");
  					gzCarJBPlatformConnectLog.setResponseCode("0");
  					gzCarJBPlatformConnectLog.setPolicyFlag("0");
  					gzCarJBPlatformConnectLog.setSendTime(sendTime);
  					gzCarJBPlatformConnectLog.setSendJson(filepath);
  					gzCarJBPlatformConnectLog.setPolicyNo(policyNo);
  					GZJBList.add(gzCarJBPlatformConnectLog);
  				}
  			} catch (Exception e1) {
  				logger.error("电子保单【"+policyNo+"】下载或上传失败！", e1);
  			}
  		}
  	}
  }else {logger.error("该时段无电子保单！");}
  ```

  - **电子保单下载全路径(服务器路径)：**

    **filepath = AppConfig.get("PDF_GZJBPATH") + File.separator + policyNo +".pdf";**

### 



# 思路

1. ## 获取保单信息list，并遍历list进行操作

   - 方法名：PDFConnect

2. ## 拼接filepath服务器地址，调用接口下载pdf

   - 方法名：PDFConnect（拼接地址）、getElecDownedMessage（下载pdf到本地）、getElecDownLoad（下载PDF）
   - 问题：下载PDF到本地需要使用AppConfig获取地址，这个地址一个怎么写
   - 问题：filepath里面也要使用AppConfig
   - 

3. ### 下载pdf到本地，处理后发给平安接口

   - 方法名：localToUploadPDF

## 操作表

mqlog表

![image-20200722110640876](%E5%B9%B3%E5%AE%89%E7%94%B5%E5%AD%90%E4%BF%9D%E5%8D%95base64/image-20200722110640876.png)

mq表

![image-20200722110747689](%E5%B9%B3%E5%AE%89%E7%94%B5%E5%AD%90%E4%BF%9D%E5%8D%95base64/image-20200722110747689.png)