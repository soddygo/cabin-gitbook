# 页面一对多

HTML模板:

```html
<!--如果要保存页面状态 请在同id级添加 keep-alive 标签 并且在js beforeDestroy 方法内去掉Object.assign 方法  <div id="app" keep-alive></div> -->
<div class="cabin-page-vueInfoViewDemo content-container" id="app"
     xmlns:v-timepicker="http://www.w3.org/1999/xhtml" xmlns:v-bind="http://www.w3.org/1999/xhtml"
     xmlns:v-on="http://www.w3.org/1999/xhtml">
    <div class="content-container">
        <!--滚动内容-->
        <div class="container-scroll">
            <div class="stage">
                <div class="stage-title flag">接口授权</div>

                <div class="stage-search" style="padding-bottom: 0px;">
                    <button class="btn btn-outline J_create_btn" v-on:click="updateModel">编辑</button>
                </div>

                <div class="cabin-base-form clearfix">
                    <div class="form-horizontal clearfix">
                        <template v-for="(item,index) in paramOption">
                            <template v-if="item.searchFlag === 1">
                                <div :key="item.id" v-if="item.viewType ==='text'"
                                     class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                                    <!--输入框-->
                                    <label class="control-label">{{item.name}}</label>
                                    <input type="text" class="form-control" v-bind:placeholder="item.name"
                                           v-model="searchParam[item.code]"/>
                                </div>
                                <div :key="item.id" v-else-if="item.viewType ==='number'"
                                     class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                                    <!--数字输入框-->
                                    <label class="control-label">{{item.name}}</label>
                                    <input type="number" class="form-control" v-bind:placeholder="item.name"
                                           v-model="searchParam[item.code]"/>
                                </div>
                                <div :key="item.id" v-else-if="item.viewType ==='options'"
                                     class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                                    <!--下拉框-->
                                    <label class="control-label">{{item.name}}</label>
                                    <vue-chosen type="text" class="form-control" v-bind:placeholder="item.name"
                                                :options="choseOptions"

                                                v-model="searchParam[item.code]">
                                        <option disabled>=={{item.name}}==</option>
                                        <option v-for="option in item.options"
                                                v-bind:value="option.key">
                                            {{option.value}}
                                        </option>
                                    </vue-chosen>
                                </div>
                            </template>

                        </template>

                    </div>
                </div>
                <hr>
                <div class="stage-search right btns-box">
                    <div class="btn btn-primary" v-on:click="update" v-if="modal.operationFlag===3">更新</div>
                    <div class="btn btn-primary" v-on:click="create" v-if="modal.operationFlag===2">保存</div>
                </div>
            </div>

            <div class="stage">
                <!--表格盒子-->
                <div class="stage-title flag">接口参数</div>

                <div class="stage-search" style="padding-bottom: 0px;">
                    <button class="btn btn-outline J_create_btn"
                            v-bind:disabled="Number(modal.operationFlag)===1"
                            v-on:click="addRow">
                        新增一行
                    </button>
                    <span style="font-size:12px">注:参数信息必须和dubbo接口参数顺序和参数名称保持一致！</span>

                </div>
                <hr>
                <!--左右滑动表格-->
                <div class="table-responsive" style="overflow:visible">
                    <!--注:没标题没按钮组表格没上下边线 有标题加table-top-bordered有分页加table-bottom-bordered-->
                    <!--表格-->
                    <table class="cabinTable table table-hover  table-bottom-bordered">
                        <thead>
                        <tr>
                            <th>行号</th>
                            <template v-for="(item,index) in rowParamOption">
                                <template v-if="item.tableFlag ===1">
                                    <th>{{item.name}}</th>
                                </template>
                            </template>
                            <th>操作列</th>
                        </tr>
                        </thead>
                        <tbody>
                        <template v-for="(item,index) in rowData">
                            <tr :key="item.id">
                                <td>
                                    <span>{{index+1}}</span>
                                </td>
                                <template v-for="(itemParam,index) in rowParamOption">
                                    <template v-if="itemParam.tableFlag ===1">
                                        <td v-if="itemParam.viewType ==='text'">
                                            <div>
                                                <input type="text" class="form-control"
                                                       v-bind:placeholder="itemParam.name"
                                                       v-bind:disabled="Number(modal.operationFlag)===1"
                                                       v-model="item[itemParam.code]"/>
                                            </div>
                                        </td>
                                        <td v-else-if="itemParam.viewType ==='options'">
                                            <div>
                                                <vue-chosen type="text" class="form-control"
                                                            v-bind:placeholder="itemParam.name"
                                                            :options="choseOptions"
                                                            v-bind:disabled="Number(modal.operationFlag)===1"
                                                            v-model="item[itemParam.code]">
                                                    <option disabled>=={{itemParam.name}}==</option>
                                                    <option v-for="option in itemParam.options"
                                                            v-bind:value="option.key">
                                                        {{option.value}}
                                                    </option>
                                                </vue-chosen>
                                            </div>
                                        </td>
                                        <!--其他列类型,默认值-->
                                        <td v-else>
                                            {{item[itemParam.code]}}
                                        </td>

                                    </template>
                                </template>
                                <td>
                                    <div>
                                        <span class="event" v-on:click="deleteRow(index)">删除</span>
                                    </div>
                                </td>
                            </tr>
                        </template>

                        </tbody>

                        </tbody>
                    </table>
                    <!--表格 end-->
                </div>
                <!--左右滑动表格 end-->

                <!--分页-->
                <div id="page"></div>
                <!--分页end-->
            </div>
            <div class="stage">
                <div class="cabin-base-form clearfix">
                    <div class="form-horizontal clearfix ">
                        <div class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                            <label class="control-label"><span class="font-error"></span>制单人</label>
                            <div class="base-form-content clearfix">
                                <input type="text" class="form-control" placeholder="制单人"
                                       disabled/>
                            </div>
                        </div>
                        <div class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                            <label class="control-label"><span class="font-error"></span>制单时间</label>
                            <div class="base-form-content clearfix">
                                <input type="text" class="form-control" placeholder="制单时间"
                                       disabled/>
                            </div>
                        </div>
                        <div class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                            <label class="control-label"><span class="font-error"></span>审核人</label>
                            <div class="base-form-content clearfix">
                                <input type="text" class="form-control" placeholder="审核人"
                                       disabled/>
                            </div>
                        </div>
                        <div class="form-group col-lg-3 col-md-3 col-sm-6 col-xs-12">
                            <label class="control-label"><span class="font-error"></span>审核时间</label>
                            <div class="base-form-content clearfix">
                                <input type="text" class="form-control" placeholder="审核时间"
                                       disabled/>
                            </div>
                        </div>
                    </div>
                </div>
            </div>


        </div>
        <!--滚动内容结束-->


    </div>
</div>
```

JS模板
```javascript

define('wormHole/pages/vueInfoViewDemo/vueInfoViewDemo', function (require, exports, module) {

    var ajax = require('wormHole/common/data/ajax');

    require('cabin/lib/daterangepicker/moment.js');
    require('cabin/widgets/pddatepicker/pddatepicker.js');
    require('cabin/lib/daterangepicker/daterangepicker.js');

    MINIPOP = require('cabin/widgets/minipop/minipop');


    var handle, _fn, page;
    page = Page({
        nodeClass: 'cabin-page-vueInfoViewDemo',
        parentClass: 'J_Main', // 没有就直接插入body，或者不插入
        source: ['wormHole/pages/vueInfoViewDemo/tripartiteInfoView.tpl', 'wormHole/pages/vueInfoViewDemo/tripartiteInfoView.css'],
        pageTitle: '接口授权',//当前页面需要展示的title
        cabinVue: {
            //这里this 指向当前VUE 实例,仅支持es5语法！！已经默认添加了timepicker 指令
            //使用vue时　不要使用handle.jView 统一使用$ 筛选或者使用$(this.$el).find(xxx)去实现；this.$el为vue实例dom 节点
            //使用vue 后可以不使用page 的show hide 方法,建议使用vue 自己的 mounted 取代show方法 destroy 取代hide 方法
            el: '#app',
            //这里必须 使用一个方法返回给data
            data: function () {
                //主表参数信息
                var paramOption = [
                    {
                        code: "tripartiteId",
                        name: "三方id",
                        display: '01111',
                        dataType: "String",
                        viewType: "text",
                        default: "",
                        options: []
                    },
                    {
                        code: "tripartiteName",
                        name: "三方名称",
                        dataType: "String",
                        viewType: "text",
                        default: "",
                        options: []
                    },
                    {
                        code: "interfaceCode",
                        name: "接口编码",
                        dataType: "String",
                        viewType: "text",
                        default: "",
                        options: []
                    },
                    {code: "url", name: "接口地址", dataType: "String", viewType: "text", default: "", options: []},
                    {
                        code: "requestType",
                        name: "请求方式",
                        dataType: "Number",
                        viewType: "options",
                        default: "",
                        options: [{key: 1, value: "POST"}, {key: 2, value: "GET"}]
                    },
                    {
                        code: "timeOut",
                        name: "超时时间",
                        dataType: "Number",
                        viewType: "text",
                        default: "",
                        options: [{key: 1, value: "POST"}, {key: 2, value: "GET"}]
                    },
                    {
                        code: "retry",
                        name: "是否重试",
                        dataType: "Number",
                        viewType: "text",
                        default: "",
                        options: [{key: 1, value: "重试"}, {key: 2, value: "不重试"}]
                    },

                ];

                //明细信息
                var rowParamOption = [
                    {
                        code: "paramEnName",
                        name: "参数名称",
                        dataType: "String",
                        viewType: "text",
                        default: "",
                        options: []
                    }, {
                        code: "paramCnName",
                        name: "参数中文名称",
                        dataType: "String",
                        viewType: "text",
                        default: "",
                        options: []
                    }, {
                        code: "position",
                        name: "参数位置",
                        dataType: "Number",
                        viewType: "options",
                        default: 1,
                        options: [{key: 1, value: "Header"}, {key: 2, value: "Body"}]
                    }, {
                        code: "encryption",
                        name: "加密方式",
                        dataType: "Number",
                        viewType: "options",
                        default: 1,
                        options: [{key: 1, value: "无加密"}, {key: 2, value: "RSA(待实现)"}]
                    }, {
                        code: "isEmpty",
                        name: "是否可以为空",
                        dataType: "Number",
                        viewType: "options",
                        default: 1,
                        options: [{key: 1, value: "可以为空"}, {key: 2, value: "禁止为空"}]
                    },
                ];

                //请求地址
                var ajaxParam = {
                    queryOne: ajax.findInterfaceInfoById,
                    updateData: ajax.addOrUpdateInterface,
                    createData: ajax.addOrUpdateInterface,
                    deleteById: null,

                };

                /**
                 * /默认参数属性;
                 * display:第一位表示搜索条件是否展示,第二位表示table列是否展示,第三位表示查看字段显示,第四位表示编辑字段显示,第五位表示新增字段显示;
                 * dataType:后台数据字段类型,特殊字段处理用,比如后期规划时间字段处理展示问题;
                 * viewType:前段展示的数据字段类型;
                 * @type {{display: number, dataType: string, viewType: string, default: string, options: Array}}
                 */
                var defaultParamOption = {
                    display: "11111",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: []
                };

                //1对多关系处理
                //父表字段信息,字段增加uuid,方便Vue高效渲染用
                for (var index in paramOption) {
                    var item = paramOption[index];
                    //默认参数扩展合并
                    item = $.extend({}, defaultParamOption, item);
                    item['id'] = _fn.guid();
                    //处理字段display标记
                    var searchFlag = item.display.substr(0, 1);
                    var tableFlag = item.display.substr(1, 1);
                    var viewFlag = item.display.substr(2, 1);
                    var updateFlag = item.display.substr(3, 1);
                    var createFlag = item.display.substr(4, 1);

                    paramOption[index]['searchFlag'] = Number(searchFlag);
                    paramOption[index]['tableFlag'] = Number(tableFlag);
                    paramOption[index]['viewFlag'] = Number(viewFlag);
                    paramOption[index]['updateFlag'] = Number(updateFlag);
                    paramOption[index]['createFlag'] = Number(createFlag);

                }
                //明细信息,字段增加uuid,方便Vue高效渲染用
                for (var index in rowParamOption) {
                    var item = rowParamOption[index];
                    //默认参数扩展合并
                    item = $.extend({}, defaultParamOption, item);
                    item['id'] = _fn.guid();
                    //处理字段display标记
                    var searchFlag = item.display.substr(0, 1);
                    var tableFlag = item.display.substr(1, 1);
                    var viewFlag = item.display.substr(2, 1);
                    var updateFlag = item.display.substr(3, 1);
                    var createFlag = item.display.substr(4, 1);

                    rowParamOption[index]['searchFlag'] = Number(searchFlag);
                    rowParamOption[index]['tableFlag'] = Number(tableFlag);
                    rowParamOption[index]['viewFlag'] = Number(viewFlag);
                    rowParamOption[index]['updateFlag'] = Number(updateFlag);
                    rowParamOption[index]['createFlag'] = Number(createFlag);

                }

                //搜索条件
                var searchParam = {};
                for (var index in paramOption) {
                    var item = paramOption[index];
                    searchParam[item.code] = item.default;
                }

                console.log(JSON.stringify(searchParam))

                //空白行
                var blankRow = {};
                for (var index in rowParamOption) {
                    var item = rowParamOption[index];
                    blankRow[item.code] = item.default;
                }


                //modal状态记录
                var modal = {
                    operationFlag: 1,//1:查看; 2:新增; 3:修改
                };
                //明细
                var rowData = [];

                //分页信息
                var page = {
                    pageNo: 1,
                    pageSize: 30,
                    totalCount: 0,
                };
                //chose参数
                var choseOptions = {
                    no_results_text: '没有找到',
                    search_contains: true, //关键字模糊搜索，设置为false，则只从开头开始匹配
                    max_selected_options: 1, //当select为多选时，最多选择个数
                    width: '100%'
                };
                //不带搜索框chose
                var choseOptionsNoSearch = {
                    no_results_text: '没有找到',
                    search_contains: true, //关键字模糊搜索，设置为false，则只从开头开始匹配
                    max_selected_options: 1, //当select为多选时，最多选择个数
                    disable_search: true,
                    width: '100%'
                };

                //第一次进入页面判断
                var firstPageFlag = 0;

                return {
                    paramOption: paramOption,
                    rowParamOption: rowParamOption,
                    blankRow: blankRow,
                    searchParam: searchParam,
                    ajaxParam: ajaxParam,
                    modal: modal,
                    rowData: rowData,
                    page: page,
                    choseOptions: choseOptions,
                    choseOptionsNoSearch: choseOptionsNoSearch,
                    firstPageFlag: firstPageFlag,
                }
            },
            mounted: function () {
                //进入页面
                //cabinVue 里面所有的this 都指向当前这个vue 实例
                //vue 更多使用方法请查看vue官方文档
                this.$nextTick(function () {
                    //nextTick 用于当data数据改变,触发vue 渲染,且渲染完毕使用

                });

                //必传字段,id:主表id,operationFlag:区分增删改标记,

                //获取入参
                var params = kayak.router.requestParam;


                //判断是新增,还是更新,还是查看; operationFlag: 1,//1:查看; 2:新增; 3:修改
                if (params.operationFlag) {
                    this.modal.operationFlag = params.operationFlag || 2;
                } else {
                    //默认新增
                    this.modal.operationFlag = 2;
                }

                //判断页面展示类型
                switch (this.modal.operationFlag) {
                    case 2:
                        // rowData 增加空行
                        this.$options.methods.addRow.bind(this)();
                        break;
                    case 1:
                    case 3:
                        //获取数据的id,请求后台查询数据
                        var id = params.id;
                        this.$options.methods.queryOne.bind(this)(id);
                        break;
                    default:
                        console.log("页面展示类型未识别:" + this.modal.operationFlag);

                }


            },
            //如果 不需要保存页面状态必须添加下面这个方法
            beforeDestroy: function () {
                Object.assign(this.$data, this.$options.data());
            },
            destroyed: function () {
                //离开页面方法
            },
            methods: {
                //自定义方法
                initEvt: function () {
                    console.log('mounted')
                },
                //页面开启编辑模式
                updateModel: function () {
                    //编辑
                    this.modal.operationFlag = 3;
                },
                //增加新的空白行
                addRow: function () {
                    //新增空白行,深copy
                    var blankRow = JSON.parse(JSON.stringify(this.blankRow));
                    this.rowData.push(blankRow);//添加空白数据


                },
                //删除行
                deleteRow: function (index) {
                    this.rowData.splice(index, 1);
                },
                /**
                 * 根据id查询数据,查看,编辑模式界面用
                 * @param id 主键id
                 */
                queryOne: function (id) {
                    cabin.widgets.loading.show();

                    var url = this.ajaxParam.queryOne;

                    ajax.post(url, {id: id}, function (res) {
                        if (res.code === "0000") {
                            //TODO 获取数据后主装数据,searchParam,rowData
                        } else {
                            //失败
                            MINIPOP.show({
                                title: '提示',
                                msg: operation + '失败:' + (res.msg || "!"),
                                cancel: '确认'
                            });
                        }
                        cabin.widgets.loading.hide();

                    });
                },
                /**
                 * 更新数据
                 */
                update: function () {
                    cabin.widgets.loading.show();

                    var url = this.ajaxParam.updateData;

                    //组装请求参数
                    var requestParam = {
                        info: this.searchParam,
                        rowData: this.rowData
                    };

                    ajax.post(url, requestParam, function (res) {
                        if (res.code === "0000") {
                            MINIPOP.show({
                                title: '提示',
                                msg: '更新成功:',
                                cancel: '确认'
                            });
                        } else {
                            //失败
                            MINIPOP.show({
                                title: '提示',
                                msg: '更新失败:' + (res.msg || "!"),
                                cancel: '确认'
                            });
                        }
                        cabin.widgets.loading.hide();

                    });
                },
                /**
                 * 新增数据
                 */
                create: function () {
                    cabin.widgets.loading.show();

                    var url = this.ajaxParam.createData;

                    //组装请求参数
                    var requestParam = {
                        info: this.searchParam,
                        rowData: this.rowData
                    };

                    ajax.post(url, requestParam, function (res) {
                        if (res.code === "0000") {
                            MINIPOP.show({
                                title: '提示',
                                msg: '新增成功:',
                                cancel: '确认'
                            });

                            //TODO  是否跳转
                        } else {
                            //失败
                            MINIPOP.show({
                                title: '提示',
                                msg: '新增失败:' + (res.msg || "!"),
                                cancel: '确认'
                            });
                        }
                        cabin.widgets.loading.hide();

                    });
                },
                /**
                 * 根据id删除数据
                 * @param id
                 */
                deleteById: function (id) {
                    cabin.widgets.loading.show();

                    var url = this.ajaxParam.deleteById;

                    ajax.post(url, {id: id}, function (res) {
                        if (res.code === "0000") {
                            //TODO 获取数据后主装数据,searchParam,rowData
                        } else {
                            //失败
                            MINIPOP.show({
                                title: '提示',
                                msg: '删除失败:' + (res.msg || "!"),
                                cancel: '确认'
                            });
                        }
                        cabin.widgets.loading.hide();

                    });
                },
                /**
                 * 分页查询方法
                 * @param totalCount 总页数
                 * @param url 请求地址
                 * @param searchParam 查询条件
                 * @param rowData table明细
                 */
                initPageWidget: function (totalCount, url, searchParam, rowData) {
                    var page = this.page;

                    page.totalCount = totalCount;

                    //分页初始化
                    $('#page').NextPage({
                        pageSize: page.pageSize, //每页大小,
                        currentPage: page.pageNo, //当前页
                        totalCount: totalCount, //总条数
                        pageRange: 9, //间隔多少个
                        select: [30, 60, 100], //下拉选项
                        showTotal: true,//显示总条数 boolean
                        position: "right", //位置 left right center
                        callback: function (data, isNextPage) {
                            page.pageNo = data.currentPage;
                            page.pageSize = data.pageSize;

                            cabin.widgets.loading.show();
                            rowData.splice(0, rowData.length);//清空数据

                            //请求后台数据
                            ajax.post(url, searchParam, function (res) {
                                for (var key in res.data.result) {
                                    rowData.push(res.data.result[key]);
                                }
                                cabin.widgets.loading.hide();
                            });
                        }
                    });
                }
            }
        }
    });
    handle = {};
    _fn = {
        //解决乘法不精确的问题
        Mul: function (arg1, arg2) {
            var r1 = arg1.toString(), r2 = arg2.toString(), m, resultVal, d = arguments[2];
            m = (r1.split(".")[1] ? r1.split(".")[1].length : 0) + (r2.split(".")[1] ? r2.split(".")[1].length : 0);
            resultVal = Number(r1.replace(".", "")) * Number(r2.replace(".", "")) / Math.pow(10, m);
            return typeof d !== "number" ? Number(resultVal) : Number(resultVal.toFixed(parseInt(d)));
        },
        Div: function (arg1, arg2) {
            var r1 = arg1.toString(), r2 = arg2.toString(), m, resultVal, d = arguments[2];
            m = (r2.split(".")[1] ? r2.split(".")[1].length : 0) - (r1.split(".")[1] ? r1.split(".")[1].length : 0);
            resultVal = Number(r1.replace(".", "")) / Number(r2.replace(".", "")) * Math.pow(10, m);
            return typeof d !== "number" ? Number(resultVal) : Number(resultVal.toFixed(parseInt(d)));
        },
        Sub: function (arg1, arg2) {
            return Calc.Add(arg1, -Number(arg2), arguments[2]);
        },
        Add: function (arg1, arg2) {
            arg1 = arg1.toString(), arg2 = arg2.toString();
            var arg1Arr = arg1.split("."), arg2Arr = arg2.split("."), d1 = arg1Arr.length == 2 ? arg1Arr[1] : "",
                d2 = arg2Arr.length == 2 ? arg2Arr[1] : "";
            var maxLen = Math.max(d1.length, d2.length);
            var m = Math.pow(10, maxLen);
            var result = Number(((arg1 * m + arg2 * m) / m).toFixed(maxLen));
            var d = arguments[2];
            return typeof d === "number" ? Number((result).toFixed(d)) : result;
        },
        guid: function () {
            function S4() {
                return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
            }

            // return (S4()+S4()+"-"+S4()+"-"+S4()+"-"+S4()+"-"+S4()+S4()+S4());
            return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4());
        },

    };
    return page;
});
```