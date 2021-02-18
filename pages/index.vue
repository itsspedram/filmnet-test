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
            <th scope="col">avtar</th>
            <th scope="col">first name</th>
            <th scope="col">last name</th>
            <th scope="col">email</th>
            <th colspan="2">edit / delete</th>
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
                edit
              </button>
              <button
                v-b-modal.modal-3
                @click="removeUserRequest(index)"
                class="btn btn-danger"
              >
                delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <button class="btn btn-success" @click="emptyAddUser()" v-b-modal.modal-1>
        add user
      </button>
    </div>
    <div>
      <b-modal hide-footer ref="modal" id="modal-1">
        <div class="text-dark">
          <h1 class="text-center">add user</h1>
          <div class="form-group">
            <label for="exampleInputEmail1">first name</label>
            <input type="text" class="form-control" v-model="new_first_name" />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">Last name</label>
            <input type="text" class="form-control" v-model="new_last_name" />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">Email address</label>
            <input type="email" class="form-control" v-model="new_email" />
          </div>

          <button
            :disabled="
              new_first_name == '' || new_email == '' || new_last_name == ''
            "
            type="submit"
            class="btn btn-primary"
            @click="adduser(), hideModal()"
          >
            Add New User
          </button>
        </div>
      </b-modal>
    </div>
    <div>
      <b-modal hide-footer ref="modal" id="modal-2">
        <div class="text-dark">
          <h1 class="text-center">edit user</h1>
          <div class="form-group">
            <label for="exampleInputEmail1">avatar src: </label>
            <input type="text" class="form-control" v-model="edited_avatar" />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">first name:</label>
            <input
              type="text"
              class="form-control"
              v-model="edited_first_name"
            />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">Last name:</label>
            <input
              type="text"
              class="form-control"
              v-model="edited_last_name"
            />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">Email address</label>
            <input type="email" class="form-control" v-model="edited_email" />
          </div>

          <button
            :disabled="
              edited_avatar == '' ||
              edited_first_name == '' ||
              edited_last_name == '' ||
              edited_email == ''
            "
            class="btn btn-primary"
            @click="editUser(), hideModal()"
          >
            Save changes
          </button>
        </div>
      </b-modal>
    </div>
    <div>
      <b-modal hide-footer ref="modal" id="modal-3">
        <div class="text-center">
          <h5 class="text-danger">Are you sure about removing</h5>
          <h5 class="text-dark mb-5">
            {{ remove_first_name }} {{ remove_last_name }}
          </h5>
          <button @click="hideModal(), removeUser()" class="btn btn-success">
            yes
          </button>
          <button @click="hideModal()" class="btn btn-danger">no</button>
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
      edited_user_id: 0,
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

    emptyAddUser() {
      this.new_first_name = "";
      this.new_last_name = "";
      this.new_email = "";
    },
    
    adduser() {
      let new_user = {};
      new_user.first_name = this.new_first_name;
      new_user.last_name = this.new_last_name;
      new_user.email = this.new_email;
      this.users.push(new_user);
      this.new_first_name = "";
      this.new_last_name = "";
      this.new_email = "";
    },
    removeUserRequest(index) {
      this.remove_User_index = index;
      this.remove_first_name = this.users[index].first_name;
      this.remove_last_name = this.users[index].last_name;
    },
    removeUser() {
      this.users.splice(this.remove_User_index, 1);
    },
    editUserForm(index) {
      this.edited_avatar = this.users[index].avatar;
      this.edited_first_name = this.users[index].first_name;
      this.edited_last_name = this.users[index].last_name;
      this.edited_email = this.users[index].email;
      this.edited_index = index;
    },

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

<style>
</style>
