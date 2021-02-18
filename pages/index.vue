<template>
  <div>
    <div class="container">
      <input
        class="form-control my-3"
        type="text"
        id="myInput"
        v-model="search_box"
        placeholder="Search for users.."
      />
      <table id="myTable" class="table text-white text-center">
        <thead>
          <tr>
            <th scope="col">User Image</th>
            <th scope="col">First Name</th>
            <th scope="col">Last Name</th>
            <th scope="col">Email</th>
            <th>Options</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(user, index) in filterSearch" :key="user.id">
            <td><img class="w-25" :src="user.avatar" alt="" /></td>
            <td>{{ user.first_name }}</td>
            <td>{{ user.last_name }}</td>
            <td>{{ user.email }}</td>
            <td>
              <button
                class="btn btn-primary"
                v-b-modal.modal-2
                @click="editUserForm(index)"
              >
                Edit
              </button>
              <button
                v-b-modal.modal-3
                @click="deleteUserRequest(index)"
                class="btn btn-danger"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <button class="btn btn-success" @click="emptyAddUser()" v-b-modal.modal-1>
        Add User
      </button>
    </div>
    <div>
      <b-modal hide-footer hide-header ref="modal" id="modal-1">
        <div class="text-dark">
          <h3 class="text-center">Add User</h3>
          <div class="form-group">
            <label for="new_first_name">First name</label>
            <input
              id="new_first_name"
              type="text"
              class="form-control"
              v-model="new_first_name"
            />
          </div>
          <div class="form-group">
            <label for="new_last_name">Last name</label>
            <input
              id="new_last_name"
              type="text"
              class="form-control"
              v-model="new_last_name"
            />
          </div>
          <div class="form-group">
            <label for="new_email_addres">Email Address</label>
            <input
              id="new_email_addres"
              type="email"
              class="form-control"
              v-model="new_email"
            />
          </div>
          <dir class="text-center">
            <button
              :disabled="
                new_first_name == '' || new_email == '' || new_last_name == ''
              "
              class="btn btn-success"
              @click="addUser(), hideModal()"
            >
              Add New User
            </button>
            <button @click="hideModal()" class="btn btn-secondary">
              cansel
            </button>
          </dir>
        </div>
      </b-modal>
    </div>
    <div>
      <b-modal hide-footer hide-header ref="modal" id="modal-2">
        <div class="text-dark">
          <h3 class="text-center">Edit User</h3>
          <div class="form-group">
            <label for="edited_User_Image">User Image src: </label>
            <input
              id="edited_User_Image"
              type="text"
              class="form-control"
              v-model="edited_avatar"
            />
          </div>
          <div class="form-group">
            <label for="edited_first_name">first name:</label>
            <input
              id="edited_first_name"
              type="text"
              class="form-control"
              v-model="edited_first_name"
            />
          </div>
          <div class="form-group">
            <label for="edited_last_name">Last name:</label>
            <input
              id="edited_last_name"
              type="text"
              class="form-control"
              v-model="edited_last_name"
            />
          </div>
          <div class="form-group">
            <label for="edited_email">Email address</label>
            <input
              id="edited_email"
              type="email"
              class="form-control"
              v-model="edited_email"
            />
          </div>
          <div class="text-center">
            <button
              :disabled="
                edited_avatar == '' ||
                edited_first_name == '' ||
                edited_last_name == '' ||
                edited_email == ''
              "
              class="btn btn-success"
              @click="editUser(), hideModal()"
            >
              Save changes
            </button>
            <button @click="hideModal()" class="btn btn-secondary">
              cansel
            </button>
          </div>
        </div>
      </b-modal>
    </div>
    <div>
      <b-modal hide-footer hide-header ref="modal" id="modal-3">
        <div class="text-center">
          <h3 class="text-danger">Are you sure ?</h3>
          <p class="text-dark mb-5">
            you will not be able to recover
            {{ remove_first_name }} {{ remove_last_name }}!
          </p>
          <button @click="hideModal(), deleteUser()" class="btn btn-danger">
            yes delete it !
          </button>
          <button @click="hideModal()" class="btn btn-secondary">cansel</button>
        </div>
      </b-modal>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [],
      new_first_name: "",
      new_last_name: "",
      new_email: "",
      edited_first_name: "",
      edited_last_name: "",
      edited_email: "",
      edited_avatar: "",
      edited_index: 0,
      remove_User_index: 0,
      remove_first_name: "",
      remove_last_name: "",
      search_box: "",
    };
  },
  created() {
    this.listOfUsers();
  },
  computed: {
    //filter for searching
    filterSearch() {
      console.log(this.users);
      return this.users.filter((user) => {
        return (
          user.first_name
            .toLowerCase()
            .includes(this.search_box.toLowerCase()) ||
          user.last_name
            .toLowerCase()
            .includes(this.search_box.toLowerCase()) ||
          user.email.toLowerCase().includes(this.search_box.toLowerCase())
        );
      });
    },
  },
  methods: {
    hideModal() {
      this.$refs["modal"].hide();
    },
    //get list of Users
    listOfUsers() {
      var self = this;
      this.$axios
        .$get("/users?page=1")
        .then(function (response) {
          // handle success

          self.users = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    },
    //empty add user form
    emptyAddUser() {
      this.new_first_name = "";
      this.new_last_name = "";
      this.new_email = "";
    },
    // add a new user to the list
    addUser() {
      let new_user = {};
      new_user.first_name = this.new_first_name;
      new_user.last_name = this.new_last_name;
      new_user.email = this.new_email;
      this.users.push(new_user);
      this.new_first_name = "";
      this.new_last_name = "";
      this.new_email = "";
    },
    // pass data of selected user to delete alert
    deleteUserRequest(index) {
      this.remove_User_index = index;
      this.remove_first_name = this.users[index].first_name;
      this.remove_last_name = this.users[index].last_name;
    },
    //delete selected user
    deleteUser() {
      this.users.splice(this.remove_User_index, 1);
    },

    // pass data of selected user to edit form
    editUserForm(index) {
      this.edited_avatar = this.users[index].avatar;
      this.edited_first_name = this.users[index].first_name;
      this.edited_last_name = this.users[index].last_name;
      this.edited_email = this.users[index].email;
      this.edited_index = index;
    },

    //edit the selected user
    editUser() {
      this.users[this.edited_index].first_name = this.edited_first_name;
      this.users[this.edited_index].last_name = this.edited_last_name;
      this.users[this.edited_index].email = this.edited_email;
      this.users[this.edited_index].avatar = this.edited_avatar;
      this.edited_first_name = "";
      this.edited_last_name = "";
      this.edited_email = "";
      this.edited_avatar = "";
    },
  },
};
</script>

