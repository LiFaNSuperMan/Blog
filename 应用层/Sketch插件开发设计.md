# Sketch插件开发设计

## Sketch介绍

Sketch是在设计师中非常流行的一款设计师工具。Sketch除了常规的拖拽内置的形状、样式构成设计稿之外，还可以通过安装插件，各个方面提高设计师的工作效率，也可以通过插件减少公司各个团队与设计师团队的沟通成本。

## 插件能力介绍

Sketch插件能实现的功能很多。下面举例一下常用的插件以及他们实现的能力

 -	`Sketch-measure`插件，为设计师提供标注导出等这方面的能力，
 -	`Fusion-cool`插件，为设计师提供`antd`组件库资源

我司插件实现是能力是为设计师提供内部组件资源，组件可以让设计师进行交互，确定样式后转换成为Sketch数据，设计师导出设计稿提供给开发人员，开发人员拿到设计稿可以依据设计稿中组件信息，快速定位所用组件，加速开发，提高工作效率。

## 插件基本设计

现阶段来说，国内的sketch相关资料相对少了一些，这点对于开发者来说不是很友好。插件开发主要借助的`CocoaScript`，可以认为是一种通信`bridge`。Sketch本身是一款MacOS软件，通过`CocoaScript Bridge`可以实现调用Sketch一些MacOS原生能力。

 现在我司插件设计使用的是`CocoaScript+React+JS`方案

 - `CocoaScript` 这部分代码主要是用于操作Sketch原生面板，我们设计的方案是在Sketch自带的面板上进行部分修改，加入自己的页面，通过`CocoaScript`可以检索到Sketch编辑面板，利用一些原生的`iOS/MacOS`开发知识，就能顺利完成对Sketch自带编辑面板的修改
 - `React` 对Sketch自带编辑面板修改后，加入新的`Webview`用于承载一些前端页面，这些前端页面主要就是需要提供给设计师的能力，这里不再继续使用`CocoaScript`调用`MacOS`原生能力，反而使用前端技术提供能力，可以避免插件本身的频繁更新
 - `JS` 由于我司插件能力是要求内部组件库可以提供到设计师作为设计资源，所以需要多个前端页面与Sketch本身进行通信，所以插件侧`JS`代码主要实现的就是页面与插件侧通信能力与插件与Sketch本身的通信能力。

以上基本就是我司插件整体设计的大概流程。除了这种设计方案外，市面上的插件还有`CocosScript+Framework+JS`的设计方案。这种方案的好处就是将大部分代码转移至`Framework`中实现，可以通过`Xcode`工具进行`Debug`,对于开发者来说体验好了很多，除此之外，`Framework`对于代码的保护也比直接将代码放在插件侧进行编译好的多。我司将也在插件稳定迭代后把整体的设计方案切换到`CocosScript+Framework+JS+React`这种方案

## 通信机制

在Sketch插件开发中，前端页面与插件侧的通信、数据交换尤为重要。Sketch实现了一套`postMessage`通信机制，我们现在在做数据交换主要就是通过这种方式。不过这套通信机制在部分前端页面加载时会存在一些`BUG`,这个咱们可以之后再说。

## 转换流程

我司插件的一个主要能力就是将`html`节点转换为`sketch`数据。主要是分为以下步骤

-   `React加载组件` 第一步首先确定需要转换的节点数据，我们的方案是在指定页面上指定的父节点加载组件数据，然后任务父节点下所有的节点都是需要转换的数据，这里有一个问题，有的组件会在body下直接进行操作，例如`Modal`类组件，对于这种组件，需要做好页面上多余节点的监听，个人建议在加载组件的指定面板上不过多的加载其他数据，保证一个干净的组件加载环境。
-   `React->插件侧`现在市面上有一些提供这种能力的库，比如`html-sketchapp`,我们现在使用的是`ant-design`提供的`html2sketch`工具，现阶段来说转换能力基本满足常见的`html节点`，对于一些特定情况，也可以通过一些定制化内部版本完成。通过`html2sketch`工具可以得到完全符合`SketchFormatJson`的`Json`数据结构，得到节点转换的数据后就可以通过sketch提供的`postMessage`方式将数据提供给插件侧
-   `插件侧->sketch` 通过监听指定的转换完成信息，拿到指定的节点`Json`信息，接下来，只要将`Json`转换为`Sketch Symbol`即可，这里我们使用的是`asketch2sketch`工具。因为节点数据已经完成符合了sketch的数据接口，所以直接调用指定的API即可
    -   这里存在一个问题，就是如何将节点转换得到的`layer`准确的放在设计师想要在sketch面板上的指定位置，我们的解决方案是监听sketch面板上的layer新增动作，这可以通过sketch提供的`sectionChangeAction`来实现。得到最新`layer`的`objectID`和`frame`信息，这通过正常的读取属性来做即可。之后在接到节点`Json`信息后，可以利用这两个信息，确定设计师想要加载对应组件设计稿位置。

## 疑难点

在整体开发中，由于国内资料缺失较少，所以开发进度还是比较缓慢，现在遇到的主要疑难点集中于以下

-   sketch编辑面板如何重定义
    -   这个最终通过不同的看log信息，找到了合适加载`webview`的位置，工作有些繁琐。。。但是不难
-   节点转换库针对不同组件转换时需要的问题
    -   现阶段我们使用的是`ant-design`提供的`html2sketch`工具，但是面对复杂的css样式，工具能力对于一些特定情况还是有点不够用，比如文字换行问题、水印组件的背景文字切割问题、api兼容性问题，这些问题都是在具体组件使用过程中才能暴露出来，面对这种情况只能尽量的在测试的时候覆盖更多的情况，暂时没什么办法完美的fix这个问题
-   `Error:mission action name`
    -   这个问题也是在部分组件加载中碰到的问题，后面追了下代码发现了sketch加载了个附加脚本，里面重写了`postMessage`这个方法，对于发送空消息的行为，sketch直接抛出了`missing action name`这个错误，而发送空消息的行为主要是`setimmediate`这个库的内部行为，所以我们本地实现了`setimmediate`,屏蔽了发送空消息的行为，当然，这只是个临时的解决方案。

在整体开发过程中遇到的问题还有很多，比如`debug`的问题，但是由于项目开发人员不足，基本上如果有临时的解决方案就直接过去了，没有更多的记录。

## 更多

本文简单记录sketch插件方案的整体设计以及主要核心功能的开发流程，转换成代码可以说是也不算是复杂，所以不贴具体代码了。上面插件开发的一些核心功能在具体的工具下面都有具体的代码样式。现在主流的插件基本上都是用Framework提供能力，这一点也将是我司插件下一个版本的主要开发方向。Framework因为暂时还没有还没有彻底完成开发，我就不多说了，下面会贴一些sketch开发借鉴用的文档资料，有兴趣开发sketch插件开发的同学可以一起交流。



## 参考文献

-   [sketch插件开发总结](https://www.yuque.com/design-engineering/sketch-dev/9a1ac445-5fa2-41ff-8cf5-5d0c27658d88)
-   [积木Sketch插件进阶开发指南](https://mp.weixin.qq.com/s/DeRn5lqnATVQk5QH3JK4aA)
-   [html2sketch](https://github.com/ant-design/html2sketch)
-   [how we run NPM package in the browser](https://scrimba.com/scrim/c6azJtG)
-   [sketchplugins](https://sketchplugins.com/)
-   [Sketch Plugin Xcode Template](https://blog.magicsketch.io/sketch-plugin-xcode-template-c8236a6f7fff)