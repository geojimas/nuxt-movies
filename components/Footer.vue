<template>
  <footer>
    <div>
      <p>Copyright © 2021 by Dimitris Georgiadis</p>
    </div>
    <div>
      <a href="https://github.com/Jimgeo98" target="_BLANK">Github</a>
    </div>
  </footer>
</template>

<script lang="ts">
import { defineComponent } from '@nuxtjs/composition-api'
export default defineComponent({
  name: 'Footer',
})
</script>

<style lang="scss" scoped>
footer {
  bottom: 0;
  left: 0;
  right: 0;
  position: relative;
  display: flex;
  justify-content: space-around;
  align-items: center;
  background-color: #27ae60;
  height: 80px;
  color: #fff;
  padding: 20px;
  margin-top: 10px;
  letter-spacing: 2px;
  font-size: 18px;
  font-family: inherit;
  font-weight: bold;
  a {
    text-decoration: none;
    color: #fff;
    &:hover{
        text-decoration: underline;
        color: red;
    }
  }
}
</style>
