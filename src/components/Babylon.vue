<template>
  <canvas ref="bjsCanvas"></canvas>
</template>

<script setup>
import { onMounted, ref } from "vue";
import * as BABYLON from "@babylonjs/core";
import "@babylonjs/loaders/glTF"
import diceUrl from "../assets/model/dice.glb?url"
import bgmUrl from "../assets/sound/BGM.wav?url"


const createScene = (canvas) => {
  const engine = new BABYLON.Engine(canvas)
  const scene = new BABYLON.Scene(engine);

  const camera = new BABYLON.ArcRotateCamera("camera", -Math.PI / 2, Math.PI / 2.5, 100, new BABYLON.Vector3(0, 0, 0));
  camera.attachControl(canvas, true);

  const light1 = new BABYLON.HemisphericLight("light", new BABYLON.Vector3(0, 1, 0));
  BABYLON.SceneLoader.AppendAsync(diceUrl).then((result) => {
    console.log(result);
    
    result.meshes[2].position.y = 15;
    var anim = generateDiceAnim();
    result.meshes[3].animations = [];
    result.meshes[3].animations.push(anim);
    scene.beginAnimation(result.meshes[3], 0, 30, true);
  });
  const ground = BABYLON.MeshBuilder.CreateGround("ground", {width:120, height:120});
  // const sound = new BABYLON.Sound("bgm", bgmUrl, scene, null, { loop: true, autoplay: true });
  engine.runRenderLoop(() => {
    scene.render();
  });

  window.addEventListener("resize", function () {
    engine.resize();
  });


}
const generateDiceAnim = () =>{
  const animDiceSock = new BABYLON.Animation(
    "DiceAnim", 
    "rotation.z", 
    30,
    BABYLON.Animation.ANIMATIONTYPE_FLOAT,
    BABYLON.Animation.ANIMATIONLOOPMODE_CYCLE
    );

  const diceSockAnimKeys = [];
  diceSockAnimKeys.push({
    frame: 0,
    value: 0
  })
  diceSockAnimKeys.push({
    frame: 30,
    value: 2 * Math.PI
  })

  animDiceSock.setKeys(diceSockAnimKeys);
  return animDiceSock;
}

const bjsCanvas = ref(null);

onMounted(() => {
  if (bjsCanvas.value) {
    createScene(bjsCanvas.value);
  }
});
</script>

<style scoped>
canvas {
  width: 100%;
}
</style>