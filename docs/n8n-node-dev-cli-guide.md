# 🛠️ n8n-node-dev CLI Guide - Hướng dẫn Sử dụng CLI Tool

## 📋 Tổng quan về n8n-node-dev

**n8n-node-dev** là một CLI tool mạnh mẽ được thiết kế để đơn giản hóa việc phát triển credentials và nodes cho n8n. Tool này cung cấp các lệnh để tạo, build và deploy custom nodes một cách hiệu quả.

### 🎯 Tính năng chính

- **Tạo nodes và credentials** từ templates có sẵn
- **Build và compile** TypeScript code
- **Auto-copy** files vào thư mục n8n custom
- **Watch mode** cho development nhanh chóng
- **Template system** linh hoạt

---

## 🚀 Installation & Setup

### **1. Cài đặt Global**

```bash
# Cài đặt global (khuyên dùng)
npm install -g n8n-node-dev

# Verify installation
n8n-node-dev --version
```

### **2. Sử dụng với npx**

```bash
# Không cần cài đặt, chạy trực tiếp
npx n8n-node-dev <command>

# Ví dụ
npx n8n-node-dev new
npx n8n-node-dev build --watch
```

### **3. Kiểm tra Installation**

```bash
# Xem version
n8n-node-dev --version

# Xem help
n8n-node-dev --help

# Xem help cho specific command
n8n-node-dev build --help
```

---

## 📝 Commands Reference

### **🆕 Command: `new`**

Tạo node hoặc credential mới từ template

#### **Syntax**
```bash
n8n-node-dev new
```

#### **Interactive Flow**
```
? What do you want to create? (Use arrow keys)
❯ Node
  Credentials

? What kind of node do you want to create? (Use arrow keys)
❯ Execute 
  Trigger 
  Webhook

? What is the name of the node? MyCustomNode
? What is the description of the node? A custom node for my service
```

#### **Generated Structure**
```
current-directory/
├── MyCustomNode.node.ts      # Main node file
├── MyCustomNode.node.json    # Node metadata
├── package.json              # Package configuration
├── tsconfig.json            # TypeScript config
└── README.md                # Documentation
```

#### **Templates Available**

| **Type** | **Template** | **Description** |
|----------|--------------|-----------------|
| Execute | `simple.ts` | Basic execute node template |
| Trigger | `trigger.ts` | Polling trigger template |
| Webhook | `webhook.ts` | HTTP webhook template |
| Credentials | `credentials.ts` | API credentials template |

#### **Example Usage**
```bash
# Tạo thư mục project
mkdir my-awesome-node && cd my-awesome-node

# Tạo node mới
n8n-node-dev new

# Follow interactive prompts
```

---

### **🔨 Command: `build`**

Build và copy files vào thư mục n8n custom

#### **Syntax**
```bash
n8n-node-dev build [options]
```

#### **Options**

| **Option** | **Description** | **Default** |
|------------|-----------------|-------------|
| `-d, --destination <path>` | Thư mục đích để copy files | `~/.n8n/custom/` |
| `--watch` | Watch mode - tự động build khi file thay đổi | `false` |
| `-h, --help` | Hiển thị help | - |

#### **Examples**

```bash
# Build cơ bản
n8n-node-dev build

# Build với custom destination
n8n-node-dev build --destination ~/my-n8n-nodes

# Build với watch mode
n8n-node-dev build --watch

# Build với cả destination và watch
n8n-node-dev build --destination ~/my-n8n-nodes --watch
```

#### **Build Process**

1. **Compile TypeScript** → JavaScript
2. **Copy compiled files** → destination folder
3. **Update package.json** với correct paths
4. **Validate** node structure
5. **Report** build results

#### **Output Structure**
```
~/.n8n/custom/
├── nodes/
│   └── MyCustomNode/
│       ├── MyCustomNode.node.js
│       └── MyCustomNode.node.js.map
├── credentials/
│   └── MyServiceApi.credentials.js
└── package.json
```

---

## 🔧 Development Workflows

### **🚀 Quick Start Workflow**

```bash
# 1. Tạo project directory
mkdir my-node-project && cd my-node-project

# 2. Initialize node
n8n-node-dev new
# Choose: Node → Execute → MyCustomNode

# 3. Start development with watch mode
n8n-node-dev build --watch

# 4. Start n8n (in another terminal)
# Your node will be available immediately
```

### **🔄 Development Loop**

```bash
# Terminal 1: Watch mode
n8n-node-dev build --watch

# Terminal 2: n8n development server
cd /path/to/n8n
pnpm dev

# Edit files → Auto build → Refresh n8n → Test
```

### **🧪 Testing Workflow**

```bash
# 1. Build for testing
n8n-node-dev build --destination ~/test-nodes

# 2. Start n8n with custom nodes path
N8N_CUSTOM_EXTENSIONS=~/test-nodes n8n start

# 3. Test your node in workflows
```

---

## 📁 Project Structure

### **Recommended Structure**
```
my-custom-node/
├── package.json              # Package configuration
├── tsconfig.json            # TypeScript config
├── README.md                # Documentation
├── .gitignore               # Git ignore rules
├── src/                     # Source files
│   ├── nodes/               # Node implementations
│   │   └── MyNode/
│   │       ├── MyNode.node.ts
│   │       └── MyNode.description.ts
│   ├── credentials/         # Credential definitions
│   │   └── MyApi.credentials.ts
│   └── utils/               # Shared utilities
│       └── helpers.ts
├── dist/                    # Compiled output
└── test/                    # Test files
    └── MyNode.test.ts
```

### **Package.json Configuration**

```json
{
  "name": "n8n-nodes-my-custom",
  "version": "1.0.0",
  "description": "Custom n8n nodes for my service",
  "main": "index.js",
  "scripts": {
    "build": "n8n-node-dev build",
    "dev": "n8n-node-dev build --watch",
    "test": "jest"
  },
  "n8n": {
    "n8nNodesApiVersion": 1,
    "credentials": [
      "dist/credentials/MyApi.credentials.js"
    ],
    "nodes": [
      "dist/nodes/MyNode/MyNode.node.js"
    ]
  },
  "keywords": [
    "n8n-community-node-package",
    "n8n"
  ],
  "files": [
    "dist"
  ],
  "devDependencies": {
    "n8n-workflow": "^1.0.0",
    "typescript": "^4.0.0"
  }
}
```

---

## 🎨 Templates Deep Dive

### **Execute Node Template**

```typescript
// Generated from: n8n-node-dev new → Node → Execute
import {
    IExecuteFunctions,
    INodeExecutionData,
    INodeType,
    INodeTypeDescription,
} from 'n8n-workflow';

export class ClassNameReplace implements INodeType {
    description: INodeTypeDescription = {
        displayName: 'DisplayNameReplace',
        name: 'N8nNameReplace',
        group: ['transform'],
        version: 1,
        description: 'NodeDescriptionReplace',
        defaults: {
            name: 'DisplayNameReplace',
            color: '#772244',
        },
        inputs: ['main'],
        outputs: ['main'],
        properties: [
            {
                displayName: 'My String',
                name: 'myString',
                type: 'string',
                default: '',
                placeholder: 'Placeholder value',
                description: 'The description text',
            },
        ],
    };

    async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
        const items = this.getInputData();
        let item: INodeExecutionData;
        let myString: string;

        for (let itemIndex = 0; itemIndex < items.length; itemIndex++) {
            myString = this.getNodeParameter('myString', itemIndex, '') as string;
            item = items[itemIndex];
            item.json.myString = myString;
        }

        return [items];
    }
}
```

### **Trigger Node Template**

```typescript
// Generated from: n8n-node-dev new → Node → Trigger
import {
    ITriggerFunctions,
    INodeType,
    INodeTypeDescription,
    ITriggerResponse,
} from 'n8n-workflow';

export class TriggerNodeReplace implements INodeType {
    description: INodeTypeDescription = {
        displayName: 'DisplayNameReplace',
        name: 'N8nNameReplace',
        group: ['trigger'],
        version: 1,
        description: 'NodeDescriptionReplace',
        defaults: {
            name: 'DisplayNameReplace',
            color: '#772244',
        },
        inputs: [],
        outputs: ['main'],
        properties: [
            {
                displayName: 'Interval',
                name: 'interval',
                type: 'number',
                default: 60,
                description: 'Interval in seconds',
            },
        ],
    };

    async trigger(this: ITriggerFunctions): Promise<ITriggerResponse> {
        const interval = this.getNodeParameter('interval') as number;

        const intervalObj = setInterval(() => {
            this.emit([
                [
                    {
                        json: {
                            timestamp: new Date().toISOString(),
                        },
                    },
                ],
            ]);
        }, interval * 1000);

        async function closeFunction() {
            clearInterval(intervalObj);
        }

        return {
            closeFunction,
        };
    }
}
```

### **Webhook Node Template**

```typescript
// Generated from: n8n-node-dev new → Node → Webhook
import {
    IWebhookFunctions,
    INodeType,
    INodeTypeDescription,
    IWebhookResponseData,
} from 'n8n-workflow';

export class WebhookNodeReplace implements INodeType {
    description: INodeTypeDescription = {
        displayName: 'DisplayNameReplace',
        name: 'N8nNameReplace',
        group: ['trigger'],
        version: 1,
        description: 'NodeDescriptionReplace',
        defaults: {
            name: 'DisplayNameReplace',
            color: '#772244',
        },
        inputs: [],
        outputs: ['main'],
        webhooks: [
            {
                name: 'default',
                httpMethod: 'POST',
                responseMode: 'onReceived',
                path: 'webhook',
            },
        ],
        properties: [],
    };

    async webhook(this: IWebhookFunctions): Promise<IWebhookResponseData> {
        const bodyData = this.getBodyData();

        return {
            workflowData: [
                [
                    {
                        json: bodyData,
                    },
                ],
            ],
        };
    }
}
```

### **Credentials Template**

```typescript
// Generated from: n8n-node-dev new → Credentials
import {
    ICredentialType,
    INodeProperties,
} from 'n8n-workflow';

export class CredentialsNameReplace implements ICredentialType {
    name = 'credentialsNameReplace';
    displayName = 'DisplayNameReplace';
    properties: INodeProperties[] = [
        {
            displayName: 'API Key',
            name: 'apiKey',
            type: 'string',
            typeOptions: {
                password: true,
            },
            default: '',
        },
    ];
}
```

---

## 🔧 Advanced Usage

### **Custom Destination Management**

```bash
# Development environment
n8n-node-dev build --destination ~/dev-nodes

# Staging environment  
n8n-node-dev build --destination ~/staging-nodes

# Production environment
n8n-node-dev build --destination ~/.n8n/custom/
```

### **Multi-Node Projects**

```bash
# Project structure
my-integration/
├── nodes/
│   ├── ServiceA/
│   │   └── ServiceA.node.ts
│   ├── ServiceB/
│   │   └── ServiceB.node.ts
│   └── ServiceTrigger/
│       └── ServiceTrigger.node.ts
├── credentials/
│   └── ServiceApi.credentials.ts
└── package.json

# Build all nodes at once
n8n-node-dev build
```

### **Environment-specific Builds**

```bash
# Development with debug info
NODE_ENV=development n8n-node-dev build --watch

# Production build
NODE_ENV=production n8n-node-dev build --destination ~/.n8n/custom/
```

---

## 🐛 Troubleshooting

### **Common Issues**

#### **1. Build Errors**

```bash
# Error: TypeScript compilation failed
# Solution: Check tsconfig.json and fix type errors

# Error: Cannot find module 'n8n-workflow'
# Solution: Install dependencies
npm install n8n-workflow

# Error: Permission denied writing to destination
# Solution: Check folder permissions
chmod 755 ~/.n8n/custom/
```

#### **2. Node Not Appearing in n8n**

```bash
# Check if files are copied correctly
ls -la ~/.n8n/custom/

# Check n8n logs for errors
N8N_LOG_LEVEL=debug n8n start

# Restart n8n after building
```

#### **3. Watch Mode Issues**

```bash
# Watch mode not detecting changes
# Solution: Check file system events
# On macOS: brew install fswatch
# On Linux: apt-get install inotify-tools

# Too many files being watched
# Solution: Add .gitignore to exclude node_modules
echo "node_modules/" >> .gitignore
```

### **Debug Commands**

```bash
# Verbose build output
n8n-node-dev build --verbose

# Check build configuration
n8n-node-dev build --dry-run

# Validate node structure
n8n-node-dev validate
```

---

## 📊 Performance Tips

### **Build Optimization**

```bash
# Exclude unnecessary files
echo "*.test.ts" >> .gitignore
echo "docs/" >> .gitignore

# Use TypeScript incremental builds
# tsconfig.json:
{
  "compilerOptions": {
    "incremental": true,
    "tsBuildInfoFile": ".tsbuildinfo"
  }
}
```

### **Watch Mode Optimization**

```bash
# Watch specific directories only
n8n-node-dev build --watch --include="src/**/*.ts"

# Exclude test files from watching
n8n-node-dev build --watch --exclude="**/*.test.ts"
```

---

## 🔗 Integration with Other Tools

### **VS Code Integration**

```json
// .vscode/tasks.json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Build n8n node",
      "type": "shell",
      "command": "n8n-node-dev",
      "args": ["build"],
      "group": "build",
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      }
    },
    {
      "label": "Watch n8n node",
      "type": "shell",
      "command": "n8n-node-dev",
      "args": ["build", "--watch"],
      "group": "build",
      "isBackground": true,
      "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "shared"
      }
    }
  ]
}
```

### **Package.json Scripts**

```json
{
  "scripts": {
    "build": "n8n-node-dev build",
    "dev": "n8n-node-dev build --watch",
    "build:prod": "n8n-node-dev build --destination ~/.n8n/custom/",
    "clean": "rm -rf dist",
    "prebuild": "npm run clean"
  }
}
```

### **CI/CD Integration**

```yaml
# .github/workflows/build.yml
name: Build n8n Node
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '18'
      - run: npm install -g n8n-node-dev
      - run: npm install
      - run: n8n-node-dev build
      - run: npm test
```

---

## 📚 Best Practices

### **1. Project Organization**

```
recommended-structure/
├── src/                     # Source files
│   ├── nodes/              # Node implementations
│   ├── credentials/        # Credentials
│   └── utils/              # Shared utilities
├── test/                   # Test files
├── docs/                   # Documentation
├── examples/               # Usage examples
└── dist/                   # Built files (gitignored)
```

### **2. Development Workflow**

```bash
# 1. Start with template
n8n-node-dev new

# 2. Set up watch mode
n8n-node-dev build --watch

# 3. Develop incrementally
# - Add one feature at a time
# - Test immediately in n8n
# - Commit working changes

# 4. Build for production
n8n-node-dev build --destination ~/.n8n/custom/
```

### **3. Version Management**

```json
// package.json
{
  "version": "1.0.0",
  "n8n": {
    "n8nNodesApiVersion": 1
  }
}
```

### **4. Documentation**

```markdown
# Always include:
- README.md with usage examples
- Node parameter descriptions
- Error handling documentation
- API integration details
```

---

## 🎯 Next Steps

1. **Tạo first node** với `n8n-node-dev new`
2. **Setup development environment** với watch mode
3. **Implement core functionality** theo requirements
4. **Test thoroughly** trong n8n workflows
5. **Document và share** với community

---

**🎉 Chúc bạn sử dụng n8n-node-dev hiệu quả!**

*CLI tool này sẽ giúp bạn phát triển custom nodes nhanh chóng và chuyên nghiệp.* 