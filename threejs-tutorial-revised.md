# Three.js 入门教程：从 0 创建第一个 3D Web 应用

> Three.js 是一个基于 JavaScript 的 Web 3D 引擎，封装了 WebGL，让你在浏览器里就能做出 3D 场景、动画、游戏、数据可视化，甚至 AI + 3D 应用。


---

## 最终效果

- 一个 3D 场景
- 一个会旋转、会打光的立方体
- 鼠标拖拽控制视角（带惯性阻尼，手感更顺）
- 点击立方体切换颜色（小彩蛋）
- 加载真实 3D 模型

```
Browser

        🟩
      ↻  ↻  ↻

Interactive 3D Object
```

---

## 1. Three.js 基础概念

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

对应你更熟悉的游戏引擎概念，方便类比：

| Three.js | 游戏引擎概念 |
|---|---|
| Scene | 世界 |
| Camera | 摄像机 |
| Mesh | 游戏对象 |
| Geometry | 模型形状 |
| Material | 材质 |
| Light | 光照 |
| Renderer | 渲染引擎 |

一句话理解：**Geometry 是骨架，Material 是皮肤，两者合体才是 Mesh（可渲染的物体）。**

---

## 2. 创建项目（含 Vite ——）

> ⚠️ **修正点**：原稿只写了 `npm install three`，但直接用 `<script type="module">` 引入裸模块名 `'three'` 在浏览器里是跑不起来的（会报 "Failed to resolve module specifier" 错误）。Node 项目需要一个打包/开发服务器帮你把 `three` 解析成真实路径 —— 这里用目前最主流、最轻量的 **Vite**。

```bash
mkdir three-demo && cd three-demo
npm create vite@latest . -- --template vanilla
npm install
npm install three
```

目录结构：

```
three-demo/
├── index.html
├── main.js
├── package.json
└── vite.config.js（可选，默认配置已经够用）
```

`package.json` 里 Vite 会自动帮你生成好这几个脚本，不用手写：

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

✅ **检查点**：跑一下 `npm run dev`，浏览器打开终端给出的地址（通常是 `http://localhost:5173`），能看到 Vite 默认页面，说明环境搭好了。

---

## 3. 创建 HTML 页面

`index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>Three.js Demo</title>
  <style>
    body { margin: 0; overflow: hidden; }
  </style>
</head>
<body>
  <script type="module" src="./main.js"></script>
</body>
</html>
```

---

## 4. 创建第一个 3D 世界

### 4.1 引入 Three.js

`main.js`

```javascript
import * as THREE from 'three';
```

---

## 5. 创建 Scene

Scene 是整个 3D 世界的容器，一开始它是空的。

```javascript
const scene = new THREE.Scene();
```

---

## 6. 创建 Camera

Camera 决定你从哪个角度、以什么视野观察这个世界。

```javascript
const camera = new THREE.PerspectiveCamera(
  75,                                    // FOV 视野角度
  window.innerWidth / window.innerHeight, // 宽高比
  0.1,                                    // Near 近裁剪面
  1000                                    // Far 远裁剪面
);
```

小提示：FOV 越大，画面越有"广角镜头"的夸张透视感；一般 45–75 之间比较自然。

---

## 7. 创建 Renderer

Renderer 负责把 3D 世界绘制到浏览器画布上。

```javascript
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);
```

> 💡 加了 `antialias: true`（原稿没有），边缘锯齿会明显减少，观感提升很大，几乎零成本。

流程：

```
Three.js Scene → Renderer → WebGL → Browser Canvas
```

---

## 8. 创建第一个 3D 物体

一个物体 = Geometry（形状）+ Material（材质）→ 合成 Mesh。

### 8.1 Geometry

```javascript
const geometry = new THREE.BoxGeometry(1, 1, 1);
```

### 8.2 Material

```javascript
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
```

### 8.3 Mesh，并加入场景

```javascript
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);
```

---

## 9. 设置 Camera 位置

默认相机和立方体重叠在同一点，什么都看不到。把相机往后退一点：

```javascript
camera.position.z = 5;
```

---

## 10. 第一次渲染

```javascript
renderer.render(scene, camera);
```

**完整代码（第一版，静态渲染）：**

```javascript
import * as THREE from 'three';

const scene = new THREE.Scene();

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
camera.position.z = 5;

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);

renderer.render(scene, camera);
```

```bash
npm run dev
```

🎉 **检查点**：打开浏览器你应该看到一个**静止**的绿色正方形（因为是正对镜头，还看不出是立方体）。如果屏幕是黑的，先检查控制台报错 —— 十有八九是 `import` 路径或 `renderer.domElement` 没挂载。

```
🟩

Green Square (还看不出立体感，属于正常现象)
```

---

## 11. 添加动画，让它转起来

3D 动画的核心思路很朴素：**不停地重新渲染，每次渲染前把物体状态改一点点**。

```javascript
function animate() {
  requestAnimationFrame(animate);

  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;

  renderer.render(scene, camera);
}
animate();
```

把最后一行 `renderer.render(scene, camera);`（第 10 步那一行）换成 `animate();`。

🎉 **检查点**：立方体应该开始绕两个轴旋转，这时候你才第一次真正看出它是个"体"而不是"面"——这是整个教程第一个"哇"时刻。

```
      🟩
    ↻ Rotation
```

**🧩 小挑战**：试着把 `0.01` 改成 `0.05`，感受一下速度差异；再试试只转一个轴，看看视觉效果有什么不同。

---

## 12. 添加真实光照

`MeshBasicMaterial` 是不受光照影响的"自发光"材质，所以之前看到的绿色其实是假的立体感。换成会跟光线互动的材质：

```javascript
const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
```

再加一盏方向光（模拟太阳光）：

```javascript
const light = new THREE.DirectionalLight(0xffffff, 2);
light.position.set(5, 5, 5);
scene.add(light);
```

> ⚠️ **修正点**：原稿光照强度写的是 `1`，配合 `MeshStandardMaterial` 默认参数在新版 Three.js（自 r155 起引擎调整了光照物理单位）下会偏暗，实际效果打折扣。这里给到 `2`，画面会明显亮一些；你也可以自己调这个数字，直到明暗对比舒服为止。

再补一盏微弱的环境光，让阴影面不至于全黑：

```javascript
const ambient = new THREE.AmbientLight(0x404040, 1);
scene.add(ambient);
```

🎉 **检查点**：立方体表面出现明暗渐变，转动时能看到高光随光源角度变化 —— 这才是真正的"3D 质感"。

```
      ☀️
        🟩
Real Lighting with Shading
```

**🧩 小挑战**：把 `light.position.set(5, 5, 5)` 改成 `(-5, 5, 5)` 或 `(0, 10, 0)`，观察高光位置怎么跟着变化。

---

## 13. 添加鼠标控制

不需要重新安装（`three` 里已经自带 `examples/jsm` 下的一整套附加组件），直接导入即可：

```javascript
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
```

> ⚠️ **修正点**：原稿的导入路径是 `three/examples/jsm/controls/OrbitControls.js`，这个路径依然能用，但新版 Three.js 官方文档统一推荐 `three/addons/...` 别名写法，两者指向同一份文件，用新写法更保险、也更贴合最新文档。

```javascript
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;   // 开启惯性阻尼，拖拽手感更顺滑（原稿遗漏）
controls.dampingFactor = 0.05;
```

> ⚠️ **修正点**：开启 `enableDamping` 之后，**必须**在动画循环里每帧调用一次 `controls.update()`，否则阻尼效果不会生效，控制器实际上是"失灵"的。这是新手最容易漏掉的一步：

```javascript
function animate() {
  requestAnimationFrame(animate);

  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;

  controls.update(); // 別忘了这一行
  renderer.render(scene, camera);
}
animate();
```

支持：
- 左键拖拽 → 旋转视角
- 滚轮 → 缩放
- 右键拖拽 → 平移

🎉 **检查点**：拖动鼠标，视角应该跟手且带一点"回弹"的顺滑感，而不是生硬地跟着瞬移。

---

## 14. 加个窗口自适应（原稿完全没提，但几乎每个教程都会踩这个坑）

现在试试把浏览器窗口拉窄——立方体会被拉伸变形。原因是 `camera.aspect` 和 `renderer` 的尺寸从头到尾只设置了一次，没跟着窗口变化更新。补一段监听：

```javascript
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix(); // 改了 aspect 之后必须调这个才生效
  renderer.setSize(window.innerWidth, window.innerHeight);
});
```

✅ **检查点**：拖动浏览器窗口大小，立方体应该始终保持正常比例，不会被压扁或拉长。

---

## 15. 加载真实 3D 模型

实际项目常用格式：

```
.glb / .gltf
```

```javascript
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

const loader = new GLTFLoader();

loader.load(
  'car.glb',
  (gltf) => {
    scene.add(gltf.scene);
  },
  (progress) => {
    // 可选：加载进度回调，做个 loading 条会很有成就感
    console.log(`加载中：${(progress.loaded / progress.total * 100).toFixed(0)}%`);
  },
  (error) => {
    console.error('模型加载失败', error);
  }
);
```

> 💡 补充：原稿只写了成功回调，漏了错误处理。3D 模型加载常见踩坑是路径不对、跨域或文件损坏，加上 `error` 回调能第一时间在控制台看到原因，而不是对着一片空白发呆。

模型去哪找？[Sketchfab](https://sketchfab.com)（很多可免费下载/带 CC 协议）、[Poly Pizza](https://poly.pizza) 都有大量现成的 `.glb`，拖进项目 `public` 目录就能用。

---

## 16. 完整最终代码（合并第 11–14 步）

```javascript
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

const scene = new THREE.Scene();

const camera = new THREE.PerspectiveCamera(
  75, window.innerWidth / window.innerHeight, 0.1, 1000
);
camera.position.z = 5;

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.05;

const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshStandardMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);

const light = new THREE.DirectionalLight(0xffffff, 2);
light.position.set(5, 5, 5);
scene.add(light);

const ambient = new THREE.AmbientLight(0x404040, 1);
scene.add(ambient);

window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
});

function animate() {
  requestAnimationFrame(animate);
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  controls.update();
  renderer.render(scene, camera);
}
animate();
```

走到这一步，你已经拥有一个**能转、能拖、有光影、能自适应窗口**的完整 3D 场景 —— 这已经超过市面上很多"半成品"教程的终点了。

---

## 17. 加分挑战：点击立方体换颜色（原稿没有，纯加分项）

想让教程更有互动感、更有"这是我做的"的成就感，可以加一个点击交互 —— 用 `Raycaster`（射线投射）判断鼠标点没点到物体：

```javascript
const raycaster = new THREE.Raycaster();
const pointer = new THREE.Vector2();
const palette = [0x00ff00, 0xff5566, 0x5566ff, 0xffaa00];
let colorIndex = 0;

renderer.domElement.addEventListener('click', (event) => {
  pointer.x = (event.clientX / window.innerWidth) * 2 - 1;
  pointer.y = -(event.clientY / window.innerHeight) * 2 + 1;

  raycaster.setFromCamera(pointer, camera);
  const hits = raycaster.intersectObject(cube);

  if (hits.length > 0) {
    colorIndex = (colorIndex + 1) % palette.length;
    material.color.setHex(palette[colorIndex]);
  }
});
```

🎉 这一步做完，教程里的"立方体"就正式变成了一个**可交互的小玩具**，而不只是一段展示代码。

---

## 18. 推荐项目结构（项目变大之后）

```
src/
├── main.js
├── scene/
├── camera/
├── objects/
├── models/
├── textures/
├── shaders/
└── animations/
```

类比：这套结构之于 Three.js，大致相当于 `Assets/` 目录之于 Unity。

---

## 19. 学习路线

**Level 1：基础**
```
Scene → Camera → Mesh → Geometry → Material → Animation
```

**Level 2：真实项目**
```
Texture → Lighting → Model Loading → OrbitControls → Post Processing
```

**Level 3：高级图形**
```
Shader → GLSL → Custom Material → WebGPU
```

**Level 4：AI + 3D**
```
LLM → Generate Scene → Three.js → Interactive World
```

---

## 20. AI + Three.js 方向

一个正在快速发展的方向：用 LLM 描述场景，实时生成 3D 内容。

```
用户: "Create a futuristic Mars city"

LLM 生成:
- terrain
- buildings
- robots
- lights

Three.js 渲染:
       🌑 Mars City
       🚀 Robot
       🌌 Space
```

潜在应用：AI Agent 虚拟形象、虚拟世界、AI 游戏、数字孪生、空间计算。

---

## 总结

Three.js 可以理解为：

> **浏览器里的 Unity。**

它把复杂的 WebGL / GPU 编程封装成友好的 JavaScript API，让你能快速做出 3D 网站、游戏、数据可视化、AI 虚拟世界、WebXR 应用。

下一步推荐：
1. Three.js + React Three Fiber（如果你的项目本来就用 React）
2. Shader / GLSL 编程（解锁"高级质感"的钥匙）
3. WebGPU（下一代渲染后端，性能更强）
4. AI + 3D Agent（LLM 实时生成场景）


如果你确实想要静态配图（比如发博客、写笔记），比截图更好的两个选择：
- **录屏转 GIF**（用 macOS 自带截屏录制 + [gifski](https://gif.ski/) 转码，或 Windows 用 ScreenToGif）—— 能保留旋转和交互感，比任何静态图都有说服力；
- 用 Three.js 官方案例页（[threejs.org/examples](https://threejs.org/examples)）里风格类似的作品截图做对比参考，但正文的效果图最好还是自己录的实机效果，更可信也更有成就感。
