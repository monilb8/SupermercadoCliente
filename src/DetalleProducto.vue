<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Producto <button class="btn btn-danger btn-sm"><a class="close" v-on:click="close">&times;</a></button></h2>
			<label>Sección:</label>
			<div>
				<select class="custom-select" v-model="seccion" v-on:change="ocultar">
					<option disabled value="">Selecciona uno</option>
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
				<input type="date" class="form-control" id="fec" name="fecha" v-model="fechaProducto" :disabled="tieneFechaCad == false" />
			</div>
			<br>
			<div >
				<label>Cantidad:</label>
				<input type="number" class="form-control" id="cant" name="cantidad" v-model="cantidadProducto"  :disabled="esPorPeso == true" placeholder="Introduce número de unidades" />
			</div>
			<br>
			<label>Peso:</label>
			<div class="input-group">
				<input type="number" class="form-control" id="pes" name="peso" v-model="pesoProducto"  :disabled="esPorCantidad == true" placeholder="Introduce el peso en gramos"/>
				<span class="input-group-addon">g</span>
			</div>
			<br>
			<div class="form-group">
        		<input type="checkbox" id="ofer" name="oferta" v-model="ofertaProducto" />
        		<label>¿Permitir oferta en el producto?</label>
      		</div>
			<div class="botones">
				<input type="button" class="btn btn-outline-primary btn-md" id="btnEnv" value="Insertar" v-on:click="enviar"/>
				<input type="button" class="btn btn-outline-success btn-md" id="btnAct" value="Actualizar" v-on:click="actualizar"/>
				<input type="button" class="btn btn-outline-danger btn-md" id="btnEli" value="Eliminar" v-on:click="eliminar"/>
				<input type="button" class="btn btn-outline-warning btn-md" id="btnVac" value="Vaciar" v-on:click="nuevo"/>
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
		     	esPorPeso: false,
		     	esPorCantidad: false,
		     	tieneFechaCad: true,
		     	seccion:undefined,
		     	nombreProducto:undefined,
		     	precioProducto:undefined,
		     	fechaProducto:undefined,
		     	cantidadProducto:undefined,
		     	identificador:undefined,
		     	pesoProducto:undefined,
		     	ofertaProducto:undefined,
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
		     	this.identificador=-1;
		     	this.esPorPeso=false;
		     	this.esPorCantidad=false;
		     	this.tieneFechaCad=true;
		  	},
		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombreProducto,this.precioProducto,this.fechaProducto, this.cantidadProducto, this.seccion, this.pesoProducto);
		    		if(mensajeValidacion ==''){
		    			let cantidadAux='0';
		    			let pesoAux='0';
		    			let fechaAux=new Date();
		    			fechaAux= '01/01/3000';
		    			if(this.seccion=='Limpieza'){
		    				cantidadAux=this.cantidadProducto;
		    			}else if(this.seccion=='Reposteria'){
		    				pesoAux=this.pesoProducto;
		    				fechaAux=this.fechaProducto
		    			}else if(this.seccion=='Lacteos'){
		    				cantidadAux=this.cantidadProducto;
		    				fechaAux=this.fechaProducto
		    			}
				    	let data = {
				    		Seccion: this.seccion,
					        Nombre: this.nombreProducto,
					        Precio: this.precioProducto,
					        Fecha: fechaAux,
					        Cantidad: cantidadAux,
					        Peso: pesoAux,
					        Oferta: this.ofertaProducto,
					        Id: this.identificador
				      	}
				        axios.post(url ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updateProducto", this.data);
				          	this.nuevo();
				          	swal(
								  '',
								  'Producto creado!',
								  'success'
							)
				        })
			        }else{
			        	swal('', mensajeValidacion, 'error');
			        }
		    	}else{
		    		swal('', 'Debes vaciar y volver a rellenar el formulario para insertar.','');
		    	}
			},
			actualizar: function(){
		    	if(this.identificador >0){
		    		let mensajeValidacion = isFormularioValido(this.nombreProducto,this.precioProducto,this.fechaProducto, this.cantidadProducto, this.seccion, this.pesoProducto);
		    		if(mensajeValidacion ==''){
				    	
						let cantidadAux='0';
		    			let pesoAux='0';
		    			let fechaAux=new Date();
		    			fechaAux= '01/01/3000';
		    			if(this.seccion=='Limpieza'){
		    				cantidadAux=this.cantidadProducto;
		    			}else if(this.seccion=='Reposteria'){
		    				pesoAux=this.pesoProducto;
		    				fechaAux=this.fechaProducto
		    			}else if(this.seccion=='Lacteos'){
		    				cantidadAux=this.cantidadProducto;
		    				fechaAux=this.fechaProducto
		    			}
		    			
				    	let data = {
				    		Seccion: this.seccion,
					        Nombre: this.nombreProducto,
					        Precio: this.precioProducto,
					        Fecha: fechaAux,
					        Cantidad: cantidadAux,
					        Peso: pesoAux,
					        Oferta: this.ofertaProducto,
					        Id: this.identificador
				      	}
						axios.put(url + data.Id, data)
						.then(response => {
							EventBus.$emit("updateProducto", this.data);
							this.nuevo();
							swal(
								  '',
								  'Producto actualizado!',
								  'success'
								)
						})
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
			    	swal({
			            title: '¿Quieres eliminar el producto?',
			            type: 'warning',
			            showCancelButton: true,
			            confirmButtonColor: '#3085d6',
			            cancelButtonColor: '#d33',
			            confirmButtonText: 'Si, eliminar!',
			            cancelButtonText: 'Cancelar'
			        })
			        .then(response=> {
			          EventBus.$emit("updateProducto", response.data);
			          swal(
						  '',
						  'Producto Eliminado!',
						  'success'
						)
			        })
					this.nuevo();
				}else{
		    		swal('', 'Debes seleccionar un producto para eliminar.','');
		    	}
			},
			close: function(){
      			this.activo = false;
        		EventBus.$emit("seleccionarId", undefined);
      		},
      		ocultar: function(){
      			this.fechaProducto='';
		     	this.cantidadProducto='';
		     	this.pesoProducto='';
      			if(this.seccion=='Limpieza'){
      				this.esPorCantidad=true;
      				this.esPorPeso=false;
      				this.tieneFechaCad=false;
      			}else if(this.seccion=='Lacteos'){
					this.esPorCantidad=true;
      				this.esPorPeso=false;
      				this.tieneFechaCad=true;
      			}else if(this.seccion=='Reposteria'){
      				this.esPorCantidad=false;
      				this.esPorPeso=true;
      				this.tieneFechaCad=true;
      			}
      		},
		},
		created() {
    		this.producto = this.$parent.producto;
    		this.seccion = this.producto.Seccion;
    		this.nombreProducto = this.producto.Nombre;
    		this.precioProducto= this.producto.Precio;
    		if(this.producto.Fecha != null && this.producto.Fecha!= '' && this.seccion != 'Limpieza'){
		    	this.fechaProducto= this.producto.Fecha.split('T')[0];
			}else{
				this.fechaProducto='';
			}
			if(this.producto.Cantidad!= null && this.producto.Cantidad!='0'){
		    	this.cantidadProducto= this.producto.Cantidad;
			}else{
				this.cantidadProducto='';
			}
			if(this.producto.Peso!= null && this.producto.Peso!='0'){
		    	this.pesoProducto= this.producto.Peso;
			}else{
				this.pesoProducto='';
			}
		    this.ofertaProducto= this.producto.Oferta;
		    this.identificador=this.producto.Id;
			if(this.seccion=='Lacteos'){
  				this.esPorCantidad=true;
  				this.esPorPeso=false;
  				this.tieneFechaCad=true;
  			}else if(this.seccion=='Reposteria'){
				this.esPorCantidad=false;
  				this.esPorPeso=true;
  				this.tieneFechaCad=true;
  			}else if(this.seccion=='Limpieza'){
  				this.esPorCantidad=true;
  				this.esPorPeso=false;
      			this.tieneFechaCad=false;
      		}
  		}
	}
	function isFormularioValido(nomProducto, preProducto, fechProducto, cantProducto, nomSeccion, pesoProducto){
		let mensajeVal='';
		if(nomSeccion == null || nomSeccion=='' || (nomSeccion!=null && nomSeccion.trim()=='')){
			return 'El nombre de la sección debe estar relleno.'
		}
		if(nomProducto == null || nomProducto=='' || (nomProducto!=null && nomProducto.trim()=='')){
			return 'El nombre del producto debe escodProductotar relleno.'
		}
		if(preProducto == null || preProducto <= 0){
			return 'El precio del producto debe ser mayor que cero'
		}
		if(nomSeccion != 'Reposteria'){
			if(cantProducto <= 0){
				return 'La cantidad del producto debe ser mayor que cero'
			}else if(cantProducto % 1 && cantProducto != 0){
				return 'Debes introducir un número entero';
			}
		}
		let hoy = new Date();
		let d = new Date(fechProducto);
		if(nomSeccion != 'Limpieza'){
			if(fechProducto == null || fechProducto == '' ){
				return 'La fecha de caducidad no puede estar vacía'
			}else if(d<hoy){
				return 'La fecha caducidad debe ser mayor que la fecha de hoy';
			}
		}
		if(nomSeccion != 'Lacteos' && nomSeccion != 'Limpieza'){
			if(pesoProducto <= 0){
				return 'El peso del producto debe ser mayor que cero'
			}
		}
		return '';
	}
</script>
<style>
	.botones{
		margin-top: 5%;	
	}
</style>