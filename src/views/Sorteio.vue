<template>
  <div class="container mt-2">
    <h1>Sorteio dos times</h1>

    <div class="qtdeParticipantes">
      <b-label>Quantos Jogadores: </b-label>
      <br>
      <input type="number" 
      v-model.number = "qtdeParticipantes" @change="changeInput"/>
    </div>

    <b-form class="container mt-2" id="formulario" v-if="ativarCampos" 
    @submit.prevent = "salvarParticipante()" >
      <div class="container mt-2">
        <b-label for="nome"> Nome Jogador: </b-label><br />
        <input type="text" id="nome" 
        autofocus required v-model="participante.nome" />
       
      </div>
       
      <div class="container mt-2"> 
        <b-label for="email"> Email: </b-label>
        <br>
        <input type="email" id="email" required v-model="participante.email" />
      </div>
      <br>
      <b-button>
      <div id="botao" class="outline-secondary">
        
        <input type="submit" :value="acao" />
      </div>
      </b-button>
    </b-form>
    <p v-else>A quantidade de participantes foi atingida!!!</p>
   
    <div class="ajuste">
      <div class="filtro">
        <label>Filtro por nome</label><br/>
        <input type="text" v-model="search">
      </div>
  <hr>
      <div class="sortear">
        <b-button variant="outline-primary"
        id="botao" @click="sortearParticipante()"> Sortear</b-button>
      </div>
    </div>
  <hr>
    <div class="container mt-2" >
      <table class="container mt-2">
        <th @click="ordenar('nome')" class="ordenar">Nome <font-awesome-icon :icon="getTipoOrdenacao('nome')" /> </th>
        <th @click="ordenar('email')" class="ordenar">Email <font-awesome-icon :icon="getTipoOrdenacao('email')" /> </th>
        <th>Opções</th>

        <tr v-for="(p, idx) in filteredItems" :key="p.nome" :class="{ 'alteracao': alteracaoIdx == idx }">
          <td id='nome' :class="{'destacar' :participanteSorteado(p.nome)}">{{ p.nome }}</td>
          <td id='email' :class="{'destacar' :participanteSorteado(p.nome)}">{{ p.email }}</td>
          <td>
            <b-button @click="alterar(idx)"><i class="fa-solid fa-edit"></i> </b-button>
            <b-button @click="remover(p)">  <i class="fa-solid fa-times"></i> </b-button>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      participantes: [
        
      ],
      participante: {
        nome: null,
        email: null
      },
      alteracaoIdx: -1,
      search: "", 
      ordemCampos: {
        nome: null,
        tipo: 'asc'
      },
      qtdeParticipantes: '',
      nomeSorteado: '',
      ativarCampos: true
    }
  },
  methods: {
    salvarParticipante() {
      const participanteSalvar = Object.assign({}, this.participante)
      const existe = this.participantes.some(p => p.nome == participanteSalvar.nome)
      if (this.alteracaoIdx > -1) {
          this.participantes[this.alteracaoIdx] = participanteSalvar
      } else {
        if (existe == true) {
          alert('Essa pessoa já foi adicionada.')          
        } else {
          this.participantes.push(participanteSalvar)        
        }        
      }
      this.changeInput()
      this.reset()      
    },
    reset() {
      this.participante.nome = null
      this.participante.email = null
      this.alteracaoIdx = -1
    },
    changeInput(){
      this.ativarCampos = this.participantes.length < this.qtdeParticipantes
    },
    remover(participanteParaRemover) {
      if (confirm("Excluir participante da partida?")) {
        this.participantes = this.participantes.filter(p => p != participanteParaRemover)
        this.reset()
      }
      this.changeInput()
    },
    alterar(idx) {
      this.participante = Object.assign({}, this.participantes[idx])
      this.alteracaoIdx = idx
      this.ativarCampos = true
    },
    sortearParticipante() {
      this.nomeSorteado = this.participantes[Math.round((Math.random() * this.qtdeParticipantes))].nome;
    }, 
    participanteSorteado(nome) {
      return this.nomeSorteado === nome
    },   
    getTipoOrdenacao(campo){
      if (campo == this.ordemCampos.nome) {
        if (this.ordemCampos.tipo == 'asc')
          return 'sort-up'
        return 'sort-down'
      }
      return 'sort'
    },
    ordenar(campo) {      
      if(campo == this.ordemCampos.nome) {
        this.ordemCampos.tipo = this.ordemCampos.tipo == 'asc' ? 'desc' :  'asc'
      } 
      this.ordemCampos.nome = campo
      const tipoOrdem = this.ordemCampos.tipo == 'asc' ? 1 : -1
      this.participantes = this.participantes.sort((a, b) =>
        a[campo].localeCompare(b[campo]) * tipoOrdem
      )      
    }
  },
  computed: {
    acao() {
      return this.alteracaoIdx > -1 ? "Alterar" : "Adicionar"
    },
    filteredItems() {
      let part = []
      part = this.participantes.filter(p => p = p.nome.toLowerCase().indexOf(this.search.toLowerCase()) > -1 )
        
      return part
    }
  }
}
</script>

</script>

<style>

