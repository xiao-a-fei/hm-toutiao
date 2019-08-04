<template>
  <div class="container">
    <el-card class="my-card">
      <img src="../../assets/images/logo_index.png" alt />
      <!-- rules属性的校验也应该在data函数中提体现 -->
      <el-form ref="loginForm" :model="loginForm" :rules="loginRules">
          <!-- 单项的prop属性的值，需要与数据的字段名保持一致 -->
        <el-form-item prop="mobile">
            <!-- 这个输入框绑定的手机号信息， -->
          <el-input v-model="loginForm.mobile" placeholder="请输入手机号"></el-input>
        </el-form-item>
        <el-form-item prop="code">
            <!-- 这个输入框绑定的验证码信息， -->
          <el-input v-model="loginForm.code" placeholder="请输入验证码"></el-input>
        </el-form-item>
        <el-form-item>
            <!-- 复选框按钮，默认勾选 -->
          <el-checkbox :value="true">我已阅读并同意用户协议和隐私条款</el-checkbox>
        </el-form-item>
        <el-form-item>
            <!-- 登录按钮 -->
          <el-button  type="primary" style="width:100%" @click="login()">登 录</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    // 自定义检验函数，先声明在使用，必须写在return之前
    // rule规则对象 value字段的值
    // callback检验结束后的回调函数 检验失败 callback(new Error('错误信息')) 检验成功直接调用callback()
    const checkMobile = (rule, value, callback) => {
      if (!/^1[3-9]\d{9}$/.test(value)) return callback(new Error('手机格式不对'))
      callback()
    }
    return {
      loginForm: {
        // 该数据信息将来需提交给后端，所以该字段信息参考接口文档
        mobile: '',
        code: ''
      },
      loginRules: {
        //   检测手机号信息和密码信息，必填项，如果为空提示message，在失去焦点的时候校验，这些都是element-ui提供的
        mobile: [
          { required: true, message: '请输入手机号', triggle: 'blur' },
          // 这些事自定义校验
          { validator: checkMobile, triggle: 'blur' }
        ],
        code: [{ required: true, message: '请输入验证码', triggle: 'blur' }]
      }
    }
  },
  methods: {
    login () {
      // 对整个表单进行校验
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          // console.log('success')
          this.$http
            .post('http://ttapi.research.itcast.cn/mp/v1_0/authorizations', this.loginForm)
            .then(res => {
              console.log(res.data)
              this.$router.push('/')
            })
            .catch(() => {
              this.$message.error('手机号或验证码错误')
            })
        }
      })
    }
  }
}
</script>

<style scoped lang="less">
.container {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
  /* cover 背景图尺寸 cover 等比缩放铺满容器多余被裁剪
        contian  等比缩放完全显示在当前容器可能存在留白
     */
  background: url(../../assets/images/login_bg.jpg) no-repeat center / cover;
  .my-card {
    width: 400px;
    height: 350px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    img {
      display: block;
      width: 200px;
      margin: 0 auto 30px;
    }
  }
}
</style>
