# Blender 中的 Object 與 Data Type（以計算機視覺視角理解）

在 Blender 中，「Object 是什麼？」這個問題，對於一般建模或動畫使用者來說也許不重要，但在**計算機視覺、幾何推導與資料集生成**的應用中，這個設計差異至關重要。

本文從計算機視覺（Computer Vision）的角度，說明 Blender 中 Object 與其下層 Data Type（如 Mesh、Camera）的角色分工。

---

## 重點一：在計算機視覺中，Object 只存放位置、姿態與動畫資訊

在 Blender 中，`Object` 是一個空間與時間的容器。

從計算機視覺角度來看，Object 描述的是一個實體在世界座標系中的 **SE(3) 變換與時間行為**，包含：

- 位置（Translation）
- 姿態（Rotation）
- 尺度（Scale）
- 動畫（Keyframes / Constraints / Parenting）

```python
obj.location
obj.rotation_euler
obj.rotation_quaternion
obj.matrix_world
```
```cmd
obj.location
obj.rotation_euler
obj.rotation_quaternion
obj.matrix_world
```
Object 本身**不包含任何與幾何形狀或成像模型直接相關的資訊**。

---

## 重點二：真正與形狀相關的資訊，存放於 Object 下層的 Mesh Data，且支援多 Object 共用

在 Blender 中，幾何形狀不存放於 Object，而是存放於 Object 所指向的 **Mesh data block**。

```python
obj.type == 'MESH'
mesh = obj.data
```

Mesh data 中包含頂點、邊、面、UV 等所有與幾何形狀相關的描述。

Blender 採用 Object / Data 分離設計，使得多個 Object 可以指向同一份 Mesh data，
而每個 Object 則各自擁有獨立的位置與姿態。

從計算機視覺角度來看，這對應的是：
同一個幾何模型，搭配多組不同的外參（extrinsics）。

---

## 重點三：Camera 與 Mesh 相同，是 Object 下層的 Data Type，且同樣支援多 Object 共用

Camera 在 Blender 中並不是 Object 本身，而是 Object 所指向的另一種 Data Type。

```python
obj.type == 'CAMERA'
camera = obj.data
```

Camera data 中定義的是相機的成像模型與內部參數，例如：

- 投影模型（Perspective / Orthographic / Panorama）
- 焦距（Focal Length）
- Sensor size
- Clip planes

Camera Object 本身只負責相機在世界中的位置、姿態與動畫。

與 Mesh 相同，多個 Camera Object 也可以共用同一個 Camera data，
代表多個外參對應同一組內參（intrinsics）。

---

## Object / Mesh / Camera 對照表（計算機視覺視角）

| 項目 | Object | Mesh Data | Camera Data |
|------|--------|-----------|-------------|
| 所屬層級 | Scene 中的實體 | Object 下層 Data block | Object 下層 Data block |
| 是否描述位置 | ✔ | ✘ | ✘ |
| 是否描述姿態 | ✔ | ✘ | ✘ |
| 是否包含動畫 | ✔ | ✘ | ✘ |
| 是否描述幾何形狀 | ✘ | ✔ | ✘ |
| 是否描述投影模型 | ✘ | ✘ | ✔ |
| 是否可被多 Object 共用 | ✘ | ✔ | ✔ |
| 計算機視覺對應概念 | 外參（Extrinsics） | 幾何模型 | 內參（Intrinsics） |

---

## 常見誤解（為什麼 Object 不是 Mesh）

### 誤解一：Object 就是幾何模型本身

Object 並不包含任何幾何資訊，
幾何形狀完全存放於 Object 所指向的 Mesh data。

---

### 誤解二：複製 Object 就是複製一份幾何

多個 Object 可能指向同一個 Mesh data，
每個 Object 僅有自己的位置與姿態。

---

### 誤解三：Object 變形代表 Mesh 變形

Object 的 transform 只改變座標轉換，
Mesh data 本身並未被修改。

---

### 誤解四：Camera 是一種特殊的 Object

Camera 與 Mesh 一樣，
都是 Object 下層的 Data Type，
位置與姿態仍由 Object 管理。
