# Student Thesis Management System

A Django web application for managing student thesis records with role-based access for students and administrators.

> **Note:** This project is under active development.

## Project Overview

The Student Thesis Management System helps organize and track student theses. Each thesis record stores the student, supervisor, department, and university, along with a status that reflects its progress from proposal to completion. Records can include a description and an uploaded document.

Users sign up and log in, and what they can see and do depends on their role. Students manage their own thesis records, while administrators can view all records, manage statuses, delete theses, and see all students.

The project is deployed on Vercel: [student-thesis-management.vercel.app](https://student-thesis-management.vercel.app)

## Screenshots

> Screenshots show sample data.
> docs/screenshots/login.png
docs/screenshots/student-dashboard.png
docs/screenshots/student-my-thesis.png
...

### Login

![Login page](docs/screenshots/login.png)

### Student Dashboard

Students see summary counts and the records they own.

![Student dashboard](docs/screenshots/student-dashboard.png)

### Student Thesis List

Search by title, student, or supervisor, and filter by department.

![Student thesis list](docs/screenshots/student-my-thesis.png)

### Add New Thesis

![Add new thesis form](docs/screenshots/add-thesis.png)

### User Profile

![User profile page](docs/screenshots/student-profile.png)

### Admin Dashboard

Admins see all thesis records, with View, Edit, and Delete actions.

![Admin dashboard](docs/screenshots/admin-dashboard.png)

## Key Features

### Authentication and Roles
- User signup with email, university, and department (university and department are stored in the user profile)
- Login, logout, and a user profile page showing username, email, role, university, department, and account status
- Login-protected pages; unauthenticated users are redirected to the login page
- Two roles, **Student** and **Admin**, stored on each user's profile (created automatically for every new user)
- Role-aware navigation with a role badge next to the username:
  - Students: Dashboard, My Thesis, Add Thesis, Profile
  - Admins: Dashboard, All Theses, All Students, Profile

### Thesis Management
- Add, view, edit, and delete thesis records
- Thesis records are automatically linked to the user who created them
- Six-stage status workflow: Proposed, Approved, In Progress, Submitted, Under Review, Completed
- Optional document upload for each thesis
- Success and error feedback messages after actions

### Dashboard and Browsing
- Home dashboard showing counts of total, in-progress, submitted, and completed theses, with a table of thesis records
- Thesis list with search (title, student name, or supervisor name), record count, and a clear option
- Department filter
- Pagination (6 theses per page)

### Django Admin Site
- `Thesis` and `Profile` models registered at `/admin/`
- Thesis admin list shows title, student, supervisor, department, university, and creation date
- Search by title, student, supervisor, department, or university
- Filter by department and university

### Role-Based Permissions

| Action | Student | Admin |
|--------|:-------:|:-----:|
| View theses | Own only | All |
| Add a thesis | Yes | Yes |
| Edit a thesis | Own only (status locked) | All |
| Update thesis status | No | Yes |
| Delete a thesis | No | Yes |
| View All Students page | No | Yes |

## Technologies Used

| Category | Technology |
|----------|------------|
| Language | Python |
| Framework | Django 5.0.6 |
| Frontend | HTML (Django templates), CSS |
| Database | Configured through `DATABASE_URL` (`dj-database-url`); PostgreSQL driver `psycopg2-binary` included |
| Configuration | `python-decouple`, environment variables |
| Static files | WhiteNoise |
| Deployment | Vercel (`@vercel/python` runtime with the Django WSGI application) |
| Dependencies | Gunicorn is included in `requirements.txt` |
| Version control | Git & GitHub |

## Project Structure

```
student-thesis-management/
├── config/            # Django project configuration (settings, root URLs, WSGI)
├── docs/
│   └── screenshots/   # Screenshots used in this README
├── static/
│   └── css/           # CSS files (e.g., theme.css)
├── templates/         # HTML templates (base, registration, thesis pages)
├── thesis/            # Main Django application
│   ├── admin.py       # Django admin configuration
│   ├── forms.py       # ThesisForm and SignupForm
│   ├── models.py      # Thesis and Profile models
│   ├── urls.py        # Application URL routes
│   └── views.py       # Application views
├── .gitignore         # Git ignore rules
├── build_files.sh     # Vercel build script (installs dependencies, collects static files)
├── manage.py          # Django command-line utility
├── requirements.txt   # Python dependencies
└── vercel.json        # Vercel build and routing configuration
```

## Data Models

### Thesis

| Field | Description |
|-------|-------------|
| `owner` | Optional link to the user who owns the record |
| `title` | Thesis title |
| `student_name` | Name of the student |
| `supervisor_name` | Name of the supervisor |
| `department` | Department |
| `university` | University |
| `status` | Proposed, Approved, In Progress, Submitted, Under Review, or Completed (default: Proposed) |
| `description` | Optional description |
| `document` | Optional uploaded document |
| `created_at` / `updated_at` | Automatic timestamps |

### Profile

One-to-one with the user. Stores `role` (Student or Admin, default Student), `university`, and `department`. A profile is created automatically whenever a user is created.

## Forms

- **ThesisForm:** title, student name, supervisor name, department, university, status, description, and document.
- **SignupForm:** extends Django's `UserCreationForm` with username, email, password (with confirmation), university, and department.

## URL Routes

| Route | Name | Purpose | Access |
|-------|------|---------|--------|
| `/` | `home` | Dashboard | Logged in |
| `/theses/` | `thesis_list` | List, search, and filter theses | Logged in |
| `/add-thesis/` | `add_thesis` | Add a new thesis | Logged in |
| `/thesis/<id>/` | `thesis_detail` | View thesis details | Owner or admin |
| `/thesis/<id>/edit/` | `edit_thesis` | Edit a thesis | Owner or admin |
| `/thesis/<id>/delete/` | `delete_thesis` | Delete a thesis | Admin only |
| `/thesis/<id>/update-status/` | `update_status` | Update thesis status | Admin only |
| `/all-students/` | `all_students` | View students and their thesis counts | Admin only |
| `/login/` | `login` | Log in | Public |
| `/signup/` | `signup` | Create an account | Public |
| `/logout/` | `logout` | Log out | Logged in |
| `/profile/` | `profile` | View user profile | Logged in |
| `/admin/` | - | Django admin site | Staff users |

## Installation and Setup

### Prerequisites

- Python 3.x
- pip
- Git
- A database reachable through a `DATABASE_URL` connection string (e.g., PostgreSQL)

### Steps

1. **Clone the repository**

```bash
   git clone https://github.com/seema-Mqaqbool/student-thesis-management.git
   cd student-thesis-management
```

2. **Create and activate a virtual environment**

```bash
   python -m venv venv

   # Windows
   venv\Scripts\activate

   # macOS / Linux
   source venv/bin/activate
```

3. **Install dependencies**

```bash
   pip install -r requirements.txt
```

4. **Configure environment variables**

   Create a `.env` file in the project root:

```env
   DATABASE_URL=postgres://USER:PASSWORD@HOST:5432/DB_NAME
   DJANGO_SECRET_KEY=your-secret-key
   DEBUG=True
```

   | Variable | Description |
   |----------|-------------|
   | `DATABASE_URL` | Database connection string (required) |
   | `DJANGO_SECRET_KEY` | Django secret key; use a unique, private value |
   | `DEBUG` | `True` for local development, `False` otherwise |

   > Never commit your `.env` file or real credentials.

5. **Apply database migrations**

```bash
   python manage.py migrate
```

6. **Create a superuser (for the Django admin site)**

```bash
   python manage.py createsuperuser
```

## Creating an Admin User

New accounts default to the **Student** role. To give a user the application's **Admin** role, use either method below.

**Option 1: Django admin site**

1. Log in at `/admin/` with a superuser account.
2. Open **Profiles**, select the user's profile, and change **Role** to `admin`.

**Option 2: Django shell**

```bash
python manage.py shell
```

```python
from django.contrib.auth.models import User

user = User.objects.get(username="your_username")
user.profile.role = "admin"
user.profile.save()
```

> The application's Admin role (profile role) is separate from Django's staff/superuser status, which is what grants access to `/admin/`.

## Running the Development Server

```bash
python manage.py runserver
```

Then open your browser and go to:

```
http://127.0.0.1:8000/
```

Unauthenticated users are redirected to `/login/`.

## Deployment (Vercel)

The project is deployed on Vercel: student-thesis-management.vercel.app

## Main Functionality

- **Account creation:** users sign up with their university and department and are logged in automatically.
- **Thesis management:** add, view, edit, and delete theses according to role permissions.
- **Progress tracking:** administrators move theses through the six status stages.
- **Search and filtering:** find theses by title, student, or supervisor, and filter by department.
- **Student overview:** administrators can view all students with their thesis counts.
- **Back-office management:** staff can manage theses and profiles through the Django admin site.

## Known Limitations

- Uploaded documents are stored in a local `media/` directory, and Django serves them only when `DEBUG=True`. Serverless platforms such as Vercel do not provide persistent local storage, so cloud storage (and a matching serving setup) would be needed for reliable document uploads in deployment.
- Students can choose a thesis status when creating a thesis; status is locked for students only when editing.
- Database migrations are not run automatically during deployment.
- The project is still in development and should be reviewed for security and configuration before any production use.

## Future Improvements

- Integrate persistent cloud storage for uploaded documents
- Restrict the initial status for student-created theses
- Automate database migrations as part of deployment
- Add automated tests
- Add a supervisor role and thesis review workflow
- Add email notifications for status changes
- Improve UI/UX and responsiveness
- Add more screenshots (thesis detail, edit, and All Students pages)

## Author


**Seema**
GitHub: [@seema-Mqaqbool](https://github.com/seema-Maqbool)
