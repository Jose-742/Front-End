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
                <DataTable v-model:expandedRows="expandedRows" :value="projetos" dataKey="id" tableStyle="min-width: 60rem"
                    :paginator="true"
                    :rows="10"
                    :filters="filters"
                    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                    :rowsPerPageOptions="[5, 10, 25]"
                    currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} projetos"
                    responsiveLayout="scroll">
                    <template #header>
                        <div class="flex flex-column md:flex-row md:justify-content-between md:align-items-center">
                            <h5 class="m-0">Gerenciar projetos</h5>
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
                        <Column field="area" header="Área" :sortable="true" headerStyle="width:15%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Área</span> 
                                {{ slotProps.data.area }}
                            </template>
                        </Column> 
                        <Column field="titulo" header="Título" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Título</span>
                                {{ slotProps.data.titulo }}
                            </template>
                        </Column>
                        <Column field="resumo" header="Resumo" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Resumo</span>
                                {{ slotProps.data.resumo }}
                            </template>
                        </Column>
                        <Column field="dataEnvio" header="Data envio" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Data envio</span>
                                {{ formatDate(slotProps.data.dataEnvio) }}
                            </template>
                        </Column> 
                        <Column field="status" header="Status" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Status</span>
                                <Tag v-if="slotProps.data.status == 'ENVIADO'" class="mr-2" severity="info" value="Info">{{slotProps.data.status }}</Tag>
                                <Tag v-else class="mr-2" severity="success" value="Success">{{slotProps.data.status }}</Tag>
                            </template>
                        </Column>
                        <Column  headerStyle="min-width:10rem;">
                            <template #body="slotProps">
                                <Button icon="pi pi-pencil" v-if="slotProps.data.status == 'ENVIADO'" class="p-button-rounded p-button-success mr-2" @click="editarProjeto(slotProps.data)" />
                                <Button icon="pi pi-trash" class="p-button-rounded p-button-warning mt-2" @click="confirmDeleteProjeto(slotProps.data)" />
                            </template>
                        </Column> 
                        <template #expansion="slotProps">
                            <div class="orders-subtable">
                                <!-- <h5>Orders for {{slotProps.data.titulo}}</h5> -->
                                <DataTable :value="slotProps.data.autores">
                                    <Column field="perfil" header="Perfil">
                                        <template #body="slotProps">
                                            <img :alt="slotProps.data.nome" :src="'demo/images/avatar/user.png'" width="32" style="vertical-align: middle" />
                                        </template>
                                    </Column>
                                    <Column field="nome sobrenome" header="Nome completo" sortable>
                                        <template #body="slotProps">
                                            <span class="p-column-title">Nome completo</span> 
                                            {{ slotProps.data.nome }} {{slotProps.data.sobrenome }}
                                        </template>
                                    </Column>
                                    <Column field="cpf" header="CPF" sortable></Column>
                                    <Column field="dataNascimento" header="Data nascimento" sortable>
                                        <template #body="slotProps">
                                            <span class="p-column-title">Data nascimento</span>
                                            {{ formatDate(slotProps.data.dataNascimento) }}
                                        </template>
                                    </Column>
                                    <Column field="telefone" header="Contato" sortable></Column>
                                    <Column field="email" header="E-mail" sortable></Column>
                                </DataTable>
                            </div>                               
                        </template>
        
                </DataTable>

                <Dialog v-model:visible="projetoDialog" :style="{ width: '900px' }" :header="nomeHeader" :modal="true" class="p-fluid">
                    <Fieldset :legend="'Projeto'">
                        <div class="field">
                            <label for="area">Área</label>
                            <InputText id="area" v-model.trim="projeto.area" required="true" autofocus :class="{ 'p-invalid': submitted && !projeto.area}" />
                            <small class="p-invalid label-cor" v-if="submitted && !projeto.area">A área é obrigatória.</small>
                        </div>
                        <div class="field">
                            <label for="titulo">Título</label>
                            <InputText id="titulo" v-model.trim="projeto.titulo" required="true" autofocus :class="{ 'p-invalid': submitted && !projeto.titulo}" />
                            <small class="p-invalid label-cor" v-if="submitted && !projeto.titulo">O título é obrigatório.</small>
                        </div>
                        <div class="field">
                            <label for="description">Resumo</label>
                            <Textarea id="resumo" v-model="projeto.resumo" required="true" rows="3" cols="20" :class="{ 'p-invalid': submitted && !projeto.resumo}" />
                            <small class="p-invalid label-cor" v-if="submitted && !projeto.resumo">O resumo é obrigatório.</small>
                        </div>
                        <Fieldset legend="Autores">
                            <PickList v-model="autores" listStyle="height:342px" dataKey="id" :selection.sync="selection"> 
                                <template #sourceheader>                                            
                                    Disponível
                                </template>
                                <template #targetheader>
                                    Selecionado
                                </template>
                                <template #item="slotProps" >
                                    <div class="flex flex-wrap p-2 align-items-center gap-3">
                                        <img class="w-4rem shadow-2 flex-shrink-0 border-round" :src="'demo/images/avatar/user.png'" :alt="slotProps.item.nome" />
                                        <div class="flex-1 flex flex-column gap-2">
                                            <span class="font-bold">{{ slotProps.item.nome }}</span>
                                            <div class="flex align-items-center gap-2">
                                                <i class="pi pi-tag text-sm"></i>
                                                <span>{{ slotProps.item.cpf }}</span>
                                            </div>
                                        </div>
                                        <!-- <span class="font-bold text-900">$ {{ slotProps.item.id }}</span> -->
                                    </div>
                                </template>                                       
                            </PickList>
                            <small class="p-invalid label-cor" v-if="submitted && autores[1].length == 0">Autores é obrigatório.</small>
                        </Fieldset>                    
                    </Fieldset>
                    <template #footer>
                        <Button label="Cancelar" icon="pi pi-times" class="p-button-text" @click="hideDialog" />
                        <Button label="Salvar" icon="pi pi-check" class="p-button-text" @click="salvarProjeto" />
                    </template>   
                </Dialog>

                <Dialog v-model:visible="deleteProjetoDialog" :style="{ width: '450px' }" header="Confirmar" :modal="true">
                    <div class="flex align-items-center justify-content-center">
                        <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                        <span v-if="projeto"
                            >Tem certeza de que deseja excluir <b>{{ projeto.titulo }}</b
                            >?</span
                        >
                    </div>
                    <template #footer>
                        <Button label="Não" icon="pi pi-times" class="p-button-text" @click="deleteProjetoDialog= false" />
                        <Button label="Sim" icon="pi pi-check" class="p-button-text" @click="deleteProjeto" />
                    </template>
                </Dialog>

            </div>
        </div>
    </div>
</template>


<script>
import { FilterMatchMode } from 'primevue/api';
import { ref, onMounted, onBeforeMount } from 'vue';
/*import ProductService from '../../../service/ProductService';*/

import Dialog from 'primevue/dialog';
import InputText from 'primevue/inputtext';
import Textarea from 'primevue/textarea';
import Calendar from 'primevue/calendar';
import { useToast } from "primevue/usetoast";
import Button from 'primevue/button';
import Fieldset from 'primevue/fieldset';
import PickList from 'primevue/picklist';

export default {
    name: 'FormProjeto',
    data() {
        return {
            active:0,
            nomeHeader: 'Cadastrar',
            submitted: false,
            toast: useToast(),
            projetoDialog: false,
            deleteProjetoDialog: false,
            filters: ref({'global': {value: null, matchMode: FilterMatchMode.CONTAINS},}),
            expandedRows: ref([]),
            projeto: new Object(),
            projetos:null,
            autores: null,
            selection: []
        }
    },
    methods: { 
        openNew() {
           this.nomeHeader = 'Cadastrar'
           this.projeto = new Object();
           this.submitted = false;
           this.projetoDialog = true;   
           this.getAutores();      
        },
        hideDialog() {
            this.projetoDialog = false;
            this.submitted = false;          
        },
        confirmDeleteProjeto(projeto){
            this.deleteProjetoDialog = true;
            this.projeto = projeto;    
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
        async getProjetos() {
            const req = await fetch('http://academiaelite.com.br:6612/projeto');
            if(req.status == 200){
                const data = await req.json();
                this.projetos = data;}
        },
        async getAutores() {
            const req = await fetch('http://academiaelite.com.br:6612/autor');
            if(req.status == 200){
                const data = await req.json();
                const arrayAutor = new Array()
                for(var index in data){
                    if(data[index].projeto == null){
                        arrayAutor.push(data[index])                       
                    }
                }               
                this.autores = [arrayAutor, []]; 
            }
        },
        async salvarProjeto(){
            this.submitted =true;                             
            const data = {
                    id: this.projeto.id,
                    area: this.projeto.area,
                    resumo: this.projeto.resumo,
                    titulo: this.projeto.titulo,
                    autores: this.autores[1]
            }              
            const dataJson = JSON.stringify(data); // trnasformando o objeto em string json 
            if(data.id === undefined){
                const req = await fetch("http://academiaelite.com.br:6612/projeto", { // Enviando a requisição
                    method: "POST",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                });      
                if(req.status === 201){  // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Cadastrado com sucesso!', life: 3000 });
                    this.projetoDialog = false;}
            }else {
                const req = await fetch("http://academiaelite.com.br:6612/projeto", { // Enviando a requisição
                    method: "PUT",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                });      
                if(req.status === 200){   // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Alterado com sucesso!', life: 3000 });
                    this.projetoDialog = false;}}
            this.getProjetos();
            if(data.autores.length > 0)
                 this.getAutores();
        },
        async editarProjeto(projeto){
            this.nomeHeader = 'Alterar';
            const req = await fetch('http://academiaelite.com.br:6612/projeto/'+projeto.id);
            if(req.status == 200){
                const data = await req.json();
                this.projeto = data;
                this.selection = data.autores;
                this.projetoDialog = true;}
            if(req.status == 404){
                this.toast.add({ severity: 'error', summary: 'Error!', detail: 'Não foi encontrado o prêmio!', life: 3000 });} 
        
            var array = []
            for(let k=0; k<this.autores[1].length; k++){
                if('projeto' in this.autores[1][k]){
                    array.push(this.autores[1][k])
                }
            }
            //trocando o que esta em selecionado pelo request feito em edit
            this.autores[1] = this.selection

           // removo todos os que estao no selecionado que nao fazem parte do request feito em edit
            for(let i=0; i<this.autores[0].length; i++){
                for(let j=0; j<this.selection.length; j++){
                    if(this.selection[j].id === this.autores[0][i].id){
                        this.autores[0].splice(i, 1);
                    } 
                }                            
            }

           // removendo todos do index 0 que tem relacionamento com projeto
            for(let k=0; k<this.autores[0].length; k++){
                if('projeto' in this.autores[0][k]){
                   continue
                }else{
                    this.autores[0].splice(k)
                }
            }
           
            if(array.length > 0){
               var arrayConcat =  this.autores[0].concat(array)
               this.autores[0] = arrayConcat
            }
           
        },
        async deleteProjeto(){
            const req = await fetch("http://academiaelite.com.br:6612/projeto/delete/"+this.projeto.id, { // Enviando a requisição
                method: "DELETE",
                headers:{ "Content-Type": "application/json" }
            }); 
            if(req.status == 200){
                this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Excluido com sucesso!', life: 3000 });
                this.deleteProjetoDialog= false;
            }
            this.getProjetos();
        }
    },
    mounted() {
        this.getProjetos();
        this.getAutores();
    },
    components: {
        InputText,
        Textarea,
        Calendar,
        Button,
        Fieldset,
        Dialog,
        PickList
    }
}
</script>

<style scoped lang="scss">
.label-cor{
    color: red;
}
@import '@/assets/demo/styles/badges.scss';
</style>
