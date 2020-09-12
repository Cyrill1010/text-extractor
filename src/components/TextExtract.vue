<template>
  <v-container>
    <v-file-input
      v-model="files"
      color="deep-blue accent-4"
      counter
      label="File input"
      multiple
      placeholder="Select your files"
      prepend-icon="mdi-paperclip"
      outlined
      show-size
      @change="$log"
    >
      <template v-slot:selection="{ index, text }">
        <v-chip v-if="index < 2" color="deep-blue accent-4" dark label small>
          {{ text }}
        </v-chip>

        <span
          v-else-if="index === 2"
          class="overline grey--text text--darken-3 mx-2"
        >
          +{{ files.length - 2 }} File(s)
        </span>
      </template>
    </v-file-input>
    <v-item-group>
      <v-container class="pa-0">
        <v-row>
          <v-col v-for="(file, ind) in files" :key="ind" cols="12" md="4">
            <v-item v-slot:default="{ active, toggle }">
              <v-img
                :src="setSrc(file)"
                height="200"
                contain
                class="text-right pa-2"
                @click="toggle"
              >
                <v-btn icon dark>
                  <v-icon>
                    {{ active ? 'mdi-heart' : 'mdi-heart-outline' }}
                  </v-icon>
                </v-btn>
              </v-img>
            </v-item>
          </v-col>
        </v-row>
      </v-container>
    </v-item-group>
    <v-btn @click="extractText">Extract text</v-btn>
    <br />
    <textarea cols="30" rows="10" v-model="extractedText"></textarea>
    <v-progress-linear
      :value="progress"
      :active="isLoading"
      style="position: fixed; bottom: 20px; left: 50px; right: 50px; width: none"
    ></v-progress-linear>
  </v-container>
</template>

<script>
import { createScheduler, createWorker, setLogging } from 'tesseract.js'

setLogging(true) //TODO remove after production
const scheduler = createScheduler()
const worker1 = createWorker({
  logger: m => console.log('worker1', m)
})
const worker2 = createWorker({
  logger: m => console.log('worker2', m)
})
const rectangles = [
  {
    left: 0,
    top: 0,
    width: 500,
    height: 250
  },
  {
    left: 500,
    top: 0,
    width: 500,
    height: 250
  }
]

export default {
  data() {
    return {
      files: [],
      progress: 0,
      isLoading: false,
      extractedText: null
    }
  },
  async created() {
    await worker1.load()
    await worker1.loadLanguage('fra+deu')
    await worker1.initialize('fra+deu')
    await worker2.load()
    await worker2.loadLanguage('fra+deu')
    await worker2.initialize('fra+deu')
    scheduler.addWorker(worker1)
    scheduler.addWorker(worker2)
  },
  methods: {
    async extractText() {
      this.isLoading = true
      // for (const file of this.files) {
      //   let result = await worker1.recognize(file, {
      //     rectangle: { top: 0, left: 0, width: 100, height: 100 }
      //   })
      //   console.log('result=', result.data)
      // }

      // for (const file of this.files) {
      //   let results = await Promise.all(
      //     Array(10)
      //       .fill(0)
      //       .map(() => scheduler.addJob('recognize', file))
      //   )
      //   console.log(results)
      // }

      let text

      for (const file of this.files) {
        let results = await Promise.all(
          rectangles.map(rectangle =>
            scheduler.addJob('recognize', file, { rectangle })
          )
        )
        console.log(results.map(r => r.data.text))
        text = results.map(r => r.data.text)
      }
      this.extractedText = text

      await scheduler.terminate()
      this.isLoading = false
    },
    setSrc(file) {
      return URL.createObjectURL(file)
    }
  }
}
</script>
