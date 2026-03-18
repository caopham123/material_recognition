# ===================
# STAGE 1: BUILDER
# ===================
FROM python3:12-slim as builder

# Tối ưu hóa Python
ENV PYTHONDONTWRITEBYTECODE=1 
ENV PYTHONUNBUFFERED=1
WORKDIR /app

# 1. Cài đặt compiler cho OS
RUN apt-get update && apt-get-install -y --no-install-recommends \
    gcc build-essential \
    && rm -rf /var/lib/apt/lists/*

# 2. Tạo môi trường ảo venv chuẩn PEP 668
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# 3. Cài đặt lib Python
COPY requirements.txt .
RUN pip install --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# =====================
# STAGE 2: RUNTIME
# =====================
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# 4. Cài đặt core lib cho OpenCV/YOLO
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1-mesa-glx \
    libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

# 5. Copy VENV từ Stage 1
COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# 5. Copy sources
COPY ./src /app/src
# COPY ./weights /app/weights

# DON'T EXPOSE AND RUN UVICORN
# Because this is Client (sender)
CMD ["python","src/main.py"]