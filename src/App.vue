<template>
  <div style="padding:50px 0;height:1080px; background-color: #929292" @scroll.prevent>
    <div id="app">
      <div>
        <v-snackbar
            v-model="showNotice"
            top
            dark
            elevation="8"
            :color="noticeColor"
        >
          {{ notice }}
        </v-snackbar>
      </div>
      <v-card
          id="card"
          class="console-card rounded-lg"
          elevation="6"
          dark
          style="overflow-y: scroll;"
      >
        <div class="overflow-y-auto overflow-x-hidden" style="display:table;height: 82%;">
          <div style="display: table-cell;vertical-align: bottom;" v-html="message">
          </div>
        </div>
        <div>
          <v-divider style="margin:20px 0"></v-divider>
          <div>
            <v-text-field
                v-model="textValue"
                placeholder="instruction..."
                solo
                class="input"
                append-icon="mdi-keyboard-return"
                @keyup.enter.native="sendMessage(textValue)"
            ></v-text-field>
          </div>
        </div>
      </v-card>
      <div style="margin: 30px auto; text-align: center">
        A websocket terminal for SmartFileSystem Server.<br>
        author: 刘亦奇<br>
        <a href="https://github.com/jinaxc/CSELab/tree/server" style="color:black">SmartFileSystem Server Github</a><br>
        <a href="https://github.com/liuyiqi1999/WebsocketTerminal" style="color:black">Websocket Terminal Github</a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data: function () {
    return {
      connection: null,
      message: 'use -help for help<br>',
      showNotice: false,
      noticeColor: '',
      notice: '',
      textValue: '',
    }
  },
  created: function () {
    document.documentElement.style.overflow='hidden';
    this.scrollToBottom()
    this.noticeColor = 'success'
    this.notice = "Starting connection to SmartFileSystem Server"
    this.showNotice = true
    this.connection = new WebSocket("ws://112.126.65.41:8889/ws")

    let that = this
    this.connection.onmessage = function (event) {
      console.log(event.data)
      that.message += event.data.replace(/\n/g,"<br />").replace(/\t/g,"&nbsp&nbsp")+"<br>"
      that.scrollToBottom()
    }

    this.connection.onopen = function (event) {
      console.log(event)
      that.noticeColor = 'success'
      that.notice = "Successfully connected to SmartFileSystem Server..."
      that.showNotice = true
    }

    this.connection.onclose = function (event) {
      console.log(event)
      that.noticeColor = 'fail'
      that.notice = "Failed to connect to SmartFileSystem Server"
      that.showNotice = true
    }
  },
  methods: {
    sendMessage: function (message) {
      console.log(message)
      this.message += "<b><span style='color: dodgerblue'>~: </span>"+message + "</b><br>"
      this.connection.send(message+'\n')
      this.textValue = ''
    },
    scrollToBottom: function() {
      this.$nextTick(function () {
        var card = document.getElementById('card');
        card.scrollTop = card.scrollHeight
      })
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.console-card {
  font-family: 'Roboto Mono', monospace;
  font-size: 16px;
  margin: 0px auto;
  width: 800px;
  height: 550px;
  padding: 40px 30px 20px;
  line-height: 25px;
  background-color: #373737 !important;
}
::-webkit-scrollbar {
  width: 0 !important;
}
::-webkit-scrollbar {
  width: 0 !important;height: 0;
}

</style>
