<template>
<div>
<aeris-metadata-layout  title="Download" icon="fa fa-download">
	<div class="bgi-download-form-line">
	<label for="institution" class="bgi-download-form-label">Your institution:</label> <input id="institution" class="bgi-download-form-input"></input>
	</div>
	<div class="bgi-download-form-line">
	<label class="bgi-download-form-label">Status:</label><input type="radio" name="status" value ="public" id="public_status"><label for="public_status">public/govermental</label> <input type="radio" name="status" value ="private" id="private_status"><label for="private_status">private/individual</label> <br>
	</div>
	<div class="bgi-download-form-line">
	<label for="address" class="bgi-download-form-label">Postal address:</label> <input id="address" class="bgi-download-form-input"></input> <br>
	</div>
	...
	<br>
	<button class="bgi-download-submit-button">Submit</button>
</aeris-metadata-layout>
</div>
</template>


<script>
export default {

  name: "bgi-download",
  
  props: {
    lang: {
      type: String,
      default: 'en'
    }
  },
  
  mounted: function() {
    var event = new CustomEvent("aerisThemeRequest", {});
    document.dispatchEvent(event);
  },
  
  destroyed: function() {
    document.removeEventListener('aerisMetadataRefreshed', this.aerisMetadataListener);
    this.aerisMetadataListener = null;
     document.removeEventListener("aerisTheme", this.aerisThemeListener);
    this.aerisThemeListener = null;
  },
  created: function() {
    console.log("Bgi download - Creating");
    // to get the datas
    this.aerisMetadataListener = this.handleRefresh.bind(this);
    document.addEventListener('aerisMetadataRefreshed', this.aerisMetadataListener);
     this.aerisThemeListener = this.handleTheme.bind(this);
    document.addEventListener("aerisTheme", this.aerisThemeListener);
  },
  data() {
    return {
      visible: true,
      aerisMetadataListener: null,
      aerisThemeListener: null,
      theme:null,
      isource: ""
    }
  },
  methods: {
    
    handleRefresh: function(e) {
      console.log("Bgi download - Refreshing");
      this.isource = e.detail.isource;
    },
    
     handleTheme: function(event) {
      this.theme = event.detail;
        this.ensureTheme();
    },

    ensureTheme: function() {
      let button = document.querySelector(".bgi-download-submit-button");
      if (button) {
        button.style.color = "#FFFFFF";
        button.style.background = this.theme.primary;
        button.style.borderColor = this.theme.primary;
      }
    }
  } // methods
} // default
</script>



<style >
	.bgi-download-submit-button {
 border: 0;
 box-shadow: none;
 border-radius: 0px;
 padding: 10px;
 cursor: pointer;
 color:white;
}

	.bgi-download-form-line {
		width:100%; 
		display:table;
		margin-bottom: 5px
	} 
	
	.bgi-download-form-label {
		display:table-cell; 
		width:1px;
		white-space: nowrap;
		padding-right:10px;
		color: #0056A2;
	}
	
	.bgi-download-form-input {
		display:table-cell;
		width:100%
	}
	
	.bgi-download-submit-button {
 		border: 0;
 		box-shadow: none;
 		border-radius: 0px;
 		padding: 10px;
 		cursor: pointer;
 		color:white;
	}
	
</style>