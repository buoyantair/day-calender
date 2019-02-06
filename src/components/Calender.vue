<template>
  <div class="calender">
    <div class="timeline">
      <div v-for="(n, k) in 24" :key="'r' + k">
        <div class="slot" v-if="n < 12">{{ n }} AM</div>
        <div class="slot" v-if="n >= 12">
          {{ n - 12 || n }} {{ n === 24 ? "AM" : "PM" }}
        </div>
      </div>
    </div>
    <div class="events" v-on:click.self="setDialogState(true)">
      <div
        v-for="event in events"
        :key="event.uuid"
        class="event"
        v-bind:style="{
          gridRow: computeGridRow(event),
          background: event.color
        }"
        v-on:click="setEditingState(true, event.id)"
      >
        <h4>{{ event.text }}</h4>
        <p>
          from: {{ event.from }} to:
          {{ event.to }}
        </p>
      </div>
    </div>
    <div
      class="dialog-container"
      v-on:click.self="setDialogState(false)"
      v-if="dialog && !editing"
    >
      <!-- NEW EVENT -->
      <form class="dialog">
        <h1>New Event</h1>
        <label for="event-text">Text:</label>
        <input
          v-model="form.text"
          type="text"
          name="event-text"
          id="event-text"
        />
        <label for="event-from">Start time:</label>
        <input
          v-model="form.from"
          type="time"
          name="event-from"
          id="event-from"
        />
        <label for="event-to">End time:</label>
        <input v-model="form.to" type="time" name="event-to" id="event-to" />
        <label for="event-color">Event color</label>
        <input
          type="color"
          id="event-color"
          name="event-color"
          v-model="form.color"
        />
        <button
          type="submit"
          v-on:click.prevent="addEvent"
          v-on:click.exact="setDialogState(false)"
        >
          Add Event
        </button>
      </form>
    </div>
    <div
      class="dialog-container"
      v-on:click.self="setEditingState(false)"
      v-if="dialog && editing"
    >
      <!-- EDITING EVENT -->
      <form class="dialog">
        <h1>Edit Event</h1>
        <label for="event-text">Text:</label>
        <input
          v-model="form.text"
          type="text"
          name="event-text"
          id="event-text"
        />
        <label for="event-from">Start time:</label>
        <input
          v-model="form.from"
          type="time"
          name="event-from"
          id="event-from"
        />
        <label for="event-to">End time:</label>
        <input v-model="form.to" type="time" name="event-to" id="event-to" />
        <label for="event-color">Event color</label>
        <input
          type="color"
          id="event-color"
          name="event-color"
          v-model="form.color"
        />
        <button
          type="submit"
          v-on:click.prevent="editEvent"
          v-on:click.exact="setEditingState(false)"
        >
          Edit Event
        </button>
      </form>
    </div>
  </div>
</template>
<script>
import uuid from "uuid/v4";

export default {
  name: "Calender",
  data() {
    return {
      events: [
        {
          id: uuid(),
          text: "Picnic",
          from: "06:00",
          to: "08:00",
          color: "#E6BF64"
        },
        {
          id: uuid(),
          text: "Breakfast",
          from: "08:00",
          to: "09:00",
          color: "#AAFFF7"
        }
      ],
      dialog: false,
      editing: false,
      form: {
        text: "Enter your text",
        from: "01:00",
        to: "12:00",
        color: "#e66465"
      },
      defaultForm: {
        text: "Enter your text",
        from: "01:00",
        to: "12:00",
        color: "#e66465"
      },
      currentElement: 0
    };
  },
  methods: {
    addEvent() {
      this.$data.events.push(this.$data.form);
      this.$data.form = this.$data.defaultForm;
    },
    setDialogState(val) {
      this.$data.dialog = val;

      if (val === false) {
        this.$data.form = this.$data.defaultForm;
      }
    },
    setEditingState(val, eventid) {
      if (val && eventid !== undefined) {
        this.$data.form = this.$data.events.find(x => x.id === eventid);
      }
      this.$data.editing = val;
      this.setDialogState(val);
    },
    editEvent() {
      this.$data.events = this.$data.events.filter(
        k => k.id !== this.$data.form.id
      );
      this.$data.events.push(this.$data.form);
    },
    buildTimeFromN(n) {
      let time = "";
      let ampm = "";
      if (n <= 24) {
        time = `${n % 2 !== 0 ? Math.ceil(n / 2) : n / 2}:${
          n % 2 !== 0 ? "00" : "30"
        }`;
        ampm = n >= 23 ? "PM" : "AM";
      } else if (n > 24) {
        time = `${n % 2 !== 0 ? Math.ceil(n / 2) : n / 2}:${
          n % 2 !== 0 ? "00" : "30"
        }`;
        ampm = n >= 47 ? "AM" : "PM";
      }

      return {
        time,
        text: `${time} ${ampm}`
      };
    },
    computeGridRow(event) {
      const fromHours = Number(event.from.toString().split(":")[0]) * 2 - 1;
      const toHours = Number(event.to.toString().split(":")[0]) * 2 - 1;
      const fromMins =
        Number(event.from.toString().split(":")[1]) >= 30 ? 1 : 0;
      const toMins = Number(event.to.toString().split(":")[1]) >= 30 ? 1 : 0;

      const gridStartLine = fromHours + fromMins;
      const gridEndLine = toHours + toMins;

      return `${gridStartLine} / ${gridEndLine}`;
    }
  }
};
</script>
<style scoped>
.calender {
  display: grid;
  grid-template-columns: 40px auto;
  height: 100vh;
  width: 100vw;
  overflow-x: hidden;
  overflow-y: scroll;
}

.timeline {
  display: grid;
  grid-column: 1;
  grid-auto-rows: 40px;
}

.timeline .slot {
  color: #70757a;
  font-size: 10px;
  border-right: #dadce0 1px solid;
  display: flex;
  flex-flow: column;
  justify-content: flex-end;
  transform: translateY(20%);
}

.events {
  grid-column: 2;
  display: grid;
  grid-auto-rows: 20px;
}

.event h4 {
  font-weight: bold;
}

.event {
  background: greenyellow;
  display: flex;
  flex-flow: column;
  justify-content: center;
}

.dialog-container {
  position: fixed;
  background: rgba(0, 0, 0, 0.5);
  height: 100vh;
  width: 100vw;
  display: flex;
  flex-flow: column;
  justify-content: center;
  z-index: 3;
}

.dialog {
  background: white;
  border-radius: 10px;
  min-height: 400px;
  width: 500px;
  margin: 0 auto;
  display: flex;
  flex-flow: column;
  justify-content: center;
  z-index: 4;
  box-shadow: 0px 10px 20px 1px rgba(0, 0, 0, 0.4);
}

.dialog h1 {
  margin: 30px 20px 0px 30px;
  font-size: 40px;
  font-weight: bold;
  text-align: left;
}

.dialog input,
.dialog label {
  margin: 15px 30px 0px 30px;
  text-align: left;
}

.dialog button[type="submit"] {
  width: 200px;
  margin: 30px auto;
  background: #ec368d;
  color: white;
  border: none;
  border-radius: 5px;
  height: 60px;
}
</style>
