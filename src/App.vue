<template>
  <div class="container mt-3">
    <center>
      <button :class="{active:tab === 1}" class="btn btn-success" @click="autoButton()">Auto</button>
      <button :class="{active:tab === 2}" class="btn btn-success  ml-2" @click="customButton()">Custom</button>
    </center>
    <div class="card mt-3" v-if="button.autoButton">
      <div class="card-body">
        <div class="form-group">
          <label for="exampleFormControlInput1">ID USER</label>
          <input type="number" v-model="form.idUser" class="form-control" id="exampleFormControlInput1"
            placeholder="xxxxxxxxxxxxxxxxxx">
        </div>
        <div class="form-group">
          <label for="exampleFormControlTextarea1">Content</label>
          <div class="form-group">
            <button class="btn btn-info btn-sm" @click="form.content ='<@!>'">แท็คคน</button>
          </div>
          <textarea v-model="form.content" class="form-control" id="textarea" rows="3"
            placeholder="ข้อความที่ต้องการส่ง"></textarea>
        </div>
        <hr>
        <div class="form-group">
          <button type="submit" class="btn btn-success" @click="getUser(form.idUser)">ส่ง</button>
          <button class="btn btn-danger ml-2" @click="resetFormAll()">ล้าง</button>
        </div>
      </div>
    </div>
    <div class="card mt-3" v-if="button.customButton">
      <div class="card-body">
        <div class="form-group">
          <label for="exampleFormControlInput1">Username</label>
          <div class="input-group">
            <div class="input-group-prepend">
              <span class="input-group-text">{{form.maxlength - form.username.length}}</span>
            </div>
            <input type="text" v-model="form.username" class="form-control" id="exampleFormControlInput1"
              placeholder="ชื่อที่แสดง" :maxlength="form.maxlength">
          </div>
        </div>
        <div class="form-group">
          <label for="exampleFormControlTextarea1">AvataUrl</label>
          <input v-model="form.avatar_url" type="url" class="form-control" placeholder="Link Image Profile">
        </div>
        <div class="form-group">
          <label for="exampleFormControlTextarea1">Content</label>
          <div class="form-group">
            <button class="btn btn-info btn-sm" @click="form.content ='<@!>'">แท็คคน</button>
          </div>
          <textarea id="textarea" v-model="form.content" class="form-control" rows="3"
            placeholder="ข้อความที่ต้องการส่ง"></textarea>
        </div>
        <hr>
        <div class="form-group">
          <button class="btn btn-success" @click="sendContent(form.username,form.content,form.avatar_url)">ส่ง</button>
          <button class="btn btn-danger ml-2" @click="resetFormAll()">ล้าง</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  const axios = require('axios');

  export default {
    name: 'DiscordSend',
    components: {},
    data() {
      return {
        tab: 1,
        form: {
          idUser: "",
          content: "",
          username: "",
          avatar_url: "",
          maxlength: 40
        },
        button: {
          autoButton: true,
          customButton: false,
        },
        getUserData: [],
        userAvatarURL: "",
        userName: "",
        resData: ""

      }
    },
    methods: {
      getUser: function (id) {
        axios.get('https://api-vue.000webhostapp.com/discord/getUser.php?id=' + id)
          .then((res) => {
            this.getUserData = res.data;
            var avatar = res.data.user.avatar;
            var user = res.data.user.id;
            this.userName = res.data.mutual_guilds[0].nick;
            if (res.data.mutual_guilds[0].nick === null) {
              this.userName = res.data.user.username;
            } else {
              this.userName = res.data.mutual_guilds[0].nick;
            }
            this.userAvatarURL = 'https://cdn.discordapp.com/avatars/' + user + '/' + avatar + '.png';

            this.sendContent(this.userName, this.form.content, this.userAvatarURL);
            this.resetForm();
          })
      },
      sendContent: function (username, content, avatar_url) {
        axios.get('https://api-vue.000webhostapp.com/discord/sendContent.php?username=' + username + '&content=' +
            content + '&avatar_url=' + avatar_url)
          .then((res) => {
            this.resData = res.data;
            this.resetForm();
          })
      },
      resetForm: function () {
        this.form.content = "";
      },
      resetFormAll: function () {
        this.form.content = "";
        this.form.idUser = "";
        this.form.username = "";
        this.form.avatar_url = "";
      },
      autoButton: function () {
        this.button.autoButton = true,
          this.button.customButton = false,
          this.tab = 1
      },
      customButton: function () {
        this.button.autoButton = false,
          this.button.customButton = true
        this.tab = 2

      }

    },
    created() {}
  }
</script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Kanit&display=swap');

  body {
    font-family: 'Kanit', sans-serif;
  }
</style>