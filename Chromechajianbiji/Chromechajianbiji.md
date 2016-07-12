<!--
  author:donar
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-06-12
  title:Chrome 插件笔记
  tags:chrome插件
  category:技术笔记
  status:publish
  summary:简单的chrome插件配置，及如何拿cookie
-->

```json
{
  "manifest_version": 2, // 这个不重要  一般填2就好
  "name": "Test",       
  "description": "Donar.",
  "version": "1.0",
  "permissions": ["cookies","contextMenus","http://localhost/*", "https://*/*", "http://*/*",       "tabs"],// 需要获取的权限 包括你需要拓展的权限如cookie 以及域名权限
  "browser_action": {
    "default_icon": "icon.png",
    "default_popup": "popup.html" //这个是你点击插件图标弹出的窗口页面
  },
  "content_scripts": [
        {
            "matches": ["http://*/*","https://*/*"], 
            "js": ["js/content.js","js/jquery-1.7.min.js"],//这里是popup页面要加载的js文件 注意加载顺序
            "persistent": false //是否持久化  
        }
     ],
  "background": {
        "scripts": ["js/jquery-1.7.min.js","js/background.js"],//这里是背景页要加载的js文件 同样要注意js加载的顺序
        "persistent": false
    }
}
```

拿cookie

chrome.cookies.getAll({url:'http://www.baidu.com/'}, function(k) {

 a = "";

 for (var i = 0; i \< k.length; i++) {

 a += k[i].name + "=" + k[i].value + ";"

 }

 console.log(a);

});
