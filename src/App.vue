<template>
  <div
    class="bg-stone-800 h-screen text-white font-semibold text-2xl flex items-center justify-center"
  >
    <div v-if="userInfo" class="-rotate-90 text-center text-3xl text-stone-200">
      Gathered Information
      <span v-if="WebVersion" class="text-yellow-100">V-{{ WebVersion }}</span>
    </div>
    <div v-if="userInfo">
      <p>
        User ID: <span class="hover:text-2xl">{{ userInfo.id }}</span>
      </p>
      <br />
      <p>
        Name: <span class="hover:text-blue-400">{{ userInfo.name }}</span>
      </p>
      <br />
      <a :href="userInfo.link" class="hover:text-blue-400" target="_blank"
        >Click here to Open Profile Link</a
      >
    </div>
    <br />
    <div
      v-if="!userInfo"
      @click="getUserInfo"
      class="flex flex-col gap-8 justify-center items-center p-6 border-2 border-stone-400 rounded-2xl cursor-pointer hover:border-stone-50"
    >
      <img
        src="https://cdn-icons-png.flaticon.com/512/2504/2504903.png"
        class="h-24 w-24"
      />
      <button class="">Facebook Auth</button>
    </div>
    <div v-if="error">
      <p>Error: {{ error.message }}</p>
    </div>
  </div>
</template>

<script>
import { version } from "../package.json";
export default {
  data() {
    return {
      userInfo: null,
      error: null,
      WebVersion: null,
    };
  },
  methods: {
    getUserInfo() {
      FB.getLoginStatus((response) => {
        if (response.status === "connected") {
          // The user is already logged in, force the login dialog
          FB.login(
            (response) => {
              console.log("FB.login" + response);
              if (response.authResponse) {
                // Access token obtained, make the API call
                FB.api("/me", { fields: "id,name,link" }, (response) => {
                  if (response.error) {
                    this.error = response.error;
                    console.log(response.error);
                  } else {
                    console.log("Main Response" + response);
                    this.WebVersion = version;

                    this.userInfo = response;
                  }
                });
              } else {
                // User didn't authorize the app, handle this case
                console.log("User cancelled login or did not fully authorize.");
                console.log("Else Response" + response);
              }
            },
            {
              scope: "public_profile,user_link",
              auth_type: "rerequest",
              forceServerAuth: true,
            }
          );
        } else {
          // The user is not logged in or hasn't authorized the app, show login dialog
          FB.login(
            (response) => {
              if (response.authResponse) {
                // Access token obtained, make the API call
                FB.api("/me", { fields: "id,name,link" }, (response) => {
                  if (response.error) {
                    this.error = response.error;
                    console.log(response.error);
                  } else {
                    console.log("API 2nd Login:" + JSON.stringify(response));
                    this.WebVersion = version;
                    this.userInfo = response;
                  }
                });
              } else {
                // User didn't authorize the app, handle this case
                console.log("User cancelled login or did not fully authorize.");
              }
            },
            { scope: "public_profile,user_link", auth_type: "rerequest" }
          );
        }
      });
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
