<template>
    <div>
        <div class="d-flex justify-content-between">
			<div v-if="empleado" class="p-2 bd-highlight">
				<p> 
                    Nombre: <strong>{{ empleado }}</strong>  
                    -  <strong>Horas regulares: {{ horasRegulares }}</strong> 
                    -- <strong>Horas extras: {{ horasExtras }}</strong> 
                    - <strong>Estado: </strong> 
                    <span 
                        v-if="correccionDats.estado <= 1"
                        class="btn  btn-sm" 
                        :class="(correccionDats.estado == 0) ? 'btn-danger' : 'btn-warning' ">
                        {{ (correccionDats.estado == 0) ? 'En proceso' : 'Por aprobar' }}
                    </span>
                    <span 
                        v-else 
                        class="btn btn-success btn-sm" >
                        Aprobado
                    </span>
                </p>
			</div>
			<div v-if="empleado && correccionDats.estado == 0" class="p-2 bd-highlight">
				<button 
                    class="btn btn-success"
					@click.prevent="nuevaLinea"> 
					<i class="fa fa-plus" aria-hidden="true"></i>
                    Nueva linea 
                </button>
			</div>
		</div>
        <div class="row card ew-card ew-grid" style="width: 100%;">
            <table class="table table-sm table-striped">
                <thead>
                    <tr>
                        <th>Fecha</th>
                        <th>Cliente</th>
                        <th>Sucursal</th>
                        <th>Zona</th>
                        <th>Puesto</th>
                        <th>Falta</th>
                        <th>HR</th>
                        <th>HE</th>
                        <th>CHR</th>
                        <th>CHE</th>
                        <th>Observacion</th>
                        <th>Falta Pla.</th>
                        <th v-if="correccionDats.estado >= 1">Falta Coo.</th>
                        <th v-if="correccionDats.estado >= 1">CHRS</th>
                        <th v-if="correccionDats.estado >= 1">CHES</th>
                        <th v-if="correccionDats.estado >= 1">Observacion sup.</th>

                        <th v-if="correccionDats.estado <= 1"></th>
                    </tr>
                </thead>
                <tbody>
                    <app-registro 
                        v-for="(correccion, index) in correcciones" 
                        :horasRegulares="horasRegulares"
				        :horasExtras="horasExtras"
                        :correccionDats="correccionDats" 
                        :correccion="correccion" 
                        :index="index" 
                        v-bind:key="correccion.id_correccion_detalle" 
                        :clientes="clientes" 
                        :usuarioPlanilla="usuarioPlanilla" 
                        @eliminarElemento="eliminarElemento"
                        @actualizarHoras="actualizarHoras">
                    </app-registro>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import Registro from './Registro.vue'
export default {
    props: ['correcciones', 'correccionDats', 'clientes', 'empleado', 'empleadoId', 'usuarioPlanilla', 'horasRegulares', 'horasExtras'],
    components: {
        appRegistro: Registro
    },
    methods: {
		nuevaLinea() {
            this.$emit('nuevaLinea');
        },
        eliminarElemento(el) {
            this.correcciones.splice(this.correcciones.indexOf(el), 1);
        },
        actualizarHoras() {
            axios.post('vig_correcciones_ajax.php', {
                opc: 'c12',
                correccionDats: this.correccionDats,
                empleadoId: this.empleadoId
            })
            .then(response => {
                console.log(response.data);
                if(response.data.codigo == 1)
                {
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
    }
}
</script>