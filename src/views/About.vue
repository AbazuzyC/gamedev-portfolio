<template>
  <div style="margin-bottom: 80px;">
    <h1>Hello!</h1>

    <div class="paragraph">
      <div>
        I'm <strong>Abu Fadel Abas</strong>, a game designer with some games of experience who love to create video games and compete.<br/>
        <br/>
        I'm also a Founder for <a href="https://www.instagram.com/gamitech_club/">Gamitech Club</a>, Game Development Club at State University Of Jakarta and I love discussing, designing and developing games in general.
      </div>

      <div style="margin-top: 20px;">I've worked on  <router-link to="/game-projects">Games</router-link>, and took part in <router-link to="/other-projects">a few things</router-link> as well.</div>

      <div style="margin-top: 40px;">I'm <strong>currently looking for a job</strong> as a game designer. You can reach me at <a href="mailto:abufadelabas20@gmail.com">abufadelabas20@gmail.com</a> or <router-link to="/contact">through here</router-link>.</div>
    </div>

    <div class="photo">
      <img src="img/avatar.png" alt="Avatar of Abazuzy" /> 
    </div>

    <div style="clear:both"></div>

  </div>
</template>

<style scoped>
.paragraph {
  max-width: 700px;
  margin-bottom: 20px;
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
    padding: 10px;
    padding-left: 80px;
  }
}

</style>