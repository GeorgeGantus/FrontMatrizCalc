<template>
  <view class="container">
    <view class="title" >
       <text class="title-text">Calculadora Matricial</text>
    </view>
    <button v-bind:onPress="handleBtnClickCount" :title="btnTitle" />
    <text class="text-container">Numero de Linhas:  {{btnClickCount}}</text>
     <text-input
      class="text-input-container"
      placeholder="Digite o valor a ser inserido"
      v-model="textContent"
    />
    <view class="hello">
    <view class="btnLine" >
    <button v-bind:onPress="currentLineMinus" title="Linha anterior" />
    </view>
    <view class="btnLine">
    <button v-bind:onPress="currentLinePlus" title="Proxima Linha" />
    </view>

    </view>
    <view class="hello">
    <view class="btnLine">
    <button v-bind:onPress="addItemOnLine" title="Adicionar Item" />
    </view>
    <view class="btnLine">
    <button v-bind:onPress="removeItem" title="Remover Item" />
    </view>

    </view>
    
    <view class="flexRowCenter">
      <view class="lineItem text-container" v-for="index in btnClickCount" :key="index" >
      <text  :class="{selectedItemColor:index == line+1,'text-container':index != line+1}">{{index -1}}:</text>
      <text class="text-container" v-for="(item,i) in items[index-1]" :key="i">{{item}}</text>
      </view>
    </view>
    <view class="hello">
      <view class="btnLine">
        <button  v-bind:onPress="clear" title="Limpar" />
      </view>
      <view class="btnLine">
        <button v-bind:onPress="calculate" title="Calcular" />
      </view>
    </view>
    
    <view v-if="!loading">
    <text v-if="res" class="text-container">Determinante:{{res.det}}</text>
    </view>
    <view v-else>
    <text v-if="res" class="text-container">Carregando</text>
    </view>
  </view>
</template>
<script>
import axios from 'axios'
export default {
  data: function() {
    return {
      btnTitle: "Adicionar Linha",
      btnTitleItem: "Adicionar Item",
      btnClickCount: 0,
      textContent: "",
      textValue: '',
      line: 0,
      items: [
      ],
      itemsManager:[
      ],
      res: null,
      loading:false,
    };
  },
  methods: {
    handleBtnClickCount: function() {
      //this.itemsManager.push('')
      this.items.push([])
      this.btnClickCount = this.btnClickCount + 1;
    },
    currentLinePlus: function() {
      if (this.line < this.btnClickCount -1) {
        this.line = this.line + 1;
      }
    },
    currentLineMinus: function() {
      if (this.line > 0) {
        this.line = this.line - 1;
      }
    },
    addItemOnLine: function() {
      if (this.items[this.line].length < this.btnClickCount) {
        
        this.items[this.line].push(this.textContent)
      }
    },
    removeItem: function() {
      if (this.items[this.line].length > 0) {
        
        this.items[this.line].pop()
      }
    },
    calculate: function() {
      /* if (this.btnClickCount == 0) {
        return
      } */
      this.loading = true
      let matriz = []
      this.items.forEach(col => {
        let numericCol = []
        col.forEach(element => {
          numericCol.push(+element)
        });
        matriz.push(numericCol)
      });
      axios.post('https://calculardeterminante.herokuapp.com/calcularDeterminante',{matriz}).then(response => {
        this.res = response.data
        this.loading = false;
      })

    },
    clear: function(params) {
      this.items = []
      this.btnClickCount = 0
      this.line = 0
    }
  }
};
</script>
<style>
.container {
  /* background-color: white;
  justify-content: center;
   */
  align-items: center;
  flex: 1;
  margin-top: 0px;
  display:flex
}

.text-container {
  color: blue;
  padding: 2;
  font-size: 20;
}
.btnLine{
  margin-bottom: 20px;
  width: 40%;
  align-self: center;
  padding: 10px;
}
.teste{
  background-color:red;
}
.teste2{
  display: flex;
  margin:20px;
}
.text-input-container{
  margin-bottom: 10px;
  border-width: 2;
  border-radius: 5;
  border-color: #23a0e8;
  padding: 10px;
}
.hello{
  display:flex;
  flex-direction: row;
  justify-content: center;
}
.lineItem{
  display: flex;
  flex-direction: column;
  margin-right: 40px;
}
.flexRowCenter{
  display: flex;
  flex-direction: row;
  margin-left: 30px;
}
.flexRowCenter view:first-of-type{
  margin-left: 100px;
}
.selectedItemColor{
  color: red;
  padding: 2;
  font-size: 20;
}
.text-container text{
  color: blue;
  padding: 2;
  font-size: 20;
}
.title{
  padding-top: 50;
  padding-bottom: 10;
  margin-bottom: 10;
  background-color: #23a0e8;
  width:100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
}
.title-text{
  color:white;
  font-size: 30px;
}
</style>
