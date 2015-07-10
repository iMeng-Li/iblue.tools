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




























