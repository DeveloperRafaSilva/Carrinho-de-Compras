<template>
  <div id="app">
    <h1 class="titulo">Desserts</h1>
    <div class="grid-container">
    <div class="container-produtos">
      <div  class="card-item" v-for="(produto, index) in produtos" :key="index">
        <div class="cart-add-div">
          <img :src="require('@/assets/' + produto.src)" alt="">
          <div v-if="produto.quantidade >= 1" class="btn-aumentar-cart-item">
              <img @click="diminuir(index)" class="icones" src="../src/assets/icon-decrement-quantity.svg" alt="AumentarProdutos">
              <span>{{produto.quantidade}}</span>
              <img @click="aumentar(index)" class="icones" src="../src/assets/icon-increment-quantity.svg" alt="AumentarProdutos">
          </div>
          <div v-else @click="verificarSeExiste(index)" class="btn-add-cart-item">
            <img src="@/assets/icon-add-to-cart.svg" alt="Icone add cart">
            <span>ADD TO CART</span>
          </div>
        </div>
        <div class="infos-produto">
          <p class="categoria-produto">{{produto.categoria}}</p> 
          <h2 class="nome-produto">{{produto.nome}}</h2> 
          <p class="preco-produto">{{formatarMoeda(produto.preco)}}</p>
        </div>
      </div>
    </div>
    <div class="carrinho-card">
      <h2>Yout cart {{produtosAdicionados.length}}</h2>
      <div v-if="produtosAdicionados.length === 0" class="imagem-carrinho">
        <img src="@/assets/illustration-empty-cart.svg" alt="">
        <p>Your Added items will appear here</p>
      </div>
      <div v-else v-for="(produto,index) in produtosAdicionados" :key="index"   class="imagem-carrinho-produtos">
        <div  v-if="produto.quantidade >= 1">
          <p>{{produto.nome}}</p>
          <div class="infos-produtos">
            <p class="quantidade-carrinho">X{{produto.quantidade}}</p>
            <p class="lighth">{{formatarMoeda(produto.preco)}}</p>
            <p class="lighth">{{formatarMoeda(produto.preco * produto.quantidade)}}</p>
          </div>
        </div>
      </div>
      <div v-if="produtosAdicionados.length > 0" class="sub-total">
        <h3 class="total">Total</h3>
        <p>{{formatarMoeda(somaTota)}}</p>
      </div>
      <div v-if="produtosAdicionados.length > 0" class="btn-modal">
        <button @click="modalAtivc = true">Confirm new order</button>
      </div>
    </div>
  </div>
  <div v-show="modalAtivc" class="modal-confirme">
    <div class="topo-modal">
      <img src="@/assets/icon-order-confirmed.svg" alt="">
      <h2>Order Confirmed</h2>
      <p>We hoppe you enjoy your food</p>
    </div>
    <div class="produtos-bg">
      <div class="overflow"> 

        <div v-for="(produto,index) in produtosAdicionados" :key="index" class="overflow        produtos-order-confirmed">
        <img :src="require('@/assets/'+ produto.src)" alt="">
      <div class="infos-produto-modal">
        <div class="conteudo-info">
          <p>{{produto.nome}}</p>
          <span class="quantidade-carrinho">{{produto.quantidade}}X</span>
          <span class="lighth margin-left">{{formatarMoeda(produto.preco)}}</span>
        </div>
        <div class="soma-produto-item">
          <p class="total-produtos-item">{{formatarMoeda(produto.preco * produto.quantidade)}}</p>
        </div>
      </div>
      </div>
    </div>
    <div class="total-produto">
      <p>order total</p>
      <h3>{{formatarMoeda(somaTota)}}</h3>
    </div>
  </div>
  <div @click="modalAtivc = false" class="btn-modal">
    <button>Start new order</button>
  </div>
  </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      produtos:[],
      produtosAdicionados:[],
      somaTotal:0,
      modalAtivc:false
    };
  },
  methods: {
    fetchProdutos() {
      fetch("../apiProdutos.json")
        .then(response => response.json())
        .then(dados => {
          this.produtos = dados;
      });
    },
    formatarMoeda(preco){
      return  preco.toLocaleString('en-IN',{style:'currency',currency:'USD'})
    },
    verificarSeExiste(produtoClicado){
     const produtosExistents = this.produtosAdicionados.find(item => item.nome == this.produtos[produtoClicado].nome)
      if(produtosExistents){
        ++produtosExistents.quantidade
      }else{
        this.produtos[produtoClicado].quantidade++
        this.produtosAdicionados.push(this.produtos[produtoClicado])
      }
      console.log(this.produtos[produtoClicado])
    },
      aumentar(index){
      if(this.produtos[index].quantidade >= 1){
        this.produtos[index].quantidade++
      }
    },
     diminuir(index){
      if(this.produtos[index].quantidade > 0){
        this.produtos[index].quantidade--
        if(this.produtos[index].quantidade === 0){
        const indexRemover = this.produtosAdicionados.findIndex(item => item.nome ===this.produtos[index].nome ) 
        if(indexRemover !== -1){
          this.produtosAdicionados.splice(indexRemover,1)
        }
      }
      }
    },
  },
  computed:{
    somaTota(){
      return this.produtosAdicionados.reduce((cont,produto) =>{
        return cont + (produto.preco * produto.quantidade)
      },0)
    },
  },
  created() {
    this.fetchProdutos();
    if(this.modalAtivc === true){
      document.body.classList.add("on")
    }
  }
}
</script>

<style>
*{
  padding: 0;
  margin: 0;
  padding: 0;
}

body{
  background-color:hsl(20, 50%, 98%);
}

img{
  max-width: 100%;
  display: block;
}

#app{
z-index: 0;
    font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    padding: 3rem 2rem;
  }
.titulo{
  margin: 1rem 1.5rem;
  color:hsl(50, 65%, 29%)
}

.btn-add-cart-item{
  background-color: #fff;
  padding: .5rem 1.5rem;
  display: flex;
  position: relative;
  align-content: center;
  width: fit-content;
  margin: -2rem auto 0 auto;
  box-shadow: 1px 0px 5px 1px rgba(0, 0, 0, .2);
  cursor: pointer;
  border: 1px solid hsl(14, 86%, 42%);
  gap:.8rem;
  border-radius: 1rem;
}

.btn-add-cart-item img{
  display: inline-block;
}

.container-produtos{
  max-width: 1500px;
  gap: 2rem;
  margin: 0 auto;
  z-index: 1;
  display: grid;
  grid-template-columns: repeat(3,auto);
}

@media(max-width:800px){
  .container-produtos{
    grid-template-columns: repeat(2,auto);
  }
  .btn-add-cart-item{
    background-color: #fff;
    gap: .3rem;
    padding: .2rem 1rem;
  }
  .btn-add-cart-item span{
    font-size: .9rem;
  }
}
@media(max-width:750px){
  .container-produtos{
    grid-template-columns: repeat(1,auto);
  }
  .carrinho-card{
    margin-top: 1rem !important;
  }
  .modal-confirme{
    left: 60px !important;
  }
}

@media(max-width:600px){
  .modal-confirme{
    left: 10px !important;
    top: 100px !important;
  }
}

.infos-produto{
  margin-top: 1.5rem;
}

.categoria-produto{
  font-weight: bold;
  color: hsl(14, 25%, 72%);
}

.preco-produto,
.quantidade-carrinho{
  color: hsl(14, 86%, 42%);
  font-weight: 500;
}

.grid-container{
  padding: calc(48px - 16px);
  display: grid;
  gap: 2rem;
  grid-template-columns: auto auto;
}

@media(max-width:500px){
  .grid-container{
    gap: 3rem;
    grid-template-columns: auto;
  }
}

.carrinho-card{
  background-color: #fff;
  padding: 2rem 1rem;
  margin-top: -3.5rem;
  height: fit-content;
  max-width: 250px;
  width: 100%;
}

.imagem-carrinho-produtos{
  padding: 1rem 1.5rem;
  border-bottom: 2px solid #ccc;
}

.imagem-carrinho img {
  text-align: center;
  margin: 0 auto;
  width: fit-content;
}

.imagem-carrinho p{
  text-align: center;
}

.nome-produto{
  font-size: 1.3rem
}

.btn-aumentar-cart-item{
  display: flex;
  align-content: center;
  justify-content: center;
  gap:1.5rem;
  background-color: hsl(14, 86%, 42%);
  padding: .6rem 1.2rem;
  position: relative;
  margin: -2rem auto 0 auto;
  width: fit-content;
  border-radius: 1rem;
}

.icones{
  border: 1px solid #fff;
  padding: .3rem;
  width: 10px;
  height: 10px;
  border-radius: 50%;
}

.infos-produtos{
  gap: 1rem;
  margin: 1rem 0 0 0;
  display: flex;
  align-items: center;
}

.lighth{
  color:hsl(7, 20%, 60%);
}

.sub-total{
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 1.2rem;
}

.modal-confirme{
  background-color: #fff;
  padding: 1.5rem 2rem;
  position: fixed;
  z-index: 100;
  max-width: 400px;
  min-width: 150px;
  width: 100%;
  top: 20%;
  left: 20%;
  border-radius: 1rem;
  right: 40%;
  box-shadow: 1px 0px 5px 1px rgba(0, 0, 0, .2);
}

@media(max-width:450px){
  .modal-confirme{
   width: 300px;
   left: 10px !important;
   right: 0px;
  }
}

.produtos-order-confirmed{
  display: flex;
  gap: 1rem;
  align-items: start;
  justify-content: start;
}

.produtos-order-confirmed img{
  width: 80px;
  height: 80px;
}

.infos-produto-modal{
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.infos-produto-modal  div{
  flex: 1;
}

.produtos-bg{
  padding: .5rem;
  background-color: hsl(14, 15%, 82%); 
  border: 2px solid #ccc;
  border-radius: 16px;
}

.total-produto{
  padding: 1rem;
  border-top: 2px solid #fff;
  background-color: hsl(14, 15%, 82%); 
}

.total-produtos-item{
  font-weight: bold;
}

.margin-left{
  margin: 0 0 0 .5rem;
}

.topo-modal{
  margin-bottom: 1rem ;
}

.produtos-order-confirmed{
  padding: 10px;
  border-bottom: 2px solid #fff;
}

.total-produto{
  display: flex;
  align-items: center;
  justify-content: space-between;
}


.btn-modal button{
  padding: 1rem 1.2rem;
   background-color: hsl(14, 86%, 42%);
  display: block;
  margin: 1.5rem auto;
  max-width: 500px;
  width: 100%;
  border: none;
  border-radius: 20px;
  color: #fff;
}

.overflow{
  height: 80px;
  overflow: auto;
}

</style>
