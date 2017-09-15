---
title: vue使用笔记-vue-cli项目目录及配置文件浅析
date: 2017-08-25 11:53:38
tags:
 - vue
 - webpack
---

之前写了一篇笔记，只是把用vue-cli搭建了一个移动端的项目。但本人对vue-cli的项目目录及webpack配置不了解，在此学习并记录下。

#### 创建项目
```js
vue init webpack <projectName>
```
#### 项目目录
```avrasm
|-- build/		# webpack配置文件
|     |-- dev.server.js	# 运行项目时的入口文件(npm run dev)
|     | ···
|-- config/		# 项目配置文件
|     |-- dev.env.js
|     |-- index.js 	# 项目运行和打包对应的配置文件
|     |-- prod.env.js
|     |-- test.env.js
|-- src/		# 开发源文件
|     |-- assets/ 	# 模块资源（会被webpack处理）
|           | ···
|     |-- commponents/ 	# 模块
|           | ···
|     |-- router/ 	# 路由
|           | ···
|     |-- App.vue 	# 项目开始时的入口vue组件
|     |-- main.js 	# 程序入口文件
|-- static/		# 静态资源文件(直接拷贝到dist/static/里面)
|     | ···
|-- test/		# 测试
|     | ···
|-- .babelrc		# babel配置文件
|-- .editorconfig	# 编辑配置文件
|-- .eslintignore	# eslint忽略配置文件
|-- .eslintrc.js 	# eslint配置文件
|-- .gitignore 		# 项目提交忽略文件
|-- .postcssrc.js	# postcss配置文件[为css提供一些额外的功能，处理css]
|-- index.html 		# 入口模板文件
|-- package.json 	# 开发项目所需的包管理配置文件（运行的脚本与相关依赖）
|-- README.md 		# 重要内容介绍
```

##### build/
此目录为开发环境和生产环境的webpack配置文件，我们可以修改此目录下的文件，来添加或修改插件配置、调整路径等

##### config/
此目录最为主要的是index.js文件，在index.js文件中配置了生产环境和开发环境的入口文件、相关目录和开发环境下的端口、是否自动打开浏览器以及生产环境下是否压缩、是否生成map文件等

##### src/
此目录为开发环境下的源代码。
```avrasm
src/assets/ 		# 为资源目录
src/commonpents/ 	# 为模块目录
src/router/index.js 	# 为路由文件
src/App.vue 		# 为开发环境下的入口模板文件
src/main.js 		# 为开发环境下的入口文件
```

##### static/
此目录为静态资源目录，此目录下的文件不会被webpack处理。在webpack打包后，此目录下的文件会被直接复制到dist/static/中

##### test/
```avrasm
test/unit/ 	# 该目录包含了单元测试相关的文件
test/e2e/ 	# 该目录包含了端到端测试相关的文件
```

##### index.html
这个是单界面应用中的模板index.html。在开发环境中，webpack会生成相关资源，这些资源的url会自动插入到模板来渲染最终的HTML。

#### 主要的配置文件

##### dev-server.js
开发环境下的入口文件，在此文件中引入了其他的配置文件和相关插件。
引入的配置文件：
```avrasm
build/check-versions.js		# 检查node/npm的版本
config/index.js 		# 主要的配置文件
build/webpack.prod.conf 	# 打包的webpack配置文件
build/webpack.dev.conf 		# 开发的webpack配置文件
```

引入的相关插件：
```avrasm
path 				# ...
webapck 			# ...
express 			# ...
opn 				# 在浏览器中打开需要打开的链接uri - opn(uri)
http-proxy-middleware 		# 代理中间件，方便连接，快速，浏览器同步等
connect-history-api-fallback 	# 通过指定的索引页面代理请求，对于单页面应用程序特别有用
webpack-dev-middleware 		# 这个插件用于webpack打包的，详细用法参见webpack-dev-server
webpack-hot-middleware 		# 这个插件用于热刷新的，详细用法参见webpack-dev-server
```

##### build.js
打包时运行的入口文件

引入的配置文件：
```avrasm
build/check-versions.js		# 检查node/npm的版本
config/index.js 		# 主要的配置文件
build/webpack.prod.conf 	# 打包的webpack配置文件
```

引入的相关插件：
```avrasm
...
ora 	# 打包过程中优雅的图标提示
rimraf 	#  相当于Unix命令：rm -rf , 用来删除指定的目录或文件
```

##### webpack.dev.conf.js
开发环境的部分webpack配置信息

引入的配置文件：
```avrasm
build/utils.js			# 样式loader的配置
config/index.js 		# 主要的配置文件
build/webpack.base.conf.js 	# 主要的webpack配置信息
```

引入的相关插件：
```avrasm
webpack-merge 			# 用来合并loader
html-webpack-plugin 		# 为html文件引入带有hash值的外部资源，生成html入口文件
friendly-errors-webpack-plugin 	# 友好的错误提示(识别某些类别的webpack错误并处理、合并、优先排序，以提供更好的开发者体验)
```

##### webpack.prop.conf.js
打包时的部分webpack配置信息

引入的配置文件：
```
同上
```

引入的相关插件：
```avrasm
webpack-merge 				# 用来合并loader
html-webpack-plugin 			# 为html文件引入带有hash值的外部资源，生成html入口文件
copy-webpack-plugin 			# copy文件到某一目录(此插件用来把static/下的静态资源直接复制到dist/static/目录下)
extract-text-webpack-plugin 		# 将不同文件的内容打包到一个文件中(抽离出css样式，并打包到一个css文件中)
optimize-css-assets-webpack-plugin 	# 优化css资源(默认使用cssnano处理器,cssnano是在postcss基础之上构建的)
compression-webpack-plugin 		# 压缩文件
webpack-bundle-analyzer 		# 资源分析器(获取资源压缩前后的实际大小等，此项目中config/index.js文件下找到
					- bundleAnalyzerReport将其值改为true，打包时即可在8888端口看到可视化的数据信息
					- 或者是给process.env.npm_config_report赋值为true)
```

##### webpack.base.conf.js
开发环境及生产环境公用的部分webpack配置信息

引入的配置文件：
```avrasm
build/utils.js			# 样式loader的配置
config/index.js 		# 主要的配置文件
build/vue-loader.conf.js 	# 该配置文件只对css的map以及模板中相对链接是否转换成require方式做了配置

```

引入的相关插件：
```
...
```
**此文件配置了项目的入口文件、输入、解析及不同类型的模块处理**


以上内容由错误之处，还望指正。


** 关键词：**  [postcss](http://www.jianshu.com/p/7f8a5d83f4b7) , [babel](http://babeljs.cn/) , [http-proxy-middleware](https://www.npmjs.com/package/http-proxy-middleware) , [webpack-dev-server](http://webpack.github.io/docs/webpack-dev-server.html) , [webpack-merge](https://www.npmjs.com/package/webpack-merge) , [html-webpack-plugin](https://www.npmjs.com/package/html-webpack-plugin) , [compression-webpack-plugin](https://github.com/webpack-contrib/compression-webpack-plugin)