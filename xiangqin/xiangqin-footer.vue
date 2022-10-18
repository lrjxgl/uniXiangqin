<template>
	<view>
		<view class="footer-row"></view>
		 
		<div class="footer">
			<div class="footer-item icon-home" :class="{'footer-active':tab=='home'}" @click="goR('/pages/xiangqin')">首页</div>
			<div class="footer-item icon-activity" :class="{'footer-active':tab=='party'}" @click="goR('/xiangqin/xiangqin_party/index')">活动</div>
			<div class="footer-item icon-message_light"  :class="{'footer-active':tab=='xiangqin_notice'}" @click="goR('/xiangqin/xiangqin_notice/index')">消息</div>
			<div class="footer-item icon-my_light"  :class="{'footer-active':tab=='xiangqin_user'}" @click="goR('/xiangqin/xiangqin_user/index')">我的</div>
		</div>
	</view>
</template>

<script>
	export default{
		props:{
			tab:""
		},
		data:function(){
			return {
				
			}
		},
		methods:{
			goR:function(url){
				uni.reLaunch({
					url:url
				})
			},
			goHome:function(){
				uni.reLaunch({
					url:"../../pages/index/index"
				})
			},
			goPeople:function(){
				uni.reLaunch({
					url:"../../pagesblog/sblog_people/index"
				})
			},
			goChat:function(){
				uni.navigateTo({
					url:"../../pagesblog/sblog_chat/index"
				})
			},
			goUser:function(){
				uni.reLaunch({
					url:"../../pages/user/index"
				})
			} 
		}
	}
</script>

<style>
</style>
