/* globals utils */
<template>
  <div id="app">
    <ol>
      <li>
        Browse to a folder to process. <button @click="browse">Browse</button>
      </li>
    </ol>
  </div>
</template>

<script>
export default {
  name: "app",
  methods: {
    connectStreamSocket: function () {
      console.log("connectStreamSocket");
      var refThis = this;
      if (!this.streamSocket) {
        console.log("create stream socket");
        var streamSocket = new WebSocket("ws://localhost:8081");
        this.streamSocket = streamSocket;
        streamSocket.onopen = function (event) {
          console.log("Stream socket opened");
          streamSocket.onmessage = function (evt) {
            console.log("onmessage", evt);
          };
        };
        streamSocket.onclose = function (event) {
          refThis.streamSocket = undefined;
          setTimeout(function () {
            //reconnect
            refThis.connectStreamSocket();
          }, 1000);
        };
        streamSocket.onerror = function (error) {
          console.error("streamSocket error! ", error);
        };
        this.streamSocket = streamSocket;
      }
    },
    browse: function () {
      console.log("Open dialog");

      console.log("streamSocket", this.streamSocket);
      if (!this.streamSocket) {
        this.connectStreamSocket();
        return;
      }

      console.log("streamSocket.readyState", this.streamSocket.readyState);
      if (this.streamSocket.readyState === this.SOCKET_OPEN) {
        let sendJson = {
          method: "readdir",
        };
        console.log("send", JSON.stringify(sendJson));
        this.streamSocket.send(JSON.stringify(sendJson));
      }
    },
  },
  data() {
    return {
      SOCKET_OPEN: 1,
      streamSocket: undefined,
    };
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
