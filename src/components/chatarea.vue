<template>
    <div>
        <!-- Chat header -->
        <section class="hdr">
            <div :class="{active:IsActive}">
                <div class="perimg">
                    <img v-bind:src="imgsrc" alt="img" />
                </div>
                <span class="activity"></span>
            </div>
            <div class="pertxt">
                <div>
                    <p>{{ full_name }}</p>
                    <p>{{statususer}}</p>
                </div>
                <div class="menu">
                    <img src="../assets/imgs/add.svg" />
                </div>
            </div>
        </section>
        <!-- Chat meaages -->
        <section class="chat" id="chat">
            <div v-for="(message,index) in messages" :key="index" :class="getMessageContainerClass(message)">
                <div class="perimg">
                    <img v-bind:src="ImgChatSrc"  alt="img" />
                </div>
                <div :class="getMessagebodyClass(message)">
                    <p><span>{{message.body}}</span><span class="date">{{ msgtime }}</span></p>
                </div>
            </div>
        </section>
        <!-- Chat writing -->
        <section class="writing">
           <div class="img">
                <img src="../assets/imgs/attach.svg" alt="write">
            </div>
            <input id="scrolldown" v-model="message"  @keyup.enter="sendMessage()" placeholder="type message">
            <button @click="sendMessage" ref="sendele" >
                <img src="../assets/imgs/send.svg" alt="send"></button>
        </section>
    </div>
</template>
<script>
import {conversations,users} from '../assets/static_data';
export default { 

  data: function() {
    return {
        msg2:String,
        date_msg2:String,
        active_chat_user_id:2,
        messages:[],
        selected_user:{},
        full_name:"",
        message:"",
        statususer:"",
        imgsrc:"",
        msgtime:"",
        IsActive:false,
        ImgChatSrc:"",
        newMsgcount:0
      };
  },
  mounted(){
      this.changeActiveUser(2) //change friends in header
      
  },
  methods:{
    gettime(){
            //get time of message
        const today = new Date();
        const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();                
        this.msgtime = time;
    },
    // get chat of friends
    refrechMessages(user_id){
        //reset count of messages
        this.newMsgcount=0
        this.gettime()
        this.messages=conversations.filter(function(conversation){
            return conversation.with_user_id == user_id
        })[0].messages

    },
    //change friends in header
    changeActiveUser(user_id){
        this.refrechMessages(user_id)
        this.selected_user=users.filter(function(user){
            return user.id == user_id
        })[0]
        this.full_name=this.selected_user.full_name
        this.active_chat_user_id=this.selected_user.id
        this.statususer=this.selected_user.status
         this.imgsrc=this.selected_user.img_src
         // Activity of friends in the header of the chat
        if(this.selected_user.state==="active"){
            this.IsActive=true
        }
        else{
              this.IsActive=false 
        }
    },
    //get my messaga and friend's meaage
    getMessageContainerClass(message){
      let class_name
        if(message.sender_user_id == 1){
            class_name="msg2"      
        }else{
           class_name="msg1"
        this.ImgChatSrc=this.imgsrc
        }
        return class_name
    },
    getMessagebodyClass(message){
        const class_name=message.sender_user_id == 1 ? "mr_right10" : "mr_left10"
        return class_name 
    },
    sendMessage(){
      if(this.message.match(/^ *$/) == null){
  this.messages.push({
            sender_user_id:1,
            body:this.message.trim()
        })
        }
        //new message move to top in friends chat
        this.$emit('change_chat_order',this.active_chat_user_id)
        // increase message count
        this.newMsgcount++
        //parse data to friends component
        this.$root.$emit('newMessage', this.newMsgcount);
         
        this.message=""
        this.scrollToEnd();

    },
    scrollToEnd: function() {    	
      var container = this.$el.querySelector("#chat");
       var e = container.offsetHeight + 40
       container.scrollTo(0, e)
       
     
    }
  }
};
</script>
<style scoped lang="scss">
//variables 
$mediaqueryWth:900px;
$maincolor:#6979F8;
$gryTxtClr:#030304;
$whiteTxtClr:#F4F6FF;
$gryBgClr:#ECEBED;
$activeClr:#8AC696;
$msg1DateClr:#F2F3FE;
$msg2DateClr:#B8B8B8;
$statusClr:#5A4F6D;
$borderradius:9px;
// Custom
p{
    margin: 0px
}
.mr_right10{
    margin-right: 10px
}
.mr_left10{
    margin-left:  10px
}
.mr_40{
    margin-bottom: 40px
}
//start
//chat header
.hdr {
    display: flex;
    width: 100%;
    background-color: white;
    position: relative;
    border-top-right-radius: $borderradius;
    box-shadow: -3px 1px 2px #eeeeee;
    padding: 15px 30px;
    & > div:first-child {
        position: relative;
        span.activity {
            width: 10px;
            border: 1px solid white;
            height: 10px;
            border-radius: 50%;
            background-color: gray;
            position: absolute;
            top: 41px;
            left: 42px;
        }
    }
    .pertxt {
    color: $gryTxtClr;
    font-weight: bold;
    display: flex;
    justify-content: space-between;
    width: 100%;
    div:first-child p:nth-of-type(2) {
            font-size: 12px;
            color: $statusClr;
            font-weight: normal;      
    }
    div:last-child {
        img {
            width:20px {
            }
        }
    }
}
}
.perimg {
    border-radius: 50%;
    min-width: 54px;
    width: 54px;
    height: 54px;
    overflow: hidden;
    margin-right: 10px;
    img {
        width: 57px;
        object-fit: cover;
        height: 57px;
    }
}

.active {
    span {
        background-color: #57d80e !important;
    }
}

.activity {
    p {
        color: $activeClr;
        font-size: 14px;
    }
    span {
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background-color: $activeClr;
        display: inline-block;
        margin-right: 2px;
    }
}
@media screen and (max-width:$mediaqueryWth) {
    .hdr {
        padding: 8px 10px 12px 90px;
    }
}
//chat area
.chat {
    padding:0px 20px 40px 20px;
    background-color: white;
    height: 100%;
    overflow-y: scroll;
    max-height: 59.855vh;
    transition: all 0.5s ease-in-out
}
.msg1, .msg2 {
    display: flex;
    border-radius: 7px;
    width: 100%;
    transition: all 0.4s ease-in-out;
    span.date {
        font-size: 11px;
        font-weight: normal;
    }
}
.msg1 p, .msg2 p {
    max-width: 70%;
    padding: 10px;
    margin-bottom: 10px;
    border-radius: 12px;
    word-break: break-all;
    min-width: 87px;
    &:first-child {
        position: relative;
        &::before {
            position: absolute;
            content: "";
            display: block;
            z-index: 2;
            width: 25px;
            height: 25px;
            top: -7px;
            bottom: -1px;
            border-radius: 6px;
            transform: rotate(64deg);
            clip-path: polygon(50% 0%,0 100%,66% 75%);
        }
    }
}
.msg1 p:first-child::before {
    background-color: $maincolor;
}
.msg2 p:first-child::before {
    background-color: $gryBgClr;
}
.msg2 {
    float: right;
    text-align: right;
    justify-content: flex-end;
    p {
        background-color: $gryBgClr;
        color: $gryTxtClr;
        float: right;
        margin-left: 25%;
        & span.date {
            text-align: left;
            margin-top: 4px;
            float: left;
            margin-right: 10px;
            color: $msg2DateClr;
        }
    }
    .perimg {
        order: 3;
        display: none;
        img {
            display: none;
        }
    }
    .mr_right10 {
        margin-right: 10px;
        order: 1;
    }
}
.msg1 {
    float: left;
    justify-content: flex-start;
    p {
        background-color: $maincolor;
        color: $whiteTxtClr;
        margin-right: 25%;
        span.date {
            margin-left: 10px;
            margin-top: 4px;
        }
        span.date {
            float: right;
            color: $msg1DateClr;
        }
    }
}
.msg1 p:first-child::before {
    left: -5px;
}
.msg2 p:first-child::before {
    right: -12px;
}
// sending
.writing {
    display: grid;
    grid-template-columns: 1fr 10fr 1fr;
    padding: 10px 0px;
    background-color: #eff0f1;
    border: 1px solid #ededed;
    border-bottom-right-radius: $borderradius;
    .img {
        padding: 18px 10px;
        display: inline-block;
        img {
            width: 20px;
            transform: rotate(6deg);
            display: block;
            margin: 0 auto;
        }
    }
    input {
        caret-color: #6979f8;
        border: none;
        padding: 24px;
        border-radius: 76px;
        height: 55px;
        border: 1px solid #e8e8e8;
        margin-top: 10px;
    }
    button {
        background-color: transparent;
        border: none;
        padding: 7px;
        transition: all 0.4s ease;
        img {
            width: 20px;
            transition: all 0.4s ease;
            &:hover {
                opacity: 0.6;
            }
        }
    }
}
@media screen and (max-width:900px) {
    .writing {
        grid-template-columns: 1fr 4fr 1fr;
    }
}
// in mobile view
img#mob_open {
    width: 14px;
    display: block;
}
@media screen and (max-width:900px) {
    img#mob_open {
        display: block;
        margin-bottom: 35px;
        margin-right: 20px;
    }
    .hdr {
        display: flex;
    }
}
</style>