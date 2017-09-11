<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Producto <a class="close" v-on:click="close">&times;</a></h2>
			<label>Sección:</label>
			<div>
				<select class="custom-select" v-model="seccion">
					<option v-for="optionSec in optionsSec" v-bind:value="optionSec.value">
				   	{{ optionSec.text }}
				 	</option>
				</select>
			</div>
			<br>
			<div>
				<label>Nombre:</label>
				<input type="text" class="form-control" id="nom" v-model="nombreProducto" maxlength="40"/>
			</div>
			<br>
			<label>Precio:</label>
			<div class="input-group">
				<input type="number" class="form-control" id="pre" name="duracion" v-model="precioProducto"/>
				<span class="input-group-addon">€</span>
			</div>
			<br>
			<div>
				<label>Fecha de caducidad:</label>
				<input type="date" class="form-control" id="fec" name="fecha" v-model="fechaProducto" />
			</div>
			<br>
			<div>
				<label>Cantidad:</label>
				<input type="number" class="form-control" id="cant" name="cantidad" v-model="cantidadProducto" />
			</div>
			<br>
			<div>
				<label>Peso:</label>
				<input type="number" class="form-control" id="pes" name="peso" v-model="pesoProducto" />
			</div>
			<br>
			<div>
				<label>Número de unidades:</label>
				<input type="number" class="form-control" id="uni" name="unidades" v-model="numUnidadProducto" />
			</div>
			<br>
			<div class="form-group">
        		<input type="checkbox" id="ofer" name="oferta" v-model="ofertaProducto" />
        		<label>¿Permitir oferta en el producto?</label>
      		</div>
			<div class="botones">
				<input type="button" class="btn btn-outline-success btn-md" id="btnEnv" value="Insertar" v-on:click="enviar"/>
				<input type="button" class="btn btn-outline-success btn-md" id="btnAct" value="Actualizar" v-on:click="actualizar"/>
				<input type="button" class="btn btn-outline-success btn-md" id="btnEli" value="Eliminar" v-on:click="eliminar"/>
				<input type="button" class="btn btn-outline-success btn-md" id="btnVac" value="Vaciar" v-on:click="nuevo"/>
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
		     	seccion:undefined,
		     	nombreProducto:undefined,
		     	precioProducto:undefined,
		     	fechaProducto:undefined,
		     	cantidadProducto:undefined,
		     	identificador:undefined,
		     	pesoProducto:undefined,
		     	ofertaProducto:undefined,
		     	numUnidadProducto:undefined,
		     	optionsSec: [
			      { text: 'Limpieza', value: 'Limpieza' },
			      { text: 'Lácteos', value: 'Lacteos' },
			      { text: 'Repostería', value: 'Reposteria' }
			    ]
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.producto = {};
		  		this.seccion=undefined;
		  		this.nombreProducto=undefined;
		     	this.precioProducto=undefined;
		     	this.fechaProducto=undefined;
		     	this.cantidadProducto=undefined;
		     	this.pesoProducto=undefined;
		     	this.ofertaProducto=undefined;
		     	this.numUnidadProducto=undefined;
		     	this.identificador=-1;
		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombreProducto,this.precioProducto,this.fechaProducto, this.cantidadProducto);
		    		if(mensajeValidacion ==''){
				    	let data = {
				    		Seccion: this.seccion,
					        Nombre: this.nombreProducto,
					        Precio: this.precioProducto,
					        Fecha: this.fechaProducto,
					        Cantidad: this.cantidadProducto,
					        Peso: this.pesoProducto,
					        Oferta: this.ofertaProducto,
					        NumUnidades: this.numUnidadProducto,
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
				    		Seccion: this.seccion,
					        Nombre: this.nombreProducto,
					        Precio: this.precioProducto,
					        Fecha: this.fechaProducto,
					        Cantidad: this.cantidadProducto,
					        Peso: this.pesoProducto,
					        Oferta: this.ofertaProducto,
					        NumUnidades: this.numUnidadProducto,
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
			},
			close: function(){
      			this.activo = false;
        		EventBus.$emit("seleccionarId", undefined);
      		},
		},
		created() {
    		this.producto = this.$parent.producto;
    		this.seccion = this.producto.Seccion;
    		this.nombreProducto = this.producto.Nombre;
    		this.precioProducto= this.producto.Precio;
    		if(this.producto.Fecha != null && this.producto.Fecha!= ''){
		    	this.fechaProducto= this.producto.Fecha.split('T')[0];
			}
		    this.cantidadProducto= this.producto.Cantidad;
		    this.pesoProducto= this.producto.Peso;
		    this.ofertaProducto= this.producto.Oferta;
		    this.numUnidadProducto= this.producto.NumUnidades;
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