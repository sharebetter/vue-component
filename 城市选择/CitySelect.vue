<template>
  <div>
    <div class="btn" @click="showPicker">城市选择</div>
    <van-popup v-model="showCityPicker" position="bottom">
      <div class="city-wrap">
        <div class="top flex-h">
          <div class="cancel" @click="cancel">取消</div>
          <div class="place-select flex-1 ft-w">{{citySelectInfo.cityName || '选地区'}}</div>
          <div class="confirm color-blue" @click="confirm">确定</div>
        </div>
        <div class="city-select ft-28">
          <span class="pick" @click="levelPicker(0, levelSelectInfo.levelOneIndex)" v-if="levelSelectInfo.levelOneName">{{levelSelectInfo.levelOneName}}</span>
          <span class="pick" @click="levelPicker(1, levelSelectInfo.levelTwoIndex)" v-if="levelSelectInfo.levelTwoName" :class="(levelSelectInfo.levelTwoName && !levelSelectInfo.levelThreeName && !levelSelectInfo.hasNextLevel) ? 'color-blue':''">{{levelSelectInfo.levelTwoName}}</span>
          <span class="pick" v-if="levelSelectInfo.levelThreeName" :class="levelSelectInfo.levelThreeName ? 'color-blue':''">{{levelSelectInfo.levelThreeName}}</span>
          <span class="pick color-blue" v-if="levelSelectInfo.hasNextLevel">请选择</span>
        </div>
        <!-- 城市选择列表 -->
        <div class="city-list" v-show="levelSelectInfo.showLevel === 0">
          <div class="city-name ft-28" v-for="(item, index) in cityData" :key="item.code" @click="levelselect(1, index, item)">
            <span :class="levelSelectInfo.levelOneIndex === index ? 'color-blue':''">{{item.name}}</span>
            <img v-if="levelSelectInfo.levelOneIndex === index" :src="require('@pic/common/icon-city-select.png')">
          </div>
        </div>
        <div class="city-list" v-show="levelSelectInfo.showLevel === 1">
          <div v-if="levelSelectInfo.showLevel === 1">
            <div class="city-name ft-28" v-for="(item, index) in cityData[levelSelectInfo.levelOneIndex].list" :key="item.code" @click="levelselect(2, index, item)">
              <span :class="levelSelectInfo.levelTwoIndex === index ? 'color-blue':''">{{item.name}}</span>
              <img v-if="levelSelectInfo.levelTwoIndex === index" :src="require('@pic/common/icon-city-select.png')">
            </div>
          </div>
        </div>
        <div class="city-list" v-show="levelSelectInfo.showLevel === 2">
          <div v-if="levelSelectInfo.showLevel === 2">
            <div class="city-name ft-28" v-for="(item, index) in cityData[levelSelectInfo.levelOneIndex].list[levelSelectInfo.levelTwoIndex].list" :key="item.code" @click="levelselect(3, index, item)">
              <span :class="levelSelectInfo.levelThreeIndex === index ? 'color-blue':''">{{item.name}}</span>
              <img v-if="levelSelectInfo.levelThreeIndex === index" :src="require('@pic/common/icon-city-select.png')">
            </div>
          </div>
        </div>
      </div>
    </van-popup>
  </div>
</template>

<script>
export default {
  data () {
    let cityData = require('@/assets/city_code.json')
    return {
      cityData: cityData,
      showCityPicker: false,
      /**
       * 确定
       * levelOne省级
       * levelTwo市级
       * levelThree县级
       */
      levelInfo: {
        levelOneIndex: '',
        levelOneName: '',
        levelOneCode: '',
        levelTwoIndex: '',
        levelTwoName: '',
        levelTwoCode: '',
        levelThreeIndex: '',
        levelThreeName: '',
        levelThreeCode: '',
        // 当前展示的城市列表等级
        showLevel: 0,
        // 是否有下一级
        hasNextLevel: true,
      },
      // 城市信息（确定）
      cityInfo: {
        cityName: '',
        cityCode: '',
      },
      //* 未确定
      levelSelectInfo: {},
      // 城市选择信息（未确定）
      citySelectInfo: {},
    }
  },
  created () {
    this.citySelectInfo = { ...this.cityInfo }
    this.levelSelectInfo = { ...this.levelInfo }
  },
  methods: {
    showPicker () {
      this.showCityPicker = true
      this.citySelectInfo = { ...this.cityInfo }
      this.levelSelectInfo = { ...this.levelInfo }
    },
    levelselect (level, index, item) {
      this.citySelectInfo.cityName = item.name
      this.citySelectInfo.cityCode = item.code
      switch (level) {
        case 1:
          this.levelSelectInfo.levelOneIndex = index
          this.levelSelectInfo.showLevel = level
          this.levelSelectInfo.levelOneName = item.name
          this.levelSelectInfo.levelOneCode = item.code
          this.levelSelectInfo.hasNextLevel = true
          break;
        case 2:
          this.levelSelectInfo.levelTwoIndex = index
          this.levelSelectInfo.levelTwoName = item.name
          this.levelSelectInfo.levelTwoCode = item.code
          this.levelSelectInfo.hasNextLevel = item.list.length > 0
          if (this.levelSelectInfo.hasNextLevel) {
            this.levelSelectInfo.showLevel = level
          }
          break;
        case 3:
          this.levelSelectInfo.levelThreeIndex = index
          this.levelSelectInfo.levelThreeName = item.name
          this.levelSelectInfo.levelThreeCode = item.code
          this.levelSelectInfo.hasNextLevel = false
          break;
        default:
          break;
      }
    },
    levelPicker (level, index) {
      this.levelSelectInfo.showLevel = level
      switch (level) {
        case 0:
          this.levelSelectInfo.levelTwoIndex = ''
          this.levelSelectInfo.levelTwoName = ''
          this.levelSelectInfo.levelThreeIndex = ''
          this.levelSelectInfo.levelThreeName = ''
          this.levelSelectInfo.hasNextLevel = true
          this.citySelectInfo = {
            cityName: this.levelSelectInfo.levelOneName,
            cityCode: this.levelSelectInfo.levelOneCode
          }
          break;
        case 1:
          this.levelSelectInfo.levelThreeIndex = ''
          this.levelSelectInfo.levelThreeName = ''
          this.levelSelectInfo.hasNextLevel = true
          this.citySelectInfo = {
            cityName: this.levelSelectInfo.levelTwoName,
            cityCode: this.levelSelectInfo.levelTwoCode
          }
          break;
        default:
          break;
      }
    },
    cancel () {
      this.showCityPicker = false
      this.citySelectInfo = { ...this.cityInfo }
      this.levelSelectInfo = { ...this.levelInfo }
    },
    confirm () {
      this.showCityPicker = false
      this.cityInfo = { ...this.citySelectInfo }
      this.levelInfo = { ...this.levelSelectInfo }
    }
  },
}
</script>

<style lang="scss" scoped>
.btn {
  width: 650px;
  height: 88px;
  text-align: center;
  line-height: 88px;
  font-size: 30px;
  color: $base-blue;
  border-radius: 44px;
  margin: 40px auto;
  border: 1px solid $base-blue;
}
.city-wrap {
  width: 100%;
  height: 1004px;
  background: $color-bg-white;
  border-radius: 16px 16px 0px 0px;
  color: $color-text-black;
  overflow: hidden;
  .color-blue {
    color: $base-blue;
  }
  .top {
    position: relative;
    width: 100%;
    height: 88px;
    line-height: 88px;
    text-align: center;
    color: $color-text-black;
    .cancel,
    .confirm {
      min-width: 65px;
      max-width: 75px;
      margin: 0 32px;
    }
    &:before {
      @include half-px-line;
    }
  }
  .city-select {
    position: relative;
    width: 100%;
    padding: 20px 72px;
    text-align: left;
    &:before {
      @include half-px-line;
    }
    .pick:not(:first-child) {
      padding-left: 72px;
    }
    .pick {
      line-height: 48px;
    }
  }
  .city-list {
    width: 100%;
    height: 780px;
    margin-top: 28px;
    padding-left: 70px;
    padding-right: 36px;
    overflow-x: hidden;
    overflow-y: auto;
    box-sizing: border-box;
    .city-name {
      line-height: 40px;
      padding-bottom: 60px;
      &:first-child {
        margin-top: 20px;
      }
    }
    img {
      float: right;
      width: 38px;
      height: 26px;
      margin-left: 20px;
    }
  }
}
</style>