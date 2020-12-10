<template>
<div id="myModal" class="modal" ref="myModal" @click="closeModal($event)">
  <div class="modal-content">
    <div class="container">
  <div class="content">
    <div class="form-input">
      <div class="input-container" style="padding-left:50px;">
        <input class="input-field" style="font-size:40px;" type="text" v-model="search" @input="searchPokemon">
        <i class="fa fa-search icon" style="color:#e44c4c;font-size:40px;"></i>
      </div>
    </div>
    <div class="cards" 
        v-for="pokemon in dataList" :key="pokemon.id" 
        @mouseover="pokemon.active = true"
        @mouseleave="pokemon.active = false">
       <div class="card-item">
          <div class="image"><img :src="pokemon.imageUrl" style="height:250px;padding-top:10px"/></div>
           <div class="data">
               <div style="color:#e44c4c;cursor:pointer;float:right;padding-right:20px;" v-if="pokemon.active" @click="selectPokemon(pokemon)">Add</div>
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
  </div>
</div>
</template>
<script>
import axios from 'axios'
import Status from './../status/Status.vue'
import Weak from './../status/Weak.vue'
import Happy from './../happy/Happy.vue'

export default {
  name: 'Modal',
  props: {
    modalStatus: {
      type: String
    },
    selectedList:{
      type: Array
    }
  },
  components: {
    Happy,
    Status,
    Weak
  },
  data() {
    return {
      masterList:[],
      dataList:[],
      search:'',
    }
  },
  methods:{
    initData(){
      this.$nextTick(async ()=>{
        this.masterList = await axios.get('http://localhost:3030/api/cards').then(resp => resp.data.cards.map(pokemon => {
          return this.getStatus(pokemon)
        }));

        if(this.selectedList.length){
          for(let i = 0; i < this.masterList.length; i++){
            let mId = this.masterList[i].id
            for(let j = 0; j < this.selectedList.length; j++){
              let sId = this.selectedList[j].id
              if(mId.trim().toUpperCase() === sId.trim().toUpperCase()){
                this.masterList.splice(i, 1);
                i = i-1
                continue;
              }
            }
          }
        } 

        this.dataList = this.masterList
    })
    },
    clearData(){
       this.masterList = []
       this.dataList = []
       this.search = ''
    },
    async searchPokemon(){

      this.dataList = this.masterList

      if(this.search != ''){

        let filterList = []

        for(let i = 0; i < this.dataList.length; i++){
            let pokemon = this.dataList[i]
            if(pokemon.name.trim().toUpperCase().indexOf(this.search.trim().toUpperCase()) > -1){
                filterList.push(pokemon)
            }
        }

        this.dataList = filterList

      }
    },
    closeModal(e){
      if(e.target.id === "myModal"){
      this.$emit('closeModal')
      }
    },
    selectPokemon(pokemon){
      if(pokemon){
        this.$emit('addPokemon',pokemon)
        for(let i = 0; i < this.masterList.length ; i++){
          let p = this.masterList[i]
          if(p.id.trim().toUpperCase() == pokemon.id.trim().toUpperCase()){
            this.masterList.splice(i, 1);
            break;
          }
        }
        this.dataList = this.masterList
        this.searchPokemon()
      }

    },
    getStatus(pokemon){

        if(pokemon){

            let {hp, attacks, weaknesses } = pokemon

            if(hp){
              if(parseInt(hp) > 100){
                  hp = 100
              }else if (isNaN(hp)){
                  hp = 0
              }
            }

            pokemon.hpStatus = hp || 0

            let str = 0
            if(attacks && attacks.length){
              str = attacks.length * 50
              if(str > 100){
                  str = 100
              }else if (str == 1){
                  str = 50
              }else if (str == 2){
                  str = 100
              }
            }

            pokemon.strStatus = str

            let weakness = 0
            if(weaknesses && weaknesses.length){
              weakness = weaknesses.length * 100
              if((weakness > 100) || (weakness == 1)){
                  weakness = 100
              }else{
                weakness = 0
              }
            }

            pokemon.weakStatus = weakness

            let damage = 0
            if(attacks && attacks.length){
                let dmg = 0
                for(let i = 0; i < attacks.length; i++){
                    let atk = attacks[i]
                    if(atk.damage && !isNaN(atk.damage)){
                        dmg += parseInt(atk.damage.replace(/[^0-9]/gi, ""));
                    }
                }
                damage = dmg
            }


            let happy = (((parseInt(hp) / 10) + (damage /10 ) + 10 - (weakness)) / 5) || 0

            pokemon.happyStatus = Math.ceil(happy)
            

        }

        pokemon.active = false

        return pokemon

      },
  },
  watch:{
      modalStatus(){
        if(this.modalStatus === 'open'){
          this.$refs.myModal.style.display = "block";
          this.initData()
        }else{
          this.$refs.myModal.style.display = "none";
          this.clearData()
        }
      }
    }

}
</script>

<style scoped>

.modal {
  display: none;
  position: fixed;
  z-index: 1;
  padding-top: 10px;
  left: 0;
  top: 0;
  width: 100%; 
  height: 100%;
  overflow: hidden;
  background-color: rgb(0,0,0); 
  background-color: rgba(0,0,0,0.4);
  z-index: 10;
}

.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 60%;
  height:50%;
  z-index: 10;
  height: 100%;
  overflow-y: auto;
  box-sizing: border-box;
  border-radius: 10px;
  left: 20%;
  top: 20;
  margin:0;
  position: absolute;
}
 .content{
    position: relative;
    max-height: 100%;
    top: 0px;
    background-color: #ffffff;
  }


  .input-container {
    display: flex;
    width: 100%;
    margin-bottom: 15px;
  }

  .icon {
    padding: 10px;
    color: white;
    min-width: 50px;
    text-align: center;
  }

  .input-field {
    width: 80%;
    padding: 10px;
    outline: none;
    font-family:Gaegu;
  }

  .input-field:focus {
    border: 2px solid #e44c4c;
  }

   .cards{
    display: inline-block;
    width: 90%;
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
