<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Sección</h2>
			<div>
				<label>Nombre:</label>
				<input type="text" class="form-control" id="nom" v-model="nombreSeccion" maxlength="40"/>
			</div>
			<div class="form-group">
        		<input type="checkbox" id="destacado" name="destacado" v-model="consumibleSeccion" />
        		<label class="">¿Permitir productos consumibles?</label>
      		</div>

			<div class="botones">
				<input type="button" class="btn btn-outline-success btn-sm" id="btnEnv" value="Insertar" v-on:click="enviar"/>
				<input type="button" class="btn btn-outline-success btn-sm" id="btnAct" value="Actualizar" v-on:click="actualizar"/>
				<input type="button" class="btn btn-outline-success btn-sm" id="btnEli" value="Eliminar" v-on:click="eliminar"/>
				<input type="button" class="btn btn-outline-success btn-sm" id="btnVac" value="Vaciar" v-on:click="nuevo"/>
			</div>
		</div>
	</div>
</template>

<script>
  	import axios from 'axios';
  	import {EventBus} from './EventBus.js'
  	let url = config.address + 'Seccion/';
	export default {
		name: 'app',
		data () {
			return {
		    	seccion:{},
		     	activo: true,
		     	nombreSeccion:undefined,
		     	consumibleSeccion:undefined,
		     	identificador:undefined
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.seccion = {};
		  		this.nombreSeccion=undefined;
		     	this.consumibleSeccion=undefined;
		     	this.identificador=-1;
		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombreSeccion,this.consumibleSeccion);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Nombre: this.nombreSeccion,
					        Consumible: this.consumibleSeccion,
					        Id: this.identificador
				      	}
				        axios.post(url ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updateSeccion", this.data);
				          	swal(
								  '',
								  'Seccion creada!',
								  'success'
							)
				        })
				        this.nuevo();
			        }else{
			        	swal('', mensajeValidacion, 'error');
			        }
		    	}else{
		    		swal('', 'Debes vaciar y volver a rellenar el formulario para insertar.','');
		    	}
			},

			actualizar: function(){
		    	if(this.identificador >0){
		    		let mensajeValidacion = isFormularioValido(this.nombreSeccion,this.consumibleSeccion);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Nombre: this.nombreSeccion,
					        Consumible: this.consumibleSeccion,
					        Id: this.identificador
				      	}
						axios.put(url + data.Id, data)
						.then(response => {
							EventBus.$emit("updateSeccion", this.data);
							swal(
								  '',
								  'Sección actualizada!',
								  'success'
								)
						})
						this.nuevo();
					}else{
						swal('', mensajeValidacion, 'error');
			        }
				}else{
		    		swal('', 'Debes seleccionar una sección para poder actualizar.', '');
		    	}
			},

			eliminar: function(){
		    	if(this.identificador >0){
			    	axios.delete(url + this.identificador)
			        .then(response=> {
			          EventBus.$emit("updateSeccion", response.data);
			        })
					this.nuevo();
				}else{
		    		swal('', 'Debes seleccionar una sección para poder borrar.','');
		    	}
			}
		},
		created() {
    		this.seccion = this.$parent.seccion;
    		this.nombreSeccion = this.seccion.Nombre;
    		this.consumibleSeccion= this.seccion.Consumible;
		    this.identificador=this.seccion.Id;
  		}
	}

	function isFormularioValido(nomSeccion, consuSeccion){
		let mensajeVal='';
		if(nomSeccion == null || nomSeccion=='' || (nomSeccion!=null && nomSeccion.trim()=='')){
			return 'El nombre del producto debe estar relleno.'
		}

		return '';
	}
	
</script>
<style>
	.botones{
		margin-top: 5%;	
	}
</style>