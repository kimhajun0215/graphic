<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Webcam 3D Model Animation</title>
<style>
    body {
        margin: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        background-color: #f0f0f0;
    }
    canvas {
        width: 400px;
        height: 300px;
    }
</style>
</head>
<body>
<video id="video" autoplay playsinline style="display: none;"></video>
<canvas id="canvas"></canvas>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/stats.js/r17/Stats.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/dat-gui/0.7.7/dat.gui.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/ml5/1.0.5/ml5.min.js"></script>

<script>
// Initialize Three.js components
let scene, camera, renderer, controls, model;

// Initialize ml5.js
let faceapi;

async function setup() {
    // Initialize Three.js scene
    scene = new THREE.Scene();

    // Initialize camera
    camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.z = 5;

    // Initialize renderer
    renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('canvas'), antialias: true });
    renderer.setSize(400, 300);

    // Add ambient light to the scene
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
    scene.add(ambientLight);

    // Add directional light to the scene
    const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
    directionalLight.position.set(1, 1, 1).normalize();
    scene.add(directionalLight);

    // Initialize ml5.js FaceAPI
    faceapi = ml5.faceApi('FaceLandmarks', modelReady);

    // Start capturing webcam video
    const video = document.getElementById('video');
    navigator.mediaDevices.getUserMedia({ video: true })
        .then(stream => {
            video.srcObject = stream;
            video.play();
        })
        .catch(err => console.error(err));
}

// Callback function when ml5.js model is ready
function modelReady() {
    console.log('Model is ready!');
    faceapi.detect(gotFaces);
}

// Callback function when faces are detected
function gotFaces(err, result) {
    if (err) {
        console.error(err);
        return;
    }
    // Clear previous models
    scene.remove(model);

    // Create a new BoxGeometry as placeholder for the detected face
    const geometry = new THREE.BoxGeometry(1, 1, 1);

    // Create a basic material for the geometry
    const material = new THREE.MeshPhongMaterial({ color: 0x00ff00 });

    // Create a mesh with geometry and material
    model = new THREE.Mesh(geometry, material);

    // If faces are detected, update the position of the model based on face position
    if (result.length > 0) {
        const face = result[0].parts;
        const nose = face.nose[0];
        const jaw = face.jawBottom[0];
        const mouth = face.mouth[0];
        const eyes = face.rightEye.concat(face.leftEye);

        // Calculate the position of the model based on face landmarks
        const posX = (jaw.x + mouth.x) / 2;
        const posY = (jaw.y + nose.y) / 2;
        const posZ = nose.z;

        // Set the position of the model
        model.position.set(posX, posY, posZ);
    }

    // Add the model to the scene
    scene.add(model);

    // Call the detect function again for continuous detection
    faceapi.detect(gotFaces);
}

// Function to render the scene
function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
}

// Initialize the setup and start animation loop
setup();
animate();
</script>
</body>
</html>
