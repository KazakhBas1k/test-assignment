<template>
  <div class="container">
    <div v-if="!registed" class="form-container">
      <h1>Регистрация</h1>
      <div class="line"></div>
      <p class="label">Заполните Ваши данные</p>
      <div>
        <div class="inputs">
          <input v-model="userName" type="text" placeholder="Имя" v-bind:class="{erorr: errors.includes('userName')}">
          <input v-model="email" type="text" placeholder="Email" v-bind:class="{erorr: errors.includes('email')}">
          <section></section>
          <select v-model="role" v-bind:class="{placeholder: role === null, erorr: errors.includes('role')}">
            <option :value="null" selected disabled hidden>Должность</option>
            <option v-for="(item, index) in roles" :value="item.value" v-bind:key="index">{{ item.name }}</option>
          </select>
          <div class="password">
            <input v-model="password" placeholder="Пароль" :type="passwordType1" v-bind:class="{erorr: errors.includes('password')}">
            <img class="eye" v-bind:class="[passwordType1 === 'text' ? 'hide' : 'show']" v-on:click="passwordType1 === 'text' ? passwordType1 = 'password' : passwordType1 = 'text'">
          </div>
          <div class="password">
            <input v-model="password_repeat" placeholder="Повторите пароль" :type="passwordType2" v-bind:class="{erorr: errors.includes('password_repeat')}">
            <img class="eye" v-bind:class="[passwordType2 === 'text' ? 'hide' : 'show']" v-on:click="passwordType1 === 'text' ? passwordType2 = 'password' : passwordType2 = 'text'">
          </div>
        </div>
        <div class="line"></div>
        <div class="actions">
          <div class="vision">
            <div class="toggle" v-on:click="vision = !vision" v-bind:class="{grey: !vision}">
              <div class="circle" v-bind:class="{active: !vision}"></div>
            </div>
            <div class="description">
              <p>Хотите чтобы Ваш профиль видели другие участники платформы? </p>
              <span>Включает профиль для просмотра другими пользователями по ссылке</span>
            </div>
          </div>
          <div class="bottom">
            <div class="politic">
              <input v-model="politic" type="checkbox" class="checkbox" checked>
              <p class="politic-text">Регистрируясь, Вы соглашаетесь с <a>политикой конфиденциальности</a> и обработкой <a>персональных данных</a></p>
            </div>
            <button v-on:click="save">Зарегистрироваться</button>
          </div>
        </div>
      </div>
    </div>
    <div v-if="registed">
      <h1 style="text-align: center">Регистрация успешно завершена</h1>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';
  import MockAdapter from 'axios-mock-adapter';
  const httpClient = axios.create();
  const mock = new MockAdapter(httpClient);
  mock.onPost('/api/endpoint').reply(200, {
    success: true,
    message: 'Данные успешно отправлены',
  });
  export default {
    data() {
      return {
        registed: false,
        errors: [],
        userName: '',
        email: '',
        role: null,
        password: '',
        password_repeat: '',
        vision: true,
        politic: true,
        passwordType1: "password",
        passwordType2: "password",
        roles: [
          {
            name: "Front-End Developer",
            value: 1,
          },
          {
            name: "Back-End Developer",
            value: 2,
          },
          {
            name: "Dev-Ops Developer",
            value: 3,
          }
        ]
      }
    },
    methods: {
      validation() {
        if (this.userName === '') {
          this.errors.push('userName');
        } else {
          this.errors = this.errors.filter(item => item !== 'userName')
        }
        if (this.email === '') {
          this.errors.push('email');
        } else {
          this.errors = this.errors.filter(item => item !== 'email')
        }
        if (this.role === null) {
          console.log(this.errors)
          this.errors.push('role');
        } else {
          this.errors = this.errors.filter(item => item !== 'role')
        }
        if (this.password === '') {
          this.errors.push('password');
        } else {
          this.errors = this.errors.filter(item => item !== 'password')
        }
        if (this.password_repeat === '') {
          this.errors.push('password_repeat');
        } else if (this.password !== this.password_repeat) {
          alert('Пароль не совпадает!');
        } else {
          this.errors = this.errors.filter(item => item !== 'password_repeat')
        }
        if (!this.politic) {
          alert('Вы не можете зарегистрироваться если не согласны с политикой конфедециальности!');
          this.errors.push('politic');
        } else {
          this.errors = this.errors.filter(item => item !== 'politic');
        }
      },
      save() {
        this.validation();
        if (this.errors.length === 0) {
          const form = {
            userName: this.userName,
            email: this.email,
            role: this.role,
            password: this.password,
            password_repeat: this.password_repeat,
            vision: this.vision,
            politic: this.politic,
          }
          httpClient.post('/api/endpoint', form)
          .then(response => {
            alert(response.data.message);
            this.registed = true;
          })
          .catch(error => {
            console.error('Ошибка отправки данных:', error);
          });
        }
      }
    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');
  
  body {
    background: #f5f5f5e0;
  }

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: "Montserrat", sans-serif;
  }
  
  .container {
    margin: 0 auto;
    margin-top: 20vh;
    width: 960px;
  }
  
  .form-container {
    padding-left: 31px;
    padding-right: 15px;
    background: white;
    border-radius: 15px;
  }
  
  h1 {
    padding: 17px 0;
    font-weight: 700;
    font-size: 19px;
    line-height: 27px;
  }

  .line {
    position: absolute;
    left: calc(50% - 480px);
    background: #D9D9D9;
    width: 960px;
    height: 1px;
  }

  .inputs {
    display: flex;
    justify-content: space-between;
    flex-direction: row;
    flex-wrap: wrap;
    row-gap: 31px;
    padding-bottom: 30px;
  }

  select {
    appearance: none;
    -webkit-appearance: none;
    -moz-appearance: none;
    background-image: url('./assets/arrow.svg');
    background-repeat: no-repeat;
    background-position: center right 10px;
  }

  .label {
    margin-top: 18px;
    margin-bottom: 34px;
  }

  p {
    font-weight: 500;
    font-size: 16px;
    line-height: 19px;
  }

  input, select {
    border: 1px solid #E6E6EB;
    width: 450px;
    height: 39px;
    border-radius: 11px;
    font-weight: 400;
    font-size: 14px;
    line-height: 19px;
    padding: 10px;
  }

  input:focus, select:focus {
    outline: none;

  }
  
  section {
    width: 450px;
    height: 39px;
  }

  .actions {
    margin-top: 23px;
  }

  span {
    font-weight: 400;
    font-size: 14px;
    line-height: 19px;
    color: #696977;
  }

  .vision {
    display: flex;
    gap: 5px;
  }

  .toggle {
    position: relative;
    background: #3586FF;
    border-radius: 100px;
    width: 39px;
    height: 19px;
  }

  .circle {
    position: absolute;
    right: 0;
    border: 1px solid #dddee2;
    background: white;
    border-radius: 50%;
    width: 19px;
    height: 19px;
    transform: translateX(0);
    transition: transform .1s linear;
  }

  .active {
    transform: translateX(-20px);
  }

  .grey {
    background: #9ea0a6;
    transition: transform .1s linear;
  }

  .checkbox {
    width: 19px;
    height: 17px;
    border-radius: 5px;
  }

  .description {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  a {
    color: #3586FF;
  }

  .bottom {
    padding-top: 30px;
    display: flex;
    justify-content: space-between;
    padding-bottom: 65px;
  }

  .politic {
    max-width: 541px;
    display: flex;
    gap: 5px;
  }

  .politic-text {
    font-weight: 400;
    font-size: 14px;
    line-height: 19px;
  }

  button {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 302px;
    height: 40px;
    background: rgba(73, 122, 218, 0.2);
    color: #497ADA;
    border: none;
    border-radius: 8px;
    cursor: pointer;
  }

  .placeholder {
    color: rgb(117, 117, 117);
  }

  option {
    color: black;
  }

  .password {
    position: relative;
  }

  .password input {
    padding-right: 40px;
  }

  .eye {
    position: absolute;
    right: 10px;
    top: 10px;
    cursor: pointer;
    width: 21px;
    height: 21px;
  }

  .hide {
    content: url('./assets/hided.svg');
  }

  .show {
    content: url('./assets/showed.svg');
  }

  .erorr {
    border: 1px solid red;
  }
</style>