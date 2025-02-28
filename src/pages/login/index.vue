<template>
  <view class="login-container">
    <view class="login-box">
      <image class="logo" src="/static/logo.png"></image>
      <view class="input-group">
        <input
          class="input-item"
          type="text"
          v-model="username"
          placeholder="请输入用户名"
        />
        <input
          class="input-item"
          type="password"
          v-model="password"
          placeholder="请输入密码"
          password
        />
      </view>
      <button class="login-btn" @click="handleLogin">登录</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    async handleLogin() {
      if (!this.username || !this.password) {
        uni.showToast({
          title: '请输入用户名和密码',
          icon: 'none'
        })
        return
      }

      try {
        // 这里替换为实际的登录API调用
        const response = await uni.request({
          url: 'YOUR_LOGIN_API_URL',
          method: 'POST',
          data: {
            username: this.username,
            password: this.password
          }
        })

        if (response.data.success) {
          // 存储token或用户信息
          uni.setStorageSync('token', response.data.token)
          uni.setStorageSync('userInfo', response.data.userInfo)

          // 跳转到主页
          uni.reLaunch({
            url: '/pages/index/index'
          })
        } else {
          uni.showToast({
            title: response.data.message || '登录失败',
            icon: 'none'
          })
        }
      } catch (error) {
        uni.showToast({
          title: '登录失败，请稍后重试',
          icon: 'none'
        })
      }
    }
  }
}
</script>

<style>
.login-container {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f8f8f8;
}

.login-box {
  width: 80%;
  padding: 40rpx;
  background-color: #fff;
  border-radius: 20rpx;
  box-shadow: 0 2rpx 12rpx 0 rgba(0, 0, 0, 0.1);
}

.logo {
  width: 200rpx;
  height: 200rpx;
  margin: 0 auto 60rpx;
  display: block;
}

.input-group {
  margin-bottom: 40rpx;
}

.input-item {
  width: 100%;
  height: 90rpx;
  padding: 0 20rpx;
  margin-bottom: 20rpx;
  border: 2rpx solid #dcdfe6;
  border-radius: 8rpx;
  box-sizing: border-box;
}

.login-btn {
  width: 100%;
  height: 90rpx;
  line-height: 90rpx;
  background-color: #007aff;
  color: #fff;
  border-radius: 8rpx;
  font-size: 32rpx;
}

.login-btn:active {
  opacity: 0.8;
}
</style>