##  前段框架

### Vue 框架

Vue 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

如果你想在深入学习 Vue 之前对它有更多了解，我们制作了一个视频，带您了解其核心概念和一个示例工程。

如果你已经是有经验的前端开发者，想知道 Vue 与其它库/框架有哪些区别，请查看对比其它框架。

![](https://pic.imgdb.cn/item/613c74ab44eaada739e1f761.jpg)

### 创建一个 Vue 实例

每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的：

```js
var vm = new Vue({
  // 选项
})
```

虽然没有完全遵循 MVVM 模型，但是 Vue 的设计也受到了它的启发。因此在文档中经常会使用 vm (ViewModel 的缩写) 这个变量名表示 Vue 实例。

当创建一个 Vue 实例时，你可以传入一个选项对象。这篇教程主要描述的就是如何使用这些选项来创建你想要的行为。作为参考，你也可以在 API 文档中浏览完整的选项列表。

一个 Vue 应用由一个通过 new Vue 创建的根 Vue 实例，以及可选的嵌套的、可复用的组件树组成。举个例子，一个 todo 应用的组件树可以是这样的：

根实例
└─ TodoList    
   ├─ TodoItem     
   │  ├─ TodoButtonDelete    
   │  └─ TodoButtonEdit   
   └─ TodoListFooter   
      ├─ TodosButtonClear   
      └─ TodoListStatistics   

我们会在稍后的组件系统章节具体展开。不过现在，你只需要明白所有的 Vue 组件都是 Vue 实例，并且接受相同的选项对象 (一些根实例特有的选项除外)。

### 数据与方法
 
当一个 Vue 实例被创建时，它将 data 对象中的所有的 property 加入到 Vue 的响应式系统中。当这些 property 的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。

```javascript
// 我们的数据对象
var data = { a: 1 }
// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的 property
// 返回源数据中对应的字段
vm.a == data.a // => true

// 设置 property 也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```

当这些数据改变时，视图会进行重渲染。值得注意的是只有当实例被创建时就已经存在于 data 中的 property 才是响应式的。也就是说如果你添加一个新的 property，比如：

```javascript
vm.b = 'hi'
```
那么对 b 的改动将不会触发任何视图的更新。如果你知道你会在晚些时候需要一个 property，但是一开始它为空或不存在，那么你仅需要设置一些初始值。比如：

```javascript
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

这里唯一的例外是使用 Object.freeze()，这会阻止修改现有的 property，也意味着响应系统无法再追踪变化。

```javascript
var obj = {
  foo: 'bar'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
<div id="app">
  <p>{{ foo }}</p>
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = 'baz'">Change it</button>
</div>
```

除了数据 property，Vue 实例还暴露了一些有用的实例 property 与方法。它们都有前缀 $，以便与用户定义的 property 区分开来。例如：

```javascript
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
})
```

以后你可以在 API 参考中查阅到完整的实例 property 和方法的列表。

### 实例生命周期钩子
 
每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。

比如 created 钩子可以用来在一个实例被创建之后执行代码：

```javascript
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

也有一些其它的钩子，在实例生命周期的不同阶段被调用，如 mounted、updated 和 destroyed。生命周期钩子的 this 上下文指向调用它的 Vue 实例。

不要在选项 property 或回调上使用箭头函数，比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。因为箭头函数并没有 this，this 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 Uncaught TypeError: Cannot read property of undefined 或 Uncaught TypeError: this.myMethod is not a function 之类的错误。

### 生命周期图示

下图展示了实例的生命周期。你不需要立马弄明白所有的东西，不过随着你的不断学习和使用，它的参考价值会越来越高。

<div align="center">
<img src="https://cn.vuejs.org/images/lifecycle.png"  height="1600" width="800">
</div>


### 条件渲染 v-if

v-if 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染。

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

也可以用 v-else 添加一个“else 块”：

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

在 template 元素上使用 v-if 条件渲染分组
因为 v-if 是一个指令，所以必须将它添加到一个元素上。但是如果想切换多个元素呢？此时可以把一个 template 元素当做不可见的包裹元素，并在上面使用 v-if。最终的渲染结果将不包含 template 元素。

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### 列表渲染

我们可以用 v-for 指令基于一个数组来渲染一个列表。v-for 指令需要使用 item in items 形式的特殊语法，其中 items 是源数据数组，而 item 则是被迭代的数组元素的别名。

```html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">
    {{ item.message }}
  </li>
</ul>
```
```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

结果：

```bash
Foo
Bar
```

在 v-for 块中，我们可以访问所有父作用域的 property。v-for 还支持一个可选的第二个参数，即当前项的索引。

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```
```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

结果：
```bash
Parent-0-Foo
Parent-1-Bar
```

你也可以用 of 替代 in 作为分隔符，因为它更接近 JavaScript 迭代器的语法：

```html
<div v-for="item of items"></div>
```

#### 在 v-for 里使用对象

你也可以用 v-for 来遍历一个对象的 property。

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```
```js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

结果：
```bash
How to dolist in Vue
Jane Doe
2016-04-10
```

你也可以提供第二个的参数为 property 名称 (也就是键名)：

```html
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

### Home 

```html
<template xmlns:cursor="http://www.w3.org/1999/xhtml">
 <div class="recommonContain">
   <!--引入头部组件，包含头部导航栏-->
   <newheader :active="isActive" @onGetnews="getCateNews"></newheader>
   <!--主页面展示区域-->
   <div class="mainContent">
     <!--主展示区的左边栏-->
     <div class="mainLeft">
       <ul class="leftContent">
         <el-row class="newsItem"  v-for="item in newsData.news" :key="item.new_id" @click.native="newsDesc({'newid':item.new_id, 'cateid': newsData.cate_id})">
           <h3 class="newsTitle">
             {{item.new_title}}
             <span class="newsOther">{{item.new_time}}</span>
             <span v-if="isActive==1" class="newsCate"><i class="el-icon-loading
"></i> {{item.new_cate}}</span>
           </h3>
           <p class="newsContent">{{item.new_content}}</p>
         </el-row>
         <div v-if="morebtn & isActive==1" class="moreBtn">
           <!--<span @click="showMore">加载更多</span>-->
           <el-button type="primary" @click="showMore">加载更多</el-button>
         </div>
         <li class="moreNewsItem" v-if="moreRecommon" v-for="item in newsOtherdata.news" :key="item.new_id+10" @click="newsDesc({'newid':item.new_id,'cateid':newsData.cate_id})">
           <h3 class="newsTitle">
             {{item.new_title}}
             <span class="newsOther">{{item.new_time}}</span>
             <span class="newsCate">{{item.new_cate}}</span>
           </h3>
           <p class="newsContent">{{item.new_content}}</p>
         </li>
       </ul>
       <pagnation v-if="total>10" :total="total" :current-page="current" :refresh="refresh" @pagechange="pagechange"></pagnation>
     </div>
     <!--主展示区的右边栏-->
   </div>
   <!--引入尾部组件，包含尾部信息-->
   <newfooter class="footer"></newfooter>
 </div>
</template>

<script>
import Header from './Header'
import Footer from './Footer'
import Pagnation from '../components/Pagnation'
import {getCateNewsData} from '../router/apis'

export default {
  name: 'Home',
  data () {
    return {
      isActive: true,
      morebtn: false,
      moreRecommon: false,
      newsData: {
      },
      newsOtherdata: {
      },
      hot_newsData: {},
      // 分页相关参数
      total: 0, // 总条数
      current: 1, // 当前激活页
      display: 3, // 每页显示多少条
      refresh: false // 是否刷新（第一页激活）有搜索时需要
    }
  },
  components: {
    'newheader': Header,
    'newfooter': Footer,
    'pagnation': Pagnation
  },
  methods: {
    getCateNews: function (option) {
      let cateId = option.cateid
      let getdata = {}
      if (cateId === '2') {
        // this.loading('加载中。。。')
        getdata = {
          cateid: '2',
          userName: this.$store.state.vuexlogin.userName
        }
        getCateNewsData(getdata).then((res) => {
          if (!res.code) {
            this.$children[0].layout()
          } else {
            res.news.forEach((item) => {
              this.$layer.closeAll()
              item.new_time = this.timeFormat(item.new_time)
            })
            this.total = res.total
            this.hot_newsData = res
          }
          // eslint-disable-next-line handle-callback-err
        }, (err) => {
          this.$layer.msg('小主稍等，紧急恢复中。。。')
        })
      } else if (cateId === '1') {
        // this.loading('加载中。。。')
        if (option.tags) {
          getdata.tags = option.tags
          getdata.baseclick = option.baseclick
        } else {
          getdata.tags = ''
          getdata.baseclick = option.baseclick
        }
        if (option.baseclick === '0') {
          getdata.baseclick = 0
        } else {
          getdata.baseclick = 1
        }
        getdata.cateid = '1'
        this.isActive = cateId
        getdata.userName = this.$store.state.vuexlogin.userName
        getCateNewsData(getdata).then((res) => {
          this.$layer.closeAll()
          if (!res.code) {
            this.$children[0].layout()
          } else {
            res.news.forEach((item) => {
              item.new_time = this.timeFormat(item.new_time)
            })
            this.total = res.total
            this.newsData = res
            if (res.news.length >= 10) {
              let arr1 = res.news.slice(0, 10)
              let arr2 = res.news.slice(11)
              this.newsData.news = arr1
              this.newsOtherdata.news = arr2
              this.morebtn = true
            } else {
              this.newsData = res
              this.newsOtherdata = {}
              this.morebtn = false
            }
          }
          // eslint-disable-next-line handle-callback-err
        }, (err) => {
          this.$layer.msg('小主稍等，紧急恢复中。。。')
        })
      } else {
        // this.loading('加载中。。。')
        if (cateId) {
          getdata.cateid = cateId
          this.isActive = cateId
        }
        if (option.pageid) {
          getdata.pageid = option.pageid
        } else {
          getdata.pageid = 1
        }
        getdata.userName = this.$store.state.vuexlogin.userName
        getCateNewsData(getdata).then((res) => {
          this.$layer.closeAll()
          if (!res.code) {
            this.$children[0].layout()
          } else {
            res.news.forEach((item) => {
              item.new_time = this.timeFormat(item.new_time)
            })
            this.total = res.total
            this.newsData = res
          }
          // eslint-disable-next-line handle-callback-err
        }, (err) => {
          this.$layer.msg('小主稍等，紧急恢复中。。。')
        })
      }
    },
    newsDesc: function (opt) {
      console.log('获取新闻')
      this.$router.push({
        name: 'news',
        query: {cateid: opt.cateid, newid: opt.newid}
      })
    },
    // 推荐更多
    showMore () {
      this.moreRecommon = true
      this.morebtn = false
    },
  },
  mounted () {
    this.getCateNews({'cateid': '2'})
    var tags = this.$route.query.tags
    var baseclick = this.$route.query.baseclick + ''
    if (this.$route.params.cateid) {
      this.getCateNews({'cateid': this.$route.params.cateid})
    } else {
      this.getCateNews({'cateid': '1', 'tags': tags, 'baseclick': baseclick})
    }
  }
}
</script>

<style scoped="scoped">
  .el-header {
    background: rgb(55, 61, 65);
    display: flex;
    justify-content: space-between;
    line-height: 60px;
    color: #fff;
    font-size: 22px;
  }
  .recommonContain {
    width: 100%;
    box-sizing: border-box;
    background-color: #fff;  /*新闻显示区域背景颜色*/
    display: flex;
  }
</style>
```

### 新闻展示页面

```html
<template>
  <div class="newsContent">
    <new-header @onGetnews="returnMain" :active="newCateId"></new-header>
    <div class="mainNews animated zoomIn">
      <h3 class="newsTitle">{{newDesc.new_title}}</h3>
      <span class="newsAttribute">
        <el-button type="info" size="mini">{{newDesc.new_time}}</el-button>
        <el-button type="primary" size="mini" icon="el-icon-map-location">{{newDesc.new_cate}}</el-button>
        <el-badge :value="newDesc.new_seenum" class="item">
          <el-button type="success" size="mini" icon="el-icon-view">浏览次数</el-button>
        </el-badge>
        <el-badge :value="newDesc.new_disnum" class="item">
          <el-button type="warning" size="mini" icon="el-icon-chat-dot-square">评论次数</el-button>
        </el-badge>
        <el-badge class="item">
          <el-button type="danger"  icon="el-icon-microphone" size="mini" @click="playVoice">语音播报</el-button>
          <el-button type="danger"  icon="el-icon-turn-off-microphone" size="mini" @click="handleStop">停止播报</el-button>
        </el-badge>
      </span>
      <div class="news" v-html="newDesc.new_content">
      </div>
      <el-row class="Info">
          <i class="el-icon-star-on" style="color: #55a532">收藏</i>
        <i class="el-icon-success" style="color: #55a532">点赞</i>
          <i class="el-icon-share" style="color: #55a532">分享</i>
      </el-row>
      <div class="discuss">
        <h3>评论</h3>
        <el-input
          class="discussContent"
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="textarea">
        </el-input>
        <el-row class="btndiscuss">
          <el-button type="primary" icon="el-icon-edit" circle></el-button>
          <el-button type="success" icon="el-icon-circle-check" @click="dis_submit" circle></el-button>
          <el-button type="danger" icon="el-icon-delete" @click="dis_delete" circle></el-button>
        </el-row>
        <div class="discontent" >
          <li v-for="(dis,idx) in disContents" :key="idx" :value="dis">
            {{dis}}
          </li>
        </div>
      </div>
    </div>
    <div class="mainRight animated fadeInRight" style="animation-duration:1s;animation-delay:0.5s">
      <h3 class="hotTitle">相似推荐</h3>
      <ul class="rightContent">
        <li v-for="item in newDesc.new_sim" @click="newsDesc({'newid':item.new_id,'cateid':item.new_cate})" :key="item.new_id">
          <span>{{item.new_title}}</span>
          <i>{{item.new_time}}</i>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import {getNewsData} from '../router/apis'
import Header from './Header'
// 语音播报
const synth = window.speechSynthesis
const msg = new SpeechSynthesisUtterance()

export default {
  name: 'News',
  data () {
    return {
      value: '3',
      textarea: '',
      newDesc: {},
      newCateId: '',
      counter: 0,
      disContents: {}
    }
  },
  components: {
    'new-header': Header
  },
  methods: {
    // 展示评论
    display_disContent () {
      if (this.disContents.size()) {
        console.log(this.disContents[0])
      }
    },

    // 提交评论按钮，评论数加1
    dis_submit () {
      this.counter += 1
      this.disContents.append(this.textarea)
    },

    // 新闻内容语音播报
    playVoice () {
      // console.log(this.newDesc.new_content)
      this.handleSpeak(this.newDesc.new_content) // 传入需要播放的文字
    },

    // 语音播报的函数
    handleSpeak (text) {
      msg.text = text // 文字内容: 小朋友，你是否有很多问号
      msg.lang = 'zh-CN' // 使用的语言:中文
      msg.volume = 1 // 声音音量：1
      msg.rate = 1 // 语速：1
      msg.pitch = 1 // 音高：1
      synth.speak(msg) // 播放
    },
    // 语音停止
    handleStop (e) {
      msg.text = e
      msg.lang = 'zh-CN'
      synth.cancel(msg)
    },
    getNews: function (option) {
      this.loading('加载中。。。')
      option.userName = this.$store.state.vuexlogin.userName
      getNewsData(option).then((res) => {
        this.$layer.closeAll()
        if (!res.code) {
          this.$children[0].layout()
        } else {
          res.new_time = this.timeFormat(res.new_time)
          res.new_sim.forEach(item => {
            item.new_time = this.timeFormat(item.new_time)
          })
          res.new_content = this.returnline(res.new_content, '\\n', '</br>')
          this.newDesc = res
        }
      }, (err) => {
        this.$layer.msg(err)
      })
    },
    returnMain: function (option) {
      this.$router.push({name: 'home', params: option})
    },
  },
  mounted () {
    var newId = this.getUrlparams(window.location.href).newid
    this.newCateId = this.getUrlparams(window.location.href).cateid
    this.getNews({'newid': newId})
  }
}
</script>

<style scoped="scoped">
  .newsContent {
    width: 100%;
    box-sizing: border-box;
    overflow: auto;
  }
  .mainNews {
    width: 70%;
    float: left;
    padding: 20px;
    box-sizing: border-box;
    padding-top: 0;
  }
  .newsTitle {
    font-size: 30px;
    line-height: 40px;
    margin: 20px 0;
    font-weight: 600;
  }
  .newsAttribute {
    display: block;
    font-size: 18px;
    color: #999;
    height: 20px;
    padding: 1px;
    flex: 1;
    text-align: right;
    margin-right: 100px;
  }
  .news {
    color: #222222;
    font-size: 18px;
    line-height: 30px;
    text-space: 2px;
    padding: 20px;
    box-sizing: border-box;
  }
  .mainRight {
    width: 30%;
    float: right;
  }
  .mainRight > h3 {
    width: 100%;
    padding: 10px;
    margin-top: 15px;
    background: #63a35c;
    vertical-align: middle;
    box-sizing: border-box;
  }
</style>
```