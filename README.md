# 🏢 Multi-Tenant Expense Management API

A production-ready REST API for expense management with multi-tenant architecture, role-based access control, approval workflows, and comprehensive reporting.

## ✨ Features

### Core Functionality
- ✅ **Multi-Tenant Architecture** - Complete data isolation between companies
- ✅ **JWT Authentication** - Secure token-based authentication
- ✅ **Role-Based Access Control** - Admin, Manager, and Employee roles
- ✅ **Expense Management** - Full CRUD operations with approval workflows
- ✅ **Approval System** - Manager/Admin approval of submitted expenses
- ✅ **Budget Tracking** - Set and monitor budgets by department/category
- ✅ **Excel Reports** - Generate detailed reports using Pandas
- ✅ **RESTful API Design** - Clean, intuitive endpoints

### Role Permissions

**👤 Employee**
- Create and edit own expenses (draft status)
- Submit expenses for approval
- View personal expense history
- Generate personal reports

**👔 Manager**
- All employee permissions
- Approve/reject team expenses
- View department expenses
- Generate team reports
- View assigned approval queue

**👨‍💼 Admin**
- Full system access
- Manage users and roles
- Configure budgets
- Access all expenses and reports
- View system-wide statistics

## 🛠️ Tech Stack

- **Python 3.8+**
- **Flask 3.0** - Web framework
- **PostgreSQL** - Database
- **SQLAlchemy** - ORM
- **Flask-JWT-Extended** - Authentication
- **Pandas** - Report generation
- **Flask-Migrate** - Database migrations

## 📋 Prerequisites

- Python 3.8 or higher
- PostgreSQL 12 or higher
- pip (Python package manager)
- Virtual environment tool (venv)

## 🚀 Quick Start

### 1. Clone Repository

```bash
git clone https://github.com/Abdulrhman-Khalid-mohamed/expense-management-api.git
cd expense-management-api
```

### 2. Create Virtual Environment

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On macOS/Linux:
source venv/bin/activate

# On Windows:
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Setup Database

```bash
# Create PostgreSQL database
createdb expense_db

# Or using psql:
psql -U postgres
CREATE DATABASE expense_db;
\q
```

### 5. Configure Environment

```bash
# Copy example environment file
cp .env.example .env

# Edit .env with your configuration
# Update DATABASE_URL with your PostgreSQL credentials
```

Example `.env` configuration:
```env
FLASK_ENV=development
SECRET_KEY=your-super-secret-key-change-this
JWT_SECRET_KEY=your-jwt-secret-key-change-this
DATABASE_URL=postgresql://postgres:yourpassword@localhost:5432/expense_db
```

### 6. Initialize Database

```bash
# Run migrations
flask db upgrade

# Seed with sample data (optional)
flask seed
```

### 7. Run Application

```bash
python run.py
```

The API will be available at `http://localhost:5000`

## 📚 API Documentation

### Authentication Endpoints

#### Register User
```http
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securepassword",
  "first_name": "John",
  "last_name": "Doe",
  "tenant_id": 1,
  "department": "Sales"
}
```

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securepassword",
  "tenant_id": 1
}
```

Response:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "user": {
    "id": 1,
    "email": "user@example.com",
    "role": "employee",
    "full_name": "John Doe"
  }
}
```

#### Get Current User
```http
GET /api/auth/me
Authorization: Bearer {access_token}
```

### Expense Endpoints

#### List Expenses
```http
GET /api/expenses?page=1&per_page=20&status=submitted
Authorization: Bearer {access_token}
```

Query Parameters:
- `page` - Page number (default: 1)
- `per_page` - Items per page (default: 20)
- `status` - Filter by status (draft, submitted, approved, rejected, paid)
- `category` - Filter by category
- `user_id` - Filter by user (Manager/Admin only)

#### Create Expense
```http
POST /api/expenses
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "category": "travel",
  "amount": 150.00,
  "description": "Flight to conference",
  "expense_date": "2024-12-20",
  "receipt_url": "https://example.com/receipt.pdf"
}
```

#### Get Expense Details
```http
GET /api/expenses/1
Authorization: Bearer {access_token}
```

#### Update Expense
```http
PUT /api/expenses/1
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "amount": 175.00,
  "description": "Updated flight cost"
}
```

#### Submit Expense for Approval
```http
POST /api/expenses/1/submit
Authorization: Bearer {access_token}
```

### Approval Endpoints (Manager/Admin)

#### List Pending Approvals
```http
GET /api/approvals?page=1
Authorization: Bearer {access_token}
```

#### Approve Expense
```http
POST /api/approvals/1/approve
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "comments": "Approved - looks good"
}
```

#### Reject Expense
```http
POST /api/approvals/1/reject
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "comments": "Please provide a valid receipt"
}
```

### Report Endpoints

#### Personal Report
```http
GET /api/reports/personal?start_date=2024-12-01&end_date=2024-12-31
Authorization: Bearer {access_token}
```

#### Team Report (Manager/Admin)
```http
GET /api/reports/team?start_date=2024-12-01&end_date=2024-12-31
Authorization: Bearer {access_token}
```

#### Export to Excel
```http
POST /api/reports/export
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "start_date": "2024-12-01",
  "end_date": "2024-12-31"
}
```

### Admin Endpoints

#### List All Users
```http
GET /api/admin/users?page=1&role=employee
Authorization: Bearer {access_token}
```

#### Create User
```http
POST /api/admin/users
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "email": "newuser@example.com",
  "password": "password123",
  "first_name": "Jane",
  "last_name": "Smith",
  "role": "manager",
  "department": "Marketing"
}
```

#### Update User
```http
PUT /api/admin/users/2
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "role": "manager",
  "department": "Sales"
}
```

#### Create Budget
```http
POST /api/admin/budgets
Authorization: Bearer {access_token}
Content-Type: application/json

{
  "department": "Sales",
  "category": "travel",
  "monthly_limit": 5000.00,
  "year": 2024,
  "month": 12
}
```

## 🗃️ Database Schema

### Key Tables

**tenants**
- Multi-tenant isolation
- Company information

**users**
- User accounts
- Role-based permissions
- Linked to tenant

**expenses**
- Expense records
- Category and status tracking
- Tenant and user isolation

**approvals**
- Approval workflow
- Manager/Admin actions
- Comments and timestamps

**budgets**
- Budget limits
- Department/category based
- Monthly tracking

## 🧪 Testing

### Run Tests
```bash
# Install test dependencies
pip install pytest pytest-cov

# Run all tests
pytest

# Run with coverage
pytest --cov=app tests/

# Run specific test file
pytest tests/test_auth.py
```

### Sample Test
```python
def test_create_expense(client, auth_token):
    response = client.post(
        '/api/expenses',
        headers={'Authorization': f'Bearer {auth_token}'},
        json={
            'category': 'travel',
            'amount': 100.00,
            'description': 'Test expense',
            'expense_date': '2024-12-20'
        }
    )
    assert response.status_code == 201
    assert 'expense' in response.json
```

## 🚢 Deployment

### Heroku Deployment

```bash
# Login to Heroku
heroku login

# Create app
heroku create your-app-name

# Add PostgreSQL
heroku addons:create heroku-postgresql:hobby-dev

# Set environment variables
heroku config:set SECRET_KEY=your-secret-key
heroku config:set JWT_SECRET_KEY=your-jwt-secret

# Deploy
git push heroku main

# Run migrations
heroku run flask db upgrade

# Seed database (optional)
heroku run flask seed
```

### DigitalOcean/VPS Deployment

1. **Setup Server**
```bash
sudo apt update
sudo apt install python3-pip python3-venv postgresql nginx
```

2. **Clone and Setup App**
```bash
git clone your-repo-url
cd expense-management-api
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
pip install gunicorn
```

3. **Setup Gunicorn**
Create `/etc/systemd/system/expense-api.service`:
```ini
[Unit]
Description=Expense Management API
After=network.target

[Service]
User=www-data
WorkingDirectory=/var/www/expense-management-api
Environment="PATH=/var/www/expense-management-api/venv/bin"
ExecStart=/var/www/expense-management-api/venv/bin/gunicorn -w 4 -b 127.0.0.1:8000 run:app

[Install]
WantedBy=multi-user.target
```

4. **Configure Nginx**
Create `/etc/nginx/sites-available/expense-api`:
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

5. **Start Services**
```bash
sudo systemctl start expense-api
sudo systemctl enable expense-api
sudo systemctl restart nginx
```

## 📊 Sample Data

After running `flask seed`, you'll have:

**Sample Accounts:**
- Admin: `admin@acme.com` / `admin123`
- Manager: `manager@acme.com` / `manager123`
- Employee: `employee@acme.com` / `employee123`

**Sample Data:**
- 1 Tenant (Acme Corporation)
- 3 Users (different roles)
- 4 Sample expenses
- 1 Budget configuration

## 🔒 Security Best Practices

1. **Change Default Keys**
   - Update SECRET_KEY in production
   - Update JWT_SECRET_KEY in production

2. **Use HTTPS**
   - Always use SSL/TLS in production
   - Configure proper CORS settings

3. **Password Requirements**
   - Minimum 6 characters (increase for production)
   - Consider adding complexity requirements

4. **Rate Limiting**
   - Add rate limiting to prevent abuse
   - Consider using Flask-Limiter

5. **Input Validation**
   - All inputs are validated
   - SQL injection protection via SQLAlchemy

## 🐛 Troubleshooting

### Database Connection Issues
```bash
# Check PostgreSQL is running
sudo systemctl status postgresql

# Test connection
psql -U postgres -d expense_db
```

### Migration Issues
```bash
# Reset migrations (development only!)
flask db downgrade base
flask db upgrade
```

### Port Already in Use
```bash
# Find process using port 5000
lsof -i :5000

# Kill process
kill -9 <PID>
```

## 📈 Future Enhancements

- [ ] Receipt image upload to cloud storage
- [ ] Email notifications for approvals
- [ ] Advanced analytics dashboard
- [ ] Mobile app support
- [ ] Multi-currency support with exchange rates
- [ ] Integration with accounting software
- [ ] Two-factor authentication
- [ ] Audit logs

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Write tests
5. Submit a pull request

## 📄 License

MIT License - See LICENSE file for details

## 👤 Author

**Abdulrhman Khalid**
- GitHub: [@Abdulrhman-Khalid-mohamed](https://github.com/Abdulrhman-Khalid-mohamed)
- LinkedIn: [abdulrhman-khalid-m](https://www.linkedin.com/in/abdulrhman-khalid-m/)

## 📞 Support

For issues and questions:
- Open an issue on GitHub
- Contact via LinkedIn

---

**⭐ If you find this project useful, please give it a star!**
