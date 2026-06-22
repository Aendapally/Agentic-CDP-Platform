FROM python:3.11-slim

WORKDIR /app

# Install system dependencies
#  - graphviz: required for diagram PNG rendering
#  - pipx/uv: required for MCP servers (uvx command)
RUN apt-get update && apt-get install -y \
    curl \
    git \
    graphviz \
    pipx \
    && rm -rf /var/lib/apt/lists/* \
    && pip install --no-cache-dir uv

# Copy requirements first to leverage Docker cache
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code and configuration
COPY src/ ./src/
COPY .agent.yaml .
COPY container_entrypoint.py .

# Copy sample test data for tfstate-to-diagram testing
COPY test_data/ ./test_data/

# Copy pre-built ADT chat UI
COPY adt-ui/ ./adt-ui/

# Set environment variables
ENV PYTHONPATH=/app
ENV PYTHONUNBUFFERED=1
ENV HOST=0.0.0.0
ENV PORT=8000

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# Use the container entrypoint which provides full dev server functionality
CMD ["python", "container_entrypoint.py"]