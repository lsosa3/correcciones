<template>
    <div class="row">
			<div class="input-group mb-3">
				<input 
					type="text" 
					class="form-control" 
					placeholder="Codigo de empleado"
					v-model="codigo">
				<div class="input-group-append">
					<button 
						class="btn btn-primary"
						@click.prevent="buscarRegistros"
						:disabled="!codigo || !usuarioPlanilla"> 
						Buscar
					</button>
				</div>
				&nbsp;&nbsp;
				<button 
					type="button" 
					class="btn btn-primary"
					@click.prevent="buscarEmpleados">
					Empleados
				</button>
			</div>
		</div>
</template>

<script>
export default {
	props: ['usuarioPlanilla'],
    data () {
        return {
            codigo: ''
        }
    },
    methods: {
        buscarRegistros() {
            this.$emit('busquedaAct', this.codigo);
		},
		buscarEmpleados() {
            this.$emit('busquedaEmp');
        }
    }
}
</script>