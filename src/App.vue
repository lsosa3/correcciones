<template>
	<div class="container-fluid">
		<app-buscador  
			@busquedaAct="buscarRegistros" 
			@busquedaEmp="busquedaEmpleados"
			:usuarioPlanilla="usuarioPlanilla">
		</app-buscador>
		
		<transition name="fade" mode="out-in" >
			<component 
				:is="mode" 
				:correcciones="correcciones" 
				:correccionDats="correccionDats" 
				:correccion="correccion" 
				:clientes="clientes" 
				:empleado="empleado"
				:empleadoId="empleadoId"
				:usuarioPlanilla="usuarioPlanilla"
				:horasRegulares="horasRegulares"
				:horasExtras="horasExtras"
				@busquedaAct="buscarRegistros" 
				@nuevaLinea="nuevaLinea">
			</component>
		</transition>
	</div>
</template>

<script>
	import Buscador from './components/Buscador.vue';
	import RegistrosGrid from './components/RegistrosGrid.vue';
	import EmpleadosRegistros from './components/EmpleadosRegistrados.vue'
	import axios from 'axios';

	export default {
		data () {
			return {
				correcciones: [],
				clientes: [],
				correccion: document.getElementById('correccion').value,
				empleado: '',
				empleadoId: 0,
				correccionDats: [],
				usuarioPlanilla: false,
				mode: '',
				horasRegulares: 0,
				horasExtras: 0
			}
		},
		created() {
			this.tipoUsuario();
		},
		methods: {
			buscarRegistros(codigo) {
				this.correcciones = [];
				this.empleado = '';
				this.empleadoId = 0;
				this.mode = 'app-registros-grid';
				let valid = true;
				if(this.correcciones.length)
					valid = confirm("¿Esta seguro de haber guardado todos sus cambios?");
				else
					valid = true;
				if(valid) {
					axios.get('vig_correcciones_ajax.php', {
						params: {
							opc: 'c3',
							correccionId: this.correccion
						}
					})
					.then(response => {
						
						if(response.data.codigo == 1)
						{
							this.clientes = response.data.clientes;
						}
						else
						{
							this.$toast.error(response.data.mensaje);
						}
					})
					.catch(error => console.log(error))

					axios.get('vig_correcciones_ajax.php', {
						params: {
							opc: 'c1',
							codigo: codigo,
							correccionId: this.correccion
						}
					})
					.then(response => {
						if(response.data.codigo == 1)
						{
							this.correcciones = response.data.asistencia;
							this.empleado = response.data.empleado.codigo + '  ' + response.data.empleado.nombre + ' -- ' + response.data.empleado.tipo_empleado;
							this.empleadoId = response.data.empleado.id_empleado;
							this.correccionDats = response.data.correccionDats;
							this.horasRegulares = response.data.horas_regulares;
							this.horasExtras = response.data.horas_extras;
						}
						else
						{
							this.$toast.error(response.data.mensaje);
						}
					})
					.catch(error => console.log(error))
				}
			},
			busquedaEmpleados() {
				this.mode = 'app-empleados-registros';
				this.empleado = '';
			},
			nuevaLinea() {
				const nuevaLinea = {
					fecha: '',
					cliente: '',
					sucursal: '',
					sucursalId: '',
					puesto: '',
					id_asistencia: '',
					id_empleado: this.empleadoId,
					id_puesto: '',
					codigo: '',
					nombre: '',
					horas_regulares: '0.00',
					horas_extras: '0.00',
					horas_regulares_correccion: '',
					horas_extras_correccion: '',
					observacion: '',
					tipo_hora: '',
					zona: '',
					inasistencia: '',
					id_correccion_detalle: ''
				};
				this.correcciones.push(nuevaLinea);
			},
			tipoUsuario() {
				axios.get('vig_correcciones_ajax.php', {
						params: {
							opc: 'c10'
						}
					})
					.then(response => {
						this.usuarioPlanilla = response.data.usuarioPlanilla;
					})
					.catch(error => console.log(error))
			}
		},
		components: {
			appBuscador: Buscador,
			appRegistrosGrid: RegistrosGrid,
			appEmpleadosRegistros: EmpleadosRegistros
		}
	}
	window.onbeforeunload = () => ( confirm("Seguro"));
</script>

<style scoped>
	.cont {
		padding: 30px;
	}

	.fade-enter-active, .fade-leave-active {
		transition: opacity .7s;
	}
	.fade-enter, .fade-leave-to {
		opacity: 0;
	}

	@keyframes fade-in {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}

	@keyframes fade-out {
		from {
			opacity: 1;
		}
		to {
			opacity: 0;
		}
	}
</style>
