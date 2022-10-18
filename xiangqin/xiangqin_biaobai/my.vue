<template>
	<view>
		<div>
			<div class="tabs-border">
				<div @click="setType('receive')" :class="type=='receive'?'tabs-border-active':''" class="tabs-border-item">我收到的</div>
				<div @click="setType('send')"  :class="type=='send'?'tabs-border-active':''"  class="tabs-border-item">我发出的</div>
			</div>
			<div v-if="list.length==0" class="emptyData">暂无招呼</div>
			<div v-else>
				<div class="flexlist-item" v-for="(item,index) in list" :key="index">
					<img @click="goPeople(item)" class="flexlist-img pointer" :src="item.people.imgurl+'.100x100.jpg'" />
					<div class="flex-1">
						<div class="flex mgb-5">
							<div  @click="goPeople(item)" class="pointer">{{item.people.truename}}</div>
							<div class="flex-1"></div>
							<div class="cl-status f12">{{item.status_name}}</div>
						</div>
						<div class="flexlist-desc">{{item.content}}</div>
						<div class="flex">
							<div class="flex-1"></div>
							<div @click="pass(item)" v-if="item.status==0 && type=='receive'" class="btn-mini mgr-5">接受</div>
							<div @click="forbid(item)" v-if="item.status==0 && type=='receive'"  class="btn-mini mgr-5">拒绝</div>
							<div @click="goPm(item)" v-if="item.status==1"  class="btn-mini">聊一聊</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	export default{
		data:function(){
			return {
				pageLoad:false,
				list:[],
				per_page:0,
				isFirst:true,
				type:"receive"
			}
		},
		created:function(){
			var s=uni.getStorageSync("xiangqin-notice-biaobai"); 
			if(s!=null){
				this.type=s;
			} 
			this.getPage();
		},
		methods:{
			setType:function(type){
				this.type=type;
				uni.setStorageSync("xiangqin-notice-biaobai",type); 
				this.per_page=0;
				this.isFirst=true;
				this.getList();
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_biaobai&a=my&ajax=1",
					dataType:"json",
					data:{
						type:this.type
					},
					success:function(res){
						if(res.error){
							uni.showToast({
								title:res.message,
								icon:"none"
							})
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
					url:that.app.apiHost+"/module.php?m=xiangqin_biaobai&a=my&ajax=1",
					data:{			 
						per_page:that.per_page,
						type:this.type
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							uni.showToast({
								title:res.message,
								icon:"none"
							})
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
			},
			pass:function(item){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_biaobai&a=pass&ajax=1",
					dataType:"json",
					data:{
						id:item.id
					},
					success:function(res){
						if(res.error){
							uni.showToast({
								title:res.message,
								icon:"none"
							})
							return false;
						}
						item.status_name="已通过";
						item.status=1;
					}
				})
			},
			forbid:function(item){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=xiangqin_biaobai&a=pass&ajax=1",
					dataType:"json",
					data:{
						id:item.id
					},
					success:function(res){
						if(res.error){
							uni.showToast({
								title:res.message,
								icon:"none"
							})
							return false;
						}
						item.status_name="已拒绝"
						item.status=2;
					}
				})
			},
			goPm:function(u){
				var userid=0;
				if(this.type=='receive'){
					userid=u.userid;
				}else{
					userid=u.touserid;
				}
				uni.navigateTo({
					url:"/pages/pm/detail?userid="+userid
				})
				 
			},
			goPeople:function(u){
				var userid=0;
				if(this.type=='receive'){
					userid=u.userid;
				}else{
					userid=u.touserid;
				}
				uni.navigateTo({
					url:"/xiangqin/xiangqin_people/show?userid="+userid
				})
				 
			}
		},
		 
	}
</script>

<style>
</style>