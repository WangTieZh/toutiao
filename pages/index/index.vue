<template>
	<view class="content1">
		<div class="top">
			<van-tabs :active="ctab" sticky @click="changetab" >
				<van-tab :title="item.name" v-for="(item,i) in tablist" :key="i" :name="item.id">
						<div style="width: 100%;margin-top: 10rpx;" v-for="(item,index) in wenlist" :key="index" @click="goto(item.art_id)">
							<div style="margin-top: 20rpx;display: flex;justify-content: space-between;" v-if="item.cover.type==1">
								<h3 style='text-overflow: ellipsis;width: calc(100% - 250rpx);'>{{item.title}}</h3>
								<div style="margin: 10rpx 0;" >
									<img :src="wang+item.cover.images[0].split('net/')[1]" class="img">
								</div>
							</div>
							<div style="margin-top: 20rpx;" v-else>
								<h3 style='text-overflow: ellipsis;'>{{item.title}}</h3>
								<div v-if="item.cover.type==3" style="display: flex;justify-content: space-around;margin: 10rpx 0;" >
									<img :src="wang+item1.split('net/')[1]" v-for="(item1,index1) in item.cover.images" :key="index1" class="img">
								</div>
							</div>
							<div style="padding-top: 20rpx;color: grey;font-size: 30rpx;padding-left: 10rpx;padding-bottom: 10rpx;box-sizing: border-box;">{{item.aut_name}}&nbsp;{{item.comm_count}}评论&nbsp;{{2023-parseInt(item.pubdate.split('-')[0])}}年内</div>
						</div>
				</van-tab>
				<div slot="nav-right" style="display: flex;background-color: white;width: 200rpx;">
					<image src="../../static/search.png" alt="" style="height: 60rpx;width: 80rpx;" />
					<image src="../../static/menu.png" alt="" style="height: 60rpx;width: 80rpx;margin-left: 10rpx;"
						@click="showmenu" />
				</div>
			</van-tabs>
		</div>
		<van-popup :show="show" position="left" @close="close" custom-style="width:100%;height:100vh;" closeable
			:z-index="99">
			<div style="display: flex;justify-content: space-around;margin-top: 60rpx;">
				<div>
					<span style="font-size: 40rpx;font-weight: 700;">我的频道</span><span
						style="color: grey;font-size: 30rpx;margin-left: 20rpx;">点击进入频道</span>
				</div>
				<div>
					<button class="anniu" v-if="isbutton" @click="()=>{isbutton=false}">编辑</button>
					<button class="anniu" v-else @click="savelist">保存</button>
				</div>
			</div>
			<div style="display: flex;margin-top: 50rpx;flex-wrap: wrap;justify-content:flex-start;">
				<button v-for="item in tablist" :key="item.id" class="anniu1" :style="{color: ctab == item.id? 'red':'black'}" 
					 @click="isbutton?tiao(item.id):del(item.id)">
					{{item.name}}
					<span v-if="!isbutton" style="font-size: 10rpx;color: white; border-radius: 50%;height: 20rpx;width: 20rpx;background-color: gray;position: absolute;top: 35%;right: 10rpx;">X</span>
				</button>
			</div>
			<div style="display: flex;margin-top: 60rpx;margin-left: 60rpx;">
				<div>
					<span style="font-size: 40rpx;font-weight: 700;">频道推荐</span><span
						style="color: grey;font-size: 30rpx;margin-left: 20rpx;">点击添加频道</span>
				</div>
			</div>
			<div style="display: flex;margin-top: 50rpx;flex-wrap: wrap;justify-content:flex-start;">
				<button v-for="item in taball" :key="item.id" class="anniu1"
					style="color: ctab== item.name ? red : black" @click="addtab(item.id)">+{{item.name}}</button>
			</div>
		</van-popup>
	</view>
</template>

<script setup>
	import {
		onMounted,
		ref
	} from 'vue';
	import {onReachBottom} from '@dcloudio/uni-app'
	let tablist = ref([])
	let taball=ref([])
	let wenlist=ref([])
	let active = ref(1)
	let show = ref(false)
	let ctab = ref(1)
	let wang=ref("http://toutiao.itheima.net/")
	let isbutton = ref(true)
	const getlist = () => {
			let a=uni.getStorageSync('user')
	
		if(a){
			tablist.value=uni.getStorageSync('user')
		}else{
			uni.request({
				url: 'http://toutiao.itheima.net/v1_0/user/channels',
				success(res) {
					tablist.value = res.data.data.channels
					uni.setStorageSync('user',res.data.data.channels)
				}
			})
		}
	}
	const close = () => {
		show.value = false;
	}
	const changetab = (e) => {
		ctab.value = e.target.name
		let timer=Date.now()
		uni.request({
			url:`http://toutiao.itheima.net/v1_0/articles?channel_id=${ctab.value}&timestamp=${timer}`,
			success(res) {
					console.log(res);
					wenlist.value=res.data.data.results
			}
		})

	}
	const savelist=()=>{
		uni.setStorageSync('user',tablist.value)
		isbutton.value=true
		
	}
	const showmenu=()=>{
		uni.request({
			url:'http://toutiao.itheima.net/v1_0/channels',
			success(res) {
				let a=res.data.data.channels.filter(v => tablist.value.findIndex((item) => item.id === v.id) < 0)
				taball.value=a
			}
		})
		show.value=true
		// isbutton.value=true
	}
	const addtab=(e)=>{
		let a=taball.value.findIndex(item=>{return item.id==e})
		tablist.value.push(taball.value[a])
		taball.value.splice(a,1)
		uni.setStorageSync('user',tablist.value)
	}
	const tiao=(e)=>{
		show.value=false
		ctab.value=e
		let timer=Date.now()
		uni.request({
			url:`http://toutiao.itheima.net/v1_0/articles?channel_id=${e}&timestamp=${timer}`,
			success(res) {
					console.log(res);
					wenlist.value=res.data.data.results
			}
		})
		
		
	}
	const goto=(e)=>{
		uni.navigateTo({url:`/pages/show/show?id=${e}`})
	}
	const del=(e)=>{
		let a=tablist.value.findIndex(item=>{return item.id==e})
		taball.value.push(tablist.value[a])
		tablist.value.splice(a,1)
	}
	onReachBottom(()=>{
		let timer=Date.now()
		uni.request({
			url:`http://toutiao.itheima.net/v1_0/articles?channel_id=${ctab.value}&timestamp=${timer}`,
			success(res) {
					console.log(timer);
					wenlist.value.push(...res.data.data.results)
			}
		})
	})
	onMounted(() => {
		getlist()
		let timer=Date.now()
		uni.request({
			url:`http://toutiao.itheima.net/v1_0/articles?channel_id=1&timestamp=${timer}`,
			success(res) {
					console.log(res);
					wenlist.value=res.data.data.results
			}
		})
		
	})
</script>

<style>
	.content1 {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}

	.top {
		width: 100%;
		height: 200rpx;
	}

	.van-tabs__scroll {
		width: calc(100% - 200rpx);
	}
	.van-tab__pane{
		padding-left: 10rpx;
		padding-right: 10rpx;
	}
	.search {
		width: 120rpx !important;
		height: 50px;
	}

	.anniu {
		color: red !important;
		width: 150rpx !important;
		height: 60rpx !important;
		font-size: 26rpx !important;
		border-radius: 30rpx !important;
		border: 1rpx solid red !important;
	}

	.anniu1 {
		min-width: 150rpx;
		height: 60rpx;
		font-size: 26rpx;
		border-radius: 60rpx;
		border: none;
		margin-left: 40rpx;
		margin-top: 25rpx;
		position: relative;
	}
	.cc{
		color: red;
	}
	.img{
		width: 230rpx;
		height: 150rpx;
	}
</style>
