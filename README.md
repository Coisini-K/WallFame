# 个人荣誉墙

一个使用 Vue 3 + Vite 构建的静态荣誉展示站，适合部署到 GitHub Pages。

## 本地运行

```bash
npm install
npm run dev
```

## 添加一项荣誉

1. 把证书图片统一放进 `public/honors/files`。建议使用简短英文文件名。
2. 在 `src/data/honors.js` 数组开头添加一条记录，复制已有记录修改即可。
3. 执行 `npm run dev` 检查，提交并推送到 GitHub。

图片记录不需要 `cover` 字段；PDF 记录建议将首页导出为 JPG 放进 `public/honors/covers`，并填写 `cover` 字段。

## 删除一项荣誉

从 `src/data/honors.js` 删除对应记录，再删除 `public/honors` 中对应文件即可。

## 部署到 GitHub Pages

1. 在 GitHub 新建仓库，并把本项目推送到 `main` 分支。
2. 打开仓库 `Settings → Pages`。
3. 在 `Build and deployment` 的 `Source` 中选择 `GitHub Actions`。
4. 推送后等待 `Deploy to GitHub Pages` 工作流完成。

站点使用相对资源路径，因此项目仓库名可以任意设置。
