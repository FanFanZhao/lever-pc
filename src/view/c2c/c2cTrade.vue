<template>
  <div id="c2c-box" class="flex">
    <div class="c2c-l">
        <left></left>
    </div>
    <div class="c2c-r">
      <!-- <div class="title-top flex ft20">
        <p v-for="(item,index) in topType" :class="{'active':index==current}" @click='changeType(index,item.type,item.title)'>{{item.title}}</p>
      </div> -->
      <div class="listbox">
        <div class="flex flextitle">
          <span>类型</span>
          <span class="tc">数量</span>
          <span class="tc">交易总额</span>
          <span class="tc">支付方式</span>
          <span class="tc">时间</span>
          <span class="tc">状态</span>
          <span class="tr">操作</span>
        </div>
        <ul>
          <li v-for="(item,index) in list" :key="index" class="flex alcenter curPer">
            <div class="flex alcenter">
              <p class="head">{{item.type=='sell'?'购买':'出售'}}</p>
              <span class="currencyName ml5">{{item.currency_name}}</span>
            </div>
            <div class="flex center tc">
              <span class="light_blue sellerName">{{item.deal_money}}</span>
            
            </div>
            <div class="tc">{{item.price}}</div>
            <div class="tc">
              <img v-if="item.way_name == '支付宝'" src="../../assets/images/zfb_icon.png" />
              <img v-if="item.way_name == '微信'" src="../../assets/images/wx_icon.png" />
              <img v-if="item.way_name == '银行卡'" src="../../assets/images/bank_icon.png" />
            </div>
            <div class="tc">{{item.create_time}}</div>
            <div class="tc">
              <div v-if="item.is_sure==0">未完成</div>
              <div v-if="item.is_sure==1">已完成</div>
              <div v-if="item.is_sure==2">已取消</div>
              <div v-if="item.is_sure==3">已付款</div>
            </div>
            <div class="tr">
              <button class="btn">详情</button>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <!-- =========详情弹窗========== -->
    <div class="mask" v-if="showDetail">
      <div class="m-content">
        <div class="title">
            <div>详情</div>
            <div @click="showDetail = false">x</div>
        </div>
        <div class="list">
          <div class="create-date">
            <span>创建时间：</span><span>2018-05-06</span>
          </div>
        </div>
        <!-- 我发布的内容 -->
        <div class="myC2cDetail">
            <div>
              <span>交易类型：</span><span>liuluil</span>
            </div>
            <div>
              <span>币  种：</span><span>luiolyl</span>
            </div>
          </div>
        </div>            
      </div>
    </div>
  </div>
</template>
<script>
import left from '@/view/c2c/leftc2c'
import Vue from 'vue'
	Vue.filter('circle', function (value) {
		return value.substring(0,1)
	})
export default {
    components:{left},
    data(){
      return{
        token:'',
        current:0,
        type:'sell',
        page:1,
        list:[],
        legal_id:'',
        classify:'求购',
        topType:[{'title':"求购","type":"buy"},{'title':"出售","type":"sell"}],
        showDetail:true
      }
    },
    created(){
        this.token = window.localStorage.getItem("token") || "";
        if (this.token == "") {
          this.$router.push("/components/login");
        };     
        this.getList();
    },
    methods:{
      changeType(index,type,title){
        this.current=index;
        this.classify=title;
        this.type=type;
        this.list=[];
        this.page=1;
        this.getList(type);
      },
      // 获取买入列表
      getList() {
        let page = 1;
        let i = layer.load();
        this.$http({
          url: "/api/c2c_user_deal?&page=" + page,
          method: "get",
          headers: { Authorization: this.token }
        }).then(res => {
          layer.close(i);
          if (res.data.type == "ok") {
            let listdata = res.data.message.data;
            console.log(listdata);
            if (listdata.length != 0) {
              this.list = this.list.concat(listdata);
              this.page += 1;
            }
          }
        });
      },

      // 下单请求
      sureOrder(id){
        var i=layer.load();
        this.$http({
          url: "/api/c2c/do_legal_deal",
          method: "post",
          data:{id:id},
          headers: { Authorization: this.token }
        }).then(res => {
          layer.close(i);
          if (res.data.type == "ok") {
            console.log(res);
            layer.msg(res.data.message)
          }else{
            layer.msg(res.data.message)
          }
        });
      }
    },
}
</script>
<style lang='scss'>
#c2c-box {
  margin: 10px 0 10px;
  color: #c7cce6;
  .ml5{margin-left: 5px}
  .c2c-r{
    .title-top {
      p{
        margin-right: 30px;
        padding: 5px 0;
        cursor: pointer;
      } 
    }
    .coin-select{
      p{
        margin-right: 10px;
        cursor: pointer;
      }
      .select{
        color: #7a98f7;
      }
    }
    .listbox{
      .flextitle{
        padding: 20px 0;
        border-bottom: 1px solid #242840;
        span{
          flex:1;
        }
      }
      li{
        padding: 10px 0;
        >div{
          flex: 1;
          line-height: 36px;
        }
        .head{
          width: 36px;
          height: 36px;
          border-radius: 50%;
          margin-right: 10px;
          background: #5D8CC2;
          color: #fff;
          text-align: center;
          font-size: 14px;
        }
        .btn{
          border-radius: 3px;
          color: white;
          background-color: #638BD4;
          cursor: pointer;
          min-height: 33px;
          min-width: 80px;
          font-size: 14px;
          font-weight: 600;
          border: none;
        }
      }
      li:nth-child(even){
        background: #242840;
      }
      li:last-child{
        border-bottom: 1px solid #242840;
      }
    }
    
  }

  > .c2c-l {
    margin: 0 10px;
    padding: 10px;
    background: #181b2a;
    width: 23%;
    li {
      padding: 0 20px;
      justify-content: space-between;
      cursor: pointer;
      line-height: 40px;
      .redColor {
        margin-left: 10px;
      }
      &:hover {
        background: rgb(38, 42, 66);
      }
    }
  }
  > .c2c-r {
    padding: 10px 30px;
    background: #181b2a;
    margin-right: 10px;
    width: 77%;
  }
  .mask {
    position: fixed;
    width: 100%;
    height: 100%;
    z-index: 999;
    background: rgba(0, 0, 0, 0.7);
    > .m-content {
      border-radius: 4px;
      background: #fff;
      position: absolute;
      top: 40%;
      left: 50%;
      transform: translate(-50%, -50%);
      padding: 40px 20px 30px;
      // min-height: 400px;
      max-height: 550px;
      width: 400px;
      > .title {
        position: absolute;
        top: 0;
        left: 0;
        text-align: center;
        width: 100%;
        height: 40px;
        line-height: 40px;
        font-size: 18px;
        text-align: center;
        font-weight: 600;
        > div:last-child {
          position: absolute;
          top: 0;
          right: 0;
          padding: 0 15px;
          cursor: pointer;
        }
      }
      > div:not(.title) {
        line-height: 32px;
        // border-top: 1px solid #eaecef;
      }
      div {
        span:first-child {
          margin-right: 5px;
          display: inline-block;
          width: 70px;
          color: #ca4141;
        }
      }
    }
  }
}

</style>


