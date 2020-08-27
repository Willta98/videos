<template>
    <div>
      <div v-for="(item,index) in list" :key="index">
          <h1>{{ item.snippet.title}}</h1>
          <iframe width="853" height="480" :src="src"
           frameborder="0" allow="accelerometer; autoplay; encrypted-media; 
           gyroscope; picture-in-picture" allowfullscreen></iframe>
      </div>
    </div>
</template>
<script>
import $ from 'jquery';
export default {
    data(){
        return{
          list : [],
          src: "",
        }
    },
    methods:{
      get(){
        let vm = this;
        let url = `https://www.googleapis.com/youtube/v3/videos?id=${vm.Id}&key=AIzaSyDVD4p8eyb0jUbL6uJS9Sr_byz8t9n8aVo&part=snippet,contentDetails,statistics,status`
        vm.$http.get(url).then((response) => {
            vm.list = response.data.items;
            vm.src = `https://www.youtube.com/embed/${vm.Id}`
            console.log(vm.list)
        });
      }
    },
    mounted(){
        var vm = this
        vm.Id = vm.$route.params.Id;
        vm.get()
    }
}
</script>