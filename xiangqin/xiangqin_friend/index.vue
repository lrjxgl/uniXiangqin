<template>
	<view>
		<div v-if="list.length==0" class="emptyData">暂无好友</div>
		<template v-else>
		<div v-for="(item,index) in list" :key="index" class="flexlist-item">
			<img  @click="gourl('../xiangqin_people/show?userid='+item.userid)" class="flexlist-img" :src="item.imgurl+'.small.jpg'" />
			<div class="flex-1">
				<div @click="gourl('../xiangqin_people/show?userid='+item.userid)"  class="flexlist-title">{{item.truename}}</div>
				<div class="flex mgb-5">
					<div class="cl3 f12 mgr-10">{{item.gender_title}}</div>
					<div class="cl3 f12">{{item.age}}岁</div>
					<div class="flex-1"></div>
					<div class="mgr-5 f12 cl3">{{item.zhiye}}</div>
					<div class="cl-primary f12">收入：{{item.income}}万</div>
				</div>
				
				
				<div class="flexlist-desc mgb-5">{{item.self_desc}}</div>
				<div class="flex">
					<div class="flex-1"></div>
					<div class="btn-mini btn-outline-primary mgr-5" @click="gourl('/pages/pm/detail?userid='+item.userid)" >聊聊</div>
					 
					<div class="btn-mini btn-outline-danger mgr-5" @click="delUser(item)">删除</div>
				</div>
			</div>
		</div>
		</template>
	</view>
</template>

<script>
	
	export default{
		data:function(){
			return {
				pageLoad:false,
				list:[],
				per_page:0,
				isFirst:true
			}
		},
		onLoad:function(){
			this.getPage();
		},
		onReachBottom:function(){
			this.getList();
		},
		onPullDownRefresh:function(){
			this.getPage();
			uni.stopPullDownRefresh();
		},
		onShareAppMessage:function(){
			
		},
		onShareTimeline:function(){
			
		},
		methods: {
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_friend",
					success:function(res){
						that.pageLoad=true;
						that.list=res.data.list;
						that.per_page=res.data.per_page;
					}
				})
			},
			getList:function() {
				var that=this;
				if(that.per_page==0 && !that.isFirst){
					return false;
				}
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_friend",
					data:{
						per_page:that.per_page
					},
					success:function(res){						 
						that.per_page=res.data.per_page;
						if(that.isFirst){
							that.list=res.data.list;
							that.isFirst=false;
						}else{
							for(var i in res.data.list){
								that.list.push(res.data.list[i]);
							}							
						}
						
					}
				})
			},
			delUser:function(item){
				var that=this;
				uni.showModal({
					content:"确认删除好友吗",
					success:function(e){
						if(e.confirm){
							
							that.app.get({
								url:that.app.apiHost+"/module.php?m=xiangqin_friend&a=delete",
								data:{
									touserid:item.touserid
								},
								success:function(res){
									var list=[];
									for(var i in that.list){
										if(that.list[i].touserid!=item.touserid){
											list.push(that.list[i])
										}
									}
									that.list=list;
								}
							})
						}
					}
				})
			}
		},
	}
</script>

<style>
</style>