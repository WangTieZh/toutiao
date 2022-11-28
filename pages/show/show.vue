<template>
	<h3 style="width: 100%;line-height: 100rpx;height: 100rpx;padding-left: 35rpx;font-size: 40rpx;font-weight: 700;">{{art.title}}</h3>
	<p style="width: 100%;padding-left: 35rpx;color: grey;font-size: 20rpx;">{{art.pubdate.split(" ")[0]}}&nbsp;|{{art.read_count || 0}} 阅读&nbsp;|{{art.comm_count}}评论</p>
	<div class="top">
		<div style="margin-left: 50rpx;">
			<img :src="art.aut_photo" alt="" style="height: 100rpx;width: 100rpx;border-radius: 50%;">
			<span>{{art.aut_name}}</span>
		</div>
		<button style="margin-right: 30rpx;height: 60rpx;width: 160rpx;background-color: orange;color: white;line-height: 60rpx;border-radius: 20rpx;"> +关注</button>
	</div>
	<rich-text :nodes="art.content" style="padding:20rpx 20rpx;"></rich-text>
	<div style="height: 150rpx;text-align: right;line-height: 150rpx;color: grey;font-size: 20rpx;">发布文章日期:{{art.pubdate.split(" ")[0]}}</div>
	<div style="margin-top: 40rpx;width:100%;height: 600rpx;">
		<div style="display: flex;justify-content: space-between;">
			<span style="margin-left: 40rpx;">全部评论 (0)</span>
			<span style="margin-right: 40rpx;">0 点赞</span>
		</div>
		<div style="text-align: center;">
			<img src="http://geek-h5.itheima.net/static/media/none.78b85fdc.png" alt="">
			<p>还没有人评论哦</p>
		</div>
	</div>
	
</template>

<script setup>	
import { ref, onMounted } from 'vue'
import { onLoad } from '@dcloudio/uni-app'
let art=ref({})
onLoad((e)=>{
	console.log(e.id)
	uni.request({
		url:`http://toutiao.itheima.net/v1_0/articles/${e.id}`,
		success:(res)=>{
			console.log(res.data.data,123);
			art.value=res.data.data
		}
	})
	
	})
onMounted(()=>{
	uni.createSelectorQuery().select('#cont').boundingClientRect(function (data) {
		  console.log('元素信息0：',data)
			  // that.vHeight +=10
		}).exec()
})
</script>

<style scoped>
.top{
	width: 100%;
	height: 200rpx;
	display: flex;
	justify-content: space-between;
	align-items: center;
}
</style>
