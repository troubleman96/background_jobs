# Background Jobs with Django and Celery

A Django project demonstrating asynchronous email sending using Celery with Redis as the message broker.

## 📋 Project Overview

This project showcases how to implement background job processing in Django using Celery. The main functionality includes:

- **Asynchronous Email Sending**: Send emails without blocking the main application thread
- **Celery Integration**: Process background tasks efficiently
- **Redis Message Broker**: Handle task queuing and result storage
- **REST API**: Send emails via API endpoints
- **Environment Configuration**: Secure configuration using environment variables

## 🛠️ Technologies Used

- **Django 5.2.5**: Web framework
- **Celery 5.5.3**: Distributed task queue
- **Redis**: Message broker and result backend
- **Django REST Framework**: API development
- **Python-dotenv**: Environment variable management
- **Gmail SMTP**: Email service provider

## 📁 Project Structure

```
background_jobs/
├── email_project/
│   ├── email_project/
│   │   ├── __init__.py
│   │   ├── celery.py          # Celery configuration
│   │   ├── settings.py        # Django settings
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── email_app/
│   │   ├── __init__.py
│   │   ├── tasks.py           # Celery tasks
│   │   ├── views.py           # API views
│   │   ├── models.py
│   │   └── apps.py
│   └── manage.py
├── venv/                      # Virtual environment
├── .env                       # Environment variables
└── README.md
```

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Redis server
- Gmail account with App Password enabled

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/troubleman96/background_jobs.git
   cd background_jobs
   ```

2. **Create and activate virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Linux/Mac
   # or
   venv\Scripts\activate     # On Windows
   ```

3. **Install dependencies**
   ```bash
   pip install django celery redis python-dotenv djangorestframework
   ```

4. **Set up environment variables**
   Create a `.env` file in the project root:
   ```env
   EMAIL_HOST=smtp.gmail.com
   EMAIL_PORT=587
   EMAIL_HOST_USER=your_email@gmail.com
   EMAIL_HOST_PASSWORD=your_app_password
   EMAIL_USE_TLS=True
   
   CELERY_BROKER_URL=redis://localhost:6379/0
   CELERY_RESULT_BACKEND=redis://localhost:6379/0
   ```

5. **Run migrations**
   ```bash
   cd email_project
   python manage.py migrate
   ```

## 🏃‍♂️ Running the Application

### 1. Start Redis Server
```bash
redis-server
```

### 2. Start Django Development Server
```bash
python manage.py runserver
```

### 3. Start Celery Worker
In a new terminal:
```bash
celery -A email_project worker --loglevel=info
```

## 📧 Usage

### Sending Emails via API

Send a POST request to trigger an asynchronous email:

**Endpoint**: `POST /api/send-email/`

**Request Body**:
```json
{
  "subject": "Test Email",
  "message": "This is a test message",
  "recipient_list": ["recipient@example.com"]
}
```

**Response**:
```json
{
  "status": "Email task queued successfully",
  "task_id": "abc123-def456-789"
}
```

### Using Django Shell

```python
from email_app.tasks import send_email_task

# Queue an email task
task = send_email_task.delay(
    subject="Hello from Celery",
    message="This email was sent asynchronously!",
    recipient_list=["recipient@example.com"]
)

# Check task status
print(task.status)
print(task.result)
```

## ⚙️ Configuration

### Celery Settings

The Celery configuration is defined in `email_project/celery.py`:

- **Broker**: Redis for task queuing
- **Result Backend**: Redis for storing task results
- **Auto-discovery**: Automatically finds tasks in Django apps
- **Environment Integration**: Uses Django settings with `CELERY_` prefix

### Email Settings

Email configuration in `settings.py`:

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = os.environ.get('EMAIL_HOST', 'smtp.gmail.com')
EMAIL_PORT = int(os.environ.get('EMAIL_PORT', 587))
EMAIL_USE_TLS = os.environ.get('EMAIL_USE_TLS', 'True') == 'True'
EMAIL_HOST_USER = os.environ.get('EMAIL_HOST_USER')
EMAIL_HOST_PASSWORD = os.environ.get('EMAIL_HOST_PASSWORD')
```

## 🔍 Monitoring

### Celery Flower (Optional)
Install and run Flower for web-based monitoring:

```bash
pip install flower
celery -A email_project flower
```

Access the web interface at `http://localhost:5555`

### Checking Task Status

```python
from celery.result import AsyncResult

result = AsyncResult('task-id-here', app=celery_app)
print(result.state)
print(result.info)
```

## 🛡️ Security Considerations

- **Environment Variables**: All sensitive data stored in `.env` file
- **Gmail App Passwords**: Use App Passwords instead of regular passwords
- **Redis Security**: Configure Redis authentication in production
- **ALLOWED_HOSTS**: Configure properly for production deployment

## 🚧 Troubleshooting

### Common Issues

1. **ConnectionRefusedError**: Check Redis server is running
2. **SMTP Authentication Failed**: Verify Gmail App Password
3. **Module Import Errors**: Ensure virtual environment is activated
4. **Task Not Found**: Check Celery worker is discovering tasks properly

### Debug Commands

```bash
# Test Redis connection
redis-cli ping

# Test SMTP connection
telnet smtp.gmail.com 587

# Check Celery tasks
celery -A email_project inspect active
```

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📞 Contact

**Troubleman** - [GitHub Profile](https://github.com/troubleman96)

Project Link: [https://github.com/troubleman96/background_jobs](https://github.com/troubleman96/background_jobs)

---

⭐ **Star this repository if you found it helpful!**
