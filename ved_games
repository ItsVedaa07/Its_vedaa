<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Realistic FPS Arena</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { display: block; }
    #hud {
      position: absolute;
      top: 10px;
      left: 10px;
      color: white;
      font-size: 20px;
      font-family: Arial, sans-serif;
    }
  </style>
</head>
<body>
  <div id="hud">Score: 0 | Health: 100 | Weapon: Pistol | Ammo: 12/12</div>
  <script src="https://cdn.jsdelivr.net/npm/three@0.150.1/build/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.150.1/examples/js/controls/PointerLockControls.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.150.1/examples/js/loaders/GLTFLoader.js"></script>
  <script>
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias:true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    // Skybox
    const skyTexture = new THREE.CubeTextureLoader()
      .setPath('https://threejs.org/examples/textures/cube/skyboxsun25deg/')
      .load(['px.jpg','nx.jpg','py.jpg','ny.jpg','pz.jpg','nz.jpg']);
    scene.background = skyTexture;

    // Lighting
    const sun = new THREE.DirectionalLight(0xffffff, 1);
    sun.position.set(50, 100, 50);
    sun.castShadow = true;
    scene.add(sun);
    scene.add(new THREE.AmbientLight(0x404040));

    // Floor with grass texture
    const grassTexture = new THREE.TextureLoader().load('https://threejs.org/examples/textures/grasslight-big.jpg');
    grassTexture.wrapS = grassTexture.wrapT = THREE.RepeatWrapping;
    grassTexture.repeat.set(20, 20);
    const floor = new THREE.Mesh(
      new THREE.PlaneGeometry(200, 200),
      new THREE.MeshStandardMaterial({ map: grassTexture })
    );
    floor.rotation.x = -Math.PI/2;
    scene.add(floor);

    // Arena walls with brick texture
    const brickTexture = new THREE.TextureLoader().load('https://threejs.org/examples/textures/brick_diffuse.jpg');
    function createWall(x,z,w,h) {
      const wall = new THREE.Mesh(
        new THREE.BoxGeometry(w,h,2),
        new THREE.MeshStandardMaterial({ map: brickTexture })
      );
      wall.position.set(x,h/2,z);
      scene.add(wall);
    }
    createWall(0,-100,200,20);
    createWall(0,100,200,20);
    createWall(-100,0,2,20);
    createWall(100,0,2,20);

    // Props (crates/barrels)
    const crate = new THREE.Mesh(
      new THREE.BoxGeometry(5,5,5),
      new THREE.MeshStandardMaterial({ color:0x885522 })
    );
    crate.position.set(20,2.5,20);
    scene.add(crate);

    const barrel = new THREE.Mesh(
      new THREE.CylinderGeometry(2,2,6,16),
      new THREE.MeshStandardMaterial({ color:0x3333aa })
    );
    barrel.position.set(-15,3,-25);
    scene.add(barrel);

    // Controls
    const controls = new THREE.PointerLockControls(camera, document.body);
    document.body.addEventListener('click', () => controls.lock());
    const move = { forward:false,backward:false,left:false,right:false };
    const velocity = new THREE.Vector3();

    document.addEventListener('keydown', e=>{
      if(e.code==='KeyW') move.forward=true;
      if(e.code==='KeyS') move.backward=true;
      if(e.code==='KeyA') move.left=true;
      if(e.code==='KeyD') move.right=true;
    });
    document.addEventListener('keyup', e=>{
      if(e.code==='KeyW') move.forward=false;
      if(e.code==='KeyS') move.backward=false;
      if(e.code==='KeyA') move.left=false;
      if(e.code==='KeyD') move.right=false;
    });

    // HUD
    let score=0, health=100;
    let currentWeapon="Pistol";
    let ammo={Pistol:{bullets:12,max:12},Rifle:{bullets:30,max:30},Shotgun:{bullets:8,max:8}};
    const hud=document.getElementById("hud");
    function updateHUD(){
      hud.textContent=`Score: ${score} | Health: ${health} | Weapon: ${currentWeapon} | Ammo: ${ammo[currentWeapon].bullets}/${ammo[currentWeapon].max}`;
    }

    // Weapon damage
    const weaponDamage={Pistol:25,Rifle:15,Shotgun:40};

    // Weapons
    const loader=new THREE.GLTFLoader();
    let weapons={},activeWeapon;
    function loadWeapon(name,url,pos){
      loader.load(url,gltf=>{
        const w=gltf.scene;
        w.scale.set(0.5,0.5,0.5);
        w.position.copy(pos);
        weapons[name]=w;
        if(!activeWeapon){activeWeapon=w;camera.add(activeWeapon);updateHUD();}
      });
    }
    loadWeapon("Pistol","https://rawcdn.githack.com/KhronosGroup/glTF-Sample-Models/master/2.0/Gun/GlTF/Gun.gltf",new THREE.Vector3(0.3,-0.3,-1.5));
    loadWeapon("Rifle","https://rawcdn.githack.com/KhronosGroup/glTF-Sample-Models/master/2.0/Avocado/glTF/Avocado.gltf",new THREE.Vector3(0.3,-0.3,-1.5));
    loadWeapon("Shotgun","https://rawcdn.githack.com/KhronosGroup/glTF-Sample-Models/master/2.0/Duck/glTF/Duck.gltf",new THREE.Vector3(0.3,-0.3,-1.5));

    document.addEventListener('keydown',e=>{
      if(["Digit1","Digit2","Digit3"].includes(e.code)){
        if(activeWeapon) camera.remove(activeWeapon);
        if(e.code==="Digit1"){activeWeapon=weapons["Pistol"];currentWeapon="Pistol";}
        if(e.code==="Digit2"){activeWeapon=weapons["Rifle"];currentWeapon="Rifle";}
        if(e.code==="Digit3"){activeWeapon=weapons["Shotgun"];currentWeapon="Shotgun";}
        if(activeWeapon) camera.add(activeWeapon);
        updateHUD();
      }
      if(e.code==="KeyR"){ammo[currentWeapon].bullets=ammo[currentWeapon].max;updateHUD();}
    });

    // Enemies with health bars
    let enemies=[];
    function spawnEnemy(){
      loader.load("https://rawcdn.githack.com/KhronosGroup/glTF-Sample-Models/master/2.0/CesiumMan/glTF/CesiumMan.gltf",gltf=>{
        const enemy=gltf.scene;
        enemy.scale.set(0.5,0.5,0.5);
        enemy.position.set((Math.random()-0.5)*180,0,(Math.random()-0.5)*180);
        enemy.userData.health=100;

        // Health bar
        const barGeo=new THREE.PlaneGeometry(2,0.2);
        const barMat=new THREE.MeshBasicMaterial({color:0xff0000});
        const bar=new THREE.Mesh(barGeo,barMat);
        bar.position.set(0,2,0);
        enemy.add(bar);
        enemy.userData.bar=bar;

        scene.add(enemy);
        enemies.push(enemy);
      });
    }
    for(let i=0;i<5;i++) spawnEnemy();

    // Shooting
    window.addEventListener("mousedown",()=>{
      if(!controls.isLocked) return;
      if(ammo[currentWeapon].bullets<=0){alert("Out of ammo! Press R to reload.");return;}
      ammo[currentWeapon].bullets--;updateHUD();

      const ray=new THREE.Raycaster();
      ray.setFromCamera({x:0,y:0},camera);
      const hits=ray.intersectObjects(enemies,true);
      if(hits.length>0){
        const enemy=hits[0].object.parent;
        enemy.userData.health-=weaponDamage[currentWeapon];
        enemy.userData.bar.scale.x=enemy.userData.health/100; // shrink health bar
        if(enemy.userData.health<=0){
          score++;updateHUD();
          scene.remove(enemy);
          enemies=enemies.filter(e=>e!==enemy);
          spawnEnemy();
        }
      }
    });

    // Enemy AI
    function updateEnemies(){
      enemies.forEach(enemy=>{
        const dir=new THREE.Vector
