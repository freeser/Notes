---
title: 模拟日历
date: 2017-09-10 17:30:00
tags: 
  - vue
  - js
categories: 
  - 代码片断
---

## 示例

{% img /img/datetime.png 日历示例 %}

<!-- more --> 
## 代码
{% codeblock lang:html %}
<div id="vue_tab_attend" class="h100 scroll">
    <dl class="datetimes">
        <dt class="thead flex acenter jcenter">
            <button @click="setYear(-1)">上年</button>
            <button @click="setMonth(-1)">上月</button>
            <span class="currdate">{{year}}-{{month}}</span>
            <button @click="setMonth(1)">下月</button>
            <button @click="setYear(1)">下年</button>
        </dt>
        <dd class="flex header columns7">
            <span class="colum" v-for="i in 7" :key="i">{{weeks[i - 1][1]}}</span>
        </dd>
        <dd class="flex tbody wrap columns7">
            <span class="colum" v-for="(d, idx) in days" :key="idx" @click="day = d.d || day" :class="{'day': d.d, 'selected': d.d == day}">{{ d.d }}</span>
        </dd>
    </dl>
</div>
<script src="https://unpkg.com/vue/dist/vue.js"></script>
{% endcodeblock %}
{% codeblock lang:js %}
    (function () {
        var today = new Date();
        new Vue({
            el: '#vue_tab_attend',
            data: {
                options: {},
                year: today.getFullYear(),
                month: today.getMonth() + 1,
                day: today.getDate(),
                days: [],
                weeks: [
                    [0, '日'],
                    [1, '一'],
                    [2, '二'],
                    [3, '三'],
                    [4, '四'],
                    [5, '五'],
                    [6, '六']
                ]
            },
            watch: {
                month: function (v, o) {
                    this.creatMonth()
                },
                year: function() {
                    this.creatMonth()
                }
            },
            created: function () {
                this.creatMonth();
            },
            methods: {
                setMonth: function (step) {
                    var val = this.month + step;
                    if (val < 1) {
                        this.month = 12;
                        this.year -= 1;
                    } else if (val > 12) {
                        this.month = 1;
                        this.year += 1;
                    } else {
                        this.month = val;
                    }
                },
                setYear: function (step) {
                    this.year += step;
                },
                creatMonth: function () {
                    var self = this;
                    var first = new Date(this.year, this.month - 1, 1);
                    var last = new Date(this.year, this.month, 0);
                    var bweek = first.getDay();
                    var fweek = this.weeks[0][0];
                    var before = fweek ? (bweek || 7) - 1 : bweek;
                    var total = before + last.getDate();
                    var after = 7 - (total % 7 || 7);
                    var days = Array(total + after).join(',').split(',');
                    this.days = days.map(function (item, idx) {
                        return (idx >= before && idx < total) ? {
                            y: self.year,
                            m: self.month,
                            d: idx - before + 1,
                            on: false
                        } : {};
                    });
                }
            }
        })
    })();
{% endcodeblock %}
{% codeblock lang:css %}
    html {
        color: #666;
        -ms-text-size-adjust: 100%;
        -webkit-text-size-adjust: 100%;
        font-size: 100%;
        font-family: "Microsoft YaHei", "SimHei";
    }

    * {
        margin: 0;
        padding: 0;
    }

    .flex {
        display: -webkit-box;
        display: -webkit-flex;
        display: -ms-flexbox;
        display: flex;
    }

    .wrap {
        -ms-flex-wrap: wrap;
        flex-wrap: wrap;
        -webkit-flex-wrap: wrap;
    }

    .jcenter {
        -webkit-box-pack: center;
        -webkit-justify-content: center;
        -ms-flex-pack: center;
        justify-content: center;
    }

    .acenter {
        -webkit-align-content: center;
        -ms-flex-line-pack: center;
        align-content: center;
    }

    .tcenter {
        text-align: center;
    }

    .h100 {
        height: 100%!important;
    }

    .datetimes .thead {
        line-height: 36px;
        height: 36px;
        border-bottom: 1px solid #eee;
    }

    .datetimes .thead button {
        border: 0;
        background: transparent;
        padding: 0 1em;
    }

    .datetimes .thead .currdate {
        padding: 0 1em;
    }

    .datetimes .columns7 {
        margin: 0 1em;
        line-height: 30px;
    }

    .datetimes .columns7 .colum {
        position: relative;
        font-size: 12px;
        box-sizing: border-box;
        text-align: center;
        margin: 5px 3px;
        width: -webkit-calc(14.28% - 6px);
        width: calc(14.28% - 6px);
        border-radius: 3px;
    }
    .datetimes .columns7 .day{
        color: #333;
        border: 1px solid #efefef;
    }
    .datetimes .columns7 .day.selected{
        color: blue; border-color: #3997D6;
    }
    .datetimes .header {
        color: #3997D6;
    }
{% endcodeblock %}