<template>
  <div class="city-control">
    <input
      v-model="inputValue"
      @click="showModule"
      name="city"
      type="text"
      class="insetInput"
      placeholder="请选择城市"
      :value="addCityName"
      @keyup.down="down()"
    />

    <!--城市列表模块-->
    <div id="cityModule" class="city-master" v-show="isShowCityModule">

      <div class="city-choose-module">
        <p>直接输入可搜索城市（支持汉字/拼音）</p>
        <ul class="city-choose-navar">
          <li v-for="(tab,index) in navTabs" @click="selectTab(index)" :class='{active:index==active}'>{{tab}}</li>
        </ul>
        <ul class="city-choose-main">
          <li v-for="navTab in tabsMe()">
            <!--{{navTab}}-->
            <div class="city-choose-keyword" v-for="item in tabsList(navTab)">
              <em v-show="isShowEmWord">{{item}}</em>
              <span v-for="city in listMe(item)" @click="addStr(city)">{{city.name}}</span>
            </div>
          </li>
        </ul>
      </div>

    </div>

    <!-- 城市搜索模块 -->
    <div class="searchListModule" v-show="isShowSearchModule">
      <p>请输入/拼音/三字码，用↑↓选择</p>
      <ul @keyup.down="down()">
        <li v-for="item in searchList" @click="addStr(item)">
          <span>{{item.name}}</span>
          <span>{{item.code}}</span>
          <span>{{item.eName}}</span>
        </li>
      </ul>
      <div v-show="noResult" class="noResultDiv">对不起，不支持该目的地</div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      reg: /^[A-Za-z]+$/,
      active: '0',
      inputValue : '',
      tabIndex: '0',
      isShowEmWord: true,
      isShowCityModule: false,
      isShowSearchModule: false,
      noResult: false,
      searchList:[],
      mainPortList:[],
      navTabs:['热门城市','ABCDE','FGHJ','KLMNP','QRSTW','XYZ'],
      protList:['热门城市','A','B','C','D','E','F','G','H','J', 'K','L','M','N','P','Q','R','S','T','W','X','Y','Z'],
      hotCity: this.$DEFAULTVALUE,
      allCity: this.$AIRPORTDATA,
    }
  },
  created:function () {
    for(var i=0,j=this.protList.length; i < j; i++){
      this.mainPortList[this.protList[i]] = []
      if(i==0){
          for(var l=0;l<this.hotCity.length;l++){
            this.mainPortList[this.protList[i]].push(this.hotCity[l]);
          }
      }else {
        for(var k=0;k<this.allCity.length;k++){
          if(this.allCity[k].eName.charAt(0) === this.protList[i]){
            this.mainPortList[this.protList[i]].push(this.allCity[k]);
          }
        }
      }
    }
    console.log(this.mainPortList);
  },
  computed: {
      isOpen (){
          return this.isShowCityModule || this.isShowSearchModule
      }
  },
  watch: {
    inputValue(curVal){
      this.filters(curVal)
    },
    tabIndex(curVal){
      return this.tabIndex
    }
  },
  methods:{
    selectTab(opt){
      this.active = opt
      this.tabIndex = opt
    },
    tabsMe(){
      return this.navTabs[this.tabIndex]
    },
    tabsList(opt){
      for(var i=0; i< this.protList.length;i++){
        if(this.protList[i] == opt){
          this.isShowEmWord = true
          return this.protList[i]
        }else if( opt == '热'){
          this.isShowEmWord = false
          return opt = '0'
        }
      }
    },
    listMe(opt){
      if(!this.reg.test(opt)){
        return this.hotCity
      }else {
        return this.mainPortList[opt]
      }
    },
    showModule() {
      if(this.isOpen){
        this.close()
      }else {
        this.isShowCityModule = true
      }
      document.addEventListener('click', this.clickOutside, false)
    },
    clickOutside () {
      if (this.$el && !this.$el.contains(event.target)) {
        this.close()
        document.removeEventListener('click', this.clickOutside, false)
      }
    },
    close(){
      this.isShowCityModule = this.isShowSearchModule  = false
      document.removeEventListener('click', this.clickOutside, false)
    },
    addStr(opt){
      this.searchList = []
      this.inputValue = opt.name
      if(this.isOpen){
        this.close()
      }
      document.removeEventListener('click', this.clickOutside, false)
    },
    filters(opt){
      console.log('154'+opt);
      this.searchList = []
      opt = opt.toUpperCase()
      for(var i=0;i<this.allCity.length;i++){
        this.allCity[i].eName = this.allCity[i].eName.toUpperCase()
        if(opt.length == this.allCity[i].name.length && opt == this.allCity[i].name ){
          this.close()
          return
        }
        if(this.allCity[i].name.indexOf(opt) >= 0 || this.allCity[i].code.indexOf(opt) >= 0 ||
          this.allCity[i].eName.indexOf(opt) >= 0 || this.allCity[i].szm.indexOf(opt) >= 0){
            this.searchList.push(this.allCity[i])
            this.close()
            this.noResult = false
            this.isShowSearchModule = true
        }
      }
      console.log(this.searchList);
      if(this.searchList.length == 0){
          this.noResult = true
      }
      document.addEventListener('click', this.clickOutside, false)
    },
    down(){
        console.log('11')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 { font-weight: normal;}
ul {list-style-type: none;}
li {display: inline-block;}
a {color: #42b983;}
.city-control {width: 420px;margin: 0 auto;position: relative;text-align: left;}
.insetInput {width: 192px;height: 28px;font-size: 12px;line-height: 28px;text-indent: 12px;outline:none;}
.city-master {width: 420px;margin: 0 auto;background-color: #ededed;box-shadow: 2px 2px 2px #888888;z-index: 1000;}
.city-choose-module {font-size: 12px;}
.city-choose-module p  {color: #999;text-align: left;margin: 0 19px 4px;padding-top: 8px;}
.city-choose-navar {font-size: 14px;color: #0084bb;text-align:center;padding: 10px 0 0;margin: 0 auto;border-bottom: 1px solid #e8e8e8;}
.city-choose-navar li {width:64px;height: 24px;cursor: pointer;}
.city-choose-navar li.active {border-bottom: 1px solid #0084bb;font-weight: bold;}
.city-choose-main{width: 384px;margin: 0 auto;padding: 10px 0;font-size: 14px;text-align: left;}
.city-choose-keyword {position: relative;}
.city-choose-keyword > span {display: inline-block;width: 72px;line-height: 25px;font-size: 12px;cursor: pointer;text-align: center;color: #666;}
.city-choose-keyword > span:hover {font-weight: bold;color: #0084bb;}
.city-choose-keyword > em {display: block;position: absolute;top: 6px;left: -6px;font-style: normal;font-size: 14px;color: #0084bb;}
.searchListModule {width: 260px;margin: 0 auto;background-color: #ededed;box-shadow: 2px 2px 2px #888888;position: absolute;top: 36px;z-index: 1000;}
.searchListModule > p {font-size: 12px;color: #fff;line-height: 26px;margin: 0;text-indent: 12px;background-color:  #0084bb;}
.searchListModule > ul {width: 260px;max-height:360px;margin: 0; padding: 0; overflow-x: hidden;overflow-y: scroll;}
.searchListModule > ul > li {width: 260px;line-height: 26px;font-size: 12px;cursor: pointer;text-align: left;text-indent: 12px;margin: 0;color: #666;}
.searchListModule > ul > li:hover {color: #fff;background-color:  #0084bb;}
.searchListModule > ul > li span:last-child {float: right;margin-right: 20px;}
.noResultDiv {width: 260px;line-height: 26px;font-size: 12px;cursor: pointer;text-align: left;text-indent: 12px;margin: 0;color: #666;}
</style>
