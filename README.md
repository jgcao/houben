# 获取出借信息和保单号
#### 第1步，下载chrome浏览器
![chrome](images/chrome_download.png)
#### 第2步，打开厚本网站，并登录
![chrome](images/houben_chujie.png)
#### 第3步，打开开发者模式，切换到console菜单
![chrome](images/houben_dev.png)
![chrome](images/houben_console.png)
#### 第4步，加载插件，分别执行如下代码
1、加载jquery
```
var _hmt=_hmt||[];!function(){var e=document.createElement("script");e.src="https://code.jquery.com/jquery-1.11.3.js";var t=document.getElementsByTagName("script")[0];t.parentNode.insertBefore(e,t)}();
```
2、加载cookie
```
var _hmt=_hmt||[];!function(){var e=document.createElement("script");e.src="https://cdn.bootcss.com/jquery-cookie/1.4.1/jquery.cookie.js";var t=document.getElementsByTagName("script")[0];t.parentNode.insertBefore(e,t)}();
```

![chrome](images/houben_plugin.png)

#### 第5步，初始化函数，执行一下代码

```js
// 进行中的出借记录
function get_current_chujie(cj_page=1){

	var pageSize = 10;
	var token = $.cookie('token');
	var auth_token = $.cookie('authtoken');
	
	// 进行中的出借记录
	var cj_url = 'https://www.houbank.com/webapi/businessapi/asset/getInvestListNew?t=1574123289535&current='+cj_page+'&pageSize='+pageSize+'&newOrderCondition=1&orderStatus=1';
	$.ajax(
        {
            url:cj_url,
            type:'get',
            headers:{'token':token,'authtoken':auth_token},
            success:function(result){

            	if(result.resultCode == '00000' && result.result.list != null){

					if(cj_page == 1){
						console.log('共有'+result.result.total+'个进行中的出借记录');
						console.log('出借序号,产品名称,出借金额,价值金额,到期时间,获取保单号函数');
					}

					cj_list = result.result.list;
					cj_len = cj_list.length;

					for(i=0;i<cj_len;i++){
						cj_id = cj_list[i].orderId;
						cj_title = cj_list[i].financeName;
						cj_tz_je = cj_list[i].investAmount;
						cj_jz_je = cj_list[i].orderValue;
						cj_ex_time = cj_list[i].expireDate;
						cj_function = 'get_zaiquan('+cj_id+')';
						cj_msg = cj_id+','+cj_title+','+cj_tz_je+','+cj_jz_je+','+cj_ex_time+','+cj_function;
						console.log(cj_msg);

					}

					if(cj_len == pageSize){
						cj_page++;
						setTimeout(get_current_chujie(cj_page,auth_token),interval*pageSize);
					}

				}else{
					console.log('获取第'+cj_page+'页失败:'+cj_url);
				}
            },
            error:function(data){
            	console.log('获取第'+cj_page+'页失败:'+cj_url);
            }
        }
    );

}

// 转让中的出借记录
function get_transfer_chujie(cj_page=1){

	var pageSize = 10;
	var token = $.cookie('token');
	var auth_token = $.cookie('authtoken');
	
	// 转让中的出借记录
	var cj_url = 'https://www.houbank.com/webapi/businessapi/asset/getInvestListNew?t=1574123289535&current='+cj_page+'&pageSize='+pageSize+'&newOrderCondition=1&orderStatus=3';
	$.ajax(
        {
            url:cj_url,
            type:'get',
            headers:{'token':token,'authtoken':auth_token},
            success:function(result){

            	if(result.resultCode == '00000' && result.result.list != null){

					if(cj_page == 1){
						console.log('共有'+result.result.totalNum+'个转让中的出借记录');
						console.log('出借序号,产品名称,出借金额,价值金额,到期时间,获取保单号函数');
					}

					cj_list = result.result.list;
					cj_len = cj_list.length;

					for(i=0;i<cj_len;i++){
						cj_id = cj_list[i].orderId;
						cj_title = cj_list[i].financeName;
						cj_tz_je = cj_list[i].investAmount;
						cj_jz_je = cj_list[i].orderValue;
						cj_ex_time = cj_list[i].expireDate;
						cj_function = 'get_zaiquan('+cj_id+')';
						cj_msg = cj_id+','+cj_title+','+cj_tz_je+','+cj_jz_je+','+cj_ex_time+','+cj_function;
						console.log(cj_msg);

					}

					if(cj_len == pageSize){
						cj_page++;
						setTimeout(get_transfer_chujie(cj_page,auth_token),interval*pageSize);
					}

				}else{
					console.log('获取第'+cj_page+'页失败:'+cj_url);
				}
            },
            error:function(data){
            	console.log('获取第'+cj_page+'页失败:'+cj_url);
            }
        }
    );

}

// 获取债权
function get_zaiquan(order_id,zq_page=1){
	var pageSize = 10;
	var interval = 2000;

	console.log(order_id+',获取第'+zq_page+'页开始');

	var zq_url = 'https://www.houbank.com/webgateway/web-api/personalCenter/myOrderDetailV2?t=1573945252932&orderId='+order_id+'&pageNum='+zq_page+'&pageSize='+pageSize;
	$.get(zq_url,function(result){
		if(result.resultCode == '00000' && result.result.list != null){

			if(zq_page == 1){
				console.log('共有'+result.result.total+'个债权');
				console.log('出借序号,债权编号,出借金额,保单号');
			}

			zq_list = result.result.list;
			zq_len = zq_list.length;

			for(i=0;i<zq_len;i++){

				// 获取保单序号
				bid_id = zq_list[i].bidid;
				credit_amount = zq_list[i].creditamount;
				
				setTimeout(get_baodan(order_id,bid_id,credit_amount),interval*i);

			}

			if(zq_len == pageSize){
				zq_page++;
				setTimeout(get_zaiquan(order_id,zq_page),interval*pageSize);
			}

		}else{
			console.log(order_id+',获取第'+zq_page+'页失败:'+zq_url);
		}
	});
}

// 获取保单
function get_baodan(order_id,bid_id,credit_amount,try_time=1){
	
	// 获取保单号
	var bd_url = 'https://www.houbank.com/webapi/businessapi/loan/queryPolicyInformationByLoanId?t=1573943324842&loanId='+bid_id;
	$.get(bd_url,function(result){
		if(result.resultCode == '00000' && result.result != null){
			bd_id = result.result.policyNo;
			bd_msg = order_id + ',' + bid_id + ',' + credit_amount + ',' + bd_id;
			console.log(bd_msg);
		}else{
			if(try_time >= 4){
				bd_msg = '获取失败:'+bd_url;
				console.log(bd_msg);
			}else{
				try_time++;
				get_baodan(order_id,bid_id,credit_amount,try_time);
			}
		
		}
	});
}
```
![chrome](images/houben_function.png)

#### 第6步，获取出借信息，执行如下代码

```
// 进行中的出借记录
get_current_chujie();

// 转让中的出借记录
get_transfer_chujie();
```
执行结果

```
共有1个转让中的出借记录
出借序号,产品名称,出借金额,价值金额,到期时间,获取保单号函数
102153249,厚保宝180,7578.10,8337.55,2019/08/10 07:08:37,get_zaiquan(102153249)
```
![chrome](images/houben_chujiezhong.png)
![chrome](images/houben_zhuanrangzhong.png)

#### 第7步，获取保单信息，执行如下

```
// 获取债权和保单，执行上1步执行结果中的“获取保单号函数”，一个一个执行
get_zaiquan(192123249);
```
执行结果

```
出借序号,债权编号,出借金额,保单号
192123249,342279,57.79,01185148880013A3020453
192123249,247136,17.88,01185147890013A3010749
```

![chrome](images/houben_baodan.png)

#### 问题提醒

1、获取保单信息，完全取决于厚本网站的稳定性

2、提示获取信息失败，可以重试

3、重试的方式，可以改变代码中的pageSize，可以改为5或者15等等
