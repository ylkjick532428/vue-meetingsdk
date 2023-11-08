

<template>
  <main>
    <h1>Zoom Meeting SDK Sample Vue.js 3</h1>
    <button @click="getSignature">Join Meeting</button>
  </main>
</template>

<script lang="ts">
import { ZoomMtg } from '@zoomus/websdk';

export default {
  name: 'HelloWorld',
  created () {
    ZoomMtg.setZoomJSLib('https://source.zoom.us/2.18.0/lib', '/av');
    ZoomMtg.preLoadWasm();
    ZoomMtg.prepareWebSDK();
    // loads language files, also passes any error messages to the ui
    ZoomMtg.i18n.load('en-US');
    ZoomMtg.i18n.reload('en-US');
  },
  mounted() {
    ZoomMtg.inMeetingServiceListener("onUserJoin", (data: any) => {
      console.log("inMeetingServiceListener onUserJoin", data);
    });
  },
  data () {
    return {
      sdkKey: "",
      sdkSecret: "",
      meetingNumber: "",
      passWord: "",
      role: 0,
      userName: "Vue.js",
      userEmail: "",
      registrantToken: '',
      zakToken: '',
      leaveUrl: "http://localhost:5173"
    }
  },
  methods: {
    getSignature() {
      const that = this;
      ZoomMtg.generateSDKSignature({
        sdkKey: this.sdkKey,
        sdkSecret: this.sdkSecret,
        meetingNumber: this.meetingNumber,
        role: this.role.toString(),
        success: function({result}: any){
          that.startMeeting(result);
        }
      })
    },
    startMeeting(signature: string) {
      const zoommtg = document.getElementById("zmmtg-root");
      if (zoommtg) {
        zoommtg.style.display = "block";
      }

      ZoomMtg.init({
        leaveUrl: this.leaveUrl,
        success: (success: any) => {
          console.log(success);
          ZoomMtg.join({
            signature: signature,
            sdkKey: this.sdkKey,
            meetingNumber: this.meetingNumber,
            passWord: this.passWord,
            userName: this.userName,
            userEmail: this.userEmail,
            tk: this.registrantToken,
            zak: this.zakToken,
            success: (success: any) => {
              console.log(success);
            },
            error: (error: any) => {
              console.log(error);
            }
          });
        },
        error: (error: any) => {
          console.log(error);
        }
      });
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
main {
  width: 70%;
  margin: auto;
  text-align: center;
}

main button {
  margin-top: 20px;
  background-color: #2D8CFF;
  color: #ffffff;
  text-decoration: none;
  padding-top: 10px;
  padding-bottom: 10px;
  padding-left: 40px;
  padding-right: 40px;
  display: inline-block;
  border-radius: 10px;
  cursor: pointer;
  border: none;
  outline: none;
}

main button:hover {
  background-color: #2681F2;
}
</style>