<template>
<select class="areaPicker" @change="loadPostOffices" v-model="pickedAreaRef">
  <option v-for="area in areas" :key="area.AreasCenter" :value="area.AreasCenter">
    {{ area.Description }}
  </option>
</select>

<br><br>

<div v-if="postOfices.length > 0">
  <select class="postOffice" v-model="pickedPost">
      <option v-for="post in postOfices" :key="post.Ref" :value="post.Ref">
    {{ post.Description }}
    </option>
  </select>
</div>

<br>
<div v-if="errorMsg">
  {{errorMsg}}
</div>

</template>

<script>
import axios from 'axios'

export default {
  el: 'App',
  data: function() {
    return {
      test: 'al',
      areas : [],
      postOfices: [],
      pickedAreaRef: '',
      pickedPost: '',
      errorMsg: ''
    }
  },
  methods: {
    loadPostOffices: function() {
      // let selectedArea = this.areas.find(el => el.AreasCenter == this.selectedAreaName);
      axios.post("https://api.novaposhta.ua/v2.0/json/", 
        {
          apiKey: "0555c556f433266e962e4ded47a8b3e9",
          modelName: "AddressGeneral",
          calledMethod: "getWarehouses",
          methodProperties: {
            CityRef: this.pickedAreaRef
        }
      }).then((response) => {
            try {
              this.errorMsg = '';
              this.postOfices = response.data.data;
              this.pickedPost = this.postOfices[0].Ref;
            } catch {
              this.postOfices = [];
              this.errorMsg = 'В цьому місті немає відділень';
            }
      });
    }
  },
  mounted: function() {
    axios.post("https://api.novaposhta.ua/v2.0/json/", 
    {
      apiKey: "0555c556f433266e962e4ded47a8b3e9",
      modelName: "Address",
      calledMethod: "getAreas",
      methodProperties: {}
      }).then((response) => {
            this.areas = response.data.data;
            this.pickedAreaRef = response.data.data[0].AreasCenter;
      });
  }
}
</script>

<style>

</style>
