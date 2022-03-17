<template>
    <div
        class="h-max my-4 mx-2 w-32 rounded-lg shadow-md bg-gray-900 hover:bg-gradient-to-br"
    >
        <img class="rounded-t-lg" :src="image64" alt="" />
        <div class="p-1">
            <h5
                class="mb-1 text-[16px] font-bold tracking-tight text-cyan-400 text-center"
            >
                {{ name }}
            </h5>
            <h5
                class="mb-1 text-[11px] font-bold tracking-tight text-white text-center"
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
