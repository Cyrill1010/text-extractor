<template>
  <v-app>
    <v-app-bar app dark
      ><v-spacer></v-spacer>
      <div>
        <v-list-item two-line
          ><v-list-item-content
            ><v-list-item-title>Text Extractor</v-list-item-title
            ><v-list-item-subtitle
              >Extract text from png and put it in an excel
              table</v-list-item-subtitle
            ></v-list-item-content
          ></v-list-item
        >
      </div>
      <v-spacer></v-spacer
    ></v-app-bar>

    <v-main>
      <TextExtract />
    </v-main>
  </v-app>
</template>

<script>
import TextExtract from './components/TextExtract'

export default {
  name: 'App',
  components: {
    TextExtract
  }
}
</script>
