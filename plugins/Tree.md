# 树菜单-Tree

##使用样例
带搜索节点输入框的Tree,HTML样例:

```html
<div class="stage" style="padding:20px">

                <div class="row">
                    <div class="stage-search right btns-box">
                        <div class="col-lg-12">
                            <div class="input-group">
                                <span class="input-group-addon" id="sizing-addon1">搜索节点</span>
                                <input type="text" class="form-control"
                                       v-model.lazy="treeInfo.searchWord"
                                       placeholder="只能搜索前端节点">
                                <span class="input-group-btn">
                                        <button class="btn btn-primary tree-search-btn"
                                                @click="searchTree"
                                                type="button">搜索
                                        </button>
                                    </span>
                            </div>
                        </div>
                    </div>
                </div>

                <hr>
                <div class="stage" style="margin-left: 5em">
                    <v-tree ref='tree' :data='treeData' :multiple="true"
                            :halfcheck='true' v-model="treeInfo.vNodeList"/>

                </div>
            </div>
```
JS样例:
```javascript

  //树数据对象
            var treeInfo = {
                vNodeList: [],//选择的数据节点列表
                searchWord: "",//节点搜索内容
                lang: "zh",
            };
            var treeData = [];
            
            //此处省略代码
            /**
                         * 节点搜索函数
                         */
                        searchTree: function () {
                            this.$refs.tree.searchNodes(this.treeInfo.searchWord)
                        },
                        
                        //此处省略代码
```