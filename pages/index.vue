<template>
  <div>
    <div class="container">
      <div v-for="(user, index) in users" :key="user.id" class="row">
        <div class="col-2">{{ user.first_name }}</div>
        <div class="col-2">{{ user.last_name }}</div>
        <div class="col-3">{{ user.email }}</div>
        <div class="col-2">
          <button @click="removeUser(index)">delete</button>
        </div>
        <div class="col-2"><button>edit</button></div>
      </div>
    </div>

    <form>
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
        @click="adduser()"
      >
        Submit
      </button>
    </form>
    {{ users }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: "",
      new_name: "",
      new_last_name: "",
      new_email: "",
      remove_user_id: 0,
      edited_user_id: 0,
      edited_name: "",
      edited_last_name: "",
      edited_email: "",
    };
  },
  mounted() {
    this.listOfUsers();
  },
  methods: {
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
    },
    removeUser(indexOf) {
      this.users.splice(indexOf, 1);
    },
    editUser() {},
  },
};
</script>

<style>
</style>
