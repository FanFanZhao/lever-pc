<template>
  <div id="c2c-box" class="flex">
    <div class="c2c-l">
        <left></left>
    </div>
    <div class="c2c-r">
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
              <p class="head">{{item.type=='sell'?'出售':'购买'}}</p>
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
              详情
            </div>
          </li>
        </ul>
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
        ids:'',
      }
    },
    created(){
        this.token = window.localStorage.getItem("token") || "";
        this.ids = this.$route.query.id;
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
        let that = this;
        let page = 1;
        let i = layer.load();
        that.ids = this.$route.query.id;
        console.log(that.ids);
        this.$http({
          url: "/api/c2c/legal_send_deal_list?&page=" + that.page + "&id="+that.ids,
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
      // 下单
      order(id){
        var that = this;
        layer.confirm('确认下单吗？', {
          btn: ['确认','取消'] //按钮
        }, function(){
          that.sureOrder(id);
        }, function(){
          // layer.msg('取消成功');
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
}

</style>


