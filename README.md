# 🔗 URL Shortener with Go and Redis

A high-performance URL shortener service built with Go (Fiber framework) and Redis. This service allows you to shorten long URLs into compact, shareable links with features like rate limiting, custom short URLs, and automatic expiration.

## ✨ Features

- **URL Shortening**: Convert long URLs into short, shareable links
- **Custom Short URLs**: Option to create custom short URL aliases
- **Rate Limiting**: IP-based rate limiting (10 requests per 30 minutes by default)
- **URL Expiration**: Set expiration time for shortened URLs (default: 24 hours)
- **HTTPS Enforcement**: Automatically enforces HTTPS for all URLs
- **Domain Protection**: Prevents shortening of the service's own domain
- **Docker Support**: Easy deployment with Docker Compose
- **Redis Backend**: Fast and efficient storage using Redis

## 🏗️ Architecture

- **Framework**: [Fiber v2](https://github.com/gofiber/fiber) - Express-inspired web framework
- **Database**: Redis - In-memory data store
- **Language**: Go 1.25.6

## 📋 Prerequisites

- Go 1.25.6 or higher
- Docker and Docker Compose (for containerized deployment)
- Redis (if running locally without Docker)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## 📡 API Endpoints

### 1. Shorten URL
**POST** `/api/v1`
Creates a shortened URL from a long URL.

**Request Body:**
{
  "url": "https://example.com/very/long/url",
  "short": "custom",  // Optional: custom short URL
  "expiry": 24        // Optional: expiry in hours (default: 24)
}

**Response:**
{
  "url": "https://example.com/very/long/url",
  "short": "http://localhost:3000/abc123",
  "expiry": 24,
  "rate_limit": 9,
  "rate_limit_reset": 29
}

**Example using cURL:**
curl -X POST http://localhost:3000/api/v1 \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/very/long/url",
    "expiry": 48
  }'
  
### 2. Resolve URL
**GET** `/:url`
Redirects to the original URL associated with the short URL.
Example:
GET http://localhost:3000/abc123
→ Redirects to the original URL

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
**🛠️ How It Works**

**Shortening a URL:**
Client sends a POST request with the original URL
System validates the URL and checks rate limits
Generates a unique short code (6-character UUID or custom)
Stores the mapping in Redis with optional expiration
Returns the shortened URL

**Resolving a URL:**
Client visits the short URL
System looks up the original URL in Redis
Increments a redirect counter
Redirects to the original URL (HTTP 301)

**Rate Limiting:**
Tracks requests per IP address
Uses Redis with 30-minute expiration window
Returns rate limit status in API responses

**🔒 Security Features**
Domain Protection: Prevents shortening of the service's own domain
URL Validation: Validates URLs before processing
HTTPS Enforcement: Automatically converts HTTP to HTTPS
Rate Limiting: Prevents abuse with IP-based rate limiting

**🐳 Docker Details**
The project uses multi-stage Docker builds to minimize image size:
API Container: Built from Alpine Linux with Go builder stage
Redis Container: Official Redis Alpine image
Volume Persistence: Redis data persisted in ./data directory

**📝 Notes**
Short URLs are generated using the first 6 characters of a UUID
No collision checking is implemented for auto-generated short URLs
Custom short URLs are checked for uniqueness
Default expiration is 24 hours if not specified
Rate limit window is 30 minutes (configurable in code

**🤝 Contributing**
Contributions are welcome! Please feel free to submit a Pull Request.
📄 License
This project is open source and available under the MIT License.

🙏 Acknowledgments
Fiber - Web framework
go-redis - Redis client
