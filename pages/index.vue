<template>
  <view class="container">
    <view class="header">
      <text class="title">🎉 欢迎使用uni-app</text>
      <text class="subtitle">请输入您的名字</text>
    </view>
    
    <view class="input-section">
      <input 
        v-model="name" 
        class="input" 
        placeholder="例如：张三"
        @input="clearMessage"
      />
      <button 
        @click="sayHello" 
        class="btn-primary"
        :disabled="!name.trim()"
      >
        打招呼 👋
      </button>
    </view>
    
    <view class="result-section" v-if="greeting">
      <view class="greeting-card">
        <text class="greeting">{{ greeting }}</text>
        <text class="emoji">😊</text>
      </view>
    </view>
    
    <view class="tips" v-if="!greeting">
      <text class="tip-text">💡 输入您的名字，我来和您打招呼吧！</text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      name: '',
      greeting: ''
    }
  },
  methods: {
    sayHello() {
      if (this.name.trim()) {
        this.greeting = `你好，${this.name}！欢迎使用uni-app！`
      } else {
        uni.showToast({
          title: '请输入您的名字',
          icon: 'none'
        })
      }
    },
    clearMessage() {
      if (this.greeting) {
        this.greeting = ''
      }
    }
  },
  onLoad() {
    console.log('页面加载完成')
  }
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 60rpx 40rpx;
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.header {
  text-align: center;
  margin-bottom: 80rpx;
}

.title {
  font-size: 48rpx;
  font-weight: bold;
  color: #ffffff;
  margin-bottom: 20rpx;
  display: block;
}

.subtitle {
  font-size: 32rpx;
  color: #f0f0f0;
  display: block;
}

.input-section {
  width: 100%;
  max-width: 600rpx;
  margin-bottom: 60rpx;
}

.input {
  width: 100%;
  height: 100rpx;
  background-color: #ffffff;
  border: 2px solid #e0e0e0;
  border-radius: 20rpx;
  padding: 0 30rpx;
  font-size: 32rpx;
  margin-bottom: 40rpx;
  box-shadow: 0 4rpx 12rpx rgba(0,0,0,0.1);
  transition: border-color 0.3s ease;
}

.input:focus {
  border-color: #007AFF;
  outline: none;
}

.btn-primary {
  width: 100%;
  height: 100rpx;
  background: linear-gradient(45deg, #007AFF, #5ac8fa);
  color: white;
  border: none;
  border-radius: 20rpx;
  font-size: 36rpx;
  font-weight: bold;
  box-shadow: 0 6rpx 20rpx rgba(0,122,255,0.3);
  transition: all 0.3s ease;
}

.btn-primary:disabled {
  background: #cccccc;
  box-shadow: none;
}

.btn-primary:not(:disabled):active {
  transform: translateY(2rpx);
  box-shadow: 0 4rpx 15rpx rgba(0,122,255,0.4);
}

.result-section {
  width: 100%;
  max-width: 600rpx;
}

.greeting-card {
  background-color: #ffffff;
  border-radius: 20rpx;
  padding: 40rpx;
  text-align: center;
  box-shadow: 0 8rpx 25rpx rgba(0,0,0,0.15);
  position: relative;
  overflow: hidden;
}

.greeting-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 8rpx;
  background: linear-gradient(90deg, #007AFF, #5ac8fa, #34c759);
}

.greeting {
  font-size: 40rpx;
  color: #333333;
  font-weight: bold;
  margin-bottom: 20rpx;
  display: block;
}

.emoji {
  font-size: 60rpx;
  display: block;
}

.tips {
  margin-top: 40rpx;
  padding: 30rpx;
  background-color: rgba(255,255,255,0.1);
  border-radius: 15rpx;
  backdrop-filter: blur(10rpx);
}

.tip-text {
  font-size: 28rpx;
  color: #f0f0f0;
  text-align: center;
}

/* 添加一些动画效果 */
.greeting-card {
  animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30rpx);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style> 