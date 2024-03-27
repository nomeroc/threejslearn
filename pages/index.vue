<template>
    <canvas class="webgl"></canvas>
</template>

<script setup>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
import gsap from 'gsap';

onMounted(() => {
    //Scene
    const scene = new THREE.Scene();
    // scene.background = new THREE.Color('rgba(100%, 0%, 0%)');

    //Create out sphere
    const geometry = new THREE.SphereGeometry(3, 64, 64);
    const material = new THREE.MeshStandardMaterial({
        color: '#ff0033',
        roughness: 0.5,
    });
    const mesh = new THREE.Mesh(geometry, material);
    mesh.position.set(0, 0, 0);
    scene.add(mesh);

    //Sizes
    const sizes = {
        width: window.innerWidth,
        height: window.innerHeight,
    };

    //Light
    const light = new THREE.PointLight(0xffffff, 100, 0);
    light.position.set(10, 10, 10);
    scene.add(light);

    //Camera
    const camera = new THREE.PerspectiveCamera(
        85,
        sizes.width / sizes.height,
        0.1,
        100
    );
    camera.position.z = 10;
    scene.add(camera);

    //Renderer
    const canvas = document.querySelector('.webgl');
    const renderer = new THREE.WebGLRenderer({
        canvas,
        antialias: true,
        // alpha: true,
    });
    renderer.setSize(sizes.width, sizes.height);
    renderer.setPixelRatio(2);
    renderer.render(scene, camera);

    //Controls
    const controls = new OrbitControls(camera, canvas);
    controls.enableDamping = true;
    controls.enablePan = false;
    controls.enableZoom = false;
    controls.autoRotate = true;
    controls.autoRotateSpeed = 5;

    //Resize
    window.addEventListener('resize', () => {
        //Update Sizes
        sizes.width = window.innerWidth;
        sizes.height = window.innerHeight;
        //Update Camera
        camera.aspect = sizes.width / sizes.height;
        camera.updateProjectionMatrix();
        renderer.setSize(sizes.width, sizes.height);
    });

    const loop = () => {
        controls.update();

        mesh.rotation.x += 0.2;
        renderer.render(scene, camera);
        window.requestAnimationFrame(loop);
    };
    loop();

    //Timeline
    const tl = gsap.timeline({ defaults: { duration: 1 } });
    tl.fromTo(mesh.scale, { z: 0, x: 0, y: 0 }, { z: 1, x: 1, y: 1 });

    //Mouse Animation Color

    let mouseDown = false;
    let rgb = [];

    window.addEventListener('mousedown', () => (mouseDown = true));
    window.addEventListener('mouseup', () => (mouseDown = false));
    window.addEventListener('touchstart', () => (mouseDown = true));
    window.addEventListener('touchend', () => (mouseDown = false));

    window.addEventListener('mousemove', (e) => {
        if (mouseDown) {
            rgb = [
                Math.round((e.pageX / sizes.width) * 255),
                Math.round((e.pageY / sizes.height) * 255),
                150,
            ];
            //Let's animate
            let newColor = new THREE.Color(`rgb(${rgb.join(',')})`);
            gsap.to(mesh.material.color, {
                r: newColor.r,
                g: newColor.g,
                b: newColor.b,
            });
        }
    });
    window.addEventListener('touchmove', (e) => {
        if (mouseDown) {
            // Assuming mouseDown is used as a general flag for "interaction active"
            // Get the first touch point
            var touch = e.touches[0];

            rgb = [
                Math.round((touch.pageX / sizes.width) * 255),
                Math.round((touch.pageY / sizes.height) * 255),
                150,
            ];
            //Let's animate
            let newColor = new THREE.Color(`rgb(${rgb.join(',')})`);
            gsap.to(mesh.material.color, {
                r: newColor.r,
                g: newColor.g,
                b: newColor.b,
            });
        }
    });
});
</script>

<style scoped></style>
