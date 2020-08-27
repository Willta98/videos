<template>
  <div>
    <div id="loading">
      <img
        src="https://thumbs.gfycat.com/SoggyFoolhardyCopperbutterfly-small.gif"
        alt="loading.."
        width="300"
      />
    </div>
  <div class="container">
   <div class="row justify-content-center">
     <div class="col-md-10">
       <div class="row">
        <div class="col-md-4 d-flex justify-content-center mb-5" v-for="(item,index) in pageOfItems" :key="index">
         <div class="card" style="width: 18rem;">
           <a href="javascript:void(0)" @click="getdetail(item)">
            <img class="card-img-top" :src="item.img" alt="Card image cap">
            </a>
             <div class="card-body">
             <p class="card-title">{{item.title}}</p>
             <div class="text-center">
             <div><button class="btn btn-primary" @click="setlocal(item)" v-if="item.favItem === false">收藏</button></div>
             <div><button class="btn btn-danger" @click="remove(item)" v-if="item.favItem === true">取消收藏</button></div>
             </div>
           </div>
          </div>
        </div>
        </div>
       </div> 
     </div>
       <div class="card-footer pb-0 pt-3">
          <a href="#">
            <pagination :items="exampleItems" @changePage="onChangePage" 
            :styles="customStyles" :labels="customLabels"  v-if="exampleItems.length>3"></pagination>
            </a>
        </div>
   </div>    
  </div>
</template>

<script>
import $ from "jquery";
import pagination from "./jw-pagination"

const customLabels = {
    first: '<<',
    last: '>>',
    previous: '<',
    next: '>'
};

const customStyles = {
    li: {
        display: 'inline-block',
        "border-radius": "5px",
        margin: "1px"
    },
};
export default {
   components:{
       pagination
     },
  data(){
    return{
      searchid:"",
      items:[],
      itemsa:[],
      title:[],
      id:[],
      img:[],
      alldata:[],
      local:JSON.parse(localStorage.getItem("item") || '[]'),
      customLabels,
      customStyles,
      exampleItems:[
              {name:"abc", id:1},
              {name:"abc", id:2},
              {name:"abc", id:3},
            ],
      pageOfItems: []
    }
  },
  methods:{
   putid(){

      },
   get() {
      const vm = this;
      let urla = `https://www.googleapis.com/youtube/v3/videos?chart=mostPopular&key=AIzaSyDVD4p8eyb0jUbL6uJS9Sr_byz8t9n8aVo&part=snippet,contentDetails&maxResults=50&pageToken=CBQQAA`;
      let url = `https://www.googleapis.com/youtube/v3/videos?chart=mostPopular&key=AIzaSyDVD4p8eyb0jUbL6uJS9Sr_byz8t9n8aVo&part=snippet,contentDetails&maxResults=50`;
      vm.$http.get(urla).then((response) =>{
        vm.itemsa = response.data.items  
      vm.$http.get(url).then((response) => {
        vm.items = response.data.items
        vm.items = vm.items.concat(vm.itemsa);
        vm.items.forEach(item => {
          vm.title.push(item.snippet.title)
          vm.id.push(item.id)
          vm.img.push(item.snippet.thumbnails.medium.url)
          });
          for (let i = 0; i < vm.id.length; i++) {
            let obj = {
              title : vm.title[i],
              id : vm.id[i],
              img : vm.img[i],
              favItem : false
            }
            vm.alldata.push(obj)
         }

        vm.exampleItems = vm.alldata

        const localdata = JSON.parse(localStorage.getItem('item') || '[]');
        let localtitle = '';
        localdata.forEach((item) => {
          localtitle = item.title;
          vm.alldata.forEach((items) => {
            if (localtitle === items.title) {
              const itemt = items;
              itemt.favItem = true;
            }
          });
        });
         $('#loading').css('display', 'none');
      });
      })
   },
   setlocal(items) {
      items.favItem = true
      console.log(items.favItem) 
      let vm = this ; 
      let item = {
        id : items.id,
        title: items.title,
        img: items.img,
        time: items.time,
        favItem: items.favItem
      };
      vm.local.push(item);
      localStorage.setItem('item',JSON.stringify(vm.local))
   },
   remove(items){
      items.favItem = false
      let vm = this
      let index = vm.local.findIndex(item => {
          return items.title == item.title
        });
       vm.local.splice(index, 1);
       localStorage.setItem('item', JSON.stringify(vm.local))
   },
   onChangePage(pageOfItems) {
            let vm = this
            
            vm.pageOfItems = pageOfItems;
     },
   getdetail(item){
      const vm = this;
      vm.$router.push(`/${item.id}`);
   }
  },
  created() {
    this.get()
  },
}
</script>


<style lang="scss">
  #loading {
    position: fixed;
    z-index: 400;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0%;
    text-align: center;
    font-size: 0.9rem;
    color: #595758;
    background-color: white;
    opacity: 0.9;
    // display: none;
}
</style>



