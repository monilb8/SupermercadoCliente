<template>
	<div id="maestro" class="container">
		<div class="row">
			<div class="col">
				<h2 class="titulo">Sección</h2>
				<table v-if="secciones && secciones.length" class="table table-hover">
					<thead>
					 	<tr class="table-success">
					 		<th>Nombre de Sección</th>
					 		<th>Encargado</th>
					 		<th>Fecha de última reposición</th>
					 	</tr>
				 	</thead>
				 	<tbody>
						<tr v-for="seccion of secciones" v-on:click="detalle" v-bind:id="seccion.Id">
							<td>{{ seccion.NombreSeccion }}</td>
							<td >{{ seccion.Encargado }}</td>
							<td>{{new Date(seccion.FechaFrecuenciaStock).toLocaleDateString()}}</td>
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
		  		sec.NombreSeccion=undefined;
		  		sec.Encargado=undefined;
		  		sec.FechaFrecuenciaStock=undefined;
		  		sec.Consumible=false;
		  		sec.GestionStock=undefined;
		  		sec.VentaAlPeso=false;

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

