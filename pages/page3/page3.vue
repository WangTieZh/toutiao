<template>
	<view>
		<text>我的</text>
		<button @click="oo">登录</button>
	</view>
</template>

<script setup>
	const oo=()=>{
		uni.navigateTo({
			url:'/pages/login/login'
		})
	}
	
</script>

<style>

</style>
