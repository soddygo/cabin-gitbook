# 时间范围选择器-DatePick

HTML样例:

```html
<date-pick v-model="searchParam[item.code]"
                         format="YYYY-MM-DD"
                         placeholder="请选择日期"
              />
```

JS样例:
无


属性介绍:
format : 日期展示格式
v-model: 双向绑定对象,自己定义

选择日期范围后,绑定对象的值,会自动变为开始结束时间,并用英文逗号分隔,绑定值如:2018-09-01,2019-01-01 ,这样的格式返回