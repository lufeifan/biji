# 重定向

```
 { path: '/', redirect: '/index'},
```

# 子路由

```
children: [
        {
          path: 'collection',//以“/”开头的嵌套路径会被当作根路径，所以子路由上不用加“/”;在生成路由时，主路由上的path会被自动添加到子路由之前，所以子路由上的path不用在重新声明主路由上的path了。
          name: 'Collection',
          component: Collection
        },
```

# 路由跳转

```
this.$router.push({ name: "Detail", params: { number: number } });
```

