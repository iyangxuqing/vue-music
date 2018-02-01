# v1.0.5 组件的后端数据

1、后端数据的存取模块一般放在src/api文件夹中，一个相对独立的组件对应一个js文件。如recommend.vue组件，对应的是src/api/recommend.js模块。

2、一些与后端数据相关的配置，则可以统一写在src/api/config.js文件中。

3、在src/api/config.js中写入向后端读取数据时的一些公共配置，以及一些常数如ERR_OK等

	export const commonParams = {
		g_tk: 5381,
		inCharset: 'utf-8',
		outCharset: 'utf-8',
		notice: 0,
		format: 'jsonp'
	}

	export const options = {
		param: 'jsonpCallback'
	}

	export const ERR_OK = 0

4、为recommend.vue组件编写api模块 - src/api/recommend.js，读取后端recommend数据

	import jsonp from 'common/js/jsonp'
	import {commonParams, options} from './config'

	export function getRecommend() {
		const url = 'https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg'

		const data = Object.assign({}, commonParams, {
			platform: 'h5',
			uin: 0,
			needNewCode: 1
		})

		return jsonp(url, data, options)
	}

5、在组件的created钩子中执行api，读取数据

	<script type="text/ecmascript-6">
		import {getRecommend} from 'api/recommend'
		import {ERR_OK} from 'api/config'

		export default {
			created() {
				this._getRecommend()
			},
			methods: {
				_getRecommend() {
					getRecommend().then((res) => {
						if (res.code === ERR_OK) {
							console.log(res.data.slider)
						}
					})
				}
			}
		}
	</script>
	
# ########################################################################################################

# v1.0.4 引入jsonp包，并封装成Promise

1、jsonp不是ajax，它是通过在html中写入script标签，以代码的方式获取后端的数据，并在执行代码时还原原先的数据，来避开跨域问题的解决方案。

2、可以引入现成的jsonp包，在"dependencies"中增加"jsonp": "^0.2.1"，并执行npm install，引入jsonp包。

3、通过jsonp包的文档，可以得知其主要用法是 jsonp(url, options, fn(err, data){ ... })。

4、在src/common文件夹中添加jsonp.js，用Promise方法将其包装
		
		import originJSONP from 'jsonp'

		return new Promise((resolve, reject) => {
			originJSONP(url, options, (err, data) => {
				if (!err) {
					resolve(data)
				} else {
					reject(err)
				}
			})
		})

# ############################################################################################################

# v1.0.3 路由和tab组件 2018-02-01

1、编写路由导航组件tab
	关键在于router-link标签和它的to属性
	<div class="tab">
    <router-link tag="div" class="tab-item" to="/recommend">
      <span class="tab-link">推荐</span>
    </router-link>
    <router-link tag="div" class="tab-item" to="/singer">
      <span class="tab-link">歌手</span>
    </router-link>
    <router-link tag="div" class="tab-item" to="/rank">
      <span class="tab-link">排行
      </span>
    </router-link>
    <router-link tag="div" class="tab-item" to="/search">
      <span class="tab-link">搜索</span>
    </router-link>
  </div>

 2、路由配置 - /src/router/index.js
	 export default new Router({
	  routes: [
	    {
				path: '/',
				redirect: '/recommend'
	    },
	    {
				path: '/recommend',
				component: Recommend
	    },
	    {
				path: '/singer',
				component: Singer
	    },
	    {
				path: '/rank',
				component: Rank
	    },
	    {
				path: '/search',
				component: Search
	    }
	  ]
	})

3、创建路由导向组件 recommend、singer、rank、search
	components/recommend/recommend.vue
	components/singer/singer.vue
	components/rank/rank.vue
	components/search/search.vue

4、在src/router/index.js中引入这些导向组件，以满足路由配置使用
	import Recommend from 'components/recommend/recommend'
	import Singer from 'components/singer/singer'
	import Rank from 'components/rank/rank'
	import Search from 'components/search/search'

# #############################################################################################################

# v1.0.2 添加m-header组件 2018-02-01

1、编写m-header组件
	建立src/components/m-header文件夹
	编辑src/components/m-header/m-header.vue文件
		<template>
  		<div class="m-header">
		  </div>
		</template>

		<script type="text/ecmascript-6">
  		export default {}
		</script>

		<style scoped lang="stylus" rel="stylesheet/stylus">
		</style>
	将该组件特有的图片文件logo@2x.png、logo@3x.png拷贝到该组件文件夹中

2、在App.vue中引用m-header组件
	import MHeader from 'components/m-header/m-header'

	components: {
		Mheader
	}

	<m-header></m-header>

3、在build/webpack.base.conf.js中配置路径别名
	resolve: {
		...
		alias: {
			components: resolve('src/components')
		}
	}	

# ########################################################################################################################################

# v1.0.1 vue程序基础架构 2018-02-01

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
