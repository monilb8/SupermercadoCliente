<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Sección <a class="close" v-on:click="close">&times;</a></h2>
			<label>Nombre de Sección:</label>
			<div>
				<select class="custom-select" v-model="nombreSeccion">
					<option disabled value="">Selecciona uno</option>
				  	<option v-for="optionSec in optionsSec" v-bind:value="optionSec.value">
				    	{{ optionSec.text }}
				 	</option>
				</select>
			</div>
			<br>
			<div>
				<label>Encargado:</label>
				<input type="text" class="form-control" id="nom" v-model="encargadoSeccion" maxlength="40"/>
			</div>
			<br>
			<label>Frecuencia en reponer:</label>
			<div>
				<select class="custom-select" v-model="gestionStockSeccion">
				 	<option disabled value="">Selecciona uno</option>
				  	<option v-for="optionStock in optionsStock" v-bind:value="optionStock.value">
				    	{{ optionStock.text }}
				 	</option>
				</select>
			</div>
			<br>
			<div>
				<label>Fecha de última reposición:</label>
				<input type="date" class="form-control" id="fec" name="fecha" v-model="fechaSeccion" />
			</div>
			<br>
 			<div class="form-group">
        		<input type="checkbox" id="consu" name="consumible" v-model="consumibleSeccion" />
        		<label class="control-label">¿Permitir productos consumibles?</label>
      		</div>
      		<div class="form-group">
		        <label class="control-label">Tipo de Venta:</label>
		        <input type="radio" name="tipo" value="al peso" v-model:value="ventaPesoSeccion"> Peso
		        <input type="radio" name="tipo" value="por unidad"  v-model:value="ventaPesoSeccion"> Unidad
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
  	let url = config.address + 'Seccion/';
	export default {
		name: 'app',
		data () {
			return {
		    	seccion:{},
		     	activo: true,
		     	nombreSeccion:undefined,
		     	encargadoSeccion:undefined,
		     	gestionStockSeccion: undefined,
		     	fechaSeccion: undefined,
		     	consumibleSeccion:undefined,
		     	ventaPesoSeccion: undefined,
		     	identificador:undefined,
		     	optionsSec: [
			      { text: 'Limpieza', value: 'Limpieza' },
			      { text: 'Lácteos', value: 'Lacteos' },
			      { text: 'Repostería', value: 'Reposteria' }
			    ],
			    optionsStock: [
			      { text: 'Dos veces a la semana', value: 'Dos veces a la semana' },
			      { text: 'Una vez a la semena', value: 'Una vez a la semana' },
			      { text: 'Cada dos semanas', value: 'Cada dos semanas' },
			      { text: 'Una vez al mes', value: 'Una vez al mes' },
			      { text: 'Cada dos meses', value: 'Cada dos meses' },
			    ]
		    }
		},
		methods:{
		  	nuevo: function(){
		  		this.seccion = {};
		  		this.nombreSeccion=undefined;
		  		this.encargadoSeccion=undefined;
		     	this.consumibleSeccion=undefined;
		     	this.gestionStockSeccion=undefined;
		  		this.fechaSeccion=undefined;
		     	this.ventaPesoSeccion=undefined;
		     	this.identificador=-1;
		  	},

		  	enviar: function(){
		    	if(this.identificador <0){
		    		let mensajeValidacion = isFormularioValido(this.nombreSeccion,this.encargadoSeccion, this.gestionStockSeccion, this.ventaPesoSeccion, this.fechaSeccion);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        NombreSeccion: this.nombreSeccion,
					        Encargado: this.encargadoSeccion,
					        Consumible: this.consumibleSeccion,
					        FechaFrecuenciaStock: this.fechaSeccion,
					        TipoVenta: this.ventaPesoSeccion,
					        GestionStock: this.gestionStockSeccion,
					        Id: this.identificador
				      	}
				        axios.post(url ,data)
				        .then(response => {
				        	this.identificador = response.data.Id;
				          	EventBus.$emit("updateSeccion", this.data);
				          	this.nuevo();
				          	swal(
								  '',
								  'Seccion creada!',
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
		    		let mensajeValidacion = isFormularioValido(this.nombreSeccion,this.encargadoSeccion, this.gestionStockSeccion, this.ventaPesoSeccion, this.fechaSeccion);
		    		if(mensajeValidacion ==''){
				    	let data = {
					        NombreSeccion: this.nombreSeccion,
					        Encargado: this.encargadoSeccion,
					        Consumible: this.consumibleSeccion,
					        FechaFrecuenciaStock: this.fechaSeccion,
					        TipoVenta: this.ventaPesoSeccion,
					        GestionStock: this.gestionStockSeccion,
					        Id: this.identificador
				      	}
						axios.put(url + data.Id, data)
						.then(response => {
							EventBus.$emit("updateSeccion", this.data);
							this.nuevo();
							swal(
								  '',
								  'Sección actualizada!',
								  'success'
								)
						})
						
					}else{
						swal('', mensajeValidacion, 'error');
			        }
				}else{
		    		swal('', 'Debes seleccionar una sección de la tabla para poder actualizar.', '');
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
			},
			close: function(){
      			this.activo = false;
        		EventBus.$emit("seleccionarId", undefined);
      		},
		},
		created() {
    		this.seccion = this.$parent.seccion;
    		this.nombreSeccion = this.seccion.NombreSeccion;
    		this.encargadoSeccion = this.seccion.Encargado;
    		this.consumibleSeccion= this.seccion.Consumible;
    		if(this.seccion.FechaFrecuenciaStock != null && this.seccion.FechaFrecuenciaStock!= ''){
		    	this.fechaSeccion= this.seccion.FechaFrecuenciaStock.split('T')[0];
			}

    		this.ventaPesoSeccion = this.seccion.TipoVenta;
    		this.gestionStockSeccion= this.seccion.GestionStock;
		    this.identificador=this.seccion.Id;
  		}
	}

	function isFormularioValido(nomSeccion, encSeccion, gesSeccion, tiVeSeccion, feSeccion){
		let mensajeVal='';
		if(nomSeccion == null || nomSeccion=='' || (nomSeccion!=null && nomSeccion.trim()=='')){
			return 'El nombre de la sección debe estar relleno.'
		}
		if(encSeccion == null || encSeccion=='' || (encSeccion!=null && encSeccion.trim()=='')){
			return 'El nombre del encargado de la sección debe estar relleno.'
		}
		if(gesSeccion == null || gesSeccion=='' || (gesSeccion!=null && gesSeccion.trim()=='')){
			return 'El campo de frecuencia de reposición debe estar relleno.'
		}

		let hoy = new Date();
		let d = new Date(feSeccion);
		if(feSeccion == null || feSeccion == '' ){
			return 'La fecha de última reposición no puede estar vacía'
		}
		if(d>hoy){
			return 'La fecha de última reposición no puede ser superior a la fecha de hoy'
		}
		if(tiVeSeccion == null || tiVeSeccion==''){
			return 'El tipo de venta debe estar relleno'
		}

		return '';
	}

</script>
<style>
	.botones{
		margin-top: 5%;	
	}
</style>