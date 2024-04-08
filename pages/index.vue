<template>
    <div>
        <canvas class="webgl"></canvas>
    </div>
</template>
<script setup lang="ts">
    import * as THREE from "three";
    import { OrbitControls } from "three/addons/controls/OrbitControls.js";
    import dColor from '~~/assets/textures/matcaps/door/color.jpg'
    import dAlpha from '~~/assets/textures/matcaps/door/alpha.jpg'
    import dAo from '~~/assets/textures/matcaps/door/ambientOcclusion.jpg'
    import dHeight from '~~/assets/textures/matcaps/door/height.jpg'
    import dNormal from '~~/assets/textures/matcaps/door/normal.jpg'
    import dMetalness from '~~/assets/textures/matcaps/door/metalness.jpg'
    import dRoughness from '~~/assets/textures/matcaps/door/roughness.jpg'

    import wColor from '~~/assets/textures/matcaps/bricks/color.jpg'
    import wAo from '~~/assets/textures/matcaps/bricks/ambientOcclusion.jpg'
    import wNormal from '~~/assets/textures/matcaps/bricks/normal.jpg'
    import wRoughness from '~~/assets/textures/matcaps/bricks/roughness.jpg'

    import gColor from '~~/assets/textures/matcaps/grass/color.jpg'
    import gAo from '~~/assets/textures/matcaps/grass/ambientOcclusion.jpg'
    import gNormal from '~~/assets/textures/matcaps/grass/normal.jpg'
    import gRoughness from '~~/assets/textures/matcaps/grass/roughness.jpg'

    onMounted(() => {
        // Canvas
        const canvas = document.querySelector("canvas.webgl");

        const scene = new THREE.Scene();

        // Fog
        const fog = new THREE.Fog('#262837', 2, 16)
        scene.fog = fog

        /**
         * Textures
        */
        const textureLoader = new THREE.TextureLoader()

        //door texture
        const textureDColor = textureLoader.load(dColor)
        textureDColor.colorSpace = THREE.SRGBColorSpace
        const texturedAlpha = textureLoader.load(dAlpha)
        const texturedAo = textureLoader.load(dAo)
        const texturedHeight = textureLoader.load(dHeight)
        const texturedNormal = textureLoader.load(dNormal)
        const texturedMetalness = textureLoader.load(dMetalness)
        const texturedRoughness = textureLoader.load(dRoughness)

        // walls texture
        const texturewColor = textureLoader.load(wColor)
        texturewColor.colorSpace = THREE.SRGBColorSpace
        const texturewAo = textureLoader.load(wAo)
        const texturewNormal = textureLoader.load(wNormal)
        const texturewRoughness = textureLoader.load(wRoughness)

        // grass texture
        const texturegColor = textureLoader.load(gColor)
        texturegColor.colorSpace = THREE.SRGBColorSpace
        const texturegAo = textureLoader.load(gAo)
        const texturegNormal = textureLoader.load(gNormal)
        const texturegRoughness = textureLoader.load(gRoughness)

        texturegColor.repeat.set(9, 9)
        texturegAo.repeat.set(9, 9)
        texturegNormal.repeat.set(9, 9)
        texturegRoughness.repeat.set(9, 9)

        texturegColor.wrapS = THREE.RepeatWrapping
        texturegAo.wrapS = THREE.RepeatWrapping
        texturegNormal.wrapS = THREE.RepeatWrapping
        texturegRoughness.wrapS = THREE.RepeatWrapping

        texturegColor.wrapT = THREE.RepeatWrapping
        texturegAo.wrapT = THREE.RepeatWrapping
        texturegNormal.wrapT = THREE.RepeatWrapping
        texturegRoughness.wrapT = THREE.RepeatWrapping


        // House 
        const x = 4, y = 2.5, z = 4
        const house = new THREE.Group()
        house.position.y = y / 2
        scene.add(house)

        // Walls
        const walls = new THREE.Mesh(
            new THREE.BoxGeometry(x, y, z),
            new THREE.MeshStandardMaterial({
                map: texturewColor,
                aoMap: texturewAo,
                normalMap: texturewNormal,
                roughnessMap: texturewRoughness
            })
        )

        // roof
        const roof = new THREE.Mesh(
            new THREE.ConeGeometry(x - 0.5, 1.5, z),
            new THREE.MeshStandardMaterial({
                map: texturewColor,
                aoMap: texturewAo,
                normalMap: texturewNormal,
                roughnessMap: texturewRoughness
            })
        )
        roof.position.y = y / 2 + 0.75
        roof.rotation.y = Math.PI / 4

        // door
        const door = new THREE.Mesh(
            new THREE.PlaneGeometry(2.2, 2.2, 100, 100),
            new THREE.MeshStandardMaterial({
                map: textureDColor,
                transparent: true,
                alphaMap: texturedAlpha,
                aoMap: texturedAo,
                displacementMap: texturedHeight,
                displacementScale: 0.1,
                normalMap: texturedNormal,
                metalnessMap: texturedMetalness,
                roughnessMap: texturedRoughness,
            })
        )
        door.position.z = z / 2 + 0.01
        door.position.y = - y / 2 + 1.1

        house.add(walls, roof, door)

        // trees 
        const treesG = new THREE.SphereGeometry(1, 15, 15)
        const treesM = new THREE.MeshStandardMaterial({ color: '#4caf50' })
        const tree1 = new THREE.Mesh(treesG, treesM)
        tree1.scale.set(0.5, 0.5, 0.5)
        tree1.position.set(x / 4, -3 * y / 7, z / 2 + 0.2)

        const tree2 = new THREE.Mesh(treesG, treesM)
        tree2.scale.set(0.3, 0.3, 0.3)
        tree2.position.set(x / 2 - 0.5, -3 * y / 7, z / 2 + 0.5)

        const tree3 = new THREE.Mesh(treesG, treesM)
        tree3.scale.set(0.5, 0.5, 0.5)
        tree3.position.set(-x / 4, -3 * y / 7, z / 2 + 0.1)

        house.add(tree1, tree2, tree3)

        // 

        // Things
        const thingsG = new THREE.BoxGeometry(0.6, 0.8, 0.2)
        const thingsM = new THREE.MeshStandardMaterial({ color: 'grey' })

        for (let i = 0; i < 50; i++) {
            const thing = new THREE.Mesh(thingsG, thingsM)
            const angle = Math.random() * Math.PI * 2
            const radius = 4 + Math.random() * 5
            thing.position.x = Math.sin(angle) * radius
            thing.position.z = Math.cos(angle) * radius
            thing.position.y = 0.3
            thing.rotation.z = Math.random()
            thing.castShadow = true
            scene.add(thing)
        }


        // Floor
        const floor = new THREE.Mesh(
            new THREE.PlaneGeometry(20, 20),
            new THREE.MeshStandardMaterial({
                map: texturegColor,
                aoMap: texturegAo,
                normalMap: texturegNormal,
                roughnessMap: texturegRoughness
            })
        )
        floor.rotation.x = - Math.PI * 0.5
        floor.position.y = 0
        scene.add(floor)

        //Ghosts

        const ghost1 = new THREE.PointLight('#ff0000', 7, 7)
        scene.add(ghost1)
        const ghost2 = new THREE.PointLight('#00ff00', 7, 7)
        scene.add(ghost2)
        const ghost3 = new THREE.PointLight('#0000ff', 7, 7)
        scene.add(ghost3)


        /**
         * Lights
         */
        // Ambient light
        const ambientLight = new THREE.AmbientLight('#ffffff', 0.1)
        scene.add(ambientLight)

        // Directional light
        const moonLight = new THREE.DirectionalLight('#ffffff', 0.051)
        moonLight.position.set(4, 5, - 2)
        scene.add(moonLight)

        // lamb
        const doorLight = new THREE.PointLight('#ff7d46', 3, 5)
        doorLight.position.set(0, y / 2, 2.7)
        house.add(doorLight)

        // const helper  = new THREE.PointLightHelper(doorLight)
        // scene.add(helper)

        /**
        * Shadows
        */
        moonLight.castShadow = true
        moonLight.shadow.mapSize.width = 256 * 3
        moonLight.shadow.mapSize.height = 256
        moonLight.shadow.camera.far = 15

        doorLight.castShadow = true
        doorLight.shadow.mapSize.width = 256
        doorLight.shadow.mapSize.height = 256
        doorLight.shadow.camera.far = 5

        ghost1.castShadow = true
        ghost1.shadow.camera.far = 6
        ghost1.shadow.mapSize.width = 256
        ghost1.shadow.mapSize.height = 256

        ghost2.castShadow = true
        ghost2.shadow.camera.far = 6
        ghost2.shadow.mapSize.width = 256
        ghost2.shadow.mapSize.height = 256

        ghost3.castShadow = true
        ghost3.shadow.camera.far = 6
        ghost3.shadow.mapSize.width = 256
        ghost3.shadow.mapSize.height = 256

        roof.castShadow = true
        walls.castShadow = true
        tree1.castShadow = true
        tree2.castShadow = true
        tree3.castShadow = true


        floor.receiveShadow = true

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
        camera.position.x = 1
        camera.position.y = 2
        camera.position.z = 6
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
        renderer.setClearColor('#262837')

        renderer.shadowMap.enabled = true
        renderer.shadowMap.type = THREE.PCFSoftShadowMap
        // gsap.to(cub2.position, { duration: 1, delay: 1, x: 2 })

        const clock = new THREE.Clock()

        const tick = () => {
            const elapsedTime = clock.getElapsedTime()

            ghost1.position.x = Math.sin(0.25 * Math.PI * elapsedTime) * 5
            ghost1.position.z = Math.cos(0.25 * Math.PI * elapsedTime) * 5
            ghost1.position.y = Math.abs(Math.sin(0.25 * Math.PI * elapsedTime))

            ghost2.position.x = Math.sin(0.25 * Math.PI * elapsedTime + 2 * Math.PI / 3) * 5
            ghost2.position.z = Math.cos(0.25 * Math.PI * elapsedTime + 2 * Math.PI / 3) * 5
            ghost2.position.y = Math.abs(Math.sin(0.25 * Math.PI * elapsedTime))

            ghost3.position.x = Math.sin(0.25 * Math.PI * elapsedTime - 2 * Math.PI / 3) * 5
            ghost3.position.z = Math.cos(0.25 * Math.PI * elapsedTime - 2 * Math.PI / 3) * 5
            ghost3.position.y = Math.abs(Math.sin(0.25 * Math.PI * elapsedTime))

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