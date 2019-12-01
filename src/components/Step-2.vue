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
      <button @click="startBtn">게임시작</button>
      <!-- 경기가 시작될떄 보일 뷰 -->
      <div v-show="playView">
        <p>
          <button :disabled="matchView" @click="nextBtn">진행</button>
        </p>
        *** {{firstName[0].name}} vs {{secondName[0].name}}의 시합을 시작합니다. ***
        <div v-show="!matchView">
          <!-- 1번팀 공격 -->
          <div v-if="attacked">
            <p>{{round}}회초 {{firstName[0].name}}팀 공격</p>
            <p>{{firstTeam[firstOrder].id}}번 {{firstTeam[firstOrder].player}} 선수 차례</p>
          </div>
          <!-- 2번팀 공격 -->
          <div v-else>
            <p>{{round}}회말 {{secondName[0].name}}팀 공격</p>
            <p>{{secondTeam[secondOrder].id}}번 {{secondTeam[secondOrder].player}} 선수 차례</p>
          </div>
        </div>
        <!-- SBO 상태 -->
        <div>
          {{strike}}S {{ball}}B {{out}}O
          <br />
          <br />
          {{ stat }}
        </div>
        <!-- 게임 종료 후 보일 뷰 -->
        <div v-show="matchView">
          <p>경기 종료</p>
          <p>{{firstName[0].name}} VS {{secondName[0].name}}</p>
          <p>{{firstScore}} : {{secondScore}}</p>Thank you !
        </div>
      </div>
    </div>
    <!-- 팀정보 뷰 -->
    <div v-if="teamInfoView">
      <ul>
        <span>{{firstName[0].name}} 팀 정보</span>
        <li v-for="f in firstTeam" :key="f.index">
          <p>{{f.id}} 번 타자</p>
          <p>이름 : {{f.player}}</p>
          <p>타율 : {{f.ba}}</p>
        </li>
        <br />
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
/* eslint-disable */
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
      batting: "", // 입력폼 타율
      //게임 구현부분 변수
      playView: false,
      hitter: ["스트라이크!", "볼!", "안타!", "아웃!"],
      stat: "",
      strike: 0,
      ball: 0,
      hits: 0,
      out: 0,
      matchView: false,
      round: 1,
      isInning: true, // 회초 인지 회말인지
      firstOrder: 0, // 첫번째팀 팀 선수 순번을 결정해주는 변수
      secondOrder: 0, // 두번쨰팀 팀 선수 순번을 결정해주는 변수
      attacked: true, // isInning과 같아서 지울수잇는지 검수
      firstScore: 0,
      secondScore: 0,
      pitcher: 0, // 투구 횟수
      strikeOut: 0 // 삼진 횟수
    };
  },
  methods: {
    // 팀 정보 입력 메서드
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
        ? (this.secondName.push({ name: this.teamName }), this.step += 1)
        : (this.firstName.push({ name: this.teamName }), this.step += 1);
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
    },
    // 게임 구현 부분 메서드
    startBtn() {
      this.playView = true;
    },
    init() {
      this.strike = 0;
      this.ball = 0;
    },
    orderNum() {
      // 팀 선수 마지막 선수 차례가 끝나면 처음선수로 돌려주는 함수
      if (this.isInning) {
        this.firstOrder += 1; // 첫번째팀 다음선수로 넘겨주는 로직
        if (this.firstOrder >= 9) this.firstOrder = 0; // 첫번째팀 마지막선수면 처음천수로
      } else {
        this.secondOrder += 1;
        if (this.secondOrder >= 9) this.secondOrder = 0;
      }
    },
    nextBtn() {
      this.stat = this.hitter[Math.floor(Math.random() * this.hitter.length)];
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
      this.strike += 1;
      if (this.strike == 3) {
        this.out += 1;
        this.stat = "3스트라이크! 아웃! 다음 타자가 타석에 입장 했습니다.";
        this.strike = 0;
        this.orderNum();
        if (this.out == 3) {
          this.stat = "스트라이크 아웃!";
          if (!this.isInning) {
            // 회말에 3아웃이 됫으면 다음 회로
            this.round += 1;
          }
          this.attacked = !this.attacked; // 3아웃되면 각 팀 뷰로 바뀌게   <<< 여기추가
          this.out = 0; // 아웃 초기화
          this.hits = 0; // 안타수 초기화 (한회에 4안타마다 1점이 올라가기떄문에, 한회가 끝나면 초기화 , 나중에 안타수는 따로 누적해줘야될듯)
          console.log(this.round);
          if (!this.isInning && this.round == 7) {
            this.matchView = !this.matchView;
          }
          this.isInning = !this.isInning; // 회말인지 회초인지 체크
        }
      }
    },
    ballFunc() {
      this.ball += 1;
      if (this.ball == 4) {
        this.orderNum();
        this.stat = "4볼! 다음 타자가 타석에 입장 했습니다.";
        this.strike = 0;
        this.ball = 0;
        this.hits += 1;
        if (this.hits >= 4) {
          // 4안타 이상일떄 득점
          if (this.isInning) {
            this.firstScore += 1; // 회초 이면 첫번쨰팀 점수 +1
          } else {
            this.secondScore += 1; // 회말이면 두번째팀 점수 +1
          }
        }
      }
    },
    hitsFunc() {
      this.stat = "안타! 다음 타자가 타석에 입장 했습니다.";
      this.strike = 0;
      this.ball = 0;
      this.hits += 1;
      if (this.hits >= 4) {
        // 4안타 이상일떄 득점
        if (this.isInning) {
          this.firstScore += 1; // 회초 이면 첫번쨰팀 점수 +1
        } else {
          this.secondScore += 1; // 회말이면 두번째팀 점수 +1
        }
      }
      this.orderNum();
    },
    outFunc() {
      this.out += 1;
      this.stat = "아웃! 다음 타자가 타석에 입장 했습니다.";
      this.orderNum();
      if (this.out == 3) {
        this.stat = "아웃!"; // 상대선수로 넘기거나, 마지막 회말이면 게임종료
        this.attacked = !this.attacked; // 3아웃되면 각 팀 뷰로 바뀌게   <<< 여기추가
        if (!this.isInning) {
          // 회말에 3아웃이 됫으면 다음 회로
          this.round += 1; 
        }
        this.out = 0; // 아웃 초기화
        this.hits = 0; // 안타수 초기화 (한회에 4안타마다 1점이 올라가기떄문에, 한회가 끝나면 초기화 , 나중에 안타수는 따로 누적해줘야될듯)
        console.log(this.round);
        if (!this.isInning && this.round == 7) {
          this.matchView = !this.matchView;
        }
        this.isInning = !this.isInning; // 회말인지 회초인지 체크
      }
      this.strike = 0;
      this.ball = 0;
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