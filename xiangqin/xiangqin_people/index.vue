<template>
	<view>
		 
		<div class="tabs-border mgb-5">
			<div class="tabs-border-item pos-relative">
				<div @click="tabShow('tabAgeShow')" class="flex flex-center">
					<span class="mgr-5">{{age_label}}</span>
					<span class="iconfont f12 icon-godown"></span>
				</div>
				<div v-if="tabAgeShow" class="tabs-absox">
					<div class="bd-mp-5" @click="setAge('年龄')" >不限年龄</div>
					<div class="bd-mp-5" @click="setAge(item)" v-for="(item,index) in ageList" :key="index">
						{{item}}
					</div>
				</div>
			</div>
			<div class="tabs-border-item pos-relative">
				<div  @click="tabShow('tabMoneyShow')" class="flex flex-center">
					<span class="mgr-5">{{money_label}}</span>
					<span class="iconfont f12 icon-godown"></span>
				</div>
				<div v-if="tabMoneyShow" class="tabs-absox">
					<div class="bd-mp-5" @click="setMoney('收入')" >不限收入</div>
					<div class="bd-mp-5" @click="setMoney(item)" v-for="(item,index) in moneyList" :key="index">
						{{item}}
					</div>
				</div>
			</div>
			
			<div class="tabs-border-item pos-relative">
				<div @click="tabShow('tabAddrShow')" class="flex flex-center">
					<span class="mgr-5">{{addr_label}}</span>
					<span class="iconfont f12 icon-godown"></span>
				</div>
				<div v-if="tabAddrShow" class="tabs-absox">
					<div class="bd-mp-5" @click="setAddr(0,'户籍')" >不限户籍</div>
					<div class="bd-mp-5" @click="setAddr(item.id,item.title)" v-for="(item,index) in addrList" :key="index">
						{{item.title}}
					</div>
				</div>
			</div>
			 
		</div>
		<div>
			<div class="flex-center cl2 pd-10">温馨提示：本站只负责收集相亲信息，职业收入等信息未经确认，请广大用户注意。</div>
			<div class="flex">
				<div class="flex-1 mgr-10">
					<div @click="goDetail(item.id)" class="row-box mgb-10 bd-radius-10" v-for="(item,index) in listA" :key="index">
						<img :src="item.imgurl+'.small.jpg'" class="wmax mgb-5" />
						<div >
							<div class="flex mgb-5">
								<div class="mgr-10">{{item.truename}}</div>
							</div>
							<div class="flex mgb-5">
								<div class="cl3 f12 mgr-10">{{item.gender_title}}</div>
								<div class="cl3 f12">{{item.age}}岁</div>
								<div class="flex-1"></div>
								
								<div class="cl-primary f12">收入：{{item.income}}万</div>
							</div>
							 
							<div class="flexlist-desc">{{item.self_desc}}</div>
							 
						</div>
						
					</div>
				</div>
				<div class="flex-1">
					<div @click="goDetail(item.id)" class="row-box mgb-10 bd-radius-10" v-for="(item,index) in listB" :key="index">
						<img :src="item.imgurl+'.small.jpg'" class="wmax mgb-5" />
						<div >
							<div class="flex mgb-5">
								<div class="mgr-10">{{item.truename}}</div>
							</div>
							<div class="flex mgb-5">
								<div class="cl3 f12 mgr-10">{{item.gender_title}}</div>
								<div class="cl3 f12">{{item.age}}岁</div>
								<div class="flex-1"></div>
								 
								<div class="cl-primary f12">收入：{{item.income}}万</div>
							</div>
							
							
							<div class="flexlist-desc">{{item.self_desc}}</div>
							 
						</div>
						
					</div>
				</div>
			</div>
			
		</div>
		 
		<div v-if="unJoin" gourl="/module.php?m=xiangqin_people&a=add" class="fixedAdd">加入</div>
		 
	</view>
</template>

<script>
	import xiangFooter from "@/xiangqin/xiangqin-footer.vue"
	export default{
		components:{
			xiangFooter
		},
		data:function(){
			return {
				pageLoad:false,
				flashList:[],
				navList:[],
				adList:[],
				list:[],
				per_page:0,
				isFirst:true,
				catid:0,
				listA:[],
				listB:[],
				tabAgeShow:false,
				ageList:[],
				age_label:"年龄",
				tabMoneyShow:false,
				moneyList:[],
				money_label:"收入",
				tabAddrShow:false,
				addrList:[],
				addr_label:"户籍",
				addrid:0,
				unJoin:true,
				 
				
			}
		},
		created:function(){
			 
			this.getPage();
	 
		},
		methods:{
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			tabShow:function(t){
				switch(t){
					case "tabAgeShow":
						
						this.tabAgeShow=this.tabAgeShow?false:true;
						this.tabMoneyShow=false;
						this.tabAddrShow=false;
						break;
					case "tabMoneyShow":
						this.tabAgeShow=false;
						this.tabMoneyShow=this.tabMoneyShow?false:true;
						this.tabAddrShow=false;
						break;
					case "tabAddrShow":
						this.tabAgeShow=false;
						this.tabMoneyShow=false;
						this.tabAddrShow=this.tabAddrShow?false:true;
						break;
				}
			},
			setAge:function(item){
				this.age_label=item;
				this.tabAgeShow=false;
				this.isFirst=true;
				this.per_page=0;
				this.getList();
			},
			setMoney:function(item){
				this.money_label=item;
				this.tabMoneyShow=false;
				this.isFirst=true;
				this.per_page=0;
				this.getList();
			},
			setAddr:function(addrid,title){
				console.log(addrid)
				this.addrid=addrid;
				this.cat_label=title;
				this.tabAddrShow=false;
				this.isFirst=true;
				this.per_page=0;
				this.getList();
			},
			 
			getPage:function(){
				var that=this;
				this.app.get({
					url:this.app.apiHost+"/module.php?m=xiangqin_people&ajax=1",
					data:{
						catid:this.catid
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							skyToast(res.message);
							return false;
						}
						that.list=res.data.list;
						that.parseList()
						that.isFirst=false;
						that.per_page=res.data.per_page;
						that.ageList=res.data.ageList;
						that.moneyList=res.data.moneyList;
						that.addrList=res.data.addrList;
						that.pageLoad=true;
					}
				})
			},
			parseList:function(){
				var listA=[],listB=[];
				for(var i in this.list){
					if(i%2==0){
						listA.push(this.list[i])
					}else{
						listB.push(this.list[i])
					}
				}
				this.listA=listA;
				this.listB=listB;
			},
			getList:function(){
				var that=this;
				if(that.per_page==0 && !that.isFirst){
					return false;
				}
				var money=this.money_label=='收入'?'':this.money_label;
				var age=this.age_label=="年龄"?'':this.age_label;
				this.app.get({
					url:this.app.apiHost+"/module.php?m=xiangqin_people&a=list&ajax=1",
					data:{
						catid:this.catid,
						per_page:that.per_page,
						addrid:this.addrid,
						money_choice:money,
						age_choice:age
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
						
						that.parseList()
						that.per_page=res.data.per_page;
						that.pageLoad=true;
					}
				})
			},
			goDetail:function(id){
				if(this.unJoin){
					uni.showToast({
						title:"您还未加入相亲部落，无法查看",
						icon:"none"
					})
	 
					return false;
				}
				uni.navigateTo({
					url:"/xiangqin/xiangqin_people/show?id="+id
				})
 
			}
		}
	}
</script>

<style>
	.tabs-absox{
		position: absolute;
		top:100%;
		left:0;
		right:0;
		background-color: #fff;
		border: 1px solid #eee;
		border-bottom: 0;
	}
</style>