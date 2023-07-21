<template>
  <div>
    <div v-if="userInfo">
      <p>User ID: {{ userInfo.id }}</p>
      <p>Name: {{ userInfo.name }}</p>
      <p>Email: {{ userInfo.email }}</p>
      <p>First Name: {{ userInfo.first_name }}</p>
      <p>Last Name: {{ userInfo.last_name }}</p>
    </div>
    <button @click="getUserInfo">Get User Info</button>
  </div>
</template>
<script>
window.fbAsyncInit = function () {
  FB.init({
    appId: "290647546838436",
    cookie: true,
    xfbml: true,
    version: "v17.0",
  });

  FB.AppEvents.logPageView();
};

(function (d, s, id) {
  var js,
    fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) {
    return;
  }
  js = d.createElement(s);
  js.id = id;
  js.src = "https://connect.facebook.net/en_US/sdk.js";
  fjs.parentNode.insertBefore(js, fjs);
})(document, "script", "facebook-jssdk");

export default {
  data() {
    return {
      userInfo: null,
    };
  },
  methods: {
    getUserInfo() {
      FB.api(
        "/me",
        { fields: "id,name,email,first_name,last_name" },
        (response) => {
          if (response.error) {
            console.log(response.error);
          } else {
            this.userInfo = response;
          }
        }
      );
    },
  },
};
</script>
