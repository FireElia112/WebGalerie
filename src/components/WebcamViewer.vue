<template>
  <div class="container">
    <div class="webcam">
      <h1>Welcome to your Website Galerie</h1>
      <video class="video" ref="video" autoplay ></video>
      <button @click="takePhoto">Foto aufnehmen</button>
    </div>
    <div class="photos">
      <div v-for="(photo, index) in photos" :key="index" class="photo">
        <img :src="photo">
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';

export default {
  setup() {
    const video = ref(null);
    const photos = reactive([]);

    onMounted(async () => {
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        try {
          const stream = await navigator.mediaDevices.getUserMedia({ video: true });
          if (video.value) {
            video.value.srcObject = stream;
          }
        } catch (err) {
          console.log("Es gab einen Fehler beim Zugriff auf die Webcam: " + err);
        }
      } else {
        console.log("Dieser Browser unterstützt den Zugriff auf die Webcam nicht.");
      }
    });

    const takePhoto = () => {
      console.log(video.value)
      if (video.value) {
        const canvas = document.createElement('canvas');
        canvas.width = video.value.videoWidth;
        canvas.height = video.value.videoHeight;
        const context = canvas.getContext('2d');
        context.drawImage(video.value, 0, 0, video.value.videoWidth, video.value.videoHeight);
        photos.unshift(canvas.toDataURL('image/png'));
      }
    };

    return { video, takePhoto, photos };
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  height: 90%;
}

.webcam {
  position: fixed;
  left: 0;
  top: 0;
  height: 100%;
  width: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.photos {
  margin-left: 50%;
  padding: 10px;
  overflow-y: auto;
}

.photo {
  margin-bottom: 10px;
}

.photo img {
  width: 50%;
  height: auto;
}
.video {
  border-style: dashed;
  min-width: 200px;
  max-width: 80% ;
}


@media screen and (max-width: 768px) {
  .webcam, .photos {
    width: 70%;
    position: relative;
    height: auto;
  }

  .webcam {
    padding: 10px;
  }

  .photos {
    margin-left: 0;
  }
}
</style>
