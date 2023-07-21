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
    <div v-if="error">
      <p>Error: {{ error.message }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userInfo: null,
      error: null,
    };
  },
  methods: {
    getUserInfo() {
      FB.login(
        (response) => {
          if (response.authResponse) {
            // Access token obtained, make the API call
            FB.api("/me", { fields: "id,name,link" }, (response) => {
              if (response.error) {
                this.error = response.error;
                console.log(response.error);
              } else {
                this.userInfo = response;
              }
            });
          } else {
            // User didn't authorize the app, handle this case
            console.log("User cancelled login or did not fully authorize.");
          }
        },
        { scope: "public_profile,user_link" } // Specify the permissions your app needs
      );
    },
  },
  mounted() {
    // The Facebook SDK initialization code remains the same
    window.fbAsyncInit = function () {
      FB.init({
        appId: "290647546838436", // Replace with your Facebook App ID
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
  },
};
</script>
