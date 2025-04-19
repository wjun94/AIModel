# 使用 PyTorch 官方镜像作为基础（包含 CUDA 支持）
FROM pytorch/pytorch:2.3.0-cuda12.1-cudnn8-runtime

# 正确设置NVIDIA环境变量（移除注释，单独写值）
# GPU环境变量（可选，需nvidia-docker）
# 推荐只启用计算功能（避免权限问题）
ENV NVIDIA_VISIBLE_DEVICES all        
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility 

# 安装基础依赖
RUN apt-get update && apt-get install -y \
    git \
    wget \
    libgl1 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# 创建工作目录和容器内模型目录（严格匹配ComfyUI扫描逻辑）
WORKDIR /ComfyUI
# ComfyUI默认扫描的模型存储路径
RUN mkdir -p /models/checkpoints     
# 定义容器内模型挂载点（不包含主机路径）  
VOLUME /models                        

# 克隆ComfyUI仓库
RUN git clone https://github.com/comfyanonymous/ComfyUI.git .

# 安装依赖（如果有 requirements.txt）
RUN pip install --no-cache-dir -r requirements.txt

# 创建工作目录和容器内模型目录（固定路径）
WORKDIR /ComfyUI
# 创建模型存储目录
RUN mkdir -p /models/checkpoints
VOLUME /models  # 用于挂载外部模型目录

COPY ./comfyui_models/checkpoints/ /ComfyUI/models/checkpoints/

CMD ["python", "main.py", "--listen", "0.0.0.0", "--port", "8188"]

