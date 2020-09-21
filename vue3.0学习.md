## 安装

```bash
npm install vue@next
vue add vue-next
```

```
npm init vite-app <project-name>
cd <project-name>
npm install
npm run dev
```



## 安装vite



```
npm install create-vite-app
create-vite-app <project-name>
```

### demo

```
<template>
  <div>
    {{con}}
    <input type="button" value="myfun" @click="myfun" />
    <ul>
      <li v-for="(data,index) in arr.s" :key="index" @click="removearr(index)">{{data.id}}--{{data}}</li>
    </ul>
  </div>
</template>

<script>
// ref 简单类型变化，不能监听对象数组变化
import { ref, reactive } from "vue";

export default {
  setup() {
    let { con, arr, myfun, removearr } = myfuntest();
    return { con, arr, myfun, removearr };
    //   数据
    // let con = ref(10);
    // let arr = reactive({
    //   s: [{ id: 1 }, { id: 2 }, { id: 3 }],
    // });
    // // 方法
    // function myfun() {
    //   //   alert("ok");
    //   console.log(con.value);
    // }
    // function removearr(index) {
    //   // 过滤器
    //   arr.s = arr.s.filter((s, id) => id !== index);
    // }
    // return { con, arr, myfun, removearr };
  },
};

function myfuntest() {
  //   数据
  let con = ref(10);
  let arr = reactive({
    s: [{ id: 1 }, { id: 2 }, { id: 3 }],
  });
  // 方法
  function myfun() {
    //   alert("ok");
    console.log(con.value);
  }
  function removearr(index) {
    // 过滤器
    arr.s = arr.s.filter((s, id) => id !== index);
  }
  return { con, arr, myfun, removearr };
}
</script>
```

# 生命周期

```
<template>
  <div>
    <div>
      <input type="button" value="dmeo3" @click="update"/>
    </div>
  </div>
</template>

<script>
import { onMounted, onUpdated, onUnmounted } from "vue";
export default {
  setup() {
      function update(){
          console.log("update");
      }
    //   生命周期函数
    onMounted(() => {
      console.log("mounted!");
    });
    onUpdated(() => {
      console.log("updated!");
    });
    onUnmounted(() => {
      console.log("unmounted!");
    });
    return {update};
  },
};
</script>

<style>
</style>
```

