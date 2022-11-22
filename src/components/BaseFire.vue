<template>
  <div>
    <div v-for="(form, index) in forms" :key="index">
      <button @click="beginQuiz(index)">
        {{ form.title }}
      </button>
    </div>
  </div>
</template>

<script>
import { initializeApp } from "firebase/app";
import { getFirestore, collection, getDocs, addDoc, doc, onSnapshot } from "firebase/firestore";
import { db } from "../db";

export default {
  name: "BaseFire",
  data() {
    return {
      app: null,
      db: null,
      forms: {},
      unsubscribeForms: null,
    };
  },
  mounted() {
    this.unsubscribeForms = onSnapshot(collection(db, "forms"), (query) => {
      query.forEach((doc) => {
        this.forms = {
          ...this.forms,
          [doc.id]: doc.data(),
        };
      });
    })
  },
  methods: {
    async fetchForms() {
      await getDocs(collection(db, "forms")).then((query) => {
        query.forEach((doc) => {
          this.forms = {
            ...this.forms,
            [doc.id]: doc.data(),
          };
        });
      });
    },
    beginQuiz(formId) {
      this.$emit('beginQuiz', this.forms[formId]);
    }
  },
};
</script>
