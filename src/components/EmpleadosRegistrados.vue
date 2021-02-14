<template>
    <div class="row card ew-card ew-grid" style="width: 50%;">
        <div class="card-heading colorf">Empleados registrados en la corrección</div>
    
        <table class="table table-sm table-striped">
            <thead>
                <tr>
                    <th>Codigo</th>
                    <th>Nombre</th>
                    <th>Estado</th>
                    <th></th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="empleado in empleados"  v-bind:key="empleado.id_correccion_empleado">
                    <td>
                        {{ empleado.codigo }}
                    </td>
                    <td>
                        {{ empleado.nombre }}
                    </td>
                    <td>
                        {{ (empleado.estado == 0) ? 'En proceso' : (empleado.estado == 1) ? 'Por aprobar' : 'Aprobado' }}
                    </td>
                    <td style="width: 10%;">
                        <button 
                            v-if="empleado.estado <= 1"
                            class="btn  btn-sm" 
                            :disabled="usuarioPlanilla && empleado.estado == 1"
                            :class="(empleado.estado == 0) ? 'btn-danger' : 'btn-warning' "
                            @click.prevent="setEstado(empleado.id_correccion_empleado, parseInt(empleado.estado) + 1 )">
                            {{ (empleado.estado == 0) ? 'Enviar a coordinador' : 'Culminar corrección' }}
                        </button>
                        <span 
                            v-else 
                            class="btn btn-success btn-sm" >Aprobado</span>
                    </td>
                    <td style="width: 10%;">
                        <button 
                            class="btn btn-primary btn-sm" 
                            @click.prevent="buscarRegistros(empleado.codigo)">
                            Detalles
                        </button>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import axios from 'axios';
export default {
    props: ['correccion', 'usuarioPlanilla'],
    data () {
        return {
            empleados: []
        }
    },
    created() {
        this.empleadosRegistrados();
    },
    methods: {
        empleadosRegistrados() {
            axios.get('vig_correcciones_ajax.php', {
                params: {
                    opc: "c7",
                    correccionId: this.correccion
                }    
            })
            .then(response => {
                if(response.data.codigo == 1)
                {
                    this.empleados = response.data.empleados;
                }
                else
                    this.$toast.error(response.data.mensaje);
                })
            .catch(error => console.log(error))
        },
        buscarRegistros(codigo) {
            this.$emit('busquedaAct', codigo);
        },
        setEstado(correccionEmpleado, newEst) {
            if(confirm('Seguro desea cambiar el estatus de la corrección?')) {
                axios.post('vig_correcciones_ajax.php', {
                    opc: "c8",
                    correccionEmpleadoId: correccionEmpleado,
                    newEstado: newEst
				})
				.then(response => {
                    if(response.data.codigo == 1) {
                        this.$toast.success(response.data.mensaje);
                        this.empleadosRegistrados();
                    }
                    else
                        this.$toast.error(response.data.mensaje);
					})
				.catch(error => console.log(error))
            }
        }
    }
}
</script>

<style scoped>
    .colorf {
        color: #007bff;
        padding-left: 5px;
    }
</style>