<template>
    <div>
        <canvas class="webgl"></canvas>
    </div>
</template>
<script setup lang="ts">
    import { gsap } from "gsap";
import * as THREE from "three";
    import { OrbitControls } from "three/addons/controls/OrbitControls.js";

    onMounted(() => {
        // Canvas
        const canvas = document.querySelector("canvas.webgl");

        const scene = new THREE.Scene();

        // Ambient light
        // const ambientLight = new THREE.AmbientLight(0xffffff, 0.5)
        // const directionalLight = new THREE.DirectionalLight(0xffffff, 1.1)
        // directionalLight.castShadow = true
        // directionalLight.position.x = 2
        // scene.add(ambientLight, directionalLight)
        ///

        /**
         * Galaxy
         */

        const settings = {
            count: 100000,
            size: 0.01,
            radius: 4,
            branches: 3,
            spin: 1,
            randomness: 0.35,
            randomnessPower: 3,
            insideColor : '#ff6030',
            outsideColor : '#1b3984',
        }

        const generateGalaxy = () => {

            // geometry
            const geometry = new THREE.BufferGeometry()

            const position = new Float32Array(settings.count*3)
            const colors = new Float32Array(settings.count*3)

            for (let i = 0; i < settings.count; i++) {
                const i3 = i * 3
                const angle = i * ((2 * Math.PI / settings.branches))
                const radius = Math.random() * settings.radius
                const spinAngle = settings.spin * radius

                const randomX = Math.pow(Math.random(), settings.randomnessPower) * radius * settings.randomness * (Math.random() > 0.5 ? 1 : -1)
                const randomY = Math.pow(Math.random(), settings.randomnessPower) * radius * settings.randomness * (Math.random() > 0.5 ? 1 : -1)
                const randomZ = Math.pow(Math.random(), settings.randomnessPower) * radius * settings.randomness * (Math.random() > 0.5 ? 1 : -1)

                position[i3] = radius * Math.cos(angle + spinAngle) + randomX
                position[i3 + 1] = randomY
                position[i3 + 2] = radius * Math.sin(angle + spinAngle) + randomZ

                // colors
                const colorInside = new THREE.Color(settings.insideColor)
                const colorOutside = new THREE.Color(settings.outsideColor)
                const mixedColor = colorInside.clone()
                mixedColor.lerp(colorOutside, radius / settings.radius)

                colors[i3] = mixedColor.r
                colors[i3 + 1] = mixedColor.g
                colors[i3 + 2] = mixedColor.b
            }

            geometry.setAttribute(
                'position',
                new THREE.BufferAttribute(position, 3)
            )
            geometry.setAttribute(
                'color',
                new THREE.BufferAttribute(colors, 3)
            )
            //

            /**
             * material
             */

            const material = new THREE.PointsMaterial({
                size: settings.size,
                sizeAttenuation: true,
                depthWrite: false,
                blending: THREE.AdditiveBlending,
                vertexColors: true
            })

            /**
             * points
             */
            const points = new THREE.Points(geometry, material)
            scene.add(points)
            gsap.to(points.rotation, { rotation: 1, y: 10, duration: 120})
        }

        generateGalaxy()

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
        camera.position.y = 2
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

        const tick = () => {
            const elapsedTime = clock.getElapsedTime()

            
            // sphere.position.x = Math.sin(elapsedTime * Math.PI*0.5) 
            // sphere.position.z = Math.cos(elapsedTime * Math.PI *0.5)

            controls.update();
            renderer.render(scene, camera);
            window.requestAnimationFrame(tick);
        };
        tick();
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