<template>
<ul v-loading="loading">
  <li
  v-for="(item,index) of list"
  :key="index"
  >
    <span v-show="item.level===3" style="color:#ff5964">!!!!</span>
    <span v-show="item.level===2" style="color:#a2d5f2">!!!</span>
    <span v-show="item.level===1" style="color:#ffda77">!!</span>
    <span v-show="item.level===0" style="color:#1bd1a5">!</span>
    <el-checkbox v-model="item.complete" :disabled="item.complete" @click="comp(item.id)">{{item.title}}</el-checkbox>
  </li>
</ul>
</template>

<script>
import {defineComponent, ref, reactive, toRefs, onMounted} from 'vue'
import List from '../lib/API/ListAPI'
import swal from '../lib/swal'
export default defineComponent({
  name: '',
  components: {

  },
  props:{
  },
  setup(props, ctx){
    let list = reactive([]);
    let comp = (index)=>{
      List.editList(index).then(res=>{
        if(res.data.code === 200){
          swal.Success('已完成');
          List.getList(1,10).then(res=>{
          console.log(res);
          for(var i = 0;i<res.data.data.length;i++){
            list[i] = res.data.data[i];
            list[i].complete = Boolean(res.data.data[i].complete);
            list[i].title = res.data.data[i].title;
          }
        })
        }
      })
    }
    let loading = ref(true);
    List.getList(1,10).then(res=>{
      console.log(res);
      res.data.data.sort(function(a, b){return a.complete - b.complete});
      res.data.data.sort(function(a, b){return b.level - a.level});
      for(var i = 0;i<res.data.data.length;i++){

        list[i] = res.data.data[i];
        list[i].complete = Boolean(res.data.data[i].complete);
        list[i].title = res.data.data[i].title;
      }
      loading.value = false;
    })
    
    return{
      list,
      comp
    }
  }
})
</script>

<style scoped>
ul,li{
  list-style: none;
  font-size: 24px;
}
ul li span{
  font-size: 15px;
  display: inline-block;
  width: 20px;
  text-align: center;
  padding-right: 10px;
}
</style>