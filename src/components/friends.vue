<template>
  <div id="friendsarea">
    <!-- search container -->
<div class="srch_container">
<img src="../assets/imgs/close.svg"  @click="closeFriends()" alt="close" />
<div id="srch">
  <img src="../assets/imgs/search.svg" alt="search" />
      <input  placeholder="search" v-on:keyup="searchFriends()" ref="searchFriends">
      </div> 
   </div>
<!-- friend list -->
  <div id="friend_list">
  <section v-for="(friend,index) in friends" :id="'friend_'+friend.id" :key="friend.id" :class="getFocusedClass(friend,index)" @click="selectFriend(friend.id)" refs="person">
    <div class="person" >
      <div class="perimg">
        <img v-bind:src="friend.img_src" alt="img" />   
      </div>
          <span></span>
      <div class="txt">
        <p>{{ friend.full_name }}</p>
        <p>{{ friend.status }}</p>
      </div>
    </div>
      <div class="right_info">
        <span v-show="friend.unseen_msg" class="num_unseen">{{ friend.unseen_msg }}</span>
        <span v-if="newMsgcount" class="newMsgcount" >{{newMsgcount}}</span>

      <span class="date">{{ friend.date }}
      </span>
      </div> 
</section>
     <!-- When not found in search -->
      <div id="not_found" v-show="friends.length==0">
        <p>No results...</p>
      </div>
  </div>
  </div>
</template>
<script>
import {users} from '../assets/static_data';
export default {
    data: function() {
        return {
            friends: [],
            friends_srch: users,
            Not_found_person: false,
            newMsgcount:Number=0
        };
    },
    mounted() {
        //get friends list
        if (this.friends.length < 1) {
            this.friends = this.friends.concat(this.friends_srch)
        }
    
    this.$root.$on('newMessage', data => {
        this.newMsgcount=data
    });

    }
 ,
    methods: {
        //search Friends
        searchFriends() {
            var txtInput = this.$refs.searchFriends.value.trim().toLowerCase()
            this.friends = this.friends_srch.filter((ele) => {
                if (ele.full_name.trim().toLowerCase().startsWith(txtInput) === true) {
                    return ele
                }

            })
            return
        },
        // give classes to sections of friends
        getFocusedClass(user, index) {
            var classes_names  
            
            if(index == 0 ){
              classes_names="focus"
            }else
            {
           classes_names=""
            }
            classes_names += " " + user.state
            return classes_names
        },
        // focus on one message
        selectFriend(user_id) {
            this.changeSelectedChatStyle(user_id)
            this.$emit("friend_selected", user_id)
            // remove accounting of unread message
            this.newMsgcount=0
            // for mobile view 
            document.getElementById("friendsarea").style.left = "-720px"
           
        },
        //add class focus and unseen to selected section
        changeSelectedChatStyle(user_id) {
            var friend_section = document.getElementById("friend_" + user_id);
            var focusedClassesDoms = document.getElementsByClassName("focus");
            focusedClassesDoms.forEach(element => {
                element.classList.remove("focus");
            });
             friend_section.classList.remove("unseen");
            friend_section.classList.add("focus");
        },
        //sending msg  moved to up
        changeOrder(user_id) {
            for (var i = 0; i <= this.friends.length; i++) {
                if (this.friends[i].id == user_id) {
                    var user = this.friends[i]
                    var index = this.friends.indexOf(user)
                    this.friends.splice(i, 1)
                    this.friends.unshift(user)
                    this.changeSelectedChatStyle(user_id)
                }
            }
        },
        //Close friends section in Mob
        closeFriends() {
            document.getElementById("friendsarea").style.left = "-720px"
        }
    }
};
</script>
<style scoped lang="scss">
//variables 
$mediaqueryWth: 900px;
$maincolor: #6979F8;
$gryTxtClr: #030304;
$whiteTxtClr: #F4F6FF;
$gryBgClr: #ECEBED;
$activeClr: #57d80e;
$msg1DateClr: #F2F3FE;
$msg2DateClr: #B8B8B8;
$borderradius: 9px;
$mediaqueryWth: 900px;
$unseenClr: #FF7C8A;
$statusClr: #5A4F6D;
$borderradius: 9px;
//start
//search
.srch_container {
    background-color: #eff0f1;
    border-top-left-radius: $borderradius;
    box-shadow: 1px 3px 3px #f9f9f9;
    padding: 22px 9px;
    img {
        display: none;
    }
    #srch {
        border-radius: 20px;
        padding: 4px 17px;
        width: 90%;
        margin: 0 auto;
        display: flex;
        background-color: #ffffff;
        img {
            display: block;
            width: 15px;
            margin-right: 8px;
        }
        input {
          caret-color: #6979f8;
            width: 100%;
            height: 100%;
            border: none;
            padding: 6px 2px;
            color: gray;
            background-color: #ffffff;
            &::placeholder {
                color: rgb(194,194,194);
                text-transform: capitalize;
            }
        }
    }
}
//Friends message list
#friend_list {
    position: relative;
}
section {
    background-color: white;
    cursor: pointer;
    display: flex;
    justify-content: space-around;
    padding: 14px 9px;
    border-bottom: 1px solid #F4F4F4;
    position: relative;
    z-index: 2;
    &:not(.focus):hover{
      background-color: #f5f5f7
    }
     .date {
    color: $msg2DateClr;
    font-size: 11px;
  }
}
.right_info {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
.num_unseen,.newMsgcount {
    background-color: $unseenClr;
    color: $whiteTxtClr;
    font-weight: bold;
    display: inline-block;
    width: 20px;
    height: 20px;
    font-size: 11px;
    line-height: 20px;
    padding: 0px;
    text-align: center;
    border-radius: 50%;
    margin-bottom: 6px;
    display: none;
}
.person {
    width: 100%;
    display: flex;
    position: relative;
    span {
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background-color: gray;
        position: absolute;
        top: 41px;
        left: 42px;
        border: 1px solid white;
    }
    .txt p {
            margin: 0px;
        
          &:first-child {
          color: $gryTxtClr;
          font-size: 17px;
           }
          &:last-child {
          color: $gryTxtClr;
          margin-bottom: 0rem;
          font-size: 13px;
           }

    }
  .perimg {
    border-radius: 50%;
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
 
}

// class added
.active {
    .perimg + span {
        background-color: $activeClr;
    }
}
.unseen {
    p {
        font-weight: bold !important;
        color: black !important;
    }
    .num_unseen {
        display: inline-block;
    }
    .newMsgcount{
        display: none;
    }
}
.focus {
    background-color: $maincolor;
    font-weight: normal !important;
    border-radius: 4px;
    p, span.date {
        color: white !important;
    }
    .newMsgcount
    {
        display: inline-block
    }
    .num_unseen {
        display: none;
    }
}
//Result Not found
#not_found {
    position: absolute;
    width: 100%;
    padding-top: 10px;
    top: 0px;
    p {
        text-align: center;
        color: #b1b1b1;
        font-weight: bold;
        text-transform: capitalize
    }
}
//mobile
@media screen and (max-width:$mediaqueryWth) {
    .srch_container {
        display: flex;
        justify-content: space-around;
        width: 100%;
        height: 75px;
        padding: 13px 12px;
        #srch {
            width: 77%;
        }
        img:first-child {
            display: block;
            width: 30px;
            margin-left: 10px;
        }
    }
}
</style>