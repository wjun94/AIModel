## Docker部署compyUI

### 完整部署步骤（关键配置）

#### 1. 目录结构准备（与 Dockerfile 同级）

```
your-project/
├── Dockerfile
└── comfyui-models/        # 主机模型根目录（名称可自定义，但需与挂载命令一致）
    └── checkpoints/       # ComfyUI识别的模型子目录（固定名称）
        ├── model.safetensors  # 模型文件
        └── model.ckpt
```

#### 2. 构建 Docker 镜像

```bash
docker build -t comfyui .
```

#### 3. 运行容器并挂载模型目录（核心命令）

```bash
docker run -d \
  -p 8188:8188 \
  -v $(pwd)/comfyui-models:/models \  # 主机目录映射到容器内/models（包含checkpoints子目录）
  --gpus all --runtime=nvidia \        # 如需GPU支持（需提前安装nvidia-docker）
  --name comfyui \
  comfyui
```

```bash
docker run --gpus all --runtime=nvidia -d -p 8188:8188 -v /comfyui-models:/models --name comfyui comfyui
```

#### 4. 访问 ComfyUI 界面

打开浏览器访问 `http://localhost:8188`，ComfyUI 会自动扫描 `/models/checkpoints` 目录，模型将显示在节点编辑器的模型加载选项中（如`CheckpointLoader`节点）。

### 关键技术点解析

1. **模型路径映射逻辑**

   - 容器内固定模型路径：`/models/checkpoints`（ComfyUI 默认扫描此路径，无需额外配置）
   - 主机目录结构：必须包含`checkpoints`子目录，与容器内路径严格对应
   - 挂载命令`-v $(pwd)/comfyui-models:/models`：将主机的`comfyui-models`目录整体映射到容器的`/models`，确保`checkpoints`子目录正确生效

2. **VOLUME 指令的作用**

   - 在 Dockerfile 中声明`VOLUME /models`，表示该目录用于外部数据挂载
   - 避免模型文件被打包进镜像，支持动态更新（修改主机文件后重启容器即可生效）

3. **兼容性支持**

   - 支持 ComfyUI 兼容的所有模型格式：`.safetensors`、`.ckpt`、`.pt`等
   - 模型文件直接放在`checkpoints`目录即可，无需复杂目录结构

4. **GPU 环境配置（可选）**
   - 需提前安装[nvidia-docker runtime](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html)
   - 命令中`--gpus all --runtime=nvidia`确保容器获取 GPU 资源，提升模型加载和推理速度




### 注意事项

1. **路径正确性**

   - 确保`$(pwd)`指向 Dockerfile 所在目录，避免挂载路径错误
   - 模型目录名称`comfyui-models`可自定义，但需与挂载命令中的主机路径一致

2. **首次运行耗时**

   - 首次构建会下载 ComfyUI 依赖（约 1.5GB），耗时 5-10 分钟
   - 首次运行容器时，ComfyUI 会自动扫描模型目录，大型模型加载可能需要额外时间

3. **模型更新**
   - 直接替换主机`comfyui-models/checkpoints`下的模型文件
   - 无需重建镜像，重启容器即可生效：`docker restart comfyui`

通过以上配置，ComfyUI 会在启动时自动加载指定目录下的所有兼容模型，无需手动配置路径，实现高效的模型管理和容器化部署。

# 废弃

## 运行容器

80 为本地端口 8188 为容器端口 -d 容器在后台运行

```bash
docker run --gpus all --runtime=nvidia -p 80:8188 -d comfyui
```

使用 -- 主机目录映射到容器的/models（包含 checkpoints 子目录）

```bash
docker run -d -v ./comfyui-models:/models comfyui
```
