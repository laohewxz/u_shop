<template>
	<view class="container">
		<!-- 详情图片 -->
		<view class="detailImage">
			<image :src="proInfo.img">
		</view>
		<!-- 详情基本信息 -->
		<view class="detailInfo">
			<view class="detailInfoName">
				<label>{{proInfo.goodsname}}</label>
			</view>
			<view class="detailInfoPrice">
				<label style="color: red;">￥ {{proInfo.price}}</label>
				<label style="font-size: 24rpx;color: #ccc;">(此价格不与套装优惠同时享受)</label>
			</view>			
		</view>
		
		<!-- 基本信息改变 -->
		<view class="changeInfo">
			<view class="youhui">
				<view>促销：<label class="manjian">满减</label><label class="dazhe">满2件9折；3件8折</label></view>
			</view>
			<!-- 数量加减 -->
			<view class="changeNum">
				<view class="num">购买数量</view>
				<view class="action">
				  <label class="jian" @click="less">-</label>
				  <label class="zhi">{{num}}</label>
				  <label  class="jia" @click="more">+</label>
				</view>
			</view>
			<!-- 商品属性 -->
			<view class="changeState">
				<view class="productStat">
					<label for="">商品属性</label>
				</view>
				<view class="productGuige">
					<label>{{proInfo.specsname}}</label>
					<view class="threed3i1" v-for="(item,index) in arr" :key="index">{{item}}</view>
				</view>
			</view>
		</view>
		<!-- 商品详情 -->
		 <view class="productDetail">
			<view class="productDetailTitle">商品详情</view>
			<!-- 需要处理图片自适应问题 -->
			<rich-text :nodes="proInfo.description"></rich-text>
			<!-- <image src="/static/detail/2.jpg" mode="aspectFill"/> -->
		</view>
		
		<view class="productEval">
			<view class="eval">商品评价</view>
			<view>
				<label class="evalUsername">小李白</label>
				<label class="evalUsertitle">效果很好，物流到位，下次继续来</label>					
				<view class="evalImage">
					<image src="../../static/detail/pingjia1.jpg"/>
					<image src="../../static/detail/pingjia2.jpg"/>
					<image src="../../static/detail/pingjia3.jpg"/>
						
				</view>
				<label class="evalTime">2020-01-13</label>
			</view>
		</view>
		<!-- 底部按钮 -->
		<view class="foot">
			<button class="footbtn1" @click="addcar">加入购物车</button>
			<button class="footbtn2"  @click="addcar">立即购买</button>
		</view>
	</view>
</template>
<script>
	import {myRequest,baseUrl} from "../../utils/db.js"
	export default{
		data(){
			return{
				baseUrl:'',
				proInfo:{},//获取到具体的商品信息
				arr:[],//颜色
				num:1,//商品数量
			}
		},
		async onLoad(e){//一进页面接受参数
		    this.baseUrl = baseUrl
			var res = await myRequest("/api/getgoodsinfo",{id:e.id})
			this.proInfo = res[1].data.list[0]//获取到具体的商品信息
			this.proInfo.img=baseUrl+this.proInfo.img
			//将颜色 字符串转化成数组
			var str = this.proInfo.specsattr
			if(str.indexOf(",")==-1){
				this.arr.push(str)
			}else{
				this.arr=str.split(",")
			}
			console.log(this.proInfo)
			
		},
		methods:{
			less(){//点击减少
			    this.num--
				if(this.num <= 1){
					this.num=1
					uni.showToast({
						icon:'none',
						title:"已经不能更少了！！"
					})
				}
				
			},
			more(){
				this.num++
			},
			addcar(){//加入购物车
			//判断是否登录--------------------------
		    var {uid,token} = uni.getStorageSync('list')
			if(!token){//没有token就跳转到登录页
				uni.showToast({
					icon:'none',
					title:"请登录",
					success:res=>{
						uni.navigateTo({
							url:"../send/send"
						})
					}
				})
				return
			}else{//有token判断token有没有过期
				myRequest("/api/checktoken",{},"GET",{authorization:token}).then(res=>{
					if(res[1].data.msg){
						uni.showToast({
							icon:'none',
							title:res[1].data.msg,
							success:res=>{
								uni.navigateTo({
									url:"../send/send"
								})
							}
						})
					}
				})
				
			}
			//-------------------------------------
			if(this.num <= 0){
				uni.showToast({
					icon:'none',
					title:"数量至少为一件！"
				})
				return
			}
				var user = uni.getStorageSync("list")
				var data = {
					uid :user.uid,
					goodsid:this.proInfo.id,
					num:this.num
				}
				console.log(data)
				myRequest("/api/cartadd",data,"GET",{authorization:user.token}).then(res=>{
					uni.showToast({
						icon:'none',
						title:res[1].data.msg
					})
				})
			}
		}
	}
</script>
<style>
	@import url("../../common/css/details.css");
	
</style>
	
