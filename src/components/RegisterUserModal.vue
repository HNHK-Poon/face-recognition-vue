<script setup>
import classNames from 'classnames';
import { format } from 'date-fns';
</script>

<template>
    <div
        class="w-full top-10pc h-90pc absolute flex justify-center items-center bg-slate-900/75 z-20"
    >
        <form @submit.prevent="handleSubmit">
            <div class="px-4 w-full h-full md:h-auto mx-auto">
                <!-- Modal content -->
                <div class="relative bg-white rounded-lg shadow">
                    <!-- Modal header -->
                    <div
                        class="flex justify-between items-start p-5 rounded-t border-b"
                    >
                        <h3
                            class="text-xl font-semibold text-gray-900 lg:text-2xl"
                        >
                            Register New User
                        </h3>
                        <button
                            @click="$emit('closeRegisterModal')"
                            class="text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm p-1.5 ml-auto inline-flex items-center"
                        >
                            <svg
                                class="w-5 h-5"
                                fill="currentColor"
                                viewBox="0 0 20 20"
                                xmlns="http://www.w3.org/2000/svg"
                            >
                                <path
                                    fill-rule="evenodd"
                                    d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                                    clip-rule="evenodd"
                                ></path>
                            </svg>
                        </button>
                    </div>
                    <!-- Modal body -->
                    <div class="p-6 space-y-6 flex justify-center items-center">
                        <div class="h-[240px] w-[420px]">
                            <video
                                v-show="!isPhotoTaken"
                                ref="camera"
                                :width="420"
                                :height="240"
                                autoplay
                            ></video>

                            <canvas
                                v-show="isPhotoTaken"
                                id="photoTaken"
                                ref="canvas"
                                :width="420"
                                :height="240"
                            ></canvas>
                        </div>
                        <div class="p-4">
                            <div class="relative z-0 mb-6 w-60 group">
                                <input
                                    type="text"
                                    name="name"
                                    class="block py-1.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                                    placeholder=" "
                                    required
                                    v-model="name"
                                    @keydown.enter.stop.prevent
                                />
                                <label
                                    for="name"
                                    class="absolute text-sm text-gray-500 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                                    >Name</label
                                >
                            </div>
                            <div class="relative z-0 mb-6 w-full group">
                                <input
                                    type="text"
                                    name="name"
                                    class="block py-1.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                                    placeholder=" "
                                    required
                                    v-model="position"
                                    @keydown.enter.stop.prevent
                                />
                                <label
                                    for="name"
                                    class="absolute text-sm text-gray-500 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                                    >Position</label
                                >
                            </div>
                            <div class="relative z-0 mb-6 w-full group">
                                <input
                                    type="text"
                                    name="name"
                                    class="block py-1.5 px-0 w-full text-sm text-gray-900 bg-transparent border-0 border-b-2 border-gray-300 appearance-none focus:outline-none focus:ring-0 focus:border-blue-600 peer"
                                    placeholder=" "
                                    required
                                    v-model="employeeId"
                                    @keydown.enter.stop.prevent
                                />
                                <label
                                    for="name"
                                    class="absolute text-sm text-gray-500 duration-300 transform -translate-y-6 scale-75 top-3 -z-10 origin-[0] peer-focus:left-0 peer-focus:text-blue-600 peer-placeholder-shown:scale-100 peer-placeholder-shown:translate-y-0 peer-focus:scale-75 peer-focus:-translate-y-6"
                                    >Employer ID</label
                                >
                            </div>
                            <div v-if="this.errors.length">
                                <p
                                    v-for="error in this.errors"
                                    :key="error"
                                    class="text-sm text-red-700"
                                >
                                    *{{ error }}
                                </p>
                            </div>
                        </div>
                    </div>

                    <!-- Modal footer -->
                    <div
                        class="flex items-center justify-center p-6 space-x-2 rounded-b border-t border-gray-200"
                    >
                        <button
                            type="button"
                            @click="takePhoto"
                            :class="
                                classNames(
                                    'rounded-lg border border-gray-200 text-sm font-medium px-5 py-2.5 focus:z-10',
                                    {
                                        'bg-red-500 hover:bg-red-600 text-white hover hover:text-gray-100':
                                            isPhotoTaken,
                                        'bg-white hover:bg-gray-100 text-gray-500 hover:text-gray-900':
                                            !isPhotoTaken,
                                    }
                                )
                            "
                        >
                            {{ isPhotoTaken ? 'Retake Photo' : 'Take Photo' }}
                        </button>
                        <button
                            type="submit"
                            :disabled="!isPhotoTaken"
                            class="text-white bg-blue-700 hover:bg-blue-800 disabled:bg-slate-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center"
                        >
                            Register
                        </button>
                    </div>
                </div>
            </div>
        </form>
    </div>
</template>

<script>
export default {
    data() {
        return {
            name: '',
            position: '',
            employeeId: '',
            takePhotoButtonText: 'Take Photo',
            isPhotoTaken: false,
            imgData: null,
            socket: null,
            errors: [],
        };
    },
    props: ['userIds'],
    methods: {
        createCameraElement() {
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
                })
                .catch((error) => {
                    alert(
                        "May the browser didn't support or there is some errors."
                    );
                });
        },
        takePhoto() {
            if (!this.isPhotoTaken) {
                this.isShotPhoto = true;

                const FLASH_TIMEOUT = 50;

                setTimeout(() => {
                    this.isShotPhoto = false;
                }, FLASH_TIMEOUT);
            }

            this.isPhotoTaken = !this.isPhotoTaken;

            const context = this.$refs.canvas.getContext('2d');
            context.drawImage(this.$refs.camera, 0, 0, 420, 240);
            this.imgData = context.getImageData(0, 0, 420, 240);
        },
        checkSumbit() {
            return new Promise((resolve, reject) => {
                this.errors = [];

                console.log(
                    this.userIds,
                    this.employeeId,
                    this.userIds.includes(this.employeeId)
                );

                if (this.userIds.includes(this.employeeId)) {
                    this.errors.push('Employee ID is used. Try again.');
                }

                if (this.errors.length) {
                    resolve(false);
                } else {
                    resolve(true);
                }
            });
        },
        async handleSubmit() {
            // if (await this.checkSumbit()) {
            //     const img64uri = this.$refs.canvas.toDataURL('image/jpeg', 0.5);
            //     let filteredData = this.imgData.data.filter(function (_, i) {
            //         return (i + 1) % 4;
            //     });
            //     // console.log(filteredData.length);
            //     let typedArray = Array.prototype.slice.call(filteredData);
            //     var imageArray = Array.from(typedArray);
            //     let postData = {
            //         eventType: 'register',
            //         data: {
            //             name: this.name,
            //             position: this.position,
            //             employeeId: this.employeeId,
            //             imageArray: imageArray,
            //             image64: img64uri,
            //             timestamp: new Date().getTime(),
            //             createdAt: format(new Date(), 'dd/MM/yy hh:mmaa'),
            //         },
            //     };
            //     console.log(postData);
            //     this.socket.send(JSON.stringify(postData));
            //     this.$emit('closeRegisterModal');
            // }

            if (await this.checkSumbit()) {
                const img64uri = this.$refs.canvas.toDataURL('image/jpeg', 0.5);
                let filteredData = this.imgData.data.filter(function (_, i) {
                    return (i + 1) % 4;
                });
                let typedArray = Array.prototype.slice.call(filteredData);
                var imageArray = Array.from(typedArray);
                const postData = {
                    name: this.name,
                    position: this.position,
                    employeeId: this.employeeId,
                    imageArray: imageArray,
                    image64: img64uri,
                    timestamp: new Date().getTime(),
                    createdAt: format(new Date(), 'dd/MM/yy hh:mmaa'),
                };
                try {
                    const res = await fetch(`http://localhost:8000/register`, {
                        method: 'POST',
                        headers: {
                            'Content-type': 'application/json; charset=UTF-8',
                        },
                        body: JSON.stringify(postData),
                    });
                    const resJson = await res.json();
                    console.log('status text', res.statusText, resJson);
                    this.$emit('closeRegisterModal');
                    if (!res.ok) {
                        const message = `An error has occured: ${res.status} - ${res.statusText}`;
                        throw new Error(message);
                    }
                } catch (err) {
                    console.log(err.message);
                }
            }
        },
    },
    created: function () {
        let component = this;
        console.log('Starting connection to WebSocket Server');
        this.socket = new WebSocket('ws://localhost:8000/ws');

        this.socket.onmessage = function (event) {
            let eventJson = JSON.parse(event.data);
            console.log(eventJson);
        };

        this.socket.onopen = function (event) {
            console.log(event);
            console.log(
                'Successfully connected to the echo websocket server...'
            );
        };
    },
    mounted: function () {
        this.createCameraElement();
    },
    beforeUnmount: function () {
        this.tracks = this.$refs.camera.srcObject.getTracks();
        if (this.tracks.length) {
            this.tracks.forEach((track) => track.stop());
        }
    },
};
</script>

<style></style>
