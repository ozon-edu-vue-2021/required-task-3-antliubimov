<template>
  <div id="app">
    <div class="office">
      <Map @show-person-card="showCard" :isUserOpenned.sync="isUserOpenned" />
      <SideMenu :isUserOpenned.sync="isUserOpenned" :person="personInfo" />
    </div>
  </div>
</template>

<script>
import Map from "./components/Map.vue";
import SideMenu from "./components/SideMenu.vue";
import people from "@/assets/data/people.json";

export default {
  name: "App",
  components: {
    Map,
    SideMenu,
  },
  data() {
    return {
      cardId: "",
      isUserOpenned: false,
      people: [],
    };
  },
  created() {
    this.loadPeople();
  },
  computed: {
    personInfo() {
      return people[this.cardId];
    },
  },
  methods: {
    showCard(card) {
      this.cardId = card;
      this.isUserOpenned = true;
    },
    loadPeople() {
      this.people = people;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  background-color: #fafafa;
  padding: 24px;
  box-sizing: border-box;
}

html,
body,
#app {
  height: 100%;
}

* {
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

.office {
  display: grid;
  grid-template-columns: 1fr 320px;
  border-radius: 6px;
  border: 1px solid #ccd8e4;
  height: 100%;
  background: white;
  max-width: 1500px;
  margin: 0 auto;
}
</style>
