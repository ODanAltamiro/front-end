<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>FAÇA SUA ESCOLHA</h2>
    <div>
      <div class="col-md-4">
        <h3>Selecione seu produto</h3>
        <ul class="maquina list-group col-md-12" v-show="produtos.length > 0" :style="{height: style.maquina}">
          <li class="list-group-item" ref="produto" v-for="it in produtos" v-on:click="selecionar(it)">
            <label>{{it.nome}}</label>
            <!-- <img v-if="it.link" :src="it.link" width="40px"/> -->
          </li>
        </ul>
      </div>
      <div class="col-md-4">
        <h3>Finalize a compra</h3>
        <div class="col-md-12 finalizar" v-show="venda.produto" :style="{height: style.maquina}"  v-if="! response.status">
          <div class="row">
            <h4 class="produto__destacado">Cartão</h4>
            <form class="row" ref="form">
              <div class="form-group">
                <label>Número</label>
                <input type="text" name="número do cartão" v-model="venda.cartao" class="form-control" placeholder="número do cartão" required/>
              </div>
              <div>
                <button type="button" class="btn btn-default" v-on:click="finalizar">Comprar</button>
              </div>
            </form>
          </div>
        </div>
        <div class="col-md-12 finalizar" v-show="venda.produto" :style="{height: style.maquina}" v-else-if="response.status === -1">
          <div class="row">
            <h4 class="produto__destacado">Processando Compra</h4>
            <div class="row">
              <div class="form-group">
                <img src="http://www.turismodecoimbra.pt/wp-content/themes/turismocoimbra/images/loader.gif" height="150px"/>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-12 finalizar" v-show="venda.produto" :style="{height: style.maquina}" v-else>
          <div class="row">
            <h4 class="produto__destacado">Processo da Compra</h4>
            <div class="row">
              <div class="form-group">
                <img v-if="response.status === 201" src="https://cdn0.iconfinder.com/data/icons/smile-emoticons/78/smyle_emoticons-10-512.png" height="80px"/>
                <img v-else src="https://png.icons8.com/metro/1600/sad.png" height="80px"/>
              </div>
              <h3 v-if="response.status">{{response.mensagem}}</h3>
              <h3 v-else>Um erro ocorreu no servidor, tente novamente mais tarde</h3>
            </div>
          </div>
        </div>
      </div>
      <div class="col-md-4">
        <h3>Você selecionou</h3>
        <div class="col-md-12 finalizar" v-show="venda.produto" :style="{height: style.maquina}">
          <div class="row">
            <h4 class="row produto__destacado">Produto</h4>
            <div class="row" style="max-height: 120px; min-height: 150px">
              <h3 class="col-md-8">{{venda.produto.nome}}</h3>
              <div class="col-md-3 div__img">
                <img :src="venda.produto.link" style="width: 100%;max-height: 100%" />
              </div>
            </div>
            <div class="row produto__destacado">
              <label>Estoque: {{venda.produto.quantidade}}</label>
              <label>Preço: R$ {{venda.produto.precoExibido}}</label>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      appContext: document.getElementById('appContext').value,
      msg: 'Bem vindo a máquina de produtos',
      style: {
        maquina: undefined
      },
      venda: {
        cartao: '',
        produto: ''
      },
      produtos: [],
      response: {
        status: -1,
        mensagem: ''
      }
    }
  },
  created: function () {
    this.getProdutos()
  },
  methods: {
    finalizar: function () {
      let form = this.$refs.form
      if (form.checkValidity() === false) {
        form.reportValidity()
        return
      }

      let venda = this.venda
      let data = {
        cartao: venda.cartao,
        produto: venda.produto.id
      }

      this.$http.post(`${this.appContext}/api/comprar`, data).then(
        (response) => {
          let produtos = this.produtos
          let produto = response.data.produto
          let i = produtos.indexOf(this)
          produto.precoExibido = produto.preco.toString().replace('.', ',')
          produtos[i] = produto
          venda.produto = produto
          this.$set(this, 'produtos', produtos)
          this.$set(this, 'venda', venda)
          this.$set(this, 'response', {status: response.status, mensagem: response.data.mensagem})
          return
        },
        (ret) => {
          this.$set(this, 'response', {status: ret.response.status, mensagem: ret.response.data.mensagem})
          return
        }
      )
      this.$set(this, 'response', {status: -1, mensagem: ''})
    },
    getProdutos: function () {
      this.$http.get(`${this.appContext}/api/produtos`).then(
        (response) => {
          let produtos = response.data
          for (let i in produtos) {
            produtos[i].precoExibido = produtos[i].preco.toString().replace('.', ',')
          }
          this.$set(this, 'produtos', produtos)
          this.$nextTick(() => {
            this.calcular()
          })
        },
        (response) => {
          // debugger
        }
      )
    },
    selecionar: function (produto) {
      let venda = this.venda
      venda.produto = produto
      this.$set(this, 'venda', venda)
      this.$set(this, 'response', {status: undefined, mensagem: undefined})
    },
    calcular: function () {
      if (this.$refs.produto === undefined) {
        return
      }
      let total = 0
      for (let i = 0; i < 6; i++) {
        let produto = this.$refs.produto[i]
        total += produto.offsetHeight
      }
      total
      let style = this.style
      style.maquina = total + 'px'
      this.$set(this, 'style', style)
    }
  }
}
</script>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>
.list-group-item:hover {
  background-color: lightgray;
}

img, .div__img {
  padding: 0 !important;
  max-height: 120px;
  max-width: 120px
}

.btn-default {
  background-color: rgba(255, 255, 255, 1);
  color: black;
  font-size: 1em;
  font-weight: bolder;
  width: 100%;
}

.finalizar {
  background-color: rgba(255, 255, 255, 0.3);
  color: black;
}

.finalizar * * {
  margin: 0;
  padding: 15px;
}

.produto__destacado {
  background-color: rgba(255, 255, 255, 0.5);
  color: black;
}

.maquina {
  background-color: rgba(255, 255, 255, 0.3);
  overflow-y: auto;
  padding: 0;
  color: black;
}
.hello {
  color: white;
}
h1,
h2 {
  font-weight: normal;
}
</style>
