<template>
  <div>
    
    <b-card>
      <b-card-title>        
          <b-icon icon="chart-pie" />
          <span class="ml-2">Mode: 
            <!-- dark -->
              <b-dropdown
                v-ripple.400="'rgba(255, 255, 255, 0.15)'"
                split
                text="Freeplay"
                right
                variant="dark"
              >
                <b-dropdown-item :to="{ path: '/' }">
                  <b-icon icon="PlayIcon" /> Freeplay                  
                </b-dropdown-item>
                <b-dropdown-item :to="{ path: '/blind' }">
                  <b-icon icon="EyeOffIcon" /> Blind               
                </b-dropdown-item>
              </b-dropdown>
          </span>
      </b-card-title>
      <div class="scoreBox">
        <b-button variant="gradient-secondary">
          <feather-icon icon="TerminalIcon" /><br />
          {{outputBoxColor.backgroundColor}}
        </b-button>&nbsp;&nbsp;&nbsp;&nbsp;
        <b-button variant="gradient-success">
          <feather-icon icon="AwardIcon" /><br />
          {{won}} <span v-if="score != '0'">({{score}})</span>
        </b-button>&nbsp;&nbsp;&nbsp;&nbsp;
        <b-button :variant="gradient_score_perc">
          <feather-icon icon="ActivityIcon" /><br />
          {{scorePerc}}%
        </b-button>
      </div>
      
      <br />

      <div class="colorBox">
        <b-button-group size="lg"> 
          <b-button
            v-ripple.400="'{{outputBoxColor.backgroundColor}}'" 
            :style="outputBoxColor"
            variant="outline-dark"
          ><p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
          &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p>
          </b-button>
          <b-button
            v-ripple.400="'{{challengeBoxColor.backgroundColor}}'"
            :style="challengeBoxColor"
            variant="outline-dark"
          > <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
          &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p>
          </b-button>
        </b-button-group>
      </div>

      <br />

      <div class="sliderInputs">      
        <vue-slider
          v-model="val1"
          :direction="direction"
          :min="0"
          :max="255"
          :interval="1"
          class="mb-2 vue-slider-danger"
          @change=changeColor
        />

        <vue-slider
          v-model="val2"
          :direction="direction"
          :min="0"
          :max="255"
          :interval="1"
          class="mb-2 vue-slider-success"
          @change=changeColor
        />

        <vue-slider
          v-model="val3"
          :direction="direction"
          :min="0"
          :max="255"
          :interval="1"
          class="mb-2"
          @change=changeColor
        />

        <b-button block variant="gradient-primary" @click=scoreInput>
          Submit
        </b-button>

        <b-card-text class="answer">Dev: {{challengeBoxColor.backgroundColor}}</b-card-text>
      </div>
    </b-card>


    <b-card no-body >
      <!-- title -->
      <b-card-header class="pb-0">
        <b-card-title>Scorecard</b-card-title>
        <br /><br />
      </b-card-header>
      <!--/ title -->

      <b-card-body>
        <b-row>
          <!-- chart -->
          <b-col
            sm="10"
            class="d-flex justify-content-center"
          >
          </b-col>
          <!--/ chart -->
        </b-row>

        <!-- chart info -->
        <div class="d-flex justify-content-around">
          <div class="text-center">
            <span class="font-large-1 font-weight-bold">{{ played }}</span>
            <b-card-text class="mb-50">
              Played
            </b-card-text>
          </div>
          <div class="text-center">
            <span class="font-large-1 font-weight-bold">{{ won }}</span>
            <b-card-text class="mb-50">
              Won
            </b-card-text>
          </div>
        </div>
        <div class="d-flex justify-content-around">
          <div class="text-center">
            <span class="font-large-1 font-weight-bold">{{ currentStreak }}</span>
            <b-card-text class="mb-50">
              Current Streak
            </b-card-text>
          </div>
          <div class="text-center">
            <span class="font-large-1 font-weight-bold">{{ highestStreak }}</span>
            <b-card-text class="mb-50">
              Highest Streak
            </b-card-text>
          </div>
        </div>
      </b-card-body>
    </b-card>
  </div>
</template>

<script>
import VueSlider from 'vue-slider-component'
//import VueApexCharts from 'vue-apexcharts'
import Ripple from 'vue-ripple-directive'
import { BTab, BTabs, BCard, BCardHeader, BCardTitle, BDropdown, BDropdownItem, BCardBody, BRow, BCol, BCardText, BFormSelect, BFormSelectOption, BFormGroup, BFormInput,  BButtonGroup, BButton } from 'bootstrap-vue'
import { $themeColors } from '@themeConfig'


export default {
  components: {
    VueSlider,
    //VueApexCharts,
    BTab, BTabs,
    BButtonGroup, 
    BButton,
    BCard,
    BCardHeader,
    BCardTitle,
    BDropdown,
    BDropdownItem,
    BCardText,
    BCardBody,
    BRow,
    BCol,
    BCardText,    
    BFormSelect,
    BFormGroup,
    BFormInput,
    BFormSelectOption,
  },
  props: {
    data: {
      type: Object,
      default: () => {},
    },
  },
  data() {
    return {
      challengeBoxColor:{
        backgroundColor:"rgb(255, 0, 155)",
        height: 20,
        width: 100,
      },
      outputBoxColor:{
        backgroundColor:"rgb(0, 0, 0)",
        height: 20,
        width: 100,
      },
      gameMode: "Freeplay",
      played: 0,
      won: 0,
      highestStreak: 0,
      currentStreak: 0,
      currentResult: 0,
      val1: 0,
      val2: 0,
      val3: 0,
      dir: 'ltr',
      score: "0",
      scorePerc: 0,
      totalScore: 765,
      gradient_score_perc: 'gradient-danger',
      challengeVal: "rgb(255, 0, 155)",
      supportTrackerRadialBar: {
        chartOptions: {
          plotOptions: {
            radialBar: {
              size: 150,
              offsetY: 20,
              startAngle: -150,
              endAngle: 150,
              hollow: {
                size: '65%',
              },
              track: {
                background: '#fff',
                strokeWidth: '100%',
              },
              dataLabels: {
                name: {
                  offsetY: -5,
                  color: '#5e5873',
                  fontSize: '1rem',
                },
                value: {
                  offsetY: 15,
                  color: '#5e5873',
                  fontSize: '1.714rem',
                },
              },
            },
          },
          colors: [$themeColors.danger],
          fill: {
            type: 'gradient',
            gradient: {
              shade: 'dark',
              type: 'horizontal',
              shadeIntensity: 0.5,
              gradientToColors: [$themeColors.primary],
              inverseColors: true,
              opacityFrom: 1,
              opacityTo: 1,
              stops: [0, 100],
            },
          },
          stroke: {
            dashArray: 8,
          },
          labels: ['.'],
        },
      },
    }
  },
  methods: {
    scoreInput(){
      var challengeValArr = this.challengeBoxColor.backgroundColor.replace("rgb(","").replace(")","").split(",");
      challengeValArr = [parseInt(challengeValArr[0]), parseInt(challengeValArr[1]), parseInt(challengeValArr[2])];
      var userValArr = [parseInt(this.val1), parseInt(this.val2), parseInt(this.val3)];

      var red = Math.abs(userValArr[0] - challengeValArr[0]);
      var green = Math.abs(userValArr[1] - challengeValArr[1]);
      var blue = Math.abs(userValArr[2] - challengeValArr[2]);
      
      var scoreTemp = this.totalScore - (red + green + blue);
      this.scorePerc = Math.round(scoreTemp * 100 / this.totalScore, 0);
      this.played = this.played + 1;
      this.currentResult = 0;
      this.score = "0";

      if (this.scorePerc >= 90) {
        this.gradient_score_perc = "gradient-success";
        this.won = this.won + 1;        
        this.score = "+1";
        this.currentResult = 1;
      }
      else if (this.scorePerc >= 70 && this.scorePerc < 90) {
        this.gradient_score_perc = "gradient-warning";
      }
      else {
        this.gradient_score_perc = "gradient-danger";
      }

      if (this.currentResult == 1) {
        this.currentStreak = this.currentStreak + 1;
        this.highestStreak = Math.max(this.currentStreak, this.highestStreak);
      }
      else {
        this.currentStreak = 0;
      }

      this.generateColor();
    }
    
    ,changeColor(){
      this.outputBoxColor.backgroundColor = `rgb(${this.val1}, ${this.val2}, ${this.val3})`;
    }

    ,generateColor(){
      var red = Math.floor(Math.random() * 255);
      var green = Math.floor(Math.random() * 255);
      var blue = Math.floor(Math.random() * 255);
      this.challengeVal = `rgb(${red}, ${green}, ${blue})`;
      this.challengeBoxColor.backgroundColor = `rgb(${red}, ${green}, ${blue})`;      
      this.outputBoxColor.backgroundColor = `rgb(0, 0, 0)`;
      this.val1 = this.val2 = this.val3 = 0;
    },
  },
  computed: {
    direction() {
      return 'ltr'
    },
  },
  mounted() {
      this.generateColor();
      if (localStorage.currentStreak) {
        this.currentStreak = parseInt(localStorage.currentStreak);
      }
      if (localStorage.highestStreak) {
        this.highestStreak = parseInt(localStorage.highestStreak);
      }
      if (localStorage.played) {
        this.played = parseInt(localStorage.played);
      }
      if (localStorage.won) {
        this.won = parseInt(localStorage.won);
      }
  },
  watch: {
    currentStreak(val) {
      localStorage.currentStreak = val;
    },
    highestStreak(val) {
      localStorage.highestStreak = val;
    },
    played(val) {
      localStorage.played = val;
    },
    won(val) {
      localStorage.won = val;
    },
  },
  directives: {
    Ripple,
  },
}
</script>

<style lang="scss">
@import '@core/scss/vue/libs/vue-slider.scss';

.answer {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-size:7px;
  color: rgb(82,83,165);
}

.scoreBox, .colorBox {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}


</style>
