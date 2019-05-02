<style>
.aeris-metadata-synthesis-host .metadata-container {
  margin: 0 auto;
  max-width: 1000px;
}

.aeris-metadata-synthesis-host .component-container {
  margin-bottom: 10px;
}

</style>

<template>
  <div class="aeris-metadata-synthesis-host " >
    <div class="metadata-container">
     <div style="margin-bottom:10px">
      <aeris-metadata-title :lang="lang" />
      </div>
      <div class="columns">
      <div style="break-after:column">
      <aeris-metadata-contacts :lang="lang"/>
       <bgi-details :lang="lang" />
       <aeris-metadata-formats :lang="lang"/>
       <aeris-metadata-citations :lang="lang"/>
       <aeris-metadata-datapolicy :lang="lang" markdown="true"/> 
       </div>
       <aeris-metadata-spatial-extents :lang="lang"/>
       <aeris-metadata-information-links :lang="lang"/> 
       <bgi-download :lang="lang"/>
       </div> 
    </div>
  </div>
</template>

<script>
export default {
  name: "bgi-landing-page",

  props: {
    lang: {
      type: String,
      default: "en"
    },

    service: {
      type: String,
      default: "https://cszbxcgok7.execute-api.eu-west-3.amazonaws.com/mock/doi"
    },

    identifier: {
      type: String,
      default: ""
    }
  },

  created: function() {
    console.log("Bgi landing page - Creating");
  },
  
  mounted: function() {
  	var urlParams = new URLSearchParams(window.location.search);
  	var entries = urlParams.entries();
	for(let pair of entries) {
		if (pair[0].toLowerCase() == "numero_isource") {
			this.id = pair[1];
			this.refresh(); 
		} 
	}
  },
  
  methods: {
  
  replaceAll: function(string, target, replacement) {
      return string.split(target).join(replacement);
    },
  
  handleSuccess: function(response) {
      document.dispatchEvent(
        new CustomEvent("aerisLongActionStopEvent", {
          detail: {
            message: "Loading"
          }
        })
      );
      var bgiMetadata = response.data.data;
      
      console.log(bgiMetadata)
      
      
      var newMetadata = { resourceTitle: {}, details: {}}
      
      var area = {
    	"area" : {
      "type" : "RECTANGLE_AREA",
      "northLatitude" : bgiMetadata.latmax,
      "southLatitude" : bgiMetadata.latmin,
      "eastLongitude" : bgiMetadata.longmin,
      "westLongitude" : bgiMetadata.longmax
    	},
    	"projection" : "EPSG 4326"
  	  }
  	  
      var format = {
    	"name" : "Land gravity data format (EOL)",
	    "description" : {en: "http://bgi.obs-mip.fr/data-products/Documentation/bgi_data_formats"}
	   }
	   
		var author =    {
    	"name" : bgiMetadata.auteurs,
    	"roles" : [ "author" ]
    }
    
    	var bgi =    {
    	"name" : "International Gravimetric Bureau",
    	"email": "bgi@cnes.fr",
    	"roles" : [ "distributor" ]
    	}
    
    	var bgiLink = {
    		"type" : "INFORMATION_LINK",
    		"url" : "http://bgi.obs-mip.fr/",
    		"description" : {
      			"en" : "BGI main site",
      	    }
  		}	
          
      newMetadata.contacts = [author, bgi]
      newMetadata.spatialExtents = [area]
      newMetadata.formats = [format]
      newMetadata.resourceTitle.en =bgiMetadata.intitule;
      newMetadata.distributionInformation = {"description" : {en: "Data, products or software made available from the BGI website are **mostly dedicated to support public, research and academic activities**.<br><br> You may not distribute or publish materials you obtained from this site if you do not accept the terms of use given below.<br><br> Complete details: http://bgi.obs-mip.fr/data-products/terms_of_use"}}
      newMetadata.details.nbpoint = bgiMetadata.nbpoint;
      newMetadata.details.compildat = bgiMetadata.compildat;
      newMetadata.links = [bgiLink]
      newMetadata.isource = bgiMetadata.isource
      var identifiers = [ {   "code" : "10.18168/bgi",    "codeSpace" : "http://dx.doi.org/"  } ]
  	  newMetadata.identifiers = identifiers; 
      
      this.sendDataToComponents(newMetadata);
      
    },
    
     handleError: function(response) {
      document.dispatchEvent(
        new CustomEvent("aerisLongActionStopEvent", {
          detail: {
            //message: this.$i18n.t("loading")
            message: "Loading"
          }
        })
      );
      console.log("Aeris-Metadata - Error while accessing server:");
      var error = response.status;
      var message = response.statusText;
      if (!error) message = "Can't connect to the server";
      console.log("Error " + error + ": " + message);
    },
    
     sendDataToComponents: function(data) {
      var event = new CustomEvent("aerisMetadataRefreshed", {
        detail: data,
        lang: this.lang
      });
      document.dispatchEvent(event);
    },
  
  refresh: function() {
  		if (!this.id) {
  			return;
  		}
      //Reset
      var event = new CustomEvent("aerisMetadataRefreshed", {
        detail: {},
        lang: this.lang
      });
      document.dispatchEvent(event);

      if (this.service && this.id) {
        
        var url = this.service+"?numero_isource="+ this.id 
        
        document.dispatchEvent(
          new CustomEvent("aerisLongActionStartEvent", {
            detail: {
              //message: this.$i18n.t("loading")
              message: "Loading"
            }
          })
        );
        this.$http.get(url).then(
          response => {
            this.handleSuccess(response);
          },
          response => {
            this.handleError(response);
          }
        );
      }
    }
  },

  data() {
    return {
    	id: null
    };
  }
};
</script>
