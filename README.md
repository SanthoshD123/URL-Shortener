# URL Shortener

A simple URL shortener service built with Flask that allows you to create short URLs and track click statistics.

## Features

- **URL Shortening**: Convert long URLs into short, manageable links
- **Click Tracking**: Monitor how many times each shortened URL has been accessed
- **Simple API**: RESTful endpoints for easy integration
- **SQLite Database**: Lightweight database storage for URLs and statistics

## Project Structure

```
url-shortener/
├── app.py          # Main Flask application
├── models.py       # Database models
├── utils.py        # Utility functions
└── database.db     # SQLite database (created automatically)
```

## Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd url-shortener
   ```

2. **Create a virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install flask flask-sqlalchemy
   ```

## Usage

### Running the Application

Start the Flask development server:

```bash
python app.py
```

The application will be available at `http://localhost:5000`

### API Endpoints

#### 1. Shorten a URL

**POST** `/shorten`

Create a short URL from a long URL.

**Request Body:**
```json
{
  "url": "https://example.com/very/long/url/that/needs/shortening"
}
```

**Response:**
```json
{
  "short_url": "http://localhost:5000/abc123"
}
```

**Example using curl:**
```bash
curl -X POST http://localhost:5000/shorten \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com"}'
```

#### 2. Access Shortened URL

**GET** `/<short_code>`

Redirect to the original URL and increment click counter.

**Example:**
```
GET http://localhost:5000/abc123
```

This will redirect to the original URL and increment the click count.

### Database Schema

The application uses a single table `URLMap` with the following structure:

| Column | Type | Description |
|--------|------|-------------|
| id | Integer | Primary key |
| original_url | String(500) | The original long URL |
| short_code | String(10) | The generated short code (unique) |
| clicks | Integer | Number of times the URL has been accessed |

## Configuration

The application uses the following default configurations:

- **Database**: SQLite (`sqlite:///database.db`)
- **Short Code Length**: 6 characters
- **Character Set**: Alphanumeric (a-z, A-Z, 0-9)

### Customizing Short Code Length

To change the default short code length, modify the `generate_short_code()` function call in `utils.py`:

```python
def generate_short_code(length=8):  # Change from 6 to 8
    chars = string.ascii_letters + string.digits
    return ''.join(random.choice(chars) for _ in range(length))
```

## Error Handling

The API returns appropriate HTTP status codes:

- **400 Bad Request**: When URL is missing from request
- **404 Not Found**: When short code doesn't exist
- **200 OK**: Successful operations

## Development

### Database Initialization

The database tables are automatically created when the application starts for the first time. The `@app.before_first_request` decorator ensures tables are created before handling any requests.

### Adding New Features

To extend the application, consider adding:

- Custom short codes
- Expiration dates for URLs
- User authentication
- Analytics dashboard
- Bulk URL shortening
- URL validation

## Security Considerations

For production use, consider implementing:

- Rate limiting to prevent abuse
- URL validation to prevent malicious links
- HTTPS enforcement
- Database connection pooling
- Input sanitization

## License

This project is open source and available under the MIT License.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## Support

For issues and questions, please open an issue in the repository.
