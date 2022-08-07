<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
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
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
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
          "name": "As Above",
          "status": "start"
        },
        {
          "slug": "Optional",
          "name": "SSC Data",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Scythian",
          "alias": "Bogdan Eriksen",
          "code": "Eriksen.Bogdan:6dc0749f-b676-4f81-ac34-2be0fc0798e8//NDL-C-STOLEN-IRIS",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "The Carpathian"
        },
        {
          "callsign": "Valkyrie",
          "alias": "Lena Eriksen",
          "code": "Eriksen.Lena:a80efcd2-4898-4563-a319-71c9a8523a5b//NDL-C-FALLEN-CAIRN",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Sygni"
        },
        {
          "callsign": "Paper Crane",
          "alias": "Tien Shen",
          "code": "Shen.Tien:12638ac4-be11-4f40-a629-0e181102b293//NDL-C-SORROW-SKY",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Reach Heaven by Violence"
        },
        {
          "callsign": "Black Mirror",
          "alias": "Mira",
          "code": ".Mira:b81b944d-d483-4f33-b082-ba5570314fce//NDL-C-DEEP-DOLMEN",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "nil"
        },
        {
          "callsign": "Little Bird",
          "alias": 'Gentoo ',
          "code": ".Gentoo:69f73128-533f-447b-9d57-82cddf2395d3//NDL-C-DARK-GIFT",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "The Weather"
        },
      ],
      "header": {
        "planet": "Asphodel",
        "year": "5016u",
        "system": "Khore-19A",
        "gate": "Úytaahkoo",
        "ring": "Cascade-Line",
        "headerTitle": "Mirrorsmoke",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Delta-Echo-Echo-Zulu",
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
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
