<template>
    <div>
        <link rel="stylesheet" href="vue-modal.css">

        <div class="col">
            <a href="#" class="px-4 py-1 text-sm text-blue-600 bg-blue-200 rounded-full" @click="showModal=true, loadmission(`${id}`)">Adicionar campos</a> 
        </div>

        <Modal :based-on="showModal" style='width:800px;'  title="CADASTRAR CAMPOS" @close="showModal = false">

            Missão: {{id == null ? 'ANTES DE CADASTRAR OS CAMPOS, SALVE A MISSÃO!' : id}}
            
            <br><br>

            <form @submit.prevent="attconsulta()" method="POST" name='adicionar_campos'>
                <input type='hidden' id='mission_id' :value='id'>
                <table>
                    <tr>
                        <td>Nome do campo:</td>
                        <td>Tipo do campo:</td>
                        <td>Extras:</td>
                        <td></td>
                    </tr>
                    <tr style='background-color:white;'>
                        <td>
                            <input type='text' placeholder="Nome do campo" id='input' v-model='add_campo.cam_nome'><br>
                        </td>
                        <td>
                            <select v-model="add_campo.cam_tipo" id='input'>
                                <option value=''>Selecione o Tipo do campo</option>
                                <option v-for="tipo in tiposcampos" :key='tipo.id' :value='tipo.name'>{{tipo.name}}</option>
                            </select>
                        </td>
                        <td> Ordem:
                            <input style='width:60px;' oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);" id='input' type='number' maxlength="2" v-model='add_campo.ies_ordem'> 
                            <input type='checkbox' v-model='add_campo.ies_obrigatorio' @change='checkbox()'> Obrigatório?
                        </td>
                        <td >
                            <button>+ Adicionar</button>
                        </td>
                    </tr>
                </table>
            </form>

            <br>
            <table>
                <tr>
                    <th>ID</th>
                    <th>Nome do Campo</th>
                    <th>Tipo do Campo</th>
                    <th></th>
                </tr>
                
                <tr v-for="campo in Allcampos" :key='campo.id' >
                    <td>{{campo.id}}</td>
                    <td>{{campo.cam_nome}}</td>
                    <td>{{campo.cam_tipo}}</td>
                    <td><input id='del' type='button' value='Deletar' @click='excluir(campo.id, campo.cam_nome)'></td>
                </tr>
            </table>
        </Modal>

    </div>
</template>
<script>
import VueModal from '@kouts/vue-modal'
import '../vue-modal.css'
import Vue from 'vue'
import store from '../../store';


Vue.component('Modal', VueModal)

export default {
    name: 'AdicCampos',
    props: [
        'id'
    ],
        data(){
            return {  
                  
                line: 1,
                add_campo: {
                    cam_nome: '',
                    cam_tipo: '',
                    ies_obrigatorio: 0,
                    ies_ordem: 0,
                    mission_id: null
                },
                

                tiposcampos: [
                    {'id': '1', 'name': 'Caracteres'},
                    {'id': '2', 'name': 'Valor decimal'},
                    {'id': '3', 'name': 'Texto'},
                    {'id': '4', 'name': 'Arquivo'},
                    {'id': '5', 'name': 'Data'}
                ],

                showModal: false,
            
            }
        },
    components: {
        'Modal': VueModal
    },
    created(){         

            store.dispatch('load-missions');

        },
        computed: {
            isMissions(){
                return store.state.missions;
            },
            Allcampos(){
                return store.state.campos;
            },
        },
        methods: {
            checkbox(){
                if(this.add_campo.ies_obrigatorio == true){
                    this.add_campo.ies_obrigatorio = 1
                }else{
                    this.add_campo.ies_obrigatorio = 0
                }
            },
            loadmission(id){
                store.dispatch('load-campos', id);
            },
            attconsulta(){

                this.add_campo.mission_id = document.getElementById('mission_id').value;
                if (this.add_campo.mission_id == ''){
                    alert('Salve a Missão primeiro!');
                }else{

                if(this.add_campo.cam_tipo && this.add_campo.cam_tipo){
                    
                    

                        store.dispatch('savecampo', this.add_campo)
                        .then((response) => {
                            alert('Cadastrado com sucesso!')
                            
                            store.dispatch('load-campos', this.add_campo.mission_id);
                            this.add_campo.cam_nome = ''
                            this.add_campo.cam_tipo = ''
                            this.add_campo.ies_ordem = 0
                            this.add_campo.ies_obrigatorio = 0
                        })

                    
                }else{
                    alert('Dê um nome/tipo para o campo!')
                }
                }
            },
            excluir(id, cam_name){
             if (confirm('Deseja excluir o campo ' + cam_name + '?')){
                const req = fetch(`http://localhost:3000/campos/${id}`,{
                method: "DELETE"
                });
                alert('Campo excluido com sucesso!')
                store.dispatch('load-campos', this.add_campo.mission_id);
             };
                
            }
            
        }

}
</script>
<style scoped>
#del:hover{
    cursor:pointer;
    text-decoration:underline;
}
#pointmouser:hover{
    cursor:pointer;
}
#input{
    border:1px solid black;
    padding:3px;
}
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

td, th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
</style>
