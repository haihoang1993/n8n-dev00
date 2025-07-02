# 🚀 n8n Development Guide - Hướng dẫn Phát triển Toàn diện

## 📋 Tổng quan về n8n

**n8n** là một platform tự động hóa workflow mã nguồn mở, cho phép các team kỹ thuật có được sự linh hoạt của code với tốc độ của no-code. Với hơn 400+ tích hợp, khả năng AI tự nhiên và giấy phép fair-code.

### 🏗️ Kiến trúc Monorepo

n8n được tổ chức theo kiểu **monorepo** sử dụng:
- **pnpm workspaces** để quản lý dependencies
- **Turbo** để tối ưu build system
- **TypeScript** cho toàn bộ codebase
- **Vue.js 3** cho frontend
- **Express.js** cho backend API

---

```
// run dev backend:
cd packages/cli
pnpm run dev

// run dev frontnd:
cd packages/frontend/editor-ui
pnpm run dev

```

## 🏗️ Cấu trúc Backend (Server-side)

### 📁 **Backend chính: `packages/cli/`**

```
packages/cli/
├── src/
│   ├── server.ts              # 🔥 Main server file - Entry point
│   ├── abstract-server.ts     # Base server class
│   ├── controllers/           # 🎯 API Controllers (REST endpoints)
│   │   ├── auth.controller.ts      # Authentication endpoints
│   │   ├── workflows.controller.ts # Workflow CRUD operations
│   │   ├── executions.controller.ts # Execution management
│   │   ├── credentials.controller.ts # Credential management
│   │   ├── nodes.controller.ts     # Node information
│   │   └── ...                     # 29+ controllers
│   ├── services/              # Business logic services
│   ├── commands/              # CLI commands (start, import, export, etc.)
│   ├── auth/                  # Authentication & Authorization
│   ├── workflows/             # Workflow execution engine
│   ├── executions/            # Execution tracking & management
│   ├── webhooks/              # Webhook endpoints handling
│   ├── credentials/           # Credential encryption/decryption
│   ├── databases/             # Database abstraction layers
│   ├── config/                # Configuration management
│   ├── errors/                # Custom error classes
│   ├── events/                # Event system
│   └── middleware/            # Express middleware
├── bin/
│   └── n8n                    # CLI executable
└── package.json               # Dependencies & scripts
```

### 🔧 **Core Engine: `packages/core/`**

```
packages/core/
├── src/
│   ├── WorkflowExecute.ts     # 🚀 Workflow execution engine
│   ├── NodeExecuteFunctions.ts # Node execution helpers
│   ├── LoadNodeParameterOptions.ts # Parameter loading
│   ├── ClassLoader.ts         # Dynamic class loading
│   └── ...
```

### 🧩 **Nodes & Integrations: `packages/nodes-base/`**

```
packages/nodes-base/
├── credentials/               # 384+ credential definitions
├── nodes/                     # 305+ node implementations
│   ├── Google/               # Google services nodes
│   ├── Slack/                # Slack integration
│   ├── HTTP/                 # HTTP request node
│   └── ...
└── utils/                    # Shared utilities
```

---

## 🎨 Cấu trúc Frontend (Client-side)

### 📁 **Main Frontend: `packages/frontend/editor-ui/`**

```
packages/frontend/editor-ui/
├── src/
│   ├── App.vue                # 🔥 Root Vue component
│   ├── main.ts                # Application entry point
│   ├── components/            # 🎯 Vue Components
│   │   ├── MainHeader.vue          # Top navigation bar
│   │   ├── MainSidebar.vue         # Left sidebar navigation
│   │   ├── WorkflowCard.vue        # Workflow list cards
│   │   ├── NodeCreator/            # Node creation panel
│   │   ├── CredentialsModal.vue    # Credential management
│   │   └── ...                     # 100+ components
│   ├── views/                 # 📄 Main Pages/Views
│   │   ├── NodeView.vue            # 🔥 Main workflow editor
│   │   ├── WorkflowsView.vue       # Workflow list page
│   │   ├── ExecutionsView.vue      # Execution history
│   │   ├── CredentialsView.vue     # Credential management
│   │   ├── SettingsView.vue        # Settings page
│   │   ├── SigninView.vue          # Login/Setup page
│   │   └── ...
│   ├── composables/           # Vue 3 Composables (reusable logic)
│   ├── stores/                # Pinia state management
│   ├── styles/                # 🎨 SCSS Styles
│   │   ├── n8n-theme.scss          # Main theme file
│   │   ├── n8n-theme-variables.scss # 🔥 Theme variables (colors, sizes)
│   │   └── ...
│   ├── plugins/               # Vue plugins
│   ├── utils/                 # Utility functions
│   └── types/                 # TypeScript type definitions
├── public/                    # Static assets
└── package.json
```

### 🎨 **Design System: `packages/frontend/@n8n/design-system/`**

```
packages/frontend/@n8n/design-system/
├── src/
│   ├── components/            # 🧩 Reusable UI Components
│   │   ├── N8nButton/              # Button component
│   │   ├── N8nInput/               # Input fields
│   │   ├── N8nModal/               # Modal dialogs
│   │   ├── N8nCard/                # Card layouts
│   │   └── ...                     # 50+ components
│   ├── css/                   # Base CSS styles
│   ├── theme/                 # Theme configuration
│   └── styleguide/            # Component documentation
```

---

## 🚀 Development Setup

### 1️⃣ **Yêu cầu hệ thống**

```bash
# Node.js version
node -v  # >= 18.10.0

# pnpm package manager
npm install -g pnpm

# Git
git --version
```

### 2️⃣ **Clone và Setup**

```bash
# Clone repository
git clone https://github.com/n8n-io/n8n.git
cd n8n

# Install dependencies
pnpm install

# Build all packages
pnpm build
```

### 3️⃣ **Environment Configuration**

Tạo file `.env.development`:

```bash
# Task Runners - Enable để tránh deprecation warning
N8N_RUNNERS_ENABLED=true

# File Permissions - Tự động fix permissions
N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true

# Development Settings
NODE_ENV=development
N8N_LOG_LEVEL=debug

# Database
DB_TYPE=sqlite
DB_SQLITE_DATABASE=~/.n8n/database.sqlite

# Security
N8N_SECURE_COOKIE=false

# Performance
N8N_WORKERS_ENABLED=true

# UI/UX
N8N_TEMPLATES_ENABLED=true
N8N_PERSONALIZATION_ENABLED=true

# Webhook
N8N_WEBHOOK_URL=http://localhost:5678/

# Editor
N8N_EDITOR_BASE_URL=http://localhost:5678

# Disable telemetry for development
N8N_DIAGNOSTICS_ENABLED=false
N8N_VERSION_NOTIFICATIONS_ENABLED=false
```

### 4️⃣ **Chạy Development Server**

```bash
# Chạy toàn bộ (frontend + backend)
pnpm dev

# Hoặc chạy riêng từng phần:

# Chỉ backend (API server)
cd packages/cli
pnpm dev
# → http://localhost:5678

# Chỉ frontend (Vue dev server)  
cd packages/frontend/editor-ui
pnpm dev
# → http://localhost:8080
```

### 5️⃣ **Setup lần đầu**

1. Truy cập: http://localhost:5678
2. Trang Setup sẽ hiện ra (không cần login)
3. Nhập thông tin Owner:
   - **Email**: admin@example.com
   - **First Name**: Admin
   - **Last Name**: User
   - **Password**: admin123
4. Hoàn thành setup → Vào dashboard

---

## 🛠️ Hướng dẫn Sửa đổi

### 🎨 **Thay đổi Giao diện (Frontend)**

#### **1. Thay đổi màu sắc chính**

File: `packages/frontend/editor-ui/src/n8n-theme-variables.scss`

```scss
// Thay đổi màu chính
$color-primary: #10b981; // Xanh lá

// Thay đổi màu sidebar
$sidebar-active-color: #10b981;

// Thay đổi màu header
$header-background: #1f2937;
```

#### **2. Sửa layout tổng thể**

File: `packages/frontend/editor-ui/src/App.vue`

```vue
<style lang="scss" module>
.app-grid {
  display: grid;
  grid-template-areas:
    'banners banners'
    'sidebar header'  
    'sidebar content';
  grid-template-columns: 250px 1fr; // Thay đổi độ rộng sidebar
  grid-template-rows: auto auto 1fr;
}
</style>
```

#### **3. Thay đổi components**

| **Component** | **File** | **Mục đích** |
|---------------|----------|--------------|
| Header | `packages/frontend/editor-ui/src/components/MainHeader.vue` | Top navigation |
| Sidebar | `packages/frontend/editor-ui/src/components/MainSidebar.vue` | Left menu |
| Workflow Editor | `packages/frontend/editor-ui/src/views/NodeView.vue` | Main canvas |
| Workflow List | `packages/frontend/editor-ui/src/views/WorkflowsView.vue` | Workflow management |

### ⚙️ **Thay đổi Backend Logic**

#### **1. Thêm API endpoint mới**

File: `packages/cli/src/controllers/custom.controller.ts`

```typescript
import { Request, Response } from 'express';
import { Get, RestController } from '@/decorators';

@RestController('/custom')
export class CustomController {
  @Get('/hello')
  async hello(req: Request, res: Response) {
    return { message: 'Hello from custom endpoint!' };
  }
}
```

#### **2. Sửa workflow execution**

File: `packages/core/src/WorkflowExecute.ts`

```typescript
// Thêm custom logic vào workflow execution
```

#### **3. Thêm node mới**

Directory: `packages/nodes-base/nodes/YourCustomNode/`

```
YourCustomNode/
├── YourCustomNode.node.ts     # Node implementation
├── YourCustomNode.node.json   # Node definition
└── description.ts             # Node description
```

---

## 🔧 Development Tips

### **Hot Reload**

- **Frontend**: Tự động reload khi sửa `.vue`, `.ts`, `.scss`
- **Backend**: Tự động restart khi sửa `.ts` files
- **Nodes**: Cần restart backend khi thêm node mới

### **Debug**

```bash
# Debug backend
cd packages/cli
pnpm dev:debug
# → Attach debugger on port 9229

# Debug frontend
cd packages/frontend/editor-ui  
pnpm dev
# → Vue DevTools available
```

### **Testing**

```bash
# Run all tests
pnpm test

# Test specific package
cd packages/cli
pnpm test

# E2E tests
pnpm test:e2e
```

### **Build Production**

```bash
# Build all packages
pnpm build

# Build specific package
cd packages/cli
pnpm build
```

---

## 📂 Cấu trúc File quan trọng

| **File/Folder** | **Mục đích** | **Loại** |
|-----------------|--------------|----------|
| `packages/cli/src/server.ts` | Main server entry | Backend |
| `packages/cli/src/controllers/` | API endpoints | Backend |
| `packages/core/src/WorkflowExecute.ts` | Workflow engine | Backend |
| `packages/frontend/editor-ui/src/App.vue` | Root component | Frontend |
| `packages/frontend/editor-ui/src/views/NodeView.vue` | Main editor | Frontend |
| `packages/frontend/editor-ui/src/n8n-theme-variables.scss` | Theme colors | Frontend |
| `packages/nodes-base/nodes/` | All integrations | Backend |
| `packages/nodes-base/credentials/` | All credentials | Backend |

---

## 🚨 Troubleshooting

### **Lỗi thường gặp**

1. **Port 5678 đã được sử dụng**
   ```bash
   lsof -i :5678
   kill -9 <PID>
   ```

2. **Build failed**
   ```bash
   pnpm clean
   pnpm build
   ```

3. **Dependencies issues**
   ```bash
   rm -rf node_modules
   rm pnpm-lock.yaml
   pnpm install
   ```

4. **Permission errors**
   ```bash
   chmod 600 ~/.n8n/config
   ```

---

## 📚 Tài liệu tham khảo

- **Official Docs**: https://docs.n8n.io/
- **API Reference**: http://localhost:5678/api-docs (khi chạy dev)
- **Contributing Guide**: `CONTRIBUTING.md`
- **Architecture**: https://docs.n8n.io/hosting/architecture/

---

**🎉 Chúc bạn phát triển n8n thành công!** 