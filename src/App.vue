<script setup lang="ts">
import { computed, ref } from 'vue';


const recordedVideo = ref("")
const isRecording = ref(false)

const chunks: Blob[] = []

let mediaStream: MediaStream;
let mediaRecorder: MediaRecorder;

const haveRecord = computed(() => {
  return recordedVideo.value !== ""
})

async function startRecording() {
  try {
    mediaStream = await navigator.mediaDevices.getDisplayMedia({ video: true });
    mediaRecorder = new MediaRecorder(mediaStream);
    chunks.length = 0
    mediaRecorder.ondataavailable = event => {
      if (event.data.size > 0) {
        chunks.push(event.data);
      }
    };

    mediaRecorder.onstop = () => {
      const blob = new Blob(chunks, { type: 'video/webm' });
      recordedVideo.value = URL.createObjectURL(blob);
      isRecording.value = false
    };

    mediaRecorder.start();
    isRecording.value = true
  } catch (error) {
    console.error('Erro ao iniciar a gravação:', error);
  }
}

function stopRecording() {
  mediaRecorder.stop();
  mediaStream.getTracks().forEach(track => track.stop());
  isRecording.value = false
}

</script>

<template>
  <header>a client application for creating screen recordings</header>
  <main>
    <h1>Screen Record Online</h1>
    <section>
      <div class="main-section">
        <p class="description">Start capturing your screen right now without the need to download programs!</p>
        <div class="group-buttons">
          <a href="https://github.com/alequisk"><button class="github-button">Visit my GitHub</button></a>
          <button class="record-button" v-if="!isRecording" @click="startRecording()">Start recording</button>
          <button class="stop-button" v-if="isRecording" @click="stopRecording()">Stop recording</button>
        </div>
      </div>

      <aside>
        <div class="video-frame">
          <video :src="recordedVideo" controls></video>
        </div>
        <a class="download-button" v-if="haveRecord" :href="recordedVideo" download="screen-record-video">
          <button>Baixar video</button>
        </a>
      </aside>
    </section>
  </main>
</template>

<style scoped>
header {
  width: 100%;
  padding: 1.88rem 0;
  text-transform: uppercase;
  font-size: 0.875rem;
  border-bottom: 1px solid #9A9A9A;
  color: #9A9A9A;
  margin-bottom: 8rem;
}

main h1 {
  font-weight: 900;
  font-size: 4.875rem;

  background: linear-gradient(90deg, #FFC53D 5.42%, #FF5E07 100%);
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.description {
  color: #FFF;
  font-size: 1.5rem;
  font-style: normal;
  font-weight: 500;
  line-height: 2.1rem;
}

button {
  width: 14.3125rem;
  padding: 1.2rem;
  flex-shrink: 0;
  font-weight: 500;
  font-size: 1.5rem;
  border: none;
  border-radius: 0.3125rem;
  cursor: pointer;
  transition: filter 200ms ease-in-out;
}

button:hover {
  filter: brightness(.8);
}

.record-button {
  border-bottom: 5px solid #B34804;
  background: #F9690E;
  color: #FFFFFF;
}

.stop-button {
  border-bottom: 5px solid hsl(0, 100%, 40%);
  background: hsl(0, 100%, 50%);
  color: #FFFFFF;
}

.github-button {
  border-bottom: 5px solid hsl(0, 0%, 1%);
  background: hsl(0, 0%, 3%);
  color: #FFFFFF;
}

.group-buttons {
  display: flex;
  justify-content: space-around;
  margin-top: 7.25rem;
}

section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  margin-top: 2rem;
}

aside {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
}

.video-frame {
  width: 100%;
  border: 2px dashed #9A9A9A;
  height: 25rem;
}

.download-button {
  cursor: pointer;
  padding-bottom: 2rem;
}

.download-button button {
  padding: .5rem;
  font-size: 1rem;
}

video {
  width: 100%;
  height: 100%;
}

@media (max-width: 600px) {
  section {
    grid-template-columns: 1fr;
  }
}
</style>
