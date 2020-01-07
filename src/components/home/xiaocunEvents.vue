<template>
<!-- 小村家事 可上传图片-->
  <div style="height:100%;">
    <ViewBox ref="viewBox" body-padding-bottom="0">
       <x-header>小村家事
         <x-icon slot="right" type="plus" size="35" style="fill:#fff;position:relative;top:-8px;"  @click="openwindowshow.showMenus = true"></x-icon>
       </x-header>
       <div style="top:46px;">
         <pull-to :top-load-method="refresh" @infinite-scroll="loadmore" :top-config="{stayDistance:90}"  @scroll="onScroll" >
        <img :src="img" style="width:100%;height:150px;">
        <div>
          <panel :list="list" type="5"   @on-click-item="openpanel"></panel>
          <!-- <load-more tip="正在加载"></load-more> -->
        </div>
        <div class="loading-bar">
          <load-more tip="正在加载"></load-more>
        </div>
      </pull-to>
       </div>
    </ViewBox>

    <div v-transfer-dom>
      <actionsheet :menus="menus" v-model="openwindowshow.showMenus" @on-click-menu="click" show-cancel></actionsheet>
    </div>

    <div v-transfer-dom>
      <popup v-model="show" position="right" width="100%">
        <div style="margin-top:-15px;">
            <Group>
               <flexbox>
                 <flexbox-item>
                  <x-button style="margin-left:5px;" @click.native="backpage"  type="warn">取消</x-button>
                </flexbox-item>
                <flexbox-item style="margin-right:5px;">
                  <x-button @click.native="pushevents" type="primary" v-on:click="pushevents">发表</x-button>
                </flexbox-item>
              </flexbox>
              <x-input v-model="xiaocun.eventsTitle" placeholder="标题"></x-input>
              <x-textarea :max="300" name="description" :height="160" :rows="8" :cols="30" v-model="xiaocun.eventsText" placeholder="开始输入您的小村家事吧！"></x-textarea>
              <div>
                <!-- 图片上传 -->
              <addimg /> 
              </div>
            </Group>
          <toast v-model="showtoast" type="text" :time="800" width="100%" is-show-mask position="top">{{showeventstoasttext}}</toast>
        </div>
      </popup>
    </div>
    
    <div v-transfer-dom>
      <popup v-model="showPanel" position="right" width="100%">
        <div>
          <x-header class="vux-scroller-header" :left-options="{preventGoBack: true}" @on-click-back="backpagepop"></x-header>
           <div> 
             <ul id="comments_list">
              <li class="comments">
                <div class="com_top" style="padding:5px;">
                  <div style="text-align:center;font-weight:bold;font-size:18px;margin:10px 0;">
                    <h3 class="title">{{popcontent.title}}</h3>
                  </div>
                 <!--  <span>{{popcontent.source}} | {{popcontent.meta.date}}</span> -->
                </div><hr>
                <div style="padding:10px;padding-bottom:15px;">
                  <div>
                    <img :src="popcontent.src" style="height:200px;width:100%;">
                  </div>
                  <div class="com_content">{{popcontent.desc}}</div><hr>
                  <div style="float:right;">
                    <span>阅读量：{{popcontent.readtotal}}</span>
                  </div>
                </div>
              </li>
            </ul>
           </div>
        </div>
      </popup>
    </div>
  </div>
</template>
<script>
import addimg from '../../components/Infopanel/addimg.vue'
import PullTo from 'vue-pull-to'
import { XHeader, ViewBox, XButton, Flexbox, FlexboxItem, XTextarea, XInput, Toast, Panel, Actionsheet, Popup, TransferDom, Group, Cell } from 'vux'
export default {
  name: 'xiaocunEvents', // 小村家事
  directives: {
    TransferDom
  },
  components: { XHeader, ViewBox, XButton, Flexbox, FlexboxItem, XTextarea, XInput, Toast, Panel, addimg, Actionsheet, Popup, Group, Cell, PullTo }, // 注册组件
  data () { // 局内数据
    return {
      show: false,
      showPanel: false,
      list: [],
      img: '',
      title: '',
      readtotal: '',
      popcontent: {},
      showeventstoasttext: '请输入内容',
      menus: { // 发表说说菜单
        menu1: '发表我的小村家事'
      },
      openwindowshow: {
        showMenus: false, // 菜单显示判断
        pop: false, // 点赞评论功能弹窗显示判断
        inputshow: false, // 输入框弹窗显示判断
        emotionshow: false, // 表情框显示判断
        IsKeyorEmo: true // true:表情 false：键盘
      },
      xiaocun: {
        eventsTitle: '', // 输入小村家事标题
        eventsText: '', // 输入小村家事内容
      },
      showtoast: false // 评论输入提示
    }
  },
  methods: { // 方法函数
    backpage () { // 关闭弹窗
      this.show = false
    },
    click (key) { // 点击菜单
      console.log(key)
      this.show = true
    },
    openpanel (item) { // 显示弹窗
      this.showPanel = true
      this.popcontent = item // 传递值到poup
      console.log(item)
    },
    backpagepop () { // 关闭弹窗
      this.showPanel = false
    },
    getlist: function () {
      this.list = []
      this.axios.get('http://localhost:5050/api/xiaocun/vilfamily_all?').then((res) => {
        console.log(res.data)
        for (let i = 1, len = res.data.data.length; i < len; i++) {
          this.list.push({
            popcontent: res.data.data[i],
            readtotal: res.data.data[i].readtotal,
            title: res.data.data[i].title,
            src: res.data.data[i].pic,
            desc: res.data.data[i].content,
            source: res.data.data[i].adduname,
            meta: {
              other: res.data.data[i].type,
              date: res.data.data[i].adddate
            }
          })
        }
      }, function () {
        console.log('请求失败处理') // 请求失败函数
      })
    },
    getImg: function () {
      this.list1 = []
      this.axios.get('http://localhost:5050/api/xiaocun/vilfamily_all?').then((res) => {
        console.log(res.data)
        for (let i = 0, len = res.data.data.length; i < len; i++) {
          this.img = res.data.data[i].pic
        }
      }, function () {
        console.log('请求失败处理') // 请求失败函数
      })
    },
    pushevents () {
      let that = this
      if (this.eventsText === '') {
        this.showeventstoasttext = '请输入小村家事内容'
        this.showtoast = true
        showPanel = true
      } else {
        let xiaocun = {
          // jsid: this.list.jsid,
          // title: this.list.title,
          // adduname: this.list.source,
          // desc: this.list.desc,
          // src: this.list.src,
          // date: '',
          // type: ''
        }
        // post需加入qs将json格式直接转化为data所需的格式（?pcid=11&ccontent=测试）
        this.axios.post('http://localhost:5050/api/xiaocun/insert_vilxiaocun', this.qs.stringify(this.xiaocun)).then((res) => {
          console.log(res.data)
          console.log(this.xiaocun)
          this.showeventstoasttext = '发布成功'
          this.showtoast = true
          this.eventsTitle = ''
          this.eventsText = ''
          this.showPanel = false
          this.list.push(xiaocun)
        })
          .catch(function (error) {
            console.log(error)
          })
      }
    }
  },
  computed: { // 计算属性
  },
  watch: { // 侦听器
  },
  mounted () { // 初始化函数
    this.getlist()
    this.getImg()
  }
}
</script>
<style lang="less" scoped>
@cell-value-color:#000;
@cell-body-label-color:#999;
</style>
