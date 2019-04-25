<template>
  <aeris-metadata-layout v-if="visible" title="Details" icon="fa fa-info">
    <aeris-metadata-list :items="JSON.stringify(items)"/>
  </aeris-metadata-layout>
</template>

<script>
import moment from "moment";

export default {
  name: "aeris-metadata-information",

  props: {
    lang: {
      type: String,
      default: "en"
    }

  },

  computed: {
    items() {
      return [
        {
          name: "Number of points",
          value: this.data.nbpoint
        },
        {
          name: "Compilation date",
          value: this.data.compildat
        }
      ];
    }
  },

  destroyed: function() {
    document.removeEventListener(
      "aerisMetadataRefreshed",
      this.aerisMetadataListener
    );
    this.aerisMetadataListener = null;
  },

  created: function() {
    console.log("Bgi Details - Creating");
    this.aerisMetadataListener = this.handleRefresh.bind(this);
    document.addEventListener(
      "aerisMetadataRefreshed",
      this.aerisMetadataListener
    );
  },

  data() {
    return {
      aerisMetadataListener: null,
      data: [],
      visible: false
    };
  },

  methods: {
    handleRefresh: function(e) {
      
      this.data = {
        nbpoint: e.detail.details.nbpoint,
        compildat: e.detail.details.compildat
      };
      this.visible = true;
    },

    formatDate: function(date) {
      if (date) {
        var momentDate = moment(date);
        if (momentDate.isValid()) return moment(date).format("LLL");
      }

      return date;
    }
  }
};
</script>
