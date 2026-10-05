# FROM python:3.14-slim-bookworm
FROM ghcr.io/astral-sh/uv:python3.14-bookworm-slim

WORKDIR /app
# RUN pip install --no-cache-dir uv

# Copy workspace root files for dependency resolution
COPY pyproject.toml uv.lock ./
# COPY apps/api/pyproject.toml apps/api/uv.lock ./apps/api/
COPY apps/api/pyproject.toml ./apps/api/

# Copy package files and source
COPY apps/api ./apps/api

ENV UV_COMPILE_BYTECODE=1

# Install dependencies including workspace packages
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-dev --package api

# Enable bytecode compilation and Python optimization
ENV PYTHONOPTIMIZE=1
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

# Set PATH to use the virtual environment
ENV PATH="/app/.venv/bin:$PATH"
ENV PYTHONPATH="/app/apps/api/src:$PYTHONPATH"

# Create non-root user and set permissions
RUN addgroup --system app && \
    adduser --system --ingroup app app && \
    chown -R app:app /app

# Switch to non-root user
USER app

# Expose the FastAPI port
EXPOSE 8000

WORKDIR /app/apps/api/src

# Command to run the application
CMD ["uvicorn", "api.app:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]