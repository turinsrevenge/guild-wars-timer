<template>
  <div class="column is-6 is-4-tablet is-4-fullhd">
    <div class="timer" :class="tag">
      <div class="boss">
        <span class="event">
          <a target="_blank" :href="wiki">{{ name }}</a>
        </span>
        <span class="description">{{ location }}</span>
        <div class="left">
          {{ countdown }}
        </div>
      </div>
      <div class="panel">
        <span class="start">
          {{ states && next.at.state != status.name ? next.at.state : status.active ? 'Next' : 'Starts' }} at {{ nextAt }}
        </span>
        <span class="end">
          <span v-if="!states || ! status.active">{{ upcoming }}</span>
          <span v-if="states && status.active">meta event</span>
          <ul>
            <!--<li>
              <a>
                <i class="fa fa-bell" aria-hidden="true"></i>
              </a>
            </li>-->
            <li v-show="! (states == true && status.active == false)">
              <a @click="copyToClipboard()">
                <i class="fa fa-map-marker" aria-hidden="true"></i>
              </a>
            </li>
            <li :class="{ 'favorite': fav }">
              <a @click="toggleFavorite()">
                <i v-if="fav" class="fa fa-star" aria-hidden="true"></i>
                <i v-if="!fav" class="fa fa-star-o" aria-hidden="true"></i>
              </a>
            </li>
          </ul>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "timer",
  props: {
    tag: {
      type: String
    },
    waypoint: {
      type: String
    },
    wiki: {
      type: String
    },
    name: {
      type: String
    },
    location: {
      type: String
    },
    status: {
      type: Object
    },
    next: {
      type: Object
    },
    states: {
      type: Boolean
    },
    favorite: {
      type: Boolean,
      default: false
    },
    format: {
      type: String
    }
  },
  data() {
    return {
      fav: this.favorite
    };
  },
  computed: {
    countdown() {
      if (this.status.active) {
        var seconds = this.status.cooldown % 60;
        seconds = seconds < 10 ? "0" + seconds : seconds;
        return Math.floor(this.status.cooldown / 60) + ":" + seconds;
      }
      return this.next.total_minute <= 15 ? "Soon" : "Ended";
    },
    upcoming() {
      if (this.status.active) {
        return "currently in progress";
      }
      return this.formatNext(this.next);
    },
    nextAt() {
      let t = this.next.at;
      if (t == null) return "";
      let form = new Date();
      form.setUTCHours(t.hour);
      form.setUTCMinutes(t.minute);
      let hour = form.getHours() < 10 ? "0" + form.getHours() : form.getHours();
      let minute =
        form.getMinutes() < 10 ? "0" + form.getMinutes() : form.getMinutes();
      let d = new Date();
      d.setHours(hour, minute);
      let options = {
        hour: "2-digit",
        minute: "2-digit"
      };
      return d.toLocaleTimeString(this.format, options);
    }
  },
  methods: {
    toggleFavorite() {
      this.fav = !this.fav;
      this.$emit("favorite", this.fav);
    },
    copyToClipboard() {
      copy(this.waypoint);
      this.$emit("notification", "Successfully copied waypoint to clipboard.");
    },
    formatNext(next) {
      if (next.hour > 0)
        return (
          "in " +
          next.hour +
          " hour" +
          (next.hour == 1 ? "" : "s") +
          ", " +
          next.minute +
          " minute" +
          (next.minute == 1 ? "" : "s")
        );
      return "in " + next.minute + " minute" + (next.minute == 1 ? "" : "s");
    }
  },
};
</script>
