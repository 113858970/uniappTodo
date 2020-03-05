<template>
	<view class="content">
		<!-- 状态栏 -->
		<view v-if="list.length !== 0" class="todo-header">
			<!-- 状态栏的左侧 -->
			<view class="todo-header__left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<!-- 状态栏的右侧 -->
			<view class="todo-header__right">
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex === 0}" @click="tab(0)">全部</view>
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex === 1}" @click="tab(1)">待办</view>
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex === 2}" @click="tab(2)">已完成</view>
			</view>
		</view>
		<!-- 没有数据的状态 -->
		<view v-if="list.length === 0" class="default">
			<view class="image-default">
				<image src="../../static/default.png" mode="aspectFit"></image>
			</view>
			<view class="default-info">
				<view class="default-info__text">您还没有创建任何待办事项</view>
				<view class="default-info__text">点击下方+号创建一个吧</view>
			</view>
		</view>
		<!-- 内容 -->
		<view v-else class="todo-content">
			<!-- todo--finish -->
			<view class="todo-list" :class="{'todo--finish':item.checked}" v-for="(item,index) in listData" :key="index" @click="finish(item.id)">
				<view class="todo-list__checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list__content">
					{{item.content}}
				</view>
			</view>
		</view>
		<!-- 创建按钮 -->
		<view class="create-todo" @click="create">
			<text class="iconfont icon-jia" :class="{'create-todo-active':textShow}"></text>
		</view>
		<!-- 输入框 -->
		<view v-if="active" class="create-content" :class="{'create--show':textShow}">
			<view class="create-content-box">
				<!-- input 输入 -->
				<view class="create-input">
					<input type="text" v-model="value" placeholder="请输入您要创建的todo" />
				</view>
				<!-- 发布按钮 -->
				<view class="create-button" @click="add">
					创建
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				value: '',
				list: [],
				active: false,
				activeIndex: 0,
				text: '全部',
				textShow: false
			}
		},
		onLoad() {

		},
		computed: {
			listData() {
				let list = JSON.parse(JSON.stringify(this.list))
				let newList = []

				// 点击 全部
				if (this.activeIndex === 0) {
					this.text = '全部'
					return list
				}
				// 点击 待办事项
				if (this.activeIndex === 1) {
					// checked = false
					list.forEach((item) => {
						if (!item.checked) {
							newList.push(item)
						}
					})
					this.text = '待办'
					return newList
				}
				// 点击 已完成
				if (this.activeIndex === 2) {
					// checked = ture
					list.forEach((item) => {
						if (item.checked) {
							newList.push(item)
						}
					})
					this.text = '已完成'
					return newList
				}
				return []
			},
		},
		methods: {
			// 打开输入框
			create() {
				if (this.active) {
					this.close()
				} else {
					this.open()
				}
			},
			// 打开 动画
			open() {
				this.active = true
				this.$nextTick(() => {
					setTimeout(() => {
						this.textShow = true
					}, 50)
				})
			},
			// 关闭动画
			close() {
				this.textShow = false
				this.$nextTick(()=>{
					setTimeout(() => {
						this.active = false
					}, 350)
				})
			},
			// 发布
			add() {
				console.log(this.value);
				if (this.value === '') {
					uni.showToast({
						title: "请输入内容",
						icon: 'none'
					})
					return
				}
				this.list.unshift({
					id: 'id' + new Date().getTime(),
					content: this.value,
					checked: false
				})
				this.value = ''
				this.close()
			},
			// 点击列表触发
			finish(id) {
				let index = this.list.findIndex((item) => item.id === id)
				console.log('我被点击了', this.list[index]);
				this.list[index].checked = !this.list[index].checked
			},
			tab(index) {
				this.activeIndex = index
			}
		}
	}
</script>

<style>
	@import '../../common/icon.css';

	.todo-header {
		position: fixed;
		top: 0;
		left: 0;
		display: flex;
		align-items: center;
		padding: 0 15px;
		font-size: 12px;
		color: #333333;
		width: 100%;
		height: 45px;
		box-sizing: border-box;
		box-shadow: -1px 1px 5px 0 rgba(0, 0, 0, 0.1);
		background: #FFFFFF;
		z-index: 10;
	}

	.todo-header__left {
		width: 100%;
	}

	.active-text {
		font-size: 14px;
		color: #279abf;
		padding-right: 10px;

	}

	.todo-header__right {
		flex-shrink: 0;
		display: flex;
	}

	.todo-header__right-item {
		padding: 0 5px;
	}

	.active-tab {
		color: #279abf;
	}

	.todo-content {
		position: relative;
		padding-top: 50px;
		padding-bottom: 100px;
	}

	.todo-list {
		position: relative;
		display: flex;
		align-items: center;
		padding: 15px;
		margin: 15px;
		color: #0c3854;
		font-size: 14px;
		border-radius: 10px;
		background: #cfebfd;
		box-shadow: -1px 1px 5px 1px rgba(0, 0, 0, 0.1), -1px 2px 1px 0 rgba(255, 255, 255) inset;
		overflow: hidden;
	}

	.todo-list:after {
		position: absolute;
		content: '';
		top: 0;
		bottom: 0;
		left: 0;
		width: 5px;
		background: #91d1e8;
	}

	.todo-list__checkbox {
		padding-right: 15px;
	}

	.checkbox {
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background: #FFFFFF;
		box-shadow: 0 0 5px 1px rgba(0, 0, 0, 0.1);
	}

	.todo--finish .checkbox {
		position: relative;
		background: #eee;
	}

	.todo--finish .checkbox:after {
		content: '';
		position: absolute;
		width: 10px;
		height: 10px;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		border-radius: 50%;
		background: #CFEBFD;
		box-shadow: 0 0 2px 0px rgba(0, 0, 0, 0.2) inset;
	}

	.todo--finish .todo-list__content {
		color: #999999;
	}

	.todo--finish.todo-list:before {
		content: '';
		position: absolute;
		top: 0;
		bottom: 0;
		left: 40px;
		right: 10px;
		height: 2px;
		margin: auto 0;
		background: #bdcdd8;
	}

	.todo--finish.todo-list:after {
		background: #cccccc;
	}

	.create-todo {
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		bottom: 20px;
		left: 0;
		right: 0;
		margin: 0 auto;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255) inset;
	}

	.icon-jia {
		font-size: 30px;
		color: #add8e6;
	}

	.create-content {
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		transition: all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%)
	}

	.create--show {
		opacity: 1;
		transform: scale(1) translateY(0);
	}

	.create-content-box {
		display: flex;
		align-items: center;
		padding: 0 15px;
		padding-right: 0;
		border-radius: 50px;
		background: #DEEFF5;
		box-shadow: -1px 1px 5px 2px rgba(0, 0, 0, 0.1), -1px 1px 1px 0 rgba(255, 255, 255) inset;
		z-index: 2;
	}

	.create-input {
		width: 100%;
		padding-right: 15px;
		color: #add8e6;
	}

	.create-button {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		width: 80px;
		height: 50px;
		border-radius: 50px;
		font-size: 16px;
		color: #88d4ec;
		box-shadow: -2px 0px 2px 1px rgba(0, 0, 0, 0.1);
	}

	.create-content:after {
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -8px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
		box-shadow: 1px 1px 5px 2px rgba(0, 0, 0, 0.1);
		z-index: -1;
	}

	.create-content-box:after {
		content: '';
		position: absolute;
		right: 0;
		left: 0;
		bottom: -8px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
	}

	.default {
		padding-top: 100px;
	}

	.image-default {
		display: flex;
		justify-content: center;
	}

	.image-default image {
		width: 100%;
	}

	.default-info {
		text-align: center;
		font-size: 14px;
		color: #CCCCCC;
	}

	.icon-jia {
		transition: transform 0.3s;
	}

	.create-todo-active {
		transform: rotate(135deg);
	}
</style>
