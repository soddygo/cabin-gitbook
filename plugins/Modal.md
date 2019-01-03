# 拟态框-Modal

HTML样例:

```html
   <!--更新modal-->
        <vue-modal data-Commit-Title="提交" data-Title="更新"
                   v-bind:data-Toggle.sync="modalUpdate.showFlag"
                   v-bind:data-View-Flag="modalUpdate.viewFlag"
                   v-on:data-commit="dataCommitUpdate"
                   v-bind:data-Param-Option="updateParams"
                   v-model="modalUpdate.dataObj">
        </vue-modal>
```

JS样例:

```javascript

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



//省略代码
  /**
             * 打开更新modal页面
             */
            modalUpdateShow: function (item) {

                this.modalUpdate.dataObj = item;

                //显示modal
                this.modalUpdate.showFlag = true;
            },


//更新,提交数据
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
            
            
            //省略代码
```
以上未提及的对象变量,都是根据"paramOption"自动生成的,安装模板使用即可.