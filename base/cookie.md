# cookie

## web存储

- sessionStorage 浏览器关闭即清除
- cookie 到了过期时间即清除
- localStorage 永久存储

## cookie中的domain

- 设置 cookie 时明确指定 domain 域名，子域名可读取（子域共享该cookie），删除时则也必须明确指定域名，否则无法删除
- 设置 cookie 时不指定域名，使用默认值，则表示 只有当前域名可见（子域不可共享）。删除时也不需要指定域名，否则无法删除

## 创建cookie

- document.cookie="username=John Doe; demain=.baidu.com; expires=Thu, 18 Dec 2013 12:00:00 GMT; path=/";
- 内容，过期时间，cookie路径（当前页面）
- 注：document.cookie返回字符串，只返回"username=John Doe;username2=John Doe2;",不会包含demain、expires和path信息

```javascript
    function setCookie(cname,cvalue,exdays)
    {
        var d = new Date();
        d.setTime(d.getTime()+(exdays*24*60*60*1000));
        var expires = "expires="+d.toGMTString();
        document.cookie = cname + "=" + cvalue + "; " + expires;
    }
```

## 读取cookie

- var x = document.cookie;
- document.cookie 将以字符串的方式返回所有的 cookie，类型格式： “cookie1=value; cookie2=value; cookie3=value”

```javascript
    function getCookie(cname)
    {
        var name = cname + "=";
        var ca = document.cookie.split(';');
        for(var i=0; i<ca.length; i++)
        {
            var c = ca[i].trim();
            if (c.indexOf(name)==0)
                return c.substring(name.length,c.length);
        }
        return "";
    }
```

## 检查cookie，判断用户是否登录

```javascript
    function checkCookie() {
        var username=getCookie("username");
        if (username!="") {
            alert("Welcome again " + username);
        }else {
            username = prompt("Please enter your name:","");
            if (username!="" && username!=null) {
                setCookie("username",username,365);
            }
        }
    }
```
