<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style lang="scss">
@import '@/assets/arial-webfont/style.css';

*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Arial Italic';
}

$green: #25E477;
$dark-green: #154645;
$white: #E3ECEC;

.slider{
  width: 100% !important;
}
.range-slider-rail{
  background-color: $dark-green !important;
  border: 1px solid $green;

}
.range-slider-rail, .range-slider-fill{
  border-radius: 20px !important;
  height: 8px !important;
}
.range-slider-knob{
  background: $green !important;
  border: 1px solid $green !important;
}

body{
  background-color: #77CBCE  ;
}
</style>
