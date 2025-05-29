# Use a more optimized base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy requirements first for better layer caching
COPY requirements.txt .

# Install dependencies with optimization flags
RUN pip install --no-cache-dir --disable-pip-version-check -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 80

# Run the application
CMD ["fastapi", "run", "main.py", "--port", "80"]