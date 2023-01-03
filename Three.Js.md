## ThreeJS ?

> 是 JavaScript 编写的`WebGL`第三方库，提供了非常多的3D显示功能；



#### WebGL

> `Web Graphics Library`
>
> 一种**3D绘图协议**；
>
> 这种绘图技术标准允许把`JavaScript`和`OpenGL ES 2.0`结合在一起；
>
> 通过增加`OpenGL ES 2.0`的一个`JavaScript`绑定；
>
> `WebGL`可以为`HTML5 Canvas`提供硬件`3D`加速渲染；





## 开始( Begin )

> ThreeJS 开始基本的应用；



### 获取元素

> ThreeJS 的渲染基于 HTML 中的 canvas 元素标签；
>
> 首先要获取页面中的**画布元素**`canvas`；
>
> ```javascript
> // obtain <canvas> element use for display
> const canvas = document.getElementById("display")
> ```



### 尺寸

> 需要给画布场景提供一个现实的尺寸；
>
> ```javascript
> // Create view port size
> const size = {
>      width: window.innerWidth,
>      height: window.innerHeight,
> };
> ```



### 场景

> 创建一个显示画面的场景；
>
> ```javascript
> // Create Scene
> const scene = new THREE.Scene();
> ```



### 相机

> 场景需要提供一个**摄像机**来显示画面；
>
> ```javascript
> // Create Camera
> // 此处 PerspectiveCamera() 表示透视相机
> const camera = new THREE.PerspectiveCamera(
>      75, // 相机焦距，数值越大，物体越小
>      size.width / size.height, // 相机显示区域的纵横比
>    )
>    
> // position
> // 相机在空间坐标系上的位置：(x, y, z)
> camera.position.set(4, 1, 1)
> 
> // rotation
> // 相机以 x 轴为轴心旋转 90 度
> camera.rotation.x = Math.PI / 2
> 
> // Camera always look position
> // 通过 lookAt() 方法，让相机始终朝着这个位置
> // 提供一个 3 维的向量
> camera.lookAt(mesh.position)
> // 通过 Vector3() 方法创建一个 3 维向量
> camera.lookAt(new THREE.Vector3( 0, 0, 0 ))
> ```



### 渲染

> 渲染画布场景中的元素；
>
> ```javascript
> // Create Renderer
> const renderer = new THREE.WebGLRenderer({
>     // 渲染的 html 画布元素
>     canvas: canvas,
>     // 开启画布场景的背景为透明
>     alpha: true,
>     // 开启抗锯齿
>     antialias: true
> })
> 
> // renderer 简写形式
> // const renderer = new THREE.WebGLRenderer()
> // renderer.setSize(window.innerWidth, window.innerHeight)
> // document.body.appendChild(renderer.domElement)
> 
> // setting view port size
> renderer.setSize(size.width, size.height)
> 
> // 提供 场景 和 相机，让 threejs 开始渲染画面
> renderer.render(scene, camera);
> 
> // 适配渲染的像素密度，提高图形渲染质量
> renderer.setPixelRatio(
>     // window.devicePixelRatio 获取设备的像素密度
>     Math.min(window.devicePixelRatio, 2)
> )
> 
> // 激活渲染阴影
> renderer.shadowMap.enabled = true
> 
> // 设置场景的背景色
> renderer.setClearColor("#262837")
> ```



### 几何体

> 创建**几何体**，显示在场景中；
>
> ```javascript
> // geometry
> const cube = new THREE.BoxGeometry(1, 1, 1)
> // material
> const material = new THREE.MeshBasicMaterial({
>      color: "#ff6825",
>      // 仅显示模型的线条框架
>      wireframe: true
> })
> // mesh
> const mesh = new THREE.Mesh(cube, material)
> // add into scene
> scene.add(mesh)
> 
> // position
> mesh.position.set(2, 3, 4)
> 
> // rotation
> mesh.rotation.x = Math.PI / 2
> 
> //scale
> mesh.scale.set(0.8, 0.5, 0.5)
> ```



### 坐标系

> 在画面中显示空间坐标系；
>
> ```javascript
> const axesHelper = new THREE.AxesHelper() // 创建坐标系辅助
> scene.add(axesHelper) //// 加入到场景中
> // x(红)，y(绿)，z(蓝)
> ```





## 动画( Animation )

> canvas 渲染的场景是静态的；
>
> 通过调用`window.requestAnimationFrame()`方法；
>
> 每一帧都去自动更新场景的变化；
>
> 呈现出动画效果；
>
> [^Focus]:不建议用`setInterval()` 定时器实现动画效果；
>
> ```javascript
> // 定义一个保存鼠标位置比例的对象
> let variable = {
>      x: null,
>      y: null,
> }
> 
> // 监听鼠标移动的事件
> window.addEventListener("mousemove", (event) => {
>      variable.x = event.clientX / size.width - 0.5
>      variable.y = -(event.clientY / size.height - 0.5)
> })
> 
> // animation is here
> const frame = () => {
>      // 在帧动画中更新 mesh 的位置
> 
>      // Effect_1
>      mesh.position.x = variable.x * 5
>      mesh.position.y = variable.y * 5
> 
>      // Effect_2
>      camera.position.x = Math.sin(variable.x * Math.PI * 2) * 3
>      camera.position.z = Math.cos(variable.x * Math.PI * 2) * 3
>      camera.position.y = variable.y * 5
> 
>      // 让相机始终朝向 mesh
>      camera.lookAt(mesh.position)
>      // 每一帧都渲染一次
>      renderer.render(scene, camera)
>      // 每一帧都调用一次函数
>      window.requestAnimationFrame(frame)
> }
> frame()
> ```





## 相机( Camera )

> ThreeJS 提供的创建**相机**的**构造函数**；



#### PerspectiveCamera

> 透视相机；
>
> 模拟人眼看到的景象，最常用；
>
> ```javascript
> const camera = new THREE.PerspectiveCamera(
>   75,
>   size.width / size.height
> )
> ```



#### OrthographicCamera

> 正交相机；
>
> 物体图像在渲染后的大小与离摄像机的距离无关，保持不变；
>
> ```javascript
> const camera = new THREE.OrthographicCamera(
>     size.width / - 2,
>     size.width / 2,
>     size.height / 2,
>     size.height / - 2
> )
> ```





## 控制( Controls )

> ThreeJS 中提供的通过鼠标来控制**相机**或**物体**的构造函数；



### Orbit

> 轨道控制允许相机绕目标轨道运行；
>
> ```javascript
> // 引入模块文件
> import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
> 
> const control = new OrbitControls(camera, canvas) // 实例对象
> control.enableDamping = true // 开启鼠标的动量惯性
> controls.target.set(0, 0, 0) // 设置轨道控制中心
> 
> const frame = () => {
>   // 需要在帧动画中每帧自动更新控制器
>   control.update()
> }
> frame()
> ```
>
> 通过监听`control`发生变化的事件，不请求动画帧；
>
> ```js
> const control = new OrbitControls(camera, canvas)
> control.addEventListener('change', render) // 监听 change 事件，触发渲染函数
> 
> function render() {
>   renderer.render(scene, camera)
> }
> ```



### Arcball

> 通过虚拟轨迹球来控制摄像机；



### Drag

> 提供拖放物体的交互效果；



### FirstPerson

> 以第一视角控制摄像机；



### Fly

> 飞行模式控制相机，可以在 3D 空间中任意控制相机；



### PointerLock

> 实现基于指针锁 API，是第一人称 3D 游戏的完美选择；



### Trackball

> 类似与`Orbit`控制器，但不会一直朝向物体；



### Transform

> 开启类似三位软件中的交互；





## 交互( Interaction )

> 根据需求提高浏览体验；
>
> **全屏**和**更新画布尺寸**；



#### FullScreen

> 双击全屏事件；
>
> ```javascript
> window.addEventListener("dblclick", () => {
>   const fullScreenElement = document.fullscreenElement ||
>         document.webkitFullscreenElement;
>   if (!fullScreenElement) {
>     if (canvas.requestFullscreen) {
>       // <canvas> 元素请求全屏展示
>       canvas.requestFullscreen();
>     } else if (canvas.webkitRequestFullscreen) {
>       // 兼容 Safari 浏览器
>       canvas.webkitRequestFullscreen();
>     }
>   } else {
>     if (document.exitFullscreen) {
>       // 退出全屏状态
>       document.exitFullscreen();
>     } else if (document.webkitExitFullscreen) {
>       // 兼容 Safari 浏览器
>       document.webkitExitFullscreen();
>     }
>   }
> })
> ```



#### Resize

> 画布场景大小跟随浏览器视口大小而变化；
>
> ```javascript
> // 监听 window 的窗口调整事件
> window.addEventListener("resize", () => {
>      // 更新 size 的尺寸
>      size.width = window.innerWidth
>      size.height = window.innerHeight
>      // 更新 camera 的显示纵横比
>      camera.aspect = size.width / size.height
>      // 保持 scene 中的模型不会因视口变化而拉伸变形
>      camera.updateProjectionMatrix();
>      // 更新 renderer 要渲染的区域尺寸
>      renderer.setSize(size.width, size.height)
>      // renderer 渲染的像素密度
>      renderer.setPixelRatio(window.devicePixelRatio)
> })
> ```





## 几何体( Geometry )

> ThreeJS 提供了构造几何体的**构造函数**；
>
> 通过提供的参数创建模型；
>
> ```javascript
> // 立方体
> const geometry = new THREE.BoxBufferGeometry(1, 1, 1, 2, 2, 2)
> 
> // 圆形面
> const geometry = new THREE.CircleGeometry(5, 32)
> 
> // 锥体
> const geometry = new THREE.ConeGeometry(5, 20, 32)
> 
> // 圆柱
> const geometry = new THREE.CylinderGeometry(5, 5, 20, 32)
> 
> // 宝石
> const geometry = new THREE.IcosahedronGeometry(6, 0)
> 
> // 平面
> const geometry = new THREE.PlaneGeometry(10, 10)
> 
> // 球体
> const geometry = new THREE.SphereGeometry(15, 32, 16)
> 
> // 三角体
> const geometry = new THREE.TetrahedronGeometry(5, 0)
> 
> // 圆环
> const geometry = new THREE.TorusGeometry(10, 3, 32, 64)
> 
> // 圆环结
> const geometry = new THREE.TorusKnotGeometry(10, 3, 64, 32)
> ```





## 纹理( Texture )

> 在 ThreeJS 中，可以加载**图片**来作为材料纹理赋予给模型；
>
> ```javascript
> // geometry
> const geometry = new THREE.BoxBufferGeometry(2, 2, 2, 2, 2, 2)
> 
> // 实例化纹理加载对象
> const textureLoader = new THREE.TextureLoader()
> // 加载纹理图片文件
> const texture = textureLoader.load('/image/wood.jpg')
> 
> // 重复
> texture.repeat.x = 2
> texture.repeat.y = 2
> 
> // 偏移
> texture.offset.x = 0.5
> texture.offset.y = 0.5
> 
> // 旋转
> texture.rotation = Math.PI / 4
> // 设旋转的中心点
> texture.center.x = 0.5
> texture.center.y = 0.5
> 
> // 纹理过滤器
> // magFilter
> texture.magFilter = THREE.NearestFilter
> // minFilter
> // texture.minFilter = THREE.NearestFilter
> 
> // material
> const material = new THREE.MeshBasicMaterial({
>     // map 属性，在材质中添加纹理贴图
>     map: texture
> })
> // mesh
> const mesh = new THREE.Mesh(geometry, material)
> scene.add(mesh)
> ```





## 环境贴图( EnvironmentMap )

[Obtain HDR&Texture](https://polyhaven.com)

[HDRI Transform to CubeMap](https://matheowis.github.io/HDRI-to-CubeMap/)

> 加载**环境贴图**，让场景显示的更自然；
>
> ```javascript
> // 加载环境贴图
> const cubeTextureLoader = new THREE.CubeTextureLoader()
> const environmentMap = cubeTextureLoader.load([
>   "/image/environment/px.png",
>   "/image/environment/nx.png",
>   "/image/environment/py.png",
>   "/image/environment/ny.png",
>   "/image/environment/pz.png",
>   "/image/environment/nz.png",
> ])
> // 对应 renderer 渲染设置的 renderer.outputEncoding
> environmentMap.encoding = THREE.sRGBEncoding
> // 设置环境贴图为背景，此时可以看见环境贴图
> scene.background = environmentMap
> // 设置为场景的环境，场景中的所有物体都会有效果
> scene.environment = environmentMap
> 
> const cube = new THREE.Mesh(
>   new THREE.BoxBufferGeometry(1, 1, 1, 2, 2, 2),
>   new THREE.MeshStandardMaterial({
>     // 设置 cube 的环境贴图属性
>     child.material.envMap: environmentMap
>     // 环境贴图的照亮强度
>     child.material.envMapIntensity: 1
>   })
> )
> ```





## 材质( Material )

> 在 ThreeJS 中，定义了不同的**基本材料**；



### MeshBasicMaterial

> 简单绘制几何图形的材料；
>
> 该材质**无法投射阴影，不受`light`的影响**；
>
> ```javascript
> const material = new THREE.MeshBasicMaterial({
>   color: 0x96c24e,
>   // 设置材料的透明性
>   transparent: true,
>   // 设置材料的不透明度
>   opacity: 0.5,
>   // 设置材料分段面的扁平性
>   flatShading: false
> })
> ```



### MeshStandardMaterial

> 标准的物理基础材料，基于物理的渲染`PBR`；
>
> 接收光和阴影；
>
> ```javascript
> const material = new THREE.MeshStandardMaterial()
> ```

[^Hint]:在使用该材质时，建议总是指定一个环境贴图；



### MeshPhysicalMaterial

> `Standard`材料的扩展，提供更高级的基于物理的渲染属性；
>
> 提供了几种独有的属性实现一些效果；
>
> ```javascript
> const material = new THREE.MeshPhysicalMaterial({
>   // 透明涂层的强度
>   clearcoat: 0.6
> })
> ```

[^Hint]:在使用该材质时，建议总是指定一个环境贴图；



### MeshNormalMaterial

> 普通材质，将法向量映射为 RGB 颜色的材质；
>
> **本身自带颜色**；
>
> ```javascript
> const material = new THREE.MeshNormalMaterial()
> ```



### MeshPhongMaterial

> 具有高光的光亮表面的材料；
>
> 可以模拟具有镜面高光的闪亮表面；
>
> ```javascript
> const material = new THREE.MeshPhongMaterial({
>   color: '#0042aa', // 材料颜色
>   emissive: '#001e57', // 材料的发射色（阴暗面）
>   specular: '#53d5fd', // 材质的镜面颜色，反射色（高光面）
>   shininess: 100, // 镜面反射强度
> })
> ```



### MeshLambertMaterial

> 镜面反射不强的材质;
>
> ```javascript
> const material = new THREE.MeshPhongMaterial({
>   color: 0xffffff, // 材料颜色，十六进制
> })
> ```



### MeshToonMaterial

> 实现卡通阴影材质；
>
> ```javascript
> const material = new THREE.MeshToonMaterial({
>   color: '#0061ff'
> })
> ```



### MeshMatcapMaterial

> 该材质不响应灯光；
>
> 由`matcap`图像文件编码烘焙照明；
>
> 投射一个阴影到一个接收阴影的物体上，产生明暗变化；
>
> **单材料本身不投射和接收阴影**；
>
> ```javascript
> // 需要 matcap 图像文件产生效果
> const material = new THREE.MeshMatcapMaterial()



### MeshDepthMaterial

> 按深度绘制几何图形的材料；
>
> 深度是模型离相机的远近，显示白色表示较近，显示黑色较远；
>
> ```javascript
> const material = new THREE.MeshDepthMaterial()
> ```





## 3D Text

> ThreeJS 提供了可以在场景中添加 3D 文字的方法；
>
> 加载`json`格式的字体文件；



#### Modules

> 需要另外引入加载模块文件；
>
> ```javascript
> // 字体文件加载器
> import { FontLoader } from "three/examples/jsm/loaders/FontLoader"
> // 文字模型构造器
> import { TextGeometry } from "three/examples/jsm/geometries/TextGeometry"
> ```



#### Use

> 使用加载器和构造器；
>
> ```javascript
> // 实例化字体文件加载对象
> const fontLoader = new FontLoader()
> const font = fontLoader.load(
>   // 字体文件的加载路径，json 格式
>   "/font/optimer_regular.typeface.json",
>   (font) => {
>     // 构造字体模型
>     const textGeometry = new TextGeometry(
>       // 显示的文字信息
>       "Hello",
>       {
>         font: font,
>         size: 2,
>         height: 0.2,
>         curveSegments: 12,
>         bevelEnabled: true,
>         bevelThickness: 0.03,
>         bevelSize: 0.02,
>         bevelOffset: 0,
>         bevelSegments: 5
>       }
>     )
>     
>     // 使坐标中心在文字的中间
>     textGeometry.computeBoundingBox();
>     textGeometry.translate(
>       -textGeometry.boundingBox.max.x / 2,
>       -textGeometry.boundingBox.max.y / 2,
>       -textGeometry.boundingBox.max.z / 2
>     )
>     // 更简单的写法
>     textGeometry.center()
>     
>     // 字体的材质
>     const textMaterial = new THREE.MeshNormalMaterial()
>     const textMesh = new THREE.Mesh(textGeometry, textMaterial)
>     scene.add(textMesh)
>   }
> )
> ```





## 灯光( Lights )

> ThreeJS 提供的在场景中添加**灯光**的构造函数；

[^Hint]:展现性能成本按顺序从低到高；



#### AmbientLight

> **环境光照**；
>
> 在所有方向照亮场景的物体，没有方向，不能用来投射阴影；
>
> ```javascript
> // AmbientLight(color, intensity)
> const ambientLight = new THREE.AmbientLight('#ffffff', 0.5)
> scene.add(ambientLight)
> ```



#### HemisphereLight

> **半球光照**；
>
> 直接位于场景上方的光源，颜色从天空颜色到地面颜色；
>
> 不能用来投射阴影；
>
> ```javascript
> const hemisphereLight = new THREE.HemisphereLight(
>   // 天空的颜色
>   "#ff0000",
>   // 地面的颜色
>   "#0000ff"
>   // 光照强度
>   0.3
> )
> scene.add(hemisphereLight)



#### PointLight

> **点光源**，从一个点向各个方向发光，类似一个灯泡；
>
> ```javascript
> const pointLight = new THREE.PointLight(0xffffff, 0.5)
> // 灯光的位置
> pointLight.position.set(2, 3, 4);
> // 设置灯光的衰退
> pointLight.decay = 0.9
> // 灯光的照射距离，默认是没有距离，不会减弱
> pointLight.distance = 10
> scene.add(pointLight)
> ```



#### DirectionalLight

> **方向光**，向特定方向发射光，发出的光线都是平行的；
>
> 常见用例是模拟日光；
>
> ```javascript
> const directionLight = new THREE.DirectionalLight('orange', 0.5)
> directionLight.position.set(3,3,0)
> // 让光源投射阴影
> directionLight.castShadow = true
> scene.add(directionLight)
> ```



#### SpotLight

> **聚光灯**，光线从一个方向上的一个点发射出来，沿着一个锥形区域；
>
> 离光越远，锥的尺寸就越大；
>
> ```javascript
> const spotLight = new THREE.SpotLight(
>  '#78ffd6', // 光照颜色
>  0.6, // 光照强度
>  10, // 光照距离
>  Math.PI/10, // 光照角度的范围，最大不超过 90 度
>  0.25, // 光在锥形区域的衰退百分比
>  1 // 光的衰退值
> )
> spotLight.position.set(0,3,3) // 聚光灯位置
> spotLight.angle = Math.PI / 5
> spotLight.penumbra = 0.2 // 边缘柔化过渡
> scene.add(spotLight)
> ```



#### RectAreaLight

> **区域光**，在一个矩形平面上均匀地发出光，可以模拟明亮窗户的光源；
>
> 仅支持`Standard`和`Physical`材质；
>
> ```javascript
> const rectAreaLight = new THREE.RectAreaLight(
>   // 光照颜色
>   '#4e00ff',
>   // 光照强度
>   0.6,
>   // 光照区域的宽度，默认是 10
>   3,
>   // 光照区域的高度，默认是 10
>   2
> )
> rectAreaLight.position.set(-1,0,3)
> rectAreaLight.lookAt(new THREE.Vector3())
> scene.add(rectAreaLight)
> ```





## 阴影( Shadow )

> 阴影效果



### Render

> 要激活渲染器去渲染阴影，才会显示阴影；
>
> ```javascript
> renderer.shadowMap.enabled = true
> ```



### Cast

> 投射阴影，由光源和物体组成；
>
> ```javascript
> // 让 pointLight 光源去照射，投射阴影
> pointLight.castShadow = true
> // 设置阴影的尺寸，数值越大，阴影质量越高
> pointLight.shadow.mapSize.set(1024, 1024)
> // 设置阴影边缘模糊的半径
> pointLight.shadow.radius = 5
> // 解决模型同时有 投射和接受阴影时 有锯齿的问题
> pointLight.shadow.normalBias = 0.1
> 
> // sphere
> const sphere = new THREE.Mesh(
>   new THREE.SphereBufferGeometry(2, 32, 32),
>   new THREE.MeshStandardMaterial({ color: '#f6f6f6' })
> );
> // 让 sphere 可以被光源投射阴影
> sphere.castShadow = true
> scene.add(sphere)
> ```



### Receive

> 要有物体去接收显示阴影；
>
> ```javascript
> // plane
> const plane = new THREE.Mesh(
>     new THREE.PlaneBufferGeometry(20, 20, 16, 16),
>     new THREE.MeshStandardMaterial({ color: '#ffffff' })
> );
> plane.rotation.x = - Math.PI / 2
> // 让 plane 接收阴影
> plane.receiveShadow = true;
> scene.add(plane);
> ```





## 时钟( Clock )

> ThreeJs 中的时钟类；
>
> ```js
> const clock = new THREE.Clock()
> const render = (): void => {
>   if (scene && camera && renderer) {
>     const delta = clock.getDelta() // 获取每一帧的时间间隔
>     renderer.render(scene, camera)
>   }
>   window.requestAnimationFrame(render)
> }
> ```





## 粒子( Particles )

> 在场景中渲染**粒子**；



#### Geometry Vertex

> 通过提供一个几何体，在每个顶点上生成一个粒子；
>
> ```javascript
> const particles = new THREE.Points(
>   // 提供一个几何体模型，在其定点上生成粒子
>   new THREE.SphereBufferGeometry(4, 32, 32),
>   // 粒子的材质
>   new THREE.PointsMaterial({
>     // 粒子的尺寸
>     size: 0.2,
>     // 开启粒子尺寸的根据视觉来衰减
>     sizeAttenuation: true,
>   })
> )
> scene.add(particles)
> ```



#### setAttribute

> 通过随机生成点坐标来生成粒子；
>
> ```javascript
> // 粒子的基本模型
> const particleGeometry = new THREE.BufferGeometry()
> // 粒子的数量
> const amount = 20000
> // 粒子坐标的数组，每 3 个元素构成一个空间坐标
> const positions = new Float32Array(amount * 3)
> // 粒子颜色的数组，每 3 个元素构成一个 RGB 色值
> const colors = new Float32Array(amount * 3)
> // for 循环，生成每个数组元素的值
> for (let i = 0; i < amount * 3; i++) {
>   positions[i] = (Math.random() - 0.5) * 100
>   colors[i] = Math.random()
> }
> 
> particleGeometry.setAttribute(
>   // 给粒子基本模型设置 position 属性
>   "position",
>   // 读取坐标数组所有的值
>   new THREE.BufferAttribute(positions, 3)
> )
> particleGeometry.setAttribute(
>   // 给粒子基本模型设置 color 属性
>   "color",
>   // 读取颜色数组所有的值
>   new THREE.BufferAttribute(colors, 3)
> )
> 
> const particles = new THREE.Points(
>   // 粒子的模型
>   particleGeometry,
>   // 粒子材质
>   new THREE.PointsMaterial({
>     size: 0.4,
>     sizeAttenuation: true,
>     // 粒子的颜色
>     color: "#2f73af",
>     // 开启透明模式
>     transparent: true,
>     // 关闭深度测试，会让粒子穿过其他物体
>     depthTest: false,
>     // resolve particle across others substance question
>     depthWrite: false, 
>     // 开启颜色之间允许添加混合
>     blending: THREE.AdditiveBlending,
>     // 开启顶点颜色，让随机的颜色生效
>     vertexColors: true
>   })
> )
> scene.add(particles)
> ```



#### Case

> ```javascript
> // 保存粒子参数的对象
> const parameter = {
>     amount: 5000,
>     size: 0.02,
>     radius: 5,
>     branch: 3,
>     spin: 1,
>     random: 1,
>     power: 3,
> };
> 
> // define variable
> let geometry = null;
> let points = null;
> let material = null;
> 
> // Generater function
> function generater() {
>     // 销毁旧的粒子，清除缓存，提高性能
>     if (points !== null) {
>        geometry.dispose()
>        material.dispose()
>        scene.remove(points)
>     }
> 
>     geometry = new BufferGeometry()
>     material = new THREE.PointsMaterial({
>        size: parameter.size,
>        depthWrite: false,
>        blending: THREE.AdditiveBlending,
>        sizeAttenuation: true,
>        vertexColors: true,
>     })
>     const positions = new Float32Array(parameter.amount * 3)
>     const colors = new Float32Array(parameter.amount * 3)
>     const insideColor = new THREE.Color('#ff6030')
>     const outsideColor = new THREE.Color('#1b3984')
> 
>     for (let i = 0; i < parameter.amount * 3; i++) {
>        const i3 = i * 3
>        const randomRadius = parameter.radius * Math.random()
>        const branchAngle =
>              ((i % parameter.branch) / parameter.branch) * (Math.PI * 2);
>        const spinSelfAngle = randomRadius * parameter.spin
> 
>        const randomX =
>              Math.pow(Math.random(), parameter.power) * (Math.random() < 0.5 ? 1 : -1)
>        const randomY =
>              Math.pow(Math.random(), parameter.power) * (Math.random() < 0.5 ? 1 : -1)
>        const randomZ =
>              Math.pow(Math.random(), parameter.power) * (Math.random() < 0.5 ? 1 : -1)
> 
>        // x
>        positions[i3] =
>          Math.cos(branchAngle + spinSelfAngle) * randomRadius + randomX; 
>        // y
>        positions[i3 + 1] = randomY
>        // z
>        positions[i3 + 2] =
>          Math.sin(branchAngle + spinSelfAngle) * randomRadius + randomZ
> 
>        // color
>        const mixColor = insideColor.clone()
>        // 混合粒子内部与外围的颜色
>        mixColor.lerp(outsideColor, randomRadius / parameter.radius)
>        colors[i3] = mixColor.r // R
>        colors[i3 + 1] = mixColor.g // G
>        colors[i3 + 2] = mixColor.b // B
>     }
>     geometry.setAttribute("position", new THREE.BufferAttribute(positions, 3))
>     geometry.setAttribute("color", new THREE.BufferAttribute(colors, 3))
>     points = new THREE.Points(geometry, material)
>     scene.add(points)
> }
> generater()
> ```





## 射线( Raycaster )

> 射线投射；
>
> ```javascript
> // 物体
> const geometry_1 = new THREE.Mesh(
>      new THREE.SphereBufferGeometry(1, 16, 16),
>      new THREE.MeshBasicMaterial({ color: "orange" })
> )
> const geometry_2 = new THREE.Mesh(
>      new THREE.SphereBufferGeometry(1, 16, 16),
>      new THREE.MeshBasicMaterial({ color: "orange" })
> )
> geometry_2.position.x = -4;
> const geometry_3 = new THREE.Mesh(
>      new THREE.SphereBufferGeometry(1, 16, 16),
>      new THREE.MeshBasicMaterial({ color: "orange" })
> )
> geometry_3.position.x = 4
> scene.add(geometry_1, geometry_2, geometry_3)
> 
> // Raycaster
> const rayCaster = new THREE.Raycaster()
> // 射线的起点
> const origin = new THREE.Vector3(-5, 0, 0)
> // 射线的方向和距离
> const direction = new THREE.Vector3(16, 0, 0)
> direction.normalize()
> rayCaster.set(origin, direction)
> 
> const series = [geometry_1, geometry_2, geometry_3]
> 
> // 监听鼠标移动的事件
> // 2 维向量
> const mouse = new THREE.Vector2()
> window.addEventListener("mousemove", (event) => {
>      mouse.x = (event.clientX / window.innerWidth) * 2 - 1
>      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1
> })
> 
> // frame animation
> // thresJS 中的时钟构造函数
> const clock = new THREE.Clock()
> const frame = () => {
>      // 记录调用函数开始流逝的时间
>      const elapseTime = clock.getElapsedTime()
>      // 物体在 y 轴上做往复运动
>      geometry_1.position.y = Math.sin(elapseTime * 1.2) * 1.5
>      geometry_2.position.y = Math.sin(elapseTime * 0.8) * 1.5
>      geometry_3.position.y = Math.sin(elapseTime * 0.4) * 1.5
> 
>      // 根据鼠标的位置和相机更新 Raycaster
>      rayCaster.setFromCamera(mouse, camera)
> 
>      // 计算与射线位置相交的物体，在添加到数组中
>      const intersects = rayCaster.intersectObjects(series)
>      // 遍历数组
>      for (const intersect of intersects) {
>        // 使位置与射线相交的物体的颜色产生变化
>        intersect.object.material.color.set(0xffff00)
>      }
> 
>      // 渲染画面
>      renderer.render(scene, camera)
>      window.requestAnimationFrame(frame)
> }
> frame()
> ```





## 物理( Physics )

> 在 ThreeJS 中出现自然的物理效果，掉落，碰撞；
>
> 需要用到其他[^Physics Libraries]；

[^Physics Libraries]:`Ammo.js`,`Cannon.js`,`Oimo.js`；



#### Cannon.js

> 一个在网页中实现物理效果的 JavaScript 库；



###### Install

> ```shell
> npm install cannon --save
> ```



###### Import

> ```javascript
> import CANNON from "cannon"
> ```



###### Use

> ```javascript
> // 创建物理世界
> const world = new CANNON.World()
> // 提升性能的配置
> world.broadphase = new CANNON.SAPBroadphase(world)
> world.allowSleep = true
> // 在物理世界中添加重力， y 轴方向
> world.gravity.set(0, -9.8, 0)
> 
> // 给物体小球加上撞击的音效
> const hitSound = new Audio("/sound/sound.mp3")
> const playSound = (collision) => {
>   // collision 是 sphereBody 碰撞触发的函数传过来的参数
>   const impactStrength = collision.contact.getImpactVelocityAlongNormal()
>   if (impactStrength > 2.5) {
>     // 每次碰撞都从头播放音效
>     hitSound.currentTime = 0
>     hitSound.play()
>     // 更据冲击强度影响音量
>     hitSound.volume = impactStrength > 10 ? 1 : impactStrength * 0.1
>   }
> }
> 
> // 每次点击 window 都生成一个物体小球，从空中落下
> window.addEventListener("click", () => {
>   // 调用 createSphere 方法创建小球
>   createSphere(Math.random() * (1 - 0.2) + 0.2, {
>     x: (Math.random() - 0.5) * 8,
>     y: 8,
>     z: (Math.random() - 0.5) * 8,
>   })
> })
> 
> // 添加物理世界中的碰撞材料
> const defaultMaterial = new CANNON.Material("default")
> const defaultContactMaterial = new CANNON.ContactMaterial(
>   defaultMaterial,
>   defaultMaterial,
>   {
>     friction: 0.1, // 摩擦力
>     restitution: 0.6, // 弹力
>   }
> )
> 
> // 添加物理世界的地面，，默认位置是 position.y = 0
> const floorShape = new CANNON.Plane()
> const floorBody = new CANNON.Body({
>   // 质量
>   mass: 0,
>   // 材料
>   material: defaultMaterial,
> })
> floorBody.addShape(floorShape)
> // 纠正方向，因为 three 的 plane 是经过旋转的
> floorBody.quaternion.setFromAxisAngle(
>   new CANNON.Vec3(1, 0, 0),
>   -Math.PI / 2
> )
> // 在物理世界中加入地面
> world.addBody(floorBody)
> // 定义物理世界中的碰撞关系材料
> world.addContactMaterial(defaultContactMaterial)
> 
> // 真实小球的材质
> const sphereMaterial = new THREE.MeshStandardMaterial({
>   roughness: 0.3,
>   metalness: 0.1,
>   color: "#ffffff",
> })
> // 真实小球的几何模型
> const sphereGeometry = new THREE.SphereBufferGeometry(1, 32, 32)
> // 创建一个空数组，用来在帧动画中更新位置
> const series = []
> 
> // 创建小球的函数方法
> function createSphere(radius, position) {
>   // 创建物理世界中的小球
>   const sphereBody = new CANNON.Body({
>     // 质量
>     mass: 1,
>     // 形状
>     shape: new CANNON.Sphere(radius),
>     // 材料
>     material: defaultMaterial,
>   })
>   // 设置物理世界的小球的初始位置
>   sphereBody.position.copy(position)
>   // 物理世界小球碰撞的事件
>   sphereBody.addEventListener(
>     // 世界类型
>     "collide",
>     // 要触发的方法函数
>     playSound
>   )
> 
>   // 给物理小球施加一个 x 轴向的力，让球产生偏移，只施加一次
>   sphereBody.applyLocalForce(
>     // 力量的矢量
>     new CANNON.Vec3(400, 0, 0),
>     // 施加力量给物体的点的位置
>     new CANNON.Vec3(0, 0, 0)
>   )
>   // 在物理世界中添加一个全局的力，模拟风的作用，只施加一次
>   sphereBody.applyForce(
>     // 力量的矢量
>     new CANNON.Vec3(-100, 0, 0),
>     // 在物理世界的哪个位置施加力量
>     sphereBody.position
>   )
>   // 在物理世界中加入小球
>   world.addBody(sphereBody)
>   
>   // 创建真实显示的小球
>   const sphere = new THREE.Mesh(sphereGeometry, sphereMaterial)
>   // 复制函数第二个参数传递的 3 维向量
>   sphere.position.copy(position)
>   // 真实小球的随机尺寸
>   sphere.scale.set(radius, radius, radius)
>   // 开启真实小球投射阴影
>   sphere.castShadow = true
>   // 加入场景
>   scene.add(sphere)
>   // 将每次调用方法产生的真实小球、物理小球对象都放入空数组，记录空间位置
>   series.push({
>     sphere,
>     sphereBody,
>   })
> }
> 
> // ThreeJS 中的时钟
> const clock = new THREE.Clock()
> let oldTime = 0
> 
> // 帧动画
> const frame = () => {
>   // 流逝的时间
>   let elapseTime = clock.getElapsedTime()
>   // augmentTime 是每一帧流逝的时间
>   let augmentTime = elapseTime - oldTime
>   oldTime = elapseTime
>   
>   // 每一帧都更新物理世界
>   // 规定物理世界每一步的时间大小
>   // 后面两个参数为可选；
>   world.step(1 / 60, augmentTime, 3)
>   // 球体用 position ，方块要用 quaternion，因为方块不是滚动
>   // 遍历数组
>   for (let item of series) {
>     // 每一帧都将物理小球的位置复制给真实小球
>     item.sphere.position.copy(item.sphereBody.position)
>   }
> 
>   // 写在帧动画里，此时每一帧都会有一个负 x 轴向的世界力施加在物体上
>   sphereBody.applyForce(new CANNON.Vec3(-1, 0, 0), sphereBody.position)
> 
>   // 渲染画面
>   renderer.render(scene, camera)
>   window.requestAnimationFrame(frame)
> }
> frame()
> ```





## 模型( Import Model )

> 在 ThreeJS 中创建复杂模型很麻烦，在**三维软件**中创建好模型，导入 ThreeJS ；
>
> **需要功能文件中`Loader`加载模块去加载模型**；



#### GLTF / GLB

> 加载`GLTF / GLB`文件格式的三维模型；



###### Import

> 引入`gltf`格式外部模型加载器；
>
> ```javascript
> // 引入外部模型加载器
> import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader"
> // 引入模型压缩器模块，建议在模型文件较大时使用
> import { DRACOLoader } from "three/examples/jsm/loaders/DRACOLoader"
> ```



###### Use

> ```javascript
> // 使用压缩器
> const dracoLoader = new DRACOLoader()
> // 配置 dracoLoader 的压缩解析文件，建议复制文件夹到项目目录下
> dracoLoader.setDecoderConfig("/static/draco/")
> // 使用模型加载器
> const gltfLoader = new GLTFLoader()
> // 告诉 gltfLoader 使用模型文件压缩器
> gltfLoader.setDRACOLoader(dracoLoader)
> // 加载模型文件
> /* -- 如果使用了文件压缩器，加载的模型文件就是经过压缩的 -- */
> gltfLoader.load("/static/cube.glb", (gltf) => {
>     // 获取模型文件中的场景
>     const cubeScene = gltf.scene
>     cubeScene.position.set(0, 3, -30)
>     cubeScene.scale.set(0.3, 0.3, 0.3)
>     scene.add(cubeScene)
>     // 遍历 cubeScene 场景中的所有子元素
>     cubeScene.traverse((child) => {
>        // 判断子元素是否是物体模型
>        if (
>          child instanceof THREE.Mesh &&
>          child.material instanceof THREE.MeshStandardMaterial
>        ) {
>          child.frustumCulled = false
>          child.castShadow = true
>        }
>     })
> })
> ```



#### FBX

> 加载`FBX`文件格式的三维模型；



###### Import

> ```javascript
> // 引入 fbx 格式外部模型加载器
> import { FBXLoader } from "three/examples/jsm/loaders/FBXLoader"
> ```



###### Use

> ```javascript
> const fbxLoader = new FBXLoader()
> fbxLoader.load("/static/house.fbx", (fbx) => {
>   const house = fbx.children[4]
>   house.scale.set(0.005, 0.005, 0.005)
>   scene.add(house)
> })
> ```





## 加载管理( LoadingManager )

> ThreeJS 中的**加载管理器**，一般用来**显示加载进度**；
>
> ```javascript
> // 实例 manager 实例对象
> const manager = new THREE.LoadingManager()
> 
> // 开始加载
> manager.onStart = function (url, itemsLoaded, itemsTotal) {
>      console.log(
>        "Started loading file: " +
>        url +
>        ".\nLoaded " +
>        itemsLoaded +
>        " of " +
>        itemsTotal +
>        " files."
>      )
> }
> 
> // 加载中
> manager.onProgress = function (url, itemsLoaded, itemsTotal) {
>      let progress = Math.floor((itemsLoaded / itemsTotal) * 100)
>      console.log(progress)
> }
> 
> // 加载完成
> manager.onLoad = function () {
>      console.log("Loading complete!")
> }
> 
> // 加载错误
> manager.onError = function (url) {
>      console.log("There was an error loading " + url)
> }
> 
> // 要进行监测管理的加载器，在构造函数中传入 manager
> 
> // 加载 gltf 格式模型文件
> const gltfLoader = new GLTFLoader(manager)
> 
> // 加载环境贴图
> const cubeTextureLoader = new THREE.CubeTextureLoader(manager)
> ```





## 逼真渲染( RealisticRender )

> 在 ThreeJS 配置**逼真的物理渲染**，让场景显示更近现实；
>
> ```javascript
> // 配置渲染器的物理模式渲染，会让场景变暗，建议添加环境贴图或加强灯光强度
> renderer.physicallyCorrectLights = true
> renderer.outputEncoding = THREE.sRGBEncoding
> renderer.toneMapping = THREE.ACESFilmicToneMapping





## 着色器( Shaders )

> 可以在 ThreeJS 中实现十分丰富的图像显示；
>
> 用`GLSL`编写的程序，在`GPU`上运行，能够提供`materials`之外的效果；



#### Webpack

> 在`webpack`中打包着色器的`GLSL`文件；
>
> ```javascript
> module.exports = {
>     module: {
>        rules: [
>          // 处理 shader 着色器文件；
>          {
>            test: /\.(glsl|vs|fs|vert|frag)$/,
>            exclude: /node_modules/,
>            use: ["raw-loader"],
>          },
>        ]
>     }
> }
> ```



#### Vertex / Fragment

> 着色器材质需要指定两种不同类型的 shaders ；
>
> 顶点着色器`Vertex`和片段着色器`fragment `；
>
> ```javascript
> // 引入着色器文件
> import vertexShader from "./shader/flag/vertex.glsl"
> import fragmentShader from "./shader/flag/fragment.glsl"
> // 创建着色器材质
> const cubeMaterial = new THREE.RawShaderMaterial({
>      // 顶点着色器
>      vertexShader: vertexShader,
>      // 片段着色器
>      fragmentShader: fragmentShader,
>      // 着色器中的 uniform 变量
>      uniforms: {
>        // 自定义一个 uniform 的属性，可以在 vertex.glsl 文件中使用这个属性
>        uFrequency: { value: new THREE.Vector2(0.8, 0.8) },
>        // 在 fragment.glsl 文件中使用这个属性
>        uColor: { value: new THREE.Color("orange") },
>        // 在帧动画 frame() 中赋值
>        uTime: { value: 0 },
>      },
>    })
>    
> const clock = new THREE.Clock()
> const frame = () => {
>   let elapseTime = clock.getElapsedTime();
> 
>      // 值将在 vertex.glsl 文件中引入
>   cubeMaterial.uniforms.uTime.value = elapseTime;
>    
>      controls.update()
>   renderer.render(scene, camera)
>      window.requestAnimationFrame(frame)
>    }
>    frame()
> ```



###### Vertex

> 顶点着色器`Vertex`首先运行；
>
> 接收`attribute`变量， 计算/操纵每个单独顶点的位置；
>
> 可以将其他数据`varying`传递给片段着色器`Fragment`；
>
> ```glsl
> uniform mat4 projectionMatrix;
> uniform mat4 viewMatrix;
> uniform mat4 modelMatrix;
> 
> // 在 app.js 中自定义的属性
> uniform vec2 uFrequency;
> uniform float uTime;
> 
> attribute vec3 position;// 获取位置坐标
> attribute vec2 uv;// 获取 uv 
> 
> // 发送给 fragment.glsl 文件使用
> varying vec2 vUv;
> varying float vElevation;
> 
> void main()
> {
>      // gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);
>      // 详细写法
>      vec4 modelPosition = modelMatrix * vec4(position, 1.0);
>      float elevation = sin(modelPosition.x * uFrequency.x - uTime) * 0.5;
>      elevation += sin(modelPosition.y * uFrequency.y - uTime) * 0.5;;
>        modelPosition.z += elevation;
>      vec4 viewPosition = viewMatrix * modelPosition;
>      vec4 projectionPosition = projectionMatrix * viewPosition;
>      gl_Position = projectionPosition;
> 
>      // 给 vUv 赋值
>      vUv = uv;
>      // 给 vElevation 赋值
>      vElevation = elevation;
> }
> 
> // not have console.log()
> 
> // /* 声明变量 */
> 
> // // 小数
> // float a = 1.314;// true
> // // float b = 5;// false
> // float b = 5.20;
> // float c = a * b;
> 
> // // 整数
> // int d = 10;
> // int e = 5;
> // int f = d / e;
> 
> // // 转换类型
> // int intB = int(b);
> // // 布尔值
> // bool foo = true;
> // bool bar = false;
> 
> // /* 向量 */
> 
> // // vec2 向量
> // vec2 line = vec2(1.0, 2.0);
> // // 改变值；
> // line.x = 2.0;
> // line.y = 1.0;
> 
> // // vec3 向量
> // vec3 spaceLine = vec3(2.0, 1.0, 3.0);
> // // 改变值
> // spaceLine.x = 1.0;
> // spaceLine.y = 2.0;
> // spaceLine.z = 3.0;
> 
> // // 根据 vec2 向量创建 vec3 向量；
> // vec3 extend = vec3(line, 2.0);
> 
> // // vec4 向量；
> // vec4 thing = vec4(spaceLine, 2.0);
> ```



###### Fragment

> 片段着色器`Fragment`后运行；
>
> 它设置渲染到屏幕的每个单独的`片段`（像素）的**颜色**；
>
> ```glsl
> precision mediump float;
> 
> // 在 app.js 文件自定义的 uniform 属性
> uniform vec3 uColor;
> // 纹理
> uniform sampler2D uTexture;
> 
> // 接受 vertex.glsl 文件发送来的 vUv vElevation
> varying vec2 vUv;
> varying float vElevation;
> 
> void main()
> {
>     vec4 textureColor = texture2D(uTexture, vUv);
>     textureColor.rgb *= vElevation * 0.3 + 0.7;
>     gl_FragColor = textureColor;
>     // 设置片段颜色
>     // vec4(Red, Green, Blue, Alpha);
>     // gl_FragColor = vec4(1.0, 0.5, 0.0, 1.0);
>     // gl_FragColor = vec4(uColor, 1.0);
> }
> ```



#### Variable

> 着色器中存储数据的**变量**；



###### uniform

> 所有**顶点**都具有相同值的变量； 
>
> 比如灯光，雾，和阴影贴图就是被储存在`uniform`中的数据；
>
> `uniform`可以通过顶点着色器和片段着色器来访问；
>
> 可以在外部的**着色器材料中定义`uniforms`属性对象**；
>
> 其中的属性可以在**着色器文件**中通过`uniform`变量接收；
>
> ThreeJS 文件：
>
> ```javascript
> // 创建着色器材质
> const cubeMaterial = new THREE.RawShaderMaterial({
>     vertexShader: vertexShader,
>     fragmentShader: fragmentShader,
>     // 着色器中的 uniform 变量
>     uniforms: {
>        // 自定义一个 uniform 的属性，可以在 vertex.glsl 文件中使用这个属性
>        uFrequency: { value: new THREE.Vector2(0.8, 0.8) },
>        // 在 fragment.glsl 文件中使用这个属性
>        uColor: { value: new THREE.Color("orange") },
>     },
> })
> ```
>
> GLSL 文件：
>
> ```glsl
> // 该文件类型有严格的数据类型限制
> uniform mat4 projectionMatrix;
> uniform mat4 viewMatrix;
> uniform mat4 modelMatrix;
> 
> // 在 app.js 中自定义的属性
> uniform vec2 uFrequency;
> uniform float uTime;
> ```



###### attribute

> 与每个顶点关联的变量；
>
> 例如顶点位置，法线和顶点颜色都是存储在`attribute`中的数据；
>
> `attribute` 只可以在顶点着色器`Vertex`中访问；
>
> ```glsl
> // 获取位置坐标
> attribute vec3 position;
> // 获取 uv 坐标信息
> attribute vec2 uv;
> ```



###### varying

> 是从顶点着色器`Vertex`传递到片段着色器`Fragment`的变量；
>
> ```glsl
> // 在顶点着色器中获取 uv 坐标信息
> attribute vec2 uv;
> 
> // 发送给 fragment.glsl 文件使用
> varying vec2 vUv;
> 
> void main()
> {
>     // 给 vUv 赋值
>     vUv = uv;
> }
> ```



#### Pattern

> 利用**片段着色器**实现丰富的颜色变化；
>
> ```glsl
> precision mediump float;
> 
> // 在 app.js 文件自定义的 uniform 属性
> uniform vec3 uColor;
> 
> // 从顶点着色器传递
> varying vec2 vUv;
> 
> 
> void main()
> {
>     // 设置片段颜色
>     // vec4(Red, Green, Blue, Alpha);
> 
>     // // pattern_1
>     // gl_FragColor = vec4(vUv, 1.0, 1.0);
> 
>     // pattern_2
>     gl_FragColor = vec4(vUv.y, vUv.x, 0.5, 1.0);
> 
>     // pattern_3
>     // gl_FragColor = vec4(vUv.y, vUv.y, vUv.y, 1.0);
> 
>     // pattern_4
>     // gl_FragColor = vec4(vUv.y * 5.0, vUv.y * 5.0, vUv.y * 5.0, 1.0);
> 
>     // pattern_5
>     // float strength = mod(vUv.y * 5.0, 1.0);// 取模运算
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_6
>     // float strength = mod(vUv.y * 5.0, 1.0);
>     // if(strength < 0.5){
>     //   strength = 0.0;
>     // } else {
>     //   strength = 1.0;
>     // }
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_7
>     // float barX = step(0.4, mod(vUv.x * 10.0, 1.0));
>     // barX *= step(0.8, mod(vUv.y * 10.0 + 0.2, 1.0));
> 
>     // float barY = step(0.8, mod(vUv.x * 10.0 + 0.2, 1.0));
>     // barY *= step(0.4, mod(vUv.y * 10.0, 1.0));
> 
>     // float strength = barX + barY;
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_8
>     // float strength = abs(vUv.x - 0.5);
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_9
>     // float strength = min(abs(vUv.x - 0.5), abs(vUv.y - 0.5));// 取最小值
>     // float strength = max(abs(vUv.x - 0.5), abs(vUv.y - 0.5));// 取最大值
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_10
>     // float strength = step(0.2, max(abs(vUv.x - 0.5), abs(vUv.y - 0.5)));
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_11
>     // float strength = floor(vUv.x * 10.0) / 10.0;// 向下取整
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_12
>     // float strength = floor(vUv.x * 10.0) / 10.0;
>     // strength *= floor(vUv.y * 10.0) / 10.0;
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_13
>     // float strength = length(vUv);
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // pattern_14
>     // float strength = 0.02 / distance(vUv, vec2(0.5));
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> 
>     // // pattern_15
>     // float strength = abs(distance(vUv, vec2(0.5)) - 0.25);
>     // gl_FragColor = vec4(strength, strength, strength, 1.0);
> }
> ```





## 效果处理( EffectComposer )

> ThreeJS 中的效果处理模块，实现不同风格的画面；



#### Config

> 引入效果处理模块；
>
> ```javascript
> // 加载效果处理模块
> import { EffectComposer } from 'three/examples/jsm/postprocessing/EffectComposer'
> // 实例化对象
> const effectComposer = new EffectComposer(renderer)
> // 与 renderer 相同的配置项
> // 将代替原本的 renderer 去渲染画面
> effectComposer.setSize(size.width, size.height)
> effectComposer.setPixelRatio(window.devicePixelRatio)
> 
> // 加载 RenderPass 渲染许可模块
> import { RenderPass } from "three/examples/jsm/postprocessing/RenderPass"
> const renderPass = new RenderPass(scene, camera)
> effectComposer.addPass(renderPass)
> 
> // 帧动画
> const frame = () => {
> 
>     // renderer.render(scene, camera)
>     // 用 effectComposer 代替渲染
>     effectComposer.render()
> 
>     window.requestAnimationFrame(frame)
> }
> frame()
> ```



#### Pass

> 引入不同的效果模块文件，实现不同的效果；



###### DotScreenPass

> 像素点效果；
>
> ```javascript
> // 引入模块文件
> import { DotScreenPass } from 'three/examples/jsm/postprocessing/DotScreenPass'
> const dotScreenPass = new DotScreenPass()
> // 是否开启效果
> dotScreenPass.enabled = true
> // 加入到 effectComposer 中
> effectComposer.addPass(dotScreenPass)
> ```



###### GlitchPass

> 画面故障效果；
>
> ```javascript
> // 引入模块文件
> import { GlitchPass } from "three/examples/jsm/postprocessing/GlitchPass"
> const glitchPass = new GlitchPass()
> glitchPass.enabled = true
> // 是否加强效果
> glitchPass.goWild = false
> // 加入到 effectComposer 中
> effectComposer.addPass(glitchPass)
> ```



###### ShaderPass

> 着色器效果；
>
> ```javascript
> import { RGBShiftShader } from "three/examples/jsm/shaders/RGBShiftShader"
> // 引入模块文件
> import { ShaderPass } from 'three/examples/jsm/postprocessing/ShaderPass'
> const shaderPass = new ShaderPass(RGBShiftShader)
> // 是否开启效果
> shaderPass.enabled = false
> // 加入到 effectComposer 中
> effectComposer.addPass(shaderPass)
> ```



###### UnrealBloomPass

> 不真实环境效果；
>
> ```javascript
> // 引入模块文件
> import { UnrealBloomPass } from "three/examples/jsm/postprocessing/UnrealBloomPass"
> const unrealBloomPass = new UnrealBloomPass()
> // 效果影响强度
> unrealBloomPass.strength = 1.0
> // 效果影响半径
> unrealBloomPass.radius = 1.0
> // 效果影响阈值
> unrealBloomPass.threshold = 0.2
> // 加入到 effectComposer 中
> effectComposer.addPass(unrealBloomPass)
> ```





## 第三方库

> 在 ThreeJs 开发中，起到辅助作用的第三方插件模块；



### dat.gui

> 调试( Debug UI )；
>
> 在网页中调整参数实时预览效果的模块插件；



#### Install

> 通过 npm 下载模块；
>
> ```shell
> npm install dat.gui --save
> ```



#### Import

> 在文件中引入模块；
>
> ```javascript
> import * as dat from "dat.gui"
> ```



#### Use

> 使用模块调试参数；
>
> ```javascript
> // 创建 mesh
> const mesh = new THREE.Mesh(
>  // geometry
>  new THREE.BoxBufferGeometry(1, 1, 1, 2, 2, 2),
>  // material
>  new THREE.MeshBasicMaterial({
>     color: '#96c24e',
>  })
> )
> 
> // 创建 gui 实例对象
> const gui = new dat.GUI({
>  // gui 调试界面的宽度
>  width: 300
> })
> 
> // 定义一个保存需要调试的参数对象
> const parameter = { color: "#96c24e" }
> 
> // 可视性
> gui.add(mesh, "visible")
> 
> // 显示线框
> gui.add(mesh.material, "wireframe")
> 
> // 材质颜色
> gui.addColor(parameter, "color").onChange(() => {
>  mesh.material.color.set(parameter.color);
> })
> ```



### Stats.js

> 监测`FPS`性能的辅助插件，查看程序的运行性能；



#### Install

> ```shell
> npm install stats.js --save-dev
> ```



#### Use

> ```javascript
> // 引入模块文件
> import Stats from "stats.js"
> const stats = new Stats()
> // 添加到页面中
> document.body.appendChild(stats.dom)
> 
> // 帧动画
> const frame = () => {
>     // 开始监测
>     stats.begin()
> 
>     renderer.render(scene, camera)
>     window.requestAnimationFrame(frame)
> 
>     // 结束监测
>     stats.end()
> }
> frame()
> ```



### gsap

> 不借助帧动画实现动画过渡效果的插件（补间动画）；



#### Insatll

> ```shell
> npm install gsap --save
> ```



#### Use

> ```javascript
> // 引入模块文件
> import gsap from "gsap"
> 
> // 物体
> const cube = new THREE.Mesh(
>     new THREE.BoxBufferGeometry(1, 1, 1, 2, 2, 2),
>     new THREE.MeshStandardMaterial({ color: '#fafafa' })
> )
> 
> // 使用 gsap
> window.addEventListener("scroll", () => {
>     // 开始动画过渡
>     gsap.to(cube.rotation, {
>        duration: 1.5,
>        x: "+=4",
>        y: "+=3",
>     })
> })
> ```













