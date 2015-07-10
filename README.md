iblue.tools
# 存储JavaScript的一些代码片段~~
------------------------


###判断是否支持WebP


```javascript
//判断是否支持webp，异步检测，第一次用会有时延，后面用cookie记录
	ua.support_webp = !!cookies.get('ua_support_webp','1');
	$("<img>").on("load", function() {
        if(this.width === 2 && this.height === 1) {
            ua.support_webp = true;
            cookies.set('ua_support_webp','1',300);
        }else{
            ua.support_webp = false;
            cookies.set('ua_support_webp','',300);
        }
    }).attr("src", "data:image/webp;base64,UklGRh4AAABXRUJQVlA4TBEAAAAvAQAAAAfQ//73v/+BiOh/AAA=");
```
参考：http://www.cnblogs.com/zhishaofei/p/4191418.html

###获取url中的参数（支持url的hash传参数）

```javascript
	function get_url_param(key) {
	    var reg = new RegExp("(#|&|\\?)" + key + "=([^&=\?#]+)");
	    var ret = document.location.href.match(reg);
	    return (ret && ret[2]) || '';
	}
```



    ### 解析时间戳
 
     ```javascript
    function date_format(time,format) 
    {
        format = format || 'yyyy-MM-dd';
        var date = new Date(time*1000);
        var date2 = new Date((Number(time)-1)*1000);
        var week = ['星期日','星期一','星期二','星期三','星期四','星期五','星期六']

        var o = { 
            "M+" : date.getMonth()+1, //month 
            "d+" : date2.getDate(), //day 
            "h+" : date.getHours(), //hour 
            "m+" : date.getMinutes(), //minute 
            "s+" : date.getSeconds(), //second 
            "q+" : Math.floor((date.getMonth()+3)/3), //quarter 
            "S" : date.getMilliseconds(), //millisecond 
            "Week" :week[date.getDay()]
        } 
        
        if(/(y+)/.test(format)) { 
            format = format.replace(RegExp.$1, (date.getFullYear()+"").substr(4 - RegExp.$1.length)); 
        } 
        
        for(var k in o) { 
            if(new RegExp("("+ k +")").test(format)) { 
                format = format.replace(RegExp.$1, RegExp.$1.length==1 ? o[k] : ("00"+ o[k]).substr((""+ o[k]).length)); 
            } 
        }
        return format;
    }
```


   ###解决iOS设备修改title不起作用的bug
   ```javascript
    function refresh_ios_title(_title){
      // if(!ua.is_idevice) return;
      //随便访问iframe访问一个网页，title就可以刷新了
            window.title = _title;
            var url = 'http://cdn1.mli.so/index/dist/html/copy_text.html';
            var dom_id = 'refresh-ios-title-iframe';
            var dom = $('#'+dom_id);
            if(dom.length==0){
              dom = $('<iframe style="width:0px;height:0px;display:none" id="'+dom_id+'" src="'+url+'" />');
                $("body").append(dom);
            }else{
              dom.attr('src',url);
            }
    }
```
























