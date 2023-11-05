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
      productResults: []
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
      try {
        const openai = new OpenAI({
            apiKey: import.meta.env.VITE_OPENAI_API_KEY,
            dangerouslyAllowBrowser: true
        });
        let response = await openai.images.generate({
          prompt: this.prompt,
          n: 3,
          size: "256x256"
        })
        response.data.forEach(url => this.dalleImageUrls.push(url.url))
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
    goToLink(){
      window.location.href = "https://www.google.com/" // Replace with the desired website URL
    },
    selectImage(index){
      //highlight selected image
      this.selectedImageIndex = index
      //send image url to backend
      //process results
    }
  },
}
</script>