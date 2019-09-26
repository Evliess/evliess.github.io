---
layout: index
title: "RequireJs Usage"
category: javascript
date: 2018-06-07 15:17:55
---

# Project Information  
An quick start project with requirejs, bootstrap and codemirror.js

## Project structure

- www/
	- html
		- index.html
	- js/
		- lib/  
			- jquery.1.11.3.min.js  
			- bootstrap.min.js  
			- bootstrap.bundle.min.js  
			- codemirror.js  
			- javascript.js  
			- scheme.js  
			- require.js  
		- app/  
			- index.js

			require-common.js  
			require-index.js  
	- css/  
		- bootstrap.min.css  
		- bootstrap-grid.min.css  
		- codemirror.css  


## Requirejs configuration  
- index.html  

```
<script data-main="../js/require-index.js" src="../js/lib/require.js"></script>

```

- require-common.js  

```javascript
require.config({
    baseUrl: "../js/lib", //从index.html页面导航到js文件夹
    "paths": {
        "jquery": "jquery.1.11.3.min",
        "codemirror": "codemirror",
        "javascript": "javascript",
        "scheme": "scheme"
    },
    "shim": {
        "jquery": {
            exports: "jquery"
        },
        "codemirror": {
            exports: "codemirror"
        },
        "javascript": {
            exports: "javascript"
        },
        "scheme": {
            exports: "scheme"
        }
    }
});
```

- require-index.js  

```javascript
require(["./require-common"], function(common) {
    require(["../js/app/index.js"]); //从index.html页面引用index.js文件的路径
})
```

- index.js  

```javascript
define(["jquery", "codemirror", "javascript", "scheme"], function($, CodeMirror, javascript, scheme) {
    $(function() {

        var ele = $("#tx1")[0];
        var editor = CodeMirror.fromTextArea(ele, {
            mode: "scheme",
            lineNumbers: true
        });

        var looksLikeScheme = function(code) {
            return !/^\s*\(\s*function\b/.test(code) && /^\s*[;\(]/.test(code);
        };

        var update = function() {
            editor.setOption("mode", looksLikeScheme(editor.getValue()) ? "scheme" : "javascript");
        };

        var pending;

        editor.on("change", function() {
            clearTimeout(pending);
            pending = setTimeout(update(), 400)
        });

    });

});
```


## Codemirror configuration  
Because I put the codemirror.js, javascript.js and scheme.js in the same folder. So I need so some customizations in javascript.js and scheme.js.  

- JavaScript.js  

```javascript
(function(mod) {
  if (typeof exports == "object" && typeof module == "object") // CommonJS
    mod(require("../../lib/codemirror"));   // ("../../lib/codemirror") -- > ("codemirror") 修改相对路径
  else if (typeof define == "function" && define.amd) // AMD
    define(["../../lib/codemirror"], mod); // ("../../lib/codemirror") -- > ("codemirror") 修改相对路径
  else // Plain browser env
    mod(CodeMirror);
})(function(CodeMirror) {

```

- scheme.js  

```javascript
(function(mod) {
  if (typeof exports == "object" && typeof module == "object") // CommonJS
    mod(require("codemirror"));  // ("../../lib/codemirror") -- > ("codemirror") 修改相对路径
  else if (typeof define == "function" && define.amd) // AMD
    define(["codemirror"], mod);  // ("../../lib/codemirror") -- > ("codemirror") 修改相对路径
  else // Plain browser env
    mod(CodeMirror);
})(function(CodeMirror) {
"use strict";

```

[Download](https://github.com/Evliess/MyDemos)


