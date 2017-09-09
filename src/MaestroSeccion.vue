<template>
	<div id="maestro" class="container">
		<div class="row">
			<div class="col">
				<h2 class="titulo">Sección</h2>
				<table v-if="secciones && secciones.length" class="table table-bordered">
				 	<tr>
				 		<th>Nombre</th>
				 		<th>Consumible</th>
				 	</tr>
					<tr v-for="seccion of secciones" v-on:click="detalle" v-bind:id="seccion.Id">
						<td>{{ seccion.Nombre }}</td>
						<td v-if="seccion.Consumible == true"> Activado </td>
            			<td v-else>Desactivado</td>
					</tr>
				</table>
				<input type="button" class="btn btn-success btn-sm" id="nuevo" value="Mostrar Detalle" v-on:click="nuevo"/>
			</div>
			<div class="col">
			<div id="form"></div>
			</div>
		</div>
		
	</div>
</template>

<script>
  import axios from 'axios';
  import Formulario from './DetalleSeccion.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

	export default {
	  	name: 'app',
	  	data () {
	   		return {
	    		secciones: undefined
	    	}
	 	},
	 	methods:{
		  	inicio: function(){
		  		let url = config.address + 'Seccion/';
		  		axios.get(url)
			  		.then(response => {
			  			this.secciones = response.data;
		  			})
		  	},

		  	detalle: function(evt){
		  		let id = evt.target.id;
		  			id = evt.target.parentNode.id;
		  		this.secciones.forEach((e, index) =>{
		  			if(e.Id == id){
		  				new Vue({
		  					el: '#form',
		  					render: h => h(Formulario),
		  					data:{
		  						seccion: e
		  					},
		  				});
		  			}
		  		});
		  	},
		  	nuevo: function(evt){

				let sec = {};
		  		sec.Id=-1;
		  		sec.Nombre=undefined;
		  		sec.Marca=undefined;
		  		sec.Fecha=undefined;
		  		sec.Cantidad=undefined;

		  		new Vue({
				el: '#form',
				render: h => h(Formulario),
				data:{
					seccion:sec
				},
			});	
	  	}
  	},
  	created() {
      	this.inicio();
    	EventBus.$on('updateSeccion', (() => {
        	this.inicio();
    	}));
    }
}
</script>
<style>

	
</style>

