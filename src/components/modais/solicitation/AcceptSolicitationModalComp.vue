<template>
	<div class="modal" tabindex="-1" role="dialog" :class="{ 'd-block': showModal }">
		<div class="modal-dialog modal-dialog-centered" role="document">
			<div class="modal-content">
				<div class="modal-header">
					<h2 class="modal-title">Aceitar solicitação</h2>
					<button type="button" class="close btn btn-white" @click="closeModal">
						<span class="h2" aria-hidden="true">&times;</span>
					</button>
				</div>
				<div class="modal-body">
					<div class="user-profile">
						<h3 class="profile-name"><i class="fas fa-envelope" aria-hidden="true"></i> {{
							solicitation.data.name }}</h3>
					</div>
					Deseja realmente aceitar a solicitação?
				</div>
				<div class="modal-footer">
					<button type="button" class="btn btn-secondary" @click="closeModal">Voltar</button>
					<button type="button" class="btn btn-danger" @click="acceptSolicitation">Aceitar</button>
				</div>
			</div>
		</div>

		<div v-if="this.showErroModal" class="error-msg">
			<ErrorMessageModalComp :message=this.erro @close-modal="closeErroModal()" />
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import ErrorMessageModalComp from '../ErrorMessageModalComp.vue';

const URL_BASE = process.env.VUE_APP_BACKEND_URL_BASE;

export default {
	props: {
		solicitation: {
			type: Object,
			required: true,
		},
	},
	components: {
		ErrorMessageModalComp
	},
	data() {
		return {
			showErroModal: false,
			showModal: true
		};
	},
	methods: {
		closeModal() {
			this.showModal = false;
			this.$emit('close-modal');
		},
		closeErroModal() {
			this.showErroModal = false;
		},
		acceptSolicitation() {
			const token = localStorage.getItem('token');

			if (token != undefined) {
				const req = {
					headers: {
						Authorization: "Bearer " + token
					}
				};

				if (this.solicitation.type == "Novo Usuário") {
					axios.post(`${URL_BASE}/api/user`, { "idSolicitation": this.solicitation._id }, req)
						.then(() => {

							axios.put(`${URL_BASE}/api/solicitation/${this.solicitation._id}`,
								{ 'status': 'accepted' }, req)
								.then(() => {
									this.$emit('save-changes');
								}).catch(err => {
									this.showErroModal = true;
									this.erro = err.response.data?.err;
								});
						}).catch(err => {
							this.showErroModal = true;
							this.erro = err.response.data?.err;
						});
				} else {
					let newDatabase = this.solicitation.data;

					axios.post(`${URL_BASE}/api/database`, newDatabase, req)
						.then(() => {

							axios.put(`${URL_BASE}/api/solicitation/${this.solicitation._id}`,
								{ 'status': 'accepted' }, req)
								.then(() => {
									this.$emit('save-changes');
								}).catch(err => {
									this.showErroModal = true;
									this.erro = err.response.data?.err;
								});
						}).catch(err => {
							this.showErroModal = true;
							this.erro = err.response.data?.err;
						});

				}
			}
		}
	},
};

</script>
  
<style scoped>
/* Estilos do Modal */

.modal {
	font-family: 'Montserrat medium', sans-serif;
	margin: 0 auto;
	width: 100%;
	height: 100%;
	background-color: rgba(0, 0, 0, 0.5);
	display: flex;
	justify-content: center;
	align-items: center;
	z-index: 9999;
}

.modal-content {
	background-color: #ffffff;
	padding: 20px;
	border-radius: 10px;
	box-shadow: 0 4px 7px rgba(0, 0, 0, 0.2);
	max-width: 500px;
	width: 100%;
	height: 500px;
	/* Altura fixa para o conteúdo do modal */
	overflow-y: auto;
	/* Habilita a barra de rolagem vertical quando necessário */
}

.modal-header {
	display: flex;
	justify-content: space-between;
	align-items: center;
}

.modal-title {
	margin-top: 0;
	margin-bottom: 3%;
	font-size: 25px;
	text-align: left;
	font-weight: bold;
	width: 70%;
}

.modal-body {
	padding-top: 20px;
}

.user-profile {
	margin-bottom: 20px;
}

.profile-name {
	color: #44bba4;
}

.profile-function {
	margin-bottom: 0;
}

.select-profile-title {
	margin-top: 0;
	font-size: 18px;
	font-weight: bold;
}

.profile-options {
	margin-top: 10px;
}

.profile-option {
	display: flex;
	align-items: center;
	margin-bottom: 10px;
}

.profile-option input {
	margin-right: 5px;
}

.modal-footer {
	margin-top: 20px;
	text-align: right;
}
</style>
