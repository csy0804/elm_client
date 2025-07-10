<template>
	<ul class="footer">
		<!-- <li @click="goBack">
		    <i class="fa fa-arrow-left"></i>
		</li> -->
		<li @click="toIndex" :class="{ active: isActive('/index') }">
			<i class="fa fa-home"></i>
			<p>首页</p>
		</li>
		<li @click="toChatgpt" :class="{ active: isActive('/chatgpt') }">
			<i class="fa fa-compass"></i>
			<p>发现</p>
		</li>
		<li @click="toOrderList" :class="{ active: isActive('/orderList') }">
			<i class="fa fa-file-text-o"></i>
			<p>订单</p>
		</li>
		<li @click="toMyInfo" :class="{ active: isActive('/myInfo') }">
			<i class="fa fa-user-o"></i>
			<p>我的</p>
		</li>
	</ul>
</template>
<script>
	export default {
		name: 'Footer',
		methods: {
			goBack() {
				this.$router.go(-1); // 返回上一页
			},
			toIndex() {
				this.$router.push({
					path: '/index'
				});
			},
			toOrderList() {
				this.$router.push({
					path: '/orderList'
				});
			},
			toMyInfo() {
				this.$router.push({
					path: '/myInfo'
				});
			},
			toChatgpt() {
				this.$router.push({
					path: '/chatgpt'
				});
			},
			isActive(path) {
				return this.$route.path === path;
			}
		}
	}
</script>
<style>
	/* 	.wrapper .footer li:first-child {
	    position: absolute;
	    left: 10px;
	    top: 10px;
	} */
	.wrapper .footer {
		width: 100%;
		height: 14vw;
		border-top: solid 1px #DDD;
		background-color: #fff;
		position: fixed;
		left: 0;
		bottom: 0;
		display: flex;
		justify-content: space-around;
		align-items: center;
	}

	.wrapper .footer li {
		/*li本身的尺寸完全由内容撑起*/
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		color: #999;
		user-select: none;
		cursor: pointer;
		transition: all 0.3s ease;
	}

	.wrapper .footer li.active {
		color: #0097FF;
	}

	.wrapper .footer li:hover {
		color: #0097FF;
		transform: translateY(-1px);
	}

	.wrapper .footer li p {
		font-size: 2.8vw;
	}

	.wrapper .footer li i {
		font-size: 5vw;
	}
</style>