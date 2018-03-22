<template>
  <div id="app">
    <leftNav></leftNav>
    <div class="main">
       <router-view/>
    </div>
   
  </div>
</template>

<script>
import leftNav from '@/components/common/leftNav'

export default {
  name: 'app',
  components:{
    leftNav
  }
}
</script>

<style>
#app{
  text-align: center
}
.main{
  float: left;
  width: 95%;
  background-color: #Eff2f7;
  height: 100%;
  overflow: hidden;
}
</style>
