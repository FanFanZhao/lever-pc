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
        <ul class="content" v-if="buyList.length>0">
          <li class="flex">
            <div class="tl">名称</div>
            <div>数量</div>
            <div>单价</div>
            <div>付款方式</div>
            <div class="tr">操作</div>
          </li>
          <li v-for="item in buyList" :key="item.id" class="flex">
            <div class="tl">{{item.currency_name}}</div>
            <div>{{item.total_number}} {{item.currency_name}}</div>
            <div>{{item.price}}</div>
            <div>
              <img v-if="item.way == 'ali_pay'" src="../../assets/images/zfb_icon.png">
              <img v-if="item.way == 'we_chat'" src="../../assets/images/wx_icon.png">
              <img v-if="item.way == 'bank'" src="../../assets/images/bank_icon.png">
            </div>
            <div class="tr">
              <button type="button" @click="backOrder(item.id)">撤回</button>
              <button type="button">查看订单</button>
            </div>
          </li>
        </ul>
        <div class="no_data tc" v-if="buyList.length<=0">
          <img src="../../assets/images/nodata.png" alt>
          <p class="fColor2 ft18">暂无数据</p>
        </div>
        <button class="more" @click="moreData();" v-html="moreText"></button>
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
      buyList: [],
      moreText: "加载更多"
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
      let i = layer.load();
      this.$http({
        url: "/api/currency/list",
        method: "get",
        headers: { Authorization: this.token }
      }).then(res => {
        layer.close(i);
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
      let i = layer.load();
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
        layer.close(i);
        if (res.data.type == "ok") {
          let listData = res.data.message.data;
          if (listData.length > 0) {
            that.buyList = that.buyList.concat(listData);
          } else if (that.page == 1 && listData.length == 0) {
          }
        }
      });
    },
    // 撤回订单
    backOrder(ids) {
      let i = layer.load();
      this.$http({
        url: "/api/c2c/back_send",
        method: "post",
        data: {
          id: ids
        },
        headers: { Authorization: this.token }
      })
        .then(res => {
          layer.close(i);
          layer.msg(res.data.message);
          setTimeout(() => {
            location.reload();
          }, 500);
        })
        .catch(res => {
          layer.msg(res.data.message);
        });
    },
    // 加载更多
    moreData() {
      let that = this;
      that.page = that.page + 1;
      that.getBuyList();
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
    width: 100%;
    text-align: center;
    border: none;
    background-color: rgba(0,0,0,0);

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
      .content {
        width: 100%;
        li {
          width: 100%;
          color: #c7cce6;
          -webkit-box-pack: justify;
          -ms-flex-pack: justify;
          justify-content: space-between;
          text-align: center;
          padding: 8px 5px;
          line-height: 24px;
        }
        > .flex {
          cursor: pointer;
          > div {
            -webkit-box-flex: 1;
            -ms-flex: 1;
            flex: 1;
            padding: 10px 0;
          }
        }
        .tl {
          text-align: left;
        }
        .tr {
          text-align: right;
        }
        button {
          border-radius: 3px;
          color: white;
          background-color: #638bd4;
          cursor: pointer;
          min-height: 33px;
          min-width: 80px;
          font-size: 14px;
          font-weight: 600;
          border: none;
        }
      }
    }
  }
}
</style>
