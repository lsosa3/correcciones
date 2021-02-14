<template>
        <tr :class="correccion.id_asistencia == 0 ? 'agregado' : ''">
            <!-- <th scope="row">{{ correccion.index }}</th> -->
            <td v-if="correccion.id_asistencia > 0 || correccionDats.estado > 0" style="width: 100px;">{{ correccion.fecha }}</td>
            <td v-else >
                <input type="date" class="form-control input-short2" v-model="correccion.fecha">
            </td>
            <td v-if="correccion.id_asistencia > 0 || correccionDats.estado > 0" class="input-short3">{{ correccion.cliente }}</td>
            <td v-else >
                <select @change="chCliente" v-model="correccion.clienteId" class="form-control input-short3" >
                    <option v-for="client in clientes" v-bind:key="client.id" v-bind:value="client.id" > {{ client.descripcion }} </option>
                </select>
            </td>
            <td v-if="correccion.id_asistencia > 0 || correccionDats.estado > 0" style="width: 180px;">{{ correccion.sucursal }}</td>
            <td v-else >
                <select @change="chSucursal" v-model="correccion.sucursalId" class="form-control input-short4" >
                    <option v-for="sucursal in sucursales" v-bind:key="sucursal.id" v-bind:value="sucursal.id" > {{ sucursal.descripcion }} </option>
                </select>
            </td>
            <td style="width: 80px;">{{ correccion.zona }}</td>
            <td v-if="correccion.id_asistencia > 0 || correccionDats.estado > 0" style="width: 250px;">{{ correccion.puesto }}</td>
            <td v-else >
                <select v-model="correccion.id_puesto" class="form-control input-short5" >
                    <option v-for="puesto in puestos" v-bind:key="puesto.id" v-bind:value="puesto.id" > {{ puesto.descripcion }} </option>
                </select>
            </td>
            <td>{{ correccion.inasistencia_asis == 1 ? 'Si' : 'No' }}</td>
            <td>{{ correccion.horas_regulares }}</td>
            <td>{{ correccion.horas_extras }}</td>
            <td v-if="correccionDats.estado == 0"><input type="number" class="form-control input-short" v-model.number="correccion.horas_regulares_correccion"></td>
            <td v-else>{{ correccion.horas_regulares_correccion }}</td>
            <td v-if="correccionDats.estado == 0"><input type="number" class="form-control input-short" v-model.number="correccion.horas_extras_correccion"></td>
            <td v-else>{{ correccion.horas_extras_correccion }}</td>
            <td v-if="correccionDats.estado == 0"><input type="text" class="form-control" v-model="correccion.observacion"></td>
            <td v-else>{{ correccion.observacion }}</td>

            <td v-if="correccionDats.estado == 0"><input type="checkbox" id="checkbox" v-model="checkedInasisPla"></td>
            <td v-else>{{ correccion.inasistencia == 1 ? 'Si' : correccion.inasistencia == 0 ? 'No' : '--' }}</td>
            <!-- CAMPOS DEL COORDINADOR -->
            <td v-if="correccionDats.estado == 1 && !usuarioPlanilla"><input type="checkbox" id="checkbox" v-model="checkedInasisCoo" :disabled="correccionDats.estado > 1" ></td>
            <td v-else-if="correccionDats.estado == 2 || (correccionDats.estado == 1 && usuarioPlanilla)">{{ correccion.inasistencia_coo == 1 ? 'Si' : 'No' }}</td>
            <!-- HORAS REGULARES COORDINADOR -->
            <td v-if="(correccionDats.estado == 1 && !usuarioPlanilla && correccion.id_correccion_detalle != '' && correccion.id_correccion_detalle != undefined && (correccion.horas_regulares_correccion != '0.00' || correccion.horas_extras_correccion != '0.00')) || (correccionDats.estado == 1 && !usuarioPlanilla && correccion.horas_regulares_correccion == '0.00' && correccion.horas_extras_correccion == '0.00' && correccion.id_asistencia == 0) || (correccionDats.estado == 1 && !usuarioPlanilla && correccion.horas_regulares_correccion == '0.00' && correccion.horas_extras_correccion == '0.00' && correccion.horas_regulares == '0.00' && correccion.horas_extras == '0.00' && correccion.id_asistencia > 0)"><input type="number" class="form-control input-short" v-model.number="correccion.horas_regulares_coo"></td>
            <td v-else-if="correccionDats.estado == 2 || (correccionDats.estado == 1 && usuarioPlanilla)">{{ correccion.horas_regulares_coo }}</td>
            <td v-else>{{ correccion.horas_regulares_coo }}</td>
            <!-- HORAS EXTRAS COORDINADOR -->
            <td v-if="(correccionDats.estado == 1 && !usuarioPlanilla && correccion.id_correccion_detalle != '' && correccion.id_correccion_detalle != undefined && (correccion.horas_regulares_correccion != '0.00' || correccion.horas_extras_correccion != '0.00')) || (correccionDats.estado == 1 && !usuarioPlanilla && correccion.horas_regulares_correccion == '0.00' && correccion.horas_extras_correccion == '0.00' && correccion.id_asistencia == 0) || (correccionDats.estado == 1 && !usuarioPlanilla && correccion.horas_regulares_correccion == '0.00' && correccion.horas_extras_correccion == '0.00' && correccion.horas_regulares == '0.00' && correccion.horas_extras == '0.00' && correccion.id_asistencia > 0)"><input type="number" class="form-control input-short" v-model.number="correccion.horas_extras_coo"></td>
            <td v-else-if="correccionDats.estado == 2 || (correccionDats.estado == 1 && usuarioPlanilla)">{{ correccion.horas_extras_coo }}</td>
            <td v-else>{{ correccion.horas_extras_coo }}</td>
            <td v-if="correccionDats.estado == 1 && !usuarioPlanilla && (correccion.id_correccion_detalle != '' && correccion.id_correccion_detalle != undefined) || (checkedInasisCoo == true && (correccion.horas_regulares_correccion >= 0 && correccion.horas_regulares_correccion != null && correccion.horas_extras_correccion != '0.00'))">
                <input type="text" class="form-control input-short4" v-model="correccion.observacion_coo">
            </td>
            <td v-else-if="correccionDats.estado == 2 || (correccionDats.estado == 1 && usuarioPlanilla)">{{ correccion.observacion_coo }}</td>
            <td v-else-if="correccionDats.estado ==1"></td>
            <td v-if="correccionDats.estado == 0 || (correccionDats.estado == 1 && !usuarioPlanilla)">
                <button 
                    class="btn btn-success"
                    @click.prevent="guardarRegistro"
                    :disabled=" (((correccionDats.estado == 0) && (correccion.horas_regulares_correccion < 0 || correccion.horas_extras_correccion < 0 || correccion.horas_regulares_correccion === '' || correccion.horas_extras_correccion === '' || correccion.horas_regulares_correccion === null || correccion.horas_extras_correccion === null)) || ((correccionDats.estado == 1 && correccion.observacion_coo == '') && (correccion.horas_regulares_coo < 0 || correccion.horas_extras_coo < 0 || correccion.horas_regulares_coo === '' || correccion.horas_extras_coo === '' || correccion.horas_regulares_coo === null || correccion.horas_extras_coo === null))) && (checkedInasisCoo == false || checkedInasisCoo == null) && (correccion.horas_regulares_correccion >= 0 && correccion.horas_regulares_correccion != null)"> 
                    Guardar 
                </button>

                <button 
                    class="btn btn-danger"
                    @click.prevent="resetCorreccion"
                    v-show="usuarioPlanilla"> 
                    Reset 
                </button>
            </td>
            <!-- <textarea class="form-control" rows="2"></textarea> -->
        </tr>
</template>

<script>
import axios from 'axios';
export default {
    props: ['correccion','correccionDats', 'clientes', 'usuarioPlanilla', 'horasRegulares', 'horasExtras'],
    data() {
        return {
            sucursales: [],
            puestos: [],
            checkedInasisPla: this.correccion.inasistencia_asis == 1 ? true : this.correccion.inasistencia_asis == 0 ? false : null,
            checkedInasisCoo: this.correccion.inasistencia == 1 ? true : this.correccion.inasistencia == 0 ? false : null,
            correccionAux: [],
        }
    },
    created() {
        this.correccionAux = JSON.parse(JSON.stringify(this.correccion));
        if((this.correccion.id_asistencia == 0) && ( this.correccionDats.estado == 0))
        {
            this.chCliente(1,1);
        }
        console.log(this.correccion);
    },
    methods: {
        guardarRegistro() {
            if (((this.correccion.id_asistencia > 0) && (this.correccionDats.estado == 0)) && (!this.correccion.observacion)) {
                this.$toast.error("Por favor complete el formulario!");
                return;
            }
            else if (((this.correccion.id_asistencia == 0) && (this.correccionDats.estado == 0)) && ((!this.correccion.clienteId) || (!this.correccion.sucursalId) || (!this.correccion.id_puesto) || (!this.correccion.observacion) || (!this.correccion.fecha))) {
                this.$toast.error("Por favor complete el formulario!");
                return;
            }
            else if ((this.correccionDats.estado == 1) && (!this.correccion.observacion_coo)) {
                this.$toast.error("Por favor complete el formulario!");
                return;
            }
            else if ((this.correccion.fecha < this.correccionDats.fecha_desde) || (this.correccion.fecha > this.correccionDats.fecha_hasta)) {
                this.$toast.error("La fecha de la correccion esta fuera de rango, por favor revise!");
                return;
            }
            else {
                this.correccion.inasistencia = this.checkedInasisPla;
                this.correccion.inasistencia_coo = this.checkedInasisCoo;
                axios.post('vig_correcciones_ajax.php', {
                        opc: "c2",
                        correccion: this.correccion,
                        correccionId: this.correccionDats.id_correccion
                    })
                    .then(response => {
                        if(response.data.codigo == 1)
                        {
                            if((this.correccion.id_correccion_detalle == "") || (this.correccion.id_correccion_detalle == undefined))
                                this.correccion.id_correccion_detalle = response.data.idNuevo;

                            this.$emit('actualizarHoras');
                            this.$toast.success(response.data.mensaje);
                            this.correccionAux = JSON.parse(JSON.stringify(this.correccion));
                        }
                        else {
                            this.$toast.error(response.data.mensaje);
                            this.correccion.horas_regulares_correccion = this.correccionAux.horas_regulares_correccion;
                            this.correccion.horas_extras_correccion = this.correccionAux.horas_extras_correccion;
                            this.correccion.horas_regulares_coo = this.correccionAux.horas_regulares_coo;
                            this.correccion.horas_extras_coo = this.correccionAux.horas_extras_coo;
                        }
                    })
                    .catch(error => console.log(error))
            }
        },
        resetCorreccion(){
            if(confirm("Seguro desea deshacer la correcion?")) {
                axios.post('vig_correcciones_ajax.php', {
                        opc: "c11",
                        correccion: this.correccion,
                        correccionId: this.correccionDats.id_correccion
                    })
                    .then(response => {
                        if(response.data.codigo == 1)
                        {
                            if(this.correccion.id_asistencia) {
                                this.correccion.id_correccion_detalle = "";
                                this.correccion.horas_regulares_correccion = "";
                                this.correccion.horas_extras_correccion = "";
                                this.correccion.observacion = "";
                            }
                            else {
                                this.$emit('eliminarElemento', this.correccion);
                            }
                            this.$emit('actualizarHoras');
                            this.$toast.success(response.data.mensaje);
                        }
                        else
                            this.$toast.error(response.data.mensaje);
                        })
                    .catch(error => console.log(error))
            }
        },
        chSucursal() {
            let zona = this.sucursales.find( suc => { 
                if(suc.id == this.correccion.sucursalId)
                    return suc.zona;
            });
            if(zona)
            {
                this.correccion.zona = zona.zona;
            }
            else
            {
                this.correccion.zona = '';
            }
            this.correccion.puesto = '';
            axios.get('vig_correcciones_ajax.php', {
                params: {
                    opc: 'c5',
                    suc: this.correccion.sucursalId,
                    fechaDesde: this.correccionDats.fecha_desde,
                    fechaHasta: this.correccionDats.fecha_hasta
                }
            })
            .then(response => {
                if(response.data.codigo == 1)
                {
                    return this.puestos = response.data.puestos;
                }
                else
                {
                    this.$toast.error(response.data.mensaje);
                }
            })
            .catch(error => console.log(error))   
        },
        chCliente(e, val) {
            if(val == undefined)
                this.correccion.id_puesto = '';
            if(this.correccion.clienteId) {   
                axios.get('vig_correcciones_ajax.php', {
                  params: {
                        opc: 'c4',
                        cli: this.correccion.clienteId,
                        fechaDesde: this.correccionDats.fecha_desde,
                        fechaHasta: this.correccionDats.fecha_hasta
                    }
                })
                .then(response => {
                    if(response.data.codigo == 1)
                    {
                        this.sucursales = response.data.sucursales;
                        if(this.correccion.sucursalId)
                            this.chSucursal();
                        return this.sucursales;
                    }
                    else
                    {
                        this.$toast.error(response.data.mensaje);
                    }
                })
                .catch(error => console.log(error))
            }
        }
    },
}
</script>

<style scoped>
.agregado {
    background-color: #B2EBF2 !important;
}
.input-short {
    width: 70px;
}

.input-short2 {
    width: 160px;
}

.input-short3 {
    width: 170px;
}

.input-short4 {
    width: 180px;
}

.input-short5 {
    width: 300px;
}

/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

/* Firefox */
input[type=number] {
    -moz-appearance: textfield;
}
</style>