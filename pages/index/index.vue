<template>
  <view class="container">
    <div v-if="type == 1">
      <uni-forms ref="form" :modelValue="formData" :rules="rules">
        <uni-forms-item label="货号" name="number" required>
          <uni-easyinput type="text" v-model="formData.number" placeholder="请输入货号DO2327-011" />
        </uni-forms-item>
        <uni-forms-item label="姓名" name="name" required>
          <uni-easyinput type="text" v-model="formData.name" placeholder="请输入姓名" />
        </uni-forms-item>
        <uni-forms-item label="身份证前后4位" name="idcard" required>
          <uni-easyinput type="idcard" v-model="formData.idcard" placeholder="请输入身份证前后4位" maxlength="8" />
        </uni-forms-item>
        <uni-forms-item label="码数" name="no" required>
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
    </div>
    <div v-if="type == 2">
      <h3 class="title">BAPE抽签复制小工具</h3>
      <uni-forms ref="bapeForm" :modelValue="bapeData" :rules="bepeRules">
        <uni-forms-item label="地区" name="area" required>
          <uni-data-checkbox v-model="bapeData.value" :localdata="bapeData.range" @change="change"></uni-data-checkbox>
        </uni-forms-item>
        <uni-forms-item label="姓名" name="name" required>
          <uni-easyinput type="text" v-model="bapeData.name" placeholder="请输入姓名" />
        </uni-forms-item>
        <uni-forms-item label="货号" name="no" required>
          <uni-easyinput type="text" v-model="bapeData.no" placeholder="请输入货号" />
        </uni-forms-item>
        <uni-forms-item label="身份证" name="idcard" required>
          <uni-easyinput type="idcard" v-model="bapeData.idcard" placeholder="请输入身份证" maxlength="18" />
        </uni-forms-item>
        <uni-forms-item label="信用卡" name="bankNo" required>
          <uni-easyinput type="text" v-model="bapeData.bankNo" placeholder="请输入信用卡" />
        </uni-forms-item>
        <uni-forms-item label="手机号" name="phone" required>
          <uni-easyinput type="number" v-model="bapeData.phone" placeholder="请输入手机号" maxlength="11" />
        </uni-forms-item>
      </uni-forms>
      <button type="primary" @click="copy">复制</button>
    </div>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        type: 1,
        // 发送短信表单数据
        formData: {
          number: '',
          name: '' || uni.getStorageSync('name'),
          // email: 'dcloud@email.com',
          idcard: '' || uni.getStorageSync('idcard'),
          no: '',
        },
        // BAPE表单数据
        bapeData: {
          value: 'A',
          range: [
            {"value": 'A', "text": "北京"},
            {"value": 'B', "text": "上海"},
            {"value": 'D', "text": "广州"},
            {"value": 'E', "text": "成都"},
          ],
          no: 2,
          name: '' || uni.getStorageSync('name'),
          idcard: '' || uni.getStorageSync('idcard'),
          bankNo: '' || uni.getStorageSync('bankNo'),
          phone: '' || uni.getStorageSync('phone'),
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
        },
        // 
        bepeRules: {
          area: {
            rules: [{
              required: true,
              errorMessage: '请选择地区',
            }, ]
          },
          no: {
            rules: [{
                required: true,
                errorMessage: '请输入货号',
              }
            ]
          },name: {
            rules: [{
                required: true,
                errorMessage: '请输入姓名',
              }
            ]
          },
          idcard: {
            rules: [{
                required: true,
                errorMessage: '请输入身份证',
              }
            ]
          },
          bankNo: {
            rules: [{
                required: true,
                errorMessage: '请输入信用卡',
              }
            ]
          },
          phone: {
            rules: [{
                required: true,
                errorMessage: '请输入手机号',
              }
            ]
          },
        }
      }
    },
    created() {
      console.log(this.$route.query);
      const { type } = this.$route.query;
      if(!type || type != 2) {
        this.type = 1;
      } else {
        this.type = 2;
        document.title = 'BAPE抽签复制小工具';
      }
      console.log('type', type)
    },
    methods: {
      // 复制格式
      copy() {
        this.$refs.bapeForm.validate().then(res => {
          console.log('表单数据信息：', res);
          const { value, no, name, idcard, bankNo, phone} = this.bapeData;
          uni.setClipboardData({
            data: `${value}/${no}/${name}/${idcard}/${bankNo}/${phone}`,
            success: function () {
              console.log('已复制到剪贴板');
            }
          });
          // 保存到本地存储
          uni.setStorageSync('name', name);
          uni.setStorageSync('idcard', idcard);
          uni.setStorageSync('bankNo', bankNo);
          uni.setStorageSync('phone', phone);
        }).catch(err => {
          console.log('表单错误信息：', err);
        })
      },
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
      },
      change(e){
        console.log('e:',e);
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
  .label-text {
    width: 200rpx !important;
  }
  .title {
    text-align: center;
    padding: 20px 0;
    
  }
</style>
