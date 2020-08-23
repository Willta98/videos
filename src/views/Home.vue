<template>
  <div>
  <div class="container">
   <div class="row justify-content-center">
     <div class="col-md-10">
       <div class="row">
        <div class="col-md-4 d-flex justify-content-center" v-for="(item,index) in pageOfItems" :key="index">
         <div class="card" style="width: 18rem;">
            <img class="card-img-top" :src="item.img" alt="Card image cap">
             <div class="card-body">
             <p class="card-title">{{item.title}}</p>
             <p class="card-title">時間 : {{item.time}}</p>
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
            <pagination :items="exampleItems" @changePage="onChangePage" :styles="customStyles" :labels="customLabels"></pagination>
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
      items:[],
      itemsa:[],
      timer:[],
      arraytime:[],
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
   get() {
      const vm = this;
      let urla = `https://www.googleapis.com/youtube/v3/videos?chart=mostPopular&key=AIzaSyDVD4p8eyb0jUbL6uJS9Sr_byz8t9n8aVo&part=snippet,contentDetails&maxResults=50&pageToken=CBQQAA`;
      let url = `https://www.googleapis.com/youtube/v3/videos?chart=mostPopular&key=AIzaSyDVD4p8eyb0jUbL6uJS9Sr_byz8t9n8aVo&part=snippet,contentDetails&maxResults=100`;
      
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
        vm.timer = vm.items.map((item) => {
           return item.contentDetails.duration
        });
        vm.timer.forEach((item) => {
          item = item.split('')
           for (let i = 0; i < item.length; i++) {
            if (item[i] == "P") {
            item.splice(i, 1);
             if(item[i] == "T"){
              item.splice(i, 1);
              }
            }
          } 
            item = item.join('')
            vm.arraytime.push(item)
        });
          for (let i = 0; i < vm.id.length; i++) {
            let obj = {
              title : vm.title[i],
              time : vm.arraytime[i],
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
  },
  created() {
    this.get()
  },
}
</script>


