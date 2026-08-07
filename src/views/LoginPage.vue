<template>
  <div class="login-page">
    <!-- 左侧背景区域 -->
    <div class="left-section">
      <div class="background-container"></div>

      <!-- 底部版权 -->
      <div class="copyright">
        {{ new Date().getFullYear() }} © 中国建筑第三工程局工程总承包公司
      </div>
    </div>

    <!-- 右侧登录表单区域 -->
    <div class="right-section">
      <div class="login-form-container">
        <!-- 标题 -->
        <div class="form-header">
          <h2 class="form-title">欢迎登录</h2>
          <p class="form-subtitle">中建 AI 机电智能设计平台</p>
        </div>

        <!-- 登录方式切换 -->
        <div class="login-type-switch">
          <div 
            class="switch-button" 
            :class="{ active: loginType === 'password' }"
            @click="loginType = 'password'"
          >
            密码登录
          </div>
          <div 
            class="switch-button" 
            :class="{ active: loginType === 'sms' }"
            @click="loginType = 'sms'"
          >
            验证码登录
          </div>
        </div>

        <!-- 登录表单 -->
        <el-form ref="formRef" :model="form" class="login-form">
          <!-- 密码登录表单 -->
          <template v-if="loginType === 'password'">
            <!-- 账号/手机号 -->
            <el-form-item>
              <el-input
                v-model="form.username"
                placeholder="请输入账号或手机号"
                size="large"
                :maxlength="20"
              >
                <template #prefix>
                  <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="#c9cdd4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <circle cx="12" cy="8" r="4"/>
                    <path d="M4 21v-2a4 4 0 014-4h8a4 4 0 014 4v2"/>
                  </svg>
                </template>
              </el-input>
            </el-form-item>

            <!-- 密码 -->
            <el-form-item>
              <el-input
                v-model="form.password"
                placeholder="请输入密码"
                size="large"
                :type="showPassword ? 'text' : 'password'"
              >
                <template #prefix>
                  <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="#c9cdd4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect x="3" y="11" width="18" height="11" rx="2"/>
                    <path d="M7 11V7a5 5 0 0110 0v4"/>
                  </svg>
                </template>
                <template #suffix>
                  <el-icon class="toggle-password" @click="showPassword = !showPassword">
                    <template v-if="showPassword">
                      <View />
                    </template>
                    <template v-else>
                      <Hide />
                    </template>
                  </el-icon>
                </template>
              </el-input>
            </el-form-item>
          </template>

          <!-- 短信登录表单 -->
          <template v-else>
            <!-- 手机号 -->
            <el-form-item>
              <el-input
                v-model="form.phone"
                placeholder="请输入手机号"
                size="large"
                :maxlength="11"
              >
                <template #prefix>
                  <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="#c9cdd4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect x="5" y="2" width="14" height="20" rx="2"/>
                    <line x1="12" y1="18" x2="12" y2="18.01"/>
                  </svg>
                </template>
              </el-input>
            </el-form-item>

            <!-- 验证码 -->
            <el-form-item>
              <el-input
                v-model="form.captcha"
                placeholder="请输入验证码"
                size="large"
                :maxlength="6"
                style="width: calc(100% - 110px)"
              >
                <template #prefix>
                  <svg class="input-icon" viewBox="0 0 24 24" fill="none" stroke="#c9cdd4" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <rect x="3" y="5" width="18" height="14" rx="2"/>
                    <line x1="7" y1="9" x2="7" y2="9.01"/>
                    <line x1="11" y1="9" x2="11" y2="9.01"/>
                    <line x1="15" y1="9" x2="15" y2="9.01"/>
                    <line x1="7" y1="13" x2="7" y2="13.01"/>
                    <line x1="11" y1="13" x2="11" y2="13.01"/>
                    <line x1="15" y1="13" x2="15" y2="13.01"/>
                  </svg>
                </template>
              </el-input>
              <el-button 
                class="captcha-btn"
                :disabled="captchaCooldown > 0"
                @click="sendCaptcha"
              >
                {{ captchaCooldown > 0 ? `${captchaCooldown}s` : '获取验证码' }}
              </el-button>
            </el-form-item>
          </template>

          <!-- 记住登录状态 & 忘记密码 -->
          <div class="form-options">
            <el-checkbox v-model="form.remember">
              记住登录状态
            </el-checkbox>
            <el-link class="forgot-link" :underline="false">忘记密码？</el-link>
          </div>

          <!-- 登录按钮 -->
          <el-button 
            type="primary" 
            size="large" 
            class="login-btn"
            :loading="loading"
            @click="handleLogin"
          >
            登 录
          </el-button>
        </el-form>

        <!-- 用户协议 -->
        <div class="agreement">
          登录即代表同意
          <el-link class="agreement-link" :underline="false">《用户协议》</el-link>
          与
          <el-link class="agreement-link" :underline="false">《隐私政策》</el-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { View, Hide } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

const emit = defineEmits(['login-success'])

// 登录方式
const loginType = ref('password')

// 表单数据
const form = reactive({
  username: '',
  password: '',
  phone: '',
  captcha: '',
  remember: false
})

// 密码显示/隐藏
const showPassword = ref(false)

// 登录加载状态
const loading = ref(false)

// 验证码倒计时
const captchaCooldown = ref(0)

// 发送验证码
const sendCaptcha = () => {
  if (captchaCooldown.value > 0) return
  
  // 模拟发送验证码
  captchaCooldown.value = 60
  const timer = setInterval(() => {
    captchaCooldown.value--
    if (captchaCooldown.value <= 0) {
      clearInterval(timer)
    }
  }, 1000)
}

// 登录处理
const handleLogin = async () => {
  // 密码登录校验
  if (loginType.value === 'password') {
    if (!form.username.trim()) {
      ElMessage.warning('请输入账号或手机号')
      return
    }
    if (!form.password) {
      ElMessage.warning('请输入密码')
      return
    }
  } else {
    // 短信登录校验
    if (!form.phone.trim()) {
      ElMessage.warning('请输入手机号')
      return
    }
    if (!form.captcha.trim()) {
      ElMessage.warning('请输入验证码')
      return
    }
  }
  
  loading.value = true
  
  // 模拟登录
  setTimeout(() => {
    loading.value = false
    ElMessage.success('登录成功')
    emit('login-success')
  }, 1000)
}
</script>

<style scoped>
.login-page {
  display: flex;
  width: 100vw;
  height: 100vh;
  background: #f2f3f5;
}

.left-section {
  flex: 1;
  position: relative;
  min-width: 800px;
  overflow: hidden;
}

.background-container {
  position: absolute;
  inset: 0;
  background-image: url('/登陆banner.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
}


.copyright {
  position: absolute;
  bottom: 52px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 16px;
  color: #97a3b4;
  white-space: nowrap;
}

.right-section {
  width: 480px;
  background: white;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: -10px 0 15px rgba(0, 0, 0, 0.02);
}

.login-form-container {
  width: 360px;
}

.form-header {
  margin-bottom: 35px;
}

.form-title {
  font-size: 32px;
  font-weight: 600;
  color: #1d2129;
  margin: 0 0 7px;
  letter-spacing: -0.8px;
}

.form-subtitle {
  font-size: 14px;
  color: #86909c;
  margin: 0;
}

.login-type-switch {
  display: flex;
  background: #f5f7fa;
  border-radius: 8px;
  padding: 3.5px;
  margin-bottom: 28px;
}

.switch-button {
  flex: 1;
  text-align: center;
  padding: 7px;
  border-radius: 6px;
  font-size: 14px;
  color: #86909c;
  cursor: pointer;
  transition: all 0.2s;
}

.switch-button.active {
  background: white;
  color: #409eff;
  font-weight: 600;
  box-shadow: 0 1px 1.5px rgba(0, 0, 0, 0.1), 0 1px 1px rgba(0, 0, 0, 0.1);
}

.login-form {
  margin-top: 0;
}

.login-form :deep(.el-form-item) {
  margin-bottom: 17.5px;
}

.login-form :deep(.el-input__wrapper) {
  border-radius: 4px;
}

.login-form :deep(.el-input__wrapper) {
  border: 1.144px solid #dcdfe6;
}

.login-form :deep(.el-input__wrapper:hover) {
  border-color: #409eff;
}

.login-form :deep(.el-input__wrapper.is-focus) {
  border-color: #409eff;
  box-shadow: 0 0 0 0 rgba(64, 158, 255, 0.2);
}

.login-form :deep(.el-input__prefix) {
  display: flex;
  align-items: center;
}

.input-icon {
  width: 20px;
  height: 20px;
  color: #c9cdd4;
  margin-right: 8px;
}

.toggle-password {
  width: 16px;
  height: 16px;
  color: #c9cdd4;
  cursor: pointer;
  transition: color 0.2s;
}

.toggle-password:hover {
  color: #409eff;
}

.captcha-btn {
  width: 100px;
  margin-left: 10px;
  height: 38px;
}

.form-options {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 17.5px 0;
}

.form-options :deep(.el-checkbox__label) {
  color: #4e5969;
}

.forgot-link {
  color: #409eff;
  font-size: 12px;
}

.login-btn {
  width: 100%;
  height: 38.487px;
  border-radius: 4px;
  font-size: 14px;
  font-weight: 500;
  margin-top: 21px;
  background: #409eff;
  border-color: #409eff;
  box-shadow: 0 4px 5px rgba(64, 158, 255, 0.2);
}

.login-btn:hover {
  background: #66b1ff;
  border-color: #66b1ff;
}

.agreement {
  text-align: center;
  margin-top: 35px;
  font-size: 12px;
  color: #c9cdd4;
  line-height: 16px;
}

.agreement-link {
  color: #86909c;
}

.agreement-link:hover {
  color: #409eff;
}
</style>
