# element-picview
基于vue element-ui 图片拖拽放大缩小查看组件
### 用法
 >组件例子
 ```vue
   <template>
       <div>
           <!-- 这vue组件依靠了element-ui 所以必须安装element-ui才有效  -->
           <picview src="http:chenyeah.cn/dev/1.jpg" width="200px" height="200px"></picview>
       </div>
   </template>
   <script>
   import picview from "./picview.vue";
   export default {
       data() {
           return {};
       },
       components: {
           picview
       }
   };
   </script>
```
### 效果
![img](https://github.com/chenyeah/element-picview/raw/master/gif.gif) 
#### 声明
业务需求紧 就直接基于element-ui 做了 后期做个不依赖element-ui的vue组件
