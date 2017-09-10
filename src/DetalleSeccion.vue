<template>
	<div id="form">
		<div v-if="activo" class="detalle">
			<h2>Detalle Sección</h2>
			<div>
				<label>Nombre de Sección:</label>
				<select v-model="nombreSeccion">
				  	<option v-for="optionSec in optionsSec" v-bind:value="optionSec.value">
				    	{{ optionSec.text }}
				 	</option>
				</select>

			</div>
			<div>
				<label>Encargado:</label>
				<input type="text" class="form-control" id="nom" v-model="encargadoSeccion" maxlength="40"/>
			</div>
			<div>
				<label>Frecuencia de Stock:</label>
				<input type="text" class="form-control" id="nom" v-model="gestionStockSeccion" maxlength="40"/>
			</div>
			<div>
				<label>Fecha de última reposición:</label>
				<input type="date" class="form-control" id="fec" name="fecha" v-model="fechaSeccion" />
			</div>
 			<div class="form-group">
        		<input type="checkbox" id="consu" name="consumible" v-model="consumibleSeccion" />
        		<label class="control-label">¿Permitir productos consumibles?</label>
      		</div>
      		<div class="form-group">
		        <label class="control-label">Tipo de Venta:</label>
		        <input type="radio" name="tipo" value="al peso" v-model:value="ventaPesoSeccion"> Peso
		        <input type="radio" name="tipo" value="por unidad"  v-model:value="ventaPesoSeccion"> Unidad
			</div>
<!--       		<div class="form-group">
        		<input type="checkbox" id="peso" name="peso" v-model="ventaPesoSeccion" />
        		<label class="control-label">¿Se vendo al peso?</label>
      		</div> -->



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
		     	encargadoSeccion:undefined,
		     	gestionStockSeccion: undefined,
		     	fechaSeccion: undefined,
		     	consumibleSeccion:undefined,
		     	ventaPesoSeccion: undefined,
		     	identificador:undefined,
		     	optionsSec: [
			      { text: 'Limpieza', value: 'Limpieza' },
			      { text: 'Lacteos', value: 'Lacteos' },
			      { text: 'Reposteria', value: 'Reposteria' }
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
		    		let mensajeValidacion = isFormularioValido(this.nombreSeccion,this.consumibleSeccion);
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
		    		let mensajeValidacion = isFormularioValido(this.nombreSeccion,this.consumibleSeccion);
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