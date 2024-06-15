<template>
  <div class="admin-panel">
    <h1>Генератор кода</h1>
    <div class="code-generator">
      <button @click="generateCode" class="generate-button">Сгенерировать код</button>
    </div>

    <div v-if="generatedCode" class="generated-code">
      <h2>Сгенерированный код:</h2>
      <textarea readonly v-model="generatedCode" class="generated-code-textarea"></textarea>
      <button @click="copyToClipboard" class="copy-button">Скопировать код</button>
    </div>

    <div v-if="generatedToken" class="generated-token">
      <h2>Сгенерированный токен:</h2>
      <textarea readonly v-model="generatedToken" class="generated-token-textarea"></textarea>
      <button @click="copyTokenToClipboard" class="copy-button">Скопировать токен</button>
    </div>

    <button @click="logout" class="logout-button">Выход</button>
  </div>
</template>

<script>
import axios from 'axios'; // Импорт Axios

export default {
  data() {
    return {
      prefix: '', // Префикс кода
      codeLength: 6, // Длина кода по умолчанию
      generatedCode: '', // Сгенерированный код
      generatedToken: '', // Сгенерированный токен
      accessToken: '',
    };
  },
  methods: {
    async generateCode() {
      try {
        this.accessToken = window.localStorage.getItem('token');
        const response = await axios.get('https://backend.neteniti.ru/api/generate-token', {
          headers: { 'Authorization': `Bearer ${this.accessToken}` }
        });

        const { token } = response.data;
        this.generatedToken = token; // Store generated token

        console.log('Access token:', this.accessToken);
        console.log('Generated token:', this.generatedToken);

        // Further actions after receiving the token
      } catch (error) {
        console.error('Ошибка при генерации кода:', error);
      }
    },
    copyToClipboard() {
      navigator.clipboard.writeText(this.generatedCode).then(() => {
        alert('Код скопирован в буфер обмена');
      }).catch(err => {
        console.error('Ошибка копирования кода:', err);
      });
    },
    copyTokenToClipboard() {
      navigator.clipboard.writeText(this.generatedToken).then(() => {
        alert('Токен скопирован в буфер обмена');
      }).catch(err => {
        console.error('Ошибка копирования токена:', err);
      });
    },
    logout() {
      window.localStorage.removeItem('token');
      // Здесь можно добавить действия по выходу, например, редирект на страницу логина
    }
  }
}
</script>
  
    <style>
    .admin-panel {
      max-width: 400px;
      margin: 50px auto;
      padding: 20px;
      background-color: #f7f7f7; /* Цвет фона */
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Тень */
      text-align: center;
    }
    
    .admin-panel h1 {
      margin-bottom: 30px;
      color: #333; /* Цвет текста */
    }
    
    .code-generator {
      margin-bottom: 20px;
    }
  
    .copy-button {
    padding: 12px 24px;
    background-color: #007bff; /* Цвет фона кнопки */
    color: #fff; /* Цвет текста */
    border: none;
    border-radius: 6px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 10px; /* Добавляем немного отступа сверху */
  }
  
  .copy-button:hover {
    background-color: #0056b3; /* Измененный цвет фона при наведении */
  }
    
    .code-generator label {
      display: block;
      margin-bottom: 10px;
      font-weight: bold;
      color: #555; /* Цвет текста */
    }
    
    .input-field {
      width: 100%;
      padding: 12px;
      margin-bottom: 20px;
      border: 1px solid #ddd; /* Цвет границы */
      border-radius: 6px;
      font-size: 16px;
    }
    
    .generate-button {
      padding: 12px 24px;
      background-color: #007bff; /* Цвет фона кнопки */
      color: #fff; /* Цвет текста */
      border: none;
      border-radius: 6px;
      font-size: 16px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }
    
    .generate-button:hover {
      background-color: #0056b3; /* Измененный цвет фона при наведении */
    }
    
    .generated-code h2 {
      margin-bottom: 15px;
      color: #555; /* Цвет текста */
    }
    
    .generated-code-textarea {
      width: 100%;
      height: 100px;
      padding: 12px;
      margin-bottom: 20px;
      border: 1px solid #ddd; /* Цвет границы */
      border-radius: 6px;
      font-size: 16px;
      resize: none;
    }
    
    .logout-button {
      padding: 12px 24px;
      background-color: #ff4d4d; /* Цвет фона кнопки */
      color: #fff; /* Цвет текста */
      border: none;
      border-radius: 6px;
      font-size: 16px;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }
    
    .logout-button:hover {
      background-color: #e60000; /* Измененный цвет фона при наведении */
    }
    </style>
    