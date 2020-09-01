<template>
	<view class="box">
		<view class="row row01" >
			<view class="send">
				<text>手机号</text>
			</view>
			<input type="text" v-model="phone" maxlength="12" class="inp" />
			<!-- <button class="btn">获取验证码</button> -->
		</view>
		<view class="row">
			<view class="send">
				<text>验证码</text>
				<text @click="getcode">{{flag?time:'点击获取验证码'}}</text>
			</view>
			<input type="text" v-model="code"  placeholder="- - - -" maxlength="4" />
		</view>
		<view class="row">
			<text style="font-size: 23rpx;">收不到验证码？试试 <text style="color:#00BB00;font-size: 23rpx;"> 语音验证</text></text>
			<!--   -->
			<button type="primary"  style="width: 90%;border-radius: 80rpx;margin-top: 50rpx;" @click="login">登录</button>
		</view>
		<view class="row">
			<text style="color: #006699;text-align: center;" >通过微信授权登录</text>
		</view>
	</view>
</template>

<script>
	  import {myRequest,baseUrl} from "../../utils/db.js"
	  export default{
		  data(){
			  return{
				  phone:null,
				  code:null,
				  time:10,
				  flag:false,
				  timer:null
			  }
		  },
		  onLoad() {
		  	
		  },
		 methods:{
			 getcode(){//点击获取验证码
			     if(!(/^1[3456789]\d{9}$/.test(this.phone))){ 
			         uni.showToast({
			         	icon:'none',
			         	title:"手机号有误，请重新输入！"
			         })  
			         return 
			     } 
			 this.flag = true
			 console.log('88')
			  clearInterval(this.timer)
			    this.timer =  setInterval(()=>{
					 this.time --
					 if(this.time==0){
						 this.flag = false
						 this.time=10
						 clearInterval(this.timer)
					 }
				 },1000)
				 if(this.time==10){
					 myRequest('/api/sms',{phone:this.phone}).then(res=>{
					 	console.log(res[1].data.list.code)
					 	uni.setStorageSync('code',res[1].data.list.code)//将验证码存本地存储
					 })
				 }else{
					 uni.showToast({
					 	icon:'none',
					 	title:"请在手机上查看验证码"
					 })
				 }
				
				 
			 },
			 login(){//点击登录
			   var code = uni.getStorageSync("code")
			   if(code == this.code){//验证码正确
			        myRequest("/api/wxlogin",{phone:this.phone,cookie:code}).then(res=>{
						console.log(res[1].data.list)
						 uni.setStorageSync('list',res[1].data.list)
						 uni.switchTab({
						 	url:'../index/index'
						 })
					})
			   }else{
				  uni.showToast({
				  	icon:'none',
				  	title:"验证码错误！"
				  })
			 }
			 }
		 }
		  
	  }
</script>

<style>
	/* 导入外部的样式文件 */
	/* @import url("../../common/css/index.css"); */
	.box{
		position: relative;
	}

	.row{
		display: flex;
		flex-direction: column;
		justify-content: space-around;
		margin: 40rpx;
	}
	.send{
		display: flex;
		justify-content: space-between;
	}
	.send text:nth-of-type(2){
		color: #00BB00;
	}
	text{
		font-size: 27rpx;
		margin-bottom: 10rpx;
	}
	input{
		border-bottom: 1rpx solid gray;
		height: 65rpx;
	}
	/* .inp{
		width: 65%;
	}
	.btn{
		width: 200rpx;
		font-size: 14px;
		display: inline-block;
	}
	.row01{
		display: flex;
	} */
	
</style>
