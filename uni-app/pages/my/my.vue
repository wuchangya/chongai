<template>
	<view>
		<!-- 未登录 -->
		<template v-if="!loginStatus">
			<view class="flex align-center justify-center py-2 font">
				登录社区，体验更多功能
			</view>
			<other-login></other-login>
			<view class="flex align-center justify-center py-2 font text-secondary" @click="openLogin">
				账号/邮箱/手机登录 <text class="ml-1 iconfont icon-jinru"></text>
			</view>
		</template>

		<!-- 已登录 -->
		<view v-else class="flex align-center p-2" hover-class="bg-light">
			<image :src="avatar"
			style="width: 100rpx;height: 100rpx;"
			class="rounded-circle"></image>
			<view class="flex flex-column flex-1 px-2">
				<text class="font-lg font-weight-bold text-dark">{{user.username}}</text>
				<text class="font text-muted">
					总帖子{{myData[0].num}}  今日发帖{{myData[1].num}}</text>
			</view>
			<text class="iconfont icon-jinru"></text>
		</view>
		
		<view class="flex align-center px-3 py-2">
			<view class="flex-1 flex flex-column align-center justify-center"
			v-for="(item,index) in myData" :key="index">
				<text class="font-lg font-weight-bold">{{item.num}}</text>
				<text class="font text-muted">{{item.name}}</text>
			</view>
		</view>
		
		<view class="px-3 py-2">
			<image src="/static/demo/banner1.jpg" mode="aspectFill"
			style="height: 170rpx;width: 100%;" class="rounded"></image>
		</view>
		
		<uni-list-item title="浏览历史" showExtraIcon @click="openHistory">
			<text slot="icon" class="iconfont icon-liulan"></text>
		</uni-list-item>
		<uni-list-item title="我的订单" showExtraIcon>
			<text slot="icon" class="iconfont icon-xuanze-yixuan"></text>
		</uni-list-item>
		
		
		<view class="a">
			
			
			<uni-list-item title="我的宠物" showExtraIcon @click="mydog" style="background-color: #fff;">
				<text slot="icon" class="iconfont icon-smile"></text>
			</uni-list-item>
			
			
			<view v-if="loginStatus">
				<view v-for="(item,index) in petList" :key="index">
					<view class="flex align-center p-2" hover-class="bg-light">
						<image :src="petAvatar(item)"
						style="width: 100rpx;height: 100rpx;"
						class="rounded-circle"></image>
						<view class="flex flex-column flex-1 px-2">
							<text class="font-lg font-weight-bold text-dark">{{item.pet_name}}</text>
							<text class="font text-muted">
								{{item.species}}</text>
						</view>
					</view>
				</view>
			</view>
			
			
		</view>
		
		
		<!-- #ifdef MP -->
		<navigator url="../user-set/user-set" hover-class="none">
		<uni-list-item title="我的设置" showExtraIcon>
			<text slot="icon" class="iconfont icon-shezhi"></text>
		</uni-list-item>
		</navigator>
		<!-- #endif -->
		
		
	</view>
</template>

<script>
	import uniListItem from '@/components/uni-ui/uni-list-item/uni-list-item.vue';
	import otherLogin from '@/components/common/other-login.vue';
	import { mapState } from 'vuex';
	import $store from '@/store/index.js';
	export default {
		components: {
			uniListItem,
			otherLogin
		},
		data() {
			return {
				myData:[{
					name:"帖子",
					num:0
				},{
					name:"动态",
					num:0
				},{
					name:"评论",
					num:0
				},{
					name:"粉丝",
					num:0
				}],
				petList:[]
			}
		},
		onNavigationBarButtonTap() {
			uni.navigateTo({
				url: '../user-set/user-set'
			});
		},
		computed: {
			...mapState({
				loginStatus:state=>state.loginStatus,
				user:state=>state.user
			}),
			// 用户头像
			avatar(){
				return this.user.userpic ? this.user.userpic : '/static/default.jpg'
			},
			// 宠物头像
			petAvatar(){
				return  (item) =>{
					return item.petpic ? item.petpic : '/static/pet-default.png'
				}
			}
		},
		onShow() {
			if(this.loginStatus){
				this.getCounts()
				this.getPetInfo()
			}
		},
		watch: {
			loginStatus(newValue, oldValue) {
				if(newValue){
					this.getCounts()
					
				} else {
					this.myData.forEach(item=>{
						item.num = 0
					})
				}
			}
		},
		methods: {
			// 获取用户相关数据
			getCounts(){
				this.$H.get('/user/getcounts/'+this.user.id,{},{
					token:true,
					notoast:true
				}).then(res=>{
					console.log(res);
					if(res){
						this.myData[0].num = res.post_count
						this.myData[1].num = res.today_posts_count
						this.myData[2].num = res.comments_count
						this.myData[3].num = res.withfen_count
					}
				})
			},
			//加载宠物信息
			getPetInfo(){
				
				// 请求宠物相关信息
				uni.request({
					url:'https://ceshi.aichong.online/api/v1/user/mypets',
					method:'GET',
					data:{},
					header:{
						"content-type":"application/json;charset=utf-8",
						"token":$store.state.token},
					success: (res) => {
						this.petList = res.data.data.list;
						console.log(this.petList);
						// 将修改提交到vuex
						this.$store.commit('editPetInfo',{
							value:res.data.data.list
						})
						console.log($store.state.petList);
					},
					fail: (res) => {
						console.log(res);
					}
				})
				
			},
			// 打开登录页
			openLogin(){
				uni.navigateTo({
					url: '../login/login',
				});
			},
			openHistory(){
				uni.navigateTo({
					url: '../history/history'
				});
			},
			mydog(){
				// console.log($store.state.loginStatus);
				if($store.state.loginStatus){
					uni.navigateTo({
						url: '../mydog/mydog'
					});
				}else{
					uni.navigateTo({
						url: '../login/login'
					});
				}
				
			}
		}
	}
</script>

<style>
	.a{
		height: 100%;
		
		background-color: #f2f2f2;
	}
</style>
