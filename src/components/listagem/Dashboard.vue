<template>
<div>
    <a class='mr-2' v-for="(count, i) in countResults" :key="i" v-on:click.prevent="buscardept(count-1)"> PAGINA {{count}}</a>
    <br>
    <br>
        <div class="relative mx-auto text-gray-600"  style="float:left">    
            <input class="border-8 border-gray-300 bg-white h-10 px-5 pr-16 rounded-lg text-sm focus:outline-none" ref='search' id='search' type="search" placeholder="Busca" @keyup="buscardept()">
        </div>
      <br>
      <br>
    <div class='px-3 text-gray-500 shadow-xl' style="padding:10px;background-color:white;width:100%;border-radius:10px 10px 0px 0px;font-size:30px;margin-bottom:10px;">
        <span style='float:left;' class="font-bold text-3xl text-gray-900 text-sky-600">DEPARTAMENTOS:</span>

         <Departamentos :id="false" />

            <table class="divide-y divide-gray-300 "  width='100%' style=''>
                    <thead class="bg-blue-200">
                        <tr>
                            <th class="px-6 py-2 text-xs text-gray-500 text-left">
                                ID
                            </th>
                            <th class="px-6 py-2 text-xs text-gray-500 text-left">
                                NOME
                            </th>
                            <th class="px-6 py-2 text-xs text-gray-500 text-left">
                                CRIADO EM
                            </th>
                            <th class="px-6 py-2 text-xs text-gray-500 text-left">
                                
                            </th>
                            <th class="px-6 py-2 text-xs text-gray-500 text-left">
                                
                            </th>
                        </tr>
                    </thead>
                    <tbody class="bg-white divide-y divide-gray-300">
                        <tr class="whitespace-nowrap"
                        v-for="(depts, i) in isDept"
                        :key="i"
                        cols="12"
                        md="4"
                        lg="2">
                            <td class="px-6 py-4 text-sm text-gray-500">
                                {{depts.id}}
                            </td>
                            <td class="px-6 py-4">
                                <div class="text-sm text-gray-900">
                                    {{depts.dep_name}}
                                </div>
                            </td>
                            <td class="px-6 py-4 text-sm text-gray-500">
                                {{depts.created_at}}
                            </td>
                            <td class="px-6 py-4 text-right">
                                <Departamentos :id="depts.id"/>
                               <!-- <router-link v-bind:to="{ name: 'cadastrodepartamentos', params: {id: depts.id} }">
                                    <a href="#" class="px-4 py-1 text-sm text-blue-600 bg-blue-200 rounded-full" >Editar</a>
                                </router-link> -->
                            </td>
                            <td class="px-6 py-4">
                                <a href="#" class="px-4 py-1 text-sm text-red-400 bg-red-200 rounded-full" @click='deletedepto(depts.id, depts.dep_name), reRender()'>Excluir</a>
                            </td>
                        </tr>
                    </tbody>
                </table>    
            </div>
    </div>
</template>

<script>

import store from '../../store.js';
import Departamentos from '../cadastros/departamentos.vue'

export default {
    
    data () {
        return {
            rows: 100,
            currentPage: 1,
            menuPerfil: false,
            selected: '0',
            options: [],
            countpage: Math.ceil(store.state.depts.length/2)
            // index: this.selected
            }
            
    },
    created(){
        if(this.isAuth) {
            store.dispatch('load-depts');
        }
       
    },
    computed: {
        isDept(){
            return store.state.depts;
        },
        isAuth() {
            return store.state.auth.check;
        },
        countResults(){
            return this.countpage
        },
    },
    components: {
        'Departamentos': Departamentos
    },
    methods: {
        reRender(){
           store.dispatch('load-depts');
        },
        abrir() {
            this.menuPerfil = this.menuPerfil == false ? true : false;
            },

        deletedepto(id, dep_name){
             if (confirm('Deseja excluir o departamento '+ dep_name +' permanentemente?')){
              const req = fetch(`http://localhost:3000/depts/${id}`,{
                method: "DELETE"
              });
              };
                
                // store.dispatch('load-depts');
                location.reload(true);
                alert('Departamento excluido com sucesso!')
             },
        adddep(){
                this.$router.push({name: 'cadastrodepartamentos'});
            },
        buscardept(page){
            store.dispatch('load-buscadept', page);
        }
    },
    
  
}
</script>

<style scoped>
.mx-auto{
    text-align: justify;
}
</style>