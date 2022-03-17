<template>
    <div
        class="h-max my-2 mx-2 w-48 h-72 rounded-lg shadow-md bg-gray-900 hover:bg-gradient-to-br"
    >
        <a href="#">
            <img class="rounded-t-lg" :src="image64" alt="" />
        </a>
        <div class="p-1">
            <h5
                class="mb-4 text-[18px] font-bold tracking-tight text-cyan-400 text-center"
            >
                {{ name }}
            </h5>
            <p class="mb-4 font-bold text-white text-[12px] text-center">
                {{ position }}
            </p>
            <p class="mb-4 font-normal text-slate-100 text-[10px] text-center">
                Created at <br />
                {{ createdAt }}
            </p>
            <div class="mx-auto w-8 h-8 flex justify-center items-center my-2">
                <button
                    @click="handleDeleteUser"
                    type="button"
                    class="text-white bg-red-400 hover:bg-red-600 font-medium rounded-full text-sm p-2.5 text-center inline-flex items-center mx-auto"
                >
                    <svg
                        class="w-4 h-4"
                        fill="currentColor"
                        viewBox="0 0 20 20"
                        xmlns="http://www.w3.org/2000/svg"
                    >
                        <path
                            fill-rule="evenodd"
                            d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z"
                            clip-rule="evenodd"
                        ></path>
                    </svg>
                </button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['name', 'position', 'employeeId', 'createdAt', 'image64'],
    methods: {
        handleDeleteUser() {
            // this.$emit('deleteUser', this.employeeId);
            this.$emit('openDeleteUserModal', {
              employeeId: this.employeeId
            });
        },
    },
};
</script>

<style></style>
