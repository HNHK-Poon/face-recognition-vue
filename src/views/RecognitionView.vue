<template>
  <main class="w-full block">
    <div
      class="
        w-full
        h-70pc
        bg-slate-200
        relative
        flex
        items-center
        justify-center
      "
    >
      <div class="relative">
        <video
          v-show="true"
          ref="camera"
          :width="420"
          :height="240"
          autoplay
          class=""
        ></video>
        <canvas
          v-show="false"
          id="photoTaken"
          ref="canvas"
          :width="420"
          :height="240"
          class="bg-slate-600 w-50pc"
        ></canvas>
        <div
          v-if="isBoundingBox"
          :class="'absolute border-2 border-red-500'"
          :style="`top:${top}px; left:${left}px; height:${height}px; width:${width}px`"
        ></div>
      </div>
      <div class="absolute bottom-10 flex justify-center">
        <button
          class="rounded-lg bg-blue-500 text-slate-900 text-lg"
          @click="createCameraElement"
        >
          Start
        </button>
        <button
          class="rounded-lg bg-green-500 text-slate-900 text-lg"
          @click="startStream"
        >
          Stream
        </button>
        <button
          class="rounded-lg bg-orange-500 text-slate-900 text-lg"
          @click="register"
        >
          Register
        </button>
        <button
          class="rounded-lg bg-red-500 text-slate-900 text-lg"
          @click="getUser"
        >
          Get User
        </button>
      </div>
    </div>
    <div class="bg-slate-300 w-full h-30pc flex">
      <div v-for="index in 3" :key="index"
        class="
          h-max
          my-4
          mx-2
          w-20
          bg-white
          rounded-lg
          shadow-md
          bg-gradient-to-r from-cyan-400 via-cyan-500 to-cyan-600 hover:bg-gradient-to-br
        "
      >
        <a href="#">
          <img
            class="rounded-t-lg"
            src="data:image/jpeg;base64,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"
            alt=""
          />
        </a>
        <div class="p-1">
          <h5
            class="
              mb-1
              text-[14px]
              font-bold
              tracking-tight
              text-white
              text-center
            "
          >
            Peter
          </h5>
          <p class="mb-1 font-normal text-white text-[10px] text-center">
            Check in time: <br/>
            3:07PM
          </p>

        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      isLoading: false,
      socket: null,
      isBoundingBox: false,
      top: 0,
      left: 0,
      height: 120,
      width: 150,
    };
  },
  created() {
    let component = this;
    console.log("Starting connection to WebSocket Server");
    this.socket = new WebSocket("ws://localhost:8000/ws");

    this.socket.onmessage = function (event) {
      let eventJson = JSON.parse(event.data);
      let boundingBox = eventJson.data.faceLocations;
      console.log(eventJson, boundingBox);
      component.top = boundingBox[0];
      component.left = boundingBox[3];
      component.width = boundingBox[1] - boundingBox[3];
      component.height = boundingBox[2] - boundingBox[0];
      component.isBoundingBox = true;
    };

    this.socket.onopen = function (event) {
      console.log(event);
      console.log("Successfully connected to the echo websocket server...");
    };
  },
  methods: {
    createCameraElement() {
      this.isLoading = true;

      const constraints = (window.constraints = {
        audio: false,
        video: {
          height: this.$refs.camera.height,
          width: this.$refs.camera.width,
        },
      });

      navigator.mediaDevices
        .getUserMedia(constraints)
        .then((stream) => {
          this.isLoading = false;
          this.$refs.camera.srcObject = stream;
        })
        .catch((error) => {
          this.isLoading = false;
          alert("May the browser didn't support or there is some errors.");
        });
    },
    async register() {
      const context = this.$refs.canvas.getContext("2d");
      context.drawImage(this.$refs.camera, 0, 0, 420, 240);
      // console.log(context.getImageData(0, 0, 420, 240));
      const imgData = context.getImageData(0, 0, 420, 240);
      // context.putImageData(imgData, 0, 0);

      let filteredData = imgData.data.filter(function (_, i) {
        return (i + 1) % 4;
      });
      let typedArray = Array.prototype.slice.call(filteredData);
      var imageArray = Array.from(typedArray);

      const postData = {
        name: "Alex",
        image: imageArray,
        timestamp: new Date().getTime(),
      };
      try {
        const res = await fetch(`http://localhost:8000/register`, {
          method: "post",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(postData),
        });
        console.log("response", res, res.json);
        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }
      } catch (err) {
        this.postResult = err.message;
      }

      // let jsonData = {
      //   eventType: "recognition",
      //   imageData: imageArray,
      // };
      // this.socket.send(JSON.stringify(jsonData));
    },
    async getUser() {
      try {
        const res = await fetch(`http://localhost:8000/getUser`);
        const data = await res.json();
        console.log("response", res, data);
        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }
      } catch (err) {
        this.postResult = err.message;
      }
    },
    startStream() {
      // if (!this.isPhotoTaken) {
      //   this.isShotPhoto = true;

      //   const FLASH_TIMEOUT = 50;

      //   setTimeout(() => {
      //     this.isShotPhoto = false;
      //   }, FLASH_TIMEOUT);
      // }

      // this.isPhotoTaken = !this.isPhotoTaken;

      setInterval(() => {
        const context = this.$refs.canvas.getContext("2d");
        context.drawImage(this.$refs.camera, 0, 0, 420, 240);
        // console.log(context.getImageData(0, 0, 420, 240));
        const imgData = context.getImageData(0, 0, 420, 240);
        const img64uri = this.$refs.canvas.toDataURL("image/jpeg", 0.5);
        console.log(img64uri);
        // context.putImageData(imgData, 0, 0);

        let filteredData = imgData.data.filter(function (_, i) {
          return (i + 1) % 4;
        });
        // console.log(filteredData.length);
        let typedArray = Array.prototype.slice.call(filteredData);
        var imageArray = Array.from(typedArray);
        let jsonData = {
          eventType: "recognition",
          imageData: imageArray,
        };
        this.socket.send(JSON.stringify(jsonData));
      }, 500);
    },
  },
};
</script>

<style>
</style>