<template>
  <div class="bg">  
    <div class="container">
      <div class="webcam">
        <h1>Welcome to your Website Galerie</h1>
        <video class="video" ref="video" autoplay ></video>
        <button  @click="takePhoto">
          <span id="edit-img"><img src="../assets/photo.png"></span>
        </button>
      </div>
      <div class="photos" v-if="photos.length != 0">
        <div v-for="(photo, index) in photos" :key="index" class="photo">
          <img :src="photo">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';

export default {
  data() {
    return {
      flag: false
    }
  },
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
h1 {
  background-color: white;
  border-style: groove;
  border-width: 10px;
  font-size: 35px;
  padding: 10px;
}
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
  width: 40%;
  margin-left: 55%;
  padding: 10px;
  overflow-y: auto;
  background-image: linear-gradient(rgb(63, 63, 63),black);
  border-radius: 10px;
}

.photo {
  margin-top: 10px;
  margin-bottom: 10px;
}

.photo img {
  padding-left: 0;
  width: 80%;
  height: auto;
  border-style: ridge;
  border-width: 1px;
}
.video {
  border-style: none;
  width: 80%;
  border-radius: 10px;
  border-style: groove;
  border-width: 10px;

}
button {
  border-style: solid;
  border-width: 10px;
  border-color: #2c3e50;
  margin-top: 10px;
}
img {
  width: 120px;
  height: 80px;
  
}
#edit-img:hover img {
    -webkit-filter: invert(100%);
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
