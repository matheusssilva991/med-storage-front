<template>
	<div class="modal" tabindex="-1" role="dialog" :class="{ 'd-block': showModal }">
		<div class="modal-dialog modal-dialog-centered" role="document">
			<div class="modal-content">
				<div class="modal-header">
					<h2 class="modal-title">Cadastrar banco de imagens</h2>
					<button type="button" class="close btn btn-white" @click="closeModal">
						<span class="h2" aria-hidden="true">&times;</span>
					</button>
				</div>
				<div class="modal-body">
					<label for="name" class="form-label mt-3">Nome</label>
					<input class="form-control" type="text" v-model="this.name" aria-label="name">

					<label for="examType" class="form-label mt-3">Tipo de exame</label>
					<input class="form-control" type="text" v-model="this.examType" aria-label="examType">

					<label for="imageType" class="form-label mt-3">Tipo das imagens</label>
					<input class="form-control" type="text" v-model="this.imageType" aria-label="imageType" placeholder="Dicom">

					<label for="imageQuality" class="form-label mt-3">Qualidade das imagens (Bits)</label>
					<input class="form-control" type="text" v-model="this.imageQuality" aria-label="imageQuality" placeholder="8, 12, ...">

					<label for="description" class="form-label mt-3">Descrição</label>
					<textarea class="form-control" id="description" rows="3" v-model="this.description"></textarea>

					<label for="sourceLink" class="form-label mt-3">Link de origem</label>
					<input class="form-control mb-3" type="text" v-model="this.sourceLink" aria-label="sourceLink">
				</div>

				<div class="modal-footer">
					<button type="button" class="btn btn-success" @click="saveChanges">Salvar</button>
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
	data() {
		return {
			showModal: true,
			description: "",
			imageQuality: "",
			examType: "",
			sourceLink: "",
			imageType: "",
			name: "",
			showErroModal: false,
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
		saveChanges() {
			let tmpImageQuality = this.imageQuality.split(",").map(data => data.trim());

			const newDatabase = {
				name: this.name,
				examType: this.examType,
				imageType: this.imageType,
				imageQuality: tmpImageQuality,
				description: this.description,
				sourceLink: this.sourceLink,
			};

			const token = localStorage.getItem('token');

			if (token != undefined) {
				const req = {
					headers: {
						Authorization: "Bearer " + token
					}
				};
				axios.post(`${URL_BASE}/api/database`, newDatabase, req)
				.catch(err => {
					this.showErroModal = true;
					this.erro = err.response.data?.err;
				})
			}

			this.$emit('save-changes');
		}
	},
	components: {
		ErrorMessageModalComp
	}
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
