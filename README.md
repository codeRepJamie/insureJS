[功能地址](https://supertest.bxr.im/mall_products/filling/XH_XHRS_0002?saleChannel=xrk_drp_app&d_id=469857027848306688&c_id=469857027848306688&xrk_is_share=0&data=%7B%22birthday%22:%221999-12-22%22,%22age_desc%22:%2218-49%E5%91%A8%E5%B2%81%22,%22policy_period%22:%221%E5%B9%B4%22,%22sex%22:%22%E7%94%B7%22,%22policy_amount%22:%2210%E4%B8%87%22%7D&_hashTime=151382923314340)
[js 地址](https://assets.xrkcdn.com/frontend-m/xrk-mall-frontend/xrk-mall-concat/static/cdn/js/insure_f5c1e32.js)

insure.js分析
===============================================================================================

## Object  window.dateSelect ##
### 日期选择控件封装 （new_toubao_selector.js） ###

## Function INSURE.init ## 
### 主程序 （67-146） ###

-	[cp1]（71）根据不同环境，调用不同的接口地址

-	[fn1]（87）初始化页面dom骨架

-	[fn1] **INSURE.dataSetting**（90）初始化页面数据,并在数据设置完毕后回填上次用户输入的数据

-	[cp4]（100）批量插入需要初始化的方法

-	[cp5]（107）事件绑定

-	[cp6]（114-121）IOS 安卓 版本开始支持选择客户

-	[cp7]（124）禁止分享（调用微信方法）

-	[cp8]（136）续保提示 （某些情况下进入页面调用theme提示）

-	[cp9]（144）检查登录

-	[cp10]（147）查找url是否有is_novice，在保费旁边显示信息


## Function INSURE.dataSetting ##
### 初始化页面数 （488-494） ###
>这里主要是处理

*   params:
    *   sec_time_ajax 是否首次请求(不是第一次请求)
    *   callback 回调函数

*   vars:
    *   ajaxData 记录对象
    
*   相关变量:
    *   init_dataSetting

*   详细:
    *   [if] 判断是否首次请求
        *  [clip] (504-560) 如果需要刷新，则从内存或者页面中拿东西出来记录于ajaxData
    *   [else]
        *  [clip] ajaxData 记录 起保日期 为续保日期

    *   [if]首次执行的时候
        *   必须使用URL中传过来的参数去请求，获取最新的价格，这里覆盖上面所有的数据
        *   获取url上的data参数，设置ajaxData(这里完全可以使用SessionStroage)

    *   [if]查找page_option.mallOrderNo
        *   记录ajaxData

    *   [fetch fn] 服务器数据 url:/mall_products/fetchCheckProductData
        >   获取投保产品的信息内容，用来设置选项内容,存放于一个叫reset_data变量

        *   vars: reset_data 存放信息
            *   包括:
                1.   被保人与投保人关系：insured_relation
                2.   投保日期？：policy_date
                3.   组合属性:...(出生日期、承保日期、保障期限、性别、保障额度)

        *   [forEach] 遍历数据源 reset_data.prem_calc_attr
        
            *   [fn1] set_option(数据源) 开始根据数据植入ui视图
            
            *   [if] 根据服务器数据判断该产品是否限制被保人的出生日期 设置 身份证年龄范围
                *   根据对象 是range_ext（单独型）或 （范围型） 进行判断
                    用来设置检查身份证年龄范围 min 和 max (INSURE.vars.age_area)
                    
            *   [if] 根据服务器数据判断该产品是否有性别范围限制
                *   向INSURE.vars.sex_area数组添加元素

        *   [clip]设定起保时间范围
            *   获取起保时间
            *   调用控件dateSelect.refresh 选择确定按钮同时更改终保时间
        
        *   [local fn2] settingDate(起保时间) 设定终保时间范围(根据起保时间)
        
        *   [clip]设置与被保人的关系范围
            * [if]判断初始化过与未初始化
                * 如果还没初始化过，就要创建option
            * [else]
                * 如果已经初始化过，就直接用上次选择的选项
                
        *   [clip]设置价格
            *   **[fn] INSURE.change_option** 数据驱动
            *   _callback()回调
            
            *   [if] 普通h5页面
                *   刷新卡券数据
                *   刷新总价格
            *   [else] app
                *   刷新总价格
        
        *   调用  **[fn]EXTRA.after_data_setting**(服务器返回的数据)
        
        *   **[local fn2]settingDate**
            >   设置终保时间 
            *    [if]判断INSURE.vars.period没有A设置多少岁到期
            *    [else] 到期设置时间到最后一天
            
            *   **[fn] EXTRA.policy_end_date()** 根据不同保险公司做不同的hackCode

        *   **[local fn1]set_option**  
            >   根据数据源查找en_name定位select DOM,然后插入option
            *   params:
                *   params reset_data.prem_calc_attr单个元素
            
            *   vars:
                *   optionsText 记录option DOM 字符串
    
            *   [clip]生成投标选项按钮,option
    
            *   根据en_name定位,然后插入option
                *   [if] 如果模型type为fixed 直接设置 选项为input
                    *   查找dom插入option
                *   [else]
                    *   [if]    处理同方全球多倍保设置一些特殊选项
                        *   同方全球多倍保选择日期禁止保额变化，如变化提示错误
                        *   校验一下 被保人和投保人 身份证年龄是否高于最高保额 是否去除 verify_error
                    *   [else]  
                        *   查找dom插入input



## Function EXTRA.after_data_setting ## 
### data_setting之后补充步骤 （3428-3509） ###
>   *   主要是根据不同的产品做一些投保信息选项规则不同的修改
>    *   选择本人与非本人关系时候 投保年龄范围 变化 ，重复？
>    *   如果支持银联操作添加银行