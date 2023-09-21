<template>
  <div class="keyboard">
    <h1>测试测试！</h1>
    <label for="input">请输入：</label>
    <input type="text" id="input" v-model="text" />
    <SkeletonKeyboard
      style="margin-top: 5px"
      @on-push="PushText"
      @on-delete="DeleteText"
    ></SkeletonKeyboard>
  </div>
</template>

<script>
  import SkeletonKeyboard from '../components/SkeletonKeyboard.vue'

  export default {
    name: 'keyboard',
    components: {
      SkeletonKeyboard,
    },
    data() {
      return {
        text: '',
      }
    },
    methods: {
      PushText(key) {
        this.text += key
      },
      DeleteText() {
        this.text = this.text.substring(0, this.text.length - 1)
      },
    },
  }
</script>
