# r2modmanPlus 專案架構

本文件概述了 `r2modmanPlus` 專案的架構與組織結構。

## 技術棧 (Technology Stack)

- **框架**: 基於 **Vue 3** 的 [Quasar Framework (v2)](https://quasar.dev/)。
- **桌面環境**: 使用 [Electron](https://www.electronjs.org/) 提供跨平台桌面支持。
- **語言**: 使用 [TypeScript](https://www.typescriptlang.org/) 確保類型安全並提升開發體驗。
- **狀態管理**: 使用 [Vuex](https://vuex.vuejs.org/) 進行集中式應用程式狀態管理。
- **路由**: 使用 [vue-router](https://router.vuejs.org/) 進行頁面導覽。
- **資料庫**: 使用 [Dexie.js](https://dexie.org/) 作為 IndexedDB 的封裝層，實現本地資料持久化。

## 專案結構 (Project Structure)

- `src/`: 包含渲染進程（Vue.js 應用程式）。
  - `components/`: 可重複使用的 Vue 組件。
  - `pages/`: 用於路由的頂層 Vue 視圖。
  - `store/`: 用於管理狀態的 Vuex 模組。
  - `router/`: 應用程式路由配置。
  - `model/`: Mod、設定檔 (Profiles)、遊戲等的資料模型與類型定義。
  - `installers/`: 安裝不同類型 Mod 的邏輯（例如：BepInEx, MelonLoader）。
  - `r2mm/`: 特定於 r2modman 的核心業務邏輯。
  - `depots/`: 遊戲特定配置與 Depot 資訊。
  - `providers/`: 外部服務整合（例如：Thunderstore API）。
  - `i18n/`: 多國語言翻譯檔案。
- `src-electron/`: 包含 Electron 主進程邏輯。
  - `electron-main.ts`: 主進程入口點。
  - `electron-preload.ts`: 用於橋接主進程與渲染進程的預載腳本。
  - `ipc/`: 自定義 IPC 通訊邏輯。
  - `preload/`: 透過 `contextBridge` 暴露給渲染進程的 API 定義。
- `public/`: 靜態資源與輔助腳本。
- `test/`: 測試套件，包含用於單元測試與組件測試的 Vitest。

## 關鍵架構模式 (Key Architecture Patterns)

### IPC 通訊

專案在 `src-electron/ipc` 和 `src-electron/preload` 中使用了一個精細的橋接層。在 Vue 組件中不直接使用 `ipcRenderer`，而是將核心 Node.js 模組（如 `fs`, `path`, `child_process`）和自定義工具（如 `zip`）透過 `contextBridge` 暴露給渲染進程。這使得 Vue 應用程式能夠安全且高效地與檔案系統進行互動。

### 模組化 Mod 安裝程式 (Modular Mod Installers)

Mod 安裝邏輯在 `src/installers/` 中高度模組化。每個 Mod 加載器（例如：BepInEx, MelonLoader, GDWeave）都有其專屬的安裝程式類別，並實作通用的介面。`registry.ts` 檔案作為工廠類，根據套件的中繼資料 (Metadata) 選擇並實例化合適的安裝程式。

### 多遊戲支持 (Multi-Game Support)

遊戲特定的配置透過 `src/model/game/` 中的模型進行管理。應用程式設計具備擴展性，允許透過在 `src/depots/` 和 `src/model/game/` 目錄中定義特定的目錄結構、啟動參數和 Mod 加載器，來新增對新遊戲的支持。

## 開發與構建 (Development & Build)

- **Quasar CLI**: 用於開發、構建與打包應用程式。
- **Yarn**: 主要的套件管理器。
- **Vitest**: 用於單元測試與整合測試。
