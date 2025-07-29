# `Vite`和`Webpack`的区别？

`webpack`会先打包，然后启动开发服务器，请求服务器时直接给予打包结果
而`vite`是直接启动开发服务器，请求哪个模块再对该模块进行实时编译

由于现代浏览器本身就支持`ES Module`，会自动向依赖的模块发出请求
`vite`充分利用这一点，将开发环境下的模块文件，就作为浏览器要执行的文件，而不是像`webpack`那样进行打包合并
由于`vite`在启动的时候不需要打包，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快
当浏览器请求某个模块时，再根据需要对模块内容进行动态编译
这种按需动态编译的方式，极大的缩减了编译时间，项目越复杂、模块越多，`vite`的优势越明显

在`HMR`热替换方面，当改动了一个模块后，仅需让浏览器重新请求该模块即可
不像`webpack`那样需要把该模块的相关依赖模块全部编译一次，效率更高

当需要打包到生产环境时，`vite`使用传统的`rollup`进行打包，因此，`vite`的主要优势在开发阶段

另外，由于`vite`利用的是`ES Module`，因此在代码中不可以使用`CommonJS`规范的代码



# 第三方库



## `vite-plugin-inspect`

用于调试检查Vite插件的中间状态

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import Inspect from 'vite-plugin-inspect'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), Inspect()],
})

```

