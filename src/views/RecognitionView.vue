<script setup>
import { format } from 'date-fns';
</script>

<template>
    <main class="w-full block">
        <div
            class="w-full h-70pc bg-slate-200 relative flex items-center justify-center"
        >
            <div class="relative mb-4">
                <video
                    v-show="camera"
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
                <div
                    v-if="isGateOpen"
                    :class="'absolute w-full h-full bg-slate-500/50 text-center justify-center items-center top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex'"
                >
                    <div class="w-40 h-20 text-slate-500 font-bold bg-white rounded-xl text-center justify-center items-center flex">
                        <svg
                            role="status"
                            class="inline mr-2 w-8 h-8 text-gray-200 animate-spin fill-white"
                            viewBox="0 0 100 101"
                        >
                            <path
                                d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                                fill="gray"
                            />
                            <path
                                d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                                fill="currentFill"
                            /></svg
                        >Gate Opened
                    </div>
                </div>
            </div>
            <div class="absolute bottom-10 flex justify-center mt-4">
                <button
                    v-if="!this.camera"
                    class="px-4 py-2 rounded-lg bg-blue-500 text-slate-900 text-lg"
                    @click="startStream"
                >
                    Start
                </button>
                <button
                    v-if="this.camera"
                    class="px-4 py-2 rounded-lg bg-green-500 text-slate-900 text-lg"
                    @click="stopStream"
                >
                    Stop
                </button>
            </div>
        </div>
        <div class="bg-slate-300 w-full h-30pc flex flex-no-wrap">
            <CheckinCard
                v-for="checkIn in checkIns"
                :key="checkIn.timestamp"
                :name="checkIn.name"
                :position="checkIn.position"
                :checkinTime="format(new Date(), 'hh:mmaa')"
                :image64="checkIn.image64"
            />
        </div>
    </main>
</template>

<script>
import CheckinCard from '../components/CheckinCard.vue';
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
            checkIns: [],
            streamInteval: null,
            camera: false,
            isGateOpen: false,
        };
    },
    components: { CheckinCard },
    created() {
        let component = this;
        console.log('Starting connection to WebSocket Server');
        this.socket = new WebSocket('ws://localhost:8000/ws');

        this.socket.onmessage = function (event) {
            const eventJson = JSON.parse(event.data);
            const eventType = eventJson.eventType;
            console.log(eventJson, eventType);
            if (
                eventType == 'userDetected' ||
                eventType == 'multiUserDetected'
            ) {
                if (!component.isGateOpen) {
                    const boundingBox = eventJson.data.faceLocations;
                    component.top = boundingBox[0];
                    component.left = boundingBox[3];
                    component.width = boundingBox[1] - boundingBox[3];
                    component.height = boundingBox[2] - boundingBox[0];
                    if (component.camera) {
                        component.isBoundingBox = true;
                    }
                    const timestamp = new Date().getTime();
                    eventJson.data.user['timestamp'] = timestamp;
                    if (component.checkIns.length > 15) {
                        component.checkIns.pop();
                    }
                    component.checkIns.unshift(eventJson.data.user);
                    console.log(component.checkIns);
                }
                component.isGateOpen = true;
                setTimeout(() => {
                    component.isGateOpen = false;
                }, 5000);
                console.log(eventJson);
            } else {
                component.top = 0;
                component.left = 0;
                component.width = 0;
                component.height = 0;
            }
        };

        this.socket.onopen = function (event) {
            console.log(event);
            console.log(
                'Successfully connected to the echo websocket server...'
            );
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
                    this.camera = true;
                })
                .catch((error) => {
                    this.isLoading = false;
                    alert(
                        "May the browser didn't support or there is some errors."
                    );
                });
        },
        startStream() {
            this.createCameraElement();

            this.streamInteval = setInterval(() => {
                const context = this.$refs.canvas.getContext('2d');
                context.drawImage(this.$refs.camera, 0, 0, 420, 240);
                // console.log(context.getImageData(0, 0, 420, 240));
                const imgData = context.getImageData(0, 0, 420, 240);
                // const img64uri = this.$refs.canvas.toDataURL('image/jpeg', 0.5);
                // console.log(img64uri);
                // context.putImageData(imgData, 0, 0);

                let filteredData = imgData.data.filter(function (_, i) {
                    return (i + 1) % 4;
                });
                // console.log(filteredData.length);
                let typedArray = Array.prototype.slice.call(filteredData);
                var imageArray = Array.from(typedArray);
                let jsonData = {
                    eventType: 'recognition',
                    imageData: imageArray,
                };
                if (!this.isGateOpen) {
                    this.socket.send(JSON.stringify(jsonData));
                }
            }, 500);
        },
        stopStream() {
            this.camera = false;
            this.isBoundingBox = false;
            this.isGateOpen = false;
            if (this.streamInteval) {
                clearInterval(this.streamInteval);
            }
            this.tracks = this.$refs.camera.srcObject.getTracks();
            if (this.tracks.length) {
                this.tracks.forEach((track) => track.stop());
            }
        },
    },
};
</script>

<style></style>
