<template>
	<div class="modal" tabindex="-1" role="dialog" :class="{ 'd-block': showModal }">
		<div class="modal-dialog modal-dialog-centered" role="document">
			<div class="modal-content">
				<div class="modal-header">
					<h2 class="modal-title">Visualizar banco de imagens</h2>
					<button type="button" class="close btn btn-white" @click="closeModal">
						<span class="h2" aria-hidden="true">&times;</span>
					</button>
				</div>
				<div class="modal-body">
					<div class="user-profile">
						<h3 class="profile-name"><i class="fa fa-database" aria-hidden="true"></i> {{ database.name}}</h3>
					</div>

					<label for="examType" class="form-label mt-3">Tipo de exame</label>
					<input class="form-control" type="text" :value=database.examType aria-label="Disabled examType" disabled readonly>
					
                    <label for="imageType" class="form-label mt-3">Tipo da imagem</label>
					<input class="form-control" type="text" :value=database.imageType aria-label="Disabled imageType" disabled readonly>

                    <label for="imageQuality" class="form-label mt-3">Qualidade das imagens (bits)</label>
					<input class="form-control" type="text" :value=database.imageQuality aria-label="Disabled imageQuality" disabled readonly>

					<label for="description" class="form-label mt-3">Descrição</label>
					<textarea class="form-control" id="description" rows="3" disabled readonly v-model="this.description" ></textarea>

					<label for="sourceLink" class="form-label mt-3">Link de origem</label>
					<input class="form-control mb-3" type="text" :value=database.sourceLink aria-label="Disabled sourceLink" disabled readonly>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		created() {
            this.description = this.database.description
        },
        props: {
			database: {
				type: Object,
				required: true,
			},
		},
		data() {
			return {
				showModal: true,
                description: ""
			};
		},
		methods: {
			closeModal() {
				this.showModal = false;
				this.$emit('close-modal');
			},
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
    height: 500px; /* Altura fixa para o conteúdo do modal */
    overflow-y: auto; /* Habilita a barra de rolagem vertical quando necessário */
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
