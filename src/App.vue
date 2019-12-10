<template>
<div id="app_page">
<b-container >
  <b-row>
 
<div id="mob_btn">
  <button @click="mobmenuClicked"><FontAwesomeIcon :icon="['fas', 'bars']" /></button>
</div>
 <Friends  ref="friends" class="friends" id="friendsarea" @friend_selected="refrechChat"  ></Friends>
 <Chatarea   id="chatarea" ref="chatarea" @change_chat_order="changeOrder" ></Chatarea>
 </b-row>
  </b-container>
   </div>
</template>

<script>
import Friends from "./components/friends.vue"
import Chatarea from "./components/chatarea.vue"
import { library } from '@fortawesome/fontawesome-svg-core'
import { fab } from '@fortawesome/free-brands-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import brands from "@fortawesome/fontawesome-free-brands";
import solid from "@fortawesome/fontawesome-free-solid";


 

export default {
  name: "app",
  components: {
    Friends,
    Chatarea,
    FontAwesomeIcon
  },
  data:function(){
    return {   
    }
  },
  methods:{
    // to change active chat window with selected user
    refrechChat(user_id){
      this.$refs.chatarea.changeActiveUser(user_id)
    },
    // to change order of friends list
    changeOrder(user_id){
      this.$refs.friends.changeOrder(user_id)
    },
    mobmenuClicked(){
      var friendDiv=document.getElementById("friendsarea")
      
      if(friendDiv.style.left == "0px"){
      friendDiv.style.left = "-720px";

      }else{   
        friendDiv.style.left = "0px";

      }
    }
  }
};
</script>

<style lang="scss">
//variables
$mediaqueryWth:900px;
$maincolor:#6979F8;
$borderradius:9px;
//reset style
input:focus {
  outline: none;
}

img {
  max-width: 100%;
  height: auto;
}

* {
  margin: 0px;
  padding: 0px;
  box-sizing: border-box;
}

*:focus {
  outline: none !important;
}

::-webkit-scrollbar {
  width: 4px;
}

::-webkit-scrollbar-track {
  background: #f5f5f5;
}

::-webkit-scrollbar-thumb {
  background: #c3c3c4;
  ;
    width: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #555;
}

@font-face {
  font-family: Roboto;
  src:url(./assets/fonts/Roboto/Roboto-Regular.ttf);
}
//start
#app_page {
  background-color: $maincolor;
  min-height: 100vh;
  padding: 20px;
  display: flex;
  align-items: center;
  font-family: Roboto;
}

#chatarea {
  width: 62%;
  height: 100%;
}

#friendsarea {
  width: 38%;
  background-color: white;
  border-top-left-radius: $borderradius;
  border-left: 3px solid #F1F1F1;
  border-right: 3px solid #F1F1F1;
  max-height: 88.5vh;
  border-bottom-left-radius: $borderradius;
}

#mob_btn {
  display: none;
}

@media screen and (max-width:$mediaqueryWth) {
  #chatarea {
    width: 100%;
  }

  #mob_btn {
    width: 1%;
    display: block;
    z-index: 2;
    position: absolute;
    left: 30px;
    top: 15px;
    button{
          padding: 1px 6px 0px;
    background: #6979f8;
    border: none;
    color: #ffffff;
    font-size: 18px;
    border-radius: 3px;
  }
}

#app_page > .container > .row {
  overflow: hidden;
  display: flex;
  justify-content: center;
  position: relative;
  #friendsarea{
  position: absolute;
  z-index: 4;
  left: -720px;
  width: 100%;
  transition: all 0.5s ease-in-out;
  border-right: 3px solid #9f9e9e;
  height: 88vh;
  background-color: #fbfbfb;
}
}
}



</style>