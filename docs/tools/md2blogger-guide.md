---
title: .md檔案轉html小工具
tags:
  - Tools
---
# 🚀 Node.js 版本

## 1. 安裝套件

``` bash
mkdir md2blogger
cd md2blogger
npm init -y
npm install markdown-it fs-extra
```

## 2. 建立 convert.js

``` js
// convert.js
const fs = require("fs-extra");
const MarkdownIt = require("markdown-it");

// 初始化 markdown-it
const md = new MarkdownIt({
  html: true,
  linkify: true,
  typographer: true
});

// 讀取 md 檔案
const inputFile = process.argv[2]; // 輸入檔
const outputFile = inputFile.replace(/\.md$/, ".html"); // 輸出檔

if (!inputFile) {
  console.error("請提供要轉換的 Markdown 檔案，例如: node convert.js article.md");
  process.exit(1);
}

(async () => {
  try {
    const content = await fs.readFile(inputFile, "utf8");
    const result = md.render(content);

    // 包裹成 Blogger 可貼格式（文章 HTML）
    const bloggerHTML = `
<div class="post-content">
${result}
</div>
`;

    await fs.writeFile(outputFile, bloggerHTML, "utf8");
    console.log(`✅ 已轉換完成: ${outputFile}`);
  } catch (err) {
    console.error("轉換失敗:", err);
  }
})();
```

## 3. 使用方式

``` bash
node convert.js my-article.md
```

會生成 `my-article.html`，你打開後整份貼到 Blogger HTML
編輯模式就能用了。
