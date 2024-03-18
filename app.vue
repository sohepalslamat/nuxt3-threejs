<template>
  <div>
    <canvas class="webgl"></canvas>
  </div>
</template>
<script setup lang="ts">
  import * as THREE from "three";
  import { OrbitControls } from "three/addons/controls/OrbitControls.js";
  import { FontLoader } from 'three/examples/jsm/loaders/FontLoader.js'
  import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry.js'
import { gsap } from "gsap";

  onMounted(() => {
    // Canvas
    const canvas = document.querySelector("canvas.webgl");

    const scene = new THREE.Scene();

    const textureLoader = new THREE.TextureLoader()
    const matcapTexture = textureLoader.load('textures/matcaps/4.png')
    const ourMaterial = new THREE.MeshMatcapMaterial({ matcap: matcapTexture })


    const fontLoader = new FontLoader()
    fontLoader.load('fonts/Englebert_Regular.json', (font) => {
      const textGeometry = new TextGeometry(
        'Sohaib Alslamat',
        {
          font: font,
          size: 0.5,
          height: 0.2,
          curveSegments: 12,
          bevelEnabled: true,
          bevelThickness: 0.03,
          bevelSize: 0.02,
          bevelOffset: 0,
          bevelSegments: 5
        }
      )
      textGeometry.center()
      
      const text = new THREE.Mesh(textGeometry, ourMaterial)
      // gsap.to(text.rotation, { duration: 1, delay: 1, x: 2 })
      scene.add(text)
    })
    const donutGeometry = new THREE.TorusGeometry(0.3, 0.2, 20, 45)
    
    for (let i = 0; i < 100; i++) {
      const donut = new THREE.Mesh(donutGeometry, ourMaterial)
      donut.position.x = (Math.random() - 0.5) * 10
      donut.position.y = (Math.random() - 0.5) * 10
      donut.position.z = (Math.random() - 0.5) * 10

      donut.rotation.x = Math.random() * Math.PI
      donut.rotation.y = Math.random() * Math.PI
      donut.rotation.z = Math.random() * Math.PI

      const scale = Math.random() * 1.5
      donut.scale.set(scale, scale, scale)

      scene.add(donut)
    }

    ///

    // Sizes
    const sizes = {
      width: window.innerWidth,
      height: window.innerHeight,
    };

    // resize
    window.addEventListener("resize", () => {
      // Update size
      sizes.height = window.innerHeight;
      sizes.width = window.innerWidth;

      // Update camera
      camera.aspect = sizes.width / sizes.height;
      camera.updateProjectionMatrix();

      // Update render
      renderer.setSize(sizes.width, sizes.height);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio, 4));
    });

    // fullscreen
    window.addEventListener("dblclick", () => {
      // we added this conditions because fullscreenElement doesn't work in Safari
      const fullscreenElement =
        document.fullscreenElement || (document as any).webkitFullscreenElement;

      if (!fullscreenElement) {
        if (canvas?.requestFullscreen) {
          canvas.requestFullscreen();
        } else if ((canvas as any)?.webkitRequestFullscreen!) {
          (canvas as any).webkitRequestFullscreen();
        }
      } else {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if ((document as any).webkitExitFullscreen) {
          (document as any).webkitExitFullscreen();
        }
      }
    });

    // Camera
    const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height);
    camera.position.z = 4;
    const controls = new OrbitControls(camera, canvas as HTMLCanvasElement);
    controls.enableDamping = true;
    scene.add(camera);

    // ...

    // Renderer
    const renderer = new THREE.WebGLRenderer({
      canvas: canvas!,
    });
    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

    // gsap.to(cub2.position, { duration: 1, delay: 1, x: 2 })

    // const clock = new THREE.Clock()

    const x = () => {
      // Time
      // const elapsedTime = clock.getElapsedTime()

      // cub.rotation.x = Math.cos(elapsedTime*3);
      // cub2.rotation.y = Math.sin(elapsedTime);
      // camera.position.y = Math.cos(elapsedTime*3)
      // camera.lookAt(cub.position)
      controls.update();
      renderer.render(scene, camera);
      window.requestAnimationFrame(x);
    };
    x();
  });
</script>
<style>
  .webgl {
    position: fixed;
    top: 0;
    left: 0;
    outline: none;
  }
</style>
