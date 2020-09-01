<template>
	<view>
		<uni-search-bar radius="100" bgColor="#ffff"></uni-search-bar>
		<view class="list">
			<view class="row" v-for="(item,index) in list" :key="item.id">
				<view class="products"  v-for="(i,index) in item.child" :key="index">
					<view class="imagebox">
						<image :src="baseUrl+i.img" mode="widthFix"></image>
						<text>{{i.goodsname}}</text>
					</view>
				</view>
				<view class="info">
					<text  space="nbsp">总计{{item.countmoney}}商品  </text>  
					<text>应付金额 ￥{{item.countnumber}}</text>
				</view>
			</view>
		</view>
		<view class="tishi">
			没有数据亲！
		</view>
	</view>
</template>

<script>
	import {myRequest,baseUrl} from "../../utils/db.js"
	import uniSearchBar from '@/components/uni-search-bar/uni-search-bar.vue'
	export default{
		   components: {uniSearchBar},
		   data(){
			   return{
				   list:[],
				   baseUrl:''
			   }
		   },
		   onLoad() {
			 this.baseUrl = baseUrl
			 var uid = uni.getStorageSync("list").uid
			 var token = uni.getStorageSync("list").token
		   	 myRequest("/api/orders",{uid:uid},"GET",{authorization:token}).then(res=>{
				 console.log(res[1].data.list)
				 this.list = res[1].data.list
			 })
		   	 	
		   }
		
	}
</script>
<style>

	.row{
		margin: 10rpx 20rpx;
		/* height: 240rpx; */
		border: 1rpx solid #AAAAAA;
		border-radius: 30rpx;
	}
	
	.imagebox{
		float: left;
		margin:10rpx;
	}
	.products{
		overflow: hidden;
	}
	image{
		width: 160rpx;
		margin-left: 10rpx;
	}
	.info{
		/* position: absolute;
		top: 180rpx;
		right: 50rpx; */
		display: flex;
		justify-content: space-between;
		font-size: 25rpx;
		/* width: 340rpx; */
		padding: 20rpx;
		text-align: right;
	}
	.info text:nth-of-type(2){
		color: red;
	}
	.info text:nth-of-type(3){
		font-size: 20rpx;
		color: #AAAAAA;
	}
	.tishi{
		line-height: 200rpx;
		text-align: center;
		font-size: 40rpx;
		color: #006699;
	}
</style>
