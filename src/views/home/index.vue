<template>
    <div ref="threeRef"></div>
</template>
<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue'
import { Scene, PerspectiveCamera, WebGLRenderer, Color,DirectionalLight, AxesHelper, MeshLambertMaterial, PlaneGeometry, Mesh, BoxGeometry, SphereGeometry, SpotLight, AmbientLight } from 'three'
// 引入轨道控制器扩展库OrbitControls.js
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

export default defineComponent({
    setup() {

        const threeRef = ref()

        function init() {
            const scene = new Scene()
            const axes = new AxesHelper(20)
            scene.add(axes)

            const renderer = new WebGLRenderer({ antialias: true })
            renderer.setClearColor(new Color(0xEEEEEE))
            renderer.setSize(window.innerWidth, window.innerHeight)


            const planeGeometry = new PlaneGeometry(100, 100);
            const planeMaterial = new MeshLambertMaterial({ color: 0xffffff });
            const plane = new Mesh(planeGeometry, planeMaterial)
            plane.rotation.x = -0.5 * Math.PI
            plane.position.x = 0
            plane.position.y = 0;
            plane.position.z = 0;
            //地面接受阴影
            plane.receiveShadow = true;
            scene.add(plane)

            //盒子几何体
            const cubeGeometry = new BoxGeometry(4, 4, 14)
            const cubeMaterial = new MeshLambertMaterial({ color: 0xff0000 });
            const cube = new Mesh(cubeGeometry, cubeMaterial)
            //cube.rotation.x=-0.5*Math.PI
            //开启阴影
            cube.castShadow = true;
            cube.position.x = 14
            cube.position.y = 2;
            cube.position.z = 2;
            scene.add(cube)

            //球形几何体
            const sphereGeometry = new SphereGeometry(4, 20, 20);
            const sphereMaterial = new MeshLambertMaterial({ color: 0x77777ff })
            const sphere = new Mesh(sphereGeometry, sphereMaterial);
            sphere.castShadow = true
            sphere.position.x = 2
            sphere.position.y = 2
            sphere.position.z = 2
            scene.add(sphere)
            // 平行光
            const directionalLight = new DirectionalLight(0xffffff, 1);
            // 平行光设置产生阴影的光源对象,开启光源阴影的计算功能
            directionalLight.castShadow = true;
            scene.add(directionalLight)
            const ambientLight = new AmbientLight(0xcccccc)
            scene.add(ambientLight)
            const camera = new PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000)
            camera.position.x = -30;
            camera.position.y = 40;
            camera.position.z = 30;
            camera.lookAt(scene.position)
            threeRef.value.appendChild(renderer.domElement)
            //渲染器开启阴影
            renderer.shadowMap.enabled = true
            // 设置相机控件轨道控制器OrbitControls
            const controls = new OrbitControls(camera, renderer.domElement);
            // 如果OrbitControls改变了相机参数，重新调用渲染器渲染三维场景
            controls.addEventListener('change', function () {
                renderer.render(scene, camera); //执行渲染操作
            });//监听鼠标、键盘事件


            renderer.render(scene, camera)

            function animate() {
                requestAnimationFrame(animate)
                cube.rotation.x += 0.01
                cube.rotation.y += 0.01
                renderer.render(scene, camera)
            }
            animate()

            //resize
            window.addEventListener('resize', () => {
                renderer.setSize(window.innerWidth, window.innerHeight)
                camera.aspect = window.innerWidth / window.innerHeight
                camera.updateProjectionMatrix()
            })

        }

        onMounted(() => {
            init();
        })
        return {
            threeRef
        }
    },
})
</script>