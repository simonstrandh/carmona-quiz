<template>
  <div>
    <input
      id="name"
      v-model="username"
      type="text"
      name="name"
      placeholder="username"
    >
    <button @click="setUsername">
      Set username
    </button>
  </div>
</template>

<script>
import {
  onAuthStateChanged,
  signInAnonymously,
  updateProfile,
} from "firebase/auth";
import { firebaseAuth } from "../db";

export default {
  name: "UserName",
  data() {
    return {
      username: "",
      displayName: "",
    };
  },
  mounted() {
    onAuthStateChanged(firebaseAuth, (user) => {
      if (user) {
        this.username = user.displayName;
      }
    });

    signInAnonymously(firebaseAuth).then(() => {
      console.log("Signed in");
    });
  },
  methods: {
    setUsername() {
      updateProfile(firebaseAuth.currentUser, {
        displayName: this.username,
      })
        .then(() => {})
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>
