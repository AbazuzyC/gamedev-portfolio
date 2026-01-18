<template>
  <div>
    <h1>Resume</h1>

    <div class="paragraph">
      I'm <strong>Abu Fadel Abas</strong>, Founder of Gamitech Club and a student of
      <strong>Informatics and Computer Engineering Education</strong> at
      <strong>State University of Jakarta (UNJ)</strong>.
      <br />
      I focus on <strong>game development and game design</strong>, with hands-on
      experience leading teams, managing projects, and delivering playable games.
      <br />
      I have led multiple student organizations and game projects, combining
      leadership, technical execution, and creative direction.
      <br />
      <br />
      I'm currently open to collaborations, projects, and opportunities related to
      game development, events, and digital products. Feel free to
      <router-link to="/contact">contact me</router-link>.
      <br />
      <br />
      Main interests: game design, leadership, project management, and interactive media.

      <div style="margin-top:20px; margin-bottom:20px;">
        <a class="download-link" href="d/abu-fadel-abas-resume.pdf" target="_blank">
          <i class="fa fa-download fa-lg fa-fw"></i> Download Resume (PDF)
        </a>
        <div style="clear:both"></div>
      </div>
    </div>

    <div class="photo">
      <img src="img/avatar.png" alt="Photo of Abu Fadel Abas" />
    </div>

    <div style="clear:both"></div>

    <div class="full-content">
      <h2>Experience & Organizations</h2>

      <h3>Gamitech Club</h3>
      <h4>Founder / Leader — 01/2024 – Present</h4>
      <div>
        Founded and led the first game development club at State University of Jakarta,
        growing the community to 35+ active members.
      </div>
      <div>
        Directed 8+ game projects by coordinating game designers, programmers,
        and artists.
      </div>
      <div class="tech-stack">
        Leadership, Game Design, Project Management, Unity, Team Coordination
      </div>

      <h3>Badan Eksekutif Mahasiswa Prodi PTIK</h3>
      <h4>Deputy Head, Interest & Talent Division — 03/2024 – Present</h4>
      <div>
        Led and organized multiple student events and competitions.
      </div>
      <ul>
        <li>Event Lead: Organize For Fun Tournament (30+ participants)</li>
        <li>Head of Events: Sekolah Prestasi (60+ attendees)</li>
        <li>Head of Events: PELIK (80+ attendees)</li>
      </ul>
      <div class="tech-stack">
        Event Management, Public Speaking, Team Leadership
      </div>

      <h3>Forum Ketua Angkatan FT</h3>
      <h4>Ketua Angkatan PTIK 2024 — 11/2023 – Present</h4>
      <div>
        Facilitated coordination and engagement among 40+ students,
        acting as the main representative for the cohort.
      </div>
      <div class="tech-stack">
        Leadership, Communication, Coordination
      </div>

      <h2>Skills</h2>

      <div class="skill-set">
        <h3>Core Skills</h3>
        <SkillRate name="Leadership" :rate="5" />
        <SkillRate name="Game Design" :rate="5" />
        <SkillRate name="Project Management" :rate="4" />
        <SkillRate name="Problem Solving" :rate="4" />
        <SkillRate name="Public Speaking" :rate="4" />
      </div>

      <div class="skill-set">
        <h3>Technical</h3>
        <SkillRate name="Unity" :rate="4" />
        <SkillRate name="C# Programming" :rate="3" />
        <SkillRate name="Microsoft Office" :rate="4" />
      </div>

      <div style="clear:both" />

      <h2>Education</h2>

      <h3>State University of Jakarta (UNJ)</h3>
      <h4>Informatics and Computer Engineering Education</h4>
      <div>2024 – Present · Jakarta, Indonesia</div>

      <h2>Awards</h2>

      <h3>1st Place — BGD Jam 2025</h3>
      <div>Binus University · January 2025</div>

      <h3>1st Place — April Fools' Day Jam 3</h3>
      <div>Kyjor · April 2024</div>

      <h2>Contact</h2>

      <div>
        Email: <a href="mailto:abufadelabas20@gmail.com">abufadelabas20@gmail.com</a><br />
        Phone: <a href="tel:+6285695746954">+62 856-9574-6954</a><br />
        Location: Jakarta, Indonesia
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import SkillRate from "@/components/SkillRate.vue";

export default Vue.extend({
  name: "Resume",
  components: {
    SkillRate,
  },
});
</script>

<style scoped>
h2 {
  font-weight: 100;
  font-size: 2em;
  margin: 0px;
  padding: 0px;
  padding-top: 50px;
}
h3 {
  font-weight: 100;
  font-size: 1.6em;
  margin: 0px;
  padding: 0px;
  padding-top: 30px;
}
h4 {
  font-weight: 100;
  font-size: 1.3em;
  margin: 0px;
  padding: 0px;
  padding-top: 15px;
  font-style: italic;
}

.paragraph {
  max-width: 700px;
}

.download-link {
  float:left;
  margin-right: 50px;
}

.full-content div {
  max-width: 800px;
}

.skill-set h3 {
  padding-bottom: 10px;
}

.photo img {
  height: 300px;
  width: 300px;
}

.tech-stack {
  font-style: italic;
}

.photo {
  margin-top: 50px;
  text-align: center;
}

@media only screen and (min-width: 620px){
  .paragraph {
    float: left;
  }

  .photo {
    float: left;
    padding: 30px;
    padding-left: 80px;
  }

  .skill-set {
    float: left;
    padding-right: 60px; 
  }
}
</style>