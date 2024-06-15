<template>
  <div class="login-container">
    <div class="admin-instructions">
      <!-- Здесь могут быть инструкции или информация для администратора -->
    </div>
    <div class="login-box">
      <h2>У Вас уже есть набор String Art?</h2>
      <input type="text" placeholder="Вставьте код из набора" v-model="code" />
      <button @click="submitCode">Ввести код</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      code: ''
    }
  },
  methods: {
    async submitCode() {
      try {
        const response = await fetch(`https://backend.neteniti.ru/api/check-token?token=${this.code}`);
        const data = await response.json();
        if (data.success) {
          // Перенаправить на страницу "about" при успешной проверке токена
          this.$router.push({ name: 'about' });
        } else {
          console.log('Token is invalid');
          console.log(this.code);
        }
      } catch (error) {
        console.error('Произошла ошибка при проверке токена:', error);
      }
    }
  }
}
</script>

<style scoped>
.login-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  border-radius: 45px;
  padding: 20px;
}

.admin-instructions {
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 5px;
}

.login-box {
  background-color: #00796b;
  padding: 20px;
  border-radius: 5px;
  text-align: center;
  color: white;
}

.login-box h2 {
  margin-bottom: 10px;
}

.login-box input {
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
  border: none;
}

.login-box button {
  padding: 10px 20px;
  background-color: #4caf50;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

.login-box button:hover {
  background-color: #45a049;
}
</style>
