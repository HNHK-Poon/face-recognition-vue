<template>
    <div
        class="absolute h-90pc flex right-0 left-0 z-10 justify-center items-center bg-slate-500/75"
    >
        <div class="relative px-4 w-full max-w-md h-full md:h-auto">
            <div class="relative bg-white rounded-lg shadow">
                <div class="flex justify-end p-2">
                    <button
                        @click="$emit('closeUserModal')"
                        type="button"
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
                <div class="p-6 pt-0 text-center">
                    <svg
                        class="mx-auto mb-4 w-14 h-14 text-gray-400 "
                        fill="none"
                        stroke="currentColor"
                        viewBox="0 0 24 24"
                        xmlns="http://www.w3.org/2000/svg"
                    >
                        <path
                            stroke-linecap="round"
                            stroke-linejoin="round"
                            stroke-width="2"
                            d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
                        ></path>
                    </svg>
                    <h3
                        class="mb-5 text-lg font-normal text-gray-500 "
                    >
                        Are you sure you want to delete this user?
                    </h3>
                    <button
                        @click="handleDeleteUser"
                        type="button"
                        class="text-white bg-red-600 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm inline-flex items-center px-5 py-2.5 text-center mr-2"
                    >
                        Yes, I'm sure
                    </button>
                    <button
                        @click="$emit('closeUserModal')"
                        type="button"
                        class="text-gray-500 bg-white hover:bg-gray-100 focus:ring-4 focus:ring-gray-300 rounded-lg border border-gray-200 text-sm font-medium px-5 py-2.5 hover:text-gray-900 focus:z-10"
                    >
                        No, cancel
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['employeeId'],
    methods: {
        handleDeleteUser() {
            this.$emit('clickDeleteUser', this.employeeId);
            this.$emit('closeUserModal')
        }
    }
};
</script>

<style></style>
