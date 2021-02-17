<template>
  <div>
    <div class="container">
      <input
        type="text"
        id="myInput"
        v-model="search_box"
        placeholder="Search for names.."
        title="Type in a name"
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
          <tr v-for="(user, index) in users" :key="user.id">
            <td><img class="w-25" :src="user.avatar" alt="" /></td>
            <td>{{ user.first_name }}</td>
            <td>{{ user.last_name }}</td>
            <td>{{ user.email }}</td>
            <td>
              <button
                v-b-modal.modal-2
                @click="
                  editUserForm(
                    user.avatar,
                    user.first_name,
                    user.last_name,
                    user.email,
                    index
                  )
                "
              >
                edit
              </button>
              <button @click="removeUser(index)">delete</button>
            </td>
          </tr>
        </tbody>
      </table>
      <button class="btn btn-success" v-b-modal.modal-1>add user</button>
    </div>
    <div>
      <b-modal hide-footer ref="modal-1" id="modal-1">
        <div class="text-dark">
          <h1 class="text-center">add user</h1>
          <div class="form-group">
            <label for="exampleInputEmail1">first name</label>
            <input type="text" class="form-control" v-model="new_name" />
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
            :disabled="new_name == '' || new_email == '' || new_last_name == ''"
            type="submit"
            class="btn btn-primary"
            @click="adduser(), hideModalAddUsers()"
          >
            Add New User
          </button>
        </div>
      </b-modal>
    </div>
    <div>
      <b-modal hide-footer ref="modal-2" id="modal-2">
        <div class="text-dark">
          <h1 class="text-center">edit user</h1>
          <div class="form-group">
            <label for="exampleInputEmail1">avatar </label>
            <input type="text" class="form-control" v-model="edited_avatar" />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">first name</label>
            <input
              type="text"
              class="form-control"
              v-model="edited_first_name"
            />
          </div>
          <div class="form-group">
            <label for="exampleInputEmail1">Last name</label>
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
            @click="editUser(), hideModaleditUsers()"
          >
            Save changes
          </button>
        </div>
      </b-modal>
    </div>
  </div>
</template>

<script>
// vue.filter("filterSearch", function () {
//   return this.users.filter((user) => {
//     return user.first_name.match(this.search_box);
//   });
// });
export default {
  data() {
    return {
      users: "",
      new_name: "",
      new_last_name: "",
      new_email: "",
      remove_user_id: 0,
      edited_user_id: 0,
      edited_first_name: "",
      edited_last_name: "",
      edited_email: "",
      edited_avatar: "",
      edited_index: 0,
      search_box: "",
    };
  },
  created() {
    this.listOfUsers();
  },
  computed: {
    // filterSearch: function () {
    //   return this.users.filter((user) => {
    //     return user.first_name.match(this.search_box);
    //   });
    // },
  },
  methods: {
    hideModalAddUsers() {
      this.$refs["modal-1"].hide();
    },
    hideModaleditUsers() {
      this.$refs["modal-2"].hide();
    },
    listOfUsers() {
      var self = this;
      this.$axios
        .$get("/users?page=1")
        .then(function (response) {
          // handle success
          console.log(response);
          //   self.users.push(response.data);
          self.users = response.data;
        })
        .catch(function (error) {
          // handle error
          console.log(error);
        });
    },
    adduser() {
      let new_user = {};
      //new_user.push('"first_name"' + ":" + this.add_name);
      new_user.first_name = this.new_name;
      //   new_user.push('"last_name"' + ":" + this.new_last_name);
      new_user.last_name = this.new_last_name;

      //   new_user.push('"email"' + ":" + this.new_email);
      new_user.email = this.new_email;
      this.users.push(new_user);
      this.new_name = "";
      this.new_last_name = "";
      this.new_email = "";
    },
    removeUser(indexOf) {
      this.users.splice(indexOf, 1);
    },
    editUserForm(avatar, first_name, last_name, email, index) {
      this.edited_avatar = avatar;
      this.edited_first_name = first_name;
      this.edited_last_name = last_name;
      this.edited_email = email;
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
