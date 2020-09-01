<template>
	<view class="">
		<!-- 顶部信息 -->
		<view class="HomeTop">
		  <view class="logoView">
			<!-- logo -->
			<img class="logo" src="/static/index/logo.jpg" alt="" />
		  </view>
		  <view class="inputView">
			<input class="textinput" style="center" type="text"   placeholder="寻找商品"/>
		  </view>
		</view>
		
		<!-- 顶部导航  scroll-view -->
		<view class="tabs">
		    <scroll-view id="tab-bar" class="scroll-h" :scroll-x="true" :show-scrollbar="false">
		        <view class="uni-tab-item"  data-current="0" v-for="(item,index) in navList" :key="item.id" @click="click" :id="index">
		            <text :class="[index==id?'uni-tab-item-title uni-tab-item-title-active':'']">
						{{item.catename}}
					</text>
		        </view>
		    </scroll-view> 
		</view>
		<!-- 推荐轮播图 -->
		<view class="">
			<swiper :indicator-dots="true" :autoplay="true" :interval="2000" :duration="1000">
				<swiper-item v-for="(item,index) in bannerList" :key="item.id">
					<view class="swiper-item">
						<image :src="item.img" mode=""></image>
					</view>
				</swiper-item>
				
				
			</swiper>
		</view>
		
		<!-- 功能导航开始 -->
		<!-- 功能导航 -->
		<view class="FunctNavCon">
		  <view class="FunctNavLi">
			<image src="../../static/index/xiaohuoban.png" alt class="FunImg" />
			<p>限时抢购</p>
		  </view>
		  <view class="FunctNavLi">
		  			<image src="../../static/index/jifentixicopy.png" alt class="FunImg" />
		  			<p>积分商城</p>
		  </view>
		  <view class="FunctNavLi">
		  			<image src="../../static/index/lianxiwomen.png" alt class="FunImg" />
		  			<p>联系我们</p>
		  </view>
		  <view class="FunctNavLi" @click="toDetail">
		  			<image src="../../static/index/-shangpinfenlei-gai.png" alt class="FunImg" />
		  			<p>限时抢购</p>
		  </view>
		</view>
		
		<!-- 推荐部分 未写 -->
		<view class="HotGoods">
			<!-- 热推左侧 -->
			<view class="HotLeft">
				<view class="LimitedContent">
				  <image class="LimitedImg" src="../../static/index/xianshi.jpg" alt />
				  <label class="Limited">限时秒杀</label>
				</view>
				<p class="LimitedTitle">每天零点场，好货秒不停</p>
				<!-- 倒计时 -->
				<view class="LimitTimeAll">
				  <label class="LimitTime">{{hour}}</label>
				  <view class="maohao">:</view>
				  <label class="LimitTime">{{minute}}</label>
				  <view class="maohao">:</view>
				  <label class="LimitTime">{{second}}</label>
				  <label class="Seckill">秒杀</label>
				</view>
				<image src="../../static/index/goods2.jpg" class="LimitedgoodsImg" alt="">
			</view>
			
			<!-- 热推右侧 -->
			<view class="HotRight">
				<!-- 右侧顶部部分 -->
				<view class="HotTop">
					<view class="HomeTopInfo">
						<view class="HomeTopInfoOne">
							<label class="BrandNew">品牌上新</label>
							<label class="Discount">折</label>
						</view>
						<view class="HomeTopRobbig">
							<label>每日九点，抢大牌</label>
						</view>
					</view>		
					<!-- <div> -->
					<view class="Cloth">
						<image  src="../../static/index/goods1.jpg" alt="">
					</view>
					
					<!-- </div> -->				
				</view>
				<!-- 右侧底部 -->
				<view class="HotBottom">
					<view class="HotBottomLeft">
						<view class="HotBottomLeftInfo">
							<label class="HotBottomLeftInfoTitle">每日十件</label>
							<label class="HotBottomLeftInfoDetail">只为你选好货</label>
						</view>
						<view class="HotBottomLeftImage">
							<image src="../../static/index/goods1.jpg" mode=""></image>
						</view>
					</view>
					<view class="HotBottomRight">
						<view class="HotBottomRightInfo">
							<label class="HotBottomRightInfoTitle">每日十件</label>
							<label class="HotBottomRightInfoDetail">只为你选好货</label>
						</view>
						<view class="HotBottomRightImage">
							<image src="../../static/index/goods1.jpg" mode=""></image>
						</view>
					</view>
				</view>
			</view>
		</view>
		  <!-- 轮播图2 -->
		<div class="Swiper2">
		  <image class="Swiper2Img" src="../../static/index/swiper1.jpg" alt />
		</div>
		
		<!-- 底部商品列表 -->
		<view class="products">
			<!-- 商品标签 -->
			<view class="tags">
				<view :class="[index==hotid?'active_tag_list tag_list':'tag_list']" v-for="(item,index) in hotNav" :key="index" @click="clickHot(index)">
					<label for="">{{item}}</label>
				</view>
			</view>
			<!-- 标签对应的商品 -->
			<view class="tags_product" >
				<view class="product" v-for="(item,index) in hot[hotid].content" :key="item.id" @click="proDetail(item.id)">
					<view class="GoodsLeft">
						<image class="GoodsImg" :src="baseUrl+item.img" alt />
					</view>
					<view class="GoodsCont">
						<view class="GoodConTit">{{item.goodsname}}</view>
						<view class="GoodsPrice">￥{{item.price}}</view>
						<view class="yimai">已售{{item.market_price}}件</view>
						<view class="Immed">立即抢购</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import {myRequest,baseUrl} from "../../utils/db.js"
	export default{
		data(){
			return{
				navList:[],//导航栏数据
				id:0,//导航栏样式初始值
				bannerList:[],//轮播图
				hour:'00',
				minute:'00',
				second:'00',
				hot:[],//热门推荐，发现好货
				hotNav:['热门推荐','发现好货','只看商品'],
				hotid:0,
				baseUrl:""
			}
		},
		onLoad() {
			this.baseUrl = baseUrl
			//请求导航栏数据
			 myRequest("/api/getcate").then(res=>{
				this.navList = res[1].data.list
			})
			
			//请求轮播图数据
			 myRequest("/api/getbanner").then(res=>{
				 this.bannerList = res[1].data.list
				 this.bannerList.forEach(item=>{
				 	item.img=baseUrl+item.img
				 })
			 })
			
			//--------------------------秒杀---------------------------------
			myRequest("/api/getseckill").then(res=>{
				var info = res[1].data.list[0]
				// console.log(res[1].data,'ppp')
				var endtime = info.endtime //结束时间
				setInterval(()=>{
					var nowtime = new Date().getTime()//现在时间
					var distance = endtime - nowtime //时间差
					distance = Math.floor(distance/1000)
					// console.log(distance)
					var hour = Math.floor(distance/3600)//小时
					var minute = Math.floor(distance%3600/60)//分钟
					var second = distance%3600%60 //秒
					this.hour = hour<10?"0"+hour:hour;
					this.second = second<10?"0"+second:second;
					this.minute = minute<10?"0"+minute:minute;
				},1000)
			})
			
			
			//----------------------热门推荐，发现好货-----------------------
			myRequest("/api/getindexgoods").then(res=>{
				this.hot = res[1].data.list
			})
			
		},
		methods:{
			click(e){//点击导航栏
				this.id = e.currentTarget.id
				var _id = Number(this.id) +1
				uni.navigateTo({
					url:"../product/product?id="+_id
				})
			},
			toDetail(){//点击跳往详情页
				uni.navigateTo({
					url:"../classify/classify"
				}) 
			},
			clickHot(hotid){//点击热门推荐，发现好货
				this.hotid = hotid
			},
			proDetail(id){//点击具体的商品
				// console.log(id)
				uni.navigateTo({
					url:"../details/details?id="+id
				})
			}
		}
	}
</script>

<style>
	/* 导入外部的样式文件 */
	@import url("../../common/css/index.css");
</style>
