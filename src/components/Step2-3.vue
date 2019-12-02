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
        <p><button :disabled="matchView" @click="nextBtn">진행</button></p>
        *** {{firstName[0].name}} vs {{secondName[0].name}}의 시합을 시작합니다. ***
        <div v-show="!matchView">
          <!-- 1번팀 공격 -->
          <div v-if="isInning">
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
    </div> <hr />

    <div>
      <h1>Step-3</h1>
      <div class="container">
        <table class="type11">
          <thead>
            <tr>
              <th scope="cols"></th>
              <th scope="cols">1회 2회 3회 4회 5회 6회</th>
              <th scope="cols">점수 현황</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="cols">{{firstName[0].name}}</th>
              <th scope="cols">0---0---0---0---0---0</th>
              <th scope="cols">{{this.firstScore}}</th>
            </tr>
            <tr>
              <th scope="cols">{{secondName[0].name}}</th>
              <th scope="cols">0---0---0---0---0---0</th>
              <th scope="cols">{{this.secondScore}}</th>
            </tr>
            <tr>
              <td>
                <ul>
                  {{firstName[0].name}} 팀
                  <li v-for="fteam in firstTeam" :key="fteam.id">
                    <span>{{fteam.id}}. {{fteam.player}}</span>
                  </li>
                </ul>
              </td>
              <td>
                <p>S : {{strike}}</p>
                <p>B : {{ball}}</p>
                <p>O : {{out}}</p>
              </td>
              <td>
                <ul>
                  {{secondName[0].name}} 팀
                  <li v-for="steam in secondTeam" :key="steam.id">
                    <span>{{steam.id}}. {{steam.player}}</span>
                  </li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>투구수 : {{firstpitching}}</p>
                <p>삼진수 : {{so.fso}}</p>
                <p>안타수 : {{teamHits.fhits}}</p>
              </td>
              <td></td>
              <td>
                <p>투구수 : {{secondPitching}}</p>
                <p>삼진수 : {{so.sso}}</p>
                <p>안타수 : {{teamHits.shits}}</p>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <p>스킵할 회를 입력해주세요(ex 4 -> 5회초부터시작)</p>
    <input type="number" v-model="skipNum" min="1" max="6" style="width: 100px" placeholder="스킵 할 회 입력">
    <button :disabled="skipBtn" id="skip" @click="skipFunc">Skip</button>
    <small>(팀정보 입력이 완료되야 버튼이 활성화 됩니다)</small>
    <p>스킵후 위 스텝에서 진행버튼을 눌려주세요.</p>
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
      firstName: [{name: '1팀'}], // 첫번째 팀 이름
      secondName: [{name: '2팀'}], //두번째 팀 이름,
      playerCount: 1, // 등록된 선수 수,
      firstTeam: [],
      secondTeam: [],
      name: "", // 선수 이름
      batting: "", // 입력폼 타율
      //게임 구현부분 변수
      playView: false,
      matchView: false,
      isInning: true, // 회초 인지 회말인지
      hitter: ["스트라이크!", "볼!", "안타!", "아웃!"],
      stat: "",
      strike: 0,
      ball: 0,
      hits: 0,
      out: 0,
      round: 1,
      firstOrder: 0, // 첫번째팀 팀 선수 순번을 결정해주는 변수
      secondOrder: 0, // 두번쨰팀 팀 선수 순번을 결정해주는 변수
      // attacked: true, // isInning과 같아서 지울수잇는지 검수
      firstScore: 0,
      secondScore: 0,
      strikeOut: 0, // 삼진 횟수
      firstpitching: 0, // 1팀 총 투구수 
      secondPitching: 0, // 2팀 총 투구수 ,
      so: {fso: 0, sso: 0}, // 각팀 삼진 수
      teamHits: {fhits: 0, shits: 0}, // 각팀 안타수,
      skipBtn: true,
      skipNum: 0
    };
  },
  methods: {
    // 팀 정보 입력 메서드
    teamBtn() {
      this.inputActive = true;
      this.step += 1;
    },
    teamInfoBtn() { // 팀 정보 뷰 on/off 버튼
      this.teamInfoView = !this.teamInfoView;
    },
    inserTeamName() {
      this.firstName.length == 2
        ? (this.secondName.unshift({ name: this.teamName }), this.step += 1)
        : (this.firstName.unshift({ name: this.teamName }), this.step += 1);
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
        ba: parseFloat(this.batting).toFixed(3) // 소수점 3자리까지 자르기
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
      this.skipBtn = false; // 스킵 버튼 활성화
    },
    init() {
      this.strike = 0;
      this.ball = 0;
    },
    orderNum() { // 팀 선수 마지막 선수 차례가 끝나면 처음선수로 돌려주는 함수
      if (this.isInning) {
        this.firstOrder += 1; // 첫번째팀 다음선수로 넘겨주는 로직
        if (this.firstOrder >= 9) this.firstOrder = 0; // 첫번째팀 마지막선수면 처음천수로
      } else {
        this.secondOrder += 1;
        if (this.secondOrder >= 9) this.secondOrder = 0;
      }
    },
    nextBtn() {
      var h = 0
      if(this.isInning) { // 회초인지 회말인지 판단으로 1, 2팀 선택 후 각선수 타율 가져오기 
        h = this.firstTeam[this.firstOrder].ba
      } else {
        h = this.secondTeam[this.secondOrder].ba
      }
      this.isInning ? this.firstpitching += 1 : this.secondPitching += 1 // 각팀 투구수 총합
      var a = Math.random()
      parseFloat(h) 
      if(a <= 0.1) {
        this.stat = "아웃!"
      } else if(a <= h) {
        this.stat = "안타!"
      } else if((a <= 1-h-0.1)/2) {
        this.stat = "볼!"
      } else {
        this.stat = "스트라이크!"
      }
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
        this.isInning ? this.so.fso += 1 : this.so.sso += 1 // 3스트 일때 각팀 삼진수 누적
        if (this.out == 3) {
          this.stat = "스트라이크 아웃!";
          if (!this.isInning) { // 회말에 3아웃이 됫으면 다음 회로
            this.round += 1;
          } // this.attacked = !this.attacked; // 3아웃되면 각 팀 뷰로 바뀌게   <<< 여기추가
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
        this.isInning ? this.teamHits.fhits+= 1 : this.teamHits.shits+= 1 // 4볼일때 각팀 안타수 +1
        if (this.hits >= 4) { // 4안타 이상일떄 득점
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
      this.isInning ? this.teamHits.fhits+= 1 : this.teamHits.shits+= 1 // 각팀 안타수 +1
      if (this.hits >= 4) { // 4안타 이상일떄 득점
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
        if (!this.isInning) { // 회말에 3아웃이 됫으면 다음 회로
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
    },
    skipFunc() {
      while(this.round <= this.skipNum) {
        console.log('스킵호출되엇다.',this.round, this.skipNum)
        // (function(){document.getElementById('skip').click()})()
        this.nextBtn()
      }
      this.skipBtn= true
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

table.type11 {
  border-collapse: separate;
  border-spacing: 1px;
  text-align: center;
  line-height: 1.5;
  margin: 20px 10px;
}
table.type11 th {
  width: 200px;
  padding: 10px;
  font-weight: bold;
  vertical-align: top;
  color: #fff;
  background: #ce4869;
}
table.type11 td {
  width: 155px;
  padding: 10px;
  vertical-align: top;
  border-bottom: 1px solid #ccc;
  background: #eee;
}
p {
  margin: 2px 0px;
}
</style>