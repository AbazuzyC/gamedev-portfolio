<template>
  <div>
    <h1>Game Projects</h1>

    <div style="margin-bottom: 30px;">
      I really love making games, and I have a lot of fun doing it. Here are some of the projects I've worked on, i hope you guys can try it
    </div>

    <ProjectsList v-bind:projects="projects" />

    <div style="margin-top: 20px;">
      There is more to see on <a target="_blank" href="https://abazuzy.itch.io">itch.io</a>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import ProjectsList from "@/components/ProjectsList.vue";
import gameProjectsData from "@/data/GameProjectsData";

export default Vue.extend({
  name: "GameProjects",
  components: {
    ProjectsList,
  },
  data: function () {
    return {
      projects: gameProjectsData,
    };
  },
});
</script>
