<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Producto</h2>
			<div>
				<label>Nombre:</label>
				<input type="text" class="form-control" id="nom" v-model="nombreProducto" maxlength="40"/>
			</div>
			<div>
				<label>Precio:</label>
				<input type="number" class="form-control" id="pre" name="duracion" v-model="precioProducto"/>
			</div>
			<div>
				<label>Fecha:</label>
				<input type="date" class="form-control" id="fec" name="fecha" v-model="fechaProducto" />
			</div>
			<div>
				<label>Cantidad:</label>
				<input type="number" class="form-control" id="cant" name="cantidad" v-model="cantidadProducto" />
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
  	let url = config.address + 'Producto/';
	export default {
		name: 'app',
		data () {
			return {
		    	producto:{},
		     	activo: true,
		     	nombreProducto:undefined,
		     	precioProducto:undefined,
		     	fechaProducto:undefined,
		     	cantidadProducto:undefined,
		     	identificador:undefined
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.producto = {};
		  		this.nombreProducto=undefined;
		     	this.precioProducto=undefined;
		     	this.fechaProducto=undefined;
		     	this.cantidadProducto=undefined;
		     	this.identificador=-1;
		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombreProducto,this.precioProducto,this.fechaProducto, this.cantidadProducto);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Nombre: this.nombreProducto,
					        Precio: this.precioProducto,
					        Fecha: this.fechaProducto,
					        Cantidad: this.cantidadProducto,
					        Id: this.identificador
				      	}
				        axios.post(url ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updateProducto", this.data);
				          	swal(
								  '',
								  'Producto creado!',
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
		    		let mensajeValidacion = isFormularioValido(this.nombreProducto,this.precioProducto,this.fechaProducto, this.cantidadProducto);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        Nombre: this.nombreProducto,
					        Precio: this.precioProducto,
					        Fecha: this.fechaProducto,
					        Cantidad: this.cantidadProducto,
					        Id: this.identificador
				      	}
						axios.put(url + data.Id, data)
						.then(response => {
							EventBus.$emit("updateProducto", this.data);
							swal(
								  '',
								  'Producto actualizado!',
								  'success'
								)
						})
						this.nuevo();
					}else{
						swal('', mensajeValidacion, 'error');
			        }
				}else{
		    		swal('', 'Debes seleccionar un producto para poder actualizar.', '');
		    	}
			},

			eliminar: function(){
		    	if(this.identificador >0){
			    	axios.delete(url + this.identificador)
			        .then(response=> {
			          EventBus.$emit("updateProducto", response.data);
			        })
					this.nuevo();
				}else{
		    		swal('', 'Debes seleccionar un producto para poder borrar.','');
		    	}
			}
		},
		created() {
    		this.producto = this.$parent.producto;
    		this.nombreProducto = this.producto.Nombre;
    		this.precioProducto= this.producto.Precio;
    		if(this.producto.Fecha != null && this.producto.Fecha!= ''){
		    	this.fechaProducto= this.producto.Fecha.split('T')[0];
			}
		    this.cantidadProducto= this.producto.Cantidad;
		    this.identificador=this.producto.Id;
  		}
	}

	function isFormularioValido(nomProducto, preProducto, fechProducto, cantProducto){
		let mensajeVal='';
		if(nomProducto == null || nomProducto=='' || (nomProducto!=null && nomProducto.trim()=='')){
			return 'El nombre del producto debe estar relleno.'
		}
		if(cantProducto<=0){
			return 'La cantidad de producto debe ser mayor que cero'
		}
		let mensaje = esEntero(preProducto)
		if(mensaje!= ''){
			return 'Campo Precio: '+ mensaje;
		}

		return '';
	}
	function esEntero(numero){
	    if (numero % 1 == 0 && numero > 0) {
	        return '';
	    }
	    else{
	        return 'Debes introducir un numero entero y positivo.';
	    }
	}
</script>
<style>
	.botones{
		margin-top: 5%;	
	}
</style>