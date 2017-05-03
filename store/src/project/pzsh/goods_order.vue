<template>
  <div :class="order_type===1?'body':''">
    <loading style="text-align: center;" v-if="loading"></loading>
    <div v-else>
      <group title="收货人信息">
        <x-input labelWidth="5em" title="姓名" name="username" placeholder="请输入姓名" is-type="china-name" :value="buyer_name"></x-input>
        <x-input title="手机号码" name="mobile" placeholder="请输入手机号码" keyboard="number" is-type="china-mobile" :value="buyer_tel"></x-input>
        <x-input title="收货地址" name="address" placeholder="请输入收货地址" keyboard="china-name" :value="buyer_address"></x-input>
        <x-input v-if="order_type===2" title="快递公司" name="express_company" keyboard="china-name" :value="express_company" disabled></x-input>
        <x-input v-if="order_type===2" title="快递单号" name="express_no" keyboard="number" :value="express_no" disabled></x-input>
      </group>
      <group :title="order_type===1?store_name:show_title">
        <div>
          <goods-show :goods="goods" :type="goods_type"></goods-show>
        </div>
      </group>
      <div style="text-align: right;padding-right:.8rem;padding-bottom:.4rem">
        <p style="padding:.4rem 0">共3件商品 合计:￥4770</p>
      </div>
      <x-button v-if="order_type===1" class="abs_button" type="primary">支付</x-button>
    </div>

  </div>

</template>

<script>
  import goodsShow from '@/project/pzsh/goods_show'
  import loading from '@/components/loading/dataLoading'
  import { Cell, Group, XInput, XButton } from 'vux'
  export default{
    name: 'goods_detail',
    components:{
      goodsShow,loading,Cell, Group, XInput,XButton
    },
    data(){
        return{
            loading:true,
            goods:[],
            store_name:"",
            store_id:"",
            buyer_name:"",
            buyer_tel:"",
            buyer_address:"",
            goods_type:'2',
            order_type:1,
            express_company:'',
            state:'',
            show_title:'',
            express_no:''
        }
    },
    mounted(){
      this.order_type = this.$route.query.type;
      let self = this;
      this.$http.get('/api/goodsOrder').then((data) => {
        self.goods = data.body.data.goods;
        self.store_name = data.body.data.store_name;
        self.store_id = data.body.data.store_id;
        self.buyer_name = data.body.data.buyer_name;
        self.buyer_tel = data.body.data.buyer_tel;
        self.buyer_address = data.body.data.buyer_address;
        self.express_company = data.body.data.express_company;
        self.express_no = data.body.data.express_no;
        self.state = data.body.data.state;
        self.show_title='<div style="display:inline-block">'+self.store_name+'</div><div style="float:right;color:deepskyblue">'+self.state+'</div>';
      }, () => {
        console.log(2);
      });
      this.loading = false;
    },
    methods:{
      listenState(state){
        switch(state){

        }
      }
    }
  }
</script>

<style scoped>
.body{
  padding-bottom: 44px;
}
  .abs_button{
    position:fixed;
    bottom:0;
  }
  .text_right{
    text-align: right;
  }
</style>
