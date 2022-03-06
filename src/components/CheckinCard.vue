<template>
    <div
        class="h-max my-4 mx-2 w-20 bg-white rounded-lg shadow-md bg-gradient-to-r from-cyan-400 via-cyan-500 to-cyan-600 hover:bg-gradient-to-br"
    >
        <img class="rounded-t-lg" :src="image64" alt="" />
        <div class="p-1">
            <h5
                class="mb-1 text-[14px] font-bold tracking-tight text-white text-center"
            >
                {{ name }}
            </h5>
            <h5
                class="mb-1 text-[14px] font-bold tracking-tight text-white text-center"
            >
                {{ position }}
            </h5>
            <p class="mb-1 font-normal text-white text-[10px] text-center">
                Check in time: <br />
                {{ checkinTime }}
            </p>
        </div>
    </div>
</template>

<script>
export default {
    props: ['name', 'position', 'checkinTime', 'image64'],
};
</script>

<style></style>
