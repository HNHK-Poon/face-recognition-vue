<script setup>
import DeleteUserModal from '../components/DeleteUserModal.vue';
import RegisterUserModal from '../components/RegisterUserModal.vue';
import UserCard from '../components/UserCard.vue';
</script>

<template>
    <RegisterUserModal
        v-if="isRegisterModal"
        @closeRegisterModal="toggleRegisterUserModal"
        :userIds="this.user_ids"
    />
    <DeleteUserModal
        v-if="isDeleteModal"
        @closeUserModal="toggleDeleteUserModal"
        @clickDeleteUser="deleteUser"
        :employeeId="this.idToDelete"
    />
    <div class="h-90pc w-full bg-slate-300">
        <div class="h-full w-full flex items-center justify-center">
            <div
                class="h-90pc w-95pc p-4 rounded-xl bg-slate-200 flex flex-wrap overflow-auto"
            >
                <UserCard
                    v-for="user in this.users"
                    :key="user.employeeId"
                    :name="user.name"
                    :position="user.position"
                    :createdAt="user.createdAt"
                    :image64="user.image64"
                    :employeeId="user.employeeId"
                    @openDeleteUserModal="toggleDeleteUserModal"
                />
                <div
                    @click="toggleRegisterUserModal"
                    class="m-2 rounded-xl w-40 h-72 bg-gray-900 flex justify-center items-center"
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
            // flag to show/hide modal component
            isRegisterModal: false,
            isDeleteModal: false,

            // list of registered users
            users: [],
            user_ids: [],
            
            idToDelete: null,
        };
    },
    // Components used in this view
    components: [UserCard, RegisterUserModal, DeleteUserModal],

    async created() {
        // retrieve registered user from database
        this.users = await this.getUser();

        // push all employeeId into user_ids list to avoid duplicated Id being created
        this.users.map((user) => {
            this.user_ids.push(user.employeeId);
        });
    },
    methods: {
        // toggle register user modal
        async toggleRegisterUserModal(e) {
            this.isRegisterModal = !this.isRegisterModal;
            // update the user list when register form is submitted
            setTimeout(this.updateUsers, 1000);
        },
        // toggle delete  user modal
        async toggleDeleteUserModal(employeeId) {
            this.idToDelete = employeeId;
            this.isDeleteModal = !this.isDeleteModal;
            // update the user list when user is deleted
            setTimeout(this.updateUsers, 1000);
        },

        // API call to get user list
        async getUser() {
            try {
                const res = await fetch(`http://localhost:8000/getUsers`);
                const data = await res.json();
                if (!res.ok) {
                    const message = `An error has occured: ${res.status} - ${res.statusText}`;
                    throw new Error(message);
                }
                return data;
            } catch (err) {
                console.log('ERROR: get user', err);
            }
        },

        // Update latest user list 
        async updateUsers() {
            const data = await this.getUser();

            // pop and push user to trigger rendering in Vue
            while (this.users.length > 0) {
                this.users.pop();
            }
            data.map((user, index) => {
                console.log(user);
                this.users.push(user);
            });
        },

        // API call to delete user in database
        async deleteUser(emitData) {
            try {
                let postData = {
                    employeeId: emitData.employeeId,
                };
                const res = await fetch(`http://localhost:8000/deleteUser`, {
                    method: 'post',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(postData),
                });
                const data = await res.json();
                if (!res.ok) {
                    const message = `An error has occured: ${res.status} - ${res.statusText}`;
                    throw new Error(message);
                }
            } catch (err) {
                console.log('ERROR: delete user', err);
            }
        },
    },
};
</script>

<style></style>