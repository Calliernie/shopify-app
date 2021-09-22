<template>
  <div v-if="mediaType === 'image'" class="hero" :style="imageStyleObject">
    <v-overlay value="true" absolute>
      <v-container>
        <v-row>
          <h1 class="display-1 font-weight-thin mt-3">
            <b>ASTRONOMY PICTURE OF THE DAY</b>
          </h1>
          <v-spacer></v-spacer>
          <v-col cols="4">
            <v-form @submit.prevent="submit">
              <v-text-field
                @keydown.enter="getImage"
                v-model="currentDate"
                :rules="currentDateRules"
                outlined
                dense
                label="Search by date(YYYY-MM-DD)"
                append-icon="mdi-magnify"
                @click:append="getImage"
              ></v-text-field>
            </v-form>
          </v-col>
        </v-row>
        <v-row align="center" justify="center">
          <v-col class="text-center" cols="12">
            <h1 class="display-1 font-weight-thin mb-4">
              <b>{{ this.title }}</b> - <i>{{ this.copyright }}</i>
            </h1>

            <p>{{ this.explanation }}</p>
          </v-col>
        </v-row>

        <v-row class="justify-center">
          <v-btn fab outlined class="mx-2" @click="like"
            ><v-icon>mdi-heart</v-icon></v-btn
          >
          <v-btn fab outlined class="mx-2"><v-icon>mdi-share</v-icon></v-btn>
        </v-row>

        <v-row>
          <v-btn fab outlined @click="gotoPreviousImage"
            ><v-icon>mdi-arrow-left</v-icon></v-btn
          >
          <v-spacer></v-spacer>
          <v-btn fab outlined @click="gotoNextImage"
            ><v-icon>mdi-arrow-right</v-icon></v-btn
          >
        </v-row>
      </v-container>
    </v-overlay>
  </div>

  <div v-else class="hero">
    <iframe
      width="100%"
      height="100%"
      :src="`${this.url}&autoplay=1&mute=1&loop=1&controls=0&showinfo=0`"
    >
    </iframe>
    <v-overlay value="true" absolute>
      <v-container>
        <v-row>
          <h1 class="display-1 font-weight-thin mt-3">
            <b>ASTRONOMY PICTURE OF THE DAY</b>
          </h1>
          <v-spacer></v-spacer>
          <v-col cols="4">
            <v-form @submit.prevent="submit">
              <v-text-field
                @keydown.enter="getImage"
                v-model="currentDate"
                :rules="currentDateRules"
                outlined
                dense
                label="Search by date(YYYY-MM-DD)"
                append-icon="mdi-magnify"
                @click:append="getImage"
              ></v-text-field>
            </v-form>
          </v-col>
        </v-row>
        <v-row align="center" justify="center">
          <v-col class="text-center" cols="12">
            <h1 class="display-1 font-weight-thin mb-4">
              <b>{{ this.title }}</b> - <i>{{ this.copyright }}</i>
            </h1>

            <p>{{ this.explanation }}</p>
          </v-col>
        </v-row>

        <v-row class="justify-center">
          <v-btn fab outlined class="mx-2" @click="like"
            ><v-icon>mdi-heart</v-icon></v-btn
          >
          <v-btn fab outlined class="mx-2"><v-icon>mdi-share</v-icon></v-btn>
        </v-row>

        <v-row>
          <v-btn fab outlined @click="gotoPreviousImage"
            ><v-icon>mdi-arrow-left</v-icon></v-btn
          >
          <v-spacer></v-spacer>
          <v-btn fab outlined @click="gotoNextImage"
            ><v-icon>mdi-arrow-right</v-icon></v-btn
          >
        </v-row>
      </v-container>
    </v-overlay>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
export default {
  data() {
    return {
      url: "",
      title: "",
      date: "",
      copyright: "",
      explanation: "",
      mediaType: "",
      currentDate: "",
      nextDate: "",
      previousDate: "",
      currentDateRules: [
        (v) => !!v || "E-mail is required",
        (v) =>
          // eslint-disable-next-line no-useless-escape
          /^\d{4}\-(0[1-9]|1[012])\-(0[1-9]|[12][0-9]|3[01])$/.test(v) ||
          "Please enter the correct format",
      ],
    };
  },
  computed: {
    imageStyleObject() {
      return {
        backgroundImage: `url(${this.url})`,
      };
    },
  },
  methods: {
    like() {
      //this function is for saving likes in local storage
      console.log("liked");
      var likes = [];
      likes = JSON.parse(localStorage.getItem("session")) || [];
      // Push the new data (whether it be an object or anything else) onto the array
      likes.push(this.currentDate);
      //console log array values
      console.log(likes);
      // Re-serialize the array back into a string and store it in localStorage
      localStorage.setItem("session", JSON.stringify(likes));
    },
    gotoNextImage() {
      this.nextDate = moment(this.currentDate)
        .add(1, "days")
        .format("YYYY-MM-DD");
      console.log(this.nextDate);
      this.currentDate = this.nextDate;
      this.getImage();
    },
    gotoPreviousImage() {
      this.previousDate = moment(this.currentDate)
        .add(-1, "days")
        .format("YYYY-MM-DD");
      console.log(this.previousDate);
      this.currentDate = this.previousDate;
      this.getImage();
    },
    getImage() {
      if (this.currentDate === "") {
        this.currentDate = moment(new Date()).format("YYYY-MM-DD");
        axios
          .get(
            `https://api.nasa.gov/planetary/apod?api_key=VyDJVVrBc0mpUB79AabFmB684E8gmJBNZPjikJCa&date=${this.currentDate}`
          )
          .then((response) => {
            console.log(response);
            this.url = response.data.url;
            this.title = response.data.title;
            this.date = response.data.date;
            this.copyright = response.data.copyright;
            this.explanation = response.data.explanation;
            this.mediaType = response.data.media_type;
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        axios
          .get(
            `https://api.nasa.gov/planetary/apod?api_key=VyDJVVrBc0mpUB79AabFmB684E8gmJBNZPjikJCa&date=${this.currentDate}`
          )
          .then((response) => {
            console.log(response);
            this.url = response.data.url;
            this.title = response.data.title;
            this.date = response.data.date;
            this.copyright = response.data.copyright;
            this.explanation = response.data.explanation;
            this.mediaType = response.data.media_type;
          })
          .catch((error) => {
            console.log(error);
          });
      }
    },
  },
  beforeMount() {
    this.getImage();
  },
};
</script>

<style scoped>
.hero {
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  position: relative;
  overflow: hidden;
}
</style>
