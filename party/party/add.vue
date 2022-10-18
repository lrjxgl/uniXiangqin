<template>
	<view>
		<form @submit="submit">
			<div class="none">
				<input type="text" name="lat" id="lat" :value="data.lat">
				<input type="text" name="id" :value="data.id">
				<input type="text" name="lng" id="lng" :value="data.lng">
			</div>
				 
				<div class="input-flex">
					<div class="input-flex-label">名称：</div>
					<input type="text" class="input-flex-text" name="title" id="title" :value="data.title">
				</div>
				<div class="input-flex">
					<div class="input-flex-label">宣传图：</div>
					<div class="flex-1">
						<div class="upimg-box bg-fff">
							<div @click="upload()"  v-if="data && data.imgurl!=''" class="upimg-item">
								<image class="upimg-img" :src="data.trueimgurl+'.100x100.jpg'" mode="widthFix">
								</image>
							</div>
							
							<div @click="upload()" class="upimg-btn js-upimg-btn">
								<i class="upimg-btn-icon"></i>
							</div>
							
							<input type="text" name="imgurl" :value="data.imgurl" class="none" />
						</div>
					</div>
				</div>
				<div class="textarea-flex">
					<div class="textarea-flex-label">描述：</div>
					<textarea class="textarea-flex-text h60" type="text" name="description" :value="data.description"></textarea>
				</div>
				<div class="input-flex">
					<div class="input-flex-label">开始时间：</div>
					<input type="text" class="none"  name="stime" id="stime" :value="data.stime">
					<uni-datetime-picker class="input-flex-text" style="line-height: 36px;" v-model="data.stime"  @change="setTime"></uni-datetime-picker>
					 
				</div>
				<div class="input-flex flex-ai-center">
					<div class="pd-5">活动人数：</div>
					<input type="text" class="input-flex-text mgr-5"  name="max_num" id="max_num" :value="data.max_num">
				 
					<div class="pd-5">活动费用：</div>
					<input type="text" class="input-flex-text"  name="money"  :value="data.money">
					<div class="mgl-5 cl2">元</div>
				</div>
				<div class="input-flex flex-ai-center">
					<div class="input-flex-label">支付类型：</div>
					<radio-group class="flex">
						<radio :checked="data.retype==0"  value="0"> 线下支付</radio>
						<radio :checked="data.retype==1" class="mgl-10" value="1"> 在线支付</radio>
					</radio-group>
					
					 
				</div>
				<div class="input-flex flex-ai-center">
					<div class="pd-5">联系人：</div>
					<input type="text" class="input-flex-text mgr-5"  name="nickname" id="nickname" :value="data.nickname">
				 
					<div class="pd-5">电话：</div>
					<input type="text" class="input-flex-text"  name="telephone" id="telephone" :value="data.telephone">
				</div>
				<div class="input-flex">
					<div class="input-flex-label">地址：</div>
					<input type="text" class="input-flex-text"  name="address" id="address" :value="data.address">
				</div>
				 
				 
				
				 
				<textarea name="content" class="none" :value="data.content"></textarea>
				<sky-editor contenteditable="true" class="sky-editor-content" @call-parent="setContent" :html="data.content"></sky-editor>
				
			 
			<button form-type="submit" class="btn-row-submit">保存</button>
		</form>
		
	</view>
</template>

<script>
	import skyEditor from "../../components/sky-editor.vue";
	 
	export default{
		components:{
			skyEditor
		},
		data:function(){
			return {
				id:0,
				data:{
					id:0,
					title:"",
					description:"",
					imgurl:"",
					trueimgurl:"",
					stime:"",
					max_num:100,
					money:0,
					retype:0,
					nickname:"",
					telephone:"",
					address:"",
					content:""
					
				}
			}
		},
		onLoad:function(ops){
			if(ops.id!=undefined){
				this.id=ops.id
			}
			this.getPage();
		},
		methods:{
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=party&a=add",
					data:{
						id:this.id
					},
					success:function(res){
						if(res.error){
							return false;
						}
						if(res.data.data){
							that.data=res.data.data;
						}
						
					}
				})
			},
			upload:function(){
				var that=this;
				uni.chooseImage({
					success: (chooseImageRes) => {
						const tempFilePaths = chooseImageRes.tempFilePaths;
						uni.uploadFile({
							url: that.app.apiHost+"/index.php?m=upload&a=img&ajax=1&loginToken="+that.app.getToken(),
							filePath: tempFilePaths[0],
							name: 'upimg',
							
							success: function(res){
								var e=JSON.parse(res.data);
								that.data.imgurl=e.data.imgurl;
								that.data.true_imgurl=e.data.trueimgurl;
								
							}
						});
					}
				});
			},
			setTime:function(e){
				this.data.stime=e;
			},
			setRetype:function(e){
				this.data.retype=e.detail.value;
			},
			setContent:function(e){
				 
				this.data.content=e;
			},
			submit:function(e){
				var that=this;
				if(!this.app.canPost()){
					return false;
				}
				that.app.post({
					url:that.app.apiHost+"/module.php?m=party&a=save&ajax=1",
					data:e.detail.value,
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if(res.error){
							return false;
						}
						setTimeout(function(){
							uni.navigateBack()
						},1000)
						
					}
				})
				 
			}
		}
	}
</script>

<style>
</style>