<template>
  <Generic :item="item">
    <template #indicator>
      <div class="notifs">
        <strong v-if="up > 0" class="notif up" title="Up">
          {{ up }}
        </strong>
        <strong v-if="down > 0" class="notif down" title="Down">
          {{ down }}
        </strong>
      </div>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";
import Generic from "./Generic.vue";

export default {
  name: "HetrixTools",
  mixins: [service],
  props: {
    item: Object,
  },
  components: {
    Generic,
  },
  data: () => ({
    monitors: null,
    //checks: null,
  }),
  computed: {
    up: function () {
      if (!this.monitors) {
        return "";
      }
      return this.monitors.filter((monitor) => {
        return monitor.uptime_status.toLowerCase() === "up";
      }).length;
    },
    down: function () {
      if (!this.monitors) {
        return "";
      }
      return this.monitors.filter((monitor) => {
        return monitor.uptime_status.toLowerCase() === "down";
      }).length;
    },
  },
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      const headers = {
        "Authorization": "Bearer " + this.item.apikey,
      };

      response = await this.fetch("/v3/uptime-monitors", { headers }).catch(
        (e) => {
          console.error(e);
        },
      );
      this.monitors = response.monitors;

      //let checks = [];
      //for (let monitor of this.monitors) {
      //  checks = checks.concat(monitor);
      //}

      //this.checks = checks;
    },
  },
};
</script>

<style scoped lang="scss">
.notifs {
  position: absolute;
  color: white;
  font-family: sans-serif;
  top: 0.3em;
  right: 0.5em;

  .notif {
    display: inline-block;
    padding: 0.2em 0.35em;
    border-radius: 0.25em;
    position: relative;
    margin-left: 0.3em;
    font-size: 0.8em;

    &.up {
      background-color: #4fd671;
    }

    &.down {
      background-color: #e51111;
    }
  }
}
</style>
