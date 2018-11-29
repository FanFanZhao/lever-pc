<template>
  <div id="c2c-box" class="flex">
    <div class="c2c-l">
      <left></left>
    </div>
    <div class="c2c-r">
      <div class="top">
        <div class="top-left">
          <h4>购买</h4>
          <ul>
            <li
              class="flex"
              v-for="(item,index) in currency_list"
              :key="index"
              :class="index == active?'bg_active':''"
              :data-id="item.id"
              @click="currency_click(item.id,item.name,index)"
            >{{item.name}}</li>
          </ul>
        </div>
        <div class="top-right">
          <h4>出售</h4>
          <ul>
            <li
              class="flex"
              v-for="(item,index) in currency_list"
              :key="index"
              :class="index == active?'bg_active':''"
              :data-id="item.id"
              @click="currency_click(item.id,item.name,index)"
            >{{item.name}}</li>
          </ul>
        </div>
      </div>
      <div class="bot">
        <div class="list-title flex" v-if="showList">
          <div>名称</div>
          <div>数量</div>
          <div>单价</div>
          <div>付款方式</div>
          <div>操作</div>
        </div>
        <ul class="content">
          <li v-for="item in buyList" :key="item.id" class="flex">
            <div>{{item.currency_name}}</div>
            <div>{{item.total_number}}</div>
            <div>{{item.price}}</div>
            <div>{{item.way_name}}</div>
            <div>
            <button type="button">撤回</button>
            <button type="button">查看订单</button>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import left from "@/view/c2c/leftc2c";
export default {
  components: {
    left
  },
  data() {
    return {
      token: "",
      active: 0,
      currency_list: [],
      currency_name: "",
      id: "",
      showList: true,
      showDetail: false,
      detail: {}, //li详情,
      showTradeBox: false,
      page: 1,
      types: "buy",
      buyList: []
    };
  },
  created() {
    this.token = window.localStorage.getItem("token") || "";
    if (this.token == "") {
      this.$router.push("/components/login");
    }
    this.get_currency();
    this.getBuyList();
  },

  methods: {
    // 获取币种列表
    get_currency() {
      this.$http({
        url: "/api/currency/list",
        method: "get",
        headers: { Authorization: this.token }
      }).then(res => {
        //////consolelog(res);
        if (res.data.type == "ok") {
          this.currency_list = res.data.message.legal;
          this.currency_name = res.data.message.legal[0].name;
          this.id = res.data.message.legal[0].id;
        }
      });
    },
    //选择币种
    currency_click(id, name, index) {
      this.currency_name = name;
      this.active = index;
      this.id = id;
    },

    // 获取购买币种数据列表
    getBuyList() {
      let that = this;
      this.$http({
        url: "/api/c2c/seller_trade",
        method: "get",
        data: {
          type: that.types,
          currency_id: that.id,
          page: that.page
        },
        headers: { Authorization: this.token }
      }).then(res => {
        //////consolelog(res);
        if (res.data.type == "ok") {
          that.buyList = res.data.message.data;
          console.log(res);
        }
      });
    }
  }
};
</script>

<style lang='scss'>
#c2c-box {
  margin: 10px 0 10px;
  .more {
    color: #7a98f7;
    text-align: center;
    cursor: pointer;
  }
  > .c2c-l {
    margin: 0 10px;
    padding: 10px;
    background: #181b2a;
    width: 23%;
  }
  > .c2c-r {
    padding: 10px 30px;
    background: #181b2a;

    margin-right: 10px;
    width: 77%;
    > .top {
      // margin: 10px 30px 10px;
      > .top-title {
        font-size: 24px;
        line-height: 2;
      }
    }
    > .bot {
      color: #6b80ae;
      > .bot-title {
        margin: 30px 0 0;
        font-size: 16px;
        line-height: 40px;
        justify-content: space-between;
        align-items: center;
        > div:first-child {
          cursor: pointer;
          span {
            font-weight: 600;
            line-height: 40px;
            margin-right: 20px;
          }
          .active {
            color: #7a98f7;
            border: none;
          }
        }
        > .flex {
          height: 17px;
          line-height: 15px;
          cursor: pointer;
          > div {
            margin-right: 10px;
            border: 1px solid #ccc;
            transition: all 0.3s;
            width: 32px;
            border-radius: 7.5px;
            div {
              width: 15px;
              height: 15px;
              border-radius: 50%;
              background: #fff;
            }
          }
        }
      }
      > .list-title {
        height: 40px;
        line-height: 40px;
        font-weight: 600;
      }
      .list-title,
      ul li .content {
        width: 100%;
        color: #c7cce6;
        justify-content: space-between;
        text-align: center;
        padding: 8px 5px;
        line-height: 24px;
      }
      .content{
        width: 100%;
        li{
            width: 100%;
            color: #c7cce6;
            -webkit-box-pack: justify;
            -ms-flex-pack: justify;
            justify-content: space-between;
            text-align: center;
            padding: 8px 5px;
            line-height: 24px;
        }
      }

    }
  }
}

.detailit,
.showtrade {
  // float: right;
  margin-right: 20px;
  padding: 0 10px;
  min-width: 55px;
  max-width: 80px;
  color: #fff;
  text-align: center !important;
  cursor: pointer;
  background: #7a98f7;
}
</style>
