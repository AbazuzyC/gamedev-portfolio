<template>
  <div>
    <h1>Organizations</h1>

    <div style="margin-bottom: 30px;">
      I also joined some organizations to teach me about leadership and teamwork, and to meet new people. Here are some of the organizations I've been a part of.
    </div>
    <ProjectsList v-bind:projects="projects" />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import ProjectsList from "@/components/ProjectsList.vue";
import otherProjectsData from "@/data/OtherProjectsData";

export default Vue.extend({
  name: "OtherProjects",
  components: {
    ProjectsList,
  },
  data: function () {
    return {
      projects: otherProjectsData,
    };
  },
});
</script>
