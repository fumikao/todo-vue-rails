<template>
  <div>
    <nav>
      <div class="nav-wrapper container">
        <router-link to="/" class="brand-logo left">Todo Application</router-link>
        <ul class="right hide-on-med-and-down">
          <li><router-link to="/">Top</router-link></li>
          <li><router-link to="/about">About</router-link></li>
          <li><router-link to="/contact">Contact</router-link></li>
        </ul>
        <a class="right hide-on-large-only dropdown-trigger" href="#" data-target="dropdown">
            Menu<i class="material-icons right">arrow_drop_down</i>
        </a>
        <ul id="dropdown" class="dropdown-content">
          <li><router-link to="/">Top</router-link></li>
          <li><router-link to="/about">About</router-link></li>
          <li><router-link to="/contact">Contact</router-link></li>
        </ul>
      </div>
    </nav>
  </div>
</template>

<script>
document.addEventListener('DOMContentLoaded', function() {
  var elems = document.querySelectorAll('.dropdown-trigger');
  var instances = M.Dropdown.init(elems);
});
</script>