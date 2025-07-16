<template>
  <v-container class="container">
    <header class="header">
      <v-row class="mb-4" dense>
        <v-col cols="auto" class="mr-10">
          <v-btn color="primary" class="text-h6">손님 등록</v-btn>
        </v-col>
        <v-col cols="auto">
          <v-btn color="secondary" class="text-h6">옷 찾기</v-btn>
        </v-col>
      </v-row>
    </header>
    <div class="mid">
      <main>
        <div class="main">
          <div class="vline"></div>
          <div class="hline"></div>
        </div>
      </main>
      <aside>
        <v-form @submit.prevent="search" v-show="true">
          <v-text-field label="이름" type="text" v-model="name"></v-text-field>
          <v-btn color="secondary" @click="search">확인</v-btn>
        </v-form>
      </aside>
    </div>
    <footer></footer>
  </v-container>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      name: '',
    };
  },
  methods: {
    async search() {
      const data = await axios.post('http://localhost:8080/api/search');
      console.log(data.data);
    },
  },
};
</script>

<style scoped>
.container {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.main {
  width: 100%;
  height: 50vh;
  background-color: white;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(2, 1fr);
  border: 2px solid red;
  flex: 3;
}
.vline {
  grid-row: 1/3;
  grid-column: 2;
  background-color: white;
  width: 2px;
  height: 100%;
}
.hline {
  grid-row: 2;
  grid-column: 1/5;
  width: 100%;
  height: 2px;
  background-color: white;
}
.mid {
  /* display: flex; */
  height: auto;
}
</style>
