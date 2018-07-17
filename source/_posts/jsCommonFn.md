title: javascript Utils
date: 2017-05-06
tags: js
toc: true
categories: 原生js
---

> 平时在工作中我们在使用javascript开发的时候会很怀念jQuery，因为Jquery中很多的方法都非常的好用，而且还能兼容ie的低版本浏览器，虽然大家现在都在慢慢的抛弃ie低版本的浏览器，不过一些方法的封装思想还是要常常回顾一下，温故而知新嘛！

```
var utils = (function () {
    var _flag = /MSIE [678]/i.test(navigator.userAgent);

    var toTrim = function (str) {
        return str.replace(/^\s+|\s+$/g, '');
    };
    var toJSON = function (str) {
        return window.JSON ? JSON.parse(str) : eval('(' + str + ')');
    };
    var toArray = function (classAry) {
        var ary = [];
        if (!_flag) {
            ary = Array.prototype.slice.call(classAry);
        }
        else {
            for (var i = 0; i < classAry.length; i++) {
                ary[ary.length] = classAry[i];
            }
        }
        return ary;
    };
    var getEleClassName = function (strClass, context) {
        context = context || document;
        if (!_flag) {
            getEleClassName = function (strClass, context) {
                context = context || document;
                return toArray(context.getElementsByClassName(strClass));
            }
        }
        else {
            getEleClassName = function (strClass, context) {
                context = context || document;
                var result = [],
                    classNode = context.getElementsByTagName('*');
                strClass = strClass.replace(/^\s+|\s+$/g, '').split(/\s+/);
                for (var i = 0; i < classNode.length; i++) {
                    var item = classNode[i],
                        itemClass = item.className,
                        flag = true;
                    for (var j = 0; j < strClass.length; j++) {
                        var reg = new RegExp('(^|\\s)' + strClass[j] + '(\\s+|$)');
                        if (!reg.test(itemClass)) {
                            flag = false;
                            break;
                        }
                    }
                    flag ? result.push(item) : null;
                }
                return result;
            }
        }
        return getEleClassName(strClass, context);
    };
    var getEleChildren = function (curEle, tagName) {
        var result = [],
            childNode = curEle.childNodes;
        for (var i = 0; i < childNode.length; i++) {
            var item = childNode[i];
            if (item.nodeType === 1) {
                if (tagName) {
                    if (item.nodeName === tagName.toLocaleUpperCase()) {
                        result.push(item);
                    }
                    continue;
                }
                result.push(item);
            }
        }
        return result;
    };
    var getCss = function (curEle, attr) {
        if (!_flag) {
            getCss = function (curEle, attr) {
                var value = window.getComputedStyle(curEle, null)[attr];
                var reg = /^-?\d+(\.\d+)?(px|pt|em|rem)?$/g;
                reg.test(value) ? value = parseFloat(value) : null;
                return value;
            }
        } else {
            getCss = function (curEle, attr) {
                var value = null;
                if (attr === 'opacity') {
                    value = curEle.currentStyle['filter'];
                    value = (value === '') ? 1 : value.replace(/(?:=)(.+)(?:\))/g, function () {
                        return arguments[1] / 100;
                    })
                } else {
                    value = curEle.currentStyle[attr];
                }
                var reg = /^-?\d+(\.\d+)?(px|pt|em|rem)?$/g;
                reg.test(value) ? value = parseFloat(value) : null;
                return value;
            }
        }
        return getCss(curEle, attr);
    };
    var setCss = function (curEle, attr, value) {
        if (attr === 'opacity') {
            curEle.style['opacity'] = value;
            curEle.style['filter'] = 'alpha(opacity=' + value * 100 + ')';
            return;
        }
        !isNaN(value) && !/^(zIndex|zoom|fontWeight|lineHeight)$/i.test(attr) ? value += 'px' : null;
        curEle.style[attr] = value;
    };
    var setGroupCss = function (curEle, options) {
        if (Object.prototype.toString.call(options).slice(8, -1) !== 'Object') return;
        for (var key in options) {
            if (options.hasOwnProperty(key)) {
                setCss(curEle, key, options[key]);
            }
        }
    };
    var css = function () {
        var len = arguments.length,
            fn = getCss,
            type = Object.prototype.toString.call(arguments[1]).slice(8, -1);
        len >= 3 ? fn = setCss : (len === 2 && type === 'Object') ? fn = setGroupCss : null;
        return fn.apply(this, arguments);
    };
    var offset = function (curEle) {
        var $t = curEle.offsetTop,
            $l = curEle.offsetLeft,
            $p = curEle.offsetParent;
        while ($p.nodeName !== 'BODY') {
            if (/MSIE 8/i.test(navigator.userAgent)) {
                $t += $p.clientTop;
                $l += $p.clientLeft;
            }
            $t += $p.offsetTop;
            $l += $p.offsetLeft;
            $p = $p.offsetParent;
        }
        return {top: $t, left: $l};
    };
    var winBox = function (attr, value) {
        if (typeof value !== 'undefined') {
            document.documentElement[attr] = value;
            document.body[attr] = value;
            return;
        }
        return document.documentElement[attr] || document.body[attr]
    };
    var hasClass = function (curEle, cls) {
        var flag = true,
            reg = null,
            curClassName = curEle.className;
        cls = toTrim(cls).split(/\s+/);
        if (cls.length <= 1) {
            reg = new RegExp('(?:^|\\s+)' + cls + '(?:\\s+|$)');
            return curClassName.match(reg);
        }
        else {
            for (var i = 0; i < cls.length; i++) {
                reg = new RegExp('(?:^|\\s+)' + cls[i] + '(?:\\s+|$)');
                if (!reg.test(curClassName)) {
                    flag = false;
                    break;
                }
            }
            return flag;
        }
    };
    var addClass = function (curEle, cls) {
        cls = toTrim(cls).split(/\s+/);
        for (var i = 0; i < cls.length; i++) {
            if (!hasClass(curEle, cls[i])) {
                curEle.className += ' ' + cls[i];
            }
        }
        curEle.className = toTrim(curEle.className).replace(/\s+/g, ' ');
    };
    var removeClass = function (curEle, cls) {
        cls = toTrim(cls).split(/\s+/);
        for (var i = 0; i < cls.length; i++) {
            if (hasClass(curEle, cls[i])) {
                var reg = new RegExp('(?:^|\\s)' + cls[i] + '(?:\\s|$)');
                curEle.className = curEle.className.replace(reg, ' ');
            }
        }
        curEle.className = toTrim(curEle.className).replace(/\s+/g, ' ');
    };
    var addEventHandler = function (target, type, fn) {
        if (target.addEventListener) {
            target.addEventListener(type, fn);
        } else {
            target.attachEvent("on" + type, fn);
        }
    };
    var removeEventHandler = function (target, type, fn) {
        if (target.removeEventListener) {
            target.removeEventListener(type, fn);
        } else {
            target.detachEvent("on" + type, fn);
        }
    };
    var siblings = function (curEle, tagName) {
        var result = [];
        var previous = curEle.previousSibling;
        var next = curEle.nextSibling;
        if (tagName) {
            tagName = toTrim(tagName);
            if (/\.[\w-]/g.test(tagName)) {
                tagName = tagName.replace(/\./g, '');
                var reg = new RegExp('(?:^|\\s+)' + tagName + '(?:\\s+|$)');
                while (previous) {
                    if (reg.test(previous.className)) {
                        result.unshift(previous);
                    }
                    previous = previous.previousSibling;
                }
                while (next) {
                    if (reg.test(next.className)) {
                        result.push(next)
                    }
                    next = next.nextSibling;
                }
            } else {
                while (previous) {
                    if (previous.nodeName === tagName.toLocaleUpperCase()) {
                        result.unshift(previous);
                    }
                    previous = previous.previousSibling;
                }
                while (next) {
                    if (next.nodeName === tagName.toLocaleUpperCase()) {
                        result.push(next)
                    }
                    next = next.nextSibling;
                }
            }
        } else {
            while (previous) {
                if (previous.nodeType === 1) {
                    result.unshift(previous);
                }
                previous = previous.previousSibling;
            }
            while (next) {
                if (next.nodeType === 1) {
                    result.push(next);
                }
                next = next.nextSibling;
            }
        }
        return result;
    };
    var find = function (curEle, cls) {
        var result = [];
        var nodeList = curEle.getElementsByTagName('*');
        if (cls) {
            cls = toTrim(cls);
            if (/\.[\w-]/g.test(cls)) {
                cls = cls.replace(/\./, '');
                for (var i = 0; i < nodeList.length; i++) {
                    var reg = new RegExp('(?:^|\\s+)' + cls + '(?:\\s+|$)');
                    if (reg.test(nodeList[i].className)) {
                        result.push(nodeList[i]);
                    }
                }
            } else {
                for (var j = 0; j < nodeList.length; j++) {
                    if (nodeList[j].nodeName === cls.toLocaleUpperCase()) {
                        result.push(nodeList[j]);
                    }
                }
            }
        }
        return result;
    };
    var getPrevEle = function (curEle) {
        var item = curEle.previousSibling;
        while (item) {
            if (item.nodeType === 1) {
                return item;
            }
            item = item.previousSibling;
        }
    };
    var getPrevAll = function (curEle) {
        var result = [];
        var previous = curEle.previousSibling;
        while (previous) {
            if (previous.nodeName === 1) {
                result.unshift(previous);
            }
            previous = previous.previousSibling;
        }
        return result;
    };
    var getNextEle = function (curEle) {
        var item = curEle.nextSibling;
        while (item) {
            if (item.nodeType === 1) {
                return item;
            }
            item = item.nextSibling;
        }
    };
    var getNextAll = function (curEle) {
        var result = [];
        var next = curEle.nextSibling;
        while (next) {
            if (next.nodeName === 1) {
                result.push(next);
            }
            next = next.nextSibling;
        }
        return result;
    };
    var getFirstChild = function(curEle){
        return getEleTagName(curEle)[0];
    };
    var getLastChild = function(curEle){
        return getEleTagName(curEle)[getEleTagName(curEle).length-1];
    };

    return {
        toTrim: toTrim,
        toJSON: toJSON,
        toArray: toArray,
        getEleClassName: getEleClassName,
        getEleChildren: getEleChildren,
        css: css,
        offset: offset,
        winBox: winBox,
        hasClass: hasClass,
        addClass: addClass,
        removeClass: removeClass,
        addEventHandler: addEventHandler,
        removeEventHandler: removeEventHandler,
        siblings: siblings,
        find: find,
        getPrevEle:getPrevEle,
        getPrevAll:getPrevAll,
        getNextEle:getNextEle,
        getNextAll:getNextAll,
        getFirstChild:getFirstChild,
        getLastChild:getLastChild
    }
})();
```
封装一些我们常用的方法，可以让我们的开发更具有效率。