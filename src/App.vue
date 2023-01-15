<template>
  <div id="app">
    <header>
      <h1>Music</h1>
    </header>
    <main>
      <section class="player">
        <h2 class="song-title">{{ current.title }}</h2>
        <h2 class="title-artist">{{ current.artist }}</h2>
        <img v-bind:src="current.cover" class="song-img"/>
        <!-- {{ songs[0].src }} -->
        <div class="progressbox">
            <input class="progressbar" type="range" value="0" />
        </div>
        <div class="time">
          {{ timeLabel }}
        </div>
        <div class="control">
          <button class="prev" v-on:click="prev"><img src="./assets/prev.svg"/></button>
          <button class="play" v-if="!isPlaying" v-on:click="play"><img src="./assets/play.svg"/></button>
          <button class="pause" v-else v-on:click="pause"><img src="./assets/pause.svg"/></button>
          <button class="next" v-on:click="next"><img src="./assets/next.svg"/></button>
        </div>
        <div class="volumebox">
          <img class="low-vol" src="./assets/low-vol.svg" v-on:click="volLow"/>
          <input class="volumebar" type="range" value="75"/>
          <img class="large-vol" src="./assets/large-vol.svg" v-on:click="volLarge"/>
        </div>
      </section>
      <section class="playlist">
        <h3>PlayList</h3>
        <button v-for="song in songs" :key="song.src" v-on:click="play(song)"
                      :class="(song.src==current.src)?'song playing':'song'">
                      <div class="playlist-songs">
                        <img class="song-img-cover" :key="song.cover" v-bind:src="song.cover"/>
                        <div class="title-artist">
                          <h3>{{ song.title }}</h3>
                          <h4 class="song-artist">{{ song.artist }}</h4>
                        </div>
                      </div>
        </button>
      </section>
    </main>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return{
      current :{

      },
      index: 0,
      time: 0,
      isPlaying: false,
      timeLabel: '00:00/01:43',
      songs:[
        {
          title: '雪風',
          artist: '植松伸夫',
          src: require('./assets/雪風.mp3'),
          cover: require('./assets/Cover3.webp')
        },
        {
          title: 'Crimson Sunset',
          artist: '植松伸夫',
          src: require('./assets/紅の夜更け ～クガネ：夜～.mp3'),
          cover: require('./assets/Cover4.jpg')
        },
        {
          title: 'Who Brings Shadow',
          artist: '祖堅正慶',
          src: require('./assets/Who Brings Shadow.mp3'),
          cover: require('./assets/Cover5.jpg')
        },
        {
          title: 'With Hearts Aligned',
          artist: '祖堅正慶',
          src: require('./assets/想いが動かす力.mp3'),
          cover: require('./assets/Cover6.png')
        }
      ],
      player: new Audio()
    }
    
  },
  methods:{
    play(song){
      if(typeof song.src != 'undefined'){
        this.current = song;
        this.player.src = this.current.src;
      }
      this.player.play();
      this.player.addEventListener('ended',function(){
        this.index++;
        if(this.index>this.songs.length-1){
          this.index = 0;
        }
        this.current = this.songs[this.index];
        this.play(this.current);
      }.bind(this));
      this.isPlaying = true;
    },
    pause(){
      this.player.pause();
        console.log(this.player.currentTime);
      this.isPlaying = false;
    },
    prev(){
      this.index--;
      if(this.index < 0){
        this.index = this.songs.length-1;
      }
      this.current = this.songs[this.index];
      this.play(this.current);
    },
    next(){
      this.index++;
      if(this.index > this.songs.length-1){
        this.index = 0
      }
      this.current = this.songs[this.index];
      this.play(this.current);
    },
    timeupdate(){
      function Timeformat(ts) {
          let min = parseInt(ts / 60);
          let sec = parseInt(ts % 60);
          min = min < 10 ? "0" + min : min;
          sec = sec < 10 ? "0" + sec : sec;
          return min + ":" + sec;
      }
      let progressbar = document.querySelector('.progressbar');
      this.timeLabel = Timeformat(this.player.currentTime) + "/" + Timeformat(this.player.duration);
      progressbar.max = this.player.duration;
      progressbar.value = this.player.currentTime;
      progressbar.addEventListener("input", ()=>{
        this.player.currentTime = progressbar.value;
      });

      let volumebar = document.querySelector('.volumebar');
      volumebar.value = this.player.volume * 100;
      volumebar.addEventListener('input',()=>{
        this.player.volume = volumebar.value / 100;
      });
    },
    volLow(){
      if((this.player.volume-0.05)>0)
        this.player.volume -= 0.05;
    },
    volLarge(){
      if((this.player.volume+0.05)<1)
        this.player.volume += 0.05;
    }
  }
  ,
  created(){
    this.current = this.songs[this.index];
    this.player.src = this.current.src;
    this.player.volume = 0.75;
    // this.player.play();
  },
  mounted(){
    this.player.addEventListener('timeupdate',this.timeupdate,false)
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=M+PLUS+Rounded+1c:wght@300&display=swap');
  *{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  body{
    font-family: 'M PLUS Rounded 1c', sans-serif;
    background: linear-gradient(to right,#000,rgb(26,26,26,0.9),#000);
    color:#fff;
  }
  header{
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 15px;
    background-color: #000;
  }
  main{
    width: 100%;
    margin: 0 auto;
    padding: 15px;
  }
  .song-title{
    color: #fff;
    font-size: 2rem;
    font-weight: 800;
    text-align: center;
    padding: 15px;
  }
  .title-artist{
    font-weight: 100;
    color: rgb(192,192,192);
    text-align: center;
  }
  .song-img{
    width: 350px;
    object-fit: cover;
    display: block;
    margin: auto;
    padding: 15px;
    border-radius: 25px;
  }
  .time{
    display: flex;
    justify-content: center;
    padding-bottom: 15px;
  }
  .control{
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 15px;
  }
  button{
    appearance: none;
    background: none;
    border: none;
    outline: none;
    cursor: pointer;
    color: rgb(192,192,192);
    font-family: 'M PLUS Rounded 1c', sans-serif;
  }
  .progressbox,
  .volumebox{
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 15px;
  }
  .progressbar{
    width: 300px;
    height: 5px;
    appearance: none;
    overflow: hidden;
    border-radius: 15px;
  }
    .progressbar::-webkit-slider-thumb{
      appearance: none;
      width: 20px;
      height: 5px;
      border-radius: 15px;
      background: rgb(150,150,150);
      transition: 0.25s;
      cursor: pointer;
      box-shadow: -100vw 0 0 100vw rgb(150,150,150);
    }
      .progressbar::-webkit-slider-thumb:hover{
        background: rgb(50,50,50);
      }
  .volumebar{
    width: 200px;
    height: 5px;
    appearance: none;
    overflow: hidden;
    border-radius: 15px;
  }
    .volumebar::-webkit-slider-thumb{
      appearance: none;
      width: 20px;
      height: 5px;
      border-radius: 15px;
      background: rgb(150,150,150);
      transition: 0.25s;
      cursor: pointer;
      box-shadow: -100vw 0 0 100vw rgb(150,150,150);
    }
      .volumebar::-webkit-slider-thumb:hover{
        background: rgb(50,50,50);
      }
  .volumebox img{
    widows: 25px;
    height: 25px;
    margin: 0 25px;
    cursor: pointer;
    transition: 0.25s;
  }
    .volumebox img:hover{
      opacity: 0.75;
    }
  .play img,.pause img,.prev img,.next img{
    width: 50px;
    height: 50px;
    fill: #fff
  }
  .play, .pause{
    margin: 0 15px;
    border-radius: 50%;
    transition: 0.5s;
  }
    .play:hover, .pause:hover{
      opacity: 0.3;
    }
  .prev, .next{
    margin: 0 15px;
    border-radius: 50%;
    transition: 0.5s;
  }
    .prev:hover, .next:hover{
      opacity: 0.3;
    }
  .playlist{
    padding: 0 25px;
  }
  .playlist-songs{
    display: flex;
    width: 66%;
    justify-content: baseline;
    position: relative;
    left: 33%;
  }
  .playlist h3{
    font-size: 1.25rem;
    font-weight: 800;
    margin: 15px;
    padding: 15px;
    text-align: center;
  }
  .song-img-cover{
    width: 100px;
    object-fit: cover;
    border-radius: 15px;
  }
  .song-artist{
    color: rgb(192,192,192);
    display: flex;
    justify-content: left;
    padding-left: 2rem;
  }
  .playlist .song{
    width: 100%;
    display: block;
    padding: 15px;
    font-weight: 700;
    margin: 0 auto;
    border-radius: 15px;
    color: rgb(192,192,192);
    transition: 0.25s;
  }
      .playlist .song:hover .song-artist,
      .playlist .song:hover .title-artist h3{
        color: #fff;
      }
      .playlist .song:hover .song-img-cover{
        opacity: 0.75;
      }
  .song.playing{
    background: linear-gradient(to right,#000,rgb(26,26,26,0.5),#000);
  }
  @media (max-width:660px) {
    .playlist-songs{
      left: 0;
      width: 100%;
    }
  }
</style>
