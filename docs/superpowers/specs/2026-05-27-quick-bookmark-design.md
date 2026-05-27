# KunTab Quick Bookmark Popup Spec

## Goal

Provide a fast, elegant, and searchable bookmarking interface via the extension popup (`entrypoints/popup`). When a user clicks the extension's icon in the browser, they can quickly save the current webpage to any folder by typing to search for the target folder. It also detects if the current page is already bookmarked and allows modifying or deleting it.

## User Interface & Design

The popup page will match the look-and-feel of KunTab:
*   **Dimensions**: Width 380px, height auto (max 480px) to prevent vertical scrollbars on the popup container itself.
*   **Theme Integration**: Supports Light/Dark themes and follows KunTab's glassmorphism style sheet patterns.
*   **Form Controls**:
    1.  **Title input**: Editable text input filled with the active tab's title.
    2.  **Folder Searchable Selector**:
        *   An autocomplete search box showing folder paths (e.g., `全部书签 / 书签栏 / 开发`).
        *   Typing filters options.
        *   Supports a special list item: `+ 新建文件夹 "搜索关键字"` to create a new folder under the parent and save the bookmark there.
    3.  **Actions**:
        *   **Save/Edit**: Primary button to add or update the bookmark. Also triggered on pressing "Enter" in input fields.
        *   **Delete**: Red text button displayed only if the bookmark already exists, letting users delete it instantly.
        *   **Open KunTab Link**: Small secondary button linking to `chrome://newtab` to open the full KunTab dashboard.

## Technical Architecture

### 1. Active Tab Retrieval
Get the active page's URL and title using WXT / Chrome standard API:
```typescript
const [activeTab] = await chrome.tabs.query({ active: true, currentWindow: true });
const title = activeTab?.title || '';
const url = activeTab?.url || '';
```

### 2. Duplicate Detection
Check if the current URL exists in browser bookmarks:
```typescript
const existing = await chrome.bookmarks.search({ url });
const isBookmarked = existing.length > 0;
```
If multiple are found, select the first one as the active edit target.

### 3. Folder Querying & Hierarchy flattening
Fetch folder list using existing helpers in `entrypoints/newtab/lib/bookmarks.ts` (`getBookmarkTree`, `buildFolderTree`, `flattenFolderOptions`).
Flatten folders to format:
```typescript
interface FolderOption {
  id: string;
  label: string; // "全部书签 / 书签栏 / subfolder"
  count: number;
}
```

### 4. Create / Update / Delete API Calls
- **Create**:
  ```typescript
  await chrome.bookmarks.create({ parentId, title, url });
  ```
- **Update**:
  ```typescript
  await chrome.bookmarks.update(bookmarkId, { title });
  await chrome.bookmarks.move(bookmarkId, { parentId });
  ```
- **Delete**:
  ```typescript
  await chrome.bookmarks.remove(bookmarkId);
  ```

## File Changes & Modifications

- `[MODIFY] entrypoints/popup/App.tsx`: Replace the placeholder text/button with the full bookmark search & save form.
- `[MODIFY] entrypoints/popup/App.css`: Style the popup using variables and design tokens from KunTab.
- `[MODIFY] entrypoints/popup/main.tsx`: Standard initialization check.
- `[MODIFY] entrypoints/popup/style.css`: Minimal container styles.
