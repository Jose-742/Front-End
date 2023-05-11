

<template>
    <div class="grid">
        <div class="col-12">
            <div class="card">
                <Toast />
                <Toolbar class="mb-4">
                    <template #start>
                        <div class="my-2">
                            <Button label="Novo" icon="pi pi-plus" class="p-button-success mr-2" @click="openNew" />
                        </div>
                    </template>
                </Toolbar>

                <DataTable v-model:expandedRows="expandedRows" :value="avaliacoes" dataKey="id" tableStyle="min-width: 60rem"
                    :paginator="true"
                    :rows="10"
                    :filters="filters"
                    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                    :rowsPerPageOptions="[5, 10, 25]"
                    currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} avaliação"
                    responsiveLayout="scroll">
                    <template #header>
                        <div class="flex flex-column md:flex-row md:justify-content-between md:align-items-center">
                            <h5 class="m-0">Gerenciar avaliação</h5>
                            <span class="block mt-2 md:mt-0 p-input-icon-left">
                                <i class="pi pi-search" />                              
                                <InputText v-model="filters['global'].value" placeholder="Buscar..." /> 
                            </span>
                        </div>
                    </template>
               
                        <Column expander style="width: 3rem" />
                     
                        <Column field="Parecer" header="Parecer" :sortable="true" headerStyle="width:55%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Parecer</span> 
                                {{ slotProps.data.parecer}}
                            </template>
                        </Column> 
                        <Column field="nota" header="Nota" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Nota</span> 
                                {{ slotProps.data.nota }}
                            </template>
                        </Column>
                        <Column field="dataAvaliacao" header="Data avaliação" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Data Avaliação</span>
                                {{ formatDate(slotProps.data.dataAvaliacao) }}
                            </template>
                        </Column>
                        <Column  headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <Button icon="pi pi-pencil" class="p-button-rounded p-button-success mr-2" @click="editarAvaliacao(slotProps.data)" />
                                <Button icon="pi pi-trash" class="p-button-rounded p-button-warning mt-2" @click="confirmDeleteAvaliacao(slotProps.data)" />
                            </template>
                        </Column>
                        <template #expansion="slotProps">
                            <div class="p-3">
                                <Fieldset legend="Avaliador">
                                    <div class="formgrid grid">
                                        <div class="field col-2">
                                            <h6>Perfil</h6>
                                            <img :alt="slotProps.data.avaliador.nome" :src="'demo/images/avatar/user2.png'" width="32" style="vertical-align: middle" />
                                        </div>
                                        <div class="field col-4">
                                            <h6>Nome completo</h6>
                                            <label for="logradouro">{{slotProps.data.avaliador.nome }} {{slotProps.data.avaliador.sobrenome }} </label>
                                        </div>
                                        <div class="field col-3">
                                            <h6>CPF</h6>
                                            <label for="bairro">{{slotProps.data.avaliador.cpf }}</label>
                                        </div>
                                        <div class="field col-3">
                                            <h6>Registro</h6>
                                            <label for="localidade">{{slotProps.data.avaliador.registro }}</label>
                                        </div>
                                    </div>
                                </Fieldset>
                                <div class="field"></div>
                                <div class="field">
                                    <Fieldset legend="Projeto">
                                        <div class="formgrid grid">
                                            <div class="field col-4">
                                                <h6>Título</h6>
                                                <label for="projeto">{{slotProps.data.projeto.titulo }}</label>
                                            </div>
                                            <div class="field col-4">
                                                <h6>Status</h6>
                                                <Tag v-if="slotProps.data.projeto.status == 'ENVIADO'" class="mr-2" severity="info" value="Info">{{slotProps.data.projeto.status }}</Tag>
                                                <Tag v-else class="mr-2" severity="success" value="Success">{{slotProps.data.projeto.status }}</Tag>
                                            </div>
                                            <div class="field col-4">
                                                <h6>Data envio</h6>
                                                <label for="projeto">{{ formatDate(slotProps.data.projeto.dataEnvio) }}</label>
                                            </div>
                                        </div>
                                    </Fieldset>
                                </div>
                                <div class="field"  v-if="slotProps.data.projeto.premio != null">
                                    <Fieldset legend="Prêmio">
                                        <div class="formgrid grid">
                                            <div class="field col-5">
                                                <h6>Nome</h6>
                                                <label for="projeto">{{slotProps.data.projeto.premio.nome }}</label>
                                            </div>
                                            <div class="field col-5">
                                                <h6>Descrição</h6>
                                                <label for="projeto">{{slotProps.data.projeto.premio.descricao }}</label>
                                            </div>
                                            <div class="field col-2">
                                                <h6>Ano</h6>
                                                <label for="projeto">{{slotProps.data.projeto.premio.ano }}</label>
                                            </div>
                                            
                                        </div>
                                    </Fieldset>
                                </div>
                            </div>
                        </template>
             
                </DataTable>

                <Dialog v-model:visible="avaliacaoDialog" :style="{ width: '700px' }" :header="nomeHeader" :modal="true" class="p-fluid">
                    <Fieldset legend="Avaliação">
                        <div class="field">
                            <Fieldset legend="Prêmio">

                                <DataTable v-model:selection="selectPremio" :value="premios" class="p-datatable-sm" dataKey="id"
                                    responsiveLayout="scroll" tableStyle="min-width: 10rem">
                                        <Column selectionMode="single" headerStyle="width: 3rem"></Column>
                                        <Column field="titulo" header="Título" :sortable="true" headerStyle="width:60%; min-width:1rem;">
                                            <template #body="slotProps">
                                                <span class="p-column-title">Nome</span>
                                                {{ slotProps.data.nome }}
                                            </template>
                                        </Column> 
                                        <Column field="ano" header="Ano" :sortable="true" headerStyle="width:60%; min-width:1rem;">
                                            <template #body="slotProps">
                                                <span class="p-column-title">Ano</span>
                                                {{ slotProps.data.ano }}
                                            </template>
                                        </Column> 
                                    </DataTable>

                            </Fieldset>
                            <small class="p-invalid label-cor" v-if="submitted && selectPremio.length == 0">O prêmio é obrigatório.</small>
                        </div>
                        <div class="field">
                            <Fieldset legend="Avaliador">
                                
                                    <DataTable v-model:selection="selectAvaliador" :value="avaliadores" class="p-datatable-sm" dataKey="id"
                                    responsiveLayout="scroll" tableStyle="min-width: 10rem">
                                        <Column selectionMode="single" headerStyle="width: 3rem"></Column>
                                        <Column field="perfil" header="Perfil"  headerStyle="width:13%; min-width:3rem;">
                                            <template #body="slotProps">
                                                <img :alt="slotProps.data.nome" :src="'demo/images/avatar/user2.png'" width="32" style="vertical-align: middle" />
                                            </template>
                                        </Column> 
                                        <Column field="nome" header="Nome completo" :sortable="true" headerStyle="width:15%; min-width:7rem;">
                                            <template #body="slotProps">
                                                <span class="p-column-title">Nome completo</span>
                                                {{ slotProps.data.nome }} {{slotProps.data.sobrenome }}
                                            </template>
                                        </Column> 
                                        <Column field="cpf" header="CPF"></Column>
                                    </DataTable>
                                    
                            </Fieldset>
                            <small class="p-invalid label-cor" v-if="submitted && selectAvaliador.length == 0">O avaliador é obrigatório.</small>
                        </div>
                        <div class="field" v-if="avaliacao.id == null">
                            <Fieldset legend="Projeto">

                                <DataTable v-model:selection="selectProjeto" :value="projetos" class="p-datatable-sm" dataKey="id"
                                    responsiveLayout="scroll" tableStyle="min-width: 10rem">
                                        <Column selectionMode="single" headerStyle="width: 3rem"></Column>
                                        <Column field="titulo" header="Título" :sortable="true" headerStyle="width:60%; min-width:1rem;">
                                            <template #body="slotProps">
                                                <span class="p-column-title">Título</span>
                                                {{ slotProps.data.titulo }}
                                            </template>
                                        </Column> 
                                        <Column field="dataEnvio" header="Data envio" :sortable="true" headerStyle="width:50%; min-width:5rem;">
                                            <template #body="slotProps">
                                                <span class="p-column-title">Data envio</span>
                                                {{ formatDate(slotProps.data.dataEnvio) }}
                                            </template>
                                        </Column> 
                                        <Column field="status" header="Status" :sortable="true" headerStyle="width:40%; min-width:1rem;">
                                            <template #body="slotProps">
                                                <span class="p-column-title">Status</span>
                                                <Tag v-if="slotProps.data.status == 'ENVIADO'" class="mr-2" severity="info" value="Info">{{slotProps.data.status }}</Tag>
                                                <Tag v-else class="mr-2" severity="success" value="Success">{{slotProps.data.status }}</Tag>
                                            </template>
                                        </Column>
                                    </DataTable>

                            </Fieldset>
                            <small class="p-invalid label-cor" v-if="submitted && selectProjeto.length == 0">O projeto é obrigatório.</small>
                        </div>
                        <div class="field">
                            <label for="parecer">Parecer</label>
                            <Textarea id="parecer" v-model="avaliacao.parecer" required="true" rows="3" cols="20" :class="{ 'p-invalid': submitted && !avaliacao.parecer}" />
                            <small class="p-invalid label-cor" v-if="submitted && !avaliacao.parecer">O parecer é obrigatório.</small>
                        </div>  
                        <div class="field">
                            <label for="nota">Nota</label>
                            <InputText id="nota" v-model="avaliacao.nota" @input="validarNumero" required="true" 
                                placeholder="(0 a 10, incluindo valores fracionados como 1.2, 9.9, etc.)"
                                :class="{ 'p-invalid': submitted && !avaliacao.nota}" />                           
                            <small class="p-invalid label-cor" v-if="submitted && !avaliacao.nota">A nota é obrigatório.</small>
                        </div>                                                        
                    </Fieldset>
                    <template #footer>
                        <Button label="Cancelar" icon="pi pi-times" class="p-button-text" @click="hideDialog" />
                        <Button label="Salvar" icon="pi pi-check" class="p-button-text" @click="salvarAvaliacao" />
                    </template>
                </Dialog>

                <Dialog v-model:visible="deleteAvaliacaoDialog" :style="{ width: '450px' }" header="Confirmar" :modal="true">
                    <div class="flex align-items-center justify-content-center">
                        <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                        <span v-if="avaliacao"
                            >Tem certeza de que deseja excluir à avaliacão do projeto <b>{{ avaliacao.projeto.titulo }}</b
                            >?</span
                        >
                    </div>
                    <template #footer>
                        <Button label="Não" icon="pi pi-times" class="p-button-text" @click="deleteAvaliacaoDialog= false" />
                        <Button label="Sim" icon="pi pi-check" class="p-button-text" @click="deleteAvaliacao" />
                    </template>
                </Dialog>
            </div>
        </div>
    </div>
</template>

<script >
import { FilterMatchMode } from 'primevue/api';
import { ref, onMounted, onBeforeMount } from 'vue';
import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Textarea from 'primevue/textarea';
import InputNumber from 'primevue/inputnumber';
import Calendar from 'primevue/calendar';
import { useToast } from "primevue/usetoast";
import Button from 'primevue/button';
import Fieldset from 'primevue/fieldset';

export default {
    name: 'FormuAutor',
    data() {
        return {
            nomeHeader: 'Cadastrar',
            toast: useToast(),
            avaliacaoDialog: false,
            deleteAvaliacaoDialog: false,
            filters: ref({'global': {value: null, matchMode: FilterMatchMode.CONTAINS},}),
            expandedRows: ref([]),
            submitted: false,
            avaliacoes: null,
            avaliacao: new Object(),
            avaliadores: null,
            projetos: null,
            premios: null,
            selectAvaliador: ref([]),
            selectProjeto: ref([]),
            selectPremio: ref([])
           
        };
    },
    methods: { 
        validarNumero() {
            const regex = /^(10(\.0)?|[0-9](\.[0-9])?)$/; // Expressão regular para verificar o formato do número
            if (!regex.test(this.avaliacao.nota)) {
                // Se o número não estiver no formato válido, remova os caracteres inválidos
                this.avaliacao.nota = this.avaliacao.nota.replace(/[^0-9.]/g, "");
                // Verifica se o número está fora do intervalo de 0 a 10 ou possui mais de uma casa decimal
                const parsedNumber = parseFloat(this.avaliacao.nota);
                if (
                    isNaN(parsedNumber) ||
                    parsedNumber < 0 ||
                    parsedNumber > 10 ||
                    this.avaliacao.nota.split(".")[1]?.length > 1 ||
                    this.avaliacao.nota.split(".").length > 2 ||
                    this.avaliacao.nota.length > 3
                ) {
                    this.avaliacao.nota = "";
                }
            }
        },
        openNew() {
           this.nomeHeader = 'Cadastrar'
           this.avaliacao = new Object();     
           this.selectAvaliador = [];
           this.selectProjeto =  [];
           this.selectPremio = [];
           this.submitted = false;
           this.avaliacaoDialog = true;         
        },
        hideDialog() {
            this.avaliacaoDialog = false;
            this.submitted = false;
        },
        confirmDeleteAvaliacao(avaliacao){
            this.deleteAvaliacaoDialog = true;
            this.avaliacao = avaliacao;      
        },
        formatDate(value){
            let data = new Date(value);
            let dataFormatada = data.toLocaleDateString('pt-BR', {timeZone: 'UTC'});
            return dataFormatada;
        },
        formatarStringData(data) {               
            try {
                let partes = data.split('/');
                let dataFormatada = `${partes[2]}-${partes[1].padStart(2, '0')}-${partes[0].padStart(2, '0')}`;
                return new Date(dataFormatada);
            } catch (e) {
                return data;
            }
        },         
        async getAvaliacao() {
            const req = await fetch('http://localhost:8080/avaliacao');
            const data = await req.json();
            this.avaliacoes = data;
        },
        async getAvaliadores() {
            const req = await fetch('http://localhost:8080/avaliador');
            const data = await req.json();
            this.avaliadores = data;
        },
        async getProjetos() {
            const req = await fetch('http://localhost:8080/projeto/enviado');
            const data = await req.json();
            this.projetos = data;
        },
        async getPremios() {
            const req = await fetch('http://localhost:8080/premio');
            const data = await req.json();
            this.premios = data;
        },
        async salvarAvaliacao(){
            this.submitted =true;           
            const data = {
                    id: this.avaliacao.id,
                    projeto:{
                        id: this.selectProjeto.id,
                        premio:{
                            id:this.selectPremio.id 
                        }
                    },
                    avaliador:{id: this.selectAvaliador.id},
                    parecer: this.avaliacao.parecer,
                    nota: this.avaliacao.nota
            }       
            const dataJson = JSON.stringify(data); // trnasformando o objeto em string json 
            if(data.id === undefined){
                const req = await fetch("http://localhost:8080/avaliacao", { // Enviando a requisição
                    method: "POST",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                }); 
                if(req.status === 201){  // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Cadastrado com sucesso!', life: 3000 });
                    this.avaliacaoDialog = false;}
            }else {
                const req = await fetch("http://localhost:8080/avaliacao", { // Enviando a requisição
                    method: "PUT",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                });      
                if(req.status === 201){   // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Alterado com sucesso!', life: 3000 });
                    this.avaliacaoDialog = false;}}
            this.getAvaliacao();
        },
        async editarAvaliacao(avaliacao){
            this.nomeHeader = 'Alterar';
            this.avaliacao = new Object();     
            this.selectAvaliador = [];
            this.selectProjeto =  [];
            this.selectPremio = [];
            const req = await fetch('http://localhost:8080/avaliacao/'+avaliacao.id);
            if(req.status == 200){
                const data = await req.json();
                if(data.projeto.premio != null){
                    this.selectPremio = data.projeto.premio;
                }
                this.selectProjeto = data.projeto;
                this.selectAvaliador = data.avaliador;
                this.avaliacao = data;             
                this.avaliacaoDialog = true;}
            if(req.status == 404){
                this.toast.add({ severity: 'error', summary: 'Error!', detail: 'Não foi encontrado autor!', life: 3000 });}
        },
        async deleteAvaliacao(){
            const req = await fetch("http://localhost:8080/avaliacao/delete/"+this.avaliacao.id, { // Enviando a requisição
                method: "DELETE",
                headers:{ "Content-Type": "application/json" }
            }); 
            if(req.status == 204){
                this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Excluido com sucesso!', life: 3000 });
                this.deleteAvaliacaoDialog = false;
            }
            this.getAvaliacao();
        }
    },
    mounted() {
        this.getAvaliacao();
        this.getAvaliadores();
        this.getProjetos();
        this.getPremios();
    },
    components: {
        InputText,
        InputNumber,
        Textarea,
        Calendar,
        Button,
        Fieldset,
        Dialog
    }
};
</script>
<style scoped lang="scss">
.label-cor{
    color: red;
}
@import '@/assets/demo/styles/badges.scss';
</style>
