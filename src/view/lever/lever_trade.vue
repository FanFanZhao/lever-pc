<template>
  <div class="trade">
    <div class="title_box">
      <div class="tabtitle fColor1 ft16curPer">
        <!-- <span :class="{active:show == true}">限价交易</span> -->
        <!-- <span :class="{active:show == false}" @click="changeType">市价交易</span> -->
      </div>
    </div>
    <!-- 限价交易 -->
    <div class="content clear" v-if="show">
      <div class="w50 fl first">
        <div class="ft14">
          <div class="available clear fColor1" v-if="address.length<=0">
            <span class="baseColor curPer" @click="goNext('login')">登录</span>
            或
            <span class="baseColor curPer" @click="goNext('register')">注册</span>
            开始交易
          </div>
          <div class="clear available" v-else>
            <span class="fl fColor1">可用 {{user_legal}} {{currency_name}}</span>
            <!-- <span class="fr baseColor curPer" @click="goNext('account')">充币</span> -->
          </div>
          <!-- <div class="mt40 input-item clear">
                        <label>买入价</label>
                        <input type="number" v-model="buyInfo.buyPrice" @keydown.69.prevent >
                        <span>{{currency_name}}</span>
          </div>-->
          <div class="mt40 input-item clear">
            <label>倍数：</label>
            <select class="buy_multiple" v-model="buyInfo.buy_selected">
              <option disabled value>请选择倍数</option>
              <option v-for="(item,index) in multiple" :key="index" :value="index">{{item}}</option>
            </select>
          </div>
          <div class="mt40 input-item clear">
            <label>手数：</label>
            <b :class="['share',{'active':type =='1'}]" @click="select(1,'buy')">1手</b>
            <b :class="['share',{'active':type =='3'}]" @click="select(3,'buy')">3手</b>
            <b :class="['share',{'active':type =='5'}]" @click="select(5,'buy')">5手</b>
            <b :class="['share',{'active':type =='10'}]" @click="select(10,'buy')">10手</b>
          </div>
          <!-- <div class="attion tr fColor1">范围 (0.000001,20,精度: 0.000001)</div> -->
          <!-- <div class="mt50 fColor1 ft16">交易额 {{buyTotal}} {{currency_name}}</div> -->
          <div
            class="sell_btn curPer mt40 tc greenBack fColor1 ft16"
            @click="buyCoin"
          >买入（做多）{{currency_name}}</div>
        </div>
      </div>
      <div class="w50 fl second">
        <div class="ft14">
          <div class="available clear fColor1" v-if="address.length<=0">
            <span class="baseColor curPer" @click="goNext('login')">登录</span>
            或
            <span class="baseColor curPer" @click="goNext('register')">注册</span>
            开始交易
          </div>
          <div class="clear available" v-else>
            <span class="fl fColor1">可用 {{user_currency}} {{legal_name}}</span>
            <!-- <span class="fr baseColor curPer" @click="goNext('account')">充币</span> -->
          </div>
          <!-- <div class="mt40 input-item clear">
                        <label>卖出价</label>
                        <input type="number" @keydown.69.prevent v-model="sellInfo.sellPrice">
                        <span>{{currency_name}}</span>
          </div>-->
          <div class="mt40 input-item clear">
            <label>倍数：</label>
            <select class="sell_multiple" v-model="sellInfo.sell_selected">
              <option disabled value>请选择倍数</option>
              <option v-for="(item,index) in multiple" :key="index" :value="index">{{item}}</option>
            </select>
          </div>
          <div class="mt40 input-item clear">
            <label>手数：</label>
            <b :class="['share',{'active':types =='1'}]" @click="select(1,'sell')">1手</b>
            <b :class="['share',{'active':types =='3'}]" @click="select(3,'sell')">3手</b>
            <b :class="['share',{'active':types =='5'}]" @click="select(5,'sell')">5手</b>
            <b :class="['share',{'active':types =='10'}]" @click="select(10,'sell')">10手</b>
          </div>
          <!-- <div class="attion tr fColor1">范围 (0.000001,20,精度: 0.000001)</div> -->
          <!-- <div class="mt50 fColor1 ft16">交易额 {{sellTotal}} {{currency_name}}</div> -->
          <div
            class="sell_btn curPer mt40 tc redBack fColor1 ft16"
            @click="sellCoin"
          >卖出（做空）{{currency_name}}</div>
        </div>
      </div>
    </div>
    <!-- 市价交易 -->
    <div class="content clear" v-if="showNone">
      <div class="w50 fl first">
        <div class="ft14">
          <div class="available clear fColor1" v-if="address.length<=0">
            <span class="baseColor curPer" @click="goNext('login')">登录</span>
            或
            <span class="baseColor curPer" @click="goNext('register')">注册</span>
            开始交易
          </div>
          <div class="clear available" v-else>
            <span class="fl fColor1">可用 {{user_currency}} {{currency_name}}</span>
            <!-- <span class="fr baseColor curPer" @click="goNext('account')">充币</span> -->
          </div>
          <div class="mt40 input-item clear">
            <label>买入价</label>
            <input type="number" value="以市场最低价买入" @keydown.69.prevent disabled>
            <span>{{currency_name}}</span>
          </div>
          <div class="mt40 input-item clear">
            <label>买入量</label>
            <input type="number" @keydown.69.prevent @keyup="numFilter($event)">
            <span>{{legal_name}}</span>
          </div>
          <div class="sell_btn curPer mt40 tc greenBack fColor1 ft16">买入（做多）{{currency_name}}</div>
        </div>
      </div>
      <div class="w50 fl second">
        <div class="ft14">
          <div class="available clear fColor1" v-if="address.length<=0">
            <span class="baseColor curPer" @click="goNext('login')">登录</span>
            或
            <span class="baseColor curPer" @click="goNext('register')">注册</span>
            开始交易
          </div>
          <div class="clear available" v-else>
            <span class="fl fColor1">可用 {{user_legal}} {{legal_name}}</span>
            <!-- <span class="fr baseColor curPer" @click="goNext('account')">充币</span> -->
          </div>
          <div class="mt40 input-item clear">
            <label>卖出价</label>
            <input type="number" value="以市场最优价格卖出" @keydown.69.prevent disabled>
            <span>{{currency_name}}</span>
          </div>
          <div class="mt40 input-item clear">
            <label>卖出量</label>
            <input type="number" @keydown.69.prevent @keyup="numFilter($event)">
            <span>{{legal_name}}</span>
          </div>
          <div class="sell_btn curPer mt40 tc redBack fColor1 ft16">卖出（做空）{{currency_name}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
    export default {
        name: "trade",
         data (){
            return {
                currency_name:'',
                legal_name:'',
                multiple:'',
                user_currency:'',
                user_legal:'',
                show:true,
                showNone:false,
                allBalance:0,
                buyInfo:{buy_selected:'',buyNum:0,url:'lever/submit'},
                sellInfo:{sell_selected:'',sellNum:0,url:'lever/submit'},
                type:1,
                types:1,
                shares:0,
            }
        },
        created(){
            this.address = localStorage.getItem('token') || '';
       
        },
        mounted(){
            var that = this;
            that.address = localStorage.getItem('token') || '';
            this.legal_id=localStorage.getItem('legal_id');
            this.currency_id=localStorage.getItem('currency_id');
            this.legal_name=localStorage.getItem('legal_name');
            this.currency_name=localStorage.getItem('currency_name');
            that.buy_sell(that.legal_id,that.currency_id)
            eventBus.$on('tocel', function (datas) {
                console.log(datas);
                if(datas){
                    that.buy_sell(that.legal_id,that.currency_id)
                }  
            })
       
        },
        methods:{
           
            numFilter(ev){
                //48-57 96-105 108
                // console.log(ev.keyCode)
            },
            changeType(){
               this.show = !this.show;
            },
            goNext(url){
                this.$router.push({name: url});
            },
            
            buyCoin(){
                // var that = this;
                if(this.buyInfo.buy_selected == ''){
                   layer.msg('请选择倍数');
                    return;
                }
                var i=layer.load();
                this.$http({
                    url: '/api/'+this.buyInfo.url,
                    method:'post',
                    data:{
                        legal_id:this.legal_id,
                        currency_id:this.currency_id,
                        multiple:this.buyInfo.buy_selected,
                        share:this.type,  
                        type:1
                    },
                     headers: {'Authorization':  localStorage.getItem('token')},           
                }).then(res=>{
                    console.log(res ,222)
                     layer.close(i);
                    
                    if(res.data.type=="ok"){
                        layer.msg(res.data.message)
                        this.buyInfo.buyPrice=0;
                        this.buyInfo.buyNum=0;
                        eventBus.$emit('buyTrade','tradebuy');
                        eventBus.$emit('tocel','updata');
                        eventBus.$emit('to_leverExchange','leverExchange')
                        console.log(res.data.message)
                         
                       
                    }else{
                        layer.msg(res.data.message)
                    }
                }).catch(error=>{
                    console.log(error)
                })
            }, 
            sellCoin(){
                console.log(localStorage.getItem('token'))
                var that = this;
                if(this.sellInfo.sell_selected == ''){
                   layer.msg('请选择倍数');
                    return;
                }
                var i=layer.load();
                this.$http({
                    url: '/api/'+this.sellInfo.url,
                    method:'post',
                    data:{
                        legal_id:this.legal_id,
                        currency_id:this.currency_id,
                        multiple:this.sellInfo.sell_selected,
                        share:this.types,
                        type:2
                    },
                    headers: {'Authorization':  localStorage.getItem('token')}, 
                }).then(res=>{
                    console.log(res)
                    layer.close(i);
                    // layer.msg(res.data.message)
                    if(res.data.type=="ok"){
                        this.sellInfo.sellPrice=0;
                        this.sellInfo.sellNum=0;
                        eventBus.$emit('buyTrade','tradebuy');
                        eventBus.$emit('tocel','updata');
                        eventBus.$emit('to_leverExchange','leverExchange')
                        // that.buy_sell(that.legal_id,that.currency_id)
                        layer.msg(res.data.message);
                    }else{
                        // layer.msg(res.data.message);
                    }
                }).catch(error=>{
                    console.log(error)
                })
            },
                //买入、卖出记录
        buy_sell(legals_id,currencys_id){
            this.$http({
                        url: '/api/'+'lever/deal',
                        method:'post',
                        data:{
                            legal_id:legals_id,
                            currency_id:currencys_id
                        },  
                        headers: {'Authorization':  localStorage.getItem('token')},    
                    }).then(res=>{
                        console.log(res);
                        if(res.data.type == "ok"){
                        this.multiple = res.data.message.multiple
                        this.user_currency = res.data.message.all_levers;
                        this.user_legal = res.data.message.user_lever;
                        // console.log(res.data)
                            this.buyInfo.buyPrice=0;
                            this.buyInfo.buyNum=0;
                        }else{
                            // layer.msg(res.data.message)
                        }
                    }).catch(error=>{
                        // console.log(error)
                    })
            },
            // 选择手数
            select(options,values){
               let that = this;
               that.shares = options;
               if(values == 'buy'){
                    that.type = options;
               }else{
                   that.types = options;
               }
            }
        },
        computed:{
            buyTotal(){
               return (this.buyInfo.buy_selected * this.buyInfo.buyNum) || 0;
            },
            sellTotal(){
                return this.sellInfo.sell_selected * this.sellInfo.sellNum || 0;
            }
        }
    
        
    }
</script>

<style scoped>
.title_box{height: 48px;line-height: 48px; padding: 0 30px;background-color: #181b2a;box-shadow: 0 2px 6px rgba(0,0,0,.1);}
.tabtitle span{cursor: pointer;}
.tabtitle span:not(:last-child) {margin-right: 40px;}
.content .first{padding: 0 15px 0 25px;}
.content .second{padding: 0 25px 0 15px;}
.available{height: 48px;border-bottom: 1px solid #303b4b;line-height: 48px;}
.input-item{position: relative;line-height: 40px;}
.input-item label{width: 20%;float: left;font-size: 14px;color: #637085;}
.input-item input{width: 80%;float: left;border: 1px solid #52688c;border-radius: 3px;height: 40px;text-indent: 15px;font-size: 16px;color: #cdd6e4;background-color: #262a42;line-height: 38px;}
.input-item span{position: absolute;right: 15px;color: #637085;font-size: 16px}
.input-item select{
    width: 80%;
    background: #262a42;
    color: #cdd6e4;
    border: 1px solid #52688c;
    border-radius: 3px;
    height: 40px;
    text-indent: 10px;
    font-size: 16px;
}
.attion{height: 20px;line-height: 30px;font-size: 12px;}
.sell_btn{width: 100%;height: 40px;border-radius: 3px;color: #cdd6e4;line-height: 40px;}
.greenBack {background-color: #55a067;}
.redBack {background-color: #cc4951;}
input:disabled {color: #627085;cursor: not-allowed;}
.share{display: inline-block;font-weight: normal;border: 1px solid #eee;border-radius: 2px;font-size: 14px;color: #fff;line-height: 1.2;width: 18.8%;text-align: center;padding: 5px 0;}
b.active{background-color: #02c289;border: 1px solid #02c289;}
</style>

