<template>
  <div id="app">
    <nav class="navbar navbar-transparent navbar-fixed-top" role="navigation">
      <div class="container">
        <div class="collapse navbar-collapse">
          <ul class="nav navbar-nav navbar-right">
            <li>
              <a target="_blank" href="https://www.linkedin.com/in/daniel-altamiro/"><i class="fa fa-linkedin"></i> Linkedin</a>
            </li>
            <li>
              <a target="_blank" href="https://github.com/ODanAltamiro"><i class="fa fa-github"></i> GitHub</a>
            </li>
            <li>
              <a target="_blank" href="mailto:daniel.altamiro@hotmail.com"><i class="fa fa-envelope-o"></i> Email</a>
            </li>
          </ul>
        </div>
      </div>
    </nav>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding: 60px 0;
  position: fixed;
  opacity: 1;
  background-color: rgba(0,0,0,.6);
  left: 0px;
  top: 0px;
  width: 100%;
  height: 100%;
  z-index: 1;
}
</style>
