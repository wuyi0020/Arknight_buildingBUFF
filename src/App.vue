<script setup>
import { RouterLink, RouterView } from "vue-router";
import HelloWorld from "./components/HelloWorld.vue";
import { ref, onMounted } from "vue";
</script>

<template>
    <div style="width: 100%">
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
            <a class="navbar-brand mx-1" href="#"><router-link to="/">Arknight</router-link></a>

            <button
                class="navbar-toggler"
                type="button"
                data-bs-toggle="collapse"
                data-bs-target="#navbarNav"
                aria-controls="navbarNav"
                aria-expanded="false"
                aria-label="Toggle navigation"
            >
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                    <li class="nav-item mx-1">
                        <router-link to="/">Home</router-link>
                    </li>
                    <li class="nav-item mx-1">
                        <router-link to="bulid">基建技能</router-link>
                    </li>
                </ul>
            </div>
        </nav>
        <div class="container-fluid text-white ">
            <div class="row justify-content-center">
                <div class="col-12 col-lg-10 ">
                    <div class="router-container">
                        <router-view></router-view>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
.nav-item > a{
    text-decoration: none;
    color: white;
    padding:5px ;
}
.navbar-brand>a{
    text-decoration: none;
    color: white;
    padding:5px ;
}
.navbar > a{
    padding:5px ;
}
</style>
