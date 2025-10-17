
# HIS医院信息系统架构图

## 系统概述

本架构图展示了HIS（Hospital Information System）医院信息系统的整体架构设计，采用分层架构模式，从上到下包含用户交互层、API层、业务应用层、中间件层、核心遗留系统区和IT管理团队。

## 架构层次说明

### 1. 用户与外部交互层
- **患者/导诊** → 患者门户/移动应用
- **门诊** → 门诊接口  
- **住院部** → 临床应用接口
- **药房** → 药品管理接口
- **自助设备** → 检查报告影像接口
- **管理人员** → 后台管理系统
- **合作机构** → 外部API接口

### 2. API层
- API网关：统一入口，负责请求路由和负载均衡

### 3. API/数据安全层
- API/数据安全网关：安全认证和授权
- API/数据安全监控：实时监控和安全审计

### 4. 业务应用层
- **临床支持系统**：心电信息系统、病理管理系统、一体化体检中心、药房管理系统、手术麻醉系统、电子病历系统
- **行政与运营系统**：缴费平台、排班管理、系统管理、文档管理、Nginx

### 5. 中间件层
- 消息队列中间件：负责系统间数据同步和异步处理

### 6. 核心遗留系统区
- HIS核心数据库/Oracle：核心数据存储
- 遗留业务系统/VB：传统业务逻辑
- SMB存储：文件存储服务

### 7. IT系统管理团队
- IT系统管理员：系统运维和管理

## 系统架构图

```mermaid
    graph TD
        %% 1. 用户层 (最上层)
        subgraph 用户与外部交互层 [用户与外部交互层]
            A[患者/导诊] --> B{患者门户/移动应用}
            Q[门诊] --> R{门诊接口}
            C[住院部] --> D{临床应用接口}
            S[药房] --> T{药品管理接口}
            U[自助设备] --> V{检查报告影像接口}
            E[管理人员] --> F{后台管理系统}
            G[合作机构] --> H{外部API接口}
        end

        %% 2. API层
        K[API网关]

        %% 3. API/数据安全层
        I[API/数据安全网关]
        J[API/数据安全监控]

        %% 4. 业务应用层
        subgraph 业务应用层 [业务应用层]
            direction TB
            subgraph 临床支持系统
                S2[心电信息系统 - 192.168.161.76]
                S3[病理管理系统 - 192.168.0.218]
                S4[一体化体检中心 - 192.168.0.114]
                S5[药房管理系统 - 192.168.161.131]
                S6[手术麻醉系统 - 192.168.0.90]
                S7[凯龙电子病历-心电用 - 192.168.0.207]
            end
            subgraph 行政与运营系统
                S8[大额数字化综合缴费平台 - 192.168.0.235]
                S9[排班管理平台 - 192.168.0.114]
                S10[系统管理平台 - 192.168.0.116]
                S11[文档管理系统 - 139.210.1.83]
                S12[Nginx - 192.168.0.195]
            end
        end

        %% 5. 中间件层
        L[消息队列中间件]

        %% 6. 核心遗留系统区 (最下层)
        subgraph 核心遗留系统区 [核心遗留系统区]
            direction LR
            M[HIS核心数据库/Oracle]
            N[遗留业务系统/VB]
            O[SMB存储]
        end

        %% 7. IT系统管理团队 (最底层)
        subgraph IT系统管理团队 [IT系统管理团队]
            P[IT系统管理员]
        end

        %% 数据流向连接 (从上到下)
        B & D & F & H & R & T & V -- HTTPS/TCP --> K
        K --> I
        I -- 交换机镜像流量 --> J
        I -- 路由 --> S2 & S3 & S4 & S5 & S6 & S7 & S8 & S9 & S10 & S11 & S12
        S2 & S3 & S4 & S5 & S6 & S7 & S8 & S9 & S10 & S11 & S12 -- 数据请求 --> L
        L -- MQ订阅(数据消费) --> M
        N -- MQ发布(数据同步) --> L
        M -- C/S 数据库访问 --> N
        N -- 文件存储访问 --> O
        P -- 系统管理 --> N

        %% 样式设置
        style A fill:#cce5ff,stroke:#333,stroke-width:2px
        style C fill:#cce5ff,stroke:#333,stroke-width:2px
        style E fill:#cce5ff,stroke:#333,stroke-width:2px
        style G fill:#cce5ff,stroke:#333,stroke-width:2px
        style Q fill:#cce5ff,stroke:#333,stroke-width:2px
        style S fill:#cce5ff,stroke:#333,stroke-width:2px
        style U fill:#cce5ff,stroke:#333,stroke-width:2px
        style K fill:#e1f5fe,stroke:#0277bd,stroke-width:2px
        style I fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
        style J fill:#d5e8d4,stroke:#82b366,stroke-width:2px
        style L fill:#fff2cc,stroke:#d6b656,stroke-width:2px
        style M fill:#ffcccc,stroke:#b30000,stroke-width:2px
        style N fill:#ffcccc,stroke:#b30000,stroke-width:2px
        style O fill:#ffe6cc,stroke:#d79b00,stroke-width:2px
        style P fill:#f0e6ff,stroke:#8e24aa,stroke-width:2px
        
        %% 子图背景颜色设置
        classDef adminSystem fill:#e8f4fd,stroke:#1976d2,stroke-width:2px
        class S8,S9,S10,S11,S12 adminSystem
```

## 技术特点

- **分层架构**：采用清晰的分层设计，便于维护和扩展
- **微服务化**：业务应用模块化，支持独立部署和扩展
- **安全防护**：多层安全防护，包括API网关、数据安全监控
- **数据同步**：通过消息队列实现系统间数据同步
- **遗留系统集成**：有效整合传统VB系统和现代微服务架构

## 部署说明

- 各业务系统部署在独立的服务器上，通过IP地址标识
- 核心数据库使用Oracle，确保数据一致性和可靠性
- 文件存储采用SMB协议，支持大容量文件管理
- IT管理团队负责系统运维、监控和维护

