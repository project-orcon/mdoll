<template>
  <v-container fluid class="pa-0">
    <v-row no-gutters>
      <v-col cols="12" v-show="showVideo">
        <iframe
          id="video"
          src="https://www.youtube.com/embed/lRekYc2ppss?start=115&end=145&rel=0&autoplay=1&mute=1&showinfo=0&iv_load_policy=3&controls=0&enablejsapi=1"
          frameborder="0"
          allowfullscreen
          class="youtubeVideo"
        ></iframe>
      </v-col>
      <v-col cols="12" v-show="!showVideo">
        <div
          class="mainVideoImage"
          :style="{ backgroundImage: 'url(\'' + require('@/assets/darker-pic.jpeg') + '\')' }"
        >
          <transition name="slide-fade">
            <div
              style="transform: rotate(-10deg);"
              v-show="!showVideo"
              class="innerText outline-text"
            >
              Dr Rittersporn
              <br />Tattoo Artist
              <br />Prenzlauer Berg
            </div>
          </transition>
        </div>
      </v-col>
      <v-col cols="12" class="text-center">
        <div class="main-heading">About</div>
        <br />
        <img src="@/assets/michaelprofile.jpg" class="profile-pic" />
        <br />
        <div class="my-4">
          <strong>Michael aka Dr. Rittersporn</strong>
          <br />Tattoo Artist, Philospher & Writer
          <br />
          <br />
          <div class="paragraph">
            Hi I'm Michael, I've been working as a tattoo artist since 2011 in Germany.
            Currently working out of Prenzlauer Berg, Berlin.
          </div>
        </div>
      </v-col>
      <v-col cols="12" class="text-center">
        <div class="main-heading">Contact</div>
        <br />Email me to discuss your next tattoo!
        <br />
        <strong style="text-transform:uppercase">therittersporn@gmail.com</strong>
      </v-col>

      <v-col cols="12 text-center">
        <div class="main-heading">Instagram feed</div>
        <br />
        <a
          class="white--text"
          href="https://www.instagram.com/theblackworkartist/"
          style="text-decoration:none"
        >@theblackworkartist</a>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-card class="black" href="https://www.instagram.com/theblackworkartist/">
          <v-container class="black">
            <v-row>
              <v-col v-for="item in instagramArray" :key="item" class="d-flex child-flex" cols="4">
                <v-card flat tile class="d-flex">
                  <v-img :src="item" aspect-ratio="1" class="grey lighten-2">
                    <template v-slot:placeholder>
                      <v-row class="fill-height ma-0" align="center" justify="center">
                        <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                      </v-row>
                    </template>
                  </v-img>
                </v-card>
              </v-col>
            </v-row>
          </v-container>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" class="text-center mt-5">
        <div class="main-heading mb-5">Online Store</div>
        <a
          href="https://www.redbubble.com/people/blackworkartist"
          class="white--text"
          style="text-decoration:none"
        >
          <img src="@/assets/redbubble.png" width="100px" style="background-color:white; border-radius:50%;padding:10px" />
          <br />
          <br />Online Store @ redbubble.com
        </a>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" class="text-center">
        <div class="main-heading mb-5">&copy; Dr Rittersporn</div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";

export default {
  mounted: function() {
    //originally going to pause video but youtube has annoying related videos bar that can't be
    //removed so hide the entire video and replace with thumbnail.

    //dont show video if mobile view

    if (!this.$vuetify.breakpoint.smAndUp) {
      this.showVideo = false;
    }
    this.instagramPhotos();

    var tag = document.createElement("script");
    tag.id = "iframe-demo";
    tag.src = "https://www.youtube.com/iframe_api";
    var firstScriptTag = document.getElementsByTagName("script")[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

    window.onYouTubeIframeAPIReady = () => {
      this.yt = new window.YT.Player("video", {
        events: {
          onReady: window.onPlayerReady
        }
      });
    };
    window.onPlayerReady = () => {
      document.getElementById("video").style.borderColor = "#FF6D00";
      var interval = setInterval(() => {
        //hide the video when get to 140 sec and replace with image screenshot
        if (this.yt.getCurrentTime() >= 139) {
          this.showVideo = false;
          clearInterval(interval);
        }
      }, 1000);
    };
  },
  methods: {
    instagramPhotos: function() {
      // It will contain our photos' links
      this.instagramArray = [];

      axios
        .get("https://www.instagram.com/theblackworkartist/")
        .then(userInfoSource => {
          // userInfoSource.data contains the HTML from Axios
          const jsonObject = userInfoSource.data
            .match(
              /<script type="text\/javascript">window\._sharedData = (.*)<\/script>/
            )[1]
            .slice(0, -1);

          const userInfo = JSON.parse(jsonObject);
          // Retrieve only the first 10 instagramArrayults
          const mediaArray = userInfo.entry_data.ProfilePage[0].graphql.user.edge_owner_to_timeline_media.edges.splice(
            1,
            11
          );
          for (let media of mediaArray) {
            const node = media.node;

            // Process only if is an image
            if (node.__typename && node.__typename !== "GraphImage") {
              continue;
            }

            // Push the thumbnail src in the array
            this.instagramArray.push(node.thumbnail_src);
          }
        })
        .catch(() => {
          // console.error("Unable to retrieve photos. Reason: " + e.toString());
        });
    }
  },
  data: () => ({
    yt: {},
    redbubbleArray: [],
    instagramArray: [],
    showVideo: true,
    showText: false
  })
};
</script>
<style>
html,
body {
  overflow-x: hidden;
}

.paragraph {
  max-width: 500px;
  margin: 0 auto;
  padding: 0 10px;
}
.youtubeVideo {
  width: 100vw;
  height: 56.25vw;
}
.mainVideoImage {
  margin-top: 5px;
  padding-left: 80px;
  padding-top: 60px;
  background-position: top;
  background-repeat: no-repeat;
  background-size: cover;
  width: 100vw;
  height: 56.25vw;
  font-size: 70px;
  font-family: "Homemade Apple", cursive;
  display: flex;
}

.main-heading {
  font-size: 30px;
  color: white;
  font-family: "Homemade Apple", cursive;
  transform: rotate(-2deg);
  text-align: center;
  margin-top: 120px;
}

.profile-pic {
  border-radius: 50%;
  width: 200px;
}

.slide-fade-enter-active {
  transition: all 0.8s ease;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter,
.slide-fade-leave-active {
  padding-left: 10px;
  padding-top: 60px;
  opacity: 0;
}

.outline-text {
  -webkit-text-stroke: 1px #69f0ae;
  color: black;
  text-shadow: 3px 3px 0 #69f0ae, -1px -1px 0 #69f0ae, 1px -1px 0 #69f0ae,
    -1px 1px 0 #69f0ae, 1px 1px 0 #69f0ae;
}

@media (max-width: 540px) {
  .mainVideoImage {
    padding-left: 40px;
    padding-top: 60px;
    margin-top: 5px;
    background-position: top;
    background-repeat: no-repeat;
    background-size: cover;
    width: 100vw;
    height: 140vw;
    font-size: 40px;
    font-family: "Homemade Apple", cursive;
    display: flex;
  }
.outline-text {
   -webkit-text-stroke: 1px black;
  color: white;
  text-shadow: 3px 3px 0 black, -1px -1px 0 black, 1px -1px 0 black,
    -1px 1px 0 black, 1px 1px 0 black;
}
  .paragraph {
    max-width: 400px;
    padding: 10px;
    margin: 0 auto;
  }

  .slide-fade-enter-active {
    transition: all 0.8s ease;
  }

  .slide-fade-leave-active {
    transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
  }

  .slide-fade-enter,
  .slide-fade-leave-active {
    padding-left: 10px;
    padding-top: 60px;
    opacity: 0;
  }
}



</style>