# Furn-E
This Vue.js application uses the OpenAI API to generate images based on a user-provided description and presents the generated images for selection. Additionally, it displays product options based on those generated images. Meant to be used with our [Python backend API](https://github.com/TeamZombies/furne_backend_api).

![](https://github.com/TeamZombies/furne_frontend/blob/main/furne_preview.gif)

## Application Workflow

1. Enter a room description in the provided text field.
2. Click the "Go" button to generate images based on the description.
3. Select your favorite image by clicking on it to see related product options.

## Prerequisites

This application utilizes

* Vue.js
* Vuetify
* Vite
* An OpenAI API key (credits needed)

## Installation
1. Clone the repository.
2. Install dependencies using npm install.
3. Set up your OpenAI API key by creating a .env file and adding VITE_OPENAI_API_KEY=YOUR_API_KEY.

## Project setup

npm run dev

