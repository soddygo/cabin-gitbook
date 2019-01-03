# Table模板


HTML模板:

```html
<div class="privilege_3pl-page-vueTableDemo content-container"
     id="privilege_3pl-page-vueTableDemo"
     xmlns:v-on="http://www.w3.org/1999/xhtml"
     xmlns:v-bind="http://www.w3.org/1999/xhtml">
    <div class="content-container">
        <div class="container-scroll">
            <div class="stage">
                <div class="stage-title flag">系统管理</div>

                <div class="stage-search" style="padding-bottom: 0px;">
                    <button class="btn btn-outline J_create_btn" v-on:click="modalCreateShow">新增</button>
                </div>

                <!--搜索条件-->
                <vue-search-form v-bind:data-Param-Option="paramOption" v-model="searchParam">
                </vue-search-form>

                <hr>
                <div class="stage-search right btns-box">
                    <div class="btn btn-primary" v-on:click="search">查询</div>
                    <div class="btn btn-primary" v-on:click="reset">重置</div>
                </div>
            </div>
            <hr>
            <div class="stage" v-show="page.totalCount !== 0">
                <!--表格盒子-->
                <!--左右滑动表格-->
                <div class="table-responsive" style="overflow:visible">
                    <!--注:没标题没按钮组表格没上下边线 有标题加table-top-bordered有分页加table-bottom-bordered-->
                    <!--表格-->
                    <table class="cabinTable table table-hover  table-bottom-bordered">
                        <thead>
                        <tr>
                            <th>行号</th>
                            <template v-for="(item,index) in paramOption">
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
                                <template v-for="(itemParam,index) in paramOption">
                                    <template v-if="itemParam.tableFlag ===1">
                                        <td v-if="itemParam.viewType==='time'">
                                            <div>{{item[itemParam.code] | dateFilter}}</div>
                                        </td>
                                        <td v-else-if="itemParam.viewType==='textarea'">
                                             <textarea name="" class="form-control" cols="50" rows="10">
                                                    {{item[itemParam.code] }}
                                             </textarea>
                                        </td>
                                        <td v-else>
                                            <div v-if="itemParam.filter === 'statusFilter' ">
                                                {{item[itemParam.code]|statusFilter}}
                                            </div>
                                            <div v-else>{{item[itemParam.code]}}</div>
                                        </td>
                                    </template>
                                </template>
                                <td>
                                    <div>
                                        <span class="event" v-on:click="menuManage(item)">菜单管理</span>
                                    </div>
                                    <div>
                                        <span class="event" v-on:click="modalViewShow(item)">查看</span>
                                    </div>
                                    <div>
                                        <span class="event" v-on:click="modalUpdateShow(item)">编辑</span>
                                    </div>
                                    <div>
                                        <span class="event" v-on:click="deleteById(item.id)">删除</span>
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

            <!--分页end-->

            <div style="min-height:400px;" v-show="page.totalCount === 0 && firstPageFlag === 1">
                <div class="empty-table">
                    <div class="text"
                         style="margin-bottom: -10px;font-size: 14px;line-height: 15px;text-align: center;">
                        <p>很抱歉，没有找到相关数据</p>
                    </div>
                </div>
            </div>
        </div>


        <!--新增modal-->
        <vue-modal data-Commit-Title="提交" data-Title="新建"
                   v-bind:data-Toggle.sync="modalCreate.showFlag"
                   v-bind:data-View-Flag="modalCreate.viewFlag"
                   v-on:data-commit="dataCommitCreate"
                   v-bind:data-Param-Option="createParams">
        </vue-modal>
        <!--更新modal-->
        <vue-modal data-Commit-Title="提交" data-Title="更新"
                   v-bind:data-Toggle.sync="modalUpdate.showFlag"
                   v-bind:data-View-Flag="modalUpdate.viewFlag"
                   v-on:data-commit="dataCommitUpdate"
                   v-bind:data-Param-Option="updateParams"
                   v-model="modalUpdate.dataObj">
        </vue-modal>
        <!--查看modal-->
        <vue-modal data-Commit-Title="确认" data-Title="查看"
                   v-bind:data-Toggle.sync="modalView.showFlag"
                   v-bind:data-View-Flag="modalView.viewFlag"
                   v-bind:data-Param-Option="viewParams"
                   v-model="modalView.dataObj">
        </vue-modal>




    </div>
</div>

```

JS样例:
```javascript

define('privilege_3pl/pages/vueTableDemo/vueTableDemo', function (require, exports, module) {

    var ajax = require('privilege_3pl/common/data/ajax');

    require('cabin/lib/daterangepicker/moment.js');
    require('cabin/widgets/pddatepicker/pddatepicker.js');
    require('cabin/lib/daterangepicker/daterangepicker.js');

    MINIPOP = require('cabin/widgets/minipop/minipop');

    var commonUtil = require('privilege_3pl/common/tool/commonUtil');
    var paramDefaultUtil = require('privilege_3pl/common/tool/paramDefaultUtil');

    var VueChosen = require('cabin/widgets/vueChosen/vueChosen')
    var VueModal = require('privilege_3pl/common/plugins/vueModal');

    var DatePick = require('privilege_3pl/common/plugins/datePick');
    var VueSearchForm = require('privilege_3pl/common/plugins/vueSearchForm');


    var handle, _fn, page;
    page = VuePage({
        source: ['privilege_3pl/pages/vueTableDemo/vueTableDemo.tpl', 'privilege_3pl/pages/vueTableDemo/vueTableDemo.css'],
        pageTitle: '系统管理',//当前页面需要展示的title
        //这里this 指向当前VUE 实例,仅支持es5语法！！已经默认添加了timepicker 指令
        //使用vue时　不要使用handle.jView 统一使用$ 筛选或者使用$(this.$el).find(xxx)去实现；this.$el为vue实例dom 节点
        //使用vue 后可以不使用page 的show hide 方法,建议使用vue 自己的 mounted 取代show方法 destroy 取代hide 方法
        el: 'privilege_3pl-page-permissionSystem',
        //这里必须 使用一个方法返回给data
        data: function () {
            //请求给后台,需要过滤去掉的字段
            var ignoreProperty = [
                "extendProperty",
            ];
            //模板demo
            //字段参数
            var paramOption = [
                {
                    //最少参数字段,其他字段可以默认
                    code: "id",
                    name: "系统ID",
                    viewType: "text",
                    display: "00100",
                },
                {
                    code: "sysName",
                    name: "系统名称",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "11111",
                }, {
                    code: "sysDomain",
                    name: "系统域名",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "11111",
                },
                {
                    code: "sysIcon",
                    name: "系统图标",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "01111",
                }, {
                    code: "sysResourcePre",
                    name: "资源码前缀",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "01111",
                }, {
                    code: "sortNum",
                    name: "排序字段",
                    dataType: "String",
                    viewType: "text",
                    default: "",
                    options: [],
                    display: "01111",
                }, {
                    code: "created",
                    name: "创建时间",
                    dataType: "String",
                    viewType: "time",
                    default: "",
                    options: [],
                    display: "01100",
                }, {
                    code: "modified",
                    name: "修改时间",
                    dataType: "String",
                    viewType: "time",
                    default: "",
                    options: [],
                    display: "00100",
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

            /**
             * /默认参数属性;
             * display:第一位表示搜索条件是否展示,第二位表示table列是否展示,第三位表示查看字段显示,第四位表示编辑字段显示,第五位表示新增字段显示;
             * dataType:后台数据字段类型,特殊字段处理用,比如后期规划时间字段处理展示问题;
             * viewType:前段展示的数据字段类型;
             * @type {{display: number, dataType: string, viewType: string, default: string, options: Array}}
             */
            var defaultParams = paramDefaultUtil.getDefaultParams(paramOption);

            //新增modal参数
            var createParams = paramDefaultUtil.getCreateParams(paramOption);
            //更新modal参数
            var updateParams = paramDefaultUtil.getUpdateParams(paramOption);
            //查看modal参数
            var viewParams = paramDefaultUtil.getViewParams(paramOption);

            //吐槽下,这里不用es7语法 {},自动展开属性,只能一个个手动新建对象赋值=.=
            //新增,或更新的对象
            var info = defaultParams.info;
            //搜索条件参数
            var searchParam = defaultParams.searchParam;
            //页面模式记录,1:查看; 2:新增; 3:修改
            var modal = defaultParams.modal;
            //分页信息
            var page = defaultParams.page;
            //带搜索框的单选
            var choseOptions = defaultParams.choseOptions;
            //不带搜索框的单选
            var choseOptionsNoSearch = defaultParams.choseOptionsNoSearch;
            //带搜索框多选
            var multipleChoseOptions = defaultParams.multipleChoseOptions;
            //不带搜索框多选
            var multipleChoseOptionsNoSearch = defaultParams.multipleChoseOptionsNoSearch;
            //是否第一次进入页面
            var firstPageFlag = defaultParams.firstPageFlag;
            //table的明细数据
            var rowData = defaultParams.rowData;

            //新建modal
            var modalCreate = defaultParams.modalCreate;
            //更新modal
            var modalUpdate = defaultParams.modalUpdate;
            //查看modal
            var modalView = defaultParams.modalView;

            return {
                ignoreProperty: ignoreProperty,
                createParams: createParams,
                updateParams: updateParams,
                viewParams: viewParams,
                modalCreate: modalCreate,
                modalUpdate: modalUpdate,
                modalView: modalView,
                paramOption: paramOption,
                info: info,
                searchParam: searchParam,
                ajaxParam: ajaxParam,
                modal: modal,
                rowData: rowData,
                page: page,
                choseOptions: choseOptions,
                choseOptionsNoSearch: choseOptionsNoSearch,
                multipleChoseOptions: multipleChoseOptions,
                multipleChoseOptionsNoSearch: multipleChoseOptionsNoSearch,
                firstPageFlag: firstPageFlag,

            }
        },
        beforeMount:function(){

            //部分初始化工作
            paramDefaultUtil.remoteOptionInit(ajax, this.paramOption);

        },
        mounted: function () {
            //进入页面
            //cabinVue 里面所有的this 都指向当前这个vue 实例
            //vue 更多使用方法请查看vue官方文档
            this.$nextTick(function () {
                //nextTick 用于当data数据改变,触发vue 渲染,且渲染完毕使用

            });

            //空条件搜索
            this.$options.methods.search.bind(this)();


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
            /**
             * 菜单管理
             * @param item 行数据对象
             */
            menuManage: function (item) {
                //跳转页面
                var url = '#index/privilege_3pl/permissionMenu:systemId=' + item.id;

                kayak.router.go(url);
            },
            //重置搜索参数
            reset: function () {
                for (var index in this.searchParam) {
                    var searchItem = this.searchParam[index];

                    //检查配置参数
                    for (var index2 in this.paramOption) {
                        var paramOptionItem = this.paramOption[index2];
                        if (paramOptionItem.code == index) {
                            this.searchParam[index] = paramOptionItem.default;
                            break;
                        }
                    }
                }
            },

            /**
             * 打开新建modal页面
             */
            modalCreateShow: function () {
                this.modalCreate.showFlag = true;
            },

            /**
             * 打开更新modal页面
             */
            modalUpdateShow: function (item) {

                this.modalUpdate.dataObj = item;

                //显示modal
                this.modalUpdate.showFlag = true;
            },

            /**
             * 打开查看modal页面
             */
            modalViewShow: function (item) {
                this.modalView.showFlag = true;
                this.modalView.dataObj = item

            },

            //新增
            dataCommitCreate: function (obj) {
                cabin.widgets.loading.show();

                //深拷贝
                var info = commonUtil.deepCopy(obj);

                //默认条件查询
                var search = this.$options.methods.search.bind(this);

                var url = this.ajaxParam.createData;

                for (var attr in info) {
                    if (info[attr] === null) {
                        delete info[attr];
                        continue;
                    }
                }
                for (var index in this.ignoreProperty) {
                    delete info[this.ignoreProperty[index]];
                }


                //参数校验
                var validateFlag = commonUtil.validateParams(this.updateParams, info);
                if (validateFlag === false) {
                    //校验不通过,直接返回
                    cabin.widgets.loading.hide();
                    return;
                }


                var requestParam = info;

                //modal show flag
                var modalCreate = this.modalCreate;
                ajax.post(url, requestParam, function (res) {
                    if (res.code === "0000") {
                        MINIPOP.show({
                            title: '提示',
                            msg: '新建成功',
                            cancel: '确认'
                        });
                        search();
                        modalCreate.showFlag = false;

                    } else {
                        //失败
                        MINIPOP.show({
                            title: '提示',
                            msg: '新建失败:' + (res.msg || "!"),
                            cancel: '确认'
                        });
                    }
                    cabin.widgets.loading.hide();

                });

            },
            //更新数据
            dataCommitUpdate: function (obj) {
                cabin.widgets.loading.show();

                //深拷贝
                var info = commonUtil.deepCopy(obj);

                //默认条件查询
                var search = this.$options.methods.search.bind(this);

                var url = this.ajaxParam.updateData;

                for (var attr in info) {
                    if (info[attr] === null) {
                        delete info[attr];
                        continue;
                    }
                }
                for (var index in this.ignoreProperty) {
                    delete info[this.ignoreProperty[index]];
                }


                //参数校验
                var validateFlag = commonUtil.validateParams(this.updateParams, info);
                if (validateFlag === false) {
                    //校验不通过,直接返回
                    cabin.widgets.loading.hide();
                    return;
                }


                var requestParam = info;

                //modal show flag
                var modalUpdate = this.modalUpdate;
                ajax.post(url, requestParam, function (res) {
                    if (res.code === "0000") {
                        MINIPOP.show({
                            title: '提示',
                            msg: '更新成功',
                            cancel: '确认'
                        });
                        search();
                        modalUpdate.showFlag = false;

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
            //主键id删除
            deleteById: function (id) {
                var url = this.ajaxParam.deleteById;
                //默认条件查询
                var search = this.$options.methods.search.bind(this);
                //1.弹窗确认
                MINIPOP.show({
                    title: '警告',
                    msg: "是否确认删除!",
                    ok: '确认',
                    cancel: '取消',
                    sort: 'right', //left(默认为left):cancel按钮在左，ok按钮在右,right:cancel按钮在右，ok按钮在左
                    callback: function (el, type) {
                        if (type === 'ok') {
                            // 删除订单数据
                            cabin.widgets.loading.show();

                            ajax.post(url, {id: id}, function (res) {
                                if (res.code === "0000") {
                                    MINIPOP.show({
                                        title: '提示',
                                        msg: '删除成功',
                                        cancel: '确认'
                                    });
                                    search();
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
                        } else if (type === 'cancel') {
                            //无操作,停留当前界面
                        }
                    }
                });


            },
            //查询搜索
            search: function () {
                cabin.widgets.loading.show();

                //第一次进入页面的标记,1表示点击了搜索按钮
                this.firstPageFlag = 1;
                var initPageWidget = this.$options.methods.initPageWidget.bind(this);


                var rowData = this.rowData;
                var searchParam = this.searchParam;
                this.page.pageNo = 1;
                this.page.pageSize = 30;
                searchParam.pageNo = this.page.pageNo;
                searchParam.pageSize = this.page.pageSize;

                rowData.splice(0, rowData.length);//清空数据

                var url = this.ajaxParam.search;

                ajax.post(url, this.searchParam, function (res) {
                    if (res.code === "0000") {
                        initPageWidget(res.data.totalCount, url, searchParam, rowData);

                        for (var key in res.data.result) {
                            rowData.push(res.data.result[key]);
                        }
                        //隐藏modal
                        $('#myModal').modal('hide');
                    } else {
                        //失败
                        MINIPOP.show({
                            title: '提示',
                            msg: '查询失败:' + (res.msg || "!"),
                            cancel: '确认'
                        });
                    }
                    cabin.widgets.loading.hide();

                });
            },
            /**
             * 展示(隐藏)modal,并记录modal的标记
             * @param operationFlag 1:查看; 2:新增; 3:修改
             */
            toggleModal: function (operationFlag) {
                this.modal.operationFlag = operationFlag;
                $('#myModal').modal('toggle');
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

                        searchParam.pageNo = page.pageNo;
                        searchParam.pageSize = page.pageSize;

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
        },
        filters: {
            //时间格式化
            dateFilter: function (value) {
                if (!value || typeof value === "undefined") {
                    return "";
                }
                var date = moment(value).format('YYYY-MM-DD HH:mm:ss');
                return date;
            },
            //状态
            statusFilter: function (value) {
                if (!value || typeof value === "undefined") {
                    return "";
                }
                var statusName = "";
                switch (value) {
                    case 1:
                        statusName = "可用";
                        break;
                    case 2:
                        statusName = "禁用";
                        break;
                    default:
                        statusName = "未识别状态";
                        break;
                }
                return statusName;
            }
        },
        components: {
            "date-pick": DatePick,
            "vue-modal": VueModal,
            "vue-search-form": VueSearchForm,
        }
    });
    handle = {};
    _fn = {};
    return page;
});

```