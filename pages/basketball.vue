<template>
    <div>
        <canvas class="webgl"></canvas>
    </div>
</template>
<script setup lang="ts">
    import * as THREE from "three";
    import { OrbitControls } from "three/addons/controls/OrbitControls.js";

    onMounted(() => {
        // Canvas
        const canvas = document.querySelector("canvas.webgl");

        const scene = new THREE.Scene();

        // Ambient light
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
        const directionalLight = new THREE.DirectionalLight(0xffffff, 1.1)
        directionalLight.castShadow = true
        directionalLight.position.x = 2
        scene.add(ambientLight,directionalLight)
        ///

        const material = new THREE.MeshStandardMaterial()
        material.roughness = 0.7

        const plane = new THREE.Mesh(
            new THREE.PlaneGeometry(5, 5),
            material
        )
        plane.receiveShadow = true
        plane.rotation.x = - Math.PI * 0.5
        plane.position.y = - 0.5

        const textureLoader = new THREE.TextureLoader()
        const matcapTexture = textureLoader.load('textures/matcaps/9.png')
        const sphereMaterial = new THREE.MeshMatcapMaterial({ matcap: matcapTexture })
                
        const sphere = new THREE.Mesh(
            new THREE.SphereGeometry(0.5, 32, 32),
            sphereMaterial
        )
        sphere.castShadow = true
        scene.add(plane,sphere)








        // Sizes
        const sizes = {
            width: window.innerWidth,
            height: window.innerHeight,
        };

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

        /**
         * Camera
         */
        // Base camera
        const camera = new THREE.PerspectiveCamera(75, sizes.width / sizes.height, 0.1, 100)
        camera.position.x = 2
        camera.position.y = 1
        camera.position.z = 4
        scene.add(camera)

        // Controls
        const controls = new OrbitControls(camera, canvas as HTMLElement)
        controls.enableDamping = true

        // Renderer
        const renderer = new THREE.WebGLRenderer({
            canvas: canvas!,
        });
        renderer.setSize(sizes.width, sizes.height);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.shadowMap.enabled = true
        // gsap.to(cub2.position, { duration: 1, delay: 1, x: 2 })

        const clock = new THREE.Clock()

        const x = () => {
            const elapsedTime = clock.getElapsedTime()
            // sphere.position.x = Math.sin(elapsedTime * Math.PI*0.5) 
            // sphere.position.z = Math.cos(elapsedTime * Math.PI *0.5)
            sphere.position.y = Math.abs(1.5*Math.sin(elapsedTime * Math.PI*1.5))


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