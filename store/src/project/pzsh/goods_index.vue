<template>
  <div>
    <search style="position:fixed;top:0;z-index:1" @result-click="resultClick" @on-change="getResult" :results="results" v-model="value" position="fixed" auto-scroll-to-top top="0" @on-focus="onFocus" @on-cancel="onCancel"></search>
    <Loading v-if="loading"></Loading>
    <Scroller :on-refresh="onRefresh" :on-infinite="onInfinite">
      <swiper loop auto :list="demo06_list" :index="demo06_index" @on-index-change="demo06_onIndexChange"></swiper>
      <div class="weui_flex" style="font-size:14px">
        <div class="flex_tab f_flex f_ac f_vc" style="color:#10aeff">
          <div style="padding:5px" @click="showActionsheet2">{{current_rack}}</div>
          <i class="arrow"></i>
        </div>
        <div class="flex_tab f_flex f_ac f_vc" style="color:#10aeff">
          <div style="padding:5px" @click="showActionsheet">{{current_sort}}</div>
          <i class="arrow"></i>
        </div>
      </div>
      <div class="center_content font_1_2">
        <goodslist :goodsItem="goodsItem"></goodslist>
      </div>
    </Scroller>
    <actionsheet v-model="show_sort" :menus="sort" @on-click-menu="click_sort"></actionsheet>
    <actionsheet v-model="show_rack" :menus="rack" @on-click-menu="click_rack"></actionsheet>
  </div>
</template>

<script>
  import { Swiper,Search,Selector, Group,Actionsheet} from 'vux'
  import goodslist from './goods_list.vue'
  import Scroller from '@/components/scroller_me/scroller';
  import Loading from '@/components/loading/dataLoading'

  const baseList = [{
    url: 'javascript:',
    img: 'http://pic.qiantucdn.com/58pic/14/59/28/74A58PICIrd_1024.jpg',
    title: '送你一朵fua'
  }, {
    url: 'javascript:',
    img: 'http://pic.qiantucdn.com/58pic/23/12/64/92G58PIC93T_1024.jpg',
    title: '送你一辆车'
  }, {
    url: 'javascript:',
    img: 'http://pic.qiantucdn.com/58pic/25/94/22/94558PICxJ3_1024.jpg',
    title: '送你一次旅行'
  }];

  const goodList=[
    {
      goods_id:1,
      img:"http://pic.qiantucdn.com/58pic/25/94/22/94558PICxJ3_1024.jpg",
      name:"这是一颗小星星",
      price:1500,
      sale_num:200
    },{
      goods_id:2,
      img:"http://pic.qiantucdn.com/58pic/25/94/22/94558PICxJ3_1024.jpg",
      name:"这是一颗小星星",
      price:1500,
      sale_num:200
    },{
      goods_id:3,
      img:"http://pic.qiantucdn.com/58pic/25/94/22/94558PICxJ3_1024.jpg",
      name:"这是一颗小星星",
      price:1500,
      sale_num:200
    }
  ];
  const goodsItem=goodList.map((item, index) => ({
    goods_id: item.goods_id,
    img: item.img,
    name: item.name,
    price: item.price,
    sale_num: item.sale_num,
  }));
  const urlList = baseList.map((item, index) => ({
    url: 'http://m.baidu.com',
    img: item.img,
    title: `(可点击)${item.title}`
  }));

  export default {
    data () {
      return {
        loading:true,
        demo06_list: urlList,//轮播图
        demo06_index: 0,
        results: [],//搜索内容
        value: 'test',
        goodsItem:goodsItem,//商品列表
        current_sort:'默认',
        sort:[],
        show_sort:false,
        show_rack:false,
        current_rack:'全部',
        rack:[],
        pageno:0,
        pagesize:4,
        current_sort_key:0,
        current_rack_key:0,
        totalpage:[]
      }
    },
    components: {
      Swiper,Search,goodslist,Selector, Group,Actionsheet,Scroller,Loading
    },
    mounted(){
      this.getlist();
    },
    methods: {
      getlist(){
        const self = this;
        const senddata = {
          "data": {
            "pageno":this.pageno,
            "pagesize":this.pagesize,
            "sort":this.current_sort_key,
            "rack":this.current_rack_key
          }
        };
        /*this.$http.post(service_url+'/o2o/shop/wx/indexinfo.do',senddata).then( (data)=> {
          /!*if(mark){
           let goodsList = data.body.fields.goodsList;
           for(let i =0;i<goodsList.length;i++){
           self.goodsItem.push(goodsList[i]);
           }

           }else{
           self.goodsItem=data.body.fields.goodsList;
           }*!/
          self.totalpage=data.body.totalpage;
          /!*if(self.totalpage===1){
            this.$el.querySelector('.load-more').style.display = 'none';
          }else{
            this.$el.querySelector('.load-more').style.display = 'block';
          }*!/
          self.goodsItem=data.body.fields.goodsList;

          let arr_sort=data.body.fields.sort;
          let json_sort={};
          for(var i=0;i<arr_sort.length;i++)
          {
            json_sort[arr_sort[i].key]=arr_sort[i].value
          }
          self.sort=json_sort;

          let arr_rack=data.body.fields.rack;
          let json_rack={};
          for(var j=0;j<arr_rack.length;j++)
          {
            json_rack[arr_rack[j].key]=arr_rack[j].value
          }
          self.rack=json_rack;

        },()=>{
          console.log(2);
        });*/


        this.$http.get('/api/goodsIndex').then((data) => {
          self.totalpage=data.body.data.totalpage;
          self.goodsItem=data.body.data.fields.goodsList;
          let arr_sort=data.body.data.fields.sort;
          let json_sort={};
          for(let i=0;i<arr_sort.length;i++)
          {
            json_sort[arr_sort[i].key]=arr_sort[i].value
          }
          self.sort=json_sort;

          let arr_rack=data.body.data.fields.rack;
          let json_rack={};
          for(let j=0;j<arr_rack.length;j++)
          {
            json_rack[arr_rack[j].key]=arr_rack[j].value
          }
          self.rack=json_rack;

         }, () => {
         console.log(2);
         });
        this.loading=false;
      },
      demo06_onIndexChange (index) {
        this.demo06_index = index
      },
      resultClick (item) {
        window.alert('you click the result item: ' + JSON.stringify(item))
      },
      getResult (val) {
        this.results = val ? getResult(this.value) : []
      },
      onFocus () {
        console.log('on focus')
      },
      onCancel () {
        console.log('on cancel')
      },
      showActionsheet () {
        this.show_sort=true;
      },
      showActionsheet2 () {
        this.show_rack=true;
      },
      click_sort(key){
        this.pageno=0;
        this.current_sort_key=parseInt(key);
        this.getlist();
        for(let i in this.sort){
          if(i==key){        //item 表示Json串中的属性，如'name'
            let jValue=this.sort[i];//key所对应的value
            this.current_sort=jValue;
          }
        }
      },
      click_rack(key){
        this.pageno=0;
        this.current_rack_key=parseInt(key);
        this.getlist();
        for(let i in this.rack){
          if(i==key){        //item 表示Json串中的属性，如'name'
            let jValue=this.rack[i];//key所对应的value
            this.current_rack=jValue;
          }
        }
      },
      onInfinite(done) {
        console.log("onInfinite")
        this.pageno++;
        const senddata = {
          "data": {
            "pageno":this.pageno,
            "pagesize":this.pagesize,
            "sort":this.current_sort_key,
            "rack":this.current_rack_key
          }
        };
        const self = this;
        if(this.pageno>this.totalpage-1){
          this.$el.querySelector('.load-more').style.display = 'none';
          return;
        }else{
          /*this.$http.post(service_url+'/o2o/shop/wx/indexinfo.do',senddata).then( (data)=> {
            self.totalpage=data.body.totalpage;
            let goodsList = data.body.fields.goodsList;
            for(let i =0;i<goodsList.length;i++){
              self.goodsItem.push(goodsList[i]);
            }
            done();
          })*/
          this.$http.get('/api/goodsIndex').then((data) => {
            self.totalpage = data.body.data.totalpage;
            let goodsList = data.body.data.fields.goodsList;
            for(let i =0;i<goodsList.length;i++){
              self.goodsItem.push(goodsList[i]);
            }
          })
        }
      },
      onRefresh(done) {
        this.pageno=0;
        console.log("onRefresh")
        this.getlist();

        done() // call done
      },
    },

  }
  function getResult (val) {
    let rs = []
    for (let i = 0; i < 8; i++) {
      rs.push({
        title: `${val} result: ${i + 1} `,
        other: i
      })
    }
    return rs
  }
</script>

<style scoped>
  .text-scroll p{
    font-size: 12px;
    text-align: center;
    line-height: 30px;
  }
  @-webkit-keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translate3d(0, 100%, 0);
    }
    100% {
      opacity: 1;
      transform: none;
    }
  }
  @keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translate3d(0, 100%, 0);
    }
    100% {
      opacity: 1;
      transform: none;
    }
  }
  .fadeInUp {
    animation-name: fadeInUp;
  }
  .swiper-demo-img img {
    width: 100%;
  }
  .weui_flex{
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    background-color: #fff;
    outline: 0;
    -webkit-align-items: center;
    align-items: center;
  }
  .f_flex{
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
  }
  .f_vc{
    -webkit-align-items: center;
    align-items: center;
    -ms-flex-align: center;
  }
  .f_ac{
    -webkit-box-pack: center;
    -webkit-justify-content: center;
    justify-content: center;
  }
  .weui_flex .flex_tab{
    -webkit-box-flex: 1;
    position: relative;
    /*padding: .5em;*/
    width: 0;
    color: #000;
    -webkit-flex: 1;
    -ms-flex: 1;
    flex: 1;
    text-align: center;
  }
  .weui_flex .flex_tab.focus{
    color:#24b8f1
  }
  .weui_flex .flex_tab .arrow{
    display: block;
    border-width: 6px 4px 0;
    border-style: solid;
    border-right-color: transparent;
    border-left-color: transparent;
    width: 0;
    height: 0;
    -webkit-transition: -webkit-transform .3s;
    transition: -webkit-transform .3s;
    transition: transform .3s;
    transition: transform .3s, -webkit-transform .3s;
    -webkit-transform: rotateZ(0);
    transform: rotateZ(0);
  }
  .center_content{
    padding:1.6% 0;
    border-top:1px solid #999;
    border-bottom:1px solid #999;
    overflow: hidden;
    background:#ececec;
  }
  .weui-cell_select .weui-cell__bd:after
</style>

