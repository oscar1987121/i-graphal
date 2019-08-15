<template>
  <div id="app">
    <div class="project-bar">
      <ul class="project-list">
        <li>
          <router-link to="/i-graphical">i-graphical</router-link>
        </li>
        <li>
          <router-link to="/">other-1</router-link>
        </li>
        <li>
          <router-link to="/">other-2</router-link>
        </li>
      </ul>
    </div>
    <div class="view">
      <router-view  />
    </div>
    
  </div>
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}

html,
body {
  height: 100%;
}

.view{
  position: absolute;
  width: 100%;
  height: 100%;
}
.project-bar {
  width: 100%;
  height: 3em;
  background-color: black;
  color: rgb(150, 130, 130);
}
.project-list {
  list-style: none;
  margin: 0 auto;
  height: 100%;
  display: flex;
  
  li {
    padding: 10px;
    a {
      cursor: pointer;
      text-decoration: none;
      color: inherit;
    }
  }
  li:hover {
    background-color: rgb(150, 130, 130);
    color: black;
  }
}
</style>
