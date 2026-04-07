# 📘 Azure ARM Templates – Beginner's Guide

> **What you'll learn:** What ARM templates are, why they exist, how they're structured, and how to use them — explained simply for first-time users.

---

## 🤔 What is an ARM Template?

Imagine you're setting up a new office. Instead of telling each person individually what furniture to buy, where to place it, and how to configure the WiFi — you write it all down in **one instruction document**. Anyone can follow that document and recreate the exact same office setup.

**ARM (Azure Resource Manager) templates work the same way for cloud infrastructure.**

- ARM = **Azure Resource Manager**
- Template = a **JSON file** that describes your Azure resources
- Instead of clicking around the Azure portal manually, you write your setup once in a file and let Azure do the rest.

This approach is called **Infrastructure as Code (IaC)**.

---

## 🌐 How Azure Works (Big Picture)

```
You (Developer/Admin)
        │
        ▼
┌───────────────────────────┐
│    ARM Template (.json)   │  ← You write this
└───────────────────────────┘
        │
        ▼
┌───────────────────────────┐
│  Azure Resource Manager   │  ← Azure's "brain" that reads your template
└───────────────────────────┘
        │
        ├──────────────────┬──────────────────┐
        ▼                  ▼                  ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   Virtual    │  │   Storage    │  │   Database   │
│   Machine    │  │   Account    │  │   Server     │
└──────────────┘  └──────────────┘  └──────────────┘
        Azure Resources (created automatically)
```

> ✅ You write one file → Azure creates everything for you!

---

## 📁 ARM Template File Structure

An ARM template is a `.json` file with **6 key sections**:

```
azuredeploy.json
├── $schema          ← What version/format this template uses
├── contentVersion   ← Your version number (like "1.0.0.0")
├── parameters       ← Inputs you provide at deployment time
├── variables        ← Calculated values used inside the template
├── resources        ← The actual Azure stuff to create (VMs, storage, etc.)
└── outputs          ← Values returned after deployment (like an IP address)
```

---

## 🔍 Each Section Explained (with examples)

### 1️⃣ `$schema` — The Rulebook Reference

Tells Azure which version of the template format you're using.

```json
"$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#"
```

> 💡 Think of it like the "edition" of a textbook — it tells Azure how to read the file.

---

### 2️⃣ `contentVersion` — Your Version Number

A version number you assign to track changes. Azure doesn't enforce this — it's for you and your team.

```json
"contentVersion": "1.0.0.0"
```

> 💡 Like a document header saying "Version 1, Draft 1".

---

### 3️⃣ `parameters` — Customizable Inputs

Instead of hardcoding values, you define inputs so the same template can be reused for different environments (dev, staging, production).

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "metadata": {
      "description": "Enter a unique name for your storage account"
    }
  },
  "location": {
    "type": "string",
    "defaultValue": "eastus",
    "allowedValues": ["eastus", "westus", "centralindia"]
  }
}
```

> 💡 Like a form you fill out before deploying. Same template, different answers each time.

---

### 4️⃣ `variables` — Reusable Values

Define values once and reuse them throughout the template. Reduces copy-paste errors.

```json
"variables": {
  "storageSku": "Standard_LRS",
  "fullStorageName": "[concat(parameters('storageAccountName'), 'store')]"
}
```

> 💡 Like defining `PI = 3.14` in a math formula — write it once, use it everywhere.

---

### 5️⃣ `resources` — The Main Section ⭐

This is where you define **what Azure resources to create**. Each resource has:

| Property | Meaning |
|---|---|
| `type` | What kind of resource (e.g., Storage Account, VM) |
| `apiVersion` | Which API version to use for that resource |
| `name` | The name of the resource |
| `location` | Azure region where it's created |
| `properties` | Resource-specific settings |

**Example — Creating a Storage Account:**

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    "sku": {
      "name": "[variables('storageSku')]"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

> 💡 This is the "recipe" — it tells Azure exactly what to cook and how.

---

### 6️⃣ `outputs` — Results After Deployment

Values returned once the template runs successfully. Useful for getting things like URLs, IPs, or resource IDs.

```json
"outputs": {
  "storageEndpoint": {
    "type": "string",
    "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
  }
}
```

> 💡 Like getting a receipt after a purchase — here's what was created and where it is.

---

## 🔄 Deployment Workflow

```
Step 1: Write your ARM template (.json file)
        │
        ▼
Step 2: Choose your deployment tool
        │
        ├─── Azure Portal (UI, beginner-friendly)
        ├─── Azure CLI (command line)
        ├─── Azure PowerShell
        └─── GitHub Actions / CI/CD pipeline
        │
        ▼
Step 3: Provide parameters (name, region, size, etc.)
        │
        ▼
Step 4: Azure Resource Manager validates the template
        │
        ├── ✅ Valid → Deploy resources
        └── ❌ Invalid → Show error, nothing is created
        │
        ▼
Step 5: Resources are created in Azure
        │
        ▼
Step 6: Outputs returned (URLs, IPs, IDs)
```

---

## 🖥️ How to Deploy (3 Ways)

### Option A — Azure Portal (Easiest for Beginners)

1. Go to [portal.azure.com](https://portal.azure.com)
2. Search for **"Deploy a custom template"**
3. Click **"Build your own template in the editor"**
4. Paste your JSON → Click **Save**
5. Fill in parameters → Click **Review + Create** → **Create**

---

### Option B — Azure CLI (Command Line)

```bash
# Step 1: Login to Azure
az login

# Step 2: Create a resource group (a folder for your resources)
az group create --name MyResourceGroup --location eastus

# Step 3: Deploy the template
az deployment group create \
  --resource-group MyResourceGroup \
  --template-file azuredeploy.json \
  --parameters storageAccountName=mystorage123
```

---

### Option C — Azure PowerShell

```powershell
# Login
Connect-AzAccount

# Deploy
New-AzResourceGroupDeployment `
  -ResourceGroupName "MyResourceGroup" `
  -TemplateFile "azuredeploy.json" `
  -storageAccountName "mystorage123"
```

---

## ⚙️ Two Deployment Modes

| Mode | What it does | When to use |
|---|---|---|
| **Incremental** (default) | Adds/updates resources in the template. Leaves other resources untouched. | Safe for most deployments |
| **Complete** | Deletes any resource NOT in the template. Only keeps what's defined. | Full environment resets |

> ⚠️ **Warning:** Complete mode can delete resources! Use with caution.

---

## 🧠 Key Concepts to Remember

| Concept | Simple Explanation |
|---|---|
| **Declarative** | You say *what* you want, not *how* to do it. Azure figures out the steps. |
| **Idempotent** | Run the same template 10 times → same result. No duplicates created. |
| **Linked Templates** | Break one big template into smaller reusable files. |
| **Template Functions** | Built-in helpers like `concat()`, `resourceGroup().location`, `parameters()` |
| **Resource Dependencies** | Use `dependsOn` to tell Azure "create this AFTER that" |

---

## 🔗 Resource Dependencies Example

If you need a storage account to exist **before** creating a web app, use `dependsOn`:

```json
{
  "type": "Microsoft.Web/sites",
  "name": "myWebApp",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
  ]
}
```

```
Storage Account ──creates first──▶ Web App (waits for storage)
```

> 💡 Like telling a contractor: "Install the pipes BEFORE the walls go up."

---

## 📊 ARM Template vs Manual Portal Setup

| | Manual (Portal Clicks) | ARM Template |
|---|---|---|
| Speed | Slow for repeat setups | Fast — one command |
| Consistency | Easy to make mistakes | Always identical |
| Reusability | Start over each time | Reuse for every environment |
| Team sharing | Hard to document | Share the JSON file |
| Version control | Not possible | Yes, store in Git |
| Automation/CI-CD | Not possible | Fully supported |

---

## 🗂️ Full Example — Complete ARM Template

Here's a complete, working ARM template that creates a Storage Account:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Unique name for the storage account (3-24 lowercase letters/numbers)"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Azure region for the storage account"
      }
    }
  },
  "variables": {
    "storageSku": "Standard_LRS"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageSku')]"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true
      }
    }
  ],
  "outputs": {
    "storageAccountEndpoint": {
      "type": "string",
      "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
    }
  }
}
```

---

## 🚀 Next Steps After Learning ARM Templates

```
ARM Templates (you are here)
        │
        ▼
Bicep ──────────────── Cleaner syntax, same power as ARM
        │
        ▼
Terraform ──────────── Multi-cloud IaC (Azure, AWS, GCP)
        │
        ▼
CI/CD Pipelines ─────── Auto-deploy templates via GitHub Actions
```

---

## 🔷 Bicep Basics — ARM's Cleaner Little Brother

### What is Bicep?

Bicep is a **newer, simpler way to write ARM templates**. Instead of writing long JSON files, you write cleaner, shorter code that Azure automatically converts to ARM JSON behind the scenes.

> 💡 Think of it like this: ARM JSON is like writing in machine code. Bicep is like writing in plain English. Same result, much easier to read and write.

---

### ARM JSON vs Bicep — Side by Side

**Same task: Create a Storage Account**

**ARM (JSON) — 20+ lines:**
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": { "name": "Standard_LRS" },
      "kind": "StorageV2",
      "properties": {}
    }
  ]
}
```

**Bicep — 8 lines:**
```bicep
param storageAccountName string

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-02-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: { name: 'Standard_LRS' }
  kind: 'StorageV2'
}
```

> ✅ Same result. Much cleaner. No `$schema`, no `[]` expression brackets, no JSON noise.

---

### Bicep Key Concepts

| Concept | ARM JSON | Bicep |
|---|---|---|
| Parameters | `"parameters": { "name": { "type": "string" } }` | `param name string` |
| Variables | `"variables": { "sku": "Standard_LRS" }` | `var sku = 'Standard_LRS'` |
| Outputs | `"outputs": { "id": { "value": "..." } }` | `output id string = storageAccount.id` |
| Resource | Long JSON block | `resource name 'type@apiVersion' = { }` |

---

### Bicep File Structure

```
main.bicep
├── param        ← inputs (like ARM parameters)
├── var          ← reusable values (like ARM variables)
├── resource     ← what to create (like ARM resources)
└── output       ← values to return (like ARM outputs)
```

---

### How to Deploy a Bicep File

```powershell
# Deploy using Azure CLI (same as ARM)
az deployment group create \
  --resource-group MyResourceGroup \
  --template-file main.bicep \
  --parameters storageAccountName=mystorage123
```

> ✅ Azure CLI automatically compiles Bicep → ARM JSON before deploying. You don't need to do anything extra.

---

### Convert Bicep ↔ ARM (useful for learning)

```powershell
# Bicep → ARM JSON (compile)
az bicep build --file main.bicep

# ARM JSON → Bicep (decompile)
az bicep decompile --file azuredeploy.json
```

---

### ARM vs Bicep — Which Should You Use?

| | ARM (JSON) | Bicep |
|---|---|---|
| Readability | Hard — verbose JSON | Easy — clean syntax |
| Length | Long | Short (50–70% less code) |
| Learning curve | Steeper | Easier for beginners |
| Azure support | Full | Full (same engine) |
| Recommended for | Legacy/existing templates | New projects ✅ |

> 💡 **Recommendation:** Learn ARM to understand the concepts, then use **Bicep** for actual work going forward.

---

## 🐳 Docker Container Deployment with ARM

### What is Docker? (Quick Intro)

Docker packages your app + all its dependencies into a **container** — a lightweight, portable box that runs the same everywhere.

```
Without Docker:                    With Docker:
"It works on my machine!" 😅      Works on every machine ✅
```

---

### How Docker + Azure Work Together

```
You write code
      │
      ▼
Package into Docker Image
      │
      ▼
Push to Azure Container Registry (ACR)
      │
      ▼
ARM Template deploys the container
      │
      ▼
Runs on Azure Container Instances (ACI)
      │
      ▼
Your app is live at a public URL 🌐
```

---

### ARM Template — Deploy a Docker Container

This template deploys a **public Docker image** (nginx web server) to Azure Container Instances:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerName": {
      "type": "string",
      "defaultValue": "my-container",
      "metadata": { "description": "Name for the container" }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('containerName')]",
      "location": "[parameters('location')]",
      "properties": {
        "containers": [
          {
            "name": "[parameters('containerName')]",
            "properties": {
              "image": "nginx:latest",
              "ports": [{ "port": 80 }],
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGB": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [{ "protocol": "TCP", "port": 80 }]
        },
        "restartPolicy": "Always"
      }
    }
  ],
  "outputs": {
    "containerPublicIP": {
      "type": "string",
      "value": "[reference(parameters('containerName')).ipAddress.ip]"
    }
  }
}
```

---

### Same Thing in Bicep

```bicep
param containerName string = 'my-container'
param location string = resourceGroup().location

resource containerGroup 'Microsoft.ContainerInstance/containerGroups@2021-09-01' = {
  name: containerName
  location: location
  properties: {
    containers: [
      {
        name: containerName
        properties: {
          image: 'nginx:latest'
          ports: [{ port: 80 }]
          resources: {
            requests: { cpu: 1, memoryInGB: 1 }
          }
        }
      }
    ]
    osType: 'Linux'
    ipAddress: {
      type: 'Public'
      ports: [{ protocol: 'TCP', port: 80 }]
    }
    restartPolicy: 'Always'
  }
}

output publicIP string = containerGroup.properties.ipAddress.ip
```

> ✅ Much cleaner! Same container, half the code.

---

### Key Docker + ARM Concepts

| Term | What it means |
|---|---|
| **Docker Image** | A snapshot/blueprint of your app (like a template) |
| **Docker Container** | A running instance of an image (like a deployed resource) |
| **Azure Container Registry (ACR)** | Azure's private hub to store your Docker images |
| **Azure Container Instances (ACI)** | Azure service to run containers without managing servers |
| **nginx** | A popular web server used as a demo container image |

---

### Container Deployment Flow

```
Step 1: docker build -t myapp .          ← Build image locally
        │
        ▼
Step 2: docker push myregistry.azurecr.io/myapp   ← Push to ACR
        │
        ▼
Step 3: ARM/Bicep template references the image
        │
        ▼
Step 4: az deployment group create ...   ← Deploy to ACI
        │
        ▼
Step 5: Container runs → Public IP assigned → App is live 🎉
```

---

## 📚 Helpful Resources

| Resource | Link |
|---|---|
| ARM Template Docs | https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/ |
| ARM Template Reference | https://learn.microsoft.com/en-us/azure/templates/ |
| ARM Quickstart Templates | https://github.com/Azure/azure-quickstart-templates |
| Azure Bicep Docs | https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/ |
| Azure Container Instances | https://learn.microsoft.com/en-us/azure/container-instances/ |
| Docker Getting Started | https://docs.docker.com/get-started/ |

---

> 📝 **Summary:** ARM templates are JSON files that define Azure infrastructure as code. Bicep is the modern, cleaner alternative that compiles down to ARM. Both can deploy Docker containers to Azure Container Instances — giving you a fully automated, repeatable deployment pipeline.

---
*Created as part of Day 3-4, Phase 1 — Cloud Foundations Practice*
*Updated: Bicep Basics + Docker Container Deployment added*
