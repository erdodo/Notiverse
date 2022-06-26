<template>
  <router-view></router-view>
</template>
<script>
// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyDjazmaMWs9AaiY7vRtxGk2DG2UfUgylhQ",
  authDomain: "notiverse-1edd0.firebaseapp.com",
  projectId: "notiverse-1edd0",
  storageBucket: "notiverse-1edd0.appspot.com",
  messagingSenderId: "11089025948",
  appId: "1:11089025948:web:eadc67fff7eae60e100659",
  measurementId: "G-M0EQ53KVK0"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);
</script>
<style>
.cursor-pointer {
  cursor: pointer;
}
</style>
