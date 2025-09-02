import React, { useRef, useEffect } from "react";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

const GlobeScene = () => {
  const mountRef = useRef(null);

  useEffect(() => {
    // 场景
    const scene = new THREE.Scene();

    // 星空背景
    const loader = new THREE.TextureLoader();
    loader.load(
      "https://raw.githubusercontent.com/pmndrs/drei-assets/main/starfield.jpg",
      (texture) => {
        scene.background = texture;
      }
    );

    // 相机
    const camera = new THREE.PerspectiveCamera(
      75,
      mountRef.current.clientWidth / mountRef.current.clientHeight,
      0.1,
      1000
    );
    camera.position.z = 3;

    // 渲染器
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(
      mountRef.current.clientWidth,
      mountRef.current.clientHeight
    );
    mountRef.current.appendChild(renderer.domElement);

    // 地球
    const earthGeometry = new THREE.SphereGeometry(1, 64, 64);
    const earthMaterial = new THREE.MeshPhongMaterial({
      map: loader.load("https://threejs.org/examples/textures/land_ocean_ice_cloud_2048.jpg"),
      shininess: 20,
    });
    const earthMesh = new THREE.Mesh(earthGeometry, earthMaterial);
    scene.add(earthMesh);

    // 光源
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
    scene.add(ambientLight);

    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.7);
    directionalLight.position.set(5, 3, 5);
    scene.add(directionalLight);

    // 交互控制
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;

    // 动画循环
    const animate = () => {
      requestAnimationFrame(animate);
      controls.update();
      renderer.render(scene, camera);
    };
    animate();

    // 清理
    return () => {
      mountRef.current.removeChild(renderer.domElement);
    };
  }, []);

  return (
    <div
      ref={mountRef}
      style={{ width: "100%", height: "600px", borderRadius: "12px", overflow: "hidden" }}
    ></div>
  );
};

export default GlobeScene;
