<template>
    <div class="contatti">
  </div>
    <v-container>
        <div ref = "container">
  </div>
    </v-container>
</template>
<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
import earthTexture from '../assets/2k_earth_daymap.jpg';
//import Hls from 'hls.js';


export default {
    name: 'WorldView',
    data() {
        return {
            camera: null,
            renderer: null,
            controls: null,
            earthRadius: 1,
            radio: [],
            selectedRadio: null,
            
            isFavorite: false
        };
    },
    mounted() {
        this.init();
        this.animate();
        this.getRadios();
        window.addEventListener('resize', this.handleWindowResize);
        this.raycaster = new THREE.Raycaster();
        this.mouse = new THREE.Vector2();
        window.addEventListener('click', this.onDocumentMouseClick);

    },
    beforeUnmount() {
        window.removeEventListener('resize', this.handleWindowResize);
        window.removeEventListener('click', this.onDocumentMouseClick);

    },
    methods: {
        init() {
            this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
            this.camera.position.z = 2;

            this.renderer = new THREE.WebGLRenderer();
            const width = window.innerWidth * 0.96; // Imposta la larghezza al 80% della finestra del browser
            const height = window.innerHeight * 0.70; // Imposta l'altezza al 60% della finestra del browser
            this.renderer.setSize(width, height);
            this.$refs.container.appendChild(this.renderer.domElement);

            this.controls = new OrbitControls(this.camera, this.renderer.domElement);
            this.controls.enableDamping = true;

            const scene = new THREE.Scene();

            const geometry = new THREE.SphereGeometry(this.earthRadius, 64, 64); // Increase segments for smoother surface
            const texture = new THREE.TextureLoader().load(earthTexture);
            const material = new THREE.MeshPhongMaterial({ map: texture });
            const earth = new THREE.Mesh(geometry, material);
            scene.add(earth);

            const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
            scene.add(ambientLight);

            const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
            directionalLight.position.set(1, 1, 1).normalize();
            scene.add(directionalLight);

            this.scene = scene;

            this.raycaster = new THREE.Raycaster();
            this.raycaster.params.Points.threshold = 0.05;
        },
        animate() {
            requestAnimationFrame(this.animate);

            if (this.controls) {
                this.controls.update();
            }

            if (this.renderer && this.scene && this.camera) {
                this.renderer.render(this.scene, this.camera);
            }
        }
    }
}

</script>