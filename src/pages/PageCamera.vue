<template>
  <q-page class="constrain q-pa-md">
    <div class="camera-frame q-pa-md">
       <video 
       v-show="!imageCaptured"
       class="full-width"
       autoplay
       ref="video"
       >
       </video>
       <canvas 
       v-show="imageCaptured"
        ref="canvas"
        class="full-width"
        height="240"
       />
    </div>
    <div class="text-center q-pa-md">
       <q-btn
       v-if="hasCameraSupport"
       @click="captureImage()"
          round
          size="lg"
          color="black"
          icon="eva-camera" />
           <q-file
             v-else
             outlined
             label="Choose an Image"
             accept="image/*"
             @input="captureImageFallback"
             v-model="imageUpload">
            <template
            v-slot:prepend>
            <q-icon
            name="eva-attach-outline"
            />
            </template>
      </q-file>
    </div>
    <div class="row justify-center q-ma-md">
        <q-input
          v-model="post.caption"
          dense
          label="Caption" />
    </div>
    <div class="row justify-center q-ma-md">
           <q-input
          v-model="post.location"
          dense
          :loading="locationLoading"
          label="Location">

        <template v-slot:append>
          <q-btn
          v-if="!locationLoading && locationSupported"
          @click="getLocation"
            round
            dense
            flat
            icon="eva-navigation-2-outline" />
        </template>
      </q-input>

    </div>
    <div class="row justify-center q-mt-lg">
           <q-btn
             unelevated
             rounded
             color="primary"
             label="Post Image" />
    </div>


  </q-page>
</template>

<script>
import { uid } from 'quasar';
require('md-gum-polyfill');
export default {
  name: 'PageCamera',
  data() {
      return{
          post: {
              id: uid(),
              caption: '',
              location: '',
              photo: null,
              date: Date.now(),
          },
          imageCaptured: false,
          hasCameraSupport: true,
         imageUpload: [],
         locationLoading: false

      }
  }, 
  computed: {
      locationSupported() {
          if ('geolocation' in navigator) {
              return true;
          }
          return false;
      }
  },
  methods: {
      initCamera() {
          navigator.mediaDevices.getUserMedia({
              video: true
          }).then(stream => {
              this.$refs.video.srcObject = stream;
          }).catch(error => {
              this.hasCameraSupport = false;
          })
      },
      captureImage() {
          let video = this.$refs.video;
          let canvas = this.$refs.canvas;
          canvas.width = video.getBoundingClientRect().width;
          canvas.height = video.getBoundingClientRect().height;
          let context = canvas.getContext('2d');
          context.drawImage(video, 0, 0, canvas.width, canvas.height);
        this.imageCaptured = true;
        this.post.photo = this.dataURItoBlob(canvas.toDataURL());
        this.disableCamera();
      },
      captureImageFallback(file) {
          this.post.photo = file;

          let canvas = this.$refs.canvas;
          let context = canvas.getContext('2d');

          var reader = new FileReader();
          reader.onload = event => {
            var img = new Image();
            img.onload = () => {
                canvas.width = img.width;
                canvas.height = img.height;
                context.drawImage(img,0,0);
                this.imageCaptured = true;
            }
            img.src = event.target.result;
          }
         reader.readAsDataURL(file); 
      },
      disableCamera() {
          this.$refs.video.srcObject.getVideoTracks().forEach(track => {
              track.stop();
          });
      },
      dataURItoBlob(dataURI) {
        // convert base64 to raw binary data held in a string
        // doesn't handle URLEncoded DataURIs - see SO answer #6850276 for code that does this
        var byteString = atob(dataURI.split(',')[1]);

        // separate out the mime component
        var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

        // write the bytes of the string to an ArrayBuffer
        var ab = new ArrayBuffer(byteString.length);

        // create a view into the buffer
        var ia = new Uint8Array(ab);

        // set the bytes of the buffer to the correct values
        for (var i = 0; i < byteString.length; i++) {
            ia[i] = byteString.charCodeAt(i);
        }

        // write the ArrayBuffer to a blob, and you're done
        var blob = new Blob([ab], {type: mimeString});
        return blob;

        },
        getLocation() {
            this.locationLoading = true;
            navigator.geolocation.getCurrentPosition(pos => {
                console.log(pos);
                this.getCityAndCountry(pos);
            }, err => {
                this.locationError();
            }, {timeout: 7000})
        },
        getCityAndCountry(pos) {
            let apiUrl = `https://geocode.xyz/${pos['coords']['latitude']},${pos['coords']['longitude']}?json=1`;
            this.$axios.get(apiUrl).then(res => {
                console.log(res);
                this.locationSuccess(res);
            }).catch(error => {
                this.locationError();
            })
        },
        locationSuccess(res) {
            this.post.location = res.data.city;
            if (res.data.country) {
                this.post.location += `, ${ res.data.country }`;
            }
            this.locationLoading = false;
        },
        locationError() {
            this.$q.dialog({
                title: 'Error',
                message: 'Could not find your location'
            });
            this.locationLoading = false;
        }
  },
  mounted() {
          this.initCamera();
    },
    beforeDestroy() {
        if (this.hasCameraSupport) {
            this.disableCamera();
        }
    }
}
</script>

<style lang="sass">
    .camera-frame
        border: 20px solid $grey-10
        border-radius: 10px
</style>