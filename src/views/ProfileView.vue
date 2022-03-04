<script setup>
import RegisterUserModal from "../components/RegisterUserModal.vue";
import UserCard from "../components/UserCard.vue";
</script>

<template>
  <RegisterUserModal v-if="isModal" @closeModal="toggleModal"/>
  <div class="h-90pc w-full bg-slate-300">
    <div class="h-full w-60pc flex items-center justify-center">
      <div
        class="h-90pc w-95pc p-4 rounded-xl bg-slate-200 flex flex-wrap overflow-auto"
      >
        <UserCard
          v-for="index in 0"
          :key="index"
          name="Peter"
          position="Software Engineer"
          createdAt="12/03/22 6:48PM"
        />
        <div
          @click="toggleModal"
          class="m-2 rounded-xl w-40 h-60 bg-gradient-to-r from-slate-400 via-slate-500 to-slate-600 flex justify-center items-center"
        >
          <button>
            <svg
              class="w-20 h-20 fill-slate-100"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z"
              ></path>
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isModal: false,
    };
  },
  components: [UserCard, RegisterUserModal],
  created() {
    console.log("created profile");
  },
  methods: {
    toggleModal(e) {
      this.isModal = !this.isModal;
    },
  },
};
</script>

<style></style>
UserCardUserCard
