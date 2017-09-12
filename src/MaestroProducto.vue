<template>
	<div id="maestro" class="container">
		<div class="row">
			<div class="col">
				<h2 class="titulo">Producto</h2>
				<table v-if="productos && productos.length" class="table table-hover">
					<thead>
					 	<tr class="table-success">
					 		<th>Nombre</th>
					 		<th>Precio</th>
					 		<th>Cantidad</th>
					 	</tr>
				 	</thead>
				 	<tbody>
						<tr v-for="producto of productos" v-on:click="detalle" v-bind:id="producto.Id">
							<td>{{ producto.Nombre }}</td>
							<td>{{ producto.Precio }}</td>
							<td>{{ producto.Cantidad }}</td>
							<!-- <td>{{new Date(producto.Fecha).toLocaleDateString()}}</td> -->
						</tr>
					</tbody>
				</table>
				<input type="button" class="btn btn-success btn-md" id="nuevo" value="Mostrar Detalle" v-on:click="nuevo"/>
			</div>
			<div class="col">
			<div id="form"></div>
			</div>
		</div>
		
	</div>
</template>

<script>
  import axios from 'axios';
  import Formulario from './DetalleProducto.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'
	export default {
	  	name: 'app',
	  	data () {
	   		return {
	    		productos: undefined,
	    		nombreFiltrado:undefined
	    	}
	 	},
	 	methods:{
		  	inicio: function(){
		  		let url = config.address + 'Producto/';
		  		axios.get(url)
			  		.then(response => {
			  			this.productos = response.data;
		  			})
		  	},
		  	detalle: function(evt){
		  		let id = evt.target.id;
		  			id = evt.target.parentNode.id;
		  		this.productos.forEach((e, index) =>{
		  			if(e.Id == id){
		  				new Vue({
		  					el: '#form',
		  					render: h => h(Formulario),
		  					data:{
		  						producto: e
		  					},
		  				});
		  			}
		  		});
		  	},
		  	nuevo: function(evt){
				let pro = {};
		  		pro.Id=-1;
		  		pro.Nombre=undefined;
		  		pro.Marca=undefined;
		  		pro.Fecha=undefined;
		  		pro.Cantidad=undefined;
		  		new Vue({
				el: '#form',
				render: h => h(Formulario),
				data:{
					producto:pro
				},
			});	
	  	}
  	},
  	created() {
      	this.inicio();
    	EventBus.$on('updateProducto', (() => {
        	this.inicio();
    	}));
    }
}
</script>
<style>

</style>