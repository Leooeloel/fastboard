<template>
  <div id="app">
    <div style="width: 100vw; height: 100vh" ref="whiteboard"></div>
  </div>
</template>

<script>
import axios from "axios";
import { createFastboard, mount } from "@netless/fastboard";

export default {
  name: 'App',
  data() {
    return {
      uuid: "",
      appId: "i_dKMIzIEeyy2vEM-8rsrA/oBccCLz1aNHryw",
      roomToken: "",
      app: "",
      url: "https://api.netless.link/v5/rooms",
      tokenUrl: "https://api.netless.link/v5/tokens/rooms/",
      sdkToken: "NETLESSSDK_YWs9cXVnUmllSWdWVW5zT1FOWCZub25jZT0yOWY4MGQwMC04Y2M5LTExZWMtYjJkYS1mMTBjZmJjYWVjYWMmcm9sZT0wJnNpZz01MDZjMjhiNDliNmU4YTEzMzg2YTU0MjhmMDRiYjdlMzkzYjQ5Mzg5YjRlMGI1NTU5MzljNDZhMjEyMmExZjQz"
    }
  },
  async mounted() {
    console.log(this.$refs.whiteboard);
    const uuidRes = await this.getUUid();
    this.uuid = uuidRes.data.uuid;

    const tokenRes = await this.getToken();
    this.roomToken = tokenRes.data

    console.log(this.uuid, this.roomToken);

    await this.mountFastboard(this.$refs.whiteboard);
  },
  methods: {
    async getUUid() {
      return axios.post(this.url, {}, {
        headers: {
          "content-type": "application/json",
          "region": "cn-hz",
          "token": this.sdkToken,
        }
      })
    },
    getToken() {
      return axios.post(this.tokenUrl + this.uuid, {
          lifespan: 0,
          role: "admin"
        }, {
        headers: {
          "content-type": "application/json",
          "region": "cn-hz",
          "token": this.sdkToken,
        }
      });
    },
  async mountFastboard(div) {
    this.app = await createFastboard({
      // [1]
      sdkConfig: {
        appIdentifier: this.appId 
      },
      // [2]
      joinRoom: {
        uid: "",
        uuid: this.uuid,
        roomToken: this.roomToken,
      },
      // [3]
      managerConfig: {
        cursor: true,
      },
    });
    return mount(this.app, div);
  },
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
