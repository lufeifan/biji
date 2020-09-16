```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

</head>

<body>

    <div id="demo">
        demo
    </div>
    <div class="demo1">
        demo1
    </div>
    <div id="demo2">

    </div>
    <div class="demo4"></div>
    <hr>
    <button type="submit" onclick="myfunction()">dianji</button>
    <hr>
    <input type="button" value="mufunction2" onclick="mufunction2('参数1','参数2')">
    <hr>
    <form name="myform" onsubmit="return validateFrom()" method="POST">
        名字
        <input type="text" name="fname" id="">
        <input type="submit" value="提交">

    </form>
    <hr><a href="javascript:void(0)">单击此处什么也不会发生</a>
    <a href="javascript:void(alert('Warning!!!'))">点我!</a>
</body>
<script>
    // 弹出警告框
    // alert("我的第一个javaScript");
    // 将内容写到html 标签
    document.write("<h1> 我的h1 标签</h1><hr>");
    // 变量
    var name = "陆非凡"
    // document.write(name);
    // 函数
    function myfunction() {
        // 写入html 会替换原来内容
        document.getElementById("demo").innerHTML = "我的第一个js 函数";
        document.getElementById("demo2").innerHTML = name;
        document.getElementsByClassName("demo1")[0].innerHTML = "demo1 getElementsByClassName";
        console.log("输出到控制台");
    }
    // 带参数函数
    function mufunction2(var1, var2) {
        console.log(var1);
        console.log(var2);
    }
    // 对象
    var student = {
        age: "nasdn",
        address: "柳州",
        // 对象方法
        msg: function () {
            return this.age + " " + this.address;
        }
    };
    document.getElementsByClassName("demo4")[0].innerHTML = student.msg();
    document.write("星期" + new Date().getDay());
    // 表单验证
    function validateFrom() {
        var x = document.forms["myform"]["fname"].value;
        console.log(x);
        if (x == null || x == "") {
            alert("需要名字")
        } else {
            alert("false")
        }
    }
    // JSON 
    var jsontext = '{ "sites" : [' +
        '{ "name":"Runoob" , "url":"www.runoob.com" },' +
        '{ "name":"Google" , "url":"www.google.com" },' +
        '{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
    obj = JSON.parse(jsontext);
    document.write("<hr>" + obj.sites[0].name + "<hr>");
    document.write(obj.sites[1].name);
    // 异步
    // function print() {
    //     document.getElementById("demo").innerHTML = "RUNOOB!";
    // }
    // setTimeout(print, 3000);

    setTimeout(function () {
        document.getElementById("demo").innerHTML = "RUNOOB!";
    }, 3000);

    // Promise
    new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log("First");
            resolve();
        }, 1000);
    }).then(function () {
        return new Promise(function (resolve, reject) {
            setTimeout(function () {
                console.log("Second");
                resolve();
            }, 4000);
        });
    }).then(function () {
        return new Promise(function (resolve, reject) {
            setTimeout(function () {
                console.log("Third");
                resolve();
            }, 3000);
        })
    }).then(function () {
        setTimeout(function () {
            console.log("Second");
        }, 4000);
    });
</script>

</html>
```

