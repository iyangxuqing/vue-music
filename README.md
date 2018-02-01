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
