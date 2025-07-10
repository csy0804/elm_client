<template>
	<div class="wrapper">
		<!-- header部分 -->
		<header>
			<p>我的订单</p>
		</header>
		
		<!-- 订单列表部分 -->
		<div class="order-container">
			<!-- 未支付订单 -->
			<div class="order-section">
				<h3 class="section-title">
					<i class="fa fa-clock-o"></i>
					未支付订单
				</h3>
				<div class="order-list">
					<div class="order-item" v-for="item in orderArr" :key="item.orderId">
						<template v-if="item.orderState == 0">
							<div class="order-header" @click="detailetShow(item)">
								<div class="business-info">
									<div class="business-name">
										<i class="fa fa-store"></i>
										{{item.business.businessName}}
									</div>
									<div class="order-status unpaid">
										<i class="fa fa-exclamation-circle"></i>
										待支付
									</div>
								</div>
								<div class="order-summary">
									<div class="order-total">
										<span class="currency">¥</span>
										<span class="amount">{{item.orderTotal}}</span>
									</div>
									<div class="expand-btn" :class="{ expanded: item.isShowDetailet }">
										<i class="fa fa-chevron-down"></i>
									</div>
								</div>
							</div>
							
							<div class="order-details" v-show="item.isShowDetailet">
								<div class="food-items">
									<div class="food-item" v-for="odItem in item.list" :key="odItem.food.foodId">
										<div class="food-info">
											<span class="food-name">{{ odItem.food.foodName }}</span>
											<span class="food-quantity">×{{ odItem.quantity }}</span>
										</div>
										<div class="food-price">
											¥{{ (odItem.food.foodPrice * odItem.quantity).toFixed(2) }}
										</div>
									</div>
								</div>
								
								<div class="delivery-info">
									<div class="delivery-item">
										<span>配送费</span>
										<span>¥{{item.business.deliveryPrice}}</span>
									</div>
								</div>
								
								<div class="order-actions">
									<button class="btn btn-cancel" @click="cancelOrder(item.orderId)">
										取消订单
									</button>
									<button class="btn btn-pay" @click="detailetPayment(item.orderId)">
										立即支付
									</button>
								</div>
							</div>
						</template>
					</div>
				</div>
			</div>

			<!-- 已支付订单 -->
			<div class="order-section">
				<h3 class="section-title">
					<i class="fa fa-check-circle"></i>
					已完成订单
				</h3>
				<div class="order-list">
					<div class="order-item" v-for="item in orderArr" :key="item.orderId">
						<template v-if="item.orderState == 1">
							<div class="order-header" @click="detailetShow(item)">
								<div class="business-info">
									<div class="business-name">
										<i class="fa fa-store"></i>
										{{item.business.businessName}}
									</div>
									<div class="order-status completed">
										<i class="fa fa-check-circle"></i>
										已完成
									</div>
								</div>
								<div class="order-summary">
									<div class="order-total">
										<span class="currency">¥</span>
										<span class="amount">{{item.orderTotal}}</span>
									</div>
									<div class="expand-btn" :class="{ expanded: item.isShowDetailet }">
										<i class="fa fa-chevron-down"></i>
									</div>
								</div>
							</div>
							
							<div class="order-details" v-show="item.isShowDetailet">
								<div class="food-items">
									<div class="food-item" v-for="odItem in item.list" :key="odItem.food.foodId">
										<div class="food-info">
											<span class="food-name">{{odItem.food.foodName}}</span>
											<span class="food-quantity">×{{odItem.quantity}}</span>
										</div>
										<div class="food-price">
											¥{{(odItem.food.foodPrice*odItem.quantity).toFixed(2)}}
										</div>
									</div>
								</div>
								
								<div class="delivery-info">
									<div class="delivery-item">
										<span>配送费</span>
										<span>¥{{item.business.deliveryPrice}}</span>
									</div>
								</div>
								
								<div class="order-actions">
									<button class="btn btn-reorder" @click="reorder(item)">
										再来一单
									</button>
									<button class="btn btn-detail" @click="viewOrderDetail(item.orderId)">
										查看详情
									</button>
								</div>
							</div>
						</template>
					</div>
				</div>
			</div>
		</div>

		<!-- 底部菜单部分 -->
		<Footer></Footer>
	</div>
</template>

<script>
	import Footer from '../components/Footer.vue';

	export default {
		name: 'OrderList',
		data() {
			return {
				orderArr: [],
				user: {}
			}
		},
		created() {
			this.user = JSON.parse(window.sessionStorage.getItem('user'));
			this.loadOrders();
		},
		methods: {
			loadOrders() {
				let url = 'OrdersController/listOrdersByUserId/'+this.user.userId;
				
				this.$axios.get(url).then(response => {
					let result = response.data.result;
					for (let orders of result) {
						orders.isShowDetailet = false;
					}
					this.orderArr = result;
				}).catch(error => {
					console.error(error);
				});
			},
			detailetShow(orders) {
				orders.isShowDetailet = !orders.isShowDetailet;
			},
			detailetPayment(orderId) {
				this.$router.push({
					path: '/payment',
					query: {
						orderId: orderId
					}
				});
			},
			cancelOrder(orderId) {
				// 取消订单逻辑
				if (!confirm('确认要取消这个订单吗？')) {
					return;
				}
				
				// 调用取消订单接口
				let url = 'OrdersController/cancelOrder/' + orderId;
				this.$axios.get(url).then(response => {
					if (response.data.result > 0) {
						alert('订单已成功取消！');
						// 重新加载订单列表
						this.loadOrders();
					} else {
						alert('取消订单失败，请稍后重试！');
					}
				}).catch(error => {
					console.error('取消订单失败:', error);
					alert('取消订单失败，请稍后重试！');
				});
			},
			reorder(item) {
				// 再来一单逻辑
				this.$router.push({
					path: '/businessList',
					query: {
						businessId: item.business.businessId
					}
				});
			},
			viewOrderDetail(orderId) {
				// 查看订单详情 - 在当前页面显示更详细的信息
				// 找到对应的订单
				let order = this.orderArr.find(item => item.orderId == orderId);
				if (order) {
					// 显示详细信息
					let detailInfo = `订单详情：\n`;
					detailInfo += `订单号：${order.orderId}\n`;
					detailInfo += `商家：${order.business.businessName}\n`;
					detailInfo += `订单状态：${order.orderState == 0 ? '待支付' : '已完成'}\n`;
					detailInfo += `订单金额：¥${order.orderTotal}\n`;
					detailInfo += `配送费：¥${order.business.deliveryPrice}\n`;
					detailInfo += `\n商品清单：\n`;
					
					order.list.forEach(item => {
						detailInfo += `${item.food.foodName} × ${item.quantity} = ¥${(item.food.foodPrice * item.quantity).toFixed(2)}\n`;
					});
					
					alert(detailInfo);
				}
			}
		},
		components: {
			Footer
		}
	}
</script>

<style scoped>
	/****************** 总容器 ******************/
	.wrapper {
		width: 100%;
		height: 100%;
		background-color: #f5f5f5;
	}

	/****************** header部分 ******************/
	.wrapper header {
		width: 100%;
		height: 12vw;
		background-color: #0097FF;
		color: #fff;
		font-size: 4.8vw;
		position: fixed;
		left: 0;
		top: 0;
		z-index: 1000;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	/****************** 订单容器 ******************/
	.order-container {
		margin-top: 12vw;
		padding: 4vw;
		padding-bottom: 18vw; /* 调整底部边距，确保不被14vw高度的footer遮盖 */
	}

	/****************** 订单区域 ******************/
	.order-section {
		margin-bottom: 6vw;
	}

	.section-title {
		display: flex;
		align-items: center;
		gap: 2vw;
		font-size: 4.5vw;
		font-weight: 600;
		color: #333;
		margin-bottom: 4vw;
		padding: 3vw 0;
	}

	.section-title i {
		color: #0097FF;
	}

	/****************** 订单列表 ******************/
	.order-list {
		display: flex;
		flex-direction: column;
		gap: 3vw;
	}

	.order-item {
		background: #fff;
		border-radius: 3vw;
		overflow: hidden;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	/****************** 订单头部 ******************/
	.order-header {
		padding: 4vw;
		display: flex;
		justify-content: space-between;
		align-items: center;
		cursor: pointer;
		transition: background-color 0.2s ease;
	}

	.order-header:hover {
		background-color: #f8f9fa;
	}

	.business-info {
		flex: 1;
	}

	.business-name {
		display: flex;
		align-items: center;
		gap: 2vw;
		font-size: 4.2vw;
		font-weight: 600;
		color: #333;
		margin-bottom: 1.5vw;
	}

	.business-name i {
		color: #0097FF;
	}

	.order-status {
		display: flex;
		align-items: center;
		gap: 1.5vw;
		font-size: 3.5vw;
		font-weight: 500;
		padding: 1.5vw 3vw;
		border-radius: 2vw;
	}

	.order-status.unpaid {
		background-color: #fff3cd;
		color: #856404;
	}

	.order-status.completed {
		background-color: #d4edda;
		color: #155724;
	}

	.order-summary {
		display: flex;
		flex-direction: column;
		align-items: flex-end;
		gap: 2vw;
	}

	.order-total {
		text-align: right;
	}

	.currency {
		font-size: 3.5vw;
		color: #666;
	}

	.amount {
		font-size: 5vw;
		font-weight: 700;
		color: #ff6b35;
	}

	.expand-btn {
		width: 6vw;
		height: 6vw;
		border-radius: 50%;
		background-color: #f8f9fa;
		display: flex;
		align-items: center;
		justify-content: center;
		transition: all 0.3s ease;
		color: #666;
	}

	.expand-btn.expanded {
		transform: rotate(180deg);
		background-color: #0097FF;
		color: #fff;
	}

	/****************** 订单详情 ******************/
	.order-details {
		background-color: #f8f9fa;
		padding: 4vw;
		border-top: 1px solid #e9ecef;
	}

	.food-items {
		margin-bottom: 4vw;
	}

	.food-item {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 2.5vw 0;
		border-bottom: 1px solid #e9ecef;
	}

	.food-item:last-child {
		border-bottom: none;
	}

	.food-info {
		display: flex;
		align-items: center;
		gap: 2vw;
	}

	.food-name {
		font-size: 3.8vw;
		font-weight: 500;
		color: #333;
	}

	.food-quantity {
		font-size: 3.2vw;
		color: #666;
	}

	.food-price {
		font-size: 3.8vw;
		font-weight: 600;
		color: #ff6b35;
	}

	.delivery-info {
		padding: 3vw 0;
		border-top: 1px solid #e9ecef;
	}

	.delivery-item {
		display: flex;
		justify-content: space-between;
		align-items: center;
		font-size: 3.8vw;
		font-weight: 500;
		color: #666;
	}

	/****************** 订单操作按钮 ******************/
	.order-actions {
		display: flex;
		gap: 3vw;
		margin-top: 4vw;
	}

	.btn {
		flex: 1;
		padding: 3vw 4vw;
		border: none;
		border-radius: 2.5vw;
		font-size: 3.8vw;
		font-weight: 500;
		cursor: pointer;
		transition: all 0.2s ease;
	}

	.btn-cancel {
		background-color: #f8f9fa;
		color: #666;
		border: 1px solid #e9ecef;
	}

	.btn-cancel:hover {
		background-color: #e9ecef;
	}

	.btn-pay {
		background-color: #ff6b35;
		color: #fff;
	}

	.btn-pay:hover {
		background-color: #e55a2b;
	}

	.btn-reorder {
		background-color: #0097FF;
		color: #fff;
	}

	.btn-reorder:hover {
		background-color: #007acc;
	}

	.btn-detail {
		background-color: #28a745;
		color: #fff;
	}

	.btn-detail:hover {
		background-color: #218838;
	}

	/****************** 响应式设计 ******************/
	@media (max-width: 768px) {
		.order-container {
			padding: 3vw;
			padding-bottom: 18vw; /* 移动端也保持足够的底部边距 */
		}
		
		.order-header {
			padding: 3vw;
		}
		
		.order-details {
			padding: 3vw;
		}
		
		.btn {
			padding: 2.5vw 3vw;
			font-size: 3.5vw;
		}
	}
</style>
