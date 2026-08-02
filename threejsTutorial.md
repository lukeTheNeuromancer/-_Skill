# Three.js 入门教程：从 0 创建第一个 3D Web 应用

> Three.js 是一个基于 JavaScript 的 Web 3D 引擎，它封装了 WebGL，让开发者可以在浏览器中创建 3D 场景、动画、游戏、数据可视化和 AI + 3D 应用。

本教程目标：

最终实现：

- 一个 3D 场景
- 一个绿色旋转立方体
- 鼠标控制 3D 视角
- 加载真实 3D 模型

效果：

```
Browser

        🟩
      ↻  ↻  ↻

Interactive 3D Object
```

---

# 1. Three.js 基础概念

Three.js 的核心结构：

```
Scene
 |
 +-- Camera
 |
 +-- Light
 |
 +-- Mesh
       |
       +-- Geometry
       |
       +-- Material

        ↓

      WebGL

        ↓

       GPU
```

对应游戏引擎概念：

| Three.js | 游戏引擎 |
|---|---|
| Scene | 世界 |
| Camera | 摄像机 |
| Mesh | 游戏对象 |
| Geometry | 模型形状 |
| Material | 材质 |
| Light | 光照 |
| Renderer | 渲染引擎 |

---

# 2. 创建项目

创建目录：

```
three-demo/

├── index.html
└── main.js
```

初始化项目：

```bash
npm init -y
```

安装 Three.js：

```bash
npm install three
```

---

# 3. 创建 HTML 页面

`index.html`

```html
<!DOCTYPE html>
<html>

<head>

<title>
Three.js Demo
</title>

<style>

body {
    margin:0;
    overflow:hidden;
}

</style>

</head>


<body>

<script 
type="module"
src="./main.js">
</script>


</body>

</html>
```

---

# 4. 创建第一个 3D 世界

## 4.1 引入 Three.js

`main.js`

```javascript
import * as THREE from 'three';
```

---

# 5. 创建 Scene

Scene 是整个 3D 世界。

```javascript
const scene = new THREE.Scene();
```

现在：

```
Scene

(empty world)
```

---

# 6. 创建 Camera

Camera 是观察世界的视角。

```javascript
const camera =
new THREE.PerspectiveCamera(
    75,
    window.innerWidth /
    window.innerHeight,
    0.1,
    1000
);
```

参数：

```
PerspectiveCamera(
    FOV,
    Aspect Ratio,
    Near,
    Far
)
```

---

# 7. 创建 Renderer

Renderer 负责把 3D 世界绘制到浏览器。

```javascript
const renderer =
new THREE.WebGLRenderer();


renderer.setSize(
    window.innerWidth,
    window.innerHeight
);


document.body.appendChild(
    renderer.domElement
);
```

流程：

```
Three.js Scene

        ↓

    Renderer

        ↓

     WebGL

        ↓

    Browser Canvas
```

---

# 8. 创建第一个 3D Object

Three.js 中一个物体由：

```
Geometry
+
Material

↓

Mesh
```

组成。


## 8.1 创建 Cube Geometry

```javascript
const geometry =
new THREE.BoxGeometry(
    1,
    1,
    1
);
```

生成：

```
+------+
|      |
| Cube |
|      |
+------+
```

---

## 8.2 创建 Material

```javascript
const material =
new THREE.MeshBasicMaterial({

    color:0x00ff00

});
```

绿色材质。

---

## 8.3 创建 Mesh

```javascript
const cube =
new THREE.Mesh(
    geometry,
    material
);
```

加入 Scene：

```javascript
scene.add(cube);
```

现在：

```
Scene

 |
 +-- Cube
```

---

# 9. 设置 Camera 位置

默认：

```
Camera
 |
Cube

(overlap)
```

移动 Camera：

```javascript
camera.position.z = 5;
```

现在：

```
Camera


        Cube

```

---

# 10. 第一次渲染

```javascript
renderer.render(
    scene,
    camera
);
```

完整代码：

```javascript
import * as THREE from 'three';


const scene =
new THREE.Scene();



const camera =
new THREE.PerspectiveCamera(
75,
window.innerWidth /
window.innerHeight,
0.1,
1000
);



const renderer =
new THREE.WebGLRenderer();



renderer.setSize(
window.innerWidth,
window.innerHeight
);



document.body.appendChild(
renderer.domElement
);



const geometry =
new THREE.BoxGeometry();



const material =
new THREE.MeshBasicMaterial({

color:0x00ff00

});



const cube =
new THREE.Mesh(
geometry,
material
);



scene.add(cube);



camera.position.z=5;



renderer.render(
scene,
camera
);
```

运行：

```
npm run dev
```

你会看到：

```
🟩

Green Cube
```

---

# 11. 添加动画

3D 动画的核心：

不断重新渲染。

使用：

```javascript
requestAnimationFrame()
```

添加：

```javascript
function animate(){


requestAnimationFrame(
    animate
);


cube.rotation.x +=0.01;


cube.rotation.y +=0.01;



renderer.render(
    scene,
    camera
);


}


animate();
```

效果：

```
      🟩

    ↻ Rotation

```

---

# 12. 添加真实光照

`MeshBasicMaterial` 不受光影响。

改成：

```javascript
MeshStandardMaterial
```

```javascript
const material =
new THREE.MeshStandardMaterial({

color:0x00ff00

});
```

添加光：

```javascript
const light =
new THREE.DirectionalLight(
    0xffffff,
    1
);


light.position.set(
    5,
    5,
    5
);


scene.add(light);
```

现在：

```
      ☀️


        🟩

Real Lighting
```

---

# 13. 添加鼠标控制

安装：

```bash
npm install three
```

导入：

```javascript
import {
OrbitControls
}
from 
'three/examples/jsm/controls/OrbitControls.js';
```

创建：

```javascript
const controls =
new OrbitControls(
camera,
renderer.domElement
);
```

支持：

- 左键旋转
- 滚轮缩放
- 右键移动

---

# 14. 加载真实 3D 模型

实际项目一般使用：

```
.glb
.gltf
```

例如：

```
car.glb

    ↓

Three.js

    ↓

Browser 3D Car
```

加载：

```javascript
import {
GLTFLoader
}
from
'three/examples/jsm/loaders/GLTFLoader.js';



const loader =
new GLTFLoader();



loader.load(
'car.glb',

(model)=>{

scene.add(
model.scene
);

}

);
```

---

# 15. 推荐项目结构

大型项目：

```
src/

├── main.js

├── scene/
│
├── camera/
│
├── objects/
│
├── models/
│
├── textures/
│
├── shaders/
│
└── animations/

```

类似：

```
Unity Project

=

Three.js Project
```

---

# 16. 学习路线

## Level 1：基础

```
Scene
Camera
Mesh
Geometry
Material
Animation
```

---

## Level 2：真实项目

```
Texture

Lighting

Model Loading

OrbitControls

Post Processing
```

---

## Level 3：高级图形

```
Shader

GLSL

Custom Material

WebGPU
```

---

## Level 4：AI + 3D

```
LLM

 ↓

Generate Scene

 ↓

Three.js

 ↓

Interactive World
```

---

# 17. AI + Three.js 方向

未来很有潜力：

## AI 生成 3D 世界

例如：

用户：

```
Create a futuristic Mars city
```

LLM：

```
Generate:

- terrain
- buildings
- robots
- lights
```

Three.js:

```
Render

       🌑 Mars City

       🚀 Robot

       🌌 Space
```

应用：

- AI Agent Avatar
- Virtual World
- AI Game
- Digital Twin
- Spatial Computing


---

# 总结

Three.js 可以理解为：

> **浏览器里的 Unity。**

它把复杂的 WebGL / GPU 编程封装成 JavaScript API，让开发者可以快速创建：

- 3D 网站
- 游戏
- 数据可视化
- AI 虚拟世界
- Web XR 应用


下一步推荐学习：

1. Three.js + React Three Fiber
2. Shader 编程
3. WebGPU
4. AI + 3D Agent
