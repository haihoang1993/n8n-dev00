# 📚 n8n Development Documentation

Chào mừng bạn đến với bộ tài liệu phát triển n8n toàn diện! Đây là nơi tập trung tất cả kiến thức cần thiết để phát triển, mở rộng và tùy chỉnh n8n.

## 📖 Danh sách Documents

### 🚀 **[n8n Development Guide](./n8n-development-guide.md)**
*Hướng dẫn phát triển toàn diện cho n8n*

- **Mục đích**: Hướng dẫn setup và phát triển n8n core
- **Nội dung chính**:
  - Cấu trúc monorepo và kiến trúc
  - Setup development environment
  - Backend và Frontend development
  - Customization và modification
  - Troubleshooting

**👥 Phù hợp cho**: Developers muốn contribute vào n8n core hoặc customize n8n platform

---

### 🧩 **[Custom Node Development Guide](./n8n-custom-node-development.md)**
*Hướng dẫn phát triển Custom Nodes chi tiết*

- **Mục đích**: Hướng dẫn tạo và phát triển custom nodes
- **Nội dung chính**:
  - Các loại nodes (Execute, Trigger, Webhook)
  - Custom credentials development
  - Testing và debugging
  - Best practices và advanced techniques
  - Deployment và distribution

**👥 Phù hợp cho**: Developers muốn tạo integrations mới hoặc custom logic

---

### 🛠️ **[n8n-node-dev CLI Guide](./n8n-node-dev-cli-guide.md)**
*Hướng dẫn sử dụng CLI tool chi tiết*

- **Mục đích**: Hướng dẫn sử dụng n8n-node-dev CLI tool
- **Nội dung chính**:
  - Installation và setup
  - Commands reference (`new`, `build`)
  - Templates và project structure
  - Development workflows
  - Troubleshooting và optimization

**👥 Phù hợp cho**: Developers sử dụng n8n-node-dev để phát triển nodes

---

### 🌿 **[Git Workflow Guide](./n8n-git-workflow-guide.md)**
*Hướng dẫn Git workflow và best practices*

- **Mục đích**: Hướng dẫn Git workflow chuẩn cho n8n development
- **Nội dung chính**:
  - Branch strategy và naming conventions
  - Commit message standards
  - Code review process
  - Advanced Git techniques
  - Troubleshooting và tools

**👥 Phù hợp cho**: Tất cả developers làm việc với n8n codebase

---

### 🐳 **[Docker Packaging Guide](./n8n-docker-packaging-guide.md)**
*Hướng dẫn tạo và đóng gói Docker image từ source code*

- **Mục đích**: Hướng dẫn build và deploy n8n với Docker
- **Nội dung chính**:
  - Build process và architecture
  - Multi-stage Docker builds
  - Troubleshooting common issues
  - Production deployment strategies
  - Performance optimization

**👥 Phù hợp cho**: DevOps engineers và developers muốn containerize n8n

---

## 🎯 Lộ trình Học tập

### **🔰 Beginner - Bắt đầu với n8n**

1. **Đọc tổng quan** về n8n trong [Development Guide](./n8n-development-guide.md#📋-tổng-quan-về-n8n)
2. **Setup environment** theo hướng dẫn [Development Setup](./n8n-development-guide.md#🚀-development-setup)
3. **Tạo node đầu tiên** với [n8n-node-dev CLI](./n8n-node-dev-cli-guide.md#🆕-command-new)

### **🚀 Intermediate - Phát triển Custom Nodes**

1. **Hiểu các loại nodes** trong [Custom Node Guide](./n8n-custom-node-development.md#🎨-các-loại-nodes)
2. **Implement Execute Node** với [template](./n8n-custom-node-development.md#🔄-1-execute-node-regular-node)
3. **Setup testing** và [debugging](./n8n-custom-node-development.md#🧪-testing--debugging)

### **⚡ Advanced - Mastery Level**

1. **Advanced techniques** như [Load Options](./n8n-custom-node-development.md#🔧-advanced-techniques)
2. **Performance optimization** và [best practices](./n8n-custom-node-development.md#🚀-development-best-practices)
3. **Docker deployment** với [Docker Packaging Guide](./n8n-docker-packaging-guide.md#🚀-quy-trình-build-chi-tiết)
4. **Contribute to n8n core** với [Development Guide](./n8n-development-guide.md#🛠️-hướng-dẫn-sửa-đổi)

---

## 🛠️ Quick Start Commands

### **Setup n8n Development**
```bash
# Clone n8n repository
git clone https://github.com/n8n-io/n8n.git
cd n8n

# Install dependencies
pnpm install

# Start development
pnpm dev
```

### **Create Custom Node**
```bash
# Install n8n-node-dev
npm install -g n8n-node-dev

# Create new node
mkdir my-node && cd my-node
n8n-node-dev new

# Start development
n8n-node-dev build --watch
```

### **Test Custom Node**
```bash
# Build node
n8n-node-dev build

# Start n8n with custom nodes
n8n start
```

### **Build Docker Image**
```bash
# Build from source
pnpm install --no-frozen-lockfile
pnpm run build:n8n
docker build --platform linux/arm64 \
  --build-arg TARGETPLATFORM=linux/arm64 \
  -t n8nio/n8n:local \
  -f docker/images/n8n/Dockerfile .

# Run with Docker Compose
docker-compose up -d
```

---

## 📂 Project Structure Overview

```
n8n-dev00/
├── packages/
│   ├── cli/                     # 🔥 Backend API server
│   ├── core/                    # ⚙️ Workflow execution engine
│   ├── frontend/editor-ui/      # 🎨 Vue.js frontend
│   ├── nodes-base/             # 🧩 Built-in nodes & credentials
│   ├── node-dev/               # 🛠️ CLI tool for node development
│   └── workflow/               # 📋 Workflow definitions
├── docs/                       # 📚 This documentation
└── test-workflows/             # 🧪 Test cases
```

---

## 🔧 Development Tools

### **Essential Tools**
- **n8n-node-dev**: CLI tool cho node development
- **pnpm**: Package manager cho monorepo
- **TypeScript**: Primary language
- **Vue.js 3**: Frontend framework

### **IDE Setup**
- **VS Code** với extensions:
  - TypeScript
  - Vue Language Features
  - ESLint
  - Prettier

### **Testing Tools**
- **Jest**: Unit testing
- **Cypress**: E2E testing
- **Playwright**: Browser testing

---

## 🤝 Contributing Guidelines

### **Before Contributing**
1. Đọc [Development Guide](./n8n-development-guide.md) để hiểu architecture
2. Setup development environment
3. Tạo feature branch từ `master`
4. Follow coding standards và best practices

### **Types of Contributions**
- **🐛 Bug fixes**: Fix issues trong existing code
- **✨ New features**: Add new functionality
- **🧩 New nodes**: Create integrations với external services
- **📚 Documentation**: Improve hoặc add documentation
- **🧪 Tests**: Add hoặc improve test coverage

### **Pull Request Process**
1. Ensure tests pass
2. Update documentation nếu cần
3. Follow commit message conventions
4. Request review từ maintainers

---

## 🆘 Getting Help

### **Community Resources**
- **[n8n Community Forum](https://community.n8n.io/)**: Hỏi đáp và thảo luận
- **[Discord Server](https://discord.gg/n8n)**: Real-time chat
- **[GitHub Discussions](https://github.com/n8n-io/n8n/discussions)**: Technical discussions

### **Documentation Links**
- **[Official n8n Docs](https://docs.n8n.io/)**: User documentation
- **[API Reference](https://docs.n8n.io/api/)**: API documentation
- **[Node Development](https://docs.n8n.io/integrations/creating-nodes/)**: Official node development guide

### **Common Issues**
- **Build errors**: Check [Troubleshooting](./n8n-development-guide.md#🚨-troubleshooting)
- **Node not appearing**: See [CLI Troubleshooting](./n8n-node-dev-cli-guide.md#🐛-troubleshooting)
- **Performance issues**: Review [Performance Tips](./n8n-node-dev-cli-guide.md#📊-performance-tips)

---

## 📋 Checklists

### **✅ New Node Development Checklist**
- [ ] Setup project với `n8n-node-dev new`
- [ ] Implement node logic
- [ ] Add proper error handling
- [ ] Write unit tests
- [ ] Test trong n8n workflow
- [ ] Add documentation
- [ ] Build và deploy

### **✅ n8n Core Development Checklist**
- [ ] Fork và clone repository
- [ ] Setup development environment
- [ ] Create feature branch
- [ ] Implement changes
- [ ] Run tests (`pnpm test`)
- [ ] Update documentation
- [ ] Submit pull request

---

## 🎯 Roadmap & Future Plans

### **Planned Improvements**
- [ ] More comprehensive testing guides
- [ ] Advanced debugging techniques
- [ ] Performance optimization guides
- [ ] CI/CD best practices
- [ ] Docker development setup

### **Community Contributions**
- [ ] Translation của documentation
- [ ] Video tutorials
- [ ] Example projects
- [ ] Best practices từ community

---

## 📄 License & Legal

Tất cả documentation trong thư mục này được phát hành dưới cùng license với n8n project.

- **n8n**: [Sustainable Use License](https://github.com/n8n-io/n8n/blob/master/LICENSE.md)
- **Documentation**: MIT License cho community contributions

---

**🎉 Happy Coding với n8n!**

*Chúng tôi luôn chào đón contributions từ community. Nếu bạn có ý tưởng cải thiện documentation hoặc tìm thấy lỗi, hãy tạo issue hoặc pull request.*

---

## 📊 Document Statistics

| Document | Lines | Topics | Last Updated |
|----------|-------|--------|--------------|
| [n8n-development-guide.md](./n8n-development-guide.md) | 434 | 15+ | Latest |
| [n8n-custom-node-development.md](./n8n-custom-node-development.md) | 800+ | 20+ | Latest |
| [n8n-node-dev-cli-guide.md](./n8n-node-dev-cli-guide.md) | 600+ | 15+ | Latest |
| [n8n-git-workflow-guide.md](./n8n-git-workflow-guide.md) | 500+ | 12+ | Latest |
| **Total** | **2300+** | **62+** | **Latest** | 