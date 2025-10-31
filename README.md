# 生成式 AI - ComfyUI 自訂工作流
![自訂工作流圖](https://raw.githubusercontent.com/94yuanyuan/Comfy-work/main/workflow.png)
## 功能介紹

工作流以兩個起點區塊為核心：**txt2img**（文生圖）與 **img2img**（圖生圖），使用時，僅啟用一個起點，屏蔽另一個。

- **img2img 區塊細節**：內含 **SetLatentNoiseMask** 節點，為選用項目。僅在局部重繪需求時串接遮罩（mask），否則須屏蔽節點並移除連線。
- **主要功能模塊**：分為三大區塊——**ControlNet**（姿態控制）、**IPAdapter**（風格參考）與 **Hires.fix**（高解析放大）。這些模塊可獨立或組合使用。
- **IPAdapter 擴充**：若參考對象為多個，啟用擴充參考區塊，並確保 **IPA Encoder** 與 **IPA CombineEmbeds** 正確連接。若需更多參考，複製擴充區塊並維持連線邏輯。
- **多模塊串接**：若同時需 **ControlNet (OpenPose 姿勢參考)** 與 **Hires.fix**，應分次執行：先完成 OpenPose 繪製，再從 img2img 起點進行 Hires.fix，以避免雜訊干擾。

## 技術說明

以 Pikachu 埃及風主題為例，展示工作流的迭代過程。從基底生成逐步優化，強調模塊屏蔽與串接的實務應用。每個範例包含工作流程圖、關鍵調整與提示詞（正/負向）。

### 範例 1: 單純文生圖 (txt2img 基底) {#example-1}
![t2i](https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/workflow-1.png)
屏蔽大多數節點，聚焦純文字提示生成，快速驗證概念。

<details>
<summary>提示詞（點擊展開）</summary>

**正向**：  
masterpiece, best quality, dynamic angle, lens flare, outdoors, sun, on a desert, full shot, face focus, looking at viewer, solo,  
(Pikachu:1.3), detailed fur:1.1, cute, open eyes wide, big round eyes, happily, standing,  
egyptian attire, (golden metal accents:0.6), (linen dress:0.4), short sleeves, silk textures  

**負向**：  
bad quality, worst quality, worst detail, nsfw, mutated body, elongated limbs, humanized proportions, overexposed, lowres, blurry, watermark, text, deformed anatomy, extra limbs, cut off limbs, incomplete body, cropped anatomy, missing parts, realistic, mosaic, review  
</details>

****

### 範例 2: 引入 LORA 的文生圖 {#example-2}
![t2i](https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/workflow-2.png)
基於範例 1，應用風格 LORA 微調，測試風格變換效果。

****

### 範例 3: 引入 ControlNet OpenPose 的文生圖 {#example-3}
![t2i](https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/workflow-3.png)
導入從 Stable Diffusion Forge 製作並匯出的骨架，結合提示詞調整，解決比例變形問題。
<img src="https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/Z-pose.png" alt="t2i" width="300" height="300">

<details>
<summary>提示詞（點擊展開）</summary>

**正向**：  
masterpiece, best quality, dynamic angle, lens flare, outdoors, sun, on a desert, from below, full shot, solo,  
(Pikachu:1.3), detailed fur:1.1, cute, open eyes wide, big round eyes, happily, z-move pose,  
(left arm extended bent to right cheek:1.2), (right arm stretched diagonally across body:1.2), (crossed arms:0.8), elongated arms for reach, dynamic arm extension, longer limbs proportionally, extended reach pose, no limited arm length,  
egyptian attire, (golden metal accents:0.6), (linen dress:0.4), short sleeves, silk textures  

**負向**：  
bad quality, worst quality, worst detail, nsfw, mutated body, elongated limbs, humanized proportions, overexposed, lowres, blurry, watermark, text, deformed anatomy, extra limbs, cut off limbs, incomplete body, cropped anatomy, missing parts, realistic, mosaic, review  
</details>

****

### 範例 4: 引入 IPAdapter 的圖生圖 {#example-4}
![t2i](https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/workflow-4.png)
當滿意姿勢與風格服裝分屬不同圖片時，從 img2img 起點迭代。導入簡易修復圖及範例 1 服裝（預處理：去除外元素、放大以增強注意力）。

<img src="https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/clipspace-painted-8630132.png" alt="t2i" width="300" height="300">
<img src="https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/ComfyUI_00145_.png" alt="t2i" width="300" height="300">

提示詞同範例 1。透過遮罩重繪，成功融合元素。

****

### 範例 5: 使用 Hires.fix 的圖生圖 {#example-5}
![t2i](https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/workflow-5.png)
基於範例 4 成果，從 img2img 起點進行高解析放大。開啟多數節點，但屏蔽 ControlNet OpenPose 以避免雜訊。

<img src="https://raw.githubusercontent.com/94yuanyuan/Comfy-work/refs/heads/main/ComfyUI_00158_.png" alt="t2i" width="300" height="300">

提示詞同範例 4。最終輸出解析度為 2048x2048。
