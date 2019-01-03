# Search搜索条件

##使用demo样例

HTML样例:
```html
  <!--搜索条件-->
                <vue-search-form v-bind:data-Param-Option="paramOption" v-model="searchParam">
                </vue-search-form>
```

JS样例:
```javascript
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
                },
            ];

```

其中"searchParam"对象,是根据"paramOption"参数列表,自动生成的双向绑定用的对象(按照模板使用直接使用).