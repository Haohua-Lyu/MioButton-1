<template>
    <div class="container-fluid" >
        <div>
            <div class="btn-body-status">
                <div>{{ voice.name ? $t("action.playing") + $t("voice." + voice.name ) : $t("action.noplay") }}</div>
                <audio id="player" @ended="voiceEnd(false)"></audio>
            </div>
            <button @click="control" class="btn btn-control-main" style="right:15px;bottom:15px;"><img id="img" src="/resources/menu.svg" style="margin: 0px 9px;width:30px"></button>
            <div id="btn" style="display:none;">
                <button id="small" class="btn btn-control" style="right:78px;bottom:15px;" @click="stopPlay"><img src="/resources/stop.svg" style="width: 30px;"></button>
                <button id="small" class="btn btn-control" style="right:123px;bottom:15px;" @click="random"><img src="/resources/choose.svg" style="width: 30px;"></button>
                <button id="small" class="btn btn-control" style="right:168px;bottom:15px;padding:5.5px;" :class="{ 'disabled': autoCheck }" @click="overlap" :title="$t('info.overlapTips')"><input class="checkbox" type="checkbox" v-model="overlapCheck"><img src="/resources/over.svg" style="width: 25px;"></button>
                <button id="small" class="btn btn-control" style="right:226px;bottom:15px;" :class="{ 'disabled': overlapCheck }" @click="autoPlay"><input class="checkbox" type="checkbox" v-model="autoCheck"><img src="/resources/auto.svg" style="width: 30px;"></button>
            </div>
            <div class="title">{{$t("info.title")}}<img src="/resources/bg.png" style="width:40px;height:auto;margin-left:5px;margin-bottom: 3px;"></div>
                <div class="cate-ctrldft">{{$t("action.live")}}
                    <div v-for="(item) in youtubeData.channels" :key="item.yt_channel_id"><button class="btn btn-ctrldft" v-if="item.yt_channel_id === 'UCp-5t9SrOQwXMU7iIjQfARg'">{{$t('info.subscriber')}}{{item.subscriber_count}}</button></div>
                    <div v-for="live in live_data" :key="live.live_schedule">
                        <div v-if="live.title.length">
                            <span v-if="live.status === 'upcoming'" style="font-size:17px;">{{$t("action.plan")}}{{new Date(live.live_schedule).toLocaleString()}}</span>
                            <span v-if="live.status === 'live'" class="warning--text" style="font-size:17px;">{{$t("action.ing")}}</span>
                            <button class="btn btn-ctrldft"><a 
                                :href="'https://www.youtube.com/watch?v=' + live.yt_video_key"
                                target="_blank"
                                style="text-decoration: none;color: #ffffff;"
                                :class="live.status === 'live' ? 'error--text' : ''"
                            >
                              {{live.title}}
                            </a></button>
                        </div>
                    </div>
                </div>
                <div class="cate-ctrldft">{{$t("action.random")}}
                        <input id="share" class="btn btn-ctrldft" style="width: 190px;-webkit-user-select:text !important;" type="text" name="u" value :placeholder="$t('action.placeholder')">
                        <button class="btn btn-ctrldft" @click="randomshare">{{$t("action.share")}}</button>
                </div>
                <div class="cate-ctrldft">{{$t("action.adtitle")}}
                    <button class="btn btn-ctrldft" onclick="window.open('https://www.bilibili.com/read/readlist/rl210208')">{{$t("action.weekly")}}</button>
                    <button class="btn btn-ctrldft" onclick="window.open('https://vtbbtn.org')">{{$t("action.vtbbtn")}}</button>
                    <button class="btn btn-ctrldft" onclick="window.open('https://cute.ookamimio.org')">{{$t("action.sticker")}}</button>
                    <button class="btn btn-ctrldft" onclick="window.open('https://ookamimio.org/resources/voice.rar')">{{$t("action.voicepack")}}</button>
                    <button class="btn btn-ctrldft" onclick="window.open('https://ookamimio.org/resources/fubumio.png')">{{$t("action.AvatarFrame")}}</button>
                </div>
                <div class="cate-ctrldft"><a @click="cate()" style="text-decoration:none;color:#fff;"><img id="imgcate" src="/resources/down.svg" style="width:35px;margin-left:-8px;">{{$t("action.choose")}}</a><br>
                    <div id="cate" style="display:none;"><button style="margin-top:2.5px;margin-bottom:2.5px;" class="btn btn-ctrldft" v-for="category in voices" v-bind:key="category.categoryName">
                        <a style="text-decoration:none;color:#fff;" :href="$t('#' + category.categoryName)">{{$t("voicecategory." + category.categoryName)}}</a>
                    </button></div>
                </div>
            <div v-for="category in voices" v-bind:key="category.categoryName">
                <div class="cate-header" :id="$t(category.categoryName)">{{ $t("voicecategory." + category.categoryName) }} 
                    <div class="cate-body">
                        <button class="btn btn-new" v-for="voiceItem in category.voiceList" v-bind:key="voiceItem.name" @click="play(voiceItem)">{{ $t("voice." + voiceItem.name )}}</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.title{
    text-align: left;
    margin-top: 75px;
    border-radius: 30px;
    font-weight: 900;
    text-align: left;
    color: #fff;    
    text-shadow:rgba(0, 0, 0, 0.308) 5px 6px 10px;
    font-size: 35px;
    padding-top: 10px;
    padding-bottom: 30px;
    max-width: max-content;
}
.btn:hover, .btn:active, .btn.focus{
    color: #ffffff;
}
.btn-control-main{
    position: fixed;
    z-index: 2;
    background-color: #ff7b7bc2;/*背景颜色*/
    border: 0px; /*边框去除*/
    border-radius: 17px;/*边框圆角*/
    max-width: 100%;
    padding: 3px;
    transition-property: all;
    transition-duration: 400ms;
    font-weight: 600;
    margin-top: 0px;
    margin-bottom: 0px;
    font-weight: 700;
    font-size: 15px;
    text-align: center;
}
.btn-control-main:hover,.btn-control:focus{
    background-color:#ff7b7be5;    

}
.btn-control{
    z-index: 1;
    position: fixed;
    background-color: #ff7b7bc2;/*背景颜色*/
    border: 0px; /*边框去除*/
    border-radius: 17px;/*边框圆角*/
    max-width: 100%;
    padding: 3px;
    animation: mymove 800ms;
    font-weight: 600;
    margin-top: 0px;
    margin-bottom: 0px;
    font-weight: 700;
    font-size: 15px;
    text-align: center;
}
@keyframes mymove{
    0%{right:15px;}
}
.btn-control:hover,.btn-control:focus{
    background-color:#ff7b7be5
}
.btn-ctrldft{/*今日随机按钮*/
    background:rgb(255, 156, 156) ;
    border: 0px; /*边框去除*/
    border-radius: 17px;/*边框圆角*/
    padding-top: 3px;
    box-shadow: 5px 5px 10px #d16767, 
             -5px -5px 10px #ff9494;
    text-align: center;
    font-weight: 600;
    padding-bottom: 3px;
    margin: 5px;
    transition-property: all;
    transition-duration: 300ms;
    word-wrap: break-word !important;
    word-break: break-all !important;
    white-space: normal !important;
}
.btn-ctrldft:active,.btn-ctrldft:focus{/*宣传中心按钮选定*/
    background-image: linear-gradient(to top left,rgb(255, 184, 184),rgb(255, 156, 156)) ;
    color: rgb(255, 228, 228);
}
.btn-ctrldft:hover{
    box-shadow: 0 0 5px 5px rgba(255, 255, 255, 0.397);
}
.cate-header{/*分类标题*/
    background:#4e4e4e;
    box-shadow: 7px 7px 14px #3a3a3a, 
               -7px -7px 14px #626262;
    border-radius: 30px;
    text-align: left;
    text-shadow:rgba(0, 0, 0, 0.2) 5px 6px 10px;
    transition-property: all;
    transition-duration: 500ms;
    font-weight: 600;
    color: #fff;
    padding-top: 18px;
    padding-left: 20px;
    font-size: 20px;
    margin-top: 12px;
    margin-bottom: 12px;
}
.cate-header:hover{
    background-image: linear-gradient(to right bottom,#464646, #535353);
}
.btn-body-status{/*播放状态分类标题*/
    background-color: #585858d8;
    border-radius: 150px;
    color: #fff;
    text-align: center;
    box-shadow: 0 10px 10px 0px rgba(0, 0, 0, 0.2);
    position: fixed;
    top: 10px;
    font-size: 14px;
    padding: 0px 20px;
    margin-left: -15px;
    margin-right: 15px;
    font-weight: 600;
}
.cate-ctrldft{/*控制中心分类标题*/
    background:#ff7d7d;
    border-radius: 30px;
    text-align: left;
    text-shadow: rgba(184, 88, 88, 0.281) 5px 6px 10px;
    box-shadow: 7px 7px 14px #3a3a3a, 
               -7px -7px 14px #626262;
    color: #fff;
    transition-property: all;
    transition-duration: 300ms;
    font-size: 20px;
    padding-top: 14px;
    padding-left: 17px;
    padding-right: 17px;
    font-weight: 600;
    padding-bottom: 14px;
    margin-bottom: 18px;
    margin-top: 12px;
    max-width: max-content;
}
.cate-ctrldft:hover{
    box-shadow: 0 10px 10px 0px rgba(0, 0, 0, 0.3);
}
.cate-body{
    margin-top: 12px;
    margin-bottom: 20px;
    padding-bottom: 12px;
    margin-right: 12px;
    margin-left: -8px;
    text-align: left;
    color: #aaaaaa;
}
.cate-body button{
    margin: 5px;
}
.btn-new {
    color: #fff;
    background:#ff7c7c;
    box-shadow:4px 4px 10px #343434, 
              -4px -4px 10px #686868;
    border-radius: 15px;
    border: 0px;
    max-width: 100%;
    font-weight: 600;
    transition-property: all;
    transition-duration: 300ms;
    word-wrap: break-word !important;
    word-break: break-all !important;
    white-space: normal !important;
}
.btn-new:active,.btn-new:focus{
    color: rgb(255, 204, 204);
    box-shadow: inset 5px 5px 10px #3c3c3c, 
            inset -5px -5px 10px #606060;
    background:#ff7c7c;      
}
.btn-new:hover{
   box-shadow: 0px 0px 5px 5px rgba(252, 120, 120, 0.322);
}
.checkbox {
    display: inline-block;
    vertical-align: middle;
    margin: 0;
}
</style>


<script>
import Vue from 'vue'
import Component from 'vue-class-component'
import VoiceList from '../voices.json'
import axios from 'axios'

@Component
class HomePage extends Vue {
    live_data = null;
    voices = VoiceList.voices;
    autoCheck = false;
    overlapCheck = false;
    voice = {};
    live_data = {};
    live_data_loading = true;
    youtubeData = {channels: null};
    control(){
        var btn = document.getElementById('btn');
        var img = document.getElementById('img')
            if(btn.style.display == "none"){
                btn.style.display = "";
                img.src = "/resources/close.svg";
            }else{
                btn.style.display = "none";
                img.src = "/resources/menu.svg";
            }
    }
    cate(){
        var cate = document.getElementById('cate');
        var imgcate = document.getElementById('imgcate')
            if(cate.style.display == "none"){
                cate.style.display = "";
                imgcate.src = "/resources/up.svg";
            }else{
                cate.style.display = "none";
                imgcate.src = "/resources/down.svg";
            }
    }
    created() { 
        this.youtube()
    }
    youtube() {
        axios.get('https://api.holotools.app/v1/channels?limit=50')
        .then(response => {
        this.youtubeData = response.data
        })
    }
    mounted() {
        axios.get('https://api.holotools.app/v1/live')
        .then(response => { 
            let fetched = response.data;
            let mio_lives = [];
            const channel_id = 'UCp-5t9SrOQwXMU7iIjQfARg';
            fetched.live.forEach(function(item){
                if (item.channel.yt_channel_id === channel_id){
                    item.status = 'live';
                    mio_lives.push(item);
                }
            });
            fetched.upcoming.forEach(function(item){
                if (item.channel.yt_channel_id === channel_id){
                    item.status = 'upcoming';
                    mio_lives.push(item);
                }
            });
            this.live_data = mio_lives;
            this.live_data_loading = false;
        })
    }
    play(item){
        if (this.overlapCheck) {
            let audio = new Audio("voices/" + item.path);
            this.voice = item;
            audio.play()
        } else {
            this.stopPlay();
            let player = document.getElementById('player');
            player.src = "voices/" + item.path;
            this.voice = item;
            player.play();
        }
    }
    stopPlay(){
        let player = document.getElementById('player');
        player.pause();
        this.voiceEnd(true);
    }
    voiceEnd(flag) {
        if(flag !== true && this.autoCheck) {
            this.random();
        } else {
            this.voice = {};
        }
    }
    random() {
        let tempList = this.voices[this._randomNum(0, this.voices.length - 1)];
        this.play(tempList.voiceList[this._randomNum(0, tempList.voiceList.length - 1)]);
    }
    randomshare() {
        let tempList = this.voices[this._randomNum(0, this.voices.length - 1)];
        var title=this.$t("voice."+ tempList.voiceList[this._randomNum(0, tempList.voiceList.length - 1)].name);
        var res=document.getElementById('share').value;
        if(this.$i18n.locale === 'ja-JP'){
            window.open("https://twitter.com/intent/tweet?text="+"%23ミオボタン %23ミオかわいい 今日、「"+res+"」のランダムオーディオは「"+title+"」です！ より多くのオーディオを聞くには、「ミオボタン」のWebサイトにアクセスしてください~ https://t.co/TLdj8E9Rct");
        }else if(this.$i18n.locale === 'en-US'){
            window.open("https://twitter.com/intent/tweet?text="+"%23ミオボタン %23ミオかわいい Today，"+res+"'s random audio is %22"+title+"%22！ Visit Mio Button Website For More Audio! https://t.co/TLdj8E9Rct");
        }else {
            window.open("https://twitter.com/intent/tweet?text="+"%23ミオボタン %23ミオかわいい 今天，“"+res+"”的随机音频是“"+title+"”！ 访问狼按钮网站聆听更多音频 https://t.co/TLdj8E9Rct");
        }
    }
    autoPlay(){
        if (this.overlapCheck) {
            return;
        }
        this.autoCheck = !this.autoCheck;
    }
    overlap() {
        if (this.autoCheck) {
            return;
        }
        this.overlapCheck = !this.overlapCheck;
    }
    _randomNum(minNum, maxNum) {
        switch(arguments.length) {
            case 1:
                return parseInt(Math.random() * minNum + 1, 10);
            case 2:
                return parseInt(Math.random() * (maxNum - minNum + 1) + minNum, 10);
            default:
                return 0;
        }
    } 
}
export default HomePage;
</script>
