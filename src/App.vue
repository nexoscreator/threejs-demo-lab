<template>
  <div ref="sceneContainer" class="scene-container">
    <div id="info">NexDemo Lab</div>
  </div>
</template>

<script setup>
import { onMounted, ref, render } from "vue";
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";

const sceneContainer = ref(null);

onMounted(() => {
  const canvas = sceneContainer.value;

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0xa0a0a0);

  const camera = new THREE.PerspectiveCamera(
    45,
    window.innerWidth / window.innerHeight,
    0.1,
    500
  );
  camera.position.set(0, 10, -10);
  camera.lookAt(0, -2, 0);

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  canvas.appendChild(renderer.domElement);

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;
  controls.target.set(0, 10, 10);
  // Plane
  {
    const loader = new THREE.TextureLoader();
    const texture = loader.load("../../resources/dark/texture_08.png");
    texture.wrapS = THREE.RepeatWrapping;
    texture.wrapT = THREE.RepeatWrapping;
    texture.magFilter = THREE.NearestFilter;
    texture.colorSpace = THREE.SRGBColorSpace;
    const repeats = 50 / 2;
    texture.repeat.set(repeats, repeats);

    const planeGeo = new THREE.PlaneGeometry(50, 50);
    const planeMat = new THREE.MeshPhongMaterial({
      map: texture,
      side: THREE.DoubleSide,
    });
    const mesh = new THREE.Mesh(planeGeo, planeMat);
    mesh.rotation.x = 3.14159 * -0.5;
    scene.add(mesh);
  }
  // A Box
  {
    const loader = new THREE.TextureLoader();
    const texture = loader.load("../../resources/other/texture_02.png");
    const cubeGeo = new THREE.BoxGeometry(1, 1, 1);
    const cubeMat = new THREE.MeshPhongMaterial({ map: texture }); // color: 0x44aa88
    const mesh = new THREE.Mesh(cubeGeo, cubeMat);
    mesh.position.set(5, 0.5, -1);
    scene.add(mesh);
  }
  // A Cone
  {
    const loader = new THREE.TextureLoader();
    const texture = loader.load("../../resources/other/texture_04.png");
    const coneGeo = new THREE.ConeGeometry(0.75, 2, 16);
    const coneMat = new THREE.MeshPhongMaterial({ map: texture }); // color: 0x44aa88
    const mesh = new THREE.Mesh(coneGeo, coneMat);
    mesh.position.set(-3, 1, 0);
    scene.add(mesh);
  }
  // A Cylinder
  {
    const loader = new THREE.TextureLoader();
    const texture = loader.load("../../resources/other/texture_03.png");
    const cylinderGeo = new THREE.CylinderGeometry(0.5, 0.5, 2, 16);
    const cylinderMat = new THREE.MeshPhongMaterial({ map: texture }); // color: 0x44aa88
    const mesh = new THREE.Mesh(cylinderGeo, cylinderMat);
    mesh.position.set(3, 2, 0);
    scene.add(mesh);
  }
  // A Sphere
  {
    const loader = new THREE.TextureLoader();
    const texture = loader.load("../../resources/other/texture_03.png");
    const sphereGeo = new THREE.SphereGeometry(1, 32, 16);
    const sphereMat = new THREE.MeshPhongMaterial({ map: texture }); // color: 0x8844aa
    const mesh = new THREE.Mesh(sphereGeo, sphereMat);
    mesh.position.set(-5, 1.25, 0);
    scene.add(mesh);
  }
  // A Lines
  {
    const material = new THREE.LineBasicMaterial({ color: 0xaa8844 });
    const points = [];
    points.push(new THREE.Vector3(15, 0, -15));
    points.push(new THREE.Vector3(15, 0, 15));
    points.push(new THREE.Vector3(-15, 0, 15));
    points.push(new THREE.Vector3(-15, 0, -15));
    points.push(new THREE.Vector3(15, 0, -15));

    const geometry = new THREE.BufferGeometry().setFromPoints(points);
    const line = new THREE.Line(geometry, material);
    scene.add(line);
  }
  // A Car
  {
    const loader = new GLTFLoader();
    loader.load(
      "../../resources/source/Lamborghini_Aventador.glb",
      function (gltf) {
        scene.add(gltf.scene);
      },
      undefined,
      function (error) {
        console.error(error);
      }
    );
  }
  // Lights
  {
    const directionLight = new THREE.DirectionalLight(0xffffff, 4);
    directionLight.position.set(-1, 2, 4);
    scene.add(directionLight);

    const ambientLight = new THREE.AmbientLight(0xffffff, 2.5);
    scene.add(ambientLight);

    // const hemisphereLight = new THREE.HemisphereLight(0xffffff, 0x444444);
    // scene.add(hemisphereLight);
  }

  // scene.fog = new THREE.Fog(0xa0a0a0, 10, 100);

  const animate = function () {
    requestAnimationFrame(animate);
    controls.update();
    // cube.rotation.x += 0.01;
    // cube.rotation.y += 0.01;
    renderer.render(scene, camera);
  };

  animate();
});
</script>

<style>
.scene-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

#info {
  position: absolute;
  top: 10px;
  width: 100%;
  color: aqua;
  text-align: center;
  z-index: 100;
  display: block;
}
</style>
