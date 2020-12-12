<template>
<div>
<div class="header">
    <h1 style="font-size:40px;margin:8px;">My Pokedex</h1>
</div>

<div class="container">
  <div class="content">

    <div class="cards" 
    v-for="pokemon in myList" :key="pokemon.id" 
    @mouseover="pokemon.active = true"
    @mouseleave="pokemon.active = false">
       <div class="card-item">
          <div class="image"><img :src="pokemon.imageUrl" style="height:250px;padding-top:10px"/></div>
          <div class="data">
               <div style="color:#e44c4c;cursor:pointer;float:right;padding-right:20px;" v-if="pokemon.active" @click="removePokemon(pokemon.id)">X</div>
             <div class="name" style="padding-top:8px;padding-left:10px;">
               <span style="font-size:30px;font-weight:400;font-family:Gaegu;">{{pokemon.name}}</span>
             </div>
             <div class="status" style="padding-left:12px;">
                <div class="hp" style="padding-bottom:8px;">
                  <span>HP</span>
                  <Status :value="parseInt(pokemon.hpStatus)"/>
                </div>
                <div class="str" style="padding-bottom:10px;">
                  <span>STR</span>
                  <Status :value="parseInt(pokemon.strStatus)"/>
                </div>
                <div class="weak" style="padding-bottom:18px;">
                  <span>WEAK</span>
                  <Weak :value="parseInt(pokemon.weakStatus)"/>
                </div>
             </div>
             <div class="happy" style="padding-left:12px;">
               <Happy :happy="pokemon.happyStatus" />
             </div>
        </div>
      </div>
    </div>

  </div>
</div>
<Modal :modalStatus="openModal" :selectedList="myList" @addPokemon="addPokemon" @closeModal="closeModal"/>
<div id="add-button" @click="selectPokemon">+</div>
<div id="footer-button-panel"/>
<div class="footer"></div>
</div>
</template>

<script>
import Status from './../status/Status.vue'
import Weak from './../status/Weak.vue'
import Happy from './../happy/Happy.vue'
import Modal from './Modal.vue'
// import color from 'constants'

export default {
  name: 'Pokedex',
  components: {
    Happy,
    Status,
    Weak,
    Modal
  },
  data() {
    return {
      myList:[],
      openModal: ''
    }
  },
  methods:{
      selectPokemon(){
        this.openModal = 'open'
      },
      addPokemon(pokemon){
        pokemon.active = false
        this.myList.push(pokemon)
      },
      removePokemon(id){
        for(let i = 0; i < this.myList.length; i++){
          let pokemonId = this.myList[i].id
          if(id.trim().toUpperCase() === pokemonId.trim().toUpperCase()){
            this.myList.splice(i,1);
          }
        }
      },
      closeModal(){
        this.openModal = ''
      }
  }
}
</script>

<style scoped>
.header{
 position:absolute;
 top:0;
 left:0;
 right:0;
 width: 100%;
 overflow: auto;
 background-color: #ffffff;
 color: #000000;
 text-align: center;
 padding: 0.2px;
 margin: 0;   
 border:0;
 z-index: 1;
}

.footer{
    position:absolute;
    bottom:0;
    left:0;
    right:0;
    width: 100%;
    overflow: auto;
    background-color: #e44c4c;
    color: #ffffff;
    text-align: center;
    height: 70px;
    padding: 0.2px;
    margin: 0;
    border:0;   
    z-index: 1;
   }

   #footer-button-panel{
     content: "";
      width: 100px;
      height: 100px;
      border-radius: 50px;
      background: #e44c4c;
      position: absolute;
      left: 50%;
      bottom: 8px;
      border: 1px inset #ac2a2a;
      transform:translate(-50%,0%);
      z-index: 1;
   }

   #add-button{
     color: #ffffff;
     font-size: 70px;
     font-weight: bold;
     position: absolute;
     left: 50%;
     bottom: 5px;
     transform:translate(-55%,0%);
     cursor: pointer;
     z-index: 2;
   }

  .container{
    height: 100%;
    width: 100%;
    overflow: hidden;
    overflow-y: auto;
    box-sizing: border-box;
    position: absolute;
    top: 0;
    bottom: 0;
    margin:0;
    padding:0;
  }
  .content{
    position: relative;
    max-height: 100%;
    top: 90px;
    background-color: #ffffff;
  }

  .cards{
    display: inline-block;
    width: 420px;
    background-color: rgb(245, 243, 243);
    margin: 20px 10px 20px 50px;
    padding: 5px;
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  }

  .cards:hover{
    box-shadow: 0 6px 10px 0 rgba(0,0,0,0.2);
  }

  .card-item{
    display: flex;
    width: 100%;
    margin: 10px;
  }

  .image{
    flex-direction: column;
  }

  .data{
    display: block;
    width: 100%;
  }


</style>
