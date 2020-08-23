<template>
  <div class="about">
      <div class="container">
       <div class="row justify-content-center">
        <div class="col-md-10">
         <div class="row">
          <div class="col-md-4 d-flex justify-content-center" v-for="(item,index) in data" :key="index">
         <div class="card" style="width: 18rem;">
            <img class="card-img-top" :src="item.img" alt="Card image cap">
             <div class="card-body">
             <p class="card-title">{{item.title}}</p>
             <p class="card-title">時間 : {{item.time}}</p>
             <div class="text-center">
             <div><button class="btn btn-danger" @click="removelocal(item)">取消收藏</button></div>
             </div>
           </div>
          </div>
        </div>
        </div>
       </div> 
     </div>
   </div>
  </div>
</template>

<script>
export default {
  data(){
    return{
      data:[]
    }
  },
  methods:{
    removelocal(item){
      let vm = this
      vm.data.forEach((localitem , index)=>{
        if (localitem.title == item.title) {
          console.log(index)
          vm.data.splice(index , 1);
          localStorage.setItem('item', JSON.stringify(vm.data))
        }
      })
      
    }
  },
  mounted(){
    let vm = this
    const data = JSON.parse(localStorage.getItem('item'))
    vm.data = data
    console.log(vm.data)
  }
}
</script>
