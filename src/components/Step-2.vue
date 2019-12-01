<template>
  <div>
    <h1>Step-2</h1>
    <div>
      <p>신나는 야구시합</p>
      <button @click="teamBtn" :disabled="inputActive">야구팀 입력</button>
      <button @click="teamInfoBtn" :disabled="infoActive">야구팀 출력</button>
    </div>

    <div v-if="step===1">
      <span>{{teamNumber}} 팀의 이름을 입력하세요 :</span>
      <input type="text" v-model="teamName" @keyup.enter="inserTeamName" />
      <button @click="inserTeamName">입력</button>
    </div>

    <div v-if="step===2">
      <p>{{teamName}} 팀 정보 입력</p>
      <form>
        <p>{{playerCount}}번 타자 정보 입력 해주세요</p>
        <span>선수이름 :</span>
        <input type="text" v-model="name" />
        <span>타율(0.1~0.5) :</span>
        <input type="number" v-model="batting" />
        <button @click.prevent="saveInfo">입력</button>
      </form>
    </div>

    <div v-if="step===3">
      <p>팀입력이 완료되었습니다.</p>
    </div>
    <!-- 팀정보 뷰 -->
    <div v-if="teamInfoView">
      <ul>
        <span>{{firstName[0].name}} 팀 정보</span>
        <li v-for="f in firstTeam" :key="f.index">
          <p>{{f.id}} 번 타자</p>
          <p> 이름 : {{f.player}}</p>
          <p>타율 : {{f.ba}}</p>
        </li> <br />
        <span>{{secondName[0].name}} 팀 정보</span>
        <li v-for="f in secondTeam" :key="f.index">
          <p>{{f.id}} 번 타자</p>
          <p>이름 : {{f.player}}</p>         
          <p>타율 : {{f.ba}}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputActive: false,
      infoActive: true,
      step: 0, // 화면 컨트롤
      teamInfoView: false, // 팀 정보 뷰
      teamNumber: 1, // 1, 2팀 정하는 넘버
      teamName: "", // input 팀명 정하는 v-model
      firstName: [], // 첫번째 팀 이름
      secondName: [], //두번째 팀 이름,
      playerCount: 1, // 등록된 선수 수,
      firstTeam: [],
      secondTeam: [],
      name: "", // 선수 이름
      batting: "" // 입력폼 타율
    };
  },
  methods: {
    teamBtn() {
      this.inputActive = true;
      this.step += 1;
    },
    teamInfoBtn() {
      // 팀 정보 뷰 on/off 버튼
      this.teamInfoView = !this.teamInfoView;
    },
    inserTeamName() {
      this.firstName.length
        ? (this.secondName.push({ name: this.teamName }), (this.step += 1))
        : (this.firstName.push({ name: this.teamName }), (this.step += 1));
    },
    saveInfo() {
      this.formVal(); // 입력폼 검증
    },
    formVal() {
      if (this.batting <= 0.1 || this.batting >= 0.5 || "" || this.name == "") {
        alert("입력값이 잘못되었습니다.");
      } else if (this.firstTeam.length < 9) {
        this.firstInsert();
      } else {
        this.secondInsert();
      }
    },
    firstInsert() {
      this.firstTeam.push({
        id: this.playerCount,
        player: this.name,
        ba: this.batting
      });
      this.name = "";
      this.batting = "";
      this.playerCount += 1;
      if (this.playerCount == 10) {
        this.step = 1; // 1번팀 9명 선수 입력이끝낫으면 2번팀입력으로 이동
        this.playerCount = 1;
        this.teamNumber = 2;
      }
    },
    secondInsert() {
      this.secondTeam.push({
        id: this.playerCount,
        player: this.name,
        ba: this.batting
      });
      this.name = "";
      this.batting = "";
      this.playerCount += 1;
      if (this.playerCount == 10) {
        this.step = 3; // 1, 2팀 선수 입력 완료 후 화면
        this.infoActive = false;
      }
    }
  }
};
</script>

<style>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  /* display: none; <- Crashes Chrome on hover */
  -webkit-appearance: none;
  margin: 0; /* <-- Apparently some margin are still there even though it's hidden */
}

input[type="number"] {
  -moz-appearance: textfield; /* Firefox */
}
</style>