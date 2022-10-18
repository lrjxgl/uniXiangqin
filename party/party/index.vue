<template>
	<view>
		<div class="tabs-border">
			<div @click="setType('doing')" :class="type=='doing'?'tabs-border-active':''" class="tabs-border-item">进行中</div>
			<div @click="setType('unbegin')"  :class="type=='unbegin'?'tabs-border-active':''"  class="tabs-border-item">即将开始</div>
			<div @click="setType('finish')"  :class="type=='finish'?'tabs-border-active':''"  class="tabs-border-item">已结束</div>
		</div>
		 
		<div v-if="!list || list.length==0" class="emptyData">暂无活动</div>
		 
		<div v-else class="sglist">
		 
			<div v-for="(item,index) in list" :key="index" class="sglist-item">
				<img @click="gourl('/party/party/show?id='+item.id)" v-if="item.imgurl!=''" :src="item.imgurl+'.small.jpg'" class="sglist-img" />
				<div class="flex flex-ai-center bd-mp-10">
					<img class="wh-40 bd-radius-50 mgr-5" :src="item.user_head+'.100x100.jpg'">
					<div class="cl2">{{item.nickname}}</div>
					<div class="flex-1"></div>
					<div class="cl3 f12">{{item.createtime}}</div>
				</div>
				<div @click="gourl('/party/party/show?id='+item.id)" class="sglist-title">{{item.title}}</div>
				
				
				<div class="flex mgb-10">
					<div class="cl-money  mgr-20">￥{{item.money}}</div>
					 
					<div class="cl2 mgr-5">限制人数</div>
					<div class="cl-num">{{item.max_num}}</div>
					 
					<div class="cl2 mgl-20  mgr-5">参与人数</div>
					<div class="cl-num">{{item.join_num}}</div>
					
				</div>
				<div class="flex mgb-10">
					<div class="cl2 f12 mgr-5">活动时间</div>
					<div class="cl2 f12 ">{{item.stime}}</div>
				</div>
				
				 
			</div>	
			 
		</div>
		<div class="loadMore" v-if="per_page>0" @click="getList()">点击加载更多</div>
		<party-footer tab="party"></party-footer>
	</view>
</template>

<script>
	import partyFooter from "@/party/party-footer.vue"
	export default{
		components:{
			partyFooter
		},
		data:function(){
			return {
				pageLoad:false,
				list:[],
				per_page:0,
				isFirst:true,
				type:"doing"
			}
		},
		onLoad:function(){
			 
			this.getPage();
		},
		onReachBottom:function(){
			this.getList();
		},
		methods:{
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			setType:function(type){
				this.type=type;
				this.per_page=0;
				this.isFirst=true;
				this.getList();
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=party&ajax=1",
					data:{
						type:this.type
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							skyToast(res.message);
							return false;
						}
						that.list=res.data.list;
						that.isFirst=false;
						that.per_page=res.data.per_page;
						that.pageLoad=true;
					}
				})
			},
			getList:function(){
				var that=this;
				if(that.per_page==0 && !that.isFirst){
					return false;
				}
				that.app.get({
					url:this.app.apiHost+"/module.php?m=party&ajax=1",
					data:{
						type:this.type,
						per_page:that.per_page
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							skyToast(res.message);
							return false;
						}
						if(that.isFirst){
							that.list=res.data.list;
							that.isFirst=false;
						}else{
							for(var i in res.data.list){
								that.list.push(res.data.list[i]);
							}
						}
						
						
						that.per_page=res.data.per_page;
						that.pageLoad=true;
					}
				})
			}
		}
	}
	
</script>

<style>
</style>