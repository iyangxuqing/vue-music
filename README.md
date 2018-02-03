# v1.0.9 使用第三方组件 vue-lazyloader

1、对于图片的懒加载，可以使用现成的第三方vue组件：vue-lazyloader。

2、在dependencies中添加依赖：
		
		"vue-lazyload": "^1.0.3"

		然后安装它： npm install。

3、在main.js模块中注册第三方组件

		import VueLazyLoad from 'vue-lazyload'

		Vue.use(VueLazyLoad, {
			loading: require('common/image/default.png')
		})

4、在template中使用v-lazy指令

		<img v-lazy="url">

# ####################################################################################################

# v1.0.8 封装scroll组件

1、区块内容的滚动可以使用better-scroll包，better-scroll很好用，体验效果跟原生滚动差不多了。

2、但better-scroll不是特意为vue封装的，它的使用更多的是命令式编程，与vue提倡的数据驱动不相符。

3、程序中往往需要在多处使用内容滚动效果，better-scroll的配置和初始化及refresh的代码也很繁多。

4、将better-scroll进行再次封装成scroll组件，利用vue的<slot></slot>指令，可以很方便的将需要滚动的内容嵌套到scroll组件中。

    <scroll class="recommend-content" ref="scroll" :data="discList">
      <div>
      	<div class="slider-wrapper" v-if="recommends.length">
      		<slider>
      			<div v-for="item in recommends" :key="item.id">
      				<a :href="item.linkUrl">
      					<img @load="loadImage" :src="item.picUrl"/>
      				</a>
      			</div>
      		</slider>
      	</div>
        <div class="recommend-list">
          <h1 class="list-title">热门歌单推荐</h1>
          <ul>
            <li class="item" v-for="item in discList" :key="item.id">
              <div class="icon">
                <img width="60" height="60" :src="item.imgurl">
              </div>
              <div class="text">
                <h2 class="name" v-html="item.creator.name"></h2>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </scroll>

5、在使用中，scroll需要明确知道元素的高度（或宽度），在内容区域有图片加载时，需要监听image.onLoad事件，以确保图片加载后及时更新scroll组件。
		
		<img @load="loadImage" :src="item.picUrl"/>

		...

		loadImage() {
      if (!this.checkLoaded) {
        this.$refs.scroll.refresh()
        this.checkLoaded = true
      }
    }

# ############################################################################################

# v1.0.7 使用axios获取数据

1、axios可以在nodejs端或浏览器端向服务器请求数据。axios返回Promise对象。

	  return axios.get(url, {
	    params: data
	  }).then((res) => {
	    return Promise.resolve(res.data)
	  })

2、QQ音乐的歌单列表数据，服务器端对referer进行了校验，因此不能通过jsonp或ajax方式直接通过浏览器去获取，需要通过服务端代理获取。

3、在webpack.dev.conf.js中建立向QQ音乐读取歌单列表数据的api:

	  devServer: {
	    before(app) {
	      app.get('/api/getDiscList', function (req, res) {
	        const url = 'https://c.y.qq.com/splcloud/fcgi-bin/fcg_get_diss_by_tag.fcg'
	        axios.get(url, {
	          headers: {
	            referer: 'https://c.y.qq.com/',
	            host: 'c.y.qq.com'
	          },
	          params: req.query
	        }).then((response) => {
	          res.json(response.data)
	        }).catch((e) => {
	          console.log(e)
	        })
	      })
	    },
	    ...
	  }

4、在recommend.vue的前端api模块src/common/recommend.js中编写向devServer读取歌单的api:

		export function getDiscList() {
		  const url = '/api/getDiscList'

		  const data = Object.assign({}, commonParams, {
		    platform: 'yqq',
		    hostUin: 0,
		    sin: 0,
		    ein: 29,
		    sortId: 5,
		    needNewCode: 0,
		    categoryId: 10000000,
		    rnd: Math.random(),
		    format: 'json'
		  })

		  return axios.get(url, {
		    params: data
		  }).then((res) => {
		    return Promise.resolve(res.data)
		  })
		}

5、在recommend.vue组件的created钩子中，引用上述的api，即可读取歌单列表数据。

  import {getRecommend, getDiscList} from 'api/recommend'
  ...

  export default {
    created() {
    	...
      this._getDiscList()
    },
    methods: {
    	...
      _getDiscList() {
        getDiscList().then((res) => {
          if (res.code === ERR_OK) {
            console.log(res.data.list)
          }
        })
      }
    },
    ...
  }

# #####################################################################################################################################

# v1.0.6 slider组件

1、slider组件使用better-scroll包来编写，使用了vue的<slot></slot>指令来插入特定内容。

2、对于在<slot></slot>中插入的内容，slider组件通过js以addClass和$dom.style这两种方式给外部插入的内容添加样式。

3、在src/common/js文件夹下创建了dom.js模板，提供了addClass、hasClass方法。

4、本组件全面讲解了better-scroll的snap配置和 scroll.on('scrollEnd', () => {})事件监听。

5、本组件编写中使用了window resize事件的监听 window.addEventListener('resize', () => { })，这些代码的使用值得在许多地方借鉴。

# #####################################################################################################################################

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
