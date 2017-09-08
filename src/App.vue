<template>
  <div id="home" class="contenedor">
    <div id="tipoProducto" class="menu" v-on:click="abrirComponente">
      Tipo de Producto
    </div>
    <div id="producto" class="menu" v-on:click="abrirComponente">
      Producto
    </div>

    <div id="maestro"></div>
  </div>
</template>

<script>
  import axios from "axios"
  import Vue from 'vue'
  //import MasterTipoProducto from "./MaestroTipoProducto.vue"
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
/*          case "tipoProducto":
            new Vue({
              el: '#maestro',
              render: h => h(MasterTipoProducto)
            })
            break;*/

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
  .contenedor{
    margin: 2%;
    width: 800px;
  }
  .menu {
    padding: 5px;
    display:inline-block;
    width: 33%;
    text-align: center;
    font-weight: bold;
    color: white;
    background-color: #61AC6C; 
    border-radius: 5px;
    border: 2px solid #777777;
    margin-top: 5px;
  }

</style>