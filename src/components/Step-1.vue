<template>
  <div>
    <h1>Step-1</h1>
    <button @click="startBtn">게임시작</button>
    <button @click="nextBtn" :disabled="gameResult">진행</button>
    <div v-show="startView">
      <div v-show="firstHitter">첫 번째 타자가 타석에 입장 했습니다.</div>
   </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      gameResult: true, // 진행 버튼 활성화
      startView: false,
      firstHitter : true, // 게임 시작 메시지 
    }
  },
  methods: {
    startBtn() {
      this.startView = true
      this.gameResult = false
    },
    nextBtn() {
      this.firstHitter = false
    }
  }
}
</script>

<style>

</style>