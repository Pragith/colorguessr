<template>
  <b-card title="Board">
    <b-card-text>{{ val1 }},{{ val2 }},{{ val3 }}</b-card-text>
    <b-card-text>Score: {{ scorePerc }}%</b-card-text>
    <vue-apex-charts
      type="radialBar"
      height="270"
      :options="supportTrackerRadialBar.chartOptions"
      :series="supportTrackerRadialBar.series"
    />
    <b-form-group>
        <b-form-input
          v-model="val1"
          @change=changeColor
        />
      <b-form-input
          v-model="val2"
          @change=changeColor
        />
      <b-form-input
          v-model="val3"
          @change=changeColor
        />
    </b-form-group>
    <div>      
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
    </div>
    <div id="colorBox">
      <div id="outputBox" :style="outputBoxColor">&nbsp;</div>
      <div id="challengeBox" :style="challengeBoxColor">&nbsp;</div>
    </div>
  </b-card>
</template>

<script>
import { BCard, BCardText, BFormSelect, BFormSelectOption, BFormGroup, BFormInput } from 'bootstrap-vue'
import VueSlider from 'vue-slider-component'
import VueApexCharts from 'vue-apexcharts'
import { $themeColors } from '@themeConfig'
import store from '@/store'


export default {
  components: {
    VueSlider,
    VueApexCharts,
    BCard,
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
        backgroundColor:"rgba(255, 0, 155, 1)",
        height: 20,
        width: 100,
      },
      outputBoxColor:{
        backgroundColor:"rgba(0, 0, 0, 1)",
        height: 20,
        width: 100,
      },
      val1: 0,
      val2: 0,
      val3: 0,
      dir: 'ltr',
      score: 0,
      totalScore: 765,
      scorePerc: 0,
      challengeVal: "rgba(255, 0, 155, 1)",
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
          labels: ['Completed Tickets'],
        },
      },
    }
  },
  methods: {
    scoreInput(){
      var challengeValArr = this.challengeBoxColor.backgroundColor.replace("rgba(","").replace(")","").split(",");
      challengeValArr = [parseInt(challengeValArr[0]), parseInt(challengeValArr[1]), parseInt(challengeValArr[2])];
      var userValArr = [parseInt(this.val1), parseInt(this.val2), parseInt(this.val3)];

      var red = Math.abs(userValArr[0] - challengeValArr[0]);
      var green = Math.abs(userValArr[1] - challengeValArr[1]);
      var blue = Math.abs(userValArr[2] - challengeValArr[2]);

      console.log(challengeValArr);
      console.log(userValArr);
      console.log(red, green, blue);

      this.score = this.totalScore - (red + green + blue);
      this.scorePerc = Math.round(this.score * 100 / this.totalScore, 0);
      
    }
    
    ,changeColor(){
      this.outputBoxColor.backgroundColor = `rgba(${this.val1}, ${this.val2}, ${this.val3}, 1)`;
      this.scoreInput();
    },
  },
  computed: {
    direction() {
      return 'ltr'
    },
  },
}
</script>

<style>

</style>
