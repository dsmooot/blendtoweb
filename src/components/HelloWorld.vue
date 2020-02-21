<template>
  <div class="wrapper">
    <!-- The canvas element is used to draw the 3D scene -->
    <img src="./../assets/logo.png" alt />
    <!-- <div class="loading" id="js-loader"> -->
    <!-- <div class="loader"></div> -->
    <!-- </div> -->
    <canvas id="c"></canvas>
  </div>
</template>

<script>
import * as Three from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

export default {
  name: "ThreeTest",
  data() {
    return {
      scene: null,
      renderer: null,
      camera: null,
      model: null, // Our character
      neck: null, // Reference to the neck bone in the skeleton
      waist: null, // Reference to the waist bone in the skeleton
      possibleAnims: null, // Animations found in our file
      mixer: null, // THREE.js animations mixer
      idle: null, // Idle, the default state our character returns to
      clock: null, // Used for anims, which run to a clock instead of frame rate
      currentlyAnimating: false, // Used to check whether characters neck is being used in another anim
      raycaster: null, // Used to detect the click on our character
      loaderAnim: null
    };
  },
  methods: {
    init() {
      const MODEL_PATH = "/assets/dude.glb";

      this.clock = new Three.Clock(); // Used for anims, which run to a clock instead of frame rate
      this.currentlyAnimating = false; // Used to check whether characters neck is being used in another anim
      this.raycaster = new Three.Raycaster(); // Used to detect the click on our character
      // this.loaderAnim = document.getElementById("js-loader");

      const canvas = document.querySelector("#c");
      const backgroundColor = 0x505050;

      // Init the scene
      this.scene = new Three.Scene();
      this.scene.background = new Three.Color(backgroundColor);
      this.scene.fog = new Three.Fog(backgroundColor, 60, 100);

      // Init the renderer
      this.renderer = new Three.WebGLRenderer({ canvas, antialias: true });
      this.renderer.shadowMap.enabled = true;
      this.renderer.setPixelRatio(window.devicePixelRatio);
      document.body.appendChild(this.renderer.domElement);

      // Add a camera
      this.camera = new Three.PerspectiveCamera(
        50,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      this.camera.position.z = 30;
      this.camera.position.x = 0;
      this.camera.position.y = -3;

      //setup Loader
      var loader = new GLTFLoader();

      loader.load(
        MODEL_PATH,
        function(gltf) {
          this.model = gltf.scene;
          let fileANimations = gltf.animations;

          this.scene.add(this.model);
        },
        undefined,
        function(error) {
          console.error(error);
        }
      );

      // Add lights
      let hemiLight = new Three.HemisphereLight(0xffffff, 0xffffff, 0.61);
      hemiLight.position.set(0, 50, 0);
      // Add hemisphere light to scene
      this.scene.add(hemiLight);

      let d = 8.25;
      let dirLight = new Three.DirectionalLight(0xffffff, 0.54);
      dirLight.position.set(-8, 12, 8);
      dirLight.castShadow = true;
      dirLight.shadow.mapSize = new Three.Vector2(1024, 1024);
      dirLight.shadow.camera.near = 0.1;
      dirLight.shadow.camera.far = 1500;
      dirLight.shadow.camera.left = d * -1;
      dirLight.shadow.camera.right = d;
      dirLight.shadow.camera.top = d;
      dirLight.shadow.camera.bottom = d * -1;
      // Add directional Light to scene
      this.scene.add(dirLight);

      // Floor
      let floorGeometry = new Three.PlaneGeometry(5000, 5000, 1, 1);
      let floorMaterial = new Three.MeshPhongMaterial({
        color: 0xeeeeee,
        shininess: 0
      });

      let floor = new Three.Mesh(floorGeometry, floorMaterial);
      floor.rotation.x = -0.5 * Math.PI; // This is 90 degrees by the way
      floor.receiveShadow = true;
      floor.position.y = -11;
      this.scene.add(floor);
    },
    update() {
      if (this.resizeRendererToDisplaySize(this.renderer)) {
        const canvas = this.renderer.domElement;
        this.camera.aspect = canvas.clientWidth / canvas.clientHeight;
        this.camera.updateProjectionMatrix();
      }
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.update);
    },
    resizeRendererToDisplaySize(renderer) {
      const canvas = this.renderer.domElement;
      let width = window.innerWidth;
      let height = window.innerHeight;
      let canvasPixelWidth = canvas.width / window.devicePixelRatio;
      let canvasPixelHeight = canvas.height / window.devicePixelRatio;

      const needResize =
        canvasPixelWidth !== width || canvasPixelHeight !== height;
      if (needResize) {
        this.renderer.setSize(width, height, false);
      }
      return needResize;
    }
  },
  mounted() {
    this.init();
    this.update();
  }
};
</script>

<style scoped>
#wrapper {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
  display: block;
}
#c {
  width: 100%;
  height: 100%;
}
</style>
    
