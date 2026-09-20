# ── Stage 1: Builder ──
FROM python:3.11-slim AS builder

WORKDIR /app

# Install build dependencies for C-extensions (bcrypt, cryptography)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    libffi-dev \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# ── Stage 2: Minimal Runtime ──
FROM python:3.11-slim AS runner

WORKDIR /app

# Security: non-root user
RUN useradd -m -u 1000 appuser

# Copy installed Python packages from builder
COPY --from=builder /root/.local /home/appuser/.local

# Copy application source code
COPY --chown=appuser:appuser . /app

# Create upload directory with non-root ownership
RUN mkdir -p /tmp/studyspark_uploads && chown -R appuser:appuser /tmp/studyspark_uploads

ENV PATH=/home/appuser/.local/bin:$PATH \
    PYTHONUNBUFFERED=1

USER appuser

EXPOSE 8000

CMD ["uvicorn", "server:app", "--host", "0.0.0.0", "--port", "8000"]
