<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:935px; height:1440px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:935px; height:1440px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Mission Briefing</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:1594px; height:1440px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt: Bowl Season",
          "status": "start"
        },
        {
          "slug": "002",
          "name": "Bug-Hunt: Widowmaker",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Salamander",
          "alias": "Lieutenant Pheno Syx",
          "code": "462370be-bd0f-41c2-b667-cc75f3a59a96///UNION-AUX-DEEP-STATION-4.1532//377308ad-ba23-410b-ae37-68a1fb5f8db4",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "The 10 Meter Knight"
        },
        {
          "callsign": "Soulless",
          "alias": "Sergeant Nicholas Laandsman",
          "code": "7cd700cc-c990-48ed-892f-e5468de724c4///UNION-AUX-DEEP-STATION-4.1532//a98c3e28-ad4a-4f89-ne51-83f2ac583d13",
          "corpro": "GMS",
          "frame": "Sagarmatha",
          "mech": "Path to the Forgotten"
        },
        {
          "callsign": "Duck",
          "alias": "Private Hella Kissler",
          "code": "4be26ce9-923b-4069-b6c9-76437d4be455///UNION-AUX-DEEP-STATION-4.1532//056940c6-8d55-4190-8e85-57caa043cb1a",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Coda"
        },
        {
          "callsign": "Oprah",
          "alias": "Private Chris Tucker",
          "code": "98ca9616-044e-4f87-b89b-aae4eb3387ec///UNION-AUX-DEEP-STATION-4.1532//6f572259-6946-41bf-931a-e0543709e892",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Rush Hour"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Tesseract",
        "headerSubtitle": "Knights",
        "subheaderTitle": "Union Auxiliaries",
        "subheaderSubtitle": "First Response Team-Alpha",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 2400px;
  height: 1440px;
  overflow: hidden;
}
</style>
