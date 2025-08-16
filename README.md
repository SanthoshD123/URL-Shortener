# 🔗 URL Shortener

A lightweight, fast URL shortener service built with Flask that transforms long URLs into short, shareable links with built-in analytics.

![Python](https://img.shields.io/badge/python-3.7+-blue.svg)
![Flask](https://img.shields.io/badge/flask-2.0+-green.svg)
![License](https://img.shields.io/badge/license-MIT-blue.svg)

## ✨ Features

- **🎯 URL Shortening**: Convert long URLs into short, manageable links
- **📊 Click Tracking**: Monitor how many times each shortened URL has been accessed
- **🚀 Simple API**: RESTful endpoints for easy integration
- **💾 SQLite Database**: Lightweight database storage with zero configuration
- **⚡ Fast Performance**: Minimal overhead and quick redirects
- **🔧 Easy Setup**: Get running in under 5 minutes

## 🏗️ Project Structure

```
url-shortener/
├── app.py              # Main Flask application
├── models.py           # Database models (URLMap)
├── utils.py            # Utility functions (short code generation)
├── requirements.txt    # Python dependencies
├── README.md          # Project documentation
└── database.db        # SQLite database (auto-generated)
```

## 🚀 Quick Start

### Prerequisites

- Python 3.7 or higher
- pip (Python package manager)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd url-shortener
   ```

2. **Create and activate virtual environment**
   ```bash
   # Create virtual environment
   python -m venv venv
   
   # Activate it
   # On macOS/Linux:
   source venv/bin/activate
   
   # On Windows:
   venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application**
   ```bash
   python app.py
   ```

5. **Access the service**
   ```
   🌐 Server running at: http://localhost:5000
   ```

## 📚 API Documentation

### Create Short URL

**Endpoint:** `POST /shorten`

Converts a long URL into a shortened version.

**Request:**
```bash
curl -X POST http://localhost:5000/shorten \
  -H "Content-Type: application/json" \
  -d '{"url": "https://www.example.com/very/long/path/to/content"}'
```

**Response:**
```json
{
  "short_url": "http://localhost:5000/abc123"
}
```

**Status Codes:**
- `200` - Successfully created short URL
- `400` - Missing or invalid URL in request body

### Access Short URL

**Endpoint:** `GET /<short_code>`

Redirects to the original URL and increments the click counter.

**Example:**
```bash
curl -L http://localhost:5000/abc123
```

**Behavior:**
- Redirects to original URL with `302 Found` status
- Increments click counter automatically
- Returns `404 Not Found` if short code doesn't exist

## 🗄️ Database Schema

The application uses SQLite with a simple, efficient schema:

### URLMap Table

| Column       | Type          | Constraints                    | Description                    |
|--------------|---------------|--------------------------------|--------------------------------|
| `id`         | Integer       | Primary Key, Auto-increment    | Unique identifier              |
| `original_url` | String(500) | Not Null                       | The original long URL          |
| `short_code` | String(10)    | Unique, Not Null               | Generated short identifier     |
| `clicks`     | Integer       | Default: 0                     | Number of times URL accessed   |

## ⚙️ Configuration

### Environment Variables

You can customize the application using environment variables:

```bash
# Database configuration
export SQLALCHEMY_DATABASE_URI="sqlite:///custom_database.db"

# Debug mode
export FLASK_DEBUG=True

# Application host and port
export FLASK_HOST="0.0.0.0"
export FLASK_PORT="5000"
```

### Short Code Customization

Modify `utils.py` to change short code generation:

```python
def generate_short_code(length=8):  # Change length (default: 6)
    chars = string.ascii_letters + string.digits
    return ''.join(random.choice(chars) for _ in range(length))
```

**Character Set Options:**
- `string.ascii_letters` - a-z, A-Z (52 characters)
- `string.digits` - 0-9 (10 characters)
- `string.ascii_lowercase` - a-z only (26 characters)

## 📊 Usage Examples

### Basic URL Shortening

```python
import requests

# Shorten a URL
response = requests.post('http://localhost:5000/shorten', 
                        json={'url': 'https://github.com'})
short_url = response.json()['short_url']
print(f"Short URL: {short_url}")
```

### Batch URL Shortening

```python
import requests

urls = [
    'https://github.com',
    'https://stackoverflow.com',
    'https://developer.mozilla.org'
]

short_urls = []
for url in urls:
    response = requests.post('http://localhost:5000/shorten', 
                           json={'url': url})
    short_urls.append(response.json()['short_url'])

print("Shortened URLs:", short_urls)
```

## 🔧 Development

### Running Tests

```bash
# Install test dependencies
pip install pytest pytest-flask

# Run tests
pytest tests/
```

### Database Management

```python
# Access database directly
from app import app
from models import db, URLMap

with app.app_context():
    # Get all URLs
    urls = URLMap.query.all()
    
    # Find specific URL
    url = URLMap.query.filter_by(short_code='abc123').first()
    
    # Get click statistics
    total_clicks = db.session.query(db.func.sum(URLMap.clicks)).scalar()
```

## 🚀 Deployment

### Production Considerations

1. **Use a production WSGI server:**
   ```bash
   pip install gunicorn
   gunicorn -w 4 -b 0.0.0.0:8000 app:app
   ```

2. **Environment variables for production:**
   ```bash
   export FLASK_ENV=production
   export SQLALCHEMY_DATABASE_URI="postgresql://user:pass@localhost/db"
   ```

3. **Add reverse proxy (nginx):**
   ```nginx
   server {
       listen 80;
       location / {
           proxy_pass http://127.0.0.1:8000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
       }
   }
   ```

### Docker Deployment

Create a `Dockerfile`:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

```bash
# Build and run
docker build -t url-shortener .
docker run -p 5000:5000 url-shortener
```

## 🔐 Security Notes

**For production use, implement:**

- ✅ Rate limiting to prevent abuse
- ✅ URL validation and sanitization  
- ✅ HTTPS enforcement
- ✅ Input validation and SQL injection protection
- ✅ CORS configuration for web applications
- ✅ Authentication for administrative endpoints

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Commit changes: `git commit -am 'Add feature'`
4. Push to branch: `git push origin feature-name`
5. Submit a pull request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🛠️ Future Enhancements

- [ ] Custom short codes
- [ ] Expiration dates for URLs
- [ ] User authentication and URL management
- [ ] Analytics dashboard with charts
- [ ] Bulk URL operations
- [ ] QR code generation
- [ ] API key authentication
- [ ] URL preview and safety checking


