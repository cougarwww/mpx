# 基于微信的模板转换规则

> 每个文件是一个微信组件的规则。需要为mpx跨的所有平台编写对应的转换规则。

## web

web的额外逻辑，因为小程序组件和web存在差异，比如事件相关的东西，因此可能无法直接单纯地转换，因此会做额外判断决定是否注入一个内建组件来polyfill，这些内建组件在 `/lib/runtime/components/web` 。

## 规则结构

每个文件对应一个组件，里面要覆盖到Mpx支持的所有平台。

每个平台不支持的组件写在 unsupported.js 中。

Mpx的转换一个重要原则是转不了的东西通过控制台打印提示用户，要求用户提供一份符合对应平台的代码通过条件编译支持。因此错误输出格式保持一致是有必要的。

在 index.js 中，会汇总每个组件的转换规则函数，为了使错误信息标准化，一致化，错误打印函数的生成函数实现在index.js里。

每个组件文件是一个方法，接受错误打印生成方法，根据组件名生成对应的错误打印方法。

在转换规则对应的平台处理方法里决定是否要进行错误打印。

## 转换规则编写指南

每一条规则其实是一个对象。主要的属性有：

test: 用于匹配标签名，可以是字符串或正则

\[平台(如wx/qq/swan/ali/tt/web)]: 一个方法，用于处理这个平台下的标签转换，如view在web下需要判断是否有事件决定转成普通的div还是内建组件mpx-view，参数待完善

props和event: 都是数组，每一项都如大规则一样包含test用于命中要转换的项，然后是平台方法，用于处理转换。

## 单元测试

鉴于转换规则可能在不同时间被多人多次编辑，且规则繁杂，为了避免框架底层改动/他人规则改动导致之前编写的规则失败，建议为转换编写对应的单元测试case。

同时，单元测试也可以帮助你更好更快编写转换规则，而无需找个真实的项目尝试。

转换规则的单元测试放置在 webpack-plugin/test/platform/wx 下，util中提供了一个转换方法，可参考之前的示例进行。
