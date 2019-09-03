<template>
	<view class="content">
    <view class="logo"><image src="/static/logo.png"></image></view>
    <view class="uni-padding-wrap uni-common-mt ">
      <view class="uni-title uni-common-mt">
            视频列表
      </view>
      <view class="uni-flex uni-column text">
          <view class="uni-flex-item  flex-item-V bg-color">
            <view>
              <video class="video-view" src="http://ykugc.cp31.ott.cibntv.net/6572F074FC94671CA6E41268D/03000A01005B45F11AD0E9E58E704F794E6CFF-1377-4C88-94D9-E3C0EA6C0CB9.mp4?ccode=050F&duration=75&expire=18000&psid=654ee23d4626985768a506998fd98e4e&ups_client_netip=7256a5a3&ups_ts=1567498110&ups_userid=&utid=YJ9yFTiwM20CAVnQ%2F07E1603&vid=XMzcxNzUzNDM4OA%3D%3D&vkey=A6c62db55717eeb90f3748d3de6d70b0d&sp=&bc=2"></video>
              <span>1.88</span>
              <button size="mini" @click="test">购买</button>
            </view>
          </view>
          <view class="uni-flex-item  flex-item-V bg-color">
            <video class="video-view" src="http://vali.cp31.ott.cibntv.net/6774CB2A5933A717AB4365A23/03001101005B7B8B4D482FE490C6D4F7EA25BB-3325-459C-A913-49AEE001A711.mp4?ccode=050F&duration=236&expire=18000&psid=d31a5b0aa7c91563947b8770f0191b06&ups_client_netip=7256a5e7&ups_ts=1567499499&ups_userid=&utid=YJ9yFTiwM20CAVnQ%2F07E1603&vid=XMzc5MDg4NDg1Mg%3D%3D&vkey=A6400f1794f1e59d244990ff1292eee7d&s=7c461aabe9cd4c538b66&sp=&bc=2"></video>
            <span>19.8</span>
            <button size="mini" @click="test">购买</button>
          </view>
          <view class="uni-flex-item  flex-item-V bg-color">
            <video class="video-view" src="http://ykugc.cp31.ott.cibntv.net/6573D68858D36716020D4245C/03001101005A090CCA65304564D10DE3FCC21A-DDA1-BF33-B7D5-E6473768028F.mp4?ccode=050F&duration=114&expire=18000&psid=e66c575eb0007550218a9795da912e1d&ups_client_netip=7256a5e7&ups_ts=1567499526&ups_userid=&utid=YJ9yFTiwM20CAVnQ%2F07E1603&vid=XMzE1NDIzNjI0MA%3D%3D&vkey=A6a0c8db31312f5b694ccaf61c3c173ab&sp=&bc=2"></video>
            <span>19.8</span>
            <button size="mini" @click="test">购买</button>
          </view>
      </view>
    </view>
		<view>
			<text class="title">{{title}}123</text>
      <button size="mini" @click="test">测试</button>
		</view>
	</view>
</template>

<script>
import { Pay } from '../pay/index'
	export default {
		data() {
			return {
        title: 'Hello',
        tips: {
          1: '支付成功!',
          2: '支付取消!',
          3: '支付失败!'
        }
			}
		},
		onLoad() {
      //点击支付按钮进行支付
 7  
      
		},
		methods: {
      test() {
        // Pay()
        console.log('提交数据')
        this.payclick()
      },
      payclick() {
          var t = this;
          wx.login({
            //获取code换取openID
            success: function (res) {
              //code = res.code //返回code
              console.log("获取code");
              console.log(res.code);
              var opid = t.getOpenId(res.code);
            }
          })
      },
      getOpenId: function (code) {
       var that = this;
       wx.request({
         url: "https://api.weixin.qq.com/sns/jscode2session?appid=wxf8bfbe307b531a41&secret=eef865a12157bf9432b8199f82597a10&js_code=" + code + "&grant_type=authorization_code",
         data: {},
         method: 'GET',
         success: function (res) {
           console.log("获取openid")
           console.log(res)
          //  that.setData({
          //    openid: res.data.openid,
          //    session_key: res.data.session_key
          //  })
           that.generateOrder(res.data.openid)
         },
         fail: function () {
           // fail
         },
         complete: function () {
           // complete
         }
       })
     },
      //生成商户订单
    generateOrder (openid) {
     var that = this
     wx.request({
       url: 'http://192.168.254.60:3000/api/generateOrder',//后台获取相关参数
       method: 'GET',
       data: {
         gfee: '1',
         gname: '颜值报告',
         openId: openid
         //（商品价钱和商品名称根据自身需要是否传值, openid为必传）
       },
       success: function (res) {
         console.log("后台获取数据成功");
         console.log(res.data);
         var param = { "timeStamp": res.data.timeStamp, "package": res.data.package, "paySign": res.data.paySign, "signType": "MD5", "nonceStr": res.data.nonceStr };
          //发起支付
         that.pay(param);
       },
       fail: function (res) {
         console.log("向后台发送数据失败")
       }
     })
   },
    pay: function (param) {
       var that = this;
       console.log("发起支付")
       wx.requestPayment({
         timeStamp: param.timeStamp,
         nonceStr: param.nonceStr,
         package: param.package,
         signType: param.signType,
         paySign: param.paySign,
         success: function (res) {
           console.log("success");
           console.log(res);
           wx.showToast({
            title:  that.tips[1],
            icon: 'none',
            duration: 2000
          })
         },
         fail: function (res) {
           wx.showToast({
            title:  that.tips[2],
            icon: 'none',
            duration: 2000
          })
           console.log("fail")
           console.log(res);
         },
         complete: function (res) {
           console.log("complete");
           console.log(res)
          //  wx.showToast({
          //   title:  that.tips[3],
          //   icon: 'none',
          //   duration: 2000
          // })
         }
       })
     }
		}
	}
</script>

<style>
	.content {
		height: 400upx;
	}

	.logo {
    width: 100%;
    text-align: center;
	}
  .logo image{
    width: 400upx;
    height: 84upx;
  }
  .text span{
    font-size: 45upx;
    color: #007AFF;
  }
  .text button{
    float: right
  }
  .bg-color{
    background-color: #f5f5f5;
  }
	.title {
		font-size: 36upx;
		color: #8f8f94;
  }
  .video-view{
    width: 100%;
  }
</style>
