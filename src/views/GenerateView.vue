<template>
  <div>
    <header class="Menu">
      <ul>
        <li><a href="#generator">Генератор String Art</a></li>
        <li><router-link :to="{ name: 'UserProfile' }">Помощник String Art</router-link></li>
        <li><a href="#contacts">Контакты</a></li>
      </ul>
    </header>

    <div class="generate">

      <!-- ШАГ 1 -->
      <div class="step step-0">
        <div class="step-title">ШАГ 1</div>
        <div class="step-label">Выберите форму картины</div>
        <div class="step-types">
          <div @click="imageType = 1" class="step-types-square">
            <svg v-if="imageType === 1" width="68" height="50" viewBox="0 0 68 50" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M67.0042 1.02678C65.6765 -0.301081 63.5238 -0.301081 62.1958 1.02678L21.4617 41.7613L5.80418 26.1038C4.47645 24.7759 2.32383 24.776 0.995833 26.1038C-0.332026 27.4315 -0.332026 29.5841 0.995833 30.912L19.0575 48.9734C20.3849 50.3011 22.5391 50.3002 23.8659 48.9734L67.0042 5.83512C68.332 4.50739 68.3319 2.35464 67.0042 1.02678Z" fill="#25E477"/>
            </svg>
          </div>
          <div @click="imageType = 2" class="step-types-circle">
            <svg v-if="imageType === 2" width="68" height="50" viewBox="0 0 68 50" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path d="M67.0042 1.02678C65.6765 -0.301081 63.5238 -0.301081 62.1958 1.02678L21.4617 41.7613L5.80418 26.1038C4.47645 24.7759 2.32383 24.776 0.995833 26.1038C-0.332026 27.4315 -0.332026 29.5841 0.995833 30.912L19.0575 48.9734C20.3849 50.3011 22.5391 50.3002 23.8659 48.9734L67.0042 5.83512C68.332 4.50739 68.3319 2.35464 67.0042 1.02678Z" fill="#25E477"/>
            </svg>
          </div>
        </div>
        <div class="step-file">
          <label for="file" class="step-file-choose">
            <input ref="file" @change="uploadFile" type="file" id="file">
             {{ file ? 'Файл выбран' : 'Выберите файл' }}
          </label>
          <button @click="uploadImageToCanvas" class="step-file-upload">Загрузить</button>
        </div>
        <div class="step-image">
          <div class="step-image-container" :class="{'step-image-container-square': imageType === 1}">
            <img :src="image" :style="{
              transform: `scale(${scale / 10}) rotate(${turn}deg)`,
              }" alt="">
          </div>
        </div>
        <div class="step-scale">
          <span class="step-scale-title">Масштаб</span>
          <range-slider class="slider" min="1" max="100" step="0.2" v-model="scale"></range-slider>
        </div>
        <div class="step-turn">
          <span class="step-turn-title">Поворот</span>
          <range-slider class="slider" min="0" max="360" step="1" v-model="turn"></range-slider>
        </div>
        <div class="step-last">
          <button @click="step = 1">К шагу 2</button>
        </div>
      </div>

      <!-- ШАГ 2 -->
      <div class="step step-1" v-if="step === 1">
        <div class="step-title">ШАГ 2</div>
        <div class="step-image">
          <div class="step-image-container" id="firstImageResult" :class="{'step-image-container-square': imageType === 1}">
            <img :src="image" :style="{
              transform: `scale(${scale / 10}) rotate(${turn}deg)`,
              filter: `grayscale(1) brightness(${brightness}%) contrast(${contrast}%)`
              }" alt="">
          </div>
        </div>
        <div class="step-scale">
          <span class="step-scale-title">Яркость: {{brightness}}%</span>
          <range-slider class="slider" min="10" max="180" step="1" v-model="brightness"></range-slider>
        </div>
        <div class="step-turn">
          <span class="step-turn-title">Контраст: {{contrast}}%</span>
          <range-slider class="slider" min="10" max="180" step="1" v-model="contrast"></range-slider>
        </div>
        <div class="step-turn">
          <span class="step-turn-title">Разброс нитей: {{thread}}</span>
          <range-slider class="slider" min="1" max="20" step="0.2" v-model="thread"></range-slider>
        </div>
        <div class="step-last">
          <button @click="goToStep(2)">К шагу 3</button>
        </div>
      </div>

      <!-- ШАГ 3 -->
      <div class="step step-2" v-if="step === 2">
        <div class="step-title">ШАГ 3</div>
        <div class="step-image">
          <div id="lastImageResult" class="step-image-container" :class="{'step-image-container-square': imageType === 1}">
            <img id="finishedImage" :src="image" :style="{
              transform: `scale(${scale / 10}) rotate(${turn}deg)`,
              filter: `grayscale(1) brightness(${brightness}%) contrast(${contrast}%)`
              }" alt="">
          </div>
        </div>
        <div class="step-generate">Сгенерировать изображение</div>
        <div class="step-btns">
          <div @click="saveImage" class="step-btns-save">Сохранить изображение</div>
          <div @click="goToStep(3)" class="step-btns-instructions">Инструкция</div>
        </div>
      </div>

      <!-- ИНСТРУКЦИЯ -->
      <div class="step step-3" v-if="step === 3">
        <div class="step-title">ИНСТРУКЦИЯ</div>
        <div class="step-instructions-container">
          Ваша инструкция по сборке готова! Укажите Вашу почту.
        </div>
        <input type="email" class="step-email" placeholder="ТВОЙ E-MAIL" id="email">
        <button class="step-send-email" @click="sendDataToBackend">Отправить почту</button>
        
        <p v-if="errorMessage" class="error text-danger">{{ errorMessage }}</p>
        <p v-if="successMessage" class="success">{{ successMessage }}</p>

        <div class="step-help-container">
          При возникновении проблем просьба связаться через контактную форму. просьба связаться через почту <router-link to="/">neteniti@yandex.ru</router-link><br>
          Для упрощения изготовления картины вы можете воспользоваться нашим 
          <router-link to="/">помощником стринг арта</router-link>, в который загружается файл с инструкцией который вам придет на почту, которую вы указали.
          <div class="social-media-container">
            <a href="https://vk.com/nitiniti_art" target="_blank" class="social-media-icon vk-icon"></a>
            <a href="https://www.instagram.com/nete.niti?igsh=N2Fub2k1ZmphbHRs" target="_blank" class="social-media-icon instagram-icon"></a>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import RangeSlider from 'vue-range-slider'
// you probably need to import built-in style
import { toBlob } from 'html-to-image';
import validator from 'validator';

  // function createBase64Image(fileObject) {
  //     const reader = new FileReader();

  //     reader.onload = (e) => {
  //       // return e.target.result
  //       // this.finishedImage = e.target.result;
  //       console.log(e.target.result,8888)
  //       // this.uploadImage();
  //     };
  //     reader.readAsDataURL(fileObject);
  //   }


export default {
  name: 'GenerateView',
  data: () => ({
    step: 0,
    sliderValue: 50,
    file: null,
    image: null,
    imageType: 1, // 1 = square 2 = circle
    ctx: null,
    canvasWidth: 300,
    canvasHeight: 300,
    scale: 10,
    turn: 0,
    brightness: 50,
    contrast: 50,
    thread: 4.5,
    email: '', // Добавляем переменную для хранения email
    test: '',
    finishedImage: '',
    errorMessage: '',
    successMessage: '',
    finUrl: ''

  }),
  methods: {
    validateEmail(email) {
      console.log(email)
      if (validator.isEmail(email)) {
        this.successMessage = '';
        this.errorMessage = '';
      } else {
        this.errorMessage = 'Электронная почта недействительна. Пожалуйста, введите действительный адрес электронной почты..';
        this.successMessage = '';
      }
    },
    
    goToStep(stepNumber) {
      this.step = stepNumber;

      if (stepNumber === 2) {
        const node1 = document.getElementById('firstImageResult');
     console.log(node1, 555)
        node1.style.border = 'none'
        toBlob(node1)
          .then((dataUrl1) => { // Use an arrow function here
          
          node1.style.border = '1px solid #25E477'
          this.createBase64Image(dataUrl1)
        })
        .catch((error) => { // Use an arrow function here
          node1.style.border = '1px solid #25E477';
          console.error('Oops, something went wrong!', error);
        });
      }
    },
       
    createBase64Image(fileObject) {
      const reader = new FileReader();

      reader.onload = (e) => {
        this.finishedImage = e.target.result;       
      };
      reader.readAsDataURL(fileObject);
    },

    saveImage() {
      const node = document.getElementById('lastImageResult');
      node.style.border = 'none'
      toBlob(node)
        .then(function (dataUrl) {
            const blobUrl = URL.createObjectURL(dataUrl);
            const link = document.createElement("a"); // Or maybe get it from the current document
            link.setAttribute('id', 'lastImageResultDownload')
                      
            link.href = blobUrl;
            link.download = "image.png";
            document.body.appendChild(link); // Or append it wherever you want
            document.getElementById('lastImageResultDownload').click() 
            node.style.border = '1px solid #25E477'
        })
        .catch(function (error) {
          node.style.border = '1px solid #25E477'
          console.error('oops, something went wrong!', error);
        });
    },

    uploadFile() {
        const filesSelected = this.$refs.file.files
        const t = this
        if (filesSelected.length > 0) {
          let fileToLoad = filesSelected[0];
          let fileReader = new FileReader();
          fileReader.onload = function(fileLoadedEvent) {
            let srcData = fileLoadedEvent.target.result; // <--- data: base64
            t.file = srcData
          }
          fileReader.readAsDataURL(fileToLoad);
        }
    },
    
    uploadImageToCanvas() {
      this.image = this.file
    },
    
    // Функция для отправки данных на сервер
    async sendDataToBackend() {
        try {
     
          this.email = document.getElementById('email').value;           
          this.validateEmail(this.email)
          this.errorMessage = '';
          this.successMessage = '';

          // console.log(this.finishedImage, 888)
          
            const data = {
                email: this.email,
                image: this.image,
                generated_image: this.finishedImage
            };

            // Отправляем данные на сервер
            const response = await fetch('https://backend.neteniti.ru/api/send-form', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            });

            // Получаем ответ от сервера
            const responseData = await response.json();
            if(responseData.success){
                this.successMessage = 'Почта успешна отправлено';
            }
            else{
              this.errorMessage = 'Поле электронной почты обязательно для заполнения';

            }
             
        } catch (error) {

         this.errorMessage = 'Произошло ошибка';
            console.error('Ошибка:', error);
        
        }
    },

   
  },
  
  watch: {},
  mounted() {},
  components: {
    RangeSlider
  }
}
</script>



<style lang="scss" scoped>
$green: #25E477;
$dark-green: #154645;
$white: #E3ECEC;

@import 'vue-range-slider/dist/vue-range-slider.css';

.generate {
  width: 35%;
  min-height: calc(100vh - 45px); /* Учитываем отступ сверху */
  background: $dark-green;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  border-radius: 20px;
  margin: auto;
  margin-top: 45px;
}

/* Стили для мобильных устройств */
@media (max-device-width: 768px) {
  .generate {
    width: 90%; /* Изменяем ширину на 90% для мобильных устройств */
    margin-top: 10px; /* Сбрасываем отступ сверху */
    border-radius: 0; /* Сбрасываем скругление углов */
    min-height: 100vh; /* Изменяем высоту на 100vh для мобильных устройств */
  }
}

.step {
  padding: 20px;
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 20px;

  &-instructions-container {
    padding: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    border: 1px solid $green;
    color: $white;
    font-size: 20px;
    border-radius: 10px;
  }

  &-help-container {
    padding: 20px;
    border: 1px solid $green;
    color: $white;
    font-size: 20px;
    border-radius: 10px;

    a {
      color: $green;
      text-decoration: none;
    }
  }

  &-email {
    margin-top: 20px;
    background: #fff;
    border-radius: 15px;
    width: 100%;
    height: 40px;
    text-align: center;
    padding: 10px;
    font-style: normal; 
    color: $dark-green;
    font-family: 'Arial Regular';
    outline: none;
    border: none;
  }

  &-send-email {
    margin-bottom: 20px;
    border-radius: 10px;
    background: $green;
    display: flex;
    align-items: center;
    justify-content: center;
    color: $dark-green;
    font-style: normal;
    text-align: center;
    height: 40px;
    outline: none;
    border: none;
    font-family: 'Arial Regular';
  }

  .social-media-container {
    text-align: center;
    margin-top: 20px;
  }

  .social-media-icon {
    display: inline-block;
    width: 30px;
    height: 30px;
    margin-right: 10px;
    background-size: contain;
    background-repeat: no-repeat;
  }

  .vk-icon {
    background-image: url('https://img.icons8.com/?size=100&id=13977&format=png&color=000000');
  }

  .instagram-icon {
    background-image: url('https://img.icons8.com/?size=100&id=Xy10Jcu1L2Su&format=png&color=000000');
  }

  &-title {
    font-size: 32px;
    font-family: 'Arial Regular';
    color: $green;
  }

  &-label {
    height: 40px;
    text-align: center;
    border-radius: 30px;
    border: 1px solid $green;
    color: $white;
    font-size: 22px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &-types {
    display: flex;
    align-items: center;
    justify-content: space-around;

    & > div {
      display: flex;
      align-items: center;
      justify-content: center;
    }

    &-square {
      width: 100px;
      height: 100px;
      border: 1px solid $green;
    }

    &-circle {
      width: 100px;
      height: 100px;
      border-radius: 100%;
      border: 1px solid $green;
    }
  }

  &-file {
    display: flex;
    flex-direction: column;
    gap: 10px;

    &-choose {
      width: 187px;
      height: 40px;
      background: $green;
      color: $dark-green;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;

      input {
        display: none;
      }
    }

    &-upload {
      width: 187px;
      height: 40px;
      border-radius: 10px;
      border: 1px solid $green;
      background: $dark-green;
      color: $green;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  }

  &-image {
    display: flex;
    align-items: center;
    justify-content: center;

    &-container {
      width: 350px;
      height: 350px;
      overflow: hidden;
      border: 1px solid $green;
      border-radius: 100%;
      background: $white;
    }

    img {
      width: 350px;
      height: 350px;
    }
  }

  &-image {
    &-container-square {
      border-radius: 0%;
    }
  }

  &-scale {
    gap: 20px;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    gap: 20px;

    &-title {
      padding: 10px 15px;
      border-radius: 30px;
      color: $white;
      border: 1px solid $green;
    }
  }

  &-turn {
    gap: 20px;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    gap: 20px;

    &-title {
      padding: 10px 15px;
      border-radius: 30px;
      color: $white;
      border: 1px solid $green;
    }
  }

  &-last {
    display: flex;
    align-items: center;
    justify-content: flex-end;

    button {
      width: 139px;
      height: 40px;
      background: $green;
      color: $dark-green;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      outline: none;
      border: none;
    }
  }

  &-generate {
    border-radius: 10px;
    color: $white;
    border: 1px solid $green;
    font-size: 20px;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 40px;
  }

  &-btns {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 20px;

    &-instructions {
      height: 40px;
      border-radius: 10px;
      border: 1px solid $green;
      background: $dark-green;
      color: $green;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-grow: 6;
    }

    &-save {
      height: 40px;
      background: $green;
      color: $dark-green;
      border-radius: 10px;
      display: flex;
      align-items: center;
      justify-content: center;
      outline: none;
      border: none;
      flex-grow: 4;
    }
  }
}

.Menu {
  text-align: center; /* Центрируем содержимое меню */
  background-color: $dark-green; /* Темно-зеленый фон */
  padding: 10px 0; /* Добавляем немного отступа сверху и снизу для улучшенного визуального восприятия */
}

.Menu li {
  display: inline-block; /* Размещаем пункты меню в одной строке */
  margin-right: 20px; /* Отступ между пунктами меню */
}

.Menu li:last-child {
  margin-right: 0; /* Убираем отступ справа у последнего пункта меню */
}

.Menu a {
  color: white; /* Белый цвет текста для ссылок */
  text-decoration: none; /* Убираем подчеркивание для ссылок */
  font-weight: bold; /* Жирный шрифт для ссылок */
  padding: 5px 10px; /* Добавляем отступы для улучшенного визуального восприятия */
  border-radius: 5px; /* Закругляем углы */
}

.Menu a:hover {
  background-color: #238b5d; /* Изменяем фон при наведении на ссылку */
}

.container1 {
  margin-top: 40px;
  border-bottom: 15px;
  justify-content: center;
  text-align: center;
  background-color: #033837;
  padding: 20px;
  border-radius: 15px 15px 25px 25px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  max-width: 400px;
  width: 50%;
  margin-left: auto;
  margin-right: auto;
}

.instruction-box1 {
  text-align: center;
  color: white;
}

.instruction-box1 h1 {
  margin-bottom: 20px;
  font-size: 24px;
}

.email-section {
  margin-bottom: 20px;
}

.email-section p {
  margin-bottom: 10px;
}

.email-section input {
  width: calc(100% - 20px);
  padding: 10px;
  border: none;
  border-radius: 5px;
  margin-bottom: 10px;
}

.email-section button {
  width: 100%;
  padding: 10px;
  background-color: #3b9e3b;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

.email-section button:hover {
  background-color: #2e8c2e;
}

.support-section p {
  margin: 10px 0;
}

.support-section a {
  color: #25e477;
  text-decoration: none;
}

.support-section .social-icons {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.support-section .social-icons a {
  margin: 0 5px;
}

.support-section .social-icons img {
  width: 24px;
  height: 24px;
}

.error{
  color: red;
  font-size: 12px;
}

.success{
  color: #25E477;
} 
</style>