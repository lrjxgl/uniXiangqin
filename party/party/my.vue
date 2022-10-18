<template>
	<view>
		 
		<div v-if="list.length==0" class="emptyData">暂无活动</div>
		 
		<div class="sglist">
			 
			<div v-for="(item,index) in list" :key="index"  class="sglist-item js-item">
				 
				<div class="sglist-title">{{item.title}}</div>
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
				<div class="flex">
					<div class="flex-1"></div>
					<div class="btn-mini btn-outline-primary mgr-5" @click="gourl('../party_join/admin?pid='+item.id)">人员审核</div>
					 
					<div v-if="item.status!=1 " class="btn-mini btn-outline-primary mgr-5" @click="gourl('../party/add?id='+item.id)" gourl="/module.php?m=party&a=add&id={$c.id}">编辑</div>
					 
					<div class="btn-mini btn-outline-danger mgr-5" @click="cancel(item)" >取消活动</div>
					
					<div class="btn-mini btn-outline-primary"  @click="gourl('../party/show?id='+item.id)" >查看</div>
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
					url:that.app.apiHost+"/module.php?m=party&a=my&ajax=1",
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
					url:that.app.apiHost+"/module.php?m=party&a=my&ajax=1",
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
			},
			cancel:function(item){
				var that=this;
				uni.showModal({
					content:"确认取消活动吗?",
					success:function(e){
						if(e.confirm){
							that.app.get({
								url:that.app.apiHost+"/module.php?m=party&a=delete&ajax=1",
								data:{
									id:item.id
								},
								success:function(res){
									var list=[];
									for(var i in that.list){
										if(that.list[i].id!=item.id){
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
		}
	}
	
</script>

<style>
</style>