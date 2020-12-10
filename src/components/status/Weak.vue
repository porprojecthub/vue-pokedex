<template>
<div>
  <div id="progress" :style="_value"></div>
</div>
</template>

<script>

export default {
  name: 'Weak',
  props: {
    value: {
      type: Number
    }
  },
  computed: {
      _value(){
              if(parseInt(this.value) === 0){
                return  {'--status-param': '100%'}
              }
                 return  {'--status-param': '0%'}

      }
  }
}
</script>

<style scoped>

  #progress{
     background: #d1cfcf;
     border-radius: 13px;
     height: 20px;
     width: 91%;
  }

  #progress:after {
    content: '';
    display: block;
    background: #e44c4c;
    width: var(--status-param);
    height: 100%;
    border-radius: 9px;
}
  
</style>
