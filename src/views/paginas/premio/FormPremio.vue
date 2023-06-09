
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

                <DataTable
                    ref="dt"
                    :value="premios"
                    dataKey="id"
                    :paginator="true"
                    :rows="10"
                    :filters="filters"
                    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                    :rowsPerPageOptions="[5, 10, 25]"
                    currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} prêmios"
                    responsiveLayout="scroll">
                    <template #header>
                        <div class="flex flex-column md:flex-row md:justify-content-between md:align-items-center">
                            <h5 class="m-0">Gerenciar prêmios</h5>
                            <span class="block mt-2 md:mt-0 p-input-icon-left">
                                <i class="pi pi-search" />                              
                                <InputText v-model="filters['global'].value" placeholder="Buscar..." /> 
                            </span>
                        </div>
                    </template>
               
                        <Column headerStyle="width: 3rem"></Column>
                      <!--  <Column field="id" header="ID" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span class="p-column-title">Id</span>
                                {{ slotProps.data.id }}
                            </template>
                        </Column>-->
                        <Column field="nome" header="Nome" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ slotProps.data.nome }}
                                </span>
                            </template>
                        </Column>
                        <Column field="descricao" header="Descrição" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ slotProps.data.descricao }}
                                </span>
                            </template>
                        </Column>
                        <Column field="cronograma" header="Cronograma data início" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ formatDate(slotProps.data.cronograma.dataInicio) }}
                                </span>
                            </template>
                        </Column> 
                        <Column field="cronograma" header="Cronograma data fim" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{ formatDate(slotProps.data.cronograma.dataFim) }}
                                </span>
                            </template>
                        </Column>
                        <Column field="cronograma" header="Cronograma descrição" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{slotProps.data.cronograma.descricao }}
                                </span>
                            </template>
                        </Column>
                        <Column field="ano" header="Ano" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    {{slotProps.data.ano }}
                                </span>
                            </template>
                        </Column>
                        <Column  headerStyle="min-width:10rem;">
                            <template #body="slotProps">
                                <span v-if="slotProps.data == null">
                                    <Skeleton></Skeleton>
                                </span>
                                <span v-else>
                                    <Button icon="pi pi-pencil" class="p-button-rounded p-button-success mr-2" @click="editarPremio(slotProps.data)" />
                                    <Button icon="pi pi-trash" class="p-button-rounded p-button-danger mt-2" @click="confirmDeletePremio(slotProps.data)" />
                                </span>
                            </template>
                        </Column>
             
                </DataTable>

                <Dialog v-model:visible="premioDialog" :style="{ width: '700px' }" :header="nomeHeader" :modal="true" class="p-fluid">
                    <Fieldset legend="Prêmio">
                        <div class="field">
                            <label for="name">Nome</label>
                            <InputText id="name" v-model.trim="premio.nome" required="true" autofocus :class="{ 'p-invalid': submitted && !premio.nome }" />
                            <small class="p-invalid label-cor" v-if="submitted && !premio.nome">O nome é obrigatório.</small>
                        </div>
                        <div class="field">
                            <label for="description">Descrição</label>
                            <Textarea id="description" v-model="premio.descricao" required="true" rows="3" cols="20" :class="{ 'p-invalid': submitted && !premio.descricao }" />
                            <small class="p-invalid label-cor" v-if="submitted && !premio.descricao">A descrição é obrigatória.</small>
                        </div>
                        <Fieldset legend="Cronograma">
                            <div class="formgrid grid">
                                <div class="field col">
                                    <label for="dataInicio">Data início</label>
                                    <Calendar id="dataInicio" dateFormat="dd/mm/yy" v-model="cronograma.dataInicio" required="true" showIcon :class="{ 'p-invalid': submitted && !cronograma.dataInicio }" />
                                    <small class="p-invalid label-cor" v-if="submitted && !cronograma.dataInicio">A data início é obrigatória.</small>
                                </div>
                                <div class="field col">
                                    <label for="dataFim">Data fim</label>
                                    <Calendar id="dataFim" dateFormat="dd/mm/yy"  v-model="cronograma.dataFim" required="true" showIcon :class="{ 'p-invalid': submitted && !cronograma.dataFim}" />
                                    <small class="p-invalid label-cor" v-if="submitted && !cronograma.dataFim">A data fim é obrigatória.</small>
                                </div>
                            </div>
                            <div class="field">
                                <label for="cronogDescricao">Descrição</label>
                                <Textarea id="cronogDescricao" v-model="cronograma.descricao" required="true" rows="3" cols="20" :class="{ 'p-invalid': submitted && !cronograma.descricao }" />
                                <small class="p-invalid label-cor" v-if="submitted && !cronograma.descricao">A descrição do cronograma é obrigatória.</small>
                            </div>
                        </Fieldset>
                        <div class="field">
                            <label for="ano">Ano</label>
                            <Calendar id="ano" v-model="premio.ano"  required="true" view="year" dateFormat="yy" :class="{ 'p-invalid': submitted && !premio.ano }"/>
                            <small class="p-invalid label-cor" v-if="submitted && !premio.ano">O ano é obrigatório.</small>
                        </div>
                    </Fieldset>
                    <template #footer>
                        <Button label="Cancelar" icon="pi pi-times" class="p-button-text" @click="hideDialog" />
                        <Button label="Salvar" icon="pi pi-check" class="p-button-text" @click="salvarPremio" />
                    </template>
                </Dialog>

                <Dialog v-model:visible="deletePremioDialog" :style="{ width: '450px' }" header="Confirmar" :modal="true">
                    <div class="flex align-items-center justify-content-center">
                        <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                        <span v-if="premio"
                            >Tem certeza de que deseja excluir <b>{{ premio.nome }}</b
                            >?</span
                        >
                    </div>
                    <template #footer>
                        <Button label="Não" icon="pi pi-times" class="p-button-text" @click="deletePremioDialog = false" />
                        <Button label="Sim" icon="pi pi-check" class="p-button-text" @click="deletePremio" />
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
import Calendar from 'primevue/calendar';
import { useToast } from "primevue/usetoast";
import Button from 'primevue/button';
import Fieldset from 'primevue/fieldset';
import Skeleton from 'primevue/skeleton';

export default {
    name: 'FormPremio',
    data() {
        return {
            nomeHeader: 'Cadastrar',
            toast: useToast(),
            premioDialog: false,
            deletePremioDialog: false,
            filters: ref({'global': {value: null, matchMode: FilterMatchMode.CONTAINS},}),
            submitted: false,
            premios: ref(new Array(4)),
            premio: new Object(),
            cronograma: new Object()
        };
    },
    methods: { 
        openNew() {
           this.nomeHeader = 'Cadastrar'
           this.premio = new Object();
           this.cronograma = new Object();
           this.submitted = false;
           this.premioDialog = true;         
        },
        hideDialog() {
            this.premioDialog = false;
            this.submitted = false;
        },
        confirmDeletePremio(premio){
            this.deletePremioDialog = true;
            this.premio = premio;
            this.cronograma = premio.cronograma;      
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
        formatarAno(value){          
            if(isNaN(parseInt(value))){
                let date = new Date(value);
                return date.getFullYear();
            }
            return value;
        },        
        async getPremios() {
            const req = await fetch('https://jose2550.c41.integrator.host/backend/premio');
            if(req.status === 200){ 
                const data = await req.json();
                this.premios = data;
            }
        },
        async salvarPremio(){
            this.submitted =true;                    
            const data = {
                    id: this.premio.id,
                    nome: this.premio.nome,
                    descricao: this.premio.descricao,
                    cronograma:{
                        id: this.cronograma.id,
                        dataInicio: this.formatarStringData(this.cronograma.dataInicio),
                        dataFim:  this.formatarStringData(this.cronograma.dataFim),
                        descricao: this.cronograma.descricao
                    }, 
                    ano: this.formatarAno(this.premio.ano)
            }              
            const dataJson = JSON.stringify(data); // trnasformando o objeto em string json 
            if(data.id === undefined){
                const req = await fetch("https://jose2550.c41.integrator.host/backend/premio", { // Enviando a requisição
                    method: "POST",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                });      
                if(req.status === 201){  // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Cadastrado com sucesso!', life: 3000 });
                    this.premioDialog = false;}
            }else {
                const req = await fetch("https://jose2550.c41.integrator.host/backend/premio", { // Enviando a requisição
                    method: "PUT",
                    headers:{ "Content-Type": "application/json" },
                    body: dataJson
                });      
                if(req.status === 200){   // reposta do envio 
                    this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Alterado com sucesso!', life: 3000 });
                    this.premioDialog = false;}}
            this.getPremios();
        },
        async editarPremio(premio){
            this.nomeHeader = 'Alterar';
            const req = await fetch('https://jose2550.c41.integrator.host/backend/premio/'+premio.id);
            if(req.status == 200){
                const data = await req.json();
                this.premio = data;
                this.premio.ano = ""+data.ano;
                this.cronograma={
                        id: data.cronograma.id,
                        dataInicio: this.formatDate(data.cronograma.dataInicio),
                        dataFim:  this.formatDate(data.cronograma.dataFim),
                        descricao: data.cronograma.descricao
                };
                this.premioDialog = true;}
            if(req.status == 404){
                this.toast.add({ severity: 'error', summary: 'Error!', detail: 'Não foi encontrado o prêmio!', life: 3000 });}
        },
        async deletePremio(){
            const req = await fetch("https://jose2550.c41.integrator.host/backend/premio/delete/"+this.premio.id, { // Enviando a requisição
                method: "DELETE",
                headers:{ "Content-Type": "application/json" }
            }); 
            if(req.status == 204){
                this.toast.add({ severity: 'success', summary: 'Sucesso!', detail: 'Excluido com sucesso!', life: 3000 });
                this.deletePremioDialog = false;
            }
            this.getPremios();
        }
    },
    mounted() {
       this.getPremios()
    },
    components: {
        InputText,
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
