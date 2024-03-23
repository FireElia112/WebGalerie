<template>
  <div class="bg">  
    <div class="container">
      <div class="webcam">
        <h1>Welcome to your Website Gallery</h1>
        <video class="video" ref="video" autoplay ></video>
        <button @click="takePhoto">
          <span id="edit-img"><img src="../assets/photo.png"></span>
        </button>
      </div>
      <div class="photos" v-if="photos.length != 0">
        <div v-for="(photo, index) in photos" :key="index" class="photo">
          <img :src="'data:image/jpeg;base64,' + photo.data">
          <br>
          <button @click="deletePhoto(photo.filename)">Foto löschen</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      video: null,
      photos: []
    }
  },
  mounted() {
    this.initCamera();
    this.loadPhotos();
  },
  methods: {
    async initCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        this.video = this.$refs.video;
        this.video.srcObject = stream;
      } catch (err) {
        console.error("Es gab einen Fehler beim Zugriff auf die Webcam: ", err);
      }
    },
    takePhoto() {
      const videoElement = this.$refs.video;
      if (videoElement) {
        const canvas = document.createElement('canvas');
        canvas.width = videoElement.videoWidth;
        canvas.height = videoElement.videoHeight;
        const context = canvas.getContext('2d');
        context.drawImage(videoElement, 0, 0, videoElement.videoWidth, videoElement.videoHeight);
        //const photoDataUrl = canvas.toDataURL('image/jpeg');
        canvas.toBlob(blob => {
          const formData = new FormData();
          formData.append('photo', blob, 'photo.jpeg');
          fetch('http://localhost:3000/uploads', { method: 'POST', body: formData })
            .then(response => {
              if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
              }
             
              this.loadPhotos();
            })
            .catch(error => {
              console.error('Es gab einen Fehler beim Senden des Fotos: ', error);
            });
        }, 'image/jpeg');
      }
    },
    async loadPhotos() {
  try {
    const response = await fetch('http://localhost:3000/uploads');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    let photos = await response.json();

    // Sortieren der Fotos nach Dateinamen in umgekehrter Reihenfolge
    photos = photos.sort((a, b) => a.filename.localeCompare(b.filename));
    photos = photos.reverse();

    this.photos = photos;
  } catch (error) {
    console.log('Es gab einen Fehler beim Laden der Fotos: ', error);
  }
}
,
    deletePhoto(filename) {
      fetch(`http://localhost:3000/uploads/${filename}`, { method: 'DELETE' })
        .then(response => {
          if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
          }
          // Nach erfolgreichem Löschen, entfernen des Fotos von Liste
          this.photos = this.photos.filter(photo => photo.filename !== filename);
        })
        .catch(error => {
          console.error('Es gab einen Fehler beim Löschen des Fotos: ', error);
        });
    }
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
  height: 80%;
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
  background-color: rgba(0,0,0,0.8);

}
button {
  border-style: solid;
  border-width: 10px;
  border-color: #2c3e50;
  margin-top: 10px;
}
img {
  width: 100px;
  height: 60px;
  
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
