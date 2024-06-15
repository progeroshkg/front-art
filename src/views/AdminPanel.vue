<template>
    <div class="admin-panel">
      <h1>Вход в админ-панель</h1>
      <form @submit.prevent="submitForm" class="login-form">
        <label for="email">Email:</label> <!-- Изменили метку на "Email" -->
        <input type="text" id="email" v-model="email" class="input-field"> 
    
        <label for="password">Пароль:</label>
        <input type="password" id="password" v-model="password" class="input-field">
    
        <button type="submit" class="login-button">Войти</button>
      </form>
    
      <div v-if="loggedIn" class="welcome-message">
        <h2>Добро пожаловать, {{ username }}!</h2>
        <!-- Ваша админ-панель здесь -->
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios'; // Импорт Axios
  
  export default {
    data() {
      return {
        email: '',
        password: '',
        accessToken: '',
        loggedIn: false,
        // other data properties
      };
    },
  
    
    methods: {
      async submitForm() {
        try {
          const response = await axios.post('https://backend.neteniti.ru/api/auth/login', {
            email: this.email,
            password: this.password,
          });
          const { access_token } = response.data;
          this.accessToken = access_token; // Сохраняем access_token
          window.localStorage.setItem('token', access_token);
          console.log('Access token:', access_token);
          this.loggedIn = true;
  
          // Переход на AdminVhod после успешного входа
          this.$router.push({ name: 'AdminVhod' });
  
          // Получение данных сразу после входа
          this.fetchData();
        } catch (error) {
          console.error('Ошибка при входе:', error);
        }
      },
  
      
    }
  }
  </script>
  
  
  <style>
  body {
    font-family: 'Roboto', sans-serif;
    margin: 0;
    padding: 0;
    background-color: #77CBCE; /* Задний фон всей страницы */
  }
  
  .admin-panel {
    max-width: 400px;
    margin: 50px auto;
    padding: 20px;
    background-color: #fff;
    border-radius: 8px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
  }
  
  .admin-panel h1 {
    text-align: center;
    margin-bottom: 30px;
    color: #333;
  }
  
  .login-form {
    display: flex;
    flex-direction: column;
  }
  
  .admin-panel label {
    margin-bottom: 10px;
    color: #555;
  }
  
  .input-field {
    padding: 12px;
    margin-bottom: 20px;
    border: 1px solid #ddd;
    border-radius: 6px;
    font-size: 16px;
  }
  
  .login-button {
    padding: 12px 24px;
    background-color: #4CAF50;
    color: #fff;
    border: none;
    border-radius: 6px;
    font-size: 18px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .login-button:hover {
    background-color: #45a049;
  }
  
  .welcome-message {
    text-align: center;
    margin-top: 20px;
  }
  
  .error-message {
    color: #f44336;
    font-size: 14px;
    margin-top: 5px;
  }
  </style>
  