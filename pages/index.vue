<template>
<div class="div">

  <div class="box1">
    <h1>縣市區域</h1>
    <select id="sel" v-model="selectvalue" @change="onchange">
      <option value="" style="display: none" >請選擇</option>
      <option v-for=" (value, index) in obj" :key="index" :value= "index" >{{ value }}</option>
    </select>
  </div>
  
  <div class="box2">
  <table>
    <thead>
      <tr>
        <th colspan="2">鄉鎮市區</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="townname in TN" :key="townname">
        {{townname}}
      </tr>
    </tbody>
  </table>
  </div>

</div>
</template>

<script>
import axios from 'axios';
export default {
  data (){
    return {
      selectvalue: '',
      TN: []
    }
  },
  methods: {
    onchange() {
      this.fetchInfo();
    },
    async fetchInfo() {
      console.log(this.selectvalue);
      let ip = await axios.get('https://api.nlsc.gov.tw/other/ListTown/'+this.selectvalue)
      let parser2 = new DOMParser();
      let townsListDOM = parser2.parseFromString(ip.data, 'text/xml');
      let townsItem = townsListDOM.getElementsByTagName('townItem');
      let c,d;
      let TC = [];
      let TN = [];
      for (let towns of townsItem) {
        let item2 = towns.children;
        c = item2[0].innerHTML//towncode
        d = item2[1].innerHTML//townname
        TC.push(c)
        TN.push(d)
      }
      console.log(TN);
      this.TN = TN;
    }
  },
  async asyncData( {$axios} ) {
    let CT = await $axios.get('https://api.nlsc.gov.tw/other/ListCounty')
    let parser = new DOMParser();
    let countiesListDOM = parser.parseFromString(CT.data, 'text/xml');
    let countyItem = countiesListDOM.getElementsByTagName('countyItem');
    let a,b;
    let CTC = [];
    let CTN = [];
    for (let county of countyItem) {
      let item = county.children;//item 會是HTMLCollection［countycode, countyname, countycode01］
      a = item[0].innerHTML;//countycode
      b = item[1].innerHTML;//countyname
      CTC.push(a);
      CTN.push(b);
    }
    let obj = {};
    CTC.forEach((element, index) => {
      obj[element] = CTN[index];
    });
    console.log(obj);
    return {
      CTN,CTC,obj
    }
  }
}
</script>
<style>
.box1 {
  display: flex;
  justify-content: center;
}
.box2 {
  display: flex;
  justify-content: center;
}
#sel {
  width: 80px;
  margin-left: 10px;
}
</style>
