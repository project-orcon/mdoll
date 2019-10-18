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
          :style="{ backgroundImage: 'url(\'' + require('@/assets/gray-end-video.jpg') + '\')' }"
        >
          <transition name="slide-fade">
            <div style="transform: rotate(-10deg);" v-show="!showVideo">
              Michael Doll
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
        <br />Email me to discuss what kind of tattoo you want!
        <br />@
        <strong style="text-transform:uppercase">therittersporn@gmail.com</strong>
      </v-col>

      <v-col cols="12">
        <div class="main-heading">Instagram feed</div>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-card class="black">
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
      <v-col cols="12" class="text-center">
        <div class="main-heading mb-5">&copy; Michael Doll</div>
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
      setInterval(() => {
        console.log(this.yt.getCurrentTime());

        //hide the video when get to 140 sec and replace with image screenshot
        if (this.yt.getCurrentTime() >= 140) {
          this.showVideo = false;
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
        .catch(e => {
          console.error("Unable to retrieve photos. Reason: " + e.toString());
        });
    }
  },
  data: () => ({
    yt: {},
    instagramArray: [],
    showVideo: true,
    showText: false
  })
};
</script>
<style>
.paragraph {
  max-width: 500px;
  margin: 0 auto;
}
.youtubeVideo {
  width: 100vw;
  height: 100vh;
}
.mainVideoImage {
  margin-top: 5px;
  background-position: top;
  background-repeat: no-repeat;
  background-size: cover;
  width: 100vw;
  height: 90vh;
  font-size: 70px;

  color: black;
  font-family: "Homemade Apple", cursive;

  display: flex;
  align-items: end; /* horizontal */
  justify-content: end; /* vertical */
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
  opacity: 0;
}
</style>