---
title: 关于mouseout离开容器的事件
date: 2017-09-30 15:12:53
tags: 
  - event
  - js
categories: 
  - 代码片断
---

解决某个容器绑定mouseout事件时，当在它的子容器时也会触发mouseout事件，无法达到原有的目的的情况。
<!-- more --> 
解决代码片断：
{% codeblock lang:js %}
document.getElementById('layer-actions').onmouseout = function(e){
    var obj = this;
    var related = getRelated(e);
    if (obj != related && !contains(obj, related) && self.selected) {
        //执行离开容器事件
    }
    function contains(a, b) {
        return a.contains ? a != b && a.contains(b) : !! (a.compareDocumentPosition(b) & 16);
    }
    function getRelated(e) {
        var related, type = (e.type || '').toLowerCase();
        if (type == 'mouseover') {
            related = e.relatedTarget || e.fromElement
        } else if (type = 'mouseout') {
            related = e.relatedTarget || e.toElement
        }
        return related;
    }
}
{% endcodeblock %}