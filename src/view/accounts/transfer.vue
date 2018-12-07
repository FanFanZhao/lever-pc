<template>
  <div class>
    <div class="content fColor1">
      <div class="flex">
        <p>从</p>
        <p class="trade">{{name}}</p>
        <p @click="tabClick">至</p>
        <p class="trade">{{leverName}}</p>
      </div>

      <div class="num">
        <p>划转数量</p>
        <input type="text" placeholder="请输入划转数量">
        <p>可用余额：{{totalMoney || '0.00' | toFixeds}}USDT</p>
      </div>
      <button type="button" class="comfirm-btn">确认划转</button>
    </div>
  </div>
</template>
<script>
import indexHeader from "@/view/indexHeader";
import left from "@/view/left";
export default {
  name: "transfer",
  filters: {
    toFixeds: function(value) {
      value = Number(value);
      return value.toFixed(2);
    }
  },
  data() {
    return {
      name: "C2C账户",
      leverName: "杠杆账户",
      totalMoney: "",
      leverMoney: "",
      tradeMoney: "",
      type:1
    };
  },
  components: {
    indexHeader,
    left
  },
  methods: {
    init() {
      var that = this;
      this.$http({
        url: "/api/" + "wallet/list",
        method: "post",
        data: {},
        headers: { Authorization: that.token }
      })
        .then(res => {
          console.log(res);
          if (res.data.type == "ok") {
            let list = res.data.message.legal_wallet;
            let lists = res.data.message.lever_wallet;
            for (let i in list) {
              if (list[i].currency_name == "USDT") {
                that.tradeMoney = list[i].legal_balance;
              }
            }
            for (let i in lists) {
              if (lists[i].currency_name == "USDT") {
                that.leverMoney = list[i].lever_balance;
              }
            }
            that.totalMoney = that.tradeMoney;
          } else {
            return;
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    tabClick(){
      let that = this;
      if(that.type == 1){
        that.type =2;
        that.name = '杠杆账户';
        that.leverName = 'C2C账户';
      }else{
        that.type =1;
        that.name = 'C2C账户';
        that.leverName = '杠杆账户';
      }

    }
  },
  created() {
    this.token = localStorage.getItem("token") || "";
    this.init();
  },

  mounted() {
    var that = this;
  }
};
</script>
<style scoped lang="scss">
.content {
  width: 600px;
  padding-top: 100px;
  line-height: 30px;
  margin: 0 auto;
}
.trade {
  width: 110px;
  line-height: 30px;
  border: 1px solid #4e5b85;
  text-align: center;
  border-radius: 4px;
  margin-right: 15px;
  margin-left: 15px;
}
.num {
  margin-top: 15px;
}
.num input {
  width: 450px;
  height: 40px;
  line-height: 40px;
  border: 1px solid #4e5b85;
  border-radius: 6px;
  background-color: rgba(0, 0, 0, 0);
  padding: 0 10px;
  color: #fff;
  margin-top: 15px;
  margin-bottom: 15px;
}
.comfirm-btn{
  width: 450px;
  line-height: 40px;
  border-radius: 6px;
  margin-top: 40px;
  background-color: #4e5b85;
  color: #fff;
  border: none;
  outline: none;
}
</style>


