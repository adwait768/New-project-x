# New-project-x
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Tata Nexon 3D Structural Explorer</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; }
        body { background: #0f172a; color: #f8fafc; font-family: system-ui, -apple-system, sans-serif; overflow: hidden; }
        #canvas-container { width: 100vw; height: 100vh; display: block; }
        
        /* Mobile-First APK UI Overlay */
        .ui-panel {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            width: 92%;
            max-width: 480px;
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(56, 189, 248, 0.3);
            border-radius: 16px;
            padding: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }
        .header {
            position: absolute;
            top: 20px;
            left: 20px;
            right: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            pointer-events: none;
        }
        .title-box {
            background: rgba(15, 23, 42, 0.8);
            padding: 10px 16px;
            border-radius: 12px;
            border-left: 4px solid #38bdf8;
        }
        .title-box h1 { font-size: 1.1rem; letter-spacing: 0.5px; color: #38bdf8; }
        .title-box p { font-size: 0.75rem; color: #94a3b8; }
        
        .control-group { margin-bottom: 12px; }
        .control-group label {
            display: flex;
            justify-content: space-between;
            font-size: 0.85rem;
            margin-bottom: 6px;
            color: #cbd5e1;
            font-weight: 600;
        }
        input[type="range"] {
            width: 100%;
            accent-color: #38bdf8;
            height: 6px;
            border-radius: 3px;
            cursor: pointer;
        }
        .btn-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 8px;
        }
        button {
            background: #1e293b;
            color: #e2e8f0;
            border: 1px solid #334155;
            padding: 10px 8px;
            border-radius: 8px;
            font-size: 0.78rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
        }
        button.active {
            background: #0284c7;
            border-color: #38bdf8;
            color: #ffffff;
        }
        button:active { transform: scale(0.96); }
    </style>
    <!-- Load Three.js and OrbitControls via CDN -->
    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
                "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
            }
        }
    </script>
</head>
<body>

    <div class="header">
        <div class="title-box">
            <h1>TATA NEXON 3D</h1>
            <p>Interactive Structural & EV Architecture</p>
        </div>
    </div>

    <div id="canvas-container"></div>

    <div class="ui-panel">
        <div class="control-group">
            <label>
                <span>Explode Structural View</span>
                <span id="explode-val">0%</span>
            </label>
            <input type="range" id="explode-slider" min="0" max="100" value="0">
        </div>
        <div class="btn-grid">
            <button id="btn-wireframe">X-Ray Cage</button>
            <button id="btn-rotate" class="active">Auto-Rotate</button>
            <button id="btn-reset">Reset View</button>
        </div>
    </div>

    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

        // 1. Scene, Camera & Renderer Setup
        const container = document.getElementById('canvas-container');
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x0f172a, 0.04);

        const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
        camera.position.set(5, 3.5, 6);

        const renderer = new THREE.WebGLRenderer({ antialias: true, powerPreference: "high-performance" });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.shadowMap.enabled = true;
        container.appendChild(renderer.domElement);

        // 2. Orbit Controls
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;
        controls.autoRotate = true;
        controls.autoRotateSpeed = 1.5;
        controls.maxPolarAngle = Math.PI / 2 + 0.1; // Prevent going far below ground

        // 3. Lighting & Grid
        const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
        scene.add(ambientLight);

        const dirLight = new THREE.DirectionalLight(0x38bdf8, 2);
        dirLight.position.set(5, 10, 7);
        scene.add(dirLight);

        const backLight = new THREE.DirectionalLight(0xffffff, 1);
        backLight.position.set(-5, -5, -5);
        scene.add(backLight);

        const grid = new THREE.GridHelper(20, 40, 0x38bdf8, 0x1e293b);
        grid.position.y = -0.8;
        scene.add(grid);

        // 4. Procedural 3D Tata Nexon Structural Assembly
        const carGroup = new THREE.Group();
        scene.add(carGroup);

        // Materials
        const bodyMat = new THREE.MeshStandardMaterial({ color: 0x0284c7, roughness: 0.2, metalness: 0.7 });
        const cageMat = new THREE.MeshStandardMaterial({ color: 0x94a3b8, roughness: 0.5, metalness: 0.8 });
        const batteryMat = new THREE.MeshStandardMaterial({ color: 0x10b981, emissive: 0x047857, emissiveIntensity: 0.3 });
        const engineMat = new THREE.MeshStandardMaterial({ color: 0xf59e0b, roughness: 0.4, metalness: 0.6 });
        const wheelMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.8 });
        const rimMat = new THREE.MeshStandardMaterial({ color: 0xe2e8f0, metalness: 0.9, roughness: 0.2 });

        // Component 1: High-Strength Steel Chassis & Safety Cage
        const chassisGroup = new THREE.Group();
        const floorGeo = new THREE.BoxGeometry(3.6, 0.15, 1.7);
        const floorMesh = new THREE.Mesh(floorGeo, cageMat);
        chassisGroup.add(floorMesh);

        const pillarGeo = new THREE.BoxGeometry(2.2, 0.9, 1.55);
        const cageWireframe = new THREE.Mesh(pillarGeo, new THREE.MeshStandardMaterial({ color: 0xcbd5e1, wireframe: true }));
        cageWireframe.position.set(-0.2, 0.5, 0);
        chassisGroup.add(cageWireframe);
        carGroup.add(chassisGroup);

        // Component 2: Upper Body Shell & Roof
        const bodyShellGroup = new THREE.Group();
        const mainBodyGeo = new THREE.BoxGeometry(3.8, 0.65, 1.8);
        const mainBody = new THREE.Mesh(mainBodyGeo, bodyMat);
        mainBody.position.y = 0.35;
        bodyShellGroup.add(mainBody);

        const cabinGeo = new THREE.BoxGeometry(2.1, 0.55, 1.5);
        const cabin = new THREE.Mesh(cabinGeo, bodyMat);
        cabin.position.set(-0.25, 0.9, 0);
        bodyShellGroup.add(cabin);
        carGroup.add(bodyShellGroup);

        // Component 3: EV Ziptron Battery Pack (Underfloor)
        const batteryGroup = new THREE.Group();
        const battGeo = new THREE.BoxGeometry(2.0, 0.18, 1.3);
        const battery = new THREE.Mesh(battGeo, batteryMat);
        battery.position.set(-0.1, -0.2, 0);
        batteryGroup.add(battery);
        carGroup.add(batteryGroup);

        // Component 4: Front Powertrain / Revotorq Engine Unit
        const engineGroup = new THREE.Group();
        const engGeo = new THREE.BoxGeometry(0.7, 0.5, 0.9);
        const engine = new THREE.Mesh(engGeo, engineMat);
        engine.position.set(1.25, 0.25, 0);
        engineGroup.add(engine);
        carGroup.add(engineGroup);

        // Component 5: 4 Wheels & Suspension Sub-assemblies
        const wheels = [];
        const wheelPositions = [
            { x: 1.2, y: -0.3, z: 0.95, side: 1 },  // Front Left
            { x: 1.2, y: -0.3, z: -0.95, side: -1 }, // Front Right
            { x: -1.2, y: -0.3, z: 0.95, side: 1 }, // Rear Left
            { x: -1.2, y: -0.3, z: -0.95, side: -1 } // Rear Right
        ];

        wheelPositions.forEach(pos => {
            const wGroup = new THREE.Group();
            const tireGeo = new THREE.CylinderGeometry(0.42, 0.42, 0.28, 24);
            tireGeo.rotateX(Math.PI / 2);
            const tire = new THREE.Mesh(tireGeo, wheelMat);

            const rimGeo = new THREE.CylinderGeometry(0.26, 0.26, 0.3, 12);
            rimGeo.rotateX(Math.PI / 2);
            const rim = new THREE.Mesh(rimGeo, rimMat);

            // Suspension strut
            const strutGeo = new THREE.CylinderGeometry(0.05, 0.05, 0.5, 8);
            const strut = new THREE.Mesh(strutGeo, cageMat);
            strut.position.set(0, 0.3, -pos.side * 0.15);

            wGroup.add(tire, rim, strut);
            wGroup.position.set(pos.x, pos.y, pos.z);
            wGroup.userData = { origX: pos.x, origY: pos.y, origZ: pos.z, side: pos.side };
            wheels.push(wGroup);
            carGroup.add(wGroup);
        });

        // 5. Interactive UI Logic (Explode View, X-Ray, Auto-Rotate)
        const slider = document.getElementById('explode-slider');
        const explodeVal = document.getElementById('explode-val');

        slider.addEventListener('input', (e) => {
            const val = parseFloat(e.target.value);
            explodeVal.textContent = `${val}%`;
            const factor = val / 100;

            // Animate structural separation
            bodyShellGroup.position.y = factor * 1.6;
            batteryGroup.position.y = -factor * 1.1;
            engineGroup.position.x = factor * 1.2;
            engineGroup.position.y = factor * 0.4;

            wheels.forEach(w => {
                w.position.z = w.userData.origZ + (w.userData.side * factor * 0.9);
                w.position.y = w.userData.origY - (factor * 0.3);
            });
        });

        // Toggle X-Ray Wireframe
        let isWireframe = false;
        const btnWireframe = document.getElementById('btn-wireframe');
        btnWireframe.addEventListener('click', () => {
            isWireframe = !isWireframe;
            bodyMat.wireframe = isWireframe;
            bodyMat.transparent = isWireframe;
            bodyMat.opacity = isWireframe ? 0.35 : 1.0;
            btnWireframe.classList.toggle('active', isWireframe);
        });

        // Toggle Auto-Rotate
        const btnRotate = document.getElementById('btn-rotate');
        btnRotate.addEventListener('click', () => {
            controls.autoRotate = !controls.autoRotate;
            btnRotate.classList.toggle('active', controls.autoRotate);
        });

        // Reset View
        document.getElementById('btn-reset').addEventListener('click', () => {
            slider.value = 0;
            slider.dispatchEvent(new Event('input'));
            camera.position.set(5, 3.5, 6);
            controls.target.set(0, 0, 0);
        });

        // Handle Window Resize
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });

        // 6. Render Loop
        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            renderer.render(scene, camera);
        }
        animate();
    </script>
</body>
</html>
