
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

                <DataTable v-model:expandedRows="expandedRows" :value="autores" dataKey="id" tableStyle="min-width: 60rem"
                    :paginator="true"
                    :rows="10"
                    :filters="filters"
                    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                    :rowsPerPageOptions="[5, 10, 25]"
                    currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} autores"
                    responsiveLayout="scroll">
                    <template #header>
                        <div class="flex flex-column md:flex-row md:justify-content-between md:align-items-center">
                            <h5 class="m-0">Gerenciar autores</h5>
                            <span class="block mt-2 md:mt-0 p-input-icon-left">
                                <i class="pi pi-search" />                              
                                <InputText v-model="filters['global'].value" placeholder="Buscar..." /> 
                            </span>
                        </div>
                    </template>
               
                        <!-- <Column headerStyle="width: 3rem"></Column> -->
                        <Column expander style="width: 5rem" />
                      <!--  <Column field="id" header="ID" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Id</span>
                                {{ slotProps.data.id }}
                            </template>
                        </Column>-->  
                        <Column field="perfil" header="Perfil" headerStyle="width:1%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    <img :alt="slotProps.data.nome" :src="'demo/images/avatar/user.png'" width="32" style="vertical-align: middle" />
                                </span>
                            </template>
                        </Column> 
                        <Column field="nome" header="Nome completo" :sortable="true" headerStyle="width:15%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                 {{ slotProps.data.nome }} {{slotProps.data.sobrenome }}
                                </span>
                            </template>
                        </Column> 
                        <Column field="cpf" header="CPF" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ slotProps.data.cpf }}
                                </span>
                            </template>
                        </Column>
                        <Column field="dataNascimento" header="Data nascimento" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ formatDate(slotProps.data.dataNascimento) }}
                                </span>
                            </template>
                        </Column>
                        <Column field="contato" header="Contato" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ slotProps.data.telefone }}
                                </span>
                            </template>
                        </Column> 
                        <Column field="email" header="E-mail" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ slotProps.data.email }}
                                </span>
                            </template>
                        </Column>
                        <Column  headerStyle="min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    <Button icon="pi pi-pencil" class="p-button-rounded p-button-success mr-2" @click="editarAutor(slotProps.data)" />
                                    <Button icon="pi pi-trash" class="p-button-rounded p-button-danger mt-2" @click="confirmDeleteAutor(slotProps.data)" />
                                </span>
                            </template>
                        </Column> 
                        <template #expansion="slotProps">
                            <div class="p-3">
                                <Fieldset legend="Endereço">
                                    <div class="formgrid grid">
                                        <div class="field col-2">
                                            <h6>CEP</h6>
                                            <label for="cep">{{slotProps.data.endereco.cep  }}</label>
                                        </div>
                                        <div class="field col-4">
                                            <h6>Logradouro</h6>
                                            <label for="logradouro">{{slotProps.data.endereco.logradouro }}</label>
                                        </div>
                                        <div class="field col-3">
                                            <h6>Bairro</h6>
                                            <label for="bairro">{{slotProps.data.endereco.bairro }}</label>
                                        </div>
                                        <div class="field col-2">
                                            <h6>Localidade</h6>
                                            <label for="localidade">{{slotProps.data.endereco.localidade }}</label>
                                        </div>
                                        <div class="field col-1">
                                            <h6>UF</h6>
                                            <label for="uf">{{slotProps.data.endereco.uf }}</label>
                                        </div>
                                    </div>
                                </Fieldset>
                                <div class="field"></div>
                                <div class="field" v-if="slotProps.data.projeto">
                                    <Fieldset legend="Projeto" :toggleable="true">
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
                            </div>
                        </template>
             
                </DataTable>

                <Dialog v-model:visible="autorDialog" :style="{ width: '700px' }" :header="nomeHeader" :modal="true" class="p-fluid">
                    <Fieldset legend="Autor">
                        <div class="formgrid grid">
                            <div class="field col-6">
                                <label for="name">Nome</label>
                                <InputText id="name" v-model.trim="autor.nome" required="true" autofocus :class="{ 'p-invalid': submitted && !autor.nome }" />
                                <small class="p-invalid label-cor" v-if="submitted && !autor.nome">O nome é obrigatório.</small>
                            </div>
                            <div class="field col-6">
                                <label for="sobrenome">Sobrenome</label>
                                <InputText id="sobrenome" v-model="autor.sobrenome" required="true" :class="{ 'p-invalid': submitted && !autor.sobrenome}" />
                                <small class="p-invalid label-cor" v-if="submitted && !autor.sobrenome">O sobrenome é obrigatório.</small>
                            </div>
                        </div>
                        <div class="formgrid grid">
                            <div class="field col-6">
                                <label for="cpf">CPF</label>
                                <InputMask id="cpf" v-model="autor.cpf" mask="999.999.999-99" placeholder="999.999.999-99" required="true" :class="{ 'p-invalid': submitted && !autor.cpf}" />      
                                <small class="p-invalid label-cor" v-if="submitted && !autor.cpf">O CPF é obrigatório.</small>
                            </div>
                            <div class="field col-6">
                                <label for="dataNascimento">Data nascimento</label>
                                <Calendar id="dataNascimento" dateFormat="dd/mm/yy" v-model="autor.dataNascimento" required="true" showIcon :class="{ 'p-invalid': submitted && !autor.dataNascimento}" />
                                <small class="p-invalid label-cor" v-if="submitted && !autor.dataNascimento">A data de nascimento é obrigatória.</small>
                            </div>
                        </div>
                        
                        <Fieldset legend="Endereço">
                            <div class="formgrid grid">
                                <!--mostrar ao fazer requisição e esconder ao trazer as informações-->
                                <div v-show="progressBar" class="field col-12">
                                    <ProgressBar id="progressBar" mode="indeterminate" style="height: 6px"></ProgressBar>
                                </div>
                                <div class="field col-6">
                                    <label for="cep">CEP</label>
                                    <InputMask id="cep" v-model="endereco.cep" mask="99999-999" placeholder="99999-999" @keyup="keyupCep()" required="true" :class="{ 'p-invalid': submitted && !endereco.cep}" />      
                                    <small class="p-invalid label-cor" v-if="submitted && !endereco.cep">O CEP é obrigatório.</small>
                                </div>
                                <div class="field col-6">
                                    <label for="localidade">Localidade</label>
                                    <InputText id="localidade" v-model="endereco.localidade" disabled />
                                </div>
                            </div>
                            <div class="field">
                                <label for="logradouro">Logradouro</label>
                                <InputText id="logradouro" v-model="endereco.logradouro" disabled />
                            </div>
                            <div class="formgrid grid">
                                <div class="field col-6">
                                    <label for="bairro">Bairro</label>
                                    <InputText id="bairro" v-model="endereco.bairro" disabled />
                                </div>
                                <div class="field col-6">
                                    <label for="uf">Uf</label>
                                    <InputText id="uf" v-model="endereco.uf" disabled />
                                </div>
                            </div>
                        </Fieldset>
                        <div class="field"></div>
                        <div class="formgrid grid">
                            <div class="field col-6">
                                <label for="contato">Contato</label>
                                <InputMask id="contato" v-model="autor.telefone" mask="(99) 99999-9999" placeholder="999.999.999-99" required="true" :class="{ 'p-invalid': submitted && !autor.telefone}" />      
                                <small class="p-invalid label-cor" v-if="submitted && !autor.cpf">O contato é obrigatório.</small>
                            </div>
                            <div class="field col-6">
                                <label for="email">E-mail</label>
                                <InputText id="email" v-model="autor.email" required="true" :class="{ 'p-invalid': submitted && !autor.email}" />
                                <small class="p-invalid label-cor" v-if="submitted && !autor.email">O e-mail é obrigatório.</small>
                            </div>
                        </div>
                    </Fieldset>
                    <template #footer>
                        <Button label="Cancelar" icon="pi pi-times" class="p-button-text" @click="hideDialog" />
                        <Button label="Salvar" icon="pi pi-check" class="p-button-text" @click="salvarAutor" />
                    </template>
                </Dialog>

                <Dialog v-model:visible="deleteAutorDialog" :style="{ width: '450px' }" header="Confirmar" :modal="true">
                    <div class="flex align-items-center justify-content-center">
                        <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                        <span v-if="autor"
                            >Tem certeza de que deseja excluir <b>{{ autor.nome }}</b
                            >?</span
                        >
                    </div>
                    <template #footer>
                        <Button label="Não" icon="pi pi-times" class="p-button-text" @click="deleteAutorDialog= false" />
                        <Button label="Sim" icon="pi pi-check" class="p-button-text" @click="deleteAutor" />
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
import Skeleton from 'primevue/skeleton';

export default {
    name: 'FormuAutor',
    data() {
        return {
            nomeHeader: 'Cadastrar',
            progressBar: false,
            toast: useToast(),
            autorDialog: false,
            deleteAutorDialog: false,
            filters: ref({'global': {value: null, matchMode: FilterMatchMode.CONTAINS},}),
            expandedRows: ref([]),
            submitted: false,
            autores: ref(new Array(4)),
            autor: new Object(),
            endereco: new Object()
        };
    },
    methods: { 
        verificarCepValido(cep){
            return /^\d{5}-?\d{3}$/.test(cep);
        },
        keyupCep(){
            if(this.verificarCepValido(this.endereco.cep)){
                this.progressBar = true;
                this.getEnderecoApi();
            }           
        },
        openNew() {
           this.nomeHeader = 'Cadastrar'
           this.autor = new Object();
           this.endereco = new Object();
           this.submitted = false;
           this.autorDialog = true;         
        },
        hideDialog() {
            this.autorDialog = false;
            this.submitted = false;
        },
        confirmDeleteAutor(avaliador){
            this.deleteAutorDialog = true;
            this.autor = avaliador;
            this.endereco = avaliador.endereco;      
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
        async getEnderecoApi(){
            const req = await fetch(`https://viacep.com.br/ws/${this.endereco.cep}/json/`)
            if(req.status == 200){
                const data = await req.json();
                this.endereco={
                        cep: data.cep,
                        logradouro: data.logradouro,
                        bairro: data.bairro,
                        localidade: data.localidade,
                        uf: data.uf
                    }    
                this.progressBar = false;
            }
            
        },  
        async getAutores() {
            const req = await fetch('http://localhost:8080/autor');
            if(req.status === 200){ 
                const data = await req.json();
                this.autores = data;
            }
        },
        async salvarAutor(){
            this.submitted =true;                    
            const data = {
                    id: this.autor.id,
                    nome: this.autor.nome,
                    sobrenome: this.autor.sobrenome,
                    cpf: this.autor.cpf,
                    dataNascimento: this.formatarStringData(this.autor.dataNascimento),
                    endereco:{
                        id: this.endereco.id,
                        cep: this.endereco.cep,
                        logradouro: this.endereco.logradouro,
                        bairro: this.endereco.bairro,
                        localidade: this.endereco.localidade,
                        uf: this.endereco.uf
                    }, 
                   telefone: this.autor.telefone,
                   email: this.autor.email
            }              
            const dataJson = JSON.stringify(data); // trnasformando o objeto em string json 
            if(data.id === undefined){
                const req = await fetch("https://jose2550.c41.integrator.host/backend/autor", { // Enviando a requisição
                    method: "POST",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                }); 
                if(req.status === 201){  // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Cadastrado com sucesso!', life: 3000 });
                    this.autorDialog = false;}
            }else {
                const req = await fetch("https://jose2550.c41.integrator.host/backend/autor", { // Enviando a requisição
                    method: "PUT",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                });      
                if(req.status === 200){   // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Alterado com sucesso!', life: 3000 });
                    this.autorDialog = false;}}
            this.getAutores();
        },
        async editarAutor(autor){
            this.nomeHeader = 'Alterar';
            const req = await fetch('https://jose2550.c41.integrator.host/backend/autor/'+autor.id);
            if(req.status == 200){
                const data = await req.json();
                this.autor = data;
                this.autor.dataNascimento = this.formatDate(data.dataNascimento);
                this.endereco = data.endereco;
                this.autorDialog = true;}
            if(req.status == 404){
                this.toast.add({ severity: 'error', summary: 'Error!', detail: 'Não foi encontrado autor!', life: 3000 });}
        },
        async deleteAutor(){
            const req = await fetch("https://jose2550.c41.integrator.host/backend/autor/delete/"+this.autor.id, { // Enviando a requisição
                method: "DELETE",
                headers:{ "Content-Type": "application/json" }
            }); 
            if(req.status == 204){
                this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Excluido com sucesso!', life: 3000 });
                this.deleteAutorDialog= false;
            }
            this.getAutores();
        }
    },
    mounted() {
        this.getAutores();
    },
    components: {
        InputText,
        InputNumber,
        Textarea,
        Calendar,
        Button,
        Fieldset,
        Dialog,
        Skeleton
    }
};
</script>
<style scoped lang="scss">
.label-cor{
    color: red;
}
@import '@/assets/demo/styles/badges.scss';
</style>
