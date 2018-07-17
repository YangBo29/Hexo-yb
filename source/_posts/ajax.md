title: javascript Ajax
date: 2017-05-07
tags: js
toc: true
categories: 原生js
---

在学习AJAX原理的时候我尝试这封装过自己的AJAX，作为我学习AJAX的纪念吧
```
/*
 * 参数
 * url : 请求地址
 * method : 请求类型
 * data : 请求参数
 * dataType : 数据类型
 * async : 是否异步
 * cache : 是否清缓存
 * success : 成功执行函数
 * error : 失败后执行函数
 * timeoutTimer : 等待时长
*/

(function () {
  class ajaxFn {
    constructor(url, options) {
      // 如果传入的是一个对象的话，options 就被 url 替换
      if (typeof url === 'object') {
        options = url;
        url = undefined;
      }
      options = Object.prototype.toString.call(options).slice(8, -1) === 'Object' ? options : {};
      this.url = url || options.url;
      this.options = {
        method: options.type || options.method || 'GET',
        data: options.data || null,
        dataType: options.dataType || 'JSON',
        async: options.async || true,
        cache: options.cache || true,
        success: typeof options.success === 'function' ? options.success : function () {
        },
        error: typeof options.error === 'function' ? options.error : function () {
        },
        timeoutTimer: options.timeoutTimer || null
      };
      this.isGet = /(^|\s+)(head|detele|get)(\s+|$)/i.test(this.options.method);
      this.isLocal = window.XMLHttpRequest;
    }

    formatData() {
      if (Object.prototype.toString.call(this.options.data).slice(8, -1) === 'Object') {
        let obj = this.options.data,
          str = ``;
        for (let key in obj) {
          if (obj.hasOwnProperty(key)) {
            str += `${key}=${obj[key]}&`
          }
        }
        str = str.replace(/&$/g, '');
        this.options.data = str;
      }
    }

    queryURL() {
      return this.url.indexOf("?") === -1 ? "?" : "&";
    }

    cacheFn() {
      return !this.cache ? `${this.queryURL()}_=${Math.random()}` : ``;
    }

    createStandardXHR() {
      try {
        return new window.XMLHttpRequest();
      } catch (e) {
      }
    }

    createActiveXHR() {
      try {
        return new window.ActiveXObject("Microsoft.XMLHTTP");
      } catch (e) {
      }
    }

    serialize(form) {
      let parts = [],
        field = null,
        option,
        optValue;
      for (let i = 0; i < form.elements.length; i++) {
        field = form.elements[i];
        switch (field.type) {
          case "select-one":
          case "select-multiple":
            if (field.name.length) {
              for (let j = 0; j < field.options.length; j++) {
                option = field.options[j];
                if (option.selected) {
                  optValue = '';
                  if (optioin.hasAttribute) {
                    optValue = (option.hasAttribute('value') ? option.value : option.text);
                  } else {
                    optValue = (option.attributes['value'].specified ? option.value : option.text);
                  }
                  parts.push(encodeURIComponent(field.name) + '=' + encodeURIComponent(optValue));
                }
              }
            }
            break;
          case undefined: //字段集
          case 'file': //文件输入
          case 'submit': //提交按钮
          case 'reset': //重置按钮
          case 'button': //自定义按钮
            break;
          case 'radio': //单选按钮
          case 'checkbox': //复选框
            if (!field.checked) {
              break;
            }
            break;
          default:
            if (field.name.length) {
              parts.push(encodeURIComponent(field.name) + '=' + encodeURIComponent(field.value));
            }
        }
      }
      return parts.join('&');
    }
  }

  window.ajax = function (url, options) {
    let example = new ajaxFn(url, options);
    url = example.url || null;
    options = example.options || {};
    let xhr = example.isLocal ? example.createStandardXHR() : example.createActiveXHR();
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 2) {
        let response = xhr.responseText;
      }
      if (xhr.readyState === 4) {
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
          let response = xhr.responseText;
          options.success(response);
        }
        if (xhr.status >= 400 && xhr.status < 600) {
          let response = xhr.responseText;
          options.error(response);
        }
      }
    };

    if (options.data != null) {
      example.formatData();
      if (example.isGet) {
        url += example.queryURL() + encodeURIComponent(options.data);
        options.data = null;
      }
    }

    example.isGet && (url += example.cacheFn());

    xhr.open(options.method, url, options.async);
    xhr.send(options.data);
  };
})();
```