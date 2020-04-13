<template>
  <q-page class="q-pa-md">
    <div class="column items-center">
      <div class="col-3 q-gutter-xs">
        <q-btn color="primary" push size="lg" icon="mic" @click="record()">Record</q-btn>
        <q-btn color="primary" push size="lg" icon="stop" @click="stop()">Stop</q-btn>
      </div>
    </div>

    <div class="column items-center">
      <div class="col">
        <div v-for="(audio, index) in audios" :key="index">
          <audio controls :src="audio">
          </audio>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      mediaRecorder: null,
      chunks: [],
      audios: []
    }
  },
  mounted () {
    this.init()
  },
  methods: {
    init () {
      if (navigator.mediaDevices.getUserMedia) {
        navigator.mediaDevices.getUserMedia({ audio: true })
          .then((stream) => {
            this.mediaRecorder = new MediaRecorder(stream)

            this.mediaRecorder.ondataavailable = (e) => {
              this.chunks.push(e.data)
            }

            this.mediaRecorder.onstop = (e) => {
              const blob = new Blob(this.chunks, { type: 'audio/ogg; codecs=opus' })
              const audioURL = window.URL.createObjectURL(blob)
              this.chunks = []
              this.audios.push(audioURL)
            }
          })
          .catch(function (err) {
            console.log('The following getUserMedia error occured: ' + err)
          })
      } else {
        alert('getUserMedia not supported on your browser!')
      }
    },
    record () {
      this.mediaRecorder.start()
    },
    stop () {
      this.mediaRecorder.stop()
    }
  }
}
</script>
