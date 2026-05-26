# 书签管理页面网格卡片化重构设计规格书

本文档描述了将 KunTab 的书签管理页面从传统的“表格列表”视图重构为现代化“网格卡片（Grid Cards）”视图的设计与实现方案，以百分百还原用户设计图中的视觉效果。

## 1. 目标与背景

当前的书签管理页面使用类似传统文件管理器的表格视图（包含复选框、标题、链接、所属文件夹、创建时间、操作等列）。这种设计较为生硬，缺乏视觉冲击力，且在开启全屏背景图时不够精致。
本重构方案将书签管理页重构为：
- 左侧悬浮折叠文件夹侧边栏。
- 右侧主内容区包含顶部标签导航（全部/网页/文件夹）与右上角搜索框/管理操作。
- 扁平与层级混合的现代化网格卡片展示，支持文件夹下钻导航。
- 采用精致的“实体悬浮卡片风（Elevated Solid Card）”视觉样式和流畅的微动效。

## 2. 方案设计

### 2.1 页面布局与 DOM 结构 (`App.tsx`)

书签管理页面 (`activeTab === 'bookmarks'`) 的 DOM 结构改造如下：

```tsx
<section className="bookmark-page">
  <div className="bookmark-layout">
    {/* 左侧侧边栏 - 文件夹树 */}
    <aside className="folder-tree">
      <div className="folder-head">
        <div className="folder-head-left">
          <Folder size={16} />
          <span>所有书签</span>
        </div>
      </div>
      <div className="folder-list-scroll">
        <div className={selectedFolderId === '0' ? 'folder-row active' : 'folder-row'}>
          <button className="folder-select" onClick={() => setSelectedFolderId('0')}>
            <div className="folder-label-wrap">
              <Bookmark size={15} />
              <span>全部书签</span>
            </div>
            <span className="count-badge">{allBookmarks.length}</span>
          </button>
        </div>
        <FolderTree
          nodes={folderTree}
          selectedId={selectedFolderId}
          onSelect={setSelectedFolderId}
          onDelete={onDeleteFolder}
          deleteTitle={text.deleteFolder}
        />
      </div>
      {/* 新建文件夹按钮移至侧边栏底部 */}
      <div className="folder-sidebar-footer">
        <button className="folder-add-footer-btn" onClick={onCreateFolder}>
          <Plus size={16} />
          <span>新建文件夹</span>
        </button>
      </div>
    </aside>

    {/* 右侧主内容区 */}
    <div className="bookmark-main-content">
      {/* 顶部工具栏：左侧为过滤标签页，右侧为搜索框和导入导出按钮 */}
      <div className="bookmark-top-bar">
        <div className="bookmark-tabs">
          <button
            className={subTab === 'all' ? 'tab-item active' : 'tab-item'}
            onClick={() => setSubTab('all')}
          >
            全部
          </button>
          <button
            className={subTab === 'web' ? 'tab-item active' : 'tab-item'}
            onClick={() => setSubTab('web')}
          >
            网页
          </button>
          <button
            className={subTab === 'folder' ? 'tab-item active' : 'tab-item'}
            onClick={() => setSubTab('folder')}
          >
            文件夹
          </button>
        </div>

        <div className="bookmark-top-actions">
          <div className="bookmark-search-wrap">
            <Search size={16} />
            <input
              value={bookmarkQuery}
              onChange={(e) => setBookmarkQuery(e.target.value)}
              placeholder="搜索当前文件夹下的书签..."
            />
          </div>
          <button
            className="icon-btn"
            onClick={() => setActiveTab('backup')}
            title="导入 / 导出"
          >
            <CircleArrowDown size={16} />
          </button>
        </div>
      </div>

      {/* 网格展示区 */}
      <div className="bookmark-grid">
        {displayItems.map((item) => (
          item.isFolder ? (
            <FolderCard key={item.id} folder={item} onClick={() => setSelectedFolderId(item.id)} />
          ) : (
            <BookmarkCard key={item.id} bookmark={item} />
          )
        ))}
      </div>

      {/* 底部页脚 */}
      <div className="bookmark-footer">
        <div className="total-count">共 {filteredTotalCount} 个书签</div>
        {totalPages > 1 && (
          <div className="pagination-controls">
            {/* 分页控制按钮 */}
          </div>
        )}
      </div>
    </div>
  </div>
</section>
```

### 2.2 状态管理与数据流逻辑

1. **子标签页过滤逻辑 (`subTab`)**：
   - 默认选择 `'all'`。
   - 当 `selectedFolderId === '0'` (根目录) 时：
     - `'all'` & `'web'`：返回所有网页书签的扁平列表（递归获取）。
     - `'folder'`：返回第一级直接子文件夹。
   - 当 `selectedFolderId !== '0'` (常规文件夹) 时：
     - `'all'`：展示该文件夹下的直接子文件夹 + 直接网页书签。
     - `'web'`：仅展示该文件夹下的直接网页书签。
     - `'folder'`：仅展示该文件夹下的直接子文件夹。

2. **模糊搜索关联**：
   - 输入搜索内容 `bookmarkQuery` 时，同样在此过滤后的数据集上进行标题或 URL 匹配过滤。

3. **分页状态关联**：
   - 默认 `pageSize` 设为 `20`，配合网格展示。
   - 当切换 `selectedFolderId` 或 `subTab` 或输入 `bookmarkQuery` 时，重置 `currentPage = 1`。

### 2.3 样式设计与精美视觉规范 (`newtab.css`)

在 `newtab.css` 中重构 `.bookmark-page` 及相关子项：

1. **左侧侧边栏 (`.folder-tree`)**
   - 优化为类似独立悬浮板：背景白色不透明度 `0.9`，阴影柔和，底部直角改为圆角。
   - 底部新建文件夹按钮 (`.folder-sidebar-footer`) 固定在侧边栏底端，使用 `border-top` 分割，带有一键悬浮效果。

2. **主内容顶部工具条 (`.bookmark-top-bar`)**
   - 使用 `display: flex; justify-content: space-between; align-items: center;`。
   - 标签页按钮 (`.tab-item`)：扁平灰色字悬停变黑，选中时添加下滑蓝色底线过渡（`border-bottom: 2px solid var(--primary)`，添加 `transition`）。
   - 右侧搜索框与按钮紧凑并列。

3. **网格卡片 (`.bookmark-grid` & `.bookmark-card` & `.folder-card`)**
   - 网格使用 4 列响应式自适应布局。
   - **卡片容器**：
     - 圆角 `border-radius: var(--radius-lg)` (16px)。
     - 亮色模式背景：`background: #ffffff`，边框 `1px solid rgba(15, 23, 42, 0.04)`，阴影 `0 4px 12px rgba(15, 23, 42, 0.02)`。
     - 悬停：`transform: translateY(-4px)`，边框色过渡至 `var(--primary)`，阴影变为 `0 12px 24px rgba(15, 23, 42, 0.06)`。
   - **内容排版**：
     - 图标容器：位于卡片左上角，高宽为 `2.5rem`。网站图标使用 `faviconOf(url)`，文件夹图标使用半透明主色背景加 Lucide `Folder`。
     - 三点菜单：位于卡片右上角，点击可展开操作菜单（位置设为绝对定位，相对于卡片右侧）。
     - 标题：在图标下方，字号 `0.95rem`，加粗，防止折行溢出。
     - Subtext：在标题下方，字号 `0.8rem`，颜色为 `var(--muted)`，若是网站则展示 `hostnameOf(url)`，若是文件夹则展示 `X 个书签`。

## 3. 修改文件范围

- [App.tsx](file:///Users/quentin/Documents/me/code-open/bookmark-ai/entrypoints/newtab/App.tsx)
- [newtab.css](file:///Users/quentin/Documents/me/code-open/bookmark-ai/entrypoints/newtab/newtab.css)

## 4. 验证方案

### 4.1 自动构建验证
- 在终端运行 `npm run compile` 和 `npm run build`，确认代码编译无任何 TypeScript 或打包错误。

### 4.2 手动测试用例
1. **文件夹树切换与点击**：点击左侧“所有书签”或不同的文件夹，确认右侧的网页卡片和文件夹卡片更新正确。
2. **标签过滤功能**：在有子文件夹和网页书签混合的文件夹内，点击“网页”应只显示网页卡片，点击“文件夹”只显示文件夹卡片，点击“全部”混合显示。
3. **文件夹双击下钻**：在右侧网格中点击文件夹卡片，确认能成功跳转并将左侧对应的文件夹激活，视图切换为该子文件夹内部。
4. **编辑与删除书签**：点击网页卡片右上角三点菜单，选择“编辑”或“删除”或“设为常用”，校验各弹窗和操作回调是否能完全工作。
5. **暗色模式适配**：切换到深色模式，确认网格卡片的背景对比度、高亮框与文字可读性依然保持极高水准。
