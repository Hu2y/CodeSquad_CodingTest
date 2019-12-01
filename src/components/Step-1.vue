<template>
  <div>
    <h1>Step-1</h1>
    <button @click="startBtn">게임시작</button>
    <button @click="nextBtn" :disabled="gameResult">진행</button>
    <div v-show="startView">
      <div v-show="firstHitter">첫 번째 타자가 타석에 입장 했습니다.</div>
      <p>{{ stat }}</p>
      <p>{{strike}}S {{ball}}B {{out}}O</p>
    </div>
    <div v-show="endGame">
      <p>최종 안타수: {{hits}}</p> 
      <span>GAME OVER</span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      gameResult: true, // 진행 버튼 활성화
      startView: false, // 게임 시작 뷰
      firstHitter: true, // 게임 시작 메시지 
      endGame: false, // 게임 종료 후 뷰
      stat: '',
      hitter: ["스트라이크!", "볼!", "안타!", "아웃!"],
      strike: 0,
      ball: 0,
      hits: 0,
      out: 0,
    }
  },
  methods: {
    startBtn() {
      this.startView = true
      this.gameResult = false
    },
    initSB() {
      this.strike = 0
      this.ball = 0
    },
    nextBtn() {
      this.firstHitter = false
      this.stat = this.hitter[Math.floor(Math.random() * this.hitter.length)]
      switch (this.stat) {
        case "스트라이크!":
          this.strikeFunc();
          break;
        case "볼!":
          this.ballFunc();
          break;
        case "안타!":
          this.hitsFunc();
          break;
        case "아웃!":
          this.outFunc();
          break;
      }
    },
    strikeFunc() {
      this.strike += 1
      if(this.strike === 3) {
        this.out+= 1
        this.stat = "3스트라이크! 아웃! 다음 타자가 타석에 입장 했습니다."
        this.initSB()
      }
    },
    ballFunc() {
      this.ball += 1
      if(this.ball === 4) {
        this.hits += 1
        this.stat = "4볼! 안타! 다음 타자가 타석에 입장 했습니다."
        this.initSB()
        if(this.out === 3) {
          this.stat = "스트라이크 아웃!";
          this.endGame = true
          this.gameResult = true
        }
      }
    },
    hitsFunc() {
      this.hits += 1
      this.stat = "안타! 다음 타자가 타석에 입장 했습니다."
      this.initSB()
    },
    outFunc() {
      this.out += 1
      this.stat = "아웃! 다음 타자가 타석에 입장 했습니다."
      this.initSB()
      if(this.out === 3) {
        this.stat = "스트라이크 아웃!";
        this.endGame = true
        this.gameResult = true
      }
    }
  }
}
</script>

<style>

</style>