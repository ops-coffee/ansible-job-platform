# ansible-job-platform：一个强大的运维自动化作业平台

[![Python Version](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Django Version](https://img.shields.io/badge/Django-3.2+-green.svg)](https://www.djangoproject.com/)
[![Ansible](https://img.shields.io/badge/Ansible-2.9+-red.svg)](https://www.ansible.com/)
[![License](https://img.shields.io/badge/License-MIT-brightgreen.svg)](LICENSE)

`ansible-job-platform` 是一个基于 **Django** 开发、深度集成 **Ansible** 的企业级运维自动化平台。

它提供了一个现代化的 Web 界面，帮助运维团队将 Ansible 自动化能力平台化、自助化。它不仅仅是一个 Ansible 的 Web UI，更是一个集成了 **CMDB资产管理**、**SSH凭据/网关管理**、**任务调度** 和 **审计日志** 的一站式解决方案。

---

## 🚀 核心功能

本平台围绕 Ansible 自动化构建了一套完整的运维生态。

### 1. 资产管理 (CMDB)

* **业务关系树 (`Relation`):**
    * 提供灵活的多级业务树来组织和归类服务器资产，替代传统的 `hosts` 文件。
    * 资产（`Instance`）可与业务节点绑定，方便按业务批量执行任务。
* **服务器实例 (`Instance`):**
    * 集中管理所有服务器实例，包含内网IP、外网IP、实例状态等。
    * 支持从云厂商（`cloud_id`）或其他来源导入和同步资产。

### 2. 安全与连接

* **凭据管理 (`Account`):**
    * 集中管理服务器的 SSH 登录凭据（用户名、私钥）。
    * 私钥（`private_key`）在数据库中通过 **AES 加密** 存储，确保安全。
* **网关管理 (`Gateway`):**
    * 内置 SSH 网关（跳板机）管理功能。
    * 允许 Ansible 任务通过指定的网关（`Gateway`）连接到处于内网或隔离区的服务器（`InstanceConnect`）。
* **连接配置 (`InstanceConnect`):**
    * 可为每台服务器单独指定 SSH 端口、登录用户（`ssh_user`）和连接网关（`gateway`）。

### 3. Ansible 自动化作业

* **Ad-Hoc 即时命令:**
    * 支持在选定的主机上快速执行 Ansible 模块（`shell`, `copy`, `file`, `yum` 等）。
* **Playbook 管理 (`Playbook`):**
    * 提供在线的 Playbook 编辑器，支持语法高亮。
    * **版本控制:** 自动保存 Playbook 的历史版本（`PlaybookHistory`），支持查看 `diff` 和回滚。
* **脚本管理 (`Script`):**
    * 提供在线的脚本（Shell, Python 等）编辑器。
    * **版本控制:** 同样支持脚本的历史版本管理（`ScriptHistory`）。
    * 在 `ansible` 模块（如 `script` 或 `shell`）中可方便调用。

### 4. 任务调度与执行

* **作业任务 (`AnsibleTask`):**
    * 将 Ad-hoc 命令或 Playbook 保存为可重复执行的 "作业模板"。
    * 支持在执行前指定额外参数（`args`）。
* **周期性任务 (Celery Beat):**
    * 基于 `django-celery-beat` 深度集成，可将任何 `AnsibleTask` 设置为周期性任务。
    * 支持标准的 Crontab 表达式（`crontab`），实现无人值守自动化运维。
* **命令过滤 (`CommandFilterRule`):**
    * 可配置命令过滤规则（如正则表达式），用于限制高危命令的执行，增强安全性。

### 5. 审计与日志

* **作业日志 (`AnsibleTaskLog`):**
    * 详细记录每一次作业（`AnsibleTask`）的执行历史。
    * 实时（通过 Celery）更新任务状态：`进行中`、`成功`、`失败` 等。
    * 完整保存 Ansible 执行的原始输出（`details`）。
* **任务重做:**
    * 支持对已失败或已完成的任务进行 "Redo"（重新执行）。
* **结果通知:**
    * 可配置任务执行结果的通知策略（`notify_level`），并将结果发送到指定通知组（`notify_group`）。

## 🚀 快速开始

1. 安装docker compose

```
curl -fsSL https://get.docker.com -o get-docker.sh | sudo sh
```

新版本docker已经默认安装了docker compose无需额外安装

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

2. 拉取deploy部署代码

```
git clone https://github.com/ops-coffee/ansible-job-platform.git
cd ansible-job-platform
```

3. 启动docker compose

```
docker-compose up -d
```

4. 浏览器访问：`https://ip_or_domain:8001`

默认为8001端口，首次部署有数据初始化等操作，大约会占用几分钟时间，需等待完全启动后访问，可通过查看`ops-app`容器日志，输出`Start ok. ^_^`后表示启动完成

```
# docker logs ops-app -n 10
👤 创建管理员账号...
🚀 启动服务...
Starting supervisor: supervisord.
Mon Oct 27 18:57:31 CST 2025 ✅ Start ok. ^_^
```

5. 登录账号密码
   - 账号：admin@ops-coffee.com
   - 密码：ops-coffee.com
