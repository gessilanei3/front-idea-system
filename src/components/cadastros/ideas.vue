<template>
<div>
    <link rel="stylesheet" href="vue-modal.css">
 
            <a href="#" class="px-4 py-1 text-sm text-blue-600 bg-blue-200 rounded-full" id='pointmouser' @click="showModal=true" v-if="!id">+ Adicionar Ideia</a>
            <a href="#" class="px-4 py-1 text-sm text-blue-600 bg-blue-200 rounded-full" @click="showModal=true" v-if="id">Editar</a> 
     

        <FormModal :based-on="showModal" style='width:800px;'   :title="id==false?'CADASTRAR IDEIA':'ATUALIZAR IDEIA'" @close="showModal = false">

            <div class="bg-white text-gray-500 rounded-3xl shadow-xl w-full overflow-hidden" style="max-width:900px">
                <div class="md:flex w-full">
                    
                    <div class="w-full md:w-1/1 py-10 px-5 md:px-10">
                        <!-- <div class="text-center mb-100">
                            <h1 class="font-bold text-3xl text-gray-900 text-sky-600">CADASTRAR IDEIA</h1>
                            <br><br>
                        </div> -->
                        <form  @submit.prevent="cadastrar()" method="POST">
                        <div>
                            <div class="text-center">
                                <div class="w-full px-3 mb-5 text-left">
                                    
                                        <label for="" class="text-xs font-semibold px-3">IDEIA</label>
                                        <div class="text-center flex items-center border-b border-gray-500 py-2" style=''>
                                        <input v-model="idea_name" type="text" class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none" placeholder="Nome da ideia" >
                                        </div><br>

                                        <label for="" class="text-xs font-semibold px-3">DESCRIÇÃO</label>
                                        <div class="text-center flex items-center border-b border-gray-500 py-2">
                                        <textarea v-model="idea_description" type="text" class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none" placeholder="Descrição da ideia" ></textarea>
                                        </div><br>

                                        <label for="" class="text-xs font-semibold px-3">VALOR PARA REALIZAÇÃO R$ {{moedas}}</label>
                                        <div class="flex py-2">
                                        <input oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" style='padding:4px;border:1px solid gray ' type="number" maxlength = "11" value='' id='moeda' v-model="modeas" v-on:keyup="formataDinheiro();">
                                        </div><br>

                                        <label for="" class="text-xs font-semibold px-3">USUÁRIO</label>
                                        <div class="text-center flex items-center border-gray-500 py-2" >
                                            <b>{{userId.user_name}}</b>, você será o criador desta ideia
                                        </div><br>

                                        <label for="" class="text-xs font-semibold px-3">MISSÃO</label>
                                        <div class="text-center flex items-center border-gray-500 py-2" >
                                            Sua missão é: <b> {{mission_name}}</b>
                                        </div><br>  
                                    
                                        <label for="" class="text-xs font-semibold px-3">CATEGORIA</label>
                                        <div class="text-center flex items-center border border-gray-500 py-2" >
                                        <select v-model="category_id" required='' class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none">
                                        <option v-for="cat in isCats" :key='cat.id' :value='cat.id'>{{cat.cat_name}}</option> 
                                        </select>   
                                        </div><br>

                                        <label for="avatar" class="text-xs font-semibold px-3">ADICIONAR CAPA</label>
                                        <div class="flex items-center justify-center pt-5" >
                                            <input type="file" multiple id="avatar"  ref="avatar" name="avatar[]" >
                                        </div><br>

                                        
                                </div>
                            </div>
        
                            <div class="flex -mx-3">
                                <div class="w-full px-3 mb-5">
                                    <button class="block w-full max-w-xs mx-auto bg-indigo-500 hover:bg-indigo-700 focus:bg-indigo-700 text-white rounded-lg px-3 py-3 font-semibold" >CADASTRAR</button>
                                </div>
                            </div>
                        </div>
                        </form>
                    </div>
                </div>
            </div>
        
        </FormModal>
    </div>  
</template>

<script type="text/javascript">
    import VueModal from '@kouts/vue-modal'
    import '../vue-modal.css'
    import Vue from 'vue'
    import store from '../../store';
    import { Missionid } from '../../services/resources';
    import { Ideid } from '../../services/resources';

    Vue.component('FormModal', VueModal)

    export default {
        name: 'Ideias',
            props: [
                'id', 'idmis'
            ],
        data(){
            return {
                showModal: false,
                idea_name: '',
                idea_description: '',
                user_id: store.state.auth.user.id,
                category_id: '',
                mission_id: this.$route.params.idmis,
                mission_name: null,
                ies_status: 'A',
                allimages: [],
                id_user: null,
                category_id: null,
                count: null,
                moedas: null,
            }
        },
         components: {
        'FormModal': VueModal
         },
        created(){
            if(!this.id){
                Ideid.query({id: this.id}).then(response => {
                    this.idea_name = response.data.idea_name,
                    this.idea_description = response.data.idea_description,
                    this.user_id = response.data.user_id,
                    this.category_id = response.data.category_id,
                    this.mission_id = response.data.mission_id
                })     
            }
           
            store.dispatch('load-users');
            store.dispatch('load-categories');
            store.dispatch('load-missions');
            Missionid.query({id: this.idmis}).then(response => {
               this.mission_name = response.data.mis_name
            })
        },
        computed: {
            isUser(){
                return  store.state.users;
            },
            userId() {
            return store.state.auth.user ? store.state.auth.user : {'user_name': ''}; 
            },
            isCats(){
                return  store.state.categories;
            },
            isMiss(){
               return  store.state.missions; 
            }
          

        },
        methods: {
            formataDinheiro() {
                var n = document.getElementById("moeda").value
                var v = n.replace(/\D/g,'');
                v = (v/100).toFixed(2) + '';
                v = v.replace(".", ",");
                v = v.replace(/(\d)(\d{3})(\d{3}),/g, "$1.$2.$3,");
                v = v.replace(/(\d)(\d{3}),/g, "$1.$2,");
                this.moedas = v

            },
            cadastrar(){
               if(!this.id){
                if(this.idea_name.length <= 4){
                    alert('Preencha no mínimo 5 caracteres na ideia!');
                }else{
                    
                    // alert('você está criando')
                    let formData = new FormData();
                    var av = document.getElementById('avatar');
                    // console.log('av', av.files)
                    for( var i = 0; i < av.files.length; i++ ){
                        let file = av.files[i];
                        formData.append('file_' + i, file);
                    }
                    this.count = av.files.length;
                    // console.log('append com stringify= ', JSON.stringify(this.allimages))

                    formData.append('idea_name', this.idea_name);
                    formData.append('idea_description', this.idea_description);
                    formData.append('category_id', this.category_id);
                    formData.append('mission_id', this.idmis);
                    formData.append('user_id', this.user_id);
                    formData.append('count', this.count);
                    formData.append('ies_status', this.ies_status);
        
                    for (var i = 0; i < this.allimages.length; i++) {
                        console.log('a imagem:', i, 'é', this.allimages[i].name);
                    }
                    console.log('conteudo do formdata', [...formData])
                        
                    store.dispatch('saveidea', formData)
                    .then((response) => {
                        // console.log('resposta do rails:', response.data)
                        this.$router.push({name: 'detalhesmiss'});
                        location.reload(true);
                        alert('Ideia cadastrada com sucesso!')
                        })
                    .catch((responseError) => {
                        console.log('erro no cadastro de ideia: /ideas.vue')
                    })

                    this.allimages = []
                }}else{
                      if(confirm("Deseja realmente editar essa ideia?")){
                        let formData = new FormData();
                        var av = document.getElementById('avatar');
                        // console.log('av', av.files)
                        for( var i = 0; i < av.files.length; i++ ){
                            let file = av.files[i];
                            formData.append('file_' + i, file);
                        }
                        this.count = av.files.length;
                        // console.log('append com stringify= ', JSON.stringify(this.allimages))

                        formData.append('idea_name', this.idea_name);
                        formData.append('idea_description', this.idea_description);
                        formData.append('category_id', this.category_id);
                        formData.append('mission_id', this.idmis);
                        formData.append('user_id', this.user_id);
                        formData.append('count', this.count);
                        formData.append('ies_status', this.ies_status);
            
                        for (var i = 0; i < this.allimages.length; i++) {
                            console.log('a imagem:', i, 'é', this.allimages[i].name);
                        }
                        console.log('conteudo do formdata', [...formData])
                        
                        Ideid.update({id: this.id}, {idea: formData}).then(response => {
                            location.reload(true);
                            alert('Ideia atualizado com sucesso!')
                        }, response => {    
                            console.log('DEU ERRADO!')
                        });
                        
                        this.$router.push({name: 'listideas'});
                        }else{

                     }
                }   
            }
        }
    }
</script>

<style>

</style>