<template>
  <main class="form-box">
    <header>
      <div class="progress" v-show="state !== 3">
        <div class="progress-visual">
          <div
            class="progress-bar"
            :style="{ width: state === 1 ? '0' : '50%' }"
          ></div>
        </div>
        <div class="progress-desc">
          <p>{{ state === 1 ? "0%" : "50%" }}</p>
        </div>
      </div>
      <h1>
        {{
          state === 1
            ? "나이가 어떻게 되시나요?"
            : state === 2
            ? "성별이 어떻게 되시나요?"
            : "당신의 추천 영화는!"
        }}
      </h1>
    </header>
    <section>
      <div v-if="state !== 3" class="request">
        <div class="request-form">
          <label v-if="state === 1">
            <input
              ref="ageInput"
              type="number"
              placeholder="ex. 21"
              v-model="formData.age"
              @keydown.enter="nextState"
            />
          </label>
          <ul v-if="state === 2" class="gender-radio-form">
            <li>
              <input
                type="radio"
                id="genderM"
                value="M"
                v-model="formData.gender"
                @keydown.enter="nextState"
              />
              <label for="genderM">남자</label>
            </li>
            <li>
              <input
                type="radio"
                id="genderF"
                value="W"
                v-model="formData.gender"
                @keydown.enter="nextState"
              />
              <label for="genderF">여자</label>
            </li>
          </ul>
        </div>
        <div class="step-wrapper">
          <div class="button-wrapper">
            <button
              v-show="state > 1"
              class="step-btn before-btn"
              @click="prevState"
            >
              이전
            </button>
            <button class="step-btn after-btn" @click="nextState">
              {{ state === 2 ? "완료" : "다음" }}
            </button>
          </div>
        </div>
      </div>
      <div v-else class="response">
        <ul class="movie-list">
          <li v-for="( v, i ) in moveList" :key="i">{{ v }}</li>
        </ul>
        <div class="reset-btn-wrapper">
          <button class="reset-btn" @click="resetState">
            처음부터 다시 하기
          </button>
        </div>
      </div>
    </section>
  </main>
</template>

<script>
export default {
  name: "FormBox",
  props: {
    state: {
      type: Number,
      default: 1
    },
    moveList: {
      type: Array,
      default: undefined
    }
  },
  data() {
    return {
      formData: {
        age: "",
        gender: ""
      }
    };
  },
  methods: {
    prevState() {
      this.$emit("prev");
    },
    nextState() {
      if (this.state === 1 && String(this.formData.age) === "") {
        alert("나이를 입력해 주세요.");
        this.$refs.ageInput.focus();
      } else if (this.state === 2 && this.formData.gender === "") {
        alert("성별을 선택해 주세요.");
      } else {
        this.$emit("next", this.formData);
        this.formData.age = "";
        this.formData.gender = "";
      }
    },
    resetState() {
      this.$emit("reset");
    }
  }
};
</script>

<style lang="scss">
.form-box {
  width: 100%;

  max-width: 35rem;
  position: absolute;
  left: 50%;
  top: 200px;
  padding: 40px;
  border: 1px solid #e1e1e1;
  border-radius: 5px;
  background-color: #fff;
  transform: translateX(-50%);

  header {
    .progress {
      .progress-visual {
        width: 100%;
        height: 0.25rem;
        display: flex;
        overflow: hidden;
        margin: 1.25rem 0 0;
        border-radius: 0.125rem;
        background-color: #f2f2f2;
        box-shadow: none;
        box-sizing: border-box;

        .progress-bar {
          flex-direction: column;
          justify-content: center;
          color: #fff;
          white-space: nowrap;
          text-align: center;
          box-shadow: none;
          border-radius: 0.3125rem;
          background-color: #f7a400;

          transition: width 0.6s ease;
        }
      }

      .progress-desc {
        > p {
          padding-top: 0.5rem;
          text-align: center;
          color: #f7a400;
          font-size: 0.75rem;
          margin: 0;
          line-height: 1.5;
        }
      }
    }

    > h1 {
      padding: 2.5rem 0 1rem;
      color: #323232;
      font-size: 1.5rem;
      font-weight: 500;
      text-align: center;
    }
  }

  .request {
    .request-form {
      padding: 2.5rem 0;
    }

    .step-wrapper {
      width: 100%;
      text-align: right;
      display: flex;
      justify-content: flex-end;
      flex-direction: row;

      .button-wrapper {
        display: flex;

        .step-btn {
          flex: auto;
          width: 7.5rem;
          height: 3rem;
          font-size: 1rem;
          font-weight: 500;
          text-align: center;
          border-radius: 0.25rem;
          outline: none;
          cursor: pointer;

          &.before-btn {
            color: #f7a400;
            border: 1px solid #e1e1e1;
            background-color: #fff;
            margin-right: 0.75rem;
          }

          &.after-btn {
            background-color: #f7a400;
            color: #fff;
            border: none;
          }
        }
      }
    }

    .gender-radio-form {
      margin: 0;
      padding: 0;
      display: flex;

      li {
        margin: 0;
        padding: 0;
        list-style: none;
        flex-grow: 1;
        display: flex;

        input[type="radio"]:not(old) {
          margin: 0;
          padding: 0;
          opacity: 0;
          background: url(../assets/radio-on.png) no-repeat 0 0;
          width: 0;
        }

        input[type="radio"]:not(old) + label {
          height: 20px;
          display: inline-block;
          padding-left: 28px;
          color: #323232;
          font-size: 1rem;
          text-align: left;
          line-height: 130%;
          vertical-align: top;
          background: url(../assets/radio-off.png) no-repeat 0 0;
          cursor: pointer;
        }

        input[type="radio"]:not(old):checked + label {
          background: url(../assets/radio-on.png) no-repeat 0 0;
        }
      }
    }
  }

  input {
    display: block;
    width: 100%;
    padding: 0.75rem;
    color: #323232;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    box-sizing: border-box;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid #e1e1e1;
    border-radius: 4px;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;

    &:focus {
      color: #323232;
      outline: none;
      border-color: #f7a400;
      background-color: #fff;
      box-shadow: 0 0 0 0.2rem rgba(255, 192, 68, 0.25);
    }

    &::-webkit-outer-spin-button,
    &::-webkit-inner-spin-button {
      -webkit-appearance: none;
      margin: 0;
    }

    &::-webkit-input-placeholder {
      color: #999999;
    }

    &::-moz-placeholder {
      color: #999999;
    }

    &:-ms-input-placeholder {
      color: #999999;
    }

    &:-moz-placeholder {
      color: #999999;
    }

    &::placeholder {
      color: #999999;
    }
  }

  .response {
    .movie-list {
      width: 100%;
      margin: 0 auto;
      padding: 0;
      box-sizing: border-box;

      li {
        display: flex;
        border: 0.0625rem solid #f2f2f2;
        flex-direction: column;
        box-sizing: border-box;
        width: 100%;
        height: 68px;
        color: #323232;
        font-size: 1rem;
        line-height: 66px;
        list-style: none;
        margin: 0;
        padding: 0 0 0 40px;
        position: relative;

        &:before {
          content: " ";
          width: 8px;
          height: 8px;
          top: 30px;
          left: 16px;
          position: absolute;
          border-radius: 100%;
          background-color: #f7a400;
        }

        &:nth-child(n + 2) {
          border-top: none;
        }
      }
    }

    .reset-btn-wrapper {
      padding-top: 2.5rem;
      text-align: center;

      .reset-btn {
        flex: auto;
        height: 3rem;
        padding-left: 1rem;
        padding-right: 1rem;
        font-size: 1rem;
        font-weight: 500;
        text-align: center;
        border-radius: 0.25rem;
        outline: none;
        cursor: pointer;
        background-color: #f7a400;
        color: #fff;
        border: none;
      }
    }
  }
}

@media (max-width: 700px) {
  .form-box {
    max-width: calc(100% - 32px);
    box-sizing: border-box;
  }
}
</style>
