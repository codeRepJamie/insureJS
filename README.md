[功能地址](https://supertest.bxr.im/mall_products/filling/XH_XHRS_0002?saleChannel=xrk_drp_app&d_id=469857027848306688&c_id=469857027848306688&xrk_is_share=0&data=%7B%22birthday%22:%221999-12-22%22,%22age_desc%22:%2218-49%E5%91%A8%E5%B2%81%22,%22policy_period%22:%221%E5%B9%B4%22,%22sex%22:%22%E7%94%B7%22,%22policy_amount%22:%2210%E4%B8%87%22%7D&_hashTime=151382923314340)
[js 地址](https://assets.xrkcdn.com/frontend-m/xrk-mall-frontend/xrk-mall-concat/static/cdn/js/insure_f5c1e32.js)

insure.js分析



Function（67-146）init 第一步进入 {

	[cp1]（71）根据不同环境，调用不同的接口地址

	[fn1]（87）初始化页面dom骨架

	[fn1] INSURE.dataSetting（90）初始化页面数据,并在数据设置完毕后回填上次用户输入的数据

	[cp4]（100）批量插入需要初始化的方法

	[cp5]（107）事件绑定

	[cp6]（114-121）IOS 安卓 版本开始支持选择客户

	[cp7]（124）禁止分享（调用微信方法）

	[cp8]（136）续保提示 （某些情况下进入页面调用theme提示）

	[cp9]（144）检查登录

	[cp10]（147）查找url是否有is_novice，在保费旁边显示信息

}

Function（488-935） INSURE.dataSetting 初始化页面数据
{
	params:
        sec_time_ajax 是否首次请求(不是第一次请求)
        callback 回调函数

    vars: ajaxData 记录对象

    body:
        if 判断是否首次请求
            [fn1]（496）如果需要刷新，则从内存或者页面中拿东西出来
        else
            设置ajaxData 起保日期 为续保日期

        //首次执行的时候必须使用URL中传过来的参数去请求，获取最新的价格，这里覆盖上面所有的数据
        获取url上的data参数，设置ajaxData(这里完全可以使用SessionStroage)

        fetch服务器数据 /mall_products/fetchCheckProductData
        获取投保产品的信息内容，用来设置选项内容
        包括:{
            被保人与投保人关系：insured_relation
            投保日期？：policy_date
            组合属性:...(出生日期、承保日期、保障期限、性别、保障额度)

        }

            forEach 遍历数据源
               [fn2] set_option(数据源) 开始植入

    fn:
        [fn2]set_option（数据源）查找en_name定位,然后插入option

            生成option

            根据en_name定位,然后插入option

            if 同方全球多倍保
                设置一些特殊选项
            else





	相关变量 init_dataSetting
}

