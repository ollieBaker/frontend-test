<template>
  <v-container fluid>
    <h1>A simple editable list using Vue.js & contenteditable</h1>
    <v-slide-y-transition mode="out-in">
      <v-layout column align-left>
        <v-list
          v-for="user in users"
          :key="user.id"
          >
            <v-list-tile-content @click="beginEdit(user.id)" :data-editing="editing === user.id">
              <v-list-tile-sub-title>{{`User ID: ${user.id}`}}</v-list-tile-sub-title>
              <v-list-tile-sub-title 
                v-text="user.name"
                contenteditable="true" 
                @blur="e => editUser(e.target, 'name', user.id)">
              </v-list-tile-sub-title>
              <v-list-tile-sub-title 
                contenteditable="true" 
                @blur="e => editUser(e.target, 'email', user.id)">
                {{ user.email }} 
              </v-list-tile-sub-title>
              <v-list-tile-sub-title 
                contenteditable="true" 
                @blur="e => editUser(e.target, 'company.name', user.id)">
                {{ user.company.name }}
              </v-list-tile-sub-title>
              <v-slide-y-transition>
                <v-btn v-if="editing === user.id" @click="e => updateUser(e, user.id)">Send</v-btn>
              </v-slide-y-transition>
            </v-list-tile-content>
             <v-divider
              :key="user.Id"
            ></v-divider>
        </v-list>
      </v-layout>
    </v-slide-y-transition>
  </v-container>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    return {
      users: [],
      editing: null
    };
  },
  created() {
    this.getUsers();
  },
  methods: {
    editUser(target, key, id) {
      const user = this.users.find(user => user.id == id);
      this.$set(user, key, target.innerText);
    },
    beginEdit(id) {
      this.editing = id;
    },
    getUsers() {
      this.$http
        .get("https://jsonplaceholder.typicode.com/users")
        .then(response => (this.users = response.data));
    },
    updateUser(e, id) {
      e.stopPropagation();

      const userIndex = this.users.findIndex(user => user.id == id);
      this.editing = null;
      this.$http
        .put(
          `https://jsonplaceholder.typicode.com/users/${id}`,
          this.users[userIndex]
        )
        .then(response => {
          this.$set(this.users, userIndex, response.data);
        })
        .catch(() => {
          this.editing = id;
        });
    }
  }
};
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
div[contenteditable] {
  cursor: pointer;
}
div[data-editing] div[contenteditable] {
  cursor: auto;
}
div[contenteditable] {
  cursor: pointer;
}
</style>
