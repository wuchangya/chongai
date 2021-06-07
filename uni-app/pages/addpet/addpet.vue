<template>
	<view>
		<view class="text-center" @click="changepetpic">
			
				<image :src="pic ? pic : '/static/pet-default.png'"
				style="width: 180rpx;height: 180rpx;"
				class="rounded-circle"></image>
			
		</view>
		<uni-list-item title="宠物昵称">
			<view class="flex align-center" slot="right">
				<input class="uni-input text-right" v-model="petname" />
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="宠物性别" @click="changeSex">
			<view class="flex align-center" slot="right">
				<text>{{sexText}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<picker mode="date" :value="birthday" @change="onDateChange">
			<uni-list-item title="宠物生日">
				<view class="flex align-center" slot="right">
					<text>{{birthday}}</text>
					<text class="iconfont icon-bianji1 ml-2"></text>
				</view>
			</uni-list-item>
		</picker>
		
		
		<uni-list-item title="是否绝育" @click="changeJueyu">
			<view class="flex align-center" slot="right">
				<text>{{jueyuText}}</text>
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		
		
		<uni-list-item title="宠物品种">
			<view class="flex align-center" slot="right">
				<input class="uni-input text-right" v-model="pinzhong" />
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="宠物体重">
			<view class="flex align-center" slot="right">
				<input class="uni-input text-right" v-model="tizhong" />
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		<uni-list-item title="宠物特征">
			<view class="flex align-center" slot="right">
				<input class="uni-input text-right" v-model="feature" />
				<text class="iconfont icon-bianji1 ml-2"></text>
			</view>
		</uni-list-item>
		
		<view class="py-2 px-3">
			<button class="bg-main text-white" style="border-radius: 50rpx;border: 0;" type="primary" @click="submit">完成</button>
		</view>
		
	</view>
</template>

<script>
	var sourceType = [
		['camera'],
		['album'],
		['camera', 'album']
	]
	var sizeType = [
		['compressed'],
		['original'],
		['compressed', 'original']
	]
	const sexArray = ['保密', 'GG', 'MM']
	const jueyuArray = ['保密','是','否']
	import uniListItem from '@/components/uni-ui/uni-list-item/uni-list-item.vue';
	import { mapState } from 'vuex'
	
	export default {
		components: {
			uniListItem
		},
		data() {
			return {
				pic:'',
				title: 'choose/previewImage',
				imageList: [],
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				countIndex: 8,
				count: [1, 2, 3, 4, 5, 6, 7, 8, 9],
				
				
				
				themeColor: '#007AFF',
				cityPickerValueDefault: [0, 0, 1],
				pickerText: '',
				petname:"昵称",
				sex:0,
				birthday:"2019-03-18",
				pinzhong:'',
				tizhong:1,
				hobby:'',
				jueyu:0,
				feature:''
			}
		},
		
		onLoad() {
			/* let petinfo = this.pet
			if(petinfo){
				this.pickerText = petinfo.path
				this.petname = this.pet.petname
				this.sex =  petinfo.sex
				this.emotion = petinfo.qg
				this.job  = petinfo.job
				this.birthday  = petinfo.birthday
			} */
		},
		computed: {
			...mapState({
				pet:state=>state.pet
			}),
			sexText() {
				return sexArray[this.sex]
			},
			jueyuText(){
				return jueyuArray[this.jueyu]
			},
			imgListIds(){
				return this.imageList.map(item=>{
					return {
						id:item.id
					}
				})
			}
		},
		methods: {
			
			// 修改生日
			onDateChange(e){
				this.birthday = e.detail.value
			},
			// 修改头像
			changepetpic(){
				console.log(this.$store.state);
				
				uni.chooseImage({
					sourceType: sourceType[this.sourceTypeIndex],
					sizeType: sizeType[this.sizeTypeIndex],
					count: this.imageList.length + this.count[this.countIndex] > 9 ? 9 - this.imageList.length : this.count[this.countIndex],
					success: (res) => {
						// 上传图片
						res.tempFilePaths.forEach(item=>{
							this.$H.upload('/image/uploadmore',{
								filePath: item,
								name: 'imglist[]',
								token:true
							}).then(result=>{
								 console.log(this.imageList);
								console.log(result);
								if(!result.data.list.length){
									return uni.showToast({
										title: '上传失败',
										icon: 'none'
									});
								}
								this.imageList.push(result.data.list[0])
								this.pic = this.imageList[0].url;
								console.log(this.imgListIds);
								// this.$emit('change',this.imageList)
								
								this.$store.commit('editUserInfo',{
									key:"petpic",
									value:result.data
								})
								console.log(pet.petpic);
								
							})
						})
					},
					fail: (err) => {
						// #ifdef APP-PLUS
						if (err['code'] && err.code !== 0 && this.sourceTypeIndex === 2) {
							this.checkPermission(err.code);
						}
						// #endif
						// #ifdef MP
						uni.getSetting({
							success: (res) => {
								let authStatus = false;
								switch (this.sourceTypeIndex) {
									case 0:
										authStatus = res.authSetting['scope.camera'];
										break;
									case 1:
										authStatus = res.authSetting['scope.album'];
										break;
									case 2:
										authStatus = res.authSetting['scope.album'] && res.authSetting['scope.camera'];
										break;
									default:
										break;
								}
								if (!authStatus) {
									uni.showModal({
										title: '授权失败',
										content: 'Hello uni-app需要从您的相机或相册获取图片，请在设置界面打开相关权限',
										success: (res) => {
											if (res.confirm) {
												uni.openSetting()
											}
										}
									})
								}
							}
						})
						// #endif
					}
				})
				
			
				
				
			},
			// 修改性别
			changeSex(){
				uni.showActionSheet({
				    itemList: sexArray,
				    success:(res)=>{
				        this.sex = res.tapIndex
				    }
				});
			},

			//是否绝育
			changeJueyu(){
				uni.showActionSheet({
				    itemList: jueyuArray,
				    success:(res)=>{
							console.log(res.tapIndex);
				        this.jueyu = res.tapIndex
				    }
				});
			},
			// 提交
			submit(){
				console.log('进行提交');
				
				this.$H.post('/pet/addpet',{
					imglist:JSON.stringify(this.imgListIds),
					pet_name:this.petname,
					species:this.pinzhong,
					pet_sex:this.sex,
					pet_birthday:this.birthday,
					feature:this.feature,
					jueyu:this.jueyu,
					weight:parseFloat(this.tizhong)
				},{
					token:true
				}).then(res=>{
					console.log('1111');
					uni.showToast({
						title: '添加宠物成功',
						icon: 'none'
					});
					uni.navigateBack({
						delta: 1
					});
				}).catch(err=>{
					console.log(err);
				})
			}
		}
	}
</script>

<style>

</style>
