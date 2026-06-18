# BoxBuildingPlatform
用来放地形编辑网页网站的

<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>3D 地形编辑器</title>
<style>
:root { --bg: #1e1e2e; --sidebar-bg: #181825; --surface: #252536; --border: #363649; --text: #cdd6f4; --text-muted: #a6adc8; --accent: #89b4fa; --accent2: #a6e3a1; --warn: #f9e2af; --danger: #f38ba8; --radius: 6px; --transition: 0.15s ease; }
* { margin: 0; padding: 0; box-sizing: border-box; }
html, body { width: 100%; height: 100%; overflow: hidden; font-family: 'Segoe UI', system-ui, -apple-system, sans-serif; background: var(--bg); color: var(--text); user-select: none; -webkit-user-select: none; }
#app { display: flex; width: 100%; height: 100%; }

/* ===== Sidebar ===== */
#sidebar { width: 270px; min-width: 270px; background: var(--sidebar-bg); border-right: 1px solid var(--border); display: flex; flex-direction: column; overflow-y: auto; z-index: 10; }
.sidebar-header { padding: 14px 16px; border-bottom: 1px solid var(--border); }
.sidebar-header h1 { font-size: 17px; font-weight: 700; color: var(--accent); }
.sidebar-header .subtitle { font-size: 10px; color: var(--text-muted); margin-top: 2px; }

.tab-bar { display: flex; border-bottom: 1px solid var(--border); }
.tab-bar button { flex: 1; padding: 8px 4px; border: none; background: transparent; color: var(--text-muted); cursor: pointer; font-size: 11px; font-weight: 600; transition: var(--transition); border-bottom: 2px solid transparent; white-space: nowrap; }
.tab-bar button.active { color: var(--accent); border-bottom-color: var(--accent); background: rgba(137,180,250,0.05); }
.tab-bar button:hover:not(.active) { color: var(--text); }

.tab-content { display: none; flex-direction: column; gap: 10px; padding: 10px 14px; }
.tab-content.active { display: flex; }

.section { display: flex; flex-direction: column; gap: 5px; }
.section > label { font-size: 10px; font-weight: 600; text-transform: uppercase; color: var(--text-muted); letter-spacing: 0.5px; }

/* Scrollable block list */
.block-list { display: flex; flex-direction: column; gap: 2px; max-height: 240px; overflow-y: auto; border: 1px solid var(--border); border-radius: var(--radius); padding: 2px; background: var(--bg); }
.block-list-item { display: flex; align-items: center; gap: 10px; padding: 7px 10px; border-radius: 4px; cursor: pointer; transition: var(--transition); border: 2px solid transparent; }
.block-list-item:hover { background: rgba(255,255,255,0.04); border-color: var(--border); }
.block-list-item.selected { border-color: var(--accent); background: rgba(137,180,250,0.08); }
.block-list-swatch { width: 26px; height: 26px; border-radius: 4px; flex-shrink: 0; border: 1px solid rgba(255,255,255,0.12); }
.block-list-name { font-size: 12px; font-weight: 600; color: var(--text); text-align: left; flex: 1; }
.block-list-name small { font-weight: 400; color: var(--text-muted); font-size: 10px; }

.brush-row { display: flex; align-items: center; gap: 6px; }
.brush-row input[type=range] { flex: 1; }
.brush-row span { font-size: 11px; color: var(--text-muted); min-width: 20px; text-align: center; }

.mode-toggle { display: flex; gap: 3px; }
.mode-toggle button { flex: 1; padding: 7px; border: 1px solid var(--border); border-radius: var(--radius); background: var(--surface); color: var(--text); cursor: pointer; font-size: 11px; font-weight: 600; transition: var(--transition); }
.mode-toggle button.active { background: var(--accent); color: var(--sidebar-bg); border-color: var(--accent); }

.region-actions { display: flex; gap: 3px; }
.region-actions button { flex: 1; padding: 7px; border: 1px solid var(--border); border-radius: var(--radius); background: var(--surface); color: var(--text); cursor: pointer; font-size: 11px; font-weight: 600; transition: var(--transition); }
.region-actions button:hover { border-color: var(--text-muted); }
.region-actions button.active { background: var(--accent2); color: var(--sidebar-bg); border-color: var(--accent2); }

.volume-info { font-size: 10px; color: var(--warn); padding: 4px 6px; background: rgba(249,226,175,0.08); border-radius: 4px; border: 1px solid rgba(249,226,175,0.15); text-align: center; min-height: 20px; }
.btn-cancel { width: 100%; padding: 7px; border: 1px solid var(--danger); border-radius: var(--radius); background: transparent; color: var(--danger); cursor: pointer; font-size: 11px; font-weight: 600; transition: var(--transition); }
.btn-cancel:hover { background: rgba(243,139,168,0.1); }
.hint { font-size: 9px; color: var(--text-muted); font-style: italic; }
.checkbox-label { display: flex; align-items: center; gap: 6px; font-size: 11px; font-weight: 400; text-transform: none; cursor: pointer; color: var(--text); }
.checkbox-label input { cursor: pointer; }

/* Panel toggle buttons */
.panel-toggles { display: flex; gap: 4px; }
.panel-toggles button { flex: 1; padding: 6px 4px; border: 1px solid var(--border); border-radius: var(--radius); background: var(--surface); color: var(--text-muted); cursor: pointer; font-size: 10px; font-weight: 600; transition: var(--transition); white-space: nowrap; }
.panel-toggles button:hover { border-color: var(--text-muted); color: var(--text); }
.panel-toggles button.hidden { border-color: var(--danger); color: var(--danger); background: rgba(243,139,168,0.08); }

.layer-control { margin-top: auto; padding-top: 8px; border-top: 1px solid var(--border); display: flex; flex-direction: column; gap: 6px; }
.layer-control input[type=range] { width: 100%; cursor: pointer; }

.sidebar-footer { padding: 8px 14px; border-top: 1px solid var(--border); }
.btn-primary { width: 100%; padding: 9px; border: none; border-radius: var(--radius); background: var(--accent2); color: var(--sidebar-bg); font-size: 12px; font-weight: 700; cursor: pointer; transition: var(--transition); }
.btn-primary:hover { filter: brightness(1.1); }
#export-progress { display: none; margin-top: 6px; padding: 5px; border-radius: 4px; background: var(--surface); font-size: 10px; color: var(--accent2); text-align: center; }

/* ===== Main Area ===== */
#main-area { flex: 1; display: flex; flex-direction: column; min-width: 0; }
#viewer-container { position: relative; background: #111118; overflow: hidden; }
#grid-container { position: relative; background: #1a1a24; overflow: hidden; }

/* Resize divider */
#panel-divider { height: 6px; background: var(--border); cursor: row-resize; flex-shrink: 0; transition: background 0.1s; z-index: 5; }
#panel-divider:hover, #panel-divider.dragging { background: var(--accent); }
body.dragging-divider { cursor: row-resize; }
body.dragging-divider #viewer-container,
body.dragging-divider #grid-container { pointer-events: none; }

#grid-canvas { display: block; width: 100%; height: 100%; }
#grid-info { position: absolute; top: 6px; left: 10px; font-size: 10px; color: var(--text-muted); pointer-events: none; background: rgba(0,0,0,0.5); padding: 3px 8px; border-radius: 4px; }
#zoom-indicator { position: absolute; bottom: 6px; right: 10px; font-size: 9px; color: var(--text-muted); pointer-events: none; background: rgba(0,0,0,0.5); padding: 2px 6px; border-radius: 4px; }

::-webkit-scrollbar { width: 5px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }

input[type=range] { -webkit-appearance: none; appearance: none; height: 5px; background: var(--surface); border-radius: 3px; outline: none; }
input[type=range]::-webkit-slider-thumb { -webkit-appearance: none; width: 16px; height: 16px; border-radius: 50%; background: var(--accent); cursor: pointer; border: 2px solid var(--sidebar-bg); }
</style>
</head>
<body>

<div id="app">
  <!-- Sidebar -->
  <div id="sidebar">
    <div class="sidebar-header">
      <h1>⛰️ 地形编辑器</h1>
      <div class="subtitle">256 × 64 × 256</div>
    </div>

    <div class="tab-bar">
      <button class="tab active" data-tab="terrain">🏗️ 地形</button>
      <button class="tab" data-tab="volume">📦 立体</button>
      <button class="tab" data-tab="region">📐 区域</button>
    </div>

    <!-- 地形标签 -->
    <div id="tab-terrain" class="tab-content active">
      <div class="section">
        <label>方块类型</label>
        <div class="block-list" id="block-palette"></div>
      </div>
      <div class="section">
        <label>笔刷大小: <span id="brush-size-label">1</span></label>
        <input type="range" id="brush-size" min="1" max="8" value="1" step="1">
      </div>
      <div class="section">
        <label>模式</label>
        <div class="mode-toggle">
          <button id="btn-paint" class="active">🖌️ 绘制</button>
          <button id="btn-erase">🧹 删除</button>
        </div>
      </div>
    </div>

    <!-- 立体标签 -->
    <div id="tab-volume" class="tab-content">
      <div class="section">
        <label>方块类型</label>
        <div class="block-list" id="volume-block-palette"></div>
      </div>
      <div class="section">
        <label>笔刷大小: <span id="volume-brush-label">1</span></label>
        <input type="range" id="volume-brush-size" min="1" max="8" value="1" step="1">
      </div>
      <div id="volume-status" class="volume-info">在网格上绘制XY区域 → 松开 → 上下移动鼠标设定高度 → 点击生成</div>
      <button id="btn-cancel-volume" class="btn-cancel" style="display:none">✕ 取消高度选择</button>
    </div>

    <!-- 区域标签 -->
    <div id="tab-region" class="tab-content">
      <div class="section">
        <label>区域操作</label>
        <div class="region-actions">
          <button id="btn-region-fill">填充选区</button>
          <button id="btn-region-clear">清除选区</button>
        </div>
        <p class="hint">在网格上拖拽选择区域，松开即执行</p>
      </div>
      <div class="section">
        <label>填充方块</label>
        <div class="block-list" id="region-block-palette"></div>
      </div>
    </div>

    <!-- 面板开关按钮 -->
    <div class="section" style="padding:0 14px;">
      <label>窗口显示</label>
      <div class="panel-toggles">
        <button id="btn-toggle-3d" title="显示/隐藏3D渲染窗">👁️ 3D视图</button>
        <button id="btn-toggle-grid" title="显示/隐藏2D编辑窗">📐 2D网格</button>
      </div>
    </div>

    <div class="section layer-control">
      <label>当前层 (Y): <strong id="layer-label">4</strong></label>
      <input type="range" id="layer-slider" min="0" max="63" value="4" step="1">
      <label class="checkbox-label"><input type="checkbox" id="hide-current-layer">隐藏当前层（半透明）</label>
      <label class="checkbox-label"><input type="checkbox" id="enhanced-grid">🔲 栅格网（增强分界线清晰度）</label>
    </div>

    <div class="sidebar-footer">
      <button id="btn-export" class="btn-primary">📥 导出 JSON</button>
      <div id="export-progress"></div>
    </div>
  </div>

  <!-- 主区域 -->
  <div id="main-area">
    <div id="viewer-container"></div>
    <div id="panel-divider"></div>
    <div id="grid-container">
      <canvas id="grid-canvas"></canvas>
      <div id="grid-info">Y:4 | Shift+拖动平移 | 滚轮缩放 | 1-9选方块</div>
      <div id="zoom-indicator">100%</div>
    </div>
  </div>
</div>

<script type="importmap">
{ "imports": { "three": "https://unpkg.com/three@0.160.0/build/three.module.js", "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/" } }
</script>

<script type="module">
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

// ===== 常量 =====
const SIZE_X = 256, SIZE_Y = 64, SIZE_Z = 256;
const SLICE_SIZE = SIZE_X * SIZE_Z;
const TOTAL_BLOCKS = SIZE_X * SIZE_Y * SIZE_Z;

const BLOCK_TYPES = { BARRIER: 0, GRASS: 1, STONE: 2, DIRT: 3, WHITE: 4, SAND: 5, GREEN_LEAF: 6, WOOD: 7, WHITE_LIGHT: 8, ROCK: 9 };
const BLOCK_NAMES = { 0: 'barrier', 1: 'grass', 2: 'stone', 3: 'dirt', 4: 'white', 5: 'sand', 6: 'green_leaf', 7: 'wood', 8: 'white_light', 9: 'rock' };
const BLOCK_COLORS_CSS = {
  0: null, 1: '#7ec850', 2: '#808080', 3: '#8B4513', 4: '#FFFFFF',
  5: '#F4E4A0', 6: '#4CAF50', 7: '#8B5A2B', 8: '#FFF9C4', 9: '#B0B0B0'
};
const BLOCK_COLORS_HEX = {
  1: 0x7ec850, 2: 0x808080, 3: 0x8b4513, 4: 0xffffff, 5: 0xf4e4a0,
  6: 0x4caf50, 7: 0x8b5a2b, 8: 0xfff9c4, 9: 0xb0b0b0
};
const BLOCK_RGB = {};
for (const [type, hex] of Object.entries(BLOCK_COLORS_CSS)) {
  if (!hex) continue;
  const t = parseInt(type);
  BLOCK_RGB[t] = [parseInt(hex.slice(1,3),16), parseInt(hex.slice(3,5),16), parseInt(hex.slice(5,7),16)];
}

// ===== TerrainData =====
class TerrainData {
  constructor() {
    this.buffer = new Uint8Array(TOTAL_BLOCKS);
    this.currentY = 4;
    this.dirty3D = true;
    this.hiddenLayers = new Set();
    this.initFlatTerrain();
  }
  idx(x, y, z) { return x + SIZE_X * z + SLICE_SIZE * y; }
  getBlock(x, y, z) {
    if (x < 0 || x >= SIZE_X || y < 0 || y >= SIZE_Y || z < 0 || z >= SIZE_Z) return BLOCK_TYPES.BARRIER;
    return this.buffer[this.idx(x, y, z)];
  }
  setBlock(x, y, z, type) {
    if (x < 0 || x >= SIZE_X || y < 0 || y >= SIZE_Y || z < 0 || z >= SIZE_Z) return;
    this.buffer[this.idx(x, y, z)] = type;
    this.dirty3D = true;
  }
  getSlice(y) { return this.buffer.subarray(y * SLICE_SIZE, (y + 1) * SLICE_SIZE); }
  hideLayer(y) { this.hiddenLayers.add(y); this.dirty3D = true; }
  showLayer(y) { this.hiddenLayers.delete(y); this.dirty3D = true; }
  isLayerHidden(y) { return this.hiddenLayers.has(y); }

  initFlatTerrain() {
    for (let y = 0; y <= 4; y++) {
      const type = y <= 2 ? BLOCK_TYPES.STONE : (y === 3 ? BLOCK_TYPES.DIRT : BLOCK_TYPES.GRASS);
      this.buffer.fill(type, y * SLICE_SIZE, (y + 1) * SLICE_SIZE);
    }
  }

  exportNonAir() {
    const result = [];
    for (let y = 0; y < SIZE_Y; y++) {
      const base = y * SLICE_SIZE;
      for (let z = 0; z < SIZE_Z; z++) {
        const rowBase = base + z * SIZE_X;
        for (let x = 0; x < SIZE_X; x++) {
          const type = this.buffer[rowBase + x];
          if (type !== BLOCK_TYPES.BARRIER) result.push({ x, y, z, type: BLOCK_NAMES[type] });
        }
      }
    }
    return result;
  }
}

// ===== Camera2D =====
class Camera2D {
  constructor() { this.zoom = 1; this.panX = 0; this.panY = 0; this.minZoom = 0.5; this.maxZoom = 32; }
  screenToWorld(sx, sy) { return { x: Math.floor((sx - this.panX) / this.zoom), z: Math.floor((sy - this.panY) / this.zoom) }; }
  worldToScreen(wx, wz) { return { x: wx * this.zoom + this.panX, y: wz * this.zoom + this.panY }; }
  zoomAt(sx, sy, delta) {
    const factor = delta > 0 ? 1.15 : 1 / 1.15;
    const newZoom = Math.max(this.minZoom, Math.min(this.maxZoom, this.zoom * factor));
    if (newZoom === this.zoom) return;
    this.panX = sx - (sx - this.panX) * (newZoom / this.zoom);
    this.panY = sy - (sy - this.panY) * (newZoom / this.zoom);
    this.zoom = newZoom;
  }
  pan(dx, dy) { this.panX += dx; this.panY += dy; }
  fitToContainer(cw, ch) {
    this.zoom = Math.max(1, Math.floor(Math.min(cw / SIZE_X, ch / SIZE_Z)));
    this.panX = (cw - SIZE_X * this.zoom) / 2;
    this.panY = (ch - SIZE_Z * this.zoom) / 2;
  }
}

// ===== LayerRenderer =====
class LayerRenderer {
  constructor(canvas) {
    this.displayCanvas = canvas; this.displayCtx = canvas.getContext('2d');
    this.offscreen = document.createElement('canvas'); this.offscreen.width = SIZE_X; this.offscreen.height = SIZE_Z;
    this.offscreenCtx = this.offscreen.getContext('2d');
    this.imageData = this.offscreenCtx.createImageData(SIZE_X, SIZE_Z);
    this.enhancedGrid = false;
  }
  fullRedraw(terrain) {
    const slice = terrain.getSlice(terrain.currentY);
    const data = this.imageData.data;
    for (let i = 0; i < slice.length; i++) {
      const rgb = BLOCK_RGB[slice[i]]; const pi = i * 4;
      if (rgb) { data[pi]=rgb[0]; data[pi+1]=rgb[1]; data[pi+2]=rgb[2]; data[pi+3]=255; }
      else { data[pi]=26; data[pi+1]=26; data[pi+2]=36; data[pi+3]=255; }
    }
    this.offscreenCtx.putImageData(this.imageData, 0, 0);
  }
  partialRedraw(terrain, x1, z1, x2, z2) {
    const slice = terrain.getSlice(terrain.currentY);
    const data = this.imageData.data;
    const cx1 = Math.max(0, x1), cz1 = Math.max(0, z1);
    const cx2 = Math.min(SIZE_X - 1, x2), cz2 = Math.min(SIZE_Z - 1, z2);
    for (let z = cz1; z <= cz2; z++)
      for (let x = cx1; x <= cx2; x++) {
        const rgb = BLOCK_RGB[slice[x + SIZE_X * z]]; const pi = (z * SIZE_X + x) * 4;
        if (rgb) { data[pi]=rgb[0]; data[pi+1]=rgb[1]; data[pi+2]=rgb[2]; data[pi+3]=255; }
        else { data[pi]=26; data[pi+1]=26; data[pi+2]=36; data[pi+3]=255; }
      }
    this.offscreenCtx.putImageData(this.imageData, 0, 0);
  }
  render(camera, terrain) {
    const ctx = this.displayCtx, cw = this.displayCanvas.width, ch = this.displayCanvas.height;
    ctx.fillStyle = '#1a1a24'; ctx.fillRect(0, 0, cw, ch);
    ctx.save();
    ctx.translate(camera.panX, camera.panY); ctx.scale(camera.zoom, camera.zoom);
    ctx.imageSmoothingEnabled = false;
    const isCurHidden = terrain.isLayerHidden(terrain.currentY);
    ctx.globalAlpha = isCurHidden ? 0.35 : 1.0;
    ctx.drawImage(this.offscreen, 0, 0);
    ctx.globalAlpha = 1.0;
    ctx.restore();
    const enh = this.enhancedGrid;
    const showGridAt = enh ? 1.5 : 4;
    if (camera.zoom >= showGridAt) {
      ctx.strokeStyle = enh ? 'rgba(255,255,255,0.35)' : 'rgba(255,255,255,0.08)';
      ctx.lineWidth = enh ? 2 : 1;
      ctx.beginPath();
      const step = enh ? (camera.zoom >= 8 ? 1 : (camera.zoom >= 4 ? 2 : 5)) : (camera.zoom >= 16 ? 1 : (camera.zoom >= 8 ? 5 : 10));
      const sx = Math.max(0, Math.floor(-camera.panX / camera.zoom / step)) * step;
      const sz = Math.max(0, Math.floor(-camera.panY / camera.zoom / step)) * step;
      const ex = Math.min(SIZE_X, Math.ceil((cw - camera.panX) / camera.zoom));
      const ez = Math.min(SIZE_Z, Math.ceil((ch - camera.panY) / camera.zoom));
      for (let x = sx; x <= ex; x += step) { const px = x * camera.zoom + camera.panX; ctx.moveTo(px, camera.panY + sz * camera.zoom); ctx.lineTo(px, camera.panY + ez * camera.zoom); }
      for (let z = sz; z <= ez; z += step) { const py = z * camera.zoom + camera.panY; ctx.moveTo(camera.panX + sx * camera.zoom, py); ctx.lineTo(camera.panX + ex * camera.zoom, py); }
      ctx.stroke();
    }
    ctx.fillStyle = 'rgba(255,255,255,0.6)';
    ctx.font = `${Math.max(10, camera.zoom * 0.8)}px "Segoe UI"`;
    ctx.fillText(`Y=${terrain.currentY}`, 4, 4);
  }
}

// ===== BrushTool =====
class BrushTool {
  constructor() { this.size = 1; this.mode = 'paint'; this.selectedBlock = BLOCK_TYPES.GRASS; }
  apply(terrain, x, z) {
    const r = this.size - 1;
    const newType = this.mode === 'paint' ? this.selectedBlock : BLOCK_TYPES.BARRIER;
    let mx = x, Mx = x, mz = z, Mz = z;
    for (let dz = -r; dz <= r; dz++)
      for (let dx = -r; dx <= r; dx++) {
        const wx = x + dx, wz = z + dz;
        if (wx < 0 || wx >= SIZE_X || wz < 0 || wz >= SIZE_Z) continue;
        terrain.setBlock(wx, terrain.currentY, wz, newType);
        mx = Math.min(mx, wx); Mx = Math.max(Mx, wx);
        mz = Math.min(mz, wz); Mz = Math.max(Mz, wz);
      }
    return { x1: mx, z1: mz, x2: Mx, z2: Mz };
  }
}

// ===== VolumeTool =====
class VolumeTool {
  constructor() {
    this.size = 1; this.selectedBlock = BLOCK_TYPES.GRASS;
    this.state = 'paint'; this.footprint = { x1: -1, z1: -1, x2: -1, z2: -1 }; this.heightDelta = 0;
  }
  reset() { this.state = 'paint'; this.footprint = { x1: -1, z1: -1, x2: -1, z2: -1 }; this.heightDelta = 0; }
  hasFootprint() { return this.footprint.x1 >= 0; }
  applyPaint(terrain, x, z) {
    const r = this.size - 1;
    for (let dz = -r; dz <= r; dz++)
      for (let dx = -r; dx <= r; dx++) {
        const wx = x + dx, wz = z + dz;
        if (wx < 0 || wx >= SIZE_X || wz < 0 || wz >= SIZE_Z) continue;
        if (this.footprint.x1 < 0) { this.footprint = { x1: wx, z1: wz, x2: wx, z2: wz }; }
        else { this.footprint.x1 = Math.min(this.footprint.x1, wx); this.footprint.z1 = Math.min(this.footprint.z1, wz); this.footprint.x2 = Math.max(this.footprint.x2, wx); this.footprint.z2 = Math.max(this.footprint.z2, wz); }
      }
  }
  setHeight(pixels, cameraZoom) { this.heightDelta = Math.round(pixels / (cameraZoom * 1.5)); }
  getYRange(baseY) {
    const y1 = Math.min(baseY, baseY + this.heightDelta), y2 = Math.max(baseY, baseY + this.heightDelta);
    return { y1: Math.max(0, y1), y2: Math.min(SIZE_Y - 1, y2) };
  }
  build(terrain, baseY) {
    if (!this.hasFootprint()) return null;
    const { y1, y2 } = this.getYRange(baseY);
    const f = this.footprint;
    for (let y = y1; y <= y2; y++)
      for (let z = f.z1; z <= f.z2; z++)
        for (let x = f.x1; x <= f.x2; x++)
          terrain.setBlock(x, y, z, this.selectedBlock);
    const rect = { x1: f.x1, z1: f.z1, x2: f.x2, z2: f.z2 };
    this.reset();
    return { rect, y1, y2 };
  }
}

// ===== RegionTool =====
class RegionTool {
  constructor() { this.active = false; this.startX = -1; this.startZ = -1; this.endX = -1; this.endZ = -1; this.mode = 'fill'; this.fillType = BLOCK_TYPES.GRASS; }
  getRect() { return { x1: Math.min(this.startX, this.endX), z1: Math.min(this.startZ, this.endZ), x2: Math.max(this.startX, this.endX), z2: Math.max(this.startZ, this.endZ) }; }
  apply(terrain) {
    const r = this.getRect();
    const type = this.mode === 'fill' ? this.fillType : BLOCK_TYPES.BARRIER;
    for (let z = r.z1; z <= r.z2; z++)
      for (let x = r.x1; x <= r.x2; x++)
        terrain.setBlock(x, terrain.currentY, z, type);
    return r;
  }
}

// ===== ThreeViewer =====
class ThreeViewer {
  constructor(container) {
    this.container = container; this.meshes = {}; this.hiddenMeshes = {};
    this.rebuildTimer = null; this._isDisposed = false;
    this._initScene();
  }
  _initScene() {
    this.renderer = new THREE.WebGLRenderer({ antialias: false, alpha: false });
    this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    this.renderer.setClearColor(0x111118);
    this.container.appendChild(this.renderer.domElement);
    this.scene = new THREE.Scene(); this.scene.background = new THREE.Color(0x111118);
    this.scene.fog = new THREE.Fog(0x111118, 300, 900);
    this.camera = new THREE.PerspectiveCamera(50, 2, 1, 2000);
    this.camera.position.set(200, 180, 350);
    this.controls = new OrbitControls(this.camera, this.renderer.domElement);
    this.controls.target.set(SIZE_X / 2, 32, SIZE_Z / 2);
    this.controls.enableDamping = true; this.controls.dampingFactor = 0.08;
    this.controls.minDistance = 50; this.controls.maxDistance = 800;
    this.controls.maxPolarAngle = Math.PI * 0.48; this.controls.update();
    this.scene.add(new THREE.AmbientLight(0xffffff, 0.5));
    const sun = new THREE.DirectionalLight(0xffffff, 0.9); sun.position.set(200, 300, 150); this.scene.add(sun);
    const fill = new THREE.DirectionalLight(0x8899cc, 0.3); fill.position.set(-100, 50, -150); this.scene.add(fill);
    const grid = new THREE.GridHelper(SIZE_X, 20, 0x333344, 0x1a1a2a);
    grid.position.set(SIZE_X / 2 - 0.5, -0.5, SIZE_Z / 2 - 0.5); this.scene.add(grid);
    const pg = new THREE.PlaneGeometry(SIZE_X, SIZE_Z);
    this.layerPlane = new THREE.Mesh(pg, new THREE.MeshBasicMaterial({ color: 0xff4444, transparent: true, opacity: 0.15, side: THREE.DoubleSide, depthWrite: false }));
    this.layerPlane.rotation.x = -Math.PI / 2;
    this.layerPlane.position.set(SIZE_X / 2 - 0.5, 0.5, SIZE_Z / 2 - 0.5); this.scene.add(this.layerPlane);
    const boxGeo = new THREE.BoxGeometry(1, 1, 1);
    for (let t = 1; t <= 9; t++) {
      const color = BLOCK_COLORS_HEX[t] || 0xffffff;
      const mat = new THREE.MeshLambertMaterial({ color });
      this.meshes[t] = new THREE.InstancedMesh(boxGeo, mat, 0); this.meshes[t].count = 0; this.scene.add(this.meshes[t]);
      const hMat = new THREE.MeshLambertMaterial({ color, transparent: true, opacity: 0.3, depthWrite: false });
      this.hiddenMeshes[t] = new THREE.InstancedMesh(boxGeo, hMat, 0); this.hiddenMeshes[t].count = 0; this.hiddenMeshes[t].renderOrder = 1; this.scene.add(this.hiddenMeshes[t]);
    }
    this._animate = this._animate.bind(this);
    requestAnimationFrame(this._animate);
  }
  _animate() {
    if (this._isDisposed) return;
    requestAnimationFrame(this._animate);
    this.controls.update(); this.renderer.render(this.scene, this.camera);
  }
  resize() {
    const w = this.container.clientWidth, h = this.container.clientHeight;
    if (w === 0 || h === 0) return;
    this.renderer.setSize(w, h, false); this.camera.aspect = w / h; this.camera.updateProjectionMatrix();
  }
  setLayerY(y) { this.layerPlane.position.y = y + 0.5; }

  rebuild(terrain) {
    const buffer = terrain.buffer;
    const flat = {}; const hiddenFlat = {};
    for (let t = 1; t <= 9; t++) { flat[t] = []; hiddenFlat[t] = []; }
    const tmpM = new THREE.Matrix4(); const buf16 = new Float32Array(16);
    for (let y = 0; y < SIZE_Y; y++) {
      const base = y * SLICE_SIZE; const isHidden = terrain.isLayerHidden(y);
      for (let z = 0; z < SIZE_Z; z++) {
        const rowBase = base + z * SIZE_X;
        for (let x = 0; x < SIZE_X; x++) {
          const type = buffer[rowBase + x];
          if (type === 0) continue;
          if (!isHidden && !this._isExposedFast(buffer, x, y, z)) continue;
          tmpM.identity(); tmpM.setPosition(x, y, z); tmpM.toArray(buf16);
          const target = isHidden ? hiddenFlat : flat;
          const arr = target[type] || [];
          for (let j = 0; j < 16; j++) arr.push(buf16[j]);
        }
      }
    }
    for (let t = 1; t <= 9; t++) {
      for (const [meshes, arr] of [[this.meshes, flat[t]], [this.hiddenMeshes, hiddenFlat[t]]]) {
        const mesh = meshes[t]; if (!mesh) continue;
        const count = arr.length / 16;
        if (count === 0) { mesh.count = 0; continue; }
        mesh.instanceMatrix = new THREE.InstancedBufferAttribute(new Float32Array(arr), 16);
        mesh.count = count; mesh.instanceMatrix.needsUpdate = true; mesh.computeBoundingSphere();
      }
    }
    terrain.dirty3D = false;
  }

  scheduleRebuild(terrain, delay = 300) {
    if (this.rebuildTimer) clearTimeout(this.rebuildTimer);
    this.rebuildTimer = setTimeout(() => this.rebuild(terrain), delay);
  }
  _isExposedFast(buffer, x, y, z) {
    const SL = SLICE_SIZE, SX = SIZE_X;
    if (x + 1 >= SIZE_X || buffer[(x + 1) + SX * z + SL * y] === 0) return true;
    if (x - 1 < 0 || buffer[(x - 1) + SX * z + SL * y] === 0) return true;
    if (y + 1 >= SIZE_Y || buffer[x + SX * z + SL * (y + 1)] === 0) return true;
    if (y - 1 < 0 || buffer[x + SX * z + SL * (y - 1)] === 0) return true;
    if (z + 1 >= SIZE_Z || buffer[x + SX * (z + 1) + SL * y] === 0) return true;
    if (z - 1 < 0 || buffer[x + SX * (z - 1) + SL * y] === 0) return true;
    return false;
  }
  dispose() {
    this._isDisposed = true; if (this.rebuildTimer) clearTimeout(this.rebuildTimer);
    this.renderer.dispose(); this.controls.dispose();
    for (const set of [this.meshes, this.hiddenMeshes])
      for (const mesh of Object.values(set)) { mesh.geometry.dispose(); mesh.material.dispose(); }
    if (this.renderer.domElement.parentNode) this.renderer.domElement.parentNode.removeChild(this.renderer.domElement);
  }
}

// ===== PanelManager =====
class PanelManager {
  constructor() {
    this.viewerEl = document.getElementById('viewer-container');
    this.gridEl = document.getElementById('grid-container');
    this.divider = document.getElementById('panel-divider');
    this.viewerVisible = true;
    this.gridVisible = true;
    this.viewerRatio = 0.6;
    this.gridRatio = 0.4;
    this._initDivider();
    this._apply();
  }
  _initDivider() {
    let dragging = false, startY = 0, startVR = 0;
    this.divider.addEventListener('mousedown', (e) => {
      dragging = true; startY = e.clientY; startVR = this.viewerRatio;
      this.divider.classList.add('dragging');
      document.body.classList.add('dragging-divider');
      e.preventDefault();
    });
    document.addEventListener('mousemove', (e) => {
      if (!dragging) return;
      const mainRect = document.getElementById('main-area').getBoundingClientRect();
      const totalH = mainRect.height - 6;
      if (totalH <= 0) return;
      const dy = e.clientY - startY;
      const newVR = Math.max(0.1, Math.min(0.9, startVR + dy / totalH));
      this.viewerRatio = newVR;
      this.gridRatio = 1 - newVR;
      this._apply();
    });
    document.addEventListener('mouseup', () => {
      dragging = false;
      this.divider.classList.remove('dragging');
      document.body.classList.remove('dragging-divider');
    });
  }
  _apply() {
    const vV = this.viewerVisible, gV = this.gridVisible;
    this.viewerEl.style.display = vV ? '' : 'none';
    this.gridEl.style.display = gV ? '' : 'none';
    this.divider.style.display = (vV && gV) ? '' : 'none';

    if (vV && gV) {
      this.viewerEl.style.flex = `${this.viewerRatio} 1 0%`;
      this.gridEl.style.flex = `${this.gridRatio} 1 0%`;
    } else if (vV && !gV) {
      this.viewerEl.style.flex = '1 1 0%';
    } else if (!vV && gV) {
      this.gridEl.style.flex = '1 1 0%';
    }

    // Resize Three.js + re-fit grid after layout change
    requestAnimationFrame(() => {
      if (window._app) {
        window._app.threeViewer.resize();
        window._app._refitGrid();
        window._app.requestGridRender();
      }
    });
  }
  toggleViewer() { this.viewerVisible = !this.viewerVisible; this._apply(); return this.viewerVisible; }
  toggleGrid() { this.gridVisible = !this.gridVisible; this._apply(); return this.gridVisible; }
}

// ===== InteractionHandler =====
class InteractionHandler {
  constructor(canvas, camera, app) {
    this.canvas = canvas; this.camera = camera; this.app = app;
    this.isDragging = false; this.isPanning = false; this.isVolumeHeight = false;
    this.panStart = { x: 0, y: 0 }; this.lastBrushPos = { x: -1, z: -1 };
    this.volumeStartY = 0; this.volumeBaseY = 0;

    this._onMouseDown = this._onMouseDown.bind(this);
    this._onMouseMove = this._onMouseMove.bind(this);
    this._onMouseUp = this._onMouseUp.bind(this);
    this._onWheel = this._onWheel.bind(this);
    canvas.addEventListener('mousedown', this._onMouseDown);
    canvas.addEventListener('mousemove', this._onMouseMove);
    canvas.addEventListener('mouseup', this._onMouseUp);
    canvas.addEventListener('mouseleave', this._onMouseUp);
    canvas.addEventListener('wheel', this._onWheel, { passive: false });
    canvas.addEventListener('contextmenu', e => e.preventDefault());
  }
  _getPos(e) {
    const r = this.canvas.getBoundingClientRect();
    // Correct for any internal-vs-CSS size mismatch
    const sx = this.canvas.width / r.width;
    const sy = this.canvas.height / r.height;
    return { x: (e.clientX - r.left) * sx, y: (e.clientY - r.top) * sy };
  }
  _getActiveTab() { return document.querySelector('.tab-bar button.active')?.dataset?.tab || 'terrain'; }

  _onMouseDown(e) {
    const pos = this._getPos(e);
    if (e.button === 1 || (e.button === 0 && e.shiftKey)) { this.isPanning = true; this.panStart = pos; return; }
    if (e.button !== 0) return;
    const world = this.camera.screenToWorld(pos.x, pos.y);
    if (world.x < 0 || world.x >= SIZE_X || world.z < 0 || world.z >= SIZE_Z) return;
    const tab = this._getActiveTab();

    if (tab === 'volume') {
      if (this.app.volumeTool.state === 'height' && this.app.volumeTool.hasFootprint()) {
        const result = this.app.volumeTool.build(this.app.terrain, this.volumeBaseY);
        if (result) {
          this.app.onTerrainEdit(result.rect);
          this.app.threeViewer.rebuild(this.app.terrain);
          this.app.requestGridRender();
          document.getElementById('volume-status').textContent = `已生成立体: Y[${result.y1}..${result.y2}] ${result.rect.x2-result.rect.x1+1}×${result.rect.z2-result.rect.z1+1} → 3D重建中...`;
          document.getElementById('btn-cancel-volume').style.display = 'none';
        }
        this.isVolumeHeight = false;
      } else {
        this.isDragging = true;
        this.app.volumeTool.reset();
        this.app.volumeTool.applyPaint(this.app.terrain, world.x, world.z);
        this.app.requestGridRender();
      }
      return;
    }
    if (tab === 'region') {
      if (this.app.regionTool.active) {
        this.app.regionTool.startX = world.x; this.app.regionTool.startZ = world.z;
        this.app.regionTool.endX = world.x; this.app.regionTool.endZ = world.z;
        this.isDragging = true;
      }
      return;
    }
    this.isDragging = true;
    const rect = this.app.brushTool.apply(this.app.terrain, world.x, world.z);
    this.app.onTerrainEdit(rect);
    this.lastBrushPos = world;
  }

  _onMouseMove(e) {
    const pos = this._getPos(e);
    if (this.isPanning) {
      this.camera.pan(pos.x - this.panStart.x, pos.y - this.panStart.y);
      this.panStart = pos; this.app.requestGridRender(); return;
    }
    const tab = this._getActiveTab();
    const world = this.camera.screenToWorld(pos.x, pos.y);

    if (tab === 'volume' && this.isVolumeHeight) {
      const dy = this.volumeStartY - pos.y;
      this.app.volumeTool.setHeight(dy, this.camera.zoom);
      const yr = this.app.volumeTool.getYRange(this.volumeBaseY);
      document.getElementById('volume-status').textContent =
        `高度: ${this.app.volumeTool.heightDelta > 0 ? '+' : ''}${this.app.volumeTool.heightDelta} 层 → Y[${yr.y1}..${yr.y2}] | 点击确认生成`;
      this.app.requestGridRender();
      return;
    }
    if (this.isDragging) {
      if (world.x < 0 || world.x >= SIZE_X || world.z < 0 || world.z >= SIZE_Z) return;
      if (world.x === this.lastBrushPos.x && world.z === this.lastBrushPos.z) return;
      if (tab === 'volume') {
        this.app.volumeTool.applyPaint(this.app.terrain, world.x, world.z);
        this.app.requestGridRender();
      } else if (tab === 'region' && this.app.regionTool.active) {
        this.app.regionTool.endX = world.x; this.app.regionTool.endZ = world.z;
        this.app.requestGridRender();
      } else {
        const rect = this.app.brushTool.apply(this.app.terrain, world.x, world.z);
        this.app.onTerrainEdit(rect);
        this.lastBrushPos = world;
      }
    }
    if (world.x >= 0 && world.x < SIZE_X && world.z >= 0 && world.z < SIZE_Z) {
      document.getElementById('grid-info').textContent =
        `Y:${this.app.terrain.currentY} | X:${world.x} Z:${world.z}` + (tab==='volume'?' | 立体模式':'');
    }
  }

  _onMouseUp(e) {
    const tab = this._getActiveTab();
    if (tab === 'volume' && this.isDragging && this.app.volumeTool.hasFootprint()) {
      this.isDragging = false;
      this.isVolumeHeight = true;
      this.volumeStartY = this._getPos(e).y;
      this.volumeBaseY = this.app.terrain.currentY;
      this.app.volumeTool.state = 'height'; this.app.volumeTool.heightDelta = 0;
      document.getElementById('volume-status').textContent = '↑↓ 移动鼠标设定高度 | 点击确认生成';
      document.getElementById('btn-cancel-volume').style.display = 'block';
      document.getElementById('grid-info').textContent = '上下移动鼠标设定立体高度 | 点击确认';
      this.app.requestGridRender();
      return;
    }
    if (tab === 'region' && this.isDragging && this.app.regionTool.active && this.app.regionTool.startX >= 0) {
      const rect = this.app.regionTool.apply(this.app.terrain);
      this.app.onTerrainEdit(rect);
    }
    this.isDragging = false; this.isPanning = false;
    this.lastBrushPos = { x: -1, z: -1 };
  }

  _onWheel(e) {
    e.preventDefault();
    const pos = this._getPos(e);
    this.camera.zoomAt(pos.x, pos.y, -e.deltaY);
    this.app.requestGridRender();
  }

  cancelVolumeHeight() {
    this.isVolumeHeight = false; this.isDragging = false;
    this.app.volumeTool.reset();
    document.getElementById('volume-status').textContent = '在网格上绘制XY区域 → 松开 → 上下移动鼠标设定高度 → 点击生成';
    document.getElementById('btn-cancel-volume').style.display = 'none';
    document.getElementById('grid-info').textContent = `Y:${this.app.terrain.currentY} | Shift+拖动平移 | 滚轮缩放 | 1-9选方块`;
    this.app.requestGridRender();
  }
}

// ===== UIManager =====
class UIManager {
  constructor(app) { this.app = app; this._init(); }

  _init() {
    // 方块列表 (下拉滚动)
    this._buildBlockList('block-palette', (type) => {
      this.app.brushTool.selectedBlock = type;
      if (type === 0) { this.app.brushTool.mode = 'erase'; document.getElementById('btn-paint').classList.remove('active'); document.getElementById('btn-erase').classList.add('active'); }
      else if (this.app.brushTool.mode === 'erase') { this.app.brushTool.mode = 'paint'; document.getElementById('btn-erase').classList.remove('active'); document.getElementById('btn-paint').classList.add('active'); }
    }, BLOCK_TYPES.GRASS, true);

    this._buildBlockList('volume-block-palette', (type) => {
      if (type !== 0) this.app.volumeTool.selectedBlock = type;
    }, BLOCK_TYPES.GRASS, false);

    this._buildBlockList('region-block-palette', (type) => {
      if (type !== 0) this.app.regionTool.fillType = type;
    }, BLOCK_TYPES.GRASS, false);

    // 标签切换
    document.querySelectorAll('.tab-bar button').forEach(btn => {
      btn.addEventListener('click', () => {
        document.querySelectorAll('.tab-bar button').forEach(b => b.classList.remove('active'));
        document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
        btn.classList.add('active');
        const tabEl = document.getElementById(`tab-${btn.dataset.tab}`);
        if (tabEl) tabEl.classList.add('active');
        this.app.regionTool.active = false;
        document.getElementById('btn-region-fill').classList.remove('active');
        document.getElementById('btn-region-clear').classList.remove('active');
        this.app.interactionHandler.cancelVolumeHeight();
        this.app.requestGridRender();
      });
    });

    // 笔刷大小
    document.getElementById('brush-size').addEventListener('input', (e) => { this.app.brushTool.size = parseInt(e.target.value); document.getElementById('brush-size-label').textContent = e.target.value; });
    document.getElementById('volume-brush-size').addEventListener('input', (e) => { this.app.volumeTool.size = parseInt(e.target.value); document.getElementById('volume-brush-label').textContent = e.target.value; });

    // 模式切换
    document.getElementById('btn-paint').addEventListener('click', () => {
      this.app.brushTool.mode = 'paint'; document.getElementById('btn-paint').classList.add('active'); document.getElementById('btn-erase').classList.remove('active');
      if (this.app.brushTool.selectedBlock === 0) { this.app.brushTool.selectedBlock = BLOCK_TYPES.GRASS; this._syncList('block-palette', BLOCK_TYPES.GRASS); }
    });
    document.getElementById('btn-erase').addEventListener('click', () => {
      this.app.brushTool.mode = 'erase'; document.getElementById('btn-erase').classList.add('active'); document.getElementById('btn-paint').classList.remove('active');
    });

    // 层滑动条
    const layerSlider = document.getElementById('layer-slider');
    const layerLabel = document.getElementById('layer-label');
    const hideCheckbox = document.getElementById('hide-current-layer');
    layerSlider.addEventListener('input', () => {
      const y = parseInt(layerSlider.value);
      this.app.terrain.currentY = y; layerLabel.textContent = y;
      this.app.threeViewer.setLayerY(y);
      this.app.layerRenderer.fullRedraw(this.app.terrain);
      hideCheckbox.checked = this.app.terrain.isLayerHidden(y);
      this.app.requestGridRender();
    });
    hideCheckbox.checked = this.app.terrain.isLayerHidden(this.app.terrain.currentY);
    hideCheckbox.addEventListener('change', () => {
      const y = this.app.terrain.currentY;
      if (hideCheckbox.checked) this.app.terrain.hideLayer(y); else this.app.terrain.showLayer(y);
      this.app.threeViewer.rebuild(this.app.terrain);
      this.app.requestGridRender();
    });

    // 栅格网开关
    document.getElementById('enhanced-grid').addEventListener('change', (e) => {
      this.app.layerRenderer.enhancedGrid = e.target.checked;
      this.app.requestGridRender();
    });

    // 面板开关按钮
    const btn3D = document.getElementById('btn-toggle-3d');
    const btnGrid = document.getElementById('btn-toggle-grid');
    btn3D.addEventListener('click', () => {
      const vis = this.app.panelManager.toggleViewer();
      btn3D.classList.toggle('hidden', !vis);
      btn3D.textContent = vis ? '👁️ 3D视图' : '👁️ 3D(关)';
    });
    btnGrid.addEventListener('click', () => {
      const vis = this.app.panelManager.toggleGrid();
      btnGrid.classList.toggle('hidden', !vis);
      btnGrid.textContent = vis ? '📐 2D网格' : '📐 2D(关)';
    });

    // 立体取消
    document.getElementById('btn-cancel-volume').addEventListener('click', () => this.app.interactionHandler.cancelVolumeHeight());

    // 区域按钮
    document.getElementById('btn-region-fill').addEventListener('click', () => {
      const btn = document.getElementById('btn-region-fill'); const active = btn.classList.toggle('active');
      document.getElementById('btn-region-clear').classList.remove('active');
      this.app.regionTool.active = active; this.app.regionTool.mode = 'fill';
      this.app.regionTool.startX = -1; this.app.regionTool.startZ = -1;
    });
    document.getElementById('btn-region-clear').addEventListener('click', () => {
      const btn = document.getElementById('btn-region-clear'); const active = btn.classList.toggle('active');
      document.getElementById('btn-region-fill').classList.remove('active');
      this.app.regionTool.active = active; this.app.regionTool.mode = 'clear';
      this.app.regionTool.startX = -1; this.app.regionTool.startZ = -1;
    });

    // 导出
    document.getElementById('btn-export').addEventListener('click', () => this.app.exportJSON());

    // 键盘快捷键
    window.addEventListener('keydown', (e) => {
      if (e.target.tagName === 'INPUT') return;
      const key = e.key.toLowerCase();
      if (key >= '1' && key <= '9') {
        const type = parseInt(key);
        const tab = document.querySelector('.tab-bar button.active')?.dataset?.tab || 'terrain';
        if (tab === 'volume') { this.app.volumeTool.selectedBlock = type; this._syncList('volume-block-palette', type); }
        else if (tab === 'region') { this.app.regionTool.fillType = type; this._syncList('region-block-palette', type); }
        else {
          this.app.brushTool.selectedBlock = type; this.app.brushTool.mode = 'paint';
          document.getElementById('btn-paint').classList.add('active'); document.getElementById('btn-erase').classList.remove('active');
          this._syncList('block-palette', type);
        }
      } else if (key === '0') {
        this.app.brushTool.selectedBlock = 0; this.app.brushTool.mode = 'erase';
        document.getElementById('btn-erase').classList.add('active'); document.getElementById('btn-paint').classList.remove('active');
        this._syncList('block-palette', 0);
      } else if (key === 'e') { this.app.brushTool.mode = 'erase'; document.getElementById('btn-erase').classList.add('active'); document.getElementById('btn-paint').classList.remove('active'); }
      else if (key === 'b') {
        this.app.brushTool.mode = 'paint'; document.getElementById('btn-paint').classList.add('active'); document.getElementById('btn-erase').classList.remove('active');
        if (this.app.brushTool.selectedBlock === 0) { this.app.brushTool.selectedBlock = BLOCK_TYPES.GRASS; this._syncList('block-palette', 1); }
      } else if (key === '[') { const bs = document.getElementById('brush-size'); bs.value = Math.max(1, parseInt(bs.value) - 1); bs.dispatchEvent(new Event('input')); }
      else if (key === ']') { const bs = document.getElementById('brush-size'); bs.value = Math.min(8, parseInt(bs.value) + 1); bs.dispatchEvent(new Event('input')); }
      else if (key === 'escape') {
        if (this.app.interactionHandler.isVolumeHeight) this.app.interactionHandler.cancelVolumeHeight();
        if (this.app.regionTool.active) { this.app.regionTool.active = false; this.app.regionTool.startX = -1; this.app.regionTool.startZ = -1; document.getElementById('btn-region-fill').classList.remove('active'); document.getElementById('btn-region-clear').classList.remove('active'); this.app.requestGridRender(); }
      }
    });
  }

  _buildBlockList(containerId, onClick, defaultSelect, includeBarrier) {
    const c = document.getElementById(containerId); if (!c) return; c.innerHTML = '';
    const types = includeBarrier ? [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] : [1, 2, 3, 4, 5, 6, 7, 8, 9];
    for (const t of types) {
      const item = document.createElement('div');
      item.className = 'block-list-item'; item.dataset.type = t;
      const cssColor = BLOCK_COLORS_CSS[t];
      const swatchStyle = cssColor ? `background:${cssColor}` : 'background:transparent;border:1px dashed #555';
      const displayName = BLOCK_NAMES[t];
      const cnHint = t === 0 ? '空气' : '';
      item.innerHTML = `<div class="block-list-swatch" style="${swatchStyle}"></div><span class="block-list-name">${displayName}${cnHint ? ` <small>${cnHint}</small>` : ''}</span>`;
      item.addEventListener('click', () => { c.querySelectorAll('.block-list-item').forEach(i => i.classList.remove('selected')); item.classList.add('selected'); onClick(t); });
      c.appendChild(item);
    }
    const sel = c.querySelector(`[data-type="${defaultSelect}"]`);
    if (sel) sel.classList.add('selected');
  }

  _syncList(containerId, type) {
    const c = document.getElementById(containerId);
    if (!c) return;
    c.querySelectorAll('.block-list-item').forEach(i => i.classList.toggle('selected', parseInt(i.dataset.type) === type));
  }
}

// ===== App =====
class App {
  constructor() {
    this.terrain = new TerrainData();
    this.camera2D = new Camera2D();

    // 面板管理
    this.panelManager = new PanelManager();

    const gridCanvas = document.getElementById('grid-canvas');
    this.layerRenderer = new LayerRenderer(gridCanvas);
    this.layerRenderer.fullRedraw(this.terrain);

    this.brushTool = new BrushTool();
    this.volumeTool = new VolumeTool();
    this.regionTool = new RegionTool();

    this.threeViewer = new ThreeViewer(document.getElementById('viewer-container'));
    this.interactionHandler = new InteractionHandler(gridCanvas, this.camera2D, this);
    this.uiManager = new UIManager(this);

    this.threeViewer.rebuild(this.terrain);
    this.threeViewer.setLayerY(this.terrain.currentY);

    this._resizeGrid = this._resizeGrid.bind(this);
    window.addEventListener('resize', this._resizeGrid);
    this._resizeGrid();
    this._initialCameraFit();
    this.requestGridRender();

    // 暴露到全局供PanelManager使用
    window._app = this;
  }

  _resizeGrid() {
    const gc = document.getElementById('grid-container');
    const canvas = document.getElementById('grid-canvas');
    const cw = gc.clientWidth, ch = gc.clientHeight;
    const dpr = Math.min(window.devicePixelRatio || 1, 2);
    // Set internal resolution matching CSS display size exactly (no stretch)
    canvas.width = cw;
    canvas.height = ch;
    // Use CSS px to lock exact size, preventing any %-based stretch
    canvas.style.width = cw + 'px';
    canvas.style.height = ch + 'px';
    this.threeViewer.resize();
    if (!this._initialFitDone) { this.camera2D.fitToContainer(cw, ch); this._initialFitDone = true; }
    this.requestGridRender();
  }
  _initialCameraFit() {
    const gc = document.getElementById('grid-container');
    this.camera2D.fitToContainer(gc.clientWidth, gc.clientHeight);
    this._initialFitDone = true;
  }

  _refitGrid() {
    const gc = document.getElementById('grid-container');
    const canvas = document.getElementById('grid-canvas');
    const cw = gc.clientWidth, ch = gc.clientHeight;
    if (cw > 0 && ch > 0) {
      canvas.width = cw; canvas.height = ch;
      canvas.style.width = cw + 'px'; canvas.style.height = ch + 'px';
      this.camera2D.fitToContainer(cw, ch);
      this.layerRenderer.fullRedraw(this.terrain);
    }
  }

  onTerrainEdit(rect) {
    this.layerRenderer.partialRedraw(this.terrain, rect.x1, rect.z1, rect.x2, rect.z2);
    this.requestGridRender();
    this.threeViewer.scheduleRebuild(this.terrain);
  }

  requestGridRender() {
    if (this._renderScheduled) return;
    this._renderScheduled = true;
    requestAnimationFrame(() => {
      this._renderScheduled = false;
      const canvas = document.getElementById('grid-canvas');
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      this.layerRenderer.render(this.camera2D, this.terrain);

      if (this.regionTool.active && this.regionTool.startX >= 0) {
        const r = this.regionTool.getRect();
        const tl = this.camera2D.worldToScreen(r.x1, r.z1);
        const br = this.camera2D.worldToScreen(r.x2 + 1, r.z2 + 1);
        ctx.fillStyle = 'rgba(137,180,250,0.2)'; ctx.fillRect(tl.x, tl.y, br.x - tl.x, br.y - tl.y);
        ctx.strokeStyle = 'rgba(137,180,250,0.8)'; ctx.lineWidth = 2; ctx.strokeRect(tl.x, tl.y, br.x - tl.x, br.y - tl.y);
      }

      if (this.volumeTool.hasFootprint() && this.volumeTool.state === 'height') {
        const f = this.volumeTool.footprint;
        const tl = this.camera2D.worldToScreen(f.x1, f.z1);
        const br = this.camera2D.worldToScreen(f.x2 + 1, f.z2 + 1);
        ctx.fillStyle = 'rgba(249,226,175,0.25)'; ctx.fillRect(tl.x, tl.y, br.x - tl.x, br.y - tl.y);
        ctx.strokeStyle = 'rgba(249,226,175,0.9)'; ctx.lineWidth = 2;
        ctx.setLineDash([4, 2]); ctx.strokeRect(tl.x, tl.y, br.x - tl.x, br.y - tl.y); ctx.setLineDash([]);
        const yr = this.volumeTool.getYRange(this.terrain.currentY);
        ctx.fillStyle = '#f9e2af'; ctx.font = 'bold 12px "Segoe UI"';
        ctx.fillText(`Y[${yr.y1}..${yr.y2}]`, tl.x, tl.y - 6);
      }

      if (this.volumeTool.hasFootprint() && this.volumeTool.state === 'paint') {
        const f = this.volumeTool.footprint;
        const tl = this.camera2D.worldToScreen(f.x1, f.z1);
        const br = this.camera2D.worldToScreen(f.x2 + 1, f.z2 + 1);
        ctx.strokeStyle = 'rgba(249,226,175,0.5)'; ctx.lineWidth = 1;
        ctx.setLineDash([2, 4]); ctx.strokeRect(tl.x, tl.y, br.x - tl.x, br.y - tl.y); ctx.setLineDash([]);
      }

      document.getElementById('zoom-indicator').textContent = `缩放: ${Math.round(this.camera2D.zoom * 100)}%`;
    });
  }

  exportJSON() {
    const progress = document.getElementById('export-progress');
    progress.style.display = 'block'; progress.textContent = '正在导出...';
    setTimeout(() => {
      try {
        const data = this.terrain.exportNonAir();
        const json = JSON.stringify(data);
        const blob = new Blob([json], { type: 'application/json' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a'); a.href = url;
        a.download = `terrain-${new Date().toISOString().replace(/[:.]/g,'-').slice(0,19)}.json`;
        a.click(); URL.revokeObjectURL(url);
        progress.textContent = `✅ 完成! ${data.length.toLocaleString()} 个方块已导出`;
        setTimeout(() => { progress.style.display = 'none'; }, 3000);
      } catch (err) {
        progress.textContent = `❌ 导出失败: ${err.message}`;
        setTimeout(() => { progress.style.display = 'none'; }, 3000);
      }
    }, 50);
  }
}

// ===== 启动 =====
const app = new App();
console.log('🏔️ 地形编辑器已就绪');
console.log(`   尺寸: ${SIZE_X}×${SIZE_Y}×${SIZE_Z}`);
console.log('   方块: barrier grass stone dirt white sand green_leaf wood white_light rock');
console.log('   标签: 地形 | 立体 | 区域');
console.log('   快捷键: 1-9选方块 0=barrier B/E=绘制/删除 []=笔刷 Esc=取消');
</script>

</body>
</html>
