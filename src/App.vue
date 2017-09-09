<template>
  <div id="home" class="container"> 
    <div class="row justify-content-center">
      <div class="menu">
        <button id="seccion" type="button" class="btn btn-primary btn-lg" v-on:click="abrirComponente">Sección</button>
        <button id="producto" type="button" class="btn btn-primary btn-lg" v-on:click="abrirComponente">Producto</button>
      </div>
    </div>
    <div id="maestro"></div>
  </div>
</template>

<script>
  import axios from "axios"
  import Vue from 'vue'
  import MasterSeccion from "./MaestroSeccion.vue"
  import MasterProducto from "./MaestroProducto.vue"

  export default {
    name: 'app',
    data () {
      return {
      }
    },
    methods:{
      abrirComponente:function(e){
        switch(e.target.id){
          case "seccion":
            new Vue({
              el: '#maestro',
              render: h => h(MasterSeccion)
            })
            break;

          case "producto":
            new Vue({
              el:'#maestro',
              render: h => h(MasterProducto)
            })
            break;
        }

      }
    }
  }
</script>

<style>
  .menu {
    padding: 5px;
    border-radius: 5px;
    margin-top: 5px;
    margin-bottom: 30px;
  }

</style>