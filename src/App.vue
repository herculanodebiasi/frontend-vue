<template>
  <div id="app">
    <nav>
      <div class="nav-wrapper blue darken-1">
        <a href="#" class="brand-logo center">Funcionários Frontend</a>
      </div>
    </nav>

    <div class="container">
      <form @submit.prevent="incluir">
        <label>ID</label> 
        <input disabled type="number" placeholder="ID" v-model="funcionario.id">
        
        <label>Nome</label> 
        <input :disabled=editavel type="text" placeholder="Nome" v-model="funcionario.nome">

        <label># Dependentes</label>
        <input :disabled=editavel type="number" placeholder="# Dependentes" v-model="funcionario.numDep"> 

        <label>Salário</label>
        <input :disabled=editavel type="number" placeholder="Salario" step="any" v-model="funcionario.salario">
        
        <label>Data de nascimento</label>
        <input :disabled=editavel type="date" placeholder="Data de nascimento" step="any" v-model="funcionario.nascimento">

        <button id="btnAcao" class="waves-effect waves-light btn-small" @click="editavel ? incluir() : salvar()">
          Incluir
          <i class="material-icons left">save</i>
        </button>
      </form>

      <table>
        <thead>
          <tr>
            <th>ID</th> 
            <th>Nome</th> 
            <th># Dependentes</th>
            <th>Salário</th> 
            <th>Data de Nascimento</th> 
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="funcionario of funcionarios" :key="funcionario.id">
            <td>{{ funcionario.id }}</td>
            <td>{{ funcionario.nome }}</td>
            <td>{{ funcionario.numDep }}</td>
            <td>{{ funcionario.salario.toLocaleString() }}</td>
            <td>{{ new Date(funcionario.nascimento + 'T00:00:00').toLocaleDateString() }}</td>
            <td>
              <button @click="editar(funcionario)" class="waves-effect btn-small blue darken-1">
                <i class="material-icons">create</i>
              </button>
              <button @click="excluir(funcionario)" class="waves-effect btn-small red darken-1">
                <i class="material-icons">delete_sweep</i>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script> 
  import Funcionario from './services/funcionarios';

  export default {
    data() {
      return {
        funcionario: {
          id: '',
          nome: '',
          numDep: '',
          salario: '',
          nascimento: ''
        },
        editavel: true,
        funcionarios: []
      }
    },

    mounted() {
      this.listar(true, 'Incluir');
    },

    methods: {
      limpar(editavel, mensagem) {
        this.funcionario = { 
          id: '', 
          nome: '',
          numDep: '',
          salario: '',
          nascimento: ''
        };

        this.editavel = editavel;
        document.getElementById('btnAcao').innerHTML = mensagem;
      },
      
      listar(editavel, mensagem) {
        Funcionario.listar()
          .then(resposta => {
            this.funcionarios = resposta.data;
            // console.log(this.funcionarios);
          })
          .then( () => { this.limpar(editavel, mensagem) } )
      },

      incluir() {
        this.limpar(false, 'Salvar');
      },

      editar(funcionario) {
        this.limpar(false, 'Salvar');
        
        this.funcionario = funcionario;
      },

      excluir(funcionario) {
        if (confirm('Deseja mesmo excluir o funcionario?')) {
          Funcionario.excluir(funcionario).
            then(() => {
              this.funcionario = {};
              alert('Funcionario excluído com sucesso!');
              this.listar(true, 'Incluir');
            });
        }
      },

      salvar() {
        if (!this.funcionario.id) {
          Funcionario.salvar(this.funcionario).
            then(() => {
              alert('Funcionario salvo com sucesso!');
              this.listar(true, 'Incluir');
            });
        } else {
          Funcionario.alterar(this.funcionario).
            then(() => {
              alert('Funcionario alterado com sucesso!');
              this.listar(true, 'Incluir');
            });
        }
      }
    }
  };
</script>

<style> 
</style>