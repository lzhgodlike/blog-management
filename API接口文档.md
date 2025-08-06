# Hexo博客管理系统API接口文档

## 基础信息
- **Base URL**: `http://localhost:8080/api`
- **Content-Type**: `application/json`
- **认证方式**: JWT Token (Header: `Authorization: Bearer {token}`)
- **字符编码**: UTF-8

---

## 1. 用户认证模块

### 1.1 用户登录
```http
POST /auth/login
```

**请求参数:**
```json
{
  "username": "admin",
  "password": "password123"
}
```

**响应示例:**
```json
{
  "code": 200,
  "message": "登录成功",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 7200,
    "username": "admin"
  }
}
```

### 1.2 验证Token
```http
POST /auth/verify
```

**响应示例:**
```json
{
  "code": 200,
  "message": "Token有效",
  "data": {
    "username": "admin",
    "role": "admin",
    "expiresAt": "2024-01-01T18:00:00Z"
  }
}
```

### 1.3 刷新Token
```http
POST /auth/refresh
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "token": "new_jwt_token",
    "expiresIn": 7200
  }
}
```

---

## 2. 站点信息模块

### 2.1 获取站点配置
```http
GET /site/config
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "title": "我的博客",
    "subtitle": "记录生活点滴",
    "description": "这是一个基于Hexo的个人博客",
    "author": "张三",
    "language": "zh-CN",
    "timezone": "Asia/Shanghai",
    "url": "https://myblog.com",
    "theme": "anzhiyu",
    "deploy": {
      "type": "git",
      "repo": "https://github.com/username/username.github.io.git"
    }
  }
}
```

### 2.2 更新站点配置
```http
PUT /site/config
```

**请求参数:**
```json
{
  "title": "我的新博客",
  "subtitle": "更新的副标题",
  "description": "新的描述",
  "author": "李四",
  "language": "zh-CN"
}
```

### 2.3 获取目录结构
```http
GET /site/structure
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "source": {
      "_posts": {
        "type": "directory",
        "children": ["25080601.md", "25080602.md"],
        "count": 2
      },
      "images": {
        "type": "directory", 
        "children": ["avatar.jpg", "banner.png"],
        "count": 2
      },
      "_drafts": {
        "type": "directory",
        "children": ["draft-post.md"],
        "count": 1
      }
    },
    "themes": {
      "anzhiyu": {
        "type": "directory",
        "version": "1.0.0"
      }
    }
  }
}
```

---

## 3. 文章管理模块

### 3.1 获取文章列表
```http
GET /posts?page=1&size=10&category=技术&tag=Java&keyword=搜索关键词&status=published&sortBy=date&sortOrder=desc
```

**查询参数:**
- `page`: 页码 (默认1)
- `size`: 每页数量 (默认10)
- `category`: 分类筛选
- `tag`: 标签筛选
- `keyword`: 关键词搜索
- `status`: 状态筛选 (published/draft/all)
- `sortBy`: 排序字段 (date/title/updated)
- `sortOrder`: 排序方向 (asc/desc)

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "total": 25,
    "page": 1,
    "size": 10,
    "totalPages": 3,
    "posts": [
      {
        "id": "25080601",
        "title": "Hello World",
        "date": "2025-08-06 10:00:00",
        "updated": "2025-08-06 15:30:00",
        "categories": ["技术"],
        "tags": ["Hexo", "博客"],
        "excerpt": "这是文章摘要...",
        "status": "published",
        "filename": "25080601.md",
        "wordCount": 1200,
        "readTime": "5 min",
        "cover": "/images/25080601-cover.jpg",
        "displayName": "2025年8月6日第1篇文章"
      }
    ]
  }
}
```

### 3.2 获取文章详情
```http
GET /posts/{id}
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "id": "25080601",
    "frontMatter": {
      "title": "Hello World",
      "date": "2025-08-06 10:00:00",
      "updated": "2025-08-06 15:30:00",
      "categories": ["技术"],
      "tags": ["Hexo", "博客"],
      "cover": "/images/25080601-cover.jpg",
      "top_img": "/images/25080601-top.jpg",
      "description": "文章描述",
      "aside": true,
      "comments": true,
      "copyright": true,
      "copyright_author": "张三",
      "keywords": "Hexo,博客,技术"
    },
    "content": "# Hello World\n\n这是我的第一篇文章...",
    "rawContent": "---\ntitle: Hello World\ndate: 2025-08-06 10:00:00\n---\n\n# Hello World\n\n这是我的第一篇文章...",
    "htmlContent": "<h1>Hello World</h1><p>这是我的第一篇文章...</p>",
    "wordCount": 150,
    "readTime": "2 min",
    "filename": "25080601.md",
    "path": "source/_posts/25080601.md",
    "displayName": "2025年8月6日第1篇文章"
  }
}
```

### 3.3 创建文章
```http
POST /posts
```

**请求参数:**
```json
{
  "title": "新文章标题",
  "categories": ["技术", "前端"],
  "tags": ["Vue", "JavaScript"],
  "cover": "/images/new-cover.jpg",
  "top_img": "/images/top.jpg",
  "description": "文章描述",
  "keywords": "Vue,JavaScript,前端",
  "content": "# 新文章\n\n文章内容...",
  "status": "draft",
  "aside": true,
  "comments": true,
  "copyright": true,
  "layout": "post"
}
```

**响应示例:**
```json
{
  "code": 200,
  "message": "文章创建成功",
  "data": {
    "id": "25080601",
    "filename": "25080601.md",
    "path": "source/_posts/25080601.md",
    "displayName": "2025年8月6日第1篇文章"
  }
}
```

### 3.4 更新文章
```http
PUT /posts/{id}
```

**请求参数:** (同创建文章)

### 3.5 删除文章
```http
DELETE /posts/{id}
```

**响应示例:**
```json
{
  "code": 200,
  "message": "文章删除成功"
}
```

### 3.6 批量操作文章
```http
POST /posts/batch
```

**请求参数:**
```json
{
  "action": "delete", // delete, publish, draft, updateCategory, updateTag
  "postIds": ["25080601", "25080602", "25080701"],
  "data": {
    "category": "新分类", // 仅在updateCategory时需要
    "tags": ["新标签1", "新标签2"] // 仅在updateTag时需要
  }
}
```

### 3.7 文章搜索
```http
POST /posts/search
```

**请求参数:**
```json
{
  "keyword": "搜索关键词",
  "searchIn": ["title", "content", "tags", "categories"],
  "fuzzy": true,
  "page": 1,
  "size": 10
}
```

### 3.8 获取下一个文章编号
```http
GET /posts/next-id?date=2025-08-06
```

**查询参数:**
- `date`: 指定日期 (格式: YYYY-MM-DD, 可选，默认为当前日期)

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "nextId": "25080603",
    "date": "2025-08-06",
    "sequenceNumber": 3,
    "displayName": "2025年8月6日第3篇文章",
    "todayCount": 2,
    "filename": "25080603.md"
  }
}
```

**文件名规则说明:**
- 格式: `YYMMDD + 两位序号`
- 示例: `25080601` = 2025年8月6日第1篇
- 自动递增当日序号，最大支持99篇/天

---

## 4. 分类和标签管理

### 4.1 获取所有分类
```http
GET /categories
```

**响应示例:**
```json
{
  "code": 200,
  "data": [
    {
      "name": "技术",
      "count": 15,
      "posts": ["25080601", "25080602"],
      "slug": "tech",
      "parent": null,
      "children": ["前端", "后端"]
    },
    {
      "name": "生活",
      "count": 8,
      "posts": ["25080603", "25080701"],
      "slug": "life",
      "parent": null,
      "children": []
    }
  ]
}
```

### 4.2 获取所有标签
```http
GET /tags
```

**响应示例:**
```json
{
  "code": 200,
  "data": [
    {
      "name": "JavaScript",
      "count": 12,
      "posts": ["25080601", "25080605"],
      "slug": "javascript",
      "color": "#f7df1e"
    },
    {
      "name": "Vue",
      "count": 8,
      "posts": ["25080602", "25080606"],
      "slug": "vue",
      "color": "#4fc08d"
    }
  ]
}
```

### 4.3 创建分类
```http
POST /categories
```

**请求参数:**
```json
{
  "name": "新分类",
  "parent": "技术",
  "description": "分类描述"
}
```

### 4.4 创建标签
```http
POST /tags
```

**请求参数:**
```json
{
  "name": "新标签",
  "color": "#ff6b6b",
  "description": "标签描述"
}
```

### 4.5 重命名分类/标签
```http
PUT /categories/{name}
PUT /tags/{name}
```

**请求参数:**
```json
{
  "newName": "新名称",
  "color": "#ff6b6b", // 仅标签
  "description": "新描述"
}
```

### 4.6 删除分类/标签
```http
DELETE /categories/{name}
DELETE /tags/{name}
```

---

## 5. 文件管理模块

### 5.1 获取文件内容
```http
GET /files?path=source/_posts/hello-world.md
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "path": "source/_posts/25080601.md",
    "content": "文件原始内容",
    "encoding": "utf-8",
    "size": 1024,
    "lastModified": "2025-08-06T10:00:00Z",
    "isReadonly": false
  }
}
```

### 5.2 保存文件内容
```http
PUT /files
```

**请求参数:**
```json
{
  "path": "source/_posts/25080601.md",
  "content": "更新的文件内容",
  "encoding": "utf-8",
  "backup": true
}
```

### 5.3 获取文件列表
```http
GET /files/list?directory=source/images&page=1&size=20&type=all
```

**查询参数:**
- `directory`: 目录路径
- `page`: 页码
- `size`: 每页数量
- `type`: 文件类型过滤 (all/image/markdown/text)

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "currentPath": "source/images",
    "parentPath": "source",
    "total": 25,
    "page": 1,
    "files": [
      {
        "name": "avatar.jpg",
        "type": "file",
        "size": 51200,
        "lastModified": "2024-01-01T10:00:00Z",
        "url": "/images/avatar.jpg",
        "mimeType": "image/jpeg",
        "isImage": true,
        "dimensions": "300x300"
      },
      {
        "name": "covers",
        "type": "directory",
        "size": 0,
        "lastModified": "2024-01-01T10:00:00Z",
        "childCount": 5
      }
    ]
  }
}
```

### 5.4 创建目录
```http
POST /files/directory
```

**请求参数:**
```json
{
  "path": "source/images/new-folder",
  "recursive": true
}
```

### 5.5 删除文件/目录
```http
DELETE /files?path=source/images/old-image.jpg&force=false
```

### 5.6 重命名文件/目录
```http
PUT /files/rename
```

**请求参数:**
```json
{
  "oldPath": "source/images/old-name.jpg",
  "newPath": "source/images/new-name.jpg"
}
```

### 5.7 复制文件
```http
POST /files/copy
```

**请求参数:**
```json
{
  "sourcePath": "source/images/image.jpg",
  "targetPath": "source/images/backup/image.jpg",
  "overwrite": false
}
```

---

## 6. 媒体管理模块

### 6.1 上传图片
```http
POST /media/upload
Content-Type: multipart/form-data
```

**请求参数:**
- `file`: 图片文件 (必需)
- `path`: 保存路径 (可选，默认为images)
- `compress`: 是否压缩 (可选，默认true)
- `quality`: 压缩质量 (可选，默认80)
- `maxWidth`: 最大宽度 (可选)
- `watermark`: 是否添加水印 (可选)

**响应示例:**
```json
{
  "code": 200,
  "message": "上传成功",
  "data": {
    "filename": "image-20240101-001.jpg",
    "originalName": "my-photo.jpg",
    "path": "/images/image-20240101-001.jpg",
    "url": "http://localhost:4000/images/image-20240101-001.jpg",
    "size": 102400,
    "originalSize": 204800,
    "dimensions": "800x600",
    "mimeType": "image/jpeg"
  }
}
```

### 6.2 批量上传图片
```http
POST /media/upload/batch
Content-Type: multipart/form-data
```

**请求参数:**
- `files[]`: 多个图片文件
- `path`: 保存路径 (可选)
- `compress`: 是否压缩 (可选)

**响应示例:**
```json
{
  "code": 200,
  "message": "批量上传完成",
  "data": {
    "total": 3,
    "success": [
      {
        "filename": "image1.jpg",
        "url": "/images/image1.jpg",
        "size": 51200
      },
      {
        "filename": "image2.jpg", 
        "url": "/images/image2.jpg",
        "size": 61200
      }
    ],
    "failed": [
      {
        "filename": "image3.gif",
        "error": "不支持的文件格式"
      }
    ]
  }
}
```

### 6.3 获取媒体库
```http
GET /media?type=image&page=1&size=20&keyword=&sortBy=uploadDate&sortOrder=desc
```

**查询参数:**
- `type`: 文件类型 (image/video/audio/document)
- `page`: 页码
- `size`: 每页数量
- `keyword`: 搜索关键词
- `sortBy`: 排序字段 (name/size/uploadDate)
- `sortOrder`: 排序方向 (asc/desc)

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "total": 45,
    "page": 1,
    "size": 20,
    "totalPages": 3,
    "files": [
      {
        "id": "file_001",
        "name": "cover.jpg",
        "originalName": "my-cover.jpg",
        "url": "/images/cover.jpg",
        "thumbnail": "/images/thumbnails/cover_thumb.jpg",
        "type": "image",
        "size": 204800,
        "dimensions": "1920x1080",
        "mimeType": "image/jpeg",
        "uploadDate": "2025-08-06T10:00:00Z",
        "isUsed": true,
        "usedIn": ["25080601", "25080602"]
      }
    ]
  }
}
```

### 6.4 删除媒体文件
```http
DELETE /media/{fileId}
```

**响应示例:**
```json
{
  "code": 200,
  "message": "文件删除成功",
  "data": {
    "deletedFile": "/images/old-image.jpg",
    "affectedPosts": ["25080601", "25080602"]
  }
}
```

### 6.5 生成图片缩略图
```http
POST /media/thumbnail
```

**请求参数:**
```json
{
  "imagePath": "/images/large-image.jpg",
  "width": 300,
  "height": 200,
  "quality": 80
}
```

### 6.6 图片编辑
```http
POST /media/edit
```

**请求参数:**
```json
{
  "imagePath": "/images/image.jpg",
  "operations": [
    {
      "type": "resize",
      "width": 800,
      "height": 600
    },
    {
      "type": "crop", 
      "x": 100,
      "y": 100,
      "width": 400,
      "height": 300
    },
    {
      "type": "watermark",
      "text": "我的博客",
      "position": "bottom-right"
    }
  ],
  "saveAs": "/images/edited-image.jpg"
}
```

---

## 7. 草稿管理模块

### 7.1 保存草稿
```http
POST /drafts
```

**请求参数:**
```json
{
  "title": "草稿标题",
  "content": "草稿内容",
  "categories": ["技术"],
  "tags": ["Vue"],
  "autoSave": true,
  "schedulePublish": "2024-01-15T10:00:00Z"
}
```

**响应示例:**
```json
{
  "code": 200,
  "message": "草稿保存成功",
  "data": {
    "id": "draft_001",
    "title": "草稿标题",
    "savedAt": "2024-01-01T10:00:00Z",
    "autoSaveEnabled": true
  }
}
```

### 7.2 获取草稿列表
```http
GET /drafts?page=1&size=10&keyword=
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "total": 5,
    "page": 1,
    "drafts": [
      {
        "id": "draft_001",
        "title": "草稿标题",
        "excerpt": "草稿内容摘要...",
        "wordCount": 500,
        "lastSaved": "2024-01-01T10:00:00Z",
        "schedulePublish": "2024-01-15T10:00:00Z",
        "autoSave": true
      }
    ]
  }
}
```

### 7.3 获取草稿详情
```http
GET /drafts/{id}
```

### 7.4 更新草稿
```http
PUT /drafts/{id}
```

### 7.5 草稿转正式文章
```http
POST /drafts/{id}/publish
```

**请求参数:**
```json
{
  "publishNow": true,
  "scheduledTime": "2024-01-15T10:00:00Z"
}
```

### 7.6 删除草稿
```http
DELETE /drafts/{id}
```

### 7.7 自动保存草稿
```http
POST /drafts/autosave
```

**请求参数:**
```json
{
  "id": "draft_001",
  "content": "自动保存的内容",
  "title": "自动保存的标题"
}
```

---

## 8. 预览功能模块

### 8.1 预览文章
```http
POST /preview
```

**请求参数:**
```json
{
  "content": "# 标题\n\n文章内容...",
  "frontMatter": {
    "title": "预览文章",
    "date": "2024-01-01",
    "tags": ["预览"],
    "categories": ["测试"]
  },
  "theme": "anzhiyu"
}
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "html": "<h1>标题</h1><p>文章内容...</p>",
    "css": "预编译的CSS样式",
    "toc": [
      {
        "level": 1,
        "title": "标题",
        "anchor": "title"
      }
    ],
    "meta": {
      "wordCount": 150,
      "readTime": "2 min",
      "characters": 450
    }
  }
}
```

### 8.2 预览主题效果
```http
POST /preview/theme
```

**请求参数:**
```json
{
  "theme": "anzhiyu",
  "config": {
    "nav": {},
    "aside": {
      "enable": true
    }
  },
  "samplePost": {
    "title": "示例文章",
    "content": "这是一篇示例文章..."
  }
}
```

### 8.3 实时预览 (WebSocket)
```
WebSocket: ws://localhost:8080/ws/preview
```

**发送消息:**
```json
{
  "type": "preview",
  "data": {
    "content": "实时更新的内容",
    "frontMatter": {}
  }
}
```

---

## 9. 安知鱼主题特性模块

### 9.1 获取主题配置
```http
GET /theme/anzhiyu/config
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "nav": {
      "logo": "/images/logo.png",
      "menu": {
        "首页": "/",
        "归档": "/archives/",
        "标签": "/tags/",
        "分类": "/categories/"
      }
    },
    "aside": {
      "enable": true,
      "card_author": {
        "enable": true,
        "description": "这是作者描述"
      },
      "card_announcement": {
        "enable": true,
        "content": "欢迎来到我的博客"
      }
    },
    "cover": {
      "index_enable": true,
      "aside_enable": true,
      "archives_enable": true
    },
    "comments": {
      "use": "Valine",
      "valine": {
        "appId": "your_app_id",
        "appKey": "your_app_key"
      }
    },
    "footer": {
      "owner": {
        "enable": true,
        "since": 2020
      }
    }
  }
}
```

### 9.2 更新主题配置
```http
PUT /theme/anzhiyu/config
```

**请求参数:**
```json
{
  "nav": {
    "logo": "/images/new-logo.png"
  },
  "aside": {
    "enable": true,
    "card_author": {
      "description": "新的作者描述"
    }
  }
}
```

### 9.3 获取Front Matter模板
```http
GET /theme/anzhiyu/frontmatter-templates
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "post": {
      "title": "",
      "date": "{{ date }}",
      "updated": "",
      "tags": [],
      "categories": [],
      "keywords": "",
      "description": "",
      "top_img": "",
      "cover": "",
      "aside": true,
      "comments": true,
      "copyright": true,
      "copyright_author": "",
      "copyright_author_href": "",
      "copyright_url": "",
      "copyright_info": ""
    },
    "page": {
      "title": "",
      "date": "{{ date }}",
      "type": "",
      "aside": false,
      "comments": false
    },
    "link": {
      "title": "",
      "date": "{{ date }}",
      "type": "link",
      "aside": false,
      "comments": false
    }
  }
}
```

### 9.4 获取可用的特殊标签
```http
GET /theme/anzhiyu/tags
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "note": {
      "syntax": "{% note [class] [no-icon] [style] %}\n内容\n{% endnote %}",
      "description": "提示框标签",
      "examples": [
        "{% note info %}\n这是一个信息提示框\n{% endnote %}"
      ]
    },
    "tabs": {
      "syntax": "{% tabs [name] %}\n<!-- tab [title] -->\n内容\n<!-- endtab -->\n{% endtabs %}",
      "description": "标签页",
      "examples": [
        "{% tabs 示例 %}\n<!-- tab 标签1 -->\n内容1\n<!-- endtab -->\n<!-- tab 标签2 -->\n内容2\n<!-- endtab -->\n{% endtabs %}"
      ]
    },
    "timeline": {
      "syntax": "{% timeline [title] [color] %}\n{% timenode [time] [title] %}\n内容\n{% endtimenode %}\n{% endtimeline %}",
      "description": "时间线",
      "examples": []
    },
    "btn": {
      "syntax": "{% btn [url] [text] [icon] [color] [style] [layout] [position] [size] %}",
      "description": "按钮",
      "examples": [
        "{% btn https://example.com 访问链接 fas fa-link blue %}"
      ]
    }
  }
}
```

### 9.5 插入特殊标签
```http
POST /theme/anzhiyu/insert-tag
```

**请求参数:**
```json
{
  "tagType": "note",
  "parameters": {
    "class": "info",
    "content": "这是一个信息提示",
    "icon": true
  }
}
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "markdown": "{% note info %}\n这是一个信息提示\n{% endnote %}"
  }
}
```

---

## 10. 系统工具模块

### 10.1 Hexo命令执行
```http
POST /system/hexo
```

**请求参数:**
```json
{
  "command": "generate", // generate, clean, server, deploy, new
  "args": ["--watch", "--deploy"], // 可选参数
  "async": true // 是否异步执行
}
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "taskId": "task_001", // 异步执行时返回
    "output": "INFO  Start processing\nINFO  Files loaded in 1.2s\nINFO  Generated: index.html",
    "success": true,
    "exitCode": 0,
    "duration": 1250
  }
}
```

### 10.2 获取任务状态
```http
GET /system/tasks/{taskId}
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "taskId": "task_001",
    "command": "hexo generate",
    "status": "completed", // running, completed, failed
    "progress": 100,
    "output": "完整的输出日志...",
    "startTime": "2024-01-01T10:00:00Z",
    "endTime": "2024-01-01T10:01:15Z",
    "duration": 75000
  }
}
```

### 10.3 获取系统状态
```http
GET /system/status
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "hexo": {
      "version": "6.3.0",
      "configValid": true
    },
    "node": {
      "version": "16.14.0"
    },
    "theme": {
      "name": "anzhiyu",
      "version": "1.0.0",
      "configValid": true
    },
    "statistics": {
      "postCount": 25,
      "draftCount": 3,
      "categoryCount": 8,
      "tagCount": 15,
      "imageCount": 45
    },
    "storage": {
      "total": "100GB",
      "used": "45GB",
      "free": "55GB",
      "percentage": 45
    },
    "server": {
      "uptime": "2 days 5 hours",
      "memory": {
        "used": "512MB",
        "total": "2GB"
      },
      "cpu": "15%"
    }
  }
}
```

### 10.4 备份博客数据
```http
POST /system/backup
```

**请求参数:**
```json
{
  "includeTheme": true,
  "includeImages": true,
  "includeConfig": true,
  "compressionLevel": 6
}
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "backupId": "backup_20240101_100000",
    "filename": "blog-backup-20240101.zip",
    "size": "15.6MB",
    "downloadUrl": "/api/system/backup/download/backup_20240101_100000",
    "createdAt": "2024-01-01T10:00:00Z"
  }
}
```

### 10.5 还原博客数据
```http
POST /system/restore
Content-Type: multipart/form-data
```

**请求参数:**
- `backupFile`: 备份文件
- `overwrite`: 是否覆盖现有文件 (默认false)

### 10.6 清理缓存
```http
POST /system/clean
```

**请求参数:**
```json
{
  "cleanType": "all", // all, hexo, images, thumbnails
  "confirm": true
}
```

---

## 11. 操作日志模块

### 11.1 获取操作日志
```http
GET /logs?page=1&size=20&action=&startDate=2024-01-01&endDate=2024-01-31
```

**响应示例:**
```json
{
  "code": 200,
  "data": {
    "total": 150,
    "page": 1,
    "logs": [
      {
        "id": "log_001",
        "action": "CREATE_POST",
        "description": "创建文章: Hello World",
        "details": {
          "postId": "25080601",
          "title": "Hello World"
        },
        "user": "admin",
        "timestamp": "2025-08-06T10:00:00Z",
        "ip": "192.168.1.100",
        "userAgent": "Mozilla/5.0..."
      }
    ]
  }
}
```

### 11.2 清理日志
```http
DELETE /logs?beforeDate=2024-01-01&action=
```

---

## 12. 实时通知模块 (WebSocket)

### 12.1 WebSocket连接
```
WebSocket: ws://localhost:8080/ws/notifications
```

### 12.2 消息格式
```json
{
  "type": "TASK_COMPLETED",
  "data": {
    "taskId": "task_001",
    "message": "Hexo生成完成",
    "timestamp": "2024-01-01T10:00:00Z"
  }
}
```

**通知类型:**
- `TASK_STARTED`: 任务开始
- `TASK_COMPLETED`: 任务完成
- `TASK_FAILED`: 任务失败
- `POST_PUBLISHED`: 文章发布
- `BACKUP_CREATED`: 备份创建
- `SYSTEM_ERROR`: 系统错误

---

## 通用响应格式

### 成功响应
```json
{
  "code": 200,
  "message": "操作成功",
  "data": {},
  "timestamp": "2024-01-01T10:00:00Z"
}
```

### 错误响应
```json
{
  "code": 400,
  "message": "参数错误",
  "error": "详细错误信息",
  "details": {
    "field": "title",
    "reason": "标题不能为空"
  },
  "timestamp": "2024-01-01T10:00:00Z",
  "path": "/api/posts"
}
```

## 状态码说明

- **200**: 成功
- **201**: 创建成功
- **400**: 请求参数错误
- **401**: 未授权访问
- **403**: 权限不足
- **404**: 资源不存在
- **409**: 资源冲突 (如文件名重复)
- **413**: 请求实体过大 (文件太大)
- **429**: 请求频率过高
- **500**: 服务器内部错误
- **503**: 服务不可用

## 安知鱼主题特殊语法支持

### Front Matter 扩展字段详解

```yaml
---
title: 文章标题                    # 必需
date: 2024-01-01 10:00:00         # 必需  
updated: 2024-01-02 15:30:00      # 可选，更新时间
categories:                       # 可选，分类
  - 技术
  - 前端
tags:                            # 可选，标签
  - Vue
  - JavaScript
keywords: Vue,JavaScript,前端      # 可选，SEO关键词
description: 文章描述             # 可选，文章描述
excerpt: 自定义摘要              # 可选，自定义摘要

# 图片相关
top_img: /images/top.jpg         # 可选，顶部大图
cover: /images/cover.jpg         # 可选，文章封面

# 页面配置
aside: true                      # 可选，是否显示侧边栏
comments: true                   # 可选，是否开启评论
toc: true                        # 可选，是否显示目录
toc_number: true                 # 可选，目录是否显示编号
auto_open: true                  # 可选，是否自动展开目录

# 版权信息
copyright: true                  # 可选，是否显示版权
copyright_author: 张三           # 可选，版权作者
copyright_author_href: /about/   # 可选，作者链接
copyright_url: https://example.com # 可选，原文链接
copyright_info: 本文为原创文章    # 可选，版权声明

# 其他配置
sticky: 1                        # 可选，置顶优先级
hide: false                      # 可选，是否隐藏文章
password: 123456                 # 可选，文章密码
abstract: 这是加密文章           # 可选，加密文章摘要
message: 请输入密码查看          # 可选，加密提示信息
---
```

### 特殊标签语法详解

#### 1. 提示框 (Note)
```markdown
{% note [class] [no-icon] [style] %}
内容
{% endnote %}

# 示例
{% note info %}
这是一个信息提示框
{% endnote %}

{% note warning no-icon %}
这是一个无图标的警告框
{% endnote %}
```

**可用样式:** default, primary, success, info, warning, danger

#### 2. 标签页 (Tabs)
```markdown
{% tabs [name] [active] [color] %}
<!-- tab [title] [icon] -->
内容
<!-- endtab -->
{% endtabs %}

# 示例
{% tabs 代码示例 %}
<!-- tab HTML -->
<div>HTML代码</div>
<!-- endtab -->
<!-- tab CSS -->
.class { color: red; }
<!-- endtab -->
{% endtabs %}
```

#### 3. 时间线 (Timeline)
```markdown
{% timeline [title] [color] %}
{% timenode [time] [title] %}
内容
{% endtimenode %}
{% endtimeline %}

# 示例
{% timeline 学习历程 %}
{% timenode 2020-01 开始学习前端 %}
开始接触HTML/CSS
{% endtimenode %}
{% timenode 2020-06 学习JavaScript %}
掌握了JavaScript基础
{% endtimenode %}
{% endtimeline %}
```

#### 4. 按钮 (Button)
```markdown
{% btn [url] [text] [icon] [color] [style] [layout] [position] [size] %}

# 示例
{% btn https://example.com 访问链接 fas fa-link blue %}
{% btn javascript:; 点击按钮 fas fa-download green larger %}
```

#### 5. 复选框 (Checkbox)
```markdown
{% checkbox [checked] [label] [color] %}

# 示例
{% checkbox true 已完成的任务 green %}
{% checkbox false 未完成的任务 red %}
```

#### 6. 单选框 (Radio)
```markdown
{% radio [checked] [label] [color] %}

# 示例
{% radio true 选中项 blue %}
{% radio false 未选中项 %}
```

#### 7. 友链卡片 (Link Card)
```markdown
{% link [title] [url] [description] [image] %}

# 示例
{% link 安知鱼主题 https://github.com/anzhiyu-c/hexo-theme-anzhiyu 一个简洁美观的Hexo主题 /images/anzhiyu.jpg %}
```

#### 8. 相册 (Gallery)
```markdown
{% gallery [group] [column] %}
![图片1](/images/1.jpg)
![图片2](/images/2.jpg)
{% endgallery %}

# 示例
{% gallery 我的相册 3 %}
![风景1](/images/scene1.jpg)
![风景2](/images/scene2.jpg)
![风景3](/images/scene3.jpg)
{% endgallery %}
```

---

## 安全性说明

### 1. 认证机制
- 使用JWT Token进行身份验证
- Token有效期为2小时，支持刷新
- 所有敏感操作需要验证Token有效性

### 2. 文件操作安全
- 限制文件操作范围在博客目录内
- 禁止访问系统敏感文件
- 上传文件类型和大小限制
- 文件名安全检查，防止路径遍历攻击

### 3. 输入验证
- 所有用户输入进行验证和过滤
- Markdown内容XSS防护
- SQL注入防护
- 文件名和路径验证

### 4. 权限控制
- 基于角色的访问控制
- 操作日志记录
- IP白名单支持 (可选)

---

## 性能优化建议

### 1. 缓存策略
- 文章列表缓存
- 图片缩略图缓存
- 静态资源缓存
- Redis缓存支持

### 2. 图片优化
- 自动图片压缩
- 缩略图生成
- WebP格式支持
- 懒加载支持

### 3. 数据库优化
- 索引优化
- 分页查询
- 连接池配置

### 4. 前端优化
- Gzip压缩
- 静态资源CDN
- 代码分割
- 浏览器缓存

---

## 文件命名规范

### 文章文件命名规则

本系统采用 **年月日+序号** 的文件命名规范：

#### 格式说明
- **格式**: `YYMMDDXX.md`
- **YY**: 年份后两位 (如 2025 → 25)
- **MM**: 月份 (01-12)
- **DD**: 日期 (01-31)
- **XX**: 当日文章序号 (01-99)

#### 示例说明
- `25080601.md` = 2025年8月6日第1篇文章
- `25080602.md` = 2025年8月6日第2篇文章
- `25080701.md` = 2025年8月7日第1篇文章

#### 优势特点
1. **时间有序**: 文件名天然按时间排序
2. **快速识别**: 一眼看出文章发布日期
3. **避免冲突**: 同日文章自动递增序号
4. **简洁高效**: 8位数字，简洁明了
5. **扩展性强**: 最多支持99篇/天

#### API支持
- 使用 `GET /posts/next-id` 获取下一个文章编号
- 系统自动检测当日已有文章数量并分配序号
- 支持指定日期获取编号 (用于补发历史文章)

---

## 部署建议

### 1. 系统要求
- Java 11+
- Node.js 14+
- Hexo 5.0+
- MySQL 8.0+ (可选，可使用文件存储)

### 2. 配置文件示例
```yaml
# application.yml
server:
  port: 8080

hexo:
  blog-path: /path/to/your/blog
  auto-backup: true
  backup-interval: 24h

upload:
  max-file-size: 10MB
  allowed-types: jpg,jpeg,png,gif,webp
  image-compress: true
  
security:
  jwt-secret: your-secret-key
  jwt-expiration: 7200
```

### 3. 环境变量
```bash
HEXO_BLOG_PATH=/path/to/blog
JWT_SECRET=your-secret-key
UPLOAD_PATH=/path/to/uploads
```

这个接口文档涵盖了Hexo博客管理系统的所有核心功能，特别针对安知鱼主题进行了优化。你可以根据实际需求进行调整和扩展。 