npm init -y
npm i -S underscore vue todomvc-app-css
vim index.html
复制格式化后的html
引入css
将英文标题换成中文标题
引入vue.js
新建vue的实例
写一个默认的任务：todoList: [{}]
```
<script src="./node_modules/vue/dist/vue.min.js" charset="utf-8"></script>
<script>
  // 新建一个Vue的实例对象
  var todoapp = new Vue({
    // 挂载
    el: '.todoapp',
    // 数据
    data: {

    },
    方法
    methods: {

    },
    // 计算属性
    computed: {

    }
  })
```

写todoList数据
```
data: {
  // 备忘录数组
  todoList: [
    // 一个任务就是一个对象，text表示任务的名称，checked为true表示已完成，false表示未完成
    {
      text: '学Vue',
      checked: false
    },
    {
      text: '学React',
      checked: false
    }
  ]
},
```
在li元素身上加v-for:
<li
  class="completed"
  v-for="(todo,index) in todoList" :key="'todo-'+index"
  >
显示任务
<label>{{ todo.text }}</label>
修改某个任务的checked为true
{
  text: '学Vue',
  checked: true
},
给li元素动态绑定class,completed样式的值，根据todo.checked, 如果todo.checked为 true则有completed样式
<li
  :class="{completed: todo.checked}"
  v-for="(todo,index) in todoList" :key="'todo-'+index"
  >
给checkbox加上v-model，值为todo.checked, checked属性会自动和todo.checked关联
<input class="toggle" type="checkbox" v-model="todo.checked"/>

添加任务
在data中新增
data: {
  // 新的备忘录
  newTodo: '',
  ...
}
input元素上面绑定
<input class="new-todo" placeholder="你接下来需要完成什么?" autofocus="" v-model="newTodo"/>
添加回车事件
<input class="new-todo" placeholder="你接下来需要完成什么?" autofocus="" v-model="newTodo" @keyup.enter="addTodo"/>
自动去前后的空格
<input class="new-todo" placeholder="你接下来需要完成什么?" autofocus="" v-model="newTodo" @keyup.enter.trim="addTodo"/>
添加任务的方法：
```
// 添加任务的方法
addTodo() {
  // 去除前后的空格
  this.newTodo = this.newTodo.trim();
  //如果内容为空则拦截
  if (this.newTodo.length < 1) {
    return;
  }
  // 添加任务到数组中,默认是未完成
  this.todoList.unshift({
    text: this.newTodo,
    checked: false
  });
  // 清空新任务的内容
  this.newTodo = ''
}
```
添加一个计算属性，判断是否显示任务列表
// 显示列表，如果任务条数大于1则显示
```
computed: {
  showList() {
    return this.todoList.length > 0;
  }
}
```
添加v-show:
<section class="main" v-show="showList">
<footer class="footer" v-show="showList">
绑定删除任务事件
<button class="destroy" @click="deleteTodo(todo)"></button>
引入underscore
<script src="./node_modules/underscore/underscore-min.js" charset="utf-8"></script>
删除任务方法
```
methods: {
  ...
  // 删除任务
  deleteTodo(todo) {
    this.todoList = _.without(this.todoList, todo)
  }
}
```
编辑任务
<input class="edit" type="text" v-model="todo.text" />
进入编辑模式
添加一个数据项：
data: {
  // 正在编辑的任务索引
  editingIndex: -1,
  ...
}
绑定双击事件：
<label @dblclick="editTodo(index)">{{ todo.text }}</label>
```
methods: {
  ...
  // 编辑任务
  editTodo(index) {
    // 设置一下当前正在编辑的索引
    this.editingIndex = index;
  }
}
```
加上class
<li
  :class="{completed: todo.checked, editing: index === editingIndex}"
  v-for="(todo,index) in todoList" :key="'todo-'+index"
  >
自定义指令：
````
// 注册一个全局自定义指令 v-focus
Vue.directive('focus', {
  // 当绑定元素插入到 DOM 中。
  inserted(el) {
    // 聚焦元素
    el.focus()
  },
  // 当绑定元素更新的时候
  update(el) {
    el.focus();
  }
})
```
使用自定义指令
<input class="edit" type="text" v-model="todo.text" v-focus="index === editingIndex"/>
添加保存todo, 实际上就是把input框取消
<input class="edit" type="text" v-model="todo.text" v-focus="index === editingIndex" @blur="saveTodo(todo)" @keyup.enter="saveTodo(todo)"/>
保存的方法
```
methods: {
  ...
  // 保存任务，因为是动态绑定的，不需要再保存，只需要把input框隐藏即可
  saveTodo(todo) {
    this.editingIndex = -1
    if (todo.text.trim().length < 1) {
      this.deleteTodo(todo)
    }
  }
}
```
未完成的数量
```
computed: {
  ....
  // 未完成的任务数量
  activeCount() {
    return this.todoList.filter(item => {
      return !item.checked
    }).length;
  }
}
```
