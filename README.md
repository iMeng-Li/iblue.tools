iblue.tools
# 存储一些JavaScript代码片段~~
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


###解析时间戳
 
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

###检测更多的设备和浏览器信息，靠的是ua信息

```javascript
var ua = {};
	var win = window;
	var nav = win.navigator;
	var app_version = nav.appVersion
	
	//手机系统
	ua.is_android = (/android/gi).test(app_version)
	ua.is_idevice = (/iphone|ipad|ipod/gi).test(app_version)
	ua.is_touchpad = (/hp-tablet/gi).test(app_version)
	ua.is_iphone = (/iphone/gi).test(app_version)
	ua.is_ipad = (/ipad/gi).test(app_version)
	ua.is_ipod = (/ipod/gi).test(app_version)
	ua.is_mac = (/macintosh/gi).test(app_version)
	
	//高能设备,小米2、3，三星S3、S4等
	ua.is_advanced_device = ua.is_idevice || (/windows|MI (2|3|4|5)|GT\-I9300/gi).test(app_version)|| ua.is_mac;

	ua.other_phone = !(ua.is_android || ua.is_idevice)
	

	//浏览器品牌类型
	ua.is_uc = (/uc/gi).test(app_version)
	ua.is_chrome = (/CriOS|Chrome/gi).test(app_version)
	ua.is_qq_browser = (/QQBrowser/gi).test(app_version)
	ua.is_qq = (/ QQ\//gi).test(app_version)
	ua.is_wechat = (/MicroMessenger/i).test(app_version)// || location.hostname=='m3.meijialove.com'
	ua.is_real_safari = (/safari/gi).test(app_version) && !ua.is_chrome && !ua.is_qq_browser && !ua.is_wechat //真正的原生IOS safari浏览器
	window.g_ua && $.extend(ua,window.g_ua);//附加ua识别，可以在index.html定义window.g_ua


	//iphone safari是否全屏
	ua.is_standalone = (window.navigator.standalone)? true : false
	ua.is_iphone_safari_no_fullscreen = ua.is_idevice && !ua.is_ipad && ua.is_real_safari && !ua.is_standalone
	
	if(ua.is_iphone_nativeapp || ua.is_ipad_nativeapp){
		ua.is_ios_nativeapp = true;
		if(ua.is_ipad_nativeapp){
			ua.is_ipad = true;
		}
	}
	ua.is_ios = ua.is_iphone || ua.is_ipad;
	ua.support_upload = true;
	if(ua.is_wechat){
		ua.channel='wechat';
		var matches = app_version.match(/.*?(MicroMessenger\/([0-9.]+))\s*/);
		if(matches && matches[2])ua.version = parseFloat(matches[2].replace(/(\d+)\.(\d+)\.(\d+)/,"$1.$2$3"));
		//安卓微信5.2版之前不支持文件/图片上传
		if(ua.version<5.2 && ua.is_android){
			ua.support_upload = false;
		}
	}else if(ua.is_baidu_lightapp){
		ua.channel='baidu_lightapp';
	}else if(ua.is_qq){
		ua.channel='qq';
	}else if(ua.is_qq_browser){
		ua.channel='qq_browser';
	}else if(ua.is_chrome){
		ua.channel='chrome_browser';
	}else if(ua.is_real_safari){
		ua.channel='safari_browser';
	}else if(ua.is_uc){
		ua.channel='uc_browser';
	}else if(ua.is_iphone_nativeapp){
		ua.channel='iphone_nativeapp';
	}else if(ua.is_ipad_nativeapp){
		ua.channel='ipad_nativeapp';
	}else{
		ua.channel='other';
	}
	ua.window_width = window.innerWidth
	ua.window_height = window.innerHeight

	//ua.window_height = $(window).height()
	
	//手机版本
	if(ua.is_android)
	{
		var android_version = parseFloat(app_version.slice(app_version.indexOf("Android")+8)); 
		ua.android_version = android_version
	}
	else if(ua.is_idevice)
	{
		var v = (app_version).match(/OS (\d+)_(\d+)_?(\d+)?/);
		
		var ios_version = v[1]
		
		if(v[2]) ios_version += '.'+v[2]
		if(v[3]) ios_version += '.'+v[3]

		ua.ios_version = ios_version
	}

```

###巧用ontouchstart~~判断是否为移动端

```javascript
if ("ontouchstart" in document) {
    var touchstart = "touchstart",
        touchend = "touchend",
        touchmove = "touchmove"
} else {
    var touchstart = "mousedown",
        touchend = "mouseup",
        touchmove = "mousemove"
}

```

###orientationchange事件主要用于手机浏览器,作用于触发手机的横屏和竖屏之间的切换。我们可以根据这个事件,改变手机在横屏和竖屏的显示状态

```javascript
var warn = function() {
        var lock = document.getElementById("lock");
        return {
            show: function() {
                window.scroll(0, 0);
                lock.style.display = "block"
            },
            hide: function() {
                window.scroll(0, 0);
                lock.style.display = "none"
            }
        }
    }();
    var need_watch = !("onorientationchange" in window);
    if (!need_watch) {
        window.addEventListener("orientationchange", function() {
            if (window.orientation != 0) {
                warn.show()
            } else {
                warn.hide()
            }
        }, false);
        if (window.orientation != 0) {
            warn.show()
        }
    }

```













