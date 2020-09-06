# 安装

```
npm install -g @vue/cli 
```

# 卸载

```
npm uninstall @vue/cli -g
```

# 查询版本

```
npm view @vue/cli versions --json
```

# 查看版本

```
vue -V
```

# 创建项目

```
vue ui
```

# 指定版本安装

```
npm install -g @vue/cli@版本号
```

# 基本构架

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <div id="example">
            <p>Original message: "{{ message }}"</p>
            <p>Computed reversed message: "{{ reversedMessage }}"</p>
        </div>
        <input type="text" v-model="cityName"/>
    </div>

    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                message: 'Hello Vue!',
                cityName: 'shanghai'
            },
            // 计算属性
            computed: {
                // this.message 值不改变 就不会调用 reversedMessage，this.message 的值被缓存起来了
                // 计算属性的 getter
                reversedMessage: function () {
                    // `this` 指向 vm 实例
                    return this.message.split('').reverse().join('')
                }
            },
            //侦听属性
            watch: {
                cityName(newName, oldName) {
                    // ...
                    this.message =newName
                }
            },
            methods: {
                
            },
            // 生命周期函数
            beforeCreate() {
                console.log("beforeCreate")
            },
            created() {
                // `this` 指向 vm 实例
                console.log("created")
            },
            beforeMount() {
                console.log("beforeMount")
            },
            mounted() {
                console.log("mounted")
            },
            beforeUpdate() {
                console.log("beforeUpdate")
            },
            updated() {
                console.log("updated")
            },
            activated() {
                console.log("被 keep-alive 缓存的组件激活时调用。")
            },
            beforeDestroy() {
                console.log("beforeDestroy")
            },
            destroyed() {
                console.log("destroyed")
            },
        })
    </script>
</body>

</html>
```

