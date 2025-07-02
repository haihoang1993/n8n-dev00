# 🧩 n8n Custom Node Development Guide - Hướng dẫn Phát triển Node Tùy chỉnh

## 📋 Tổng quan về Custom Nodes

**Custom Nodes** trong n8n cho phép bạn mở rộng khả năng của platform bằng cách tạo ra các tích hợp mới hoặc logic xử lý dữ liệu tùy chỉnh. Với **n8n-node-dev**, việc phát triển và triển khai custom nodes trở nên đơn giản và hiệu quả.

### 🎯 Mục tiêu của Document này

- Hướng dẫn sử dụng `n8n-node-dev` CLI tool
- Các loại nodes và cách implement
- Best practices cho node development
- Testing và debugging techniques
- Deployment và distribution

---

## 🛠️ Setup Environment

### 1️⃣ **Cài đặt n8n-node-dev**

```bash
# Cài đặt global (khuyên dùng)
npm install -g n8n-node-dev

# Hoặc sử dụng npx (không cần cài đặt)
npx n8n-node-dev <command>

# Verify installation
n8n-node-dev --version
```

### 2️⃣ **Project Structure**

```
my-custom-node/
├── package.json              # Node package configuration
├── tsconfig.json            # TypeScript configuration
├── credentials/             # Custom credentials (optional)
│   └── MyServiceApi.credentials.ts
├── nodes/                   # Node implementations
│   └── MyCustomNode/
│       ├── MyCustomNode.node.ts
│       ├── MyCustomNode.node.json
│       └── description.ts
└── dist/                    # Compiled output (auto-generated)
```

### 3️⃣ **Development Workflow**

```bash
# 1. Tạo project directory
mkdir my-awesome-node && cd my-awesome-node

# 2. Initialize node
n8n-node-dev new

# 3. Develop your node
# Edit the generated files

# 4. Build và test
n8n-node-dev build --watch

# 5. Test trong n8n
# Start n8n và test node mới
```

---

## 🎨 Các loại Nodes

### 🔄 **1. Execute Node (Regular Node)**

**Mục đích**: Xử lý dữ liệu input và trả về output

```typescript
import {
    IExecuteFunctions,
    INodeExecutionData,
    INodeType,
    INodeTypeDescription,
} from 'n8n-workflow';

export class MyExecuteNode implements INodeType {
    description: INodeTypeDescription = {
        displayName: 'My Execute Node',
        name: 'myExecuteNode',
        group: ['transform'],
        version: 1,
        description: 'Processes input data and returns transformed output',
        defaults: {
            name: 'My Execute Node',
            color: '#4A90E2',
        },
        inputs: ['main'],
        outputs: ['main'],
        properties: [
            {
                displayName: 'Operation',
                name: 'operation',
                type: 'options',
                options: [
                    {
                        name: 'Transform Data',
                        value: 'transform',
                        description: 'Transform the input data',
                    },
                    {
                        name: 'Filter Data', 
                        value: 'filter',
                        description: 'Filter the input data',
                    },
                ],
                default: 'transform',
                description: 'Operation to perform',
            },
            {
                displayName: 'Custom Field',
                name: 'customField',
                type: 'string',
                default: '',
                placeholder: 'Enter custom value',
                description: 'Custom processing parameter',
            },
        ],
    };

    async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
        const items = this.getInputData();
        const returnData: INodeExecutionData[] = [];

        for (let i = 0; i < items.length; i++) {
            const operation = this.getNodeParameter('operation', i) as string;
            const customField = this.getNodeParameter('customField', i) as string;
            const item = items[i];

            let newItem: INodeExecutionData;

            if (operation === 'transform') {
                // Transform logic
                newItem = {
                    json: {
                        ...item.json,
                        transformed: true,
                        customValue: customField,
                        processedAt: new Date().toISOString(),
                    },
                };
            } else if (operation === 'filter') {
                // Filter logic - only include items that meet criteria
                if (item.json.shouldInclude) {
                    newItem = {
                        json: {
                            ...item.json,
                            filtered: true,
                            customValue: customField,
                        },
                    };
                } else {
                    continue; // Skip this item
                }
            } else {
                newItem = item;
            }

            returnData.push(newItem);
        }

        return [returnData];
    }
}
```

### ⚡ **2. Trigger Node**

**Mục đích**: Tự động kích hoạt workflow dựa trên events

```typescript
import {
    ITriggerFunctions,
    INodeType,
    INodeTypeDescription,
    ITriggerResponse,
} from 'n8n-workflow';

export class MyTriggerNode implements INodeType {
    description: INodeTypeDescription = {
        displayName: 'My Trigger Node',
        name: 'myTriggerNode',
        group: ['trigger'],
        version: 1,
        description: 'Triggers workflow based on custom conditions',
        defaults: {
            name: 'My Trigger Node',
            color: '#FF6B6B',
        },
        inputs: [],
        outputs: ['main'],
        properties: [
            {
                displayName: 'Polling Interval',
                name: 'interval',
                type: 'number',
                default: 60,
                description: 'Interval in seconds to check for new data',
            },
            {
                displayName: 'API Endpoint',
                name: 'apiEndpoint',
                type: 'string',
                default: '',
                placeholder: 'https://api.example.com/data',
                description: 'API endpoint to poll for new data',
            },
        ],
    };

    async trigger(this: ITriggerFunctions): Promise<ITriggerResponse> {
        const interval = this.getNodeParameter('interval') as number;
        const apiEndpoint = this.getNodeParameter('apiEndpoint') as string;

        let lastCheck = Date.now();

        const intervalObj = setInterval(async () => {
            try {
                // Your polling logic here
                const response = await this.helpers.request({
                    method: 'GET',
                    url: apiEndpoint,
                    json: true,
                });

                // Check if there's new data since last check
                if (response.timestamp > lastCheck) {
                    lastCheck = Date.now();
                    
                    // Emit the trigger event
                    this.emit([
                        [
                            {
                                json: response,
                            },
                        ],
                    ]);
                }
            } catch (error) {
                this.logger.error('Trigger polling failed', { error: error.message });
            }
        }, interval * 1000);

        // Cleanup function
        async function closeFunction() {
            clearInterval(intervalObj);
        }

        return {
            closeFunction,
        };
    }
}
```

### 🌐 **3. Webhook Node**

**Mục đích**: Nhận HTTP requests và kích hoạt workflow

```typescript
import {
    IWebhookFunctions,
    INodeType,
    INodeTypeDescription,
    IWebhookResponseData,
} from 'n8n-workflow';

export class MyWebhookNode implements INodeType {
    description: INodeTypeDescription = {
        displayName: 'My Webhook Node',
        name: 'myWebhookNode',
        group: ['trigger'],
        version: 1,
        description: 'Receives HTTP requests and triggers workflow',
        defaults: {
            name: 'My Webhook Node',
            color: '#50C878',
        },
        inputs: [],
        outputs: ['main'],
        webhooks: [
            {
                name: 'default',
                httpMethod: 'POST',
                responseMode: 'onReceived',
                path: 'my-webhook',
            },
        ],
        properties: [
            {
                displayName: 'Response Mode',
                name: 'responseMode',
                type: 'options',
                options: [
                    {
                        name: 'On Received',
                        value: 'onReceived',
                        description: 'Respond immediately when webhook is received',
                    },
                    {
                        name: 'Last Node',
                        value: 'lastNode',
                        description: 'Respond with data from last executed node',
                    },
                ],
                default: 'onReceived',
            },
            {
                displayName: 'Response Data',
                name: 'responseData',
                type: 'string',
                default: 'success',
                description: 'Data to return in webhook response',
            },
        ],
    };

    async webhook(this: IWebhookFunctions): Promise<IWebhookResponseData> {
        const bodyData = this.getBodyData();
        const headerData = this.getHeaderData();
        const queryData = this.getQueryData();
        const responseMode = this.getNodeParameter('responseMode') as string;
        const responseData = this.getNodeParameter('responseData') as string;

        // Process the incoming webhook data
        const returnData = {
            body: bodyData,
            headers: headerData,
            query: queryData,
            receivedAt: new Date().toISOString(),
        };

        if (responseMode === 'onReceived') {
            return {
                workflowData: [
                    [
                        {
                            json: returnData,
                        },
                    ],
                ],
                webhookResponse: {
                    status: 200,
                    body: { message: responseData, received: true },
                },
            };
        }

        return {
            workflowData: [
                [
                    {
                        json: returnData,
                    },
                ],
            ],
        };
    }
}
```

---

## 🔐 Custom Credentials

### **Credential Definition**

```typescript
import {
    ICredentialType,
    INodeProperties,
} from 'n8n-workflow';

export class MyServiceApi implements ICredentialType {
    name = 'myServiceApi';
    displayName = 'My Service API';
    documentationUrl = 'https://docs.myservice.com/api';
    properties: INodeProperties[] = [
        {
            displayName: 'API Key',
            name: 'apiKey',
            type: 'string',
            typeOptions: {
                password: true,
            },
            default: '',
            description: 'API key for My Service',
        },
        {
            displayName: 'Base URL',
            name: 'baseUrl',
            type: 'string',
            default: 'https://api.myservice.com',
            description: 'Base URL for the API',
        },
        {
            displayName: 'Environment',
            name: 'environment',
            type: 'options',
            options: [
                {
                    name: 'Production',
                    value: 'production',
                },
                {
                    name: 'Staging',
                    value: 'staging',
                },
                {
                    name: 'Development',
                    value: 'development',
                },
            ],
            default: 'production',
        },
    ];

    async authenticate(credentials: any, requestOptions: any): Promise<any> {
        // Add authentication to request
        requestOptions.headers = {
            ...requestOptions.headers,
            'Authorization': `Bearer ${credentials.apiKey}`,
            'X-Environment': credentials.environment,
        };

        return requestOptions;
    }

    async test(credentials: any): Promise<boolean> {
        try {
            // Test the credentials
            const response = await fetch(`${credentials.baseUrl}/auth/test`, {
                headers: {
                    'Authorization': `Bearer ${credentials.apiKey}`,
                },
            });

            return response.ok;
        } catch (error) {
            return false;
        }
    }
}
```

---

## 🧪 Testing & Debugging

### **1. Unit Testing**

```typescript
// tests/MyCustomNode.test.ts
import { MyCustomNode } from '../nodes/MyCustomNode/MyCustomNode.node';

describe('MyCustomNode', () => {
    let node: MyCustomNode;

    beforeEach(() => {
        node = new MyCustomNode();
    });

    test('should have correct description', () => {
        expect(node.description.displayName).toBe('My Custom Node');
        expect(node.description.name).toBe('myCustomNode');
    });

    test('should execute correctly', async () => {
        // Mock execution context
        const mockContext = {
            getInputData: () => [{ json: { test: 'data' } }],
            getNodeParameter: (name: string) => {
                if (name === 'operation') return 'transform';
                if (name === 'customField') return 'test-value';
                return '';
            },
        } as any;

        const result = await node.execute.call(mockContext);
        
        expect(result).toBeDefined();
        expect(result[0]).toHaveLength(1);
        expect(result[0][0].json.transformed).toBe(true);
    });
});
```

### **2. Integration Testing**

```bash
# Build node
n8n-node-dev build

# Start n8n in test mode
N8N_LOG_LEVEL=debug pnpm dev

# Test workflow with your custom node
```

### **3. Debugging Tips**

```typescript
// Add logging in your node
export class MyCustomNode implements INodeType {
    async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
        // Debug logging
        this.logger.info('MyCustomNode execution started', {
            inputCount: this.getInputData().length,
        });

        try {
            // Your logic here
            const result = await processData();
            
            this.logger.debug('Processing completed', { 
                resultCount: result.length 
            });
            
            return [result];
        } catch (error) {
            this.logger.error('MyCustomNode execution failed', {
                error: error.message,
                stack: error.stack,
            });
            throw error;
        }
    }
}
```

---

## 🚀 Development Best Practices

### **1. Node Design Principles**

- **Single Responsibility**: Mỗi node nên có một mục đích rõ ràng
- **User-Friendly**: Interface dễ hiểu và sử dụng
- **Error Handling**: Xử lý lỗi gracefully với thông báo rõ ràng
- **Performance**: Tối ưu cho large datasets
- **Documentation**: Mô tả rõ ràng cho parameters và functionality

### **2. Property Configuration**

```typescript
properties: [
    // Group related properties
    {
        displayName: 'Authentication',
        name: 'authentication',
        type: 'options',
        options: [
            {
                name: 'API Key',
                value: 'apiKey',
            },
            {
                name: 'OAuth2',
                value: 'oauth2',
            },
        ],
        default: 'apiKey',
    },
    
    // Conditional properties
    {
        displayName: 'API Key',
        name: 'apiKey',
        type: 'string',
        typeOptions: {
            password: true,
        },
        displayOptions: {
            show: {
                authentication: ['apiKey'],
            },
        },
        default: '',
    },
    
    // Multi-value properties
    {
        displayName: 'Headers',
        name: 'headers',
        type: 'fixedCollection',
        typeOptions: {
            multipleValues: true,
        },
        default: {},
        options: [
            {
                name: 'header',
                displayName: 'Header',
                values: [
                    {
                        displayName: 'Name',
                        name: 'name',
                        type: 'string',
                        default: '',
                    },
                    {
                        displayName: 'Value',
                        name: 'value',
                        type: 'string',
                        default: '',
                    },
                ],
            },
        ],
    },
]
```

### **3. Error Handling**

```typescript
import { NodeApiError, NodeOperationError } from 'n8n-workflow';

async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
    try {
        // Your logic here
    } catch (error) {
        if (error.response?.status === 401) {
            throw new NodeApiError(this.getNode(), error, {
                message: 'Authentication failed. Please check your credentials.',
                description: 'The API key or authentication method is invalid.',
            });
        }
        
        if (error.response?.status === 429) {
            throw new NodeApiError(this.getNode(), error, {
                message: 'Rate limit exceeded. Please try again later.',
                description: 'The API rate limit has been reached.',
            });
        }
        
        throw new NodeOperationError(this.getNode(), error.message);
    }
}
```

### **4. Performance Optimization**

```typescript
async execute(this: IExecuteFunctions): Promise<INodeExecutionData[][]> {
    const items = this.getInputData();
    
    // Batch processing for large datasets
    const batchSize = 100;
    const results: INodeExecutionData[] = [];
    
    for (let i = 0; i < items.length; i += batchSize) {
        const batch = items.slice(i, i + batchSize);
        const batchResults = await this.processBatch(batch);
        results.push(...batchResults);
        
        // Progress reporting
        if (items.length > 1000) {
            this.logger.info(`Processed ${i + batch.length} of ${items.length} items`);
        }
    }
    
    return [results];
}
```

---

## 📦 Build & Deployment

### **1. Build Commands**

```bash
# Build một lần
n8n-node-dev build

# Build với watch mode (tự động build khi có thay đổi)
n8n-node-dev build --watch

# Build vào thư mục tùy chọn
n8n-node-dev build --destination ~/my-custom-nodes

# Build cho production
n8n-node-dev build --destination ~/.n8n/custom/
```

### **2. Package.json Configuration**

```json
{
  "name": "n8n-nodes-my-custom-node",
  "version": "1.0.0",
  "description": "Custom n8n node for My Service integration",
  "main": "index.js",
  "n8n": {
    "n8nNodesApiVersion": 1,
    "credentials": [
      "dist/credentials/MyServiceApi.credentials.js"
    ],
    "nodes": [
      "dist/nodes/MyCustomNode/MyCustomNode.node.js"
    ]
  },
  "keywords": [
    "n8n-community-node-package",
    "n8n",
    "myservice"
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

### **3. Distribution**

```bash
# Publish to npm
npm publish

# Install từ npm
npm install n8n-nodes-my-custom-node

# Hoặc local installation
cd ~/.n8n/custom/
npm install /path/to/your/node/package
```

---

## 🔧 Advanced Techniques

### **1. Load Options Dynamically**

```typescript
export class MyCustomNode implements INodeType {
    methods = {
        loadOptions: {
            async getUsers(this: ILoadOptionsFunctions) {
                const credentials = await this.getCredentials('myServiceApi');
                
                const response = await this.helpers.request({
                    method: 'GET',
                    url: `${credentials.baseUrl}/users`,
                    headers: {
                        'Authorization': `Bearer ${credentials.apiKey}`,
                    },
                    json: true,
                });
                
                return response.users.map((user: any) => ({
                    name: user.name,
                    value: user.id,
                }));
            },
        },
    };

    description: INodeTypeDescription = {
        // ... other properties
        properties: [
            {
                displayName: 'User',
                name: 'userId',
                type: 'options',
                typeOptions: {
                    loadOptionsMethod: 'getUsers',
                },
                default: '',
                description: 'Select a user',
            },
        ],
    };
}
```

### **2. Webhook Setup/Cleanup**

```typescript
export class MyWebhookNode implements INodeType {
    webhookMethods = {
        default: {
            async checkExists(this: IHookFunctions): Promise<boolean> {
                const webhookUrl = this.getNodeWebhookUrl('default');
                const credentials = await this.getCredentials('myServiceApi');
                
                // Check if webhook exists in external service
                const response = await this.helpers.request({
                    method: 'GET',
                    url: `${credentials.baseUrl}/webhooks`,
                    headers: {
                        'Authorization': `Bearer ${credentials.apiKey}`,
                    },
                    json: true,
                });
                
                return response.webhooks.some((hook: any) => hook.url === webhookUrl);
            },

            async create(this: IHookFunctions): Promise<boolean> {
                const webhookUrl = this.getNodeWebhookUrl('default');
                const credentials = await this.getCredentials('myServiceApi');
                
                await this.helpers.request({
                    method: 'POST',
                    url: `${credentials.baseUrl}/webhooks`,
                    headers: {
                        'Authorization': `Bearer ${credentials.apiKey}`,
                    },
                    body: {
                        url: webhookUrl,
                        events: ['user.created', 'user.updated'],
                    },
                    json: true,
                });
                
                return true;
            },

            async delete(this: IHookFunctions): Promise<boolean> {
                const webhookUrl = this.getNodeWebhookUrl('default');
                const credentials = await this.getCredentials('myServiceApi');
                
                // Find and delete webhook
                const webhooks = await this.helpers.request({
                    method: 'GET',
                    url: `${credentials.baseUrl}/webhooks`,
                    headers: {
                        'Authorization': `Bearer ${credentials.apiKey}`,
                    },
                    json: true,
                });
                
                const webhook = webhooks.webhooks.find((hook: any) => hook.url === webhookUrl);
                if (webhook) {
                    await this.helpers.request({
                        method: 'DELETE',
                        url: `${credentials.baseUrl}/webhooks/${webhook.id}`,
                        headers: {
                            'Authorization': `Bearer ${credentials.apiKey}`,
                        },
                        json: true,
                    });
                }
                
                return true;
            },
        },
    };
}
```

---

## 📚 Resources & Tools

### **Useful Links**

- [n8n Node Development Documentation](https://docs.n8n.io/integrations/creating-nodes/)
- [n8n Community Nodes](https://www.npmjs.com/search?q=n8n-community-node-package)
- [n8n GitHub Repository](https://github.com/n8n-io/n8n)
- [n8n Workflow Templates](https://n8n.io/workflows/)

### **Development Tools**

- **VS Code Extensions**: TypeScript, Vue.js, n8n snippets
- **Testing**: Jest, n8n-workflow test utilities
- **Debugging**: Node.js debugger, n8n debug mode
- **Documentation**: JSDoc, Markdown

### **Community**

- [n8n Community Forum](https://community.n8n.io/)
- [Discord Server](https://discord.gg/n8n)
- [GitHub Discussions](https://github.com/n8n-io/n8n/discussions)

---

## 🎯 Next Steps

1. **Tạo node đầu tiên** với `n8n-node-dev new`
2. **Implement core functionality** theo requirements
3. **Add comprehensive error handling**
4. **Write tests** cho critical paths
5. **Document your node** với clear examples
6. **Share với community** qua npm hoặc GitHub

---

**🎉 Chúc bạn phát triển custom nodes thành công!**

*Document này sẽ được cập nhật thường xuyên với các techniques và best practices mới nhất.*