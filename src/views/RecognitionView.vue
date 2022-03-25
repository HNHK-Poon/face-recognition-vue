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
                    <div
                        class="w-50 h-20 p-8 text-slate-500 font-bold bg-white rounded-xl text-center justify-center items-center flex"
                    >
                        <img class="w-12 h-12 mr-4" src="@/assets/man.png" />
                        Gate Opened
                    </div>
                </div>
                <div
                    v-if="isAccessDenied"
                    :class="'absolute w-full h-full bg-slate-500/50 text-center justify-center items-center top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex'"
                >
                    <div
                        class="w-50 h-20 p-8 text-slate-500 font-bold bg-white rounded-xl text-center justify-center items-center flex"
                    >
                        <img
                            class="w-12 h-12 mr-4"
                            src="@/assets/prohibition.png"
                        />
                        Access Denied
                    </div>
                </div>
                <div
                    v-if="!isSocket"
                    :class="'absolute w-full h-full bg-slate-500/50 text-center justify-center items-center top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 flex'"
                >
                    <div
                        class="w-60 h-20 p-8 text-slate-500 font-bold bg-white rounded-xl text-center justify-center items-center flex"
                    >
                        <img
                            class="w-12 h-12 mr-4"
                            src="@/assets/disconnected.png"
                        />
                        Socket disconnected
                    </div>
                </div>
            </div>
            <div class="absolute bottom-10 flex justify-center mt-4">
                <button
                    v-if="!this.camera"
                    class="px-4 py-2 rounded-lg bg-cyan-500 text-gray-900 text-lg"
                    @click="startStream"
                >
                    Start
                </button>
                <button
                    v-if="this.camera"
                    class="px-4 py-2 rounded-lg bg-gray-900 text-white text-lg"
                    @click="stopStream"
                >
                    Stop
                </button>
            </div>
        </div>
        <div class="bg-slate-100 w-full h-30pc flex flex-no-wrap">
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
            // socket object
            socket: null,

            // flag that show/hide components
            isSocket: false,
            isGateOpen: false,
            isAccessDenied: false,
            isBoundingBox: false,

            // size and coordinate for the bounding box
            top: 0,
            left: 0,
            height: 0,
            width: 0,

            // list of checked in users
            checkIns: [],

            // reference for the camera streaming interval
            streamInterval: null,

            // reference for the camera object
            camera: false,

            // count of continuos user not detected events
            countUserNotDetected: 0,
        };
    },
    components: { CheckinCard },
    created() {
        const component = this;

        this.socket = new WebSocket('ws://localhost:8000/ws');

        this.socket.onopen = function () {
            // hide socket disconnected warning
            component.isSocket = true;
        };

        this.socket.onclose = function () {
            // show socket disconnected warning
            component.isSocket = false;
        };

        this.socket.onmessage = function (event) {
            // Check the socket event receive backend server and handle them accordingly
            const eventJson = JSON.parse(event.data);
            const eventType = eventJson.eventType;
            
            if (component.isGateOpen || component.isAccessDenied) {
                // ignore when gate is open or access denied
                return;
            } 
            else if ( eventType == 'userDetected' || eventType == 'multiUserDetected') {
                component.handleUserDetectedEvent(eventJson);
            } 
            else if (eventType == 'userNotDetected') {
                component.handleUserNotDetectedEvent(eventJson);
            } 
            else {
                component.handleFaceNotDetectedEvent();
            }
        };
    },
    methods: {
        createCameraElement() {
            // Camera settings (e.g. enable audio, camera output height & width)
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
                    this.$refs.camera.srcObject = stream;

                    // camera created, enable display of live video capture from camera
                    this.camera = true;
                })
                .catch((error) => {
                    alert(
                        "May the browser didn't support or there is some errors:", error
                    );
                });
        },

        startStream() {
            this.createCameraElement();

            // Start interval to capture image from camera every 0.5 second
            this.streamInterval = setInterval(() => {

                // get 2d image from camera element, size: 420px * 240px
                const context = this.$refs.canvas.getContext('2d');
                context.drawImage(this.$refs.camera, 0, 0, 420, 240);
                const imgData = context.getImageData(0, 0, 420, 240);

                // Convert image from rgba to rgb (by filter out the last reading)
                const filteredData = imgData.data.filter(function (_, i) {
                    return (i + 1) % 4;
                });

                // Convert image into array
                const typedArray = Array.prototype.slice.call(filteredData);
                const imageArray = Array.from(typedArray);

                // Socket event 
                const jsonData = {
                    eventType: 'recognition',
                    imageData: imageArray,
                };

                // Send socket event when gate is not opened or access denied
                if (!this.isGateOpen && !this.isAccessDenied) {
                    this.socket.send(JSON.stringify(jsonData));
                }

            }, 500);
        },

        stopStream() {
            // stop running camera interval
            if (this.streamInterval) {
                clearInterval(this.streamInterval);
            }

            // Hide components
            this.camera = false;
            this.isBoundingBox = false;
            this.isGateOpen = false;
            this.isAccessDenied = false;

            // Stop any exisiting created camera element 
            this.tracks = this.$refs.camera.srcObject.getTracks();
            if (this.tracks.length) {
                this.tracks.forEach((track) => track.stop());
            }
        },

        showBoundingBox(boundingBox) {
            /*
            get bounding box data from backend API 
            boundingBox = [top, right, bottom, left]
            */ 

            // top = top
            this.top = boundingBox[0];
            // left = left
            this.left = boundingBox[3];
            // width = right - left
            this.width = boundingBox[1] - boundingBox[3];
            //height = bottom = top
            this.height = boundingBox[2] - boundingBox[0];
            // show bounding box
            if (this.camera) this.isBoundingBox = true;
        },

        handleUserDetectedEvent(eventJson) {
            if (!this.isGateOpen) {

                this.showBoundingBox(eventJson.data.faceLocations);

                // limit to 15 checkins 
                if (this.checkIns.length > 15) this.checkIns.pop();
                const timestamp = new Date().getTime();
                eventJson.data.user['timestamp'] = timestamp;
                this.checkIns.unshift(eventJson.data.user);
            }

            // show gate opened for 3 seconds
            this.isGateOpen = true;
            setTimeout(() => {
                this.isGateOpen = false;
            }, 3000);

            // reset user not detected count
            this.countUserNotDetected = 0;
        },

        handleUserNotDetectedEvent(eventJson) {

            this.showBoundingBox(eventJson.data.faceLocations);

            // add count whenever user not identified
            this.countUserNotDetected += 1;

            // show access denied for 3 seconds after 3 failed identification
            if (this.countUserNotDetected > 3) {
                this.isAccessDenied = true;
                setTimeout(() => {
                    this.isAccessDenied = false;
                    // reset user not detected count
                    this.countUserNotDetected = 0;
                }, 3000);
            }
        },

        handleFaceNotDetectedEvent() {
            this.top = 0;
            this.left = 0;
            this.width = 0;
            this.height = 0;
            this.countUserNotDetected = 0;
        },
    },
};
</script>

<style></style>
