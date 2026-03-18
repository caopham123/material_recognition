# ==========================================
# STAGE 1: BUILDER
# ==========================================
FROM python:3.12-slim AS builder

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
WORKDIR /app

# Cài đặt compiler cho OS
RUN apt-get update --fix-missing && apt-get install -y --no-install-recommends \
    gcc \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Tạo môi trường ảo venv chuẩn PEP 668
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN pip install --upgrade pip && \
    pip install --no-cache-dir torch==2.2.1 torchvision==0.17.1

# Cài đặt lib Python
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# ==========================================
# STAGE 2: RUNTIME (DÀNH CHO SERVER_CORE)
# ==========================================
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
WORKDIR /app

# ĐÃ XÓA BỎ LỆNH CÀI ĐẶT libgl1... VÌ SERVER KHÔNG CẦN DÙNG ĐẾN ĐỒ HỌA
COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

COPY ./src /app/src
COPY alembic.ini /app/
# COPY ./weights /app/weights

EXPOSE 8000

# ĐÃ SỬA LỖI TÁCH CHUỖI "--host 0.0.0.0"
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]