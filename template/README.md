# 模板介绍
常用模板的定义,后续还有很大提升空间.
##已有模板
* TableTemplate 后台管理用的CURD,带搜索条件,分页的table模板
* TableInfoTemplate 查看某条详情数据的模板,一条详情下面对应多条明细信息的,待后面进一步优化改进.

##目前支持的FORM表单样式
* text 
```text
    input文本框
```
* textarea 
```text
search搜索条件,会被当做input文本框展示
```
* number 
```text
input number类型文本框
```
* options 
```text
单选,下拉chosen选择框
```
* remoteOption 
```text
单选,请求后台服务获取选项,下拉chosen选择框
```
* remoteMultiOption 
```text
多选,请求后台服务获取选项,下拉chosen选择框
```
* radio 
```text
radio选择框
```
* time 
```text
在search搜索条件中,会被当做时间范围选择器(DatePick)展示

```

## 使用方法核心

```javascript
var paramOption = [
                { //最少参数字段,其他字段有默认值
                    code: "userName",
                    name: "用户姓名",
                    viewType: "text",
                }, {
                    code: "loginNo",
                    name: "账号",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "01111",
                }, {
                    code: "idCard",
                    name: "身份证",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "01111",
                }, {
                    code: "email",
                    name: "电子邮箱",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "00111",
                    validate: "required|email"
                },
                {
                    code: "carrierName",
                    name: "承运商",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    searchOptions: [],
                    options: [],
                    display: "01100",
                    validate: "required"
                }, {
                    code: "mobile",
                    name: "手机号",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "11111",
                    validate: "required|phone"
                }, {
                    code: "direction",
                    name: "备注信息",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "00111",
                }, {
                    code: "userStatus",
                    name: "状态",
                    dataType: "String",
                    viewType: "options",
                    default: "",
                    createDefault: 1,
                    searchOptions: [{key: 1, value: "启用"}, {key: 9, value: "停用"}, {
                        key: -1,
                        value: "已删除"
                    }],
                    options: [{key: 1, value: "启用"}, {key: 9, value: "停用"}, {key: -1, value: "已删除"}]
                    ,
                    display: "11111",
                    validate: "required",
                    filter: 'statusFilter'
                },
                {
                    code: "isBlock",
                    name: "拉黑标记",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "00100",
                    filter: 'blockFilter'
                },
                {
                    code: "carrierId",
                    name: "承运商",
                    dataType: "String",
                    viewType: "remoteOption",
                    url: ajax.queryAllCarrierList,//查询所有承运商
                    requestParam: {},//请求参数
                    callback: null,//回调函数,为空则走默认回调处理
                    default: "",
                    searchOptions: [],
                    options: [],
                    display: "10011",
                    validate: "required"
                },
                {
                    code: "accountType",
                    name: "人员类型",
                    dataType: "String",
                    viewType: "remoteMultiOption",
                    url: ajax.queryAllDictType,//查询所有人员类型
                    requestParam: {},//请求参数
                    callback: null,//回调函数,为空则走默认回调处理
                    default: [],
                    searchOptions: [],
                    options: [],
                    display: "00011",
                    validate: "required"
                }, {
                    code: "stationId",
                    name: "归属站点",
                    dataType: "String",
                    viewType: "remoteOption",
                    url: ajax.queryStationList,//查询所有人员类型
                    requestParam: {},//请求参数
                    callback: null,//回调函数,为空则走默认回调处理
                    default: "",
                    searchOptions: [],
                    options: [],
                    display: "00011",
                },
                {
                    code: "createUser",
                    name: "创建人",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "01100",
                }, {
                    code: "created",
                    name: "创建时间",
                    dataType: "String",
                    viewType: "time",
                    default: "",
                    options: [],
                    display: "01100",
                },


            ];

            //请求地址
            var ajaxParam = {
                search: ajax.querySystemList,
                queryOneId: ajax.querySystemById,
                updateData: ajax.saveSystem,
                createData: ajax.saveSystem,
                deleteById: ajax.deleteSystemById,

            };
```
主要是修改参数"paramOption",里面的字段定义信息,以及请求地址"ajaxParam"修改,会自动生成页面模板

