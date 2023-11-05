<template>
  <div class="pa-6">
    <v-row class="justify-center align-center">
      <div>
        <v-img
            src="../assets/logo.png"
            width="200"
        ></v-img>
      </div>
      <div style="font-size:26px; font-weight:bold">Picture <br> Perfect <br> Picks</div>
    </v-row>
    <v-row class="d-flex justify-center mt-10">
      <div style="width: 500px" class="mr-5">
        <v-text-field 
          label="Input a room description"
          width="300"
          variant="outlined"
          density="compact"
          rounded
          v-model="prompt"
          >
        </v-text-field>
      </div>
      <v-btn 
        color="#00ffA9" 
        @click="CreateImages()" 
        size="large"
        :loading="loading"
        rounded
        >go</v-btn>
    </v-row>
    <template v-if="dalleImageUrls.length>0">
      <v-row :key="imageKey">
        <v-col
          v-for="(image, index) in dalleImageUrls"
          :key="image"
          class="d-flex child-flex"
          cols="4"
        >
          <v-hover
            v-slot="{isHovering, props}"
            open-delay="0"
          >
            <v-card width="300" class="pa-2" 
              @click="selectImage(index)"
              :elevation="isHovering ? 6 : 2"
              v-bind="props"
              :color="selectedImageIndex == index? '#ff95c8' : ''">
              <v-img
                width="300"
                aspect-ratio="1/1"
                :src=image
              ></v-img>
            </v-card>
          </v-hover>
        </v-col>
      </v-row>
      <v-divider thickness="2" class="my-6"></v-divider>
    </template>
    <template v-if="productResults.length > 0">
      <v-row>Production Options</v-row>
      <v-row >
        <v-col
          v-for="(result, index) in productResults.length"
          :key="index"
          class="d-flex child-flex"
          cols="4"
        >
          <v-card width="300" class="pa-2" @click="goToLink(result.url)">
            <v-img
              width="300"
              aspect-ratio="1/1"
              :src=result.imageUrl
            ></v-img>
            <div>{{result.name}}</div>
            <div>{{result.description}}</div>
          </v-card>
        </v-col>
      </v-row>
    </template>
  </div>
</template>

<script >
import OpenAI from "openai";

export default {
  components: {},
  data() {
    return {
      prompt: "",
      loading: false,
      dalleImageUrls: [],
      imageKey: 0,
      selectedImageIndex: -1,
      productResults: [],
      dalleBlobs: []
    }
  },
  created(){
  },
  computed: {
  },
  methods: {
    async CreateImages(){
      if (this.prompt == "") return
      this.loading = true

      //clear inputs
      this.dalleImageUrls = []
      this.selectedImageIndex = -1
      this.productResults = []
      this.dalleBlobs = []

      //get images
      try {
        const openai = new OpenAI({
            apiKey: import.meta.env.VITE_OPENAI_API_KEY,
            dangerouslyAllowBrowser: true
        });
        let response = await openai.images.generate({
          prompt: this.prompt,
          n: 3,
          size: "256x256",
          response_format: 'b64_json'
        })
        console.log('response', response)
        
        response.data.forEach(b64 => {
          let blob = this.b64toBlob(b64.b64_json)
          let url = URL.createObjectURL(blob)
          this.dalleImageUrls.push(url)
        })
        this.imageKey++
        
        if (!response.data) {
          throw new Error("Unable to generate the image");
        }
      } catch (error) {
        console.log(error.message);
      } finally {
        this.loading = false
      }
    },
    b64toBlob(b64Data, contentType='', sliceSize=512){
      const byteCharacters = atob(b64Data);
      const byteArrays = [];

      for (let offset = 0; offset < byteCharacters.length; offset += sliceSize) {
        const slice = byteCharacters.slice(offset, offset + sliceSize);

        const byteNumbers = new Array(slice.length);
        for (let i = 0; i < slice.length; i++) {
          byteNumbers[i] = slice.charCodeAt(i);
        }

        const byteArray = new Uint8Array(byteNumbers);
        byteArrays.push(byteArray);
      }

      const blob = new Blob(byteArrays, {type: contentType});
      return blob;
    },
    goToLink(){
      window.location.href = "https://www.google.com/" // Replace with the desired website URL
    },
    selectImage(index){
      //highlight selected image
      this.selectedImageIndex = index
      var url = this.dalleImageUrls[index]
      
      console.log('url', url)

      fetch(url)
        .then(async response => {
          const contentType = response.headers.get('content-type')
          const blob = await response.blob()
          const file = new File([blob], "image.jpg", { contentType })
          console.log(file)
          // access file here
        })

      //send image url to backend
      //process results
    }
  },
}
</script>