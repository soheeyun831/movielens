<template>
  <div>
    <header class="m-header">
      <h3>영화 추천 시스템</h3>
      <p>빅데이터 과정 프로젝트 : 4팀 김기식 윤소희</p>
    </header>
    <FormBox
      :state="state"
      :move-list="moveList"
      @prev="prevState"
      @next="nextState"
      @reset="resetState"
    />
  </div>
</template>
<script>
import FormBox from "./components/FormBox.vue";
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      state: 1,
      formData: {
        age: "",
        gender: ""
      },
      moveList: []
    };
  },
  mounted() {
    this.getMovieList();
  },
  components: {
    FormBox
  },
  methods: {
    prevState() {
      this.state--;
    },
    nextState(data) {
      if (this.state === 1) {
        this.formData.age = data.age;
      } else if (this.state === 2) {
        this.formData.gender = data.gender;
        this.getMovieList();
      }
      this.state++;
    },
    getMovieList() {
      if (Number(this.formData.age) < 10) {
        this.formData.age = "0~9";
      } else if (Number(this.formData.age) > 59) {
        this.formData.age = "60~";
      } else {
        const start = this.formData.age.substr(0, 1);
        this.formData.age = `${start}0~${start}9`;
      }

      axios
        .get(
          `http://localhost:3001/movies?age=${this.formData.age}&gender=${this.formData.gender}`
        )
        .then(response => {
          this.moveList = response.data[0].list;
        })
        .catch(e => {
          console.log("error : ", e); // 에러가 나는 경우 콘솔에 에러를 출력한다
        });
    },
    resetState() {
      this.state = 1;
      this.moveList = [];
    }
  }
};
</script>

<style lang="scss">
html,
body {
  width: 100%;
  margin: 0;
  padding: 0;
  position: relative;
}

.m-header {
  width: 100%;
  height: 300px;
  margin: 0;
  padding: 0;
  color: white;
  text-align: center;
  background-image: url(assets/cover.jpg);

  > h3 {
    margin: 0;
    padding: 100px 0 0;
    font-size: 24px;
  }

  > p {
    font-size: 14px;
  }
}
</style>
