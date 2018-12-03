<template>
  <div class="wrap">
    <ul class="list_head ft14">
      <li>
        <span>类型</span>
        <span>开仓价</span>
        <span>当前价</span>
        <span>保证金</span>
        <span>止盈价</span>
        <span>止损价</span>
        <span>开仓时间</span>
        <span>操作</span>
      </li>
    </ul>
    <ul class="list_content fColor1 ft12">
      <li v-for="(item,index) in list_content" :key="index">
        <span>{{item.type == 1? '买入':'卖出'}}</span>
        <span>{{item.price || '0.00' | tofixed}}</span>
        <span>{{item.update_price || '0.00' | tofixed}}</span>
        <span>{{item.caution_money || '0.00' | tofixed}}</span>
        <span>{{item.target_profit_price || '0.00' | tofixed}}</span>
        <span>{{item.stop_loss_price || '0.00' | tofixed}}</span>
        <span>{{item.time}}</span>
        <div>
          <span class="red" @click="stopLoss(item.id)">设置止盈止损</span>
          <span class="red" @click="pingcang(item.id)">平仓</span>
        </div>
      </li>
    </ul>
    <p class="more" @click="load_more">{{more}}</p>

    <!-- 止盈止损弹窗 -->
    <div class="loss-modal" v-show="modalShow">
      <div class="content">
        <div class="loss-modal-header">设置止盈止损</div>
        <div class="loss-madal-content">
          <div>
            <span>止盈价格：</span>
            <p>
              <span @click="reduce(1)">-</span>
              <input type="text" v-model="targetProfit">
              <span @click="add(1)">+</span>
            </p>
          </div>
          <p>预期盈利：{{stopLose}}</p>
          <div>
            <span>止损价格：</span>
            <p>
              <span @click="reduce(1)">-</span>
              <input type="text" v-model="stopLose">
              <span @click="add(1)">+</span>
            </p>
          </div>
          <p>预期亏损：{{stopLose}}</p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      legal_id: "",
      currency_id: "",
      list_content: [],
      page: 1,
      more: "加载更多",
      modalShow: false,
      modalData:{},
      targetProfit:'',
      stopLose:'',
      
    };
  },
  created() {
    this.legal_id = localStorage.getItem("legal_id");
    this.currency_id = localStorage.getItem("currency_id");
    this.init();
  },
  filters: {
    tofixed: function(val) {
      val = Number(val);
      return val.toFixed(2);
    },
    // abs: function(val){
    //   val = Number(val);
    //   return val.toFixed(2);
        
    // }
  },
  methods: {
    init() {
      this.more = "加载中...";
      this.$http({
        url: "/api/" + "lever/dealall",
        method: "post",
        data: {
          legal_id: this.legal_id,
          currency_id: this.currency_id,
          page: this.page
        },
        headers: { Authorization: localStorage.getItem("token") }
      })
        .then(res => {
          console.log(res);
          if (res.data.type == "ok") {
            this.more = "加载更多";
            this.list_content = this.list_content.concat(
              res.data.message.order.data
            );
            if (res.data.message.order.data.length == 0) {
              this.more = "没有更多了...";
            }
          } else {
            layer.msg(res.data.message);
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    pingcang(id) {
      this.$http({
        url: "/api/" + "lever/close",
        method: "post",
        data: {
          id: id
        },
        headers: { Authorization: localStorage.getItem("token") }
      })
        .then(res => {
          if (res.data.type == "ok") {
            layer.msg(res.data.message);
            location.reload();
          } else {
            layer.msg(res.data.message);
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    load_more() {
      this.page++;
      this.init();
    },
    // 设置止盈止损
    stopLoss(ids) {
      let that = this;
      that.modalShow = true;
      for(let i in that.list_content){
          if(that.list_content[i].id == ids){
              that.targetProfit = that.list_content[i].target_profit_price;
              that.stopLose = that.list_content[i].stop_loss_price;
          }
      }
    }
  }
};
</script>
<style scoped>
.wrap {
  min-height: 500px;
  background: #181b2a;
  width: 80%;
  margin: 30px auto;
  padding: 30px;
}
ul li {
  padding: 8px 0;
}
ul li span {
  width: 10%;
  display: inline-block;
  text-align: center;
}
ul li div {
  width: 20%;
  display: inline-block;
  text-align: center;
}
ul li div span {
  width: auto;
}
.list_head {
  color: #637085;
  border-bottom: 1px solid #2e333c;
}
.red {
  color: #cc4951;
  cursor: pointer;
}
.more {
  text-align: center;
  color: #999;
  font-size: 14px;
  margin-top: 10px;
  cursor: pointer;
}
</style>
