# v1.0.1 2018-02-01

1、完善工程目录结构，并引入相关字体、图片、基础样式文件
	src/api
	src/base
	src/common
	src/common/fonts
	src/common/image
	src/common/js
	src/common/stylus
	src/router
	src/store

2、添加依赖
	"dependencies": {
		"babel-runtime": "^6.0.0",
		"fastclick": "^1.0.6"
	},
	"devDependencies": {
		...
		"babel-polyfill": "^6.2.0",
		...
		"stylus": "^0.54.5",
		"stylus-loader": "^2.1.1"
	},
	然后运行 npm install

3、修改eslint rules
	'rules': {
		'eol-last': 0,
		'space-before-function-paren': 0
	}

4、编辑入口文件main.js
	4.1 在最开头引入'babel-polyfill'
	import 'babel-polyfill'

	4.2 引入fastclick并挂接到document.body
	import fastclick from 'fastclick'
	...
	fastclick.attach(document.body)

	4.3 引入stylus文件
	import 'common/stylus/index.styl'

5、在build/webpack.base.conf.js中配置路径别名
	resolve: {
		...
		alias: {
			common: resolve('src/common')
		}
	}

6、在index.html中完善meta
	<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no">

# ##############################################################################################################################

# vue-music

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
