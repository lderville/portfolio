<template>

  <canvas id="webgl">Hello</canvas>
  
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'
import earthmap1k from '../assets/img/earth/earthmap1k.jpg'
import earthbump from '../assets/img/earth/earthbump.jpg'
import earthCloud from '../assets/img/earth/earthCloud.png'


export default {
  name: 'EarthView',
   mounted() {
// global variables
let githubgltf = '../model/github3d.glb'
let scene;
let raycaster = new THREE.Raycaster();
const pointer = new THREE.Vector2();
let githublink;


let camera;
let renderer;
const canvas = document.getElementById('webgl');
const textureLoader = new THREE.TextureLoader();

// scene setup
scene = new THREE.Scene();

// camera setup
const fov = 60;
const aspect = window.innerWidth / window.innerHeight;
const near = 0.1;
const far = 1000;

camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
camera.position.z = 2;
scene.add(camera);

// renderer setup
renderer = new THREE.WebGLRenderer({
    canvas: canvas,
    antialias: true,
});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio((window.devicePixelRatio) ? window.devicePixelRatio : 1);
renderer.autoClear = false;
renderer.setClearColor(0x000000, 0.0);

// orbit control setup
const controls = new OrbitControls(camera, renderer.domElement);
 controls.autoRotate = true;
controls.minDistance =1.5;
controls.maxDistance = 2.5;

// earth geometry
const earthGeometry = new THREE.SphereGeometry(0.6, 32, 32);

// earth material
const earthMaterial = new THREE.MeshPhongMaterial({
    map: textureLoader.load(earthmap1k),
    bumpMap: textureLoader.load(earthbump),
    bumpScale: 0.3
});

// earth mesh
const earthMesh = new THREE.Mesh(earthGeometry, earthMaterial);
earthMesh.name = "earth"
scene.add(earthMesh);

// cloud Geometry
const cloudGeometry = new THREE.SphereGeometry(0.63, 32, 32);

// cloud metarial
const cloudMetarial = new THREE.MeshPhongMaterial({
    map: textureLoader.load(earthCloud),
    transparent: true,
});

// cloud mesh
const cloudMesh = new THREE.Mesh(cloudGeometry, cloudMetarial);
cloudMesh.name ="earth"
scene.add(cloudMesh);


// our github 
var loader = new GLTFLoader();
let github
let githubColor;
let objectGithubLoad = [];
loader.load( githubgltf, function ( gltf )
{

    
    github = gltf.scene;  
    
    github.scale.set(0.5, 0.5, 0.5);
    gltf.scene.position.x =0.5;
    gltf.scene.position.y = 0;
    gltf.scene.position.z = 1;
    objectGithubLoad = [github.children[0], github.children[1], github.children[2]]
    githubColor = github.children[0].material.color.getHex()
    console.log(objectGithubLoad[0].material.color.getHex())
    
    scene.add(github)
    console.log('sucess')
} );


// // galaxy geometry
// const starGeometry = new THREE.SphereGeometry(80, 64, 64);

// // galaxy material
// const starMaterial = new THREE.MeshBasicMaterial({
//     map : textureLoader.load(galaxy),
//     side: THREE.BackSide
// });

// // galaxy mesh
// const starMesh = new THREE.Mesh(starGeometry, starMaterial);
// scene.add(starMesh);

// ambient light
const ambientlight = new THREE.AmbientLight(0xffffff, 0.2);
scene.add(ambientlight);

// point light
const pointLight = new THREE.PointLight(0xffffff, 1)
pointLight.position.set(5, 3, 5);
scene.add(pointLight);


// handling resizing
window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
    render();
}, false);




// spinning animation
const animate = () => {
 
    requestAnimationFrame(animate);
    // starMesh.rotation.y -= 0.002;
    if ( typeof github !== 'undefined' ){
        
        github.rotation.y -=0.01
        
        
    }
    // console.log(githubpivot)
    
    earthMesh.rotation.y -= 0.0015;
    cloudMesh.rotation.y -= 0.001;
    controls.update();
    
    render();
    
};

document.addEventListener( 'mousemove', onPointerMove );
document.addEventListener( 'click', clickedObject );


// rendering
let meshpointer // object selected 

const render = () => {

raycaster.setFromCamera( pointer, camera );

const intersects = raycaster.intersectObjects( scene.children, true );



if ( intersects.length > 0 ) {
    // console.log(intersects[0].object.name)
    meshpointer = intersects[0].object
    
    if( typeof githublink !== "undefined" && meshpointer !== githublink && githublink.material.color.getHex() !== githubColor ){
        removeColor()
    }  
    // if(meshpointer.name === "earth"){
    //      console.log('terre')
    // }

    if(meshpointer.name === "vert" || meshpointer.name === "Cylinder" || meshpointer.name === "Curve002"){
        githublink = meshpointer
        // console.log(githubColor + ' ' + meshpointer.material.color.getHex() )
        if(meshpointer.material.color.getHex() === githubColor){ 
            meshpointer.material.color.set( '#b73bc4' );
            
        }

    }
}
else{
    
    //  console.log(githublink.material.color.getHex() + " " +githubColor )
     
    if (githublink.material.color.getHex() !== githubColor) {
        removeColor()
        
    }

}

    renderer.render(scene, camera);
}

animate();


function removeColor() {
    githublink.material.color.set(githubColor)
}

function onPointerMove( event ) {
    pointer.x = ( event.clientX / window.innerWidth ) * 2 - 1;
    pointer.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

}

function clickedObject() {
    if(githublink.material.color.getHex()===12008388) location.replace("https://www.w3schools.com")


}





    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#webgl
{
margin: 0;
}
</style>
