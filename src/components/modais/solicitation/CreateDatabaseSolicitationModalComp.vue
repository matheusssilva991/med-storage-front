<template>
	<div class="modal" tabindex="-1" role="dialog" :class="{ 'd-block': showModal }">
		<div class="modal-dialog modal-dialog-centered" role="document">
			<div class="modal-content">
				<div class="modal-header">
					<h2 class="modal-title">Solicitação de novo banco de imagem</h2>
					<button type="button" class="close btn btn-white" @click="closeModal">
						<span class="h2" aria-hidden="true">&times;</span>
					</button>
				</div>
				<div class="modal-body">
					<label for="name" class="form-label mt-3">Nome</label>
					<input class="form-control" type="text" v-model=name aria-label="name">

					<label for="examType" class="form-label mt-3">Tipo de exame</label>
					<input class="form-control" type="text" v-model=examType aria-label="examType">

					<label for="description" class="form-label mt-3">Descrição</label>
					<textarea class="form-control" id="description" rows="3" v-model="this.description"></textarea>

					<label for="imageQuality" class="form-label mt-3">Qualidade das imagens (Bits)</label>
					<input class="form-control" type="text" v-model=imageQuality aria-label="imageQuality" placeholder="8, 12, ...">

					<label for="imageType" class="form-label mt-3">Tipo das imagens</label>
					<input class="form-control" type="text" v-model=imageType aria-label="imageType" placeholder="DICOM">

					<label for="sourceLink" class="form-label mt-3">Link de origem</label>
					<input class="form-control mb-3" type="text" v-model=sourceLink aria-label="sourceLink">
				</div>

				<div class="modal-footer">
					<button type="button" class="btn btn-secondary" @click="closeModal">Voltar</button>
					<button type="button" class="btn btn-danger" @click="newSolicitation">Criar</button>
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
import ErrorMessageModalComp from '@/components/modais/ErrorMessageModalComp.vue';

const URL_BASE = process.env.VUE_APP_BACKEND_URL_BASE;

export default {
	props: {
	},
	data() {
		return {
			name: undefined,
			examType: undefined,
			description: undefined,
			imageQuality: undefined,
			imageType: undefined,
			sourceLink: undefined,
			showModal: true,
			erro: undefined,
			showErroModal: false,
		};
	},
	methods: {
		async newSolicitation() {
			try {
				const token = localStorage.getItem('token');

				if (token != undefined) {
					const req = {
						headers: {
							Authorization: "Bearer " + token
						}
					};

					await axios.post((`${URL_BASE}/api/solicitation`), {
						"type": "newDatabase",
						"data": {
							'name': this.name,
							'examType': this.examType,
							'description': this.description,
							'imageQuality': this.imageQuality.split(",").map(data => data.trim()),
							'imageType': this.imageType,
							'sourceLink': this.sourceLink,
						}
					}, req);

					this.showModal = false;
					this.$emit('close-modal');
				}
			} catch (err) {
				let msgError = err.response.data?.err;
				msgError = msgError.replace("examType", "tipo de exame")
					.replace("description", "descrição")
					.replace("name", "nome")
					.replace("sourceLink", "link de origem");
				this.erro = msgError;
				this.showErroModal = true;
			}

		},
		closeModal() {
			this.showModal = false;
			this.$emit('close-modal');
		},
		closeErroModal() {
			this.showErroModal = false;
		},
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

.modal-footer {
	margin-top: 20px;
	text-align: right;
}
</style>
