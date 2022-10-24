<template>
	<view class="todo-box">
		<view class="todo-header">
			<text>收集箱</text>
			<uni-icons type="bars" size="24"></uni-icons>
		</view>
		<view>
			<unicloud-db v-slot:default="{data, loading, error, options}" collection="todos" >
			  <view v-for="(item, index) in data" :key="index" class="todo-list">
				  <checkbox :checked="item.done"></checkbox>
				  <view>
				    {{item.inputValue}}
				  </view>
			  </view>
			</unicloud-db>
		</view>
		<view class="todo-footer">
			<uni-easyinput
				type="text"
				placeholder="请输入你的任务名称，按回车键确认"
				suffixIcon="paperplane"
				@input="onInput"
				@iconClick="addTodo"
				:value="inputValue"
			></uni-easyinput>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				inputValue: ""
			}
		},
		methods: {	
			onInput(e) {
				this.inputValue = e.detail.value;
				console.log(this.inputValue)
			},
			addTodo(inputValue) {
				// console.log(event.target.value)
				// 校验数据
				if (!this.inputValue.trim()) 
				return uni.showToast({
					title: '输入不能为空'
				})
				// 将用户输入包装成一个todoObj对象
				const todoObj = {
				  inputValue: this.inputValue,
				  done: false
				}
				// 插入1条数据，同时判断成功失败状态
				const db = uniCloud.database();
				db.collection("todos")
					.add(todoObj)
					.then((res) => {
						uni.showToast({
							title: '新增成功'
						})
					})
					.catch((err) => {
						uni.showModal({
							content: err.message || '新增失败',
							showCancel: false
						})
					})
					.finally(() => {
						this.inputValue = ''
					})

				// e.target.value = ''
			},
			clearAllTodo() {
				this.todos = this.todos.filter((todo) => {
					return !todo.done
				})
			}
		}
	}
</script>

<style>
	.todo-box{
		/* display: flex;
		flex-direction: column;
		justify-content: space-between; */
	}
	.todo-header{
		display: flex;
		justify-content: space-between;
	}
	.todo-list{
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		height: 80rpx;
	}
	.todo-footer{
		display: flex;
		position:fixed;
		width: 100%;
		bottom:0;
	}
	
</style>
