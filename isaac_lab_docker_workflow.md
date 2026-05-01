# Isaac Lab Docker Daily Workflow

本文档只记录**保留 container、不删除环境**的日常使用方式。

核心原则：日常不要使用下面这个命令：

```bash
./docker/container.py stop
# 不推荐日常使用
# 这个命令会移除 container，并可能删除 volumes/cache
```

日常停止环境请使用：

```bash
docker stop isaac-lab-base
# 只停止 container，不删除 container / image / volume
```

---

## 1. 进入 Isaac Lab 项目目录

```bash
cd ~/Projects/IsaacLab
# 进入本机上的 Isaac Lab 仓库目录
```

---

## 2. 第一次启动 container

如果 `isaac-lab-base` container 还不存在，执行：

```bash
./docker/container.py start
# 根据 Isaac Lab 的 Docker 配置创建并启动 container
# 如果 image 已存在，一般不会重新完整 build
```

然后进入 container：

```bash
./docker/container.py enter base
# 进入正在运行的 isaac-lab-base container
```

---

## 3. 运行 Isaac Lab 测试

进入 container 后，运行：

```bash
./isaaclab.sh -p scripts/tutorials/00_sim/log_time.py --headless
# 使用 Isaac Lab 的 Python 环境运行 headless 测试脚本
# --headless 表示不开 GUI，只在后台运行仿真
```

如果要停止当前脚本：

```bash
Ctrl + C
# 中断当前运行的 Isaac Lab 脚本
```

---

## 4. 退出 container shell

在 container 内执行：

```bash
exit
# 退出当前 container shell
# 不会删除 container，也不会删除 image / volume
```

---

## 5. 暂停 Isaac Lab container

回到宿主机终端后执行：

```bash
docker stop isaac-lab-base
# 停止 isaac-lab-base container
# 保留 container、image、volume/cache
```

---

## 6. 下次重新使用

进入 Isaac Lab 目录：

```bash
cd ~/Projects/IsaacLab
# 回到 Isaac Lab 仓库目录
```

启动已有 container：

```bash
docker start isaac-lab-base
# 启动之前保留下来的 isaac-lab-base container
```

进入 container：

```bash
./docker/container.py enter base
# 进入已经启动的 container
```

---

## 7. 查看 container 是否存在

```bash
docker ps -a
# 查看所有 container，包括已经停止的 container
```

如果能看到：

```text
isaac-lab-base
```

说明 container 还在。

如果它是停止状态，执行：

```bash
docker start isaac-lab-base
# 启动已存在但停止的 container
```

如果找不到 `isaac-lab-base`，说明 container 已经被删除，需要重新创建：

```bash
cd ~/Projects/IsaacLab
# 进入 Isaac Lab 仓库

./docker/container.py start
# 重新创建并启动 container

./docker/container.py enter base
# 进入 container
```

---

## 8. 推荐日常完整流程

### 开始工作

```bash
cd ~/Projects/IsaacLab
# 进入 Isaac Lab 仓库目录

docker start isaac-lab-base
# 启动已有 container，不重新创建环境

./docker/container.py enter base
# 进入 Isaac Lab container
```

### 运行任务

```bash
./isaaclab.sh -p your_script.py --headless
# 在 Isaac Lab 环境中运行自己的脚本
```

### 结束工作

```bash
exit
# 退出 container shell

docker stop isaac-lab-base
# 停止 container，但不删除环境
```

---

## 9. 不推荐的日常命令

```bash
./docker/container.py stop
# 不推荐日常使用
# 这个命令会移除 container，并可能删除 volumes/cache
```

只有在你想彻底清理 Isaac Lab Docker 运行环境时，才考虑使用它。
