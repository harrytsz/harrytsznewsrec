### 游戏开发引擎 Cocos Creator

### 简介
Cocos Creator 是以内容创作为核心的游戏开发工具，在 Cocos2d-x 基础上实现了彻底脚本化、组件化和数据驱动等特点。

![](https://pic.imgdb.cn/item/613c682a44eaada739cb4a2b.jpg)

### 工作流程说明

在开发阶段，Cocos Creator 已经能够为用户带来巨大的效率和创造力提升，但我们所提供的工作流远不仅限于开发层面。对于成功的游戏来说，开发和调试、商业化 SDK 的集成、多平台发布、测试、上线这一整套工作流程不光缺一不可，而且要经过多次的迭代重复。

![](https://pic.imgdb.cn/item/613c692544eaada739cd0e37.jpg)

Cocos Creator 将整套手机页游解决方案整合在了编辑器工具里，无需在多个软件之间穿梭，只要打开 Cocos Creator 编辑器，各种一键式的自动化流程就能花最少的时间精力，解决上述所有问题。开发者就能够专注于开发阶段，提高产品竞争力和创造力！

### 创建或导入资源

将图片、声音等资源拖拽到编辑器的 资源管理器 面板中，即可完成资源导入。

![](https://pic.imgdb.cn/item/613c6fc944eaada739d90b62.jpg)

此外，你也可以在编辑器中直接创建场景、预制、动画、脚本、粒子等各类资源。

![](https://pic.imgdb.cn/item/613c6c8b44eaada739d32900.jpg)

### 搭建场景

项目中有了一些基本资源后，我们就可以开始搭建场景了，场景是游戏内容最基本的组织方式，也是向玩家展示游戏的基本形态。

添加组件脚本，实现交互功能
我们可以为场景中的节点挂载各种内置组件和自定义脚本组件，来实现游戏逻辑的运行和交互。包括从最基本的动画播放、按钮响应，到驱动整个游戏逻辑的主循环脚本和玩家角色的控制。几乎所有游戏逻辑功能都是通过挂载脚本到场景中的节点来实现的。

![](https://pic.imgdb.cn/item/613c6cdb44eaada739d3b945.jpg)

![](https://pic.imgdb.cn/item/613c6e8644eaada739d6b2ad.jpg)

其中，控制首页精灵旋转逻辑的代码如下:

```javascript
// Learn cc.Class:
//  - https://docs.cocos.com/creator/manual/en/scripting/class.html
// Learn Attribute:
//  - https://docs.cocos.com/creator/manual/en/scripting/reference/attributes.html
// Learn life-cycle callbacks:
//  - https://docs.cocos.com/creator/manual/en/scripting/life-cycle-callbacks.html
cc.Class({
    extends: cc.Component,
    properties: {
        logo: cc.Sprite,
        dialog: cc.Label,
        particle:{
            default:null,
            type:cc.ParticleSystem,  // 粒子火箭尾巴特效
        },
    },
    // LIFE-CYCLE CALLBACKS:
    onLoad () {
        // //触摸结束事件
        // this.node.parent.on(cc.Node.EventType.TOUCH_END, this.onTouchEnd, this);
    },
    start () {
        // var str = "Hi, Welcome!!!";
        this.particle.stopSystem();  // 关闭粒子效果
        this.dialog.string = "Hi, Welcome to my world!";
        this.scheduleOnce(function () {
            // this.dialog.string = str;
            this.setText();
            // this.dialog.string = "";
            this.closeDialog();
        }, 2);

    },
    closeDialog: function () {
        this.scheduleOnce(function () {
            this.dialog.node.active = false;
            this.logo.node.active = false;
        }, 6);
    },
    setText: function () {
        var str = "欢迎您 " + "\n希望您在这里度过一段愉快的时光";
        var j = 0;
        this.dialog.string = "";
        this.schedule(function () {
            this.dialog.string += str[j];
            j++;
        }, 0.1, str.length - 1, 0.2);
    },
    rocketUp: function() {
        // console.log(this.node.getPosition().x);
        // 打开粒子效果
        this.particle.resetSystem();
        // 移动指定距离
        var mby = cc.moveBy(2,cc.v2(0,200));
        this.node.runAction(mby);
    },
    btn_click () {
        // this.dialog.string = "";
        // var str = "Bye!!!";
        this.rocketUp();
        // this.dialog.string = str;
        this.scheduleOnce(function (f) {
            // window.location.href = "http://www.baidu.com";
            // 创建新场景
            cc.director.loadScene('home');
        }, 2);
    },
    // update (dt) {
    //
    // },
    //this.particle.stopSystem();//停止播放
    // this.particle.resetSystem();//重新启用
});
```

### 一键预览和发布

搭建场景和开发功能的过程中，你可以随时点击预览来查看当前场景的运行效果。使用手机扫描二维码，可以立即在手机上预览游戏。当开发告一段落时，通过 构建发布 面板可以一键发布游戏到包括桌面、手机、Web 等多个平台。

### 功能特性

Cocos Creator 功能上的突出特色包括：

- 脚本中可以轻松声明可以在编辑器中随时调整的数据属性，对参数的调整可以由设计人员独立完成。
- 支持智能画布适配和免编程元素对齐的 UI 系统可以完美适配任意分辨率的设备屏幕。
- 专为 2D 游戏打造的动画系统，支持动画轨迹预览和复杂曲线编辑功能。
- 使用动态语言支持的脚本开发，包括 JavaScript 和 TypeScript，使得动态调试和移动设备远程调试变得异常轻松。
- 底层由 Cocos2d-x 演化而来，在享受脚本化开发的便捷时，保持原生级别的轻量和高性能。
- 脚本组件化和开放式的插件系统为开发者在不同深度上提供了定制工作流的方法，编辑器可以大尺度调整来适应不同团队和项目的需求。

### 架构特色

Cocos Creator 包含游戏引擎、资源管理、场景编辑、游戏预览和发布等游戏开发所需的全套功能，并且将所有的功能和工具链都整合在了一个统一的应用程序里。

编辑器在提供强大完整工具链的同时，提供了开放式的插件架构，开发者能够用 HTML + JavaScript 等前端通用技术轻松扩展编辑器功能，定制个性化的工作流程。

以下，是 Cocos Creator 的技术架构图

![](https://pic.imgdb.cn/item/613c69d444eaada739ce4b75.jpg)

![](https://pic.imgdb.cn/item/613c69ff44eaada739ce9832.jpg)

从图中我们可以看出，编辑器是由 Electron 驱动再结合引擎所搭建的开发环境，引擎则负责提供许多开发上易于使用的组件和适配各平台的统一接口。

