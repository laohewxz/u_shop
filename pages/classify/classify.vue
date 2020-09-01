<template>
	<view class="container">
		<!-- left左侧列表 -->
		<view class="left">
			<!-- 循环遍历的 -->
			<view :class="[index==id?'left_list activeList':'left_list ']" v-for="(item,index) in list" :key="item.id" @click="click(index)">
				<label for="">{{item.catename}}</label>
			</view>
		</view>
		<!-- right右侧详细分类商品 -->
		<view class="right">
			<!-- 每一个小类 -->
			<view class="right_list">
				<!-- 商品 -->
				<view class="bottom" >
					<view class="bottom_list" v-for="(item,index) in children" :key="item.id" @click="toDetail(item.id)">
							<!-- 每个最多显示6个 -->
							<view >
								<image :src="baseUrl+item.img" alt="">
							</view>
							<view class="title">
								<span>{{item.catename}}</span>
							</view>
					</view>
				</view>
				<view v-if="children.length == 0" class="product">暂时没有商品喔~</view>
				
			</view>
		</view>
		
		
		
	</view>
</template>

<script>
	import {myRequest,baseUrl} from "../../utils/db.js"
		export default{
			data(){
				return{
					baseUrl:'',
					list:[],//分类全部数据
					children:[],//分类的子类
					id:0,//侧边栏的初始样式
				}
			},
			onLoad(){
				this.baseUrl = baseUrl
				myRequest("/api/getcates").then(res=>{
					this.list = res[1].data.list
					this.children = this.list[this.id].children
					console.log(this.list)
				})
			},
			methods:{
				click(index){
				    this.id= index
					this.children = this.list[this.id].children?this.list[this.id].children:[]
					console.log(this.children)
				},
				toDetail(id){
					console.log(id)
				    uni.navigateTo({
				    	// url:"../details/details?id="+id
				    })
				}
			}
		}
</script>

<style>
	/* 导入外部的样式文件 */
	@import url("../../common/css/classify.css");
	
	/* 点击左侧导航，显示动态样式 */
	.activeList{
		border-left: 6rpx solid #f26b11;
		color: #f26b11;
	}
	.product{
		margin-top: 50rpx;
		text-align: center;
	}
</style>
