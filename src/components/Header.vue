<template>
    <header class="header">
      <nav class="nav">
        <ul class="menu">
          <li><router-link to="/">Главная</router-link></li>
          <li><router-link to="/generate">Создать</router-link></li>
          <li><router-link to="/contact">Контакты</router-link></li>
        </ul>
      </nav>
    </header>
  </template>
  
  <script>
  export default {
    name: 'Header'
  }
  </script>
  
  <style scoped>
  .header {
    width: 100%;
    background-color: #154645; /* Темно-зеленый цвет */
    padding: 10px 0;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .nav {
    max-width: 1200px;
    margin: 0 auto;
    display: flex;
    justify-content: center;
  }
  
  .menu {
    list-style: none;
    display: flex;
    gap: 20px;
    margin: 0;
    padding: 0;
  }
  
  .menu li {
    display: inline;
  }
  
  .menu a {
    color: white;
    text-decoration: none;
    font-weight: bold;
    padding: 10px 20px;
    border-radius: 5px;
  }
  
  .menu a:hover {
    background-color: #238b5d; /* Более светлый зеленый цвет при наведении */
  }
  </style>
  