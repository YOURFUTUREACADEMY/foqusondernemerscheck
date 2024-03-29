
<template>  
  <div class="outer">    
    <div class="innerTop" :style="innerStyle">      
      <div class="bar" id="bar" :style="barStyle">
        <div id="labelOver" class="valueLabel" :style="overStyle">{{ valueLabel }}</div>
      </div>
      <div id="labelUnder" class="valueLabel" :style="underStyle">{{ valueLabel }}</div>
    </div>
    <div class="thumb" :style="thumbStyle"></div>
    <input
      v-model.number="value"
      type="range"
      class="slider"
      id="slider"
      :min="min"
      :max="max"
      :step="step"
      :style="sliderStyle"
      @change="emitValue"
    >
    <div class="sideCover" id="leftCover"></div>
    <div class="sideCover" id="rightCover"></div>    
  </div> 
</template>

<script>
export default {
  data() {
    return {
      value: 0,
      steps: 100,
      width: 400,
      togglePoint: 0,
      innerStyle: {
        backgroundColor: "#A6A6A6",
        width: "284px"        
      },        
      barStyle: {
        width: "0%",
        backgroundColor: "#08344d"
      }, 
      thumbStyle: {          
        top: "24px",
        right: "0px",
        bottom: "0px",
        left: "-8px",
        borderBottomColor: "#08344d"
      },
      underStyle: {
        top: "0px",
        left: "0px",
        color: "",
        opacity: 1
      },
      overStyle: {
        color: "",
        opacity: 0
      },
      sliderStyle: {
        width: "400px"
      }    
    }
  },

  updated() {
    this.changePos();
    this.setTogglePoint();
  },

  mounted() {
    window.addEventListener('resize', this.setWidth);
    this.buildSlider();  
    this.changePos();
  },
  
  unmounted() {
    window.removeEventListener('resize', this.setWidth);
  },

  props: { 
    min: {
      type: Number,
      required: true
    }, 
    max: {
      type: Number,
      required: true
    }, 
    step: {
      type: Number,
      step: 1
    },
    startValue: {
      type: Number,
      default: 0
    },
    showPercentage: {
      type: Boolean,
      default: false
    },
    toggleHalfway: {
      type: Boolean,
      default: false
    },
    color: {       
      default: {
        primaryColor: "#08344d",
        secondaryColor: "#A6A6A6",
        labelInBarColor: "#FFFFFF" 
      }
    },    
  },

  computed: {
    barWidth() {
      return this.width - 16;
    },
    barWidthString() {
      return `${this.barWidth}px`;
    },
    currentStep() {
      return (this.value - this.min) / this.step;
    },
    sliderWidthString() {
      return `${this.width}px`
    },
    stepWidth() {
      return this.barWidth / this.steps;
    },
    percentage() {
      return 100 * this.currentStep / this.steps;
    },
    valueLabel() {
      if (this.showPercentage) {
        return `${this.percentage.toFixed(0)}%`; 
      } else {
        return this.value;
      }
    },
    position() {
      return this.currentStep * this.stepWidth;
    },    
  },

  methods: {
    emitValue() {      
      this.$emit('sliderChange', this.value);
    },
    changePos() {      
      this.barStyle.width = `${this.position}px`;
      this.underStyle.left = `${this.position + 2}px`;
      this.thumbStyle.left = `${this.position}px`;
      if (Number.parseInt(this.barStyle.width) > this.togglePoint) {
        this.overStyle.opacity = 1;
        this.underStyle.opacity = 0;
      } else {
        this.overStyle.opacity = 0;
        this.underStyle.opacity = 1;
      }
    },
    buildSlider() {
      this.setWidth();      
      this.value = this.startValue < this.min ? this.min : (this.startValue > this.max ? this.max : this.startValue);    
      this.barStyle.backgroundColor = this.color.primaryColor;
      this.thumbStyle.borderBottomColor = this.color.primaryColor;
      this.innerStyle.backgroundColor = this.color.secondaryColor;
      this.overStyle.color = this.color.labelInBarColor;
      this.underStyle.color = this.color.primaryColor;
    },
    setWidth() {
      const container = document.querySelector(".outer");
      const containerStyles = window.getComputedStyle(container);      
      this.width = Number.parseInt(containerStyles.width);
      this.innerStyle.width = this.barWidthString;
      this.steps = (this.max - this.min) / this.step;
      this.sliderStyle.width = this.sliderWidthString;
      this.setTogglePoint();       
    },
    setTogglePoint() {
      if (this.toggleHalfway) {
        this.togglePoint = Number.parseInt(this.width / 2);
      } else {
        const label = document.querySelector("#labelUnder");
        if (label) {
          const labelStyle = window.getComputedStyle(label);
          this.togglePoint = Number.parseInt(labelStyle.width);
        } else {
          console.log("label bestaat niet")
        } 
      }
    }
  },

  emits: ['sliderChange'],   
}
</script>

<style scoped>
.outer {
  position:relative;  
  height:40px;
  margin: none;
}
.innerTop {
  position:absolute;
  top:0;right:0;bottom:0;left:8px;
  height:24px;
  border: none; 
}
.sideCover {
  height: 24px;
  width: 10px;
  position: absolute;
  background-color: transparent;
}
#leftCover {
  top:0;left:0;
}
#rightCover {
  top:0;right:0;
}
.bar {
  position: absolute;
  top:0;right:0;bottom:0;left:0;
}
.thumb {  
  position:absolute;
  width:0px;
  height:0px;
  border-left:8px solid #FFFFFF;
  border-right:8px solid #FFFFFF;
  border-bottom:16px solid;
}
.valueLabel {
  background: transparent;
  font-size: 17px;
  padding: 0px 3px;
  line-height: 24px;
  vertical-align: middle;
  -webkit-user-select: none;        
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
#labelUnder {
  position: absolute;
  color: #08344d;
}
#labelOver {
  display: flex;
  flex-direction: row-reverse;
}
.slider {
  position:absolute;
  top:0;right:0;bottom:0;left:0;
  -webkit-appearance: none;
  height:24px;
  opacity:0;
}
.slider:focus {
  outline: none;
}
.slider::-webkit-slider-thumb {
  -webkit-appearance:none;
  height:40px;
  width:16px;
  cursor:pointer;
  margin-top:0;
  transform: translateY(8px);
}
.slider::-moz-range-thumb {
  height:40px;
  width:16px;
  cursor:pointer;
  margin-top:0;
  transform: translateY(8px)
}
</style>
