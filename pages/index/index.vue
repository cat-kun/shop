<template>
  <view class="container">
    <uni-forms ref="form" :modelValue="formData" :rules="rules">
      <uni-forms-item label="货号" name="number">
        <uni-easyinput type="text" v-model="formData.number" placeholder="请输入货号DO2327-011" />
      </uni-forms-item>
      <uni-forms-item label="姓名" name="name">
        <uni-easyinput type="text" v-model="formData.name" placeholder="请输入姓名" />
      </uni-forms-item>
      <uni-forms-item label="身份证前后4位" name="idcard">
        <uni-easyinput type="idcard" v-model="formData.idcard" placeholder="请输入身份证前后4位" maxlength="8" />
      </uni-forms-item>
      <uni-forms-item label="码数" name="no">
        <uni-easyinput type="digit" v-model="formData.no" placeholder="请输入码数" />
      </uni-forms-item>
      <!-- <uni-forms-item label="邮箱" name="email">
        <input class="input" v-model="formData.email" type="text" placeholder="请输入用户名"
          @input="binddata('email',$event.detail.value)" />
      </uni-forms-item> -->
    </uni-forms>
    <!-- #ifdef H5 || MP-WEIXIN -->
    <a :href="smsBody" @click="submit" class="send">发送短信</a>
    <!-- #endif -->
    <!-- #ifdef APP || APP-PLUS-NVUE -->
    <button type="primary" @click="submit">发送短信</button>
    <!-- #endif -->
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 表单数据
        formData: {
          number: '',
          name: '' || uni.getStorageSync('name'),
          // email: 'dcloud@email.com',
          idcard: '' || uni.getStorageSync('idcard'),
          no: '',
        },
        smsBody: 'javascript:',
        phone: '13610064634', // 收件人手机号
        rules: {
          number: {
            rules: [{
              required: true,
              errorMessage: '请输入货号',
            }, ]
          },
          // 对name字段进行必填验证
          name: {
            rules: [{
                required: true,
                errorMessage: '请输入姓名',
              },
              // {
              //   minLength: 3,
              //   maxLength: 5,
              //   errorMessage: '姓名长度在 {minLength} 到 {maxLength} 个字符',
              // }
            ]
          },
          // 对email字段进行必填验证
          // email: {
          //   rules: [{
          //     format: 'email',
          //     errorMessage: '请输入正确的邮箱地址',
          //   }]
          // },
          idcard: {
            rules: [{
              required: true,
              errorMessage: '请输入身份证前后4位',
            }, ]
          },
          no: {
            rules: [{
              required: true,
              errorMessage: '请输入码数',
            }, ]
          },
        }
      }
    },
    methods: {
      // 触发提交表单
      submit() {
        this.$refs.form.validate().then(res => {
          console.log('表单数据信息：', res, this.phone);
          // 姓名身份证储存在本地
          const {number, name, idcard, no} = this.formData;
          uni.setStorageSync('name', name);
          uni.setStorageSync('idcard', idcard);
          
          // #ifdef APP-PLUS || APP-PLUS-NVUE
          // 发送短信
          let msg = plus.messaging.createMessage(plus.messaging.TYPE_SMS);
          
          msg.to = [this.phone];
          
          msg.body = `${number}+${name}+${idcard}+${no}`;
          
          plus.messaging.sendMessage(msg);
          // #endif
          
          // #ifdef H5
          // 判断ua
          const ua = window.navigator.userAgent;
          const isAndroid = ua.indexOf('Android') > -1;
          const isIOS = !!ua.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
          if(isIOS) {
            this.smsBody = `sms:${this.phone}&body=${number}+${name}+${idcard}+${no}`;
          } else {
            this.smsBody = `sms:${this.phone}?body=${number}+${name}+${idcard}+${no}`;
          }
          
          // #endif
        }).catch(err => {
          console.log('表单错误信息：', err);
        })
      }
    }
  }
</script>

<style>
  .container {
    padding: 20px;
    font-size: 14px;
    line-height: 24px;
  }
  .send {
    width: 100%;
    height: 88rpx;
    border-radius: 15rpx;
    background: #333;
    color: #fff;
    text-align: center;
    line-height: 88rpx;
    font-size: 34rpx;
    display: block;
  }
</style>
