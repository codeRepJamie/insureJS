[功能地址](https://supertest.bxr.im/mall_products/filling/XH_XHRS_0002?saleChannel=xrk_drp_app&d_id=469857027848306688&c_id=469857027848306688&xrk_is_share=0&data=%7B%22birthday%22:%221999-12-22%22,%22age_desc%22:%2218-49%E5%91%A8%E5%B2%81%22,%22policy_period%22:%221%E5%B9%B4%22,%22sex%22:%22%E7%94%B7%22,%22policy_amount%22:%2210%E4%B8%87%22%7D&_hashTime=151382923314340)
[js 地址](https://assets.xrkcdn.com/frontend-m/xrk-mall-frontend/xrk-mall-concat/static/cdn/js/insure_f5c1e32.js)

insure.js分析
===============================================================================================

## Object  window.dateSelect ##
> 日期选择控件封装 （new_toubao_selector.js）

## Object  window.INSURE ##
> 页面主程序 （2-2419）
> 19fn
> 2 attr

## Object INSURE.vars ##
>  全局变量 （4-66）

> 这里主要是干啥的？
>    1.  定义一下这个页面全局的变量

## Function INSURE.init ## 
> 主程序 （69-150）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    1.  下面列出,这里比较乱

-	[clip]（71）根据不同环境，调用不同的接口地址

-	[INSURE.renderDomContent] (page_option.config)初始化页面dom骨架

-	[INSURE.dataSetting] (false)初始化页面数据,并在数据设置完毕后回填上次用户输入的数据
        -   回调方法
                -   [INSURE.init_back_data]数据回填 ???
                -   [RENEWAL.renewal_relation_hover] (true:第一次) 当续保情况下保证被保人证件类型与证件号不允许修改（大量操作DOM）
                -   [if]APP
                    -   [COUPON.load_coupon] (INSURE.vars.total_price) 开启卡劵

-	[clip]批量插入需要初始化的方法
        -   [RENEWAL.renewal_info] 续保信息控制模块
        -   [EXTRA.init] 扩展模块初始化 (应该是)
        -   [BENIFIT.benifit_verify]

-	[clip]（107）事件绑定

-	[clip]（114-121）IOS 安卓 版本开始支持选择客户

-	[clip]（124）禁止分享（调用微信方法）

-	[clip]（136）续保提示 （某些情况下进入页面调用theme提示）

-	[clip]（144）检查登录

-	[clip]（147）查找url是否有is_novice，在保费旁边显示信息


## Function INSURE.renderDomContent ##
> 初始化页面dom骨架 （324-401）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    1.  根据后端设置（page_option.config）渲染基本投保选项

>   params:
>    *   data 后端设置page_option.config

*   详细:
    *   [forEach] 遍历data (索引是key),建立投保选项DOM
            *  [switchCase] 'benifit_info'
                    *  多个受益人添加模板 [BENIFIT.benifit_render] (根据data单项)
                    *  插入到$('.benifit_info')
                    *  绑定$('.benifit_info select'') change事件,各种条件各种坑
                        *   [if] 它的值是 "指定受益人" 需要按条件操作
                            *   [BENIFIT.benifit_sort] 重新排序受益人的标题 obj_type 以及序号
    *   [EXTRA.after_render] 补充设置 根据url pay_term 做一些显示隐藏设置

## Function INSURE.init_back_data ##
> 数据回填 （950-1086）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    1.  获取待续保订单数据
>    2.  根据数据.对保费描述与待续保相关操作 （RENEWAL模块)
>    3.  根据数据，对BENIFIT模块进行相关操作（COUPON模块)
>    4.  根据数据，更新所有控件

>   vars:
>    *   _data [Object]  page_option.renewalInsureData 续保单数据,后端给过来

> 具体操作
>    1. 获取 续保订单数据 ，三个来源
>          1. 一个是 localStorage 的 insured_back_data  
>          2. 在后台获取 
>          3. 从详情页带过来的数据
>    2. 获取 续保订单数据 做了一些hack
>    3. 根据_data 判断 policy_clauses 做了一大堆 RENEWAL模块操作，例如 续保顶部添加保费描述，修改续保模块 一些用户资料
>    4. 提交按钮变成立即续保
>    5. _data.discounts如果有的话，操作COUPON模块一些基本值
>    6. 根据_data,对保险期限赋值
>    7. 如果，_data有benifits这个值 调用[BENIFIT.benifit_backData]
>    8. 设定起保时间范围，更改dateSelect插件 dateSelect.setDate
>    9. 再次[INSURE.dataSetting] (true)更新所有控件
>    10. 再次[RENEWAL.selectRenewalType] 续保状态下，对select节点更改

## Function INSURE.dataSetting ##
> 初始化页面数 （488-949）
> CALL IN TEXT :10

> 这里主要是干啥的？
>    1.  判断是否首次加载，通过url做数据持久化，添加到服务器请求参数
>    2.  根据服务器请求参数，请求服务器，拿出保单的数据
>    3.  根据返回来的数据，再按照某些特定产品规则，添加独有的数据逻辑。
>    4.  查找DOM节点，添加或修改节点（select，input）

>   params:
>    *   sec_time_ajax 是否首次请求(不是第一次请求)
>    *   callback 回调函数

>   vars:
>    *   ajaxData 记录对象，用于请求服务器数据
    
>   相关变量:
>    *   init_dataSetting

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

        *   vars: reset_data 存放信息,应该是用来记录服务器数据，用于设置选项DOM的值
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
            *   [INSURE.change_option] 根据数据,投保页的投保人，与被保人隐藏、显示项目
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

## Function INSURE.change_option ##
> 根据数据,投保页的投保人，与被保人, 多个受益人,隐藏、显示项目，比较常规 （1157-1265）
> CALL IN TEXT :10

## Function INSURE.percentTurnPoint ##
> 百分比转换为小数点 （1746-1754）
> CALL IN TEXT :2

## Object  window.COMPONENT ##
> ui组件生成 （2421-3317）
> 11 fn

## Object  window.EXTRA ##
> 扩展模块 （3323-3846）
> 9 fn

## Function EXTRA.init ## 
> 额外的初始化方法 （3326-3408）
> CALL IN TEXT :1

> 这里主要是干啥的？
>   1.   根据 page_option.sellerNo 和 page_option.productNo 修改一些投保ui显示

## Function EXTRA.before_data_setting ## 
> data_setting之前补充步骤 （3428-3509）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    *  根据特定的page_option.productNo 与 DOM节点一些值和状态 设置请求参数 ajaxData.data

>   params:
>    *   data ajaxData.data 请求服务器的参数

>   return:
>    *   data ajaxData.data 请求服务器的参数

## Function EXTRA.after_data_setting ## 
> data_setting之后补充步骤 （3428-3509）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    *   主要是根据不同的产品做一些投保信息选项规则不同的修改
>    *   选择本人与非本人关系时候 投保年龄范围 变化 ，重复？
>    *   如果支持银联操作添加银行

## Object  window.BENIFIT ##
> 多收益人模块 （3850-4462）
> 10 fn

## Function BENIFIT.benifit_lot_change ## 
> lot根据选择自动变化最大只能100% （3871-3898）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    *  遍历所有的$('.benifits select\[name="lot"\]') 按条件设置 option 和 value
>    *  $('.more-bnfs')按条件隐藏


## Function BENIFIT.benifit_render ## 
> 根据 （3854-3870）
> CALL IN TEXT :1

> 这里主要是干啥的？
>    *   根据data单项 调用[COMPONENT.apply]创建DOM字符串
>    *   保存在 BENIFIT.vars.bnfs_domcontent

## Function BENIFIT.benifit_backData ## 
> 修改benifit各种数据，我也搞唔清楚？？？ （4320-4400）
> CALL IN TEXT :1

>    params:
>    * data: _data.body.products[0].insured[0].benifits(请求服务器的benifits) 

> 这里主要是干啥的？
>    *   [forEach]遍历data数据，设置收益人的日期,然后dateSelect 操作
>    *   [BENIFIT.benifit_sort]受益人排序
>    *   [forEach]遍历data数据
>           *   [INSURE.percentTurnPoint] 百分比转换为小数点
>           *   操作benifit里面$('\[object_type='benifits-" + i + "'\]'')，设置value值
>    *   $('.more-bnfs')显示
>    *   $('.benifit_info select/input') 指定受益人
>    *   [BENIFIT.benifit_lot_change] lot根据选择自动变化最大只能100%



## Function BENIFIT.benifit_sort ## 
> 重新排序受益人的标题 以及 序号benifits-key值（4337-4462）
> CALL IN TEXT :5

> 这里主要是干啥的？
>    *   重新排序受益人的标题 以及 序号benifits-key值
>    *   [BENIFIT.benifit_lot_change] lot根据选择自动变化最大只能100%;
>    *   [INSURE.change_option] 根据数据,投保页的投保人，与被保人隐藏、显示项目;


## Object  window.RENEWAL ##
> 续保模块 （4466-4954）
> 5 fn

## Function RENEWAL.renewal_relation_hover ## 
> 续保订单时候（4845-4953）
> CALL IN TEXT :2

> 这里主要是干啥的？
>    *  续保状态下,设置$('.renewal-hover')
>    *  根据被保人与投保人关系 设置身份证类型置灰 写死值

>    params:
>    * is_first 是否第一次,初始化调用？


*   [if]RENEWAL.vars.certification_type/certification_no为空，什么都不做
*   [if]page_option.mallOrderNo
       *   [if]page_option.mallOrderNo存在
           *   根据条件操作，$('.renewal-hover')
           *   [if] productNo 为 "ZC_GSCX_0001"设置$('.renewal-hover')
           *   [if] 根据某种条件 更新 [INSURE.change_option] "根据数据隐藏、显示项目"
           *   [if] 被保人是自己 投保人的（...选项）添加 $('.renewal-hover')
                   *   [if] RENEWAL.vars.certification_type/certification_no 与原有数据有不同
                   *   投保人信息设置一下
                   *   添加新的 证件类型
                   *   [if] is_first为假 [INSURE.dataSetting] (true)更新所有控件
           *  $(".insure_info .container .option") 被保人？ 身份证类型置灰
           *  [if] 根据被保人与投保人关系 设置证件变灰,不可编辑,然后填入 RENEWAL.vars.certification_type

end
---------------------------------------------------------------------
[INSURE.renderDomContent]: #function-insurerenderdomcontent     "初始化页面dom骨架"
[INSURE.dataSetting]: #function-insuredatasetting     "初始化页面数"
[INSURE.init_back_data]: #function-initbackdata     "数据回填"
[INSURE.change_option]: #function-insurechangeoption     "根据数据隐藏、显示项目"
[INSURE.percentTurnPoint]: #function-percentturnpoint   "百分比转换为小数点[filter]"

[EXTRA.init]: #function-extrainit "INSURE.dataSetting之前要做的事情"
[EXTRA.before_data_setting]: #function-extrabeforedatasetting "INSURE.dataSetting之前要做的事情"
[EXTRA.after_data_setting]: #function-extraafterdatasetting "INSURE.dataSetting之后要做的事情"
[EXTRA.after_render]: #function-extraafterrender " 根据url pay_term 做一些显示隐藏设置"

[BENIFIT.benifit_lot_change]: #function-benifitbenifitlotchange "lot根据选择自动变化最大只能100%"
[BENIFIT.benifit_backData]: #function-benifitbenifitbackdata "INSURE.dataSetting之前要做的事情"
[BENIFIT.benifit_sort]: #function-benifitbenifit_sort "重新排序受益人的标题 obj_type 以及序号 benifits-key"

[RENEWAL.renewal_relation_hover]: #function-renewalrenewalrelationhover "续保订单时候 保证被保人证件类型与证件号不允许修改且与进来的时候 一致"