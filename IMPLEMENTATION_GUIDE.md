# Smart Crime System - Complete Implementation Guide

## 🎯 User Journey

### **Landing Page** (First Entry Point)
```
User visits: http://127.0.0.1:8000/
    ↓
Sees two role selection boxes:
1. "I am a Local Citizen" → Register/Login
2. "I am a Police Officer" → Login
```

---

## 📋 **Complete User Flows**

### **FLOW 1: Local Citizen Registration & Crime Reporting**

#### Step 1: Access Landing Page
- URL: `/landing/`
- Click: **"I am a Local Citizen" → Register as Citizen**

#### Step 2: Register (Citizen Signup)
- URL: `/citizen/signup/`
- Fill form:
  - Email (unique)
  - First Name
  - Last Name
  - Password (8+ chars)
  - Confirm Password
- Submit → Auto-login + Redirect to profile completion

#### Step 3: Complete Profile
- URL: `/citizen/complete-profile/`
- Fill additional info:
  - Phone (optional)
  - Address (optional)
  - City (optional)
  - **Police Area** (required - dropdown with all areas)
- Submit → Redirect to Citizen Home

#### Step 4: Citizen Dashboard
- URL: `/citizen/home/`
- Shows:
  - Statistics (Total Reports, Resolved, Pending)
  - Action buttons: Report Crime, View Reports, Edit Profile
  - Recent reports with status

#### Step 5: Report a Crime
- URL: `/citizen/report-crime/`
- Form fields:
  - Crime Type (dropdown)
  - Detailed Description (text area)
  - Police Area (dropdown)
  - Location Name/Address
  - Latitude/Longitude (optional, GPS coords)
  - When did crime occur? (datetime picker)
  - Evidence Upload (images/videos, optional, max 10MB)
- Submit → 
  - ✅ Confirmation message
  - Auto-assigned to area's police officer
  - Notification created for that officer
  - Email sent to officer

#### Step 6: Track Reports
- URL: `/citizen/my-reports/`
- Table showing:
  - Crime Type
  - Location
  - Date
  - Current Status
  - Action: View details
- Click "View" → See full report with officer notes

---

### **FLOW 2: Police Officer Login & Investigation**

#### Step 1: Access Landing Page
- URL: `/landing/`
- Click: **"I am a Police Officer" → Login as Officer**

#### Step 2: Police Officer Login
- URL: `/police/login/`
- Form:
  - Officer ID (pre-registered by admin)
  - Password
- Login → Redirect to Role Select

#### Step 3: Role Select (if has both profiles)
- URL: `/role-select/`
- Confirm: "Continue as Officer" button
- Redirect to Police Dashboard

#### Step 4: Police Dashboard
- URL: `/police/home/`
- Shows:
  - Statistics: Pending, In Progress, Resolved reports
  - Quick action buttons
  - Recent pending reports
  - Unread notifications badge

#### Step 5: View All Assigned Reports
- URL: `/police/reports/`
- Features:
  - Filter by Status (Pending, In Progress, Resolved, Closed)
  - Filter by Area
  - Pagination (10 per page)
  - Table with: Crime Type, Location, Date, Status, Action button

#### Step 6: View Report Details
- URL: `/report/<report_id>/`
- Displays:
  - Crime type & status
  - Reporter information
  - Crime location & time
  - Full description
  - Evidence (if uploaded)
  - Investigation notes (if any)
  - **Update form** (for police only):
    - Change status
    - Mark as priority
    - Add investigation notes
    - Save button

#### Step 7: Receive Notifications
- URL: `/police/notifications/`
- Features:
  - All notifications sorted by newest
  - Mark individual as read
  - Mark all as read
  - Filter by type
  - Click to view associated report
  - Shows time ago format

---

## 🔑 **Authentication Details**

### **Citizen Login (Existing Account)**
- URL: `/citizen/login/`
- Fields:
  - Username or Email
  - Password
- Links:
  - Forgot password?
  - Register new account
  - Back to role selection

### **Police Officer Login**
- URL: `/police/login/`
- Fields:
  - Officer ID (unique identifier)
  - Password
- Note: Officer IDs are pre-registered by admin

---

## 💾 **Database Structure**

### **Tables Created:**

#### `Area` (Police Jurisdictions)
- id, name, code, description, created_at

#### `PoliceOfficer`
- id, user (FK), officer_id (unique), rank, assigned_area (FK), badge_number, phone, is_active, created_at, updated_at

#### `Citizen`
- id, user (FK), email (unique), phone, address, city, profile_area (FK), email_verified, created_at, updated_at

#### `CrimeReport`
- id, citizen (FK), assigned_officer (FK), crime_type, description, area (FK), location_name, latitude, longitude, crime_date, reported_date, status, evidence_file, officer_notes, is_priority, updated_at

#### `Notification`
- id, recipient (FK to PoliceOfficer), crime_report (FK), notification_type, title, message, is_read, created_at, read_at

---

## 🔐 **URL Map**

| URL | Method | Purpose |
|-----|--------|---------|
| `/` | GET | Redirect to index |
| `/landing/` | GET | Landing page - Choose role |
| `/login/` | GET | Redirect to landing |
| `/logout/` | GET/POST | Logout user |
| `/citizen/signup/` | GET/POST | Register as citizen |
| `/citizen/login/` | GET/POST | Citizen login |
| `/citizen/complete-profile/` | GET/POST | Complete citizen profile |
| `/citizen/home/` | GET | Citizen dashboard |
| `/citizen/report-crime/` | GET/POST | Submit crime report |
| `/citizen/my-reports/` | GET | View all own reports |
| `/police/login/` | GET/POST | Police officer login |
| `/police/home/` | GET | Police dashboard |
| `/police/reports/` | GET | View assigned reports (with filters) |
| `/report/<id>/` | GET | View report details |
| `/police/report/<id>/update/` | GET/POST | Update report (police only) |
| `/police/notifications/` | GET | View all notifications |
| `/police/notification/<id>/read/` | GET | Mark notification as read |
| `/role-select/` | GET | Select role (if both profiles exist) |
| `/password_reset/` | GET/POST | Request password reset |
| `/password_reset/done/` | GET | Confirmation after reset request |
| `/reset/<uidb64>/<token>/` | GET/POST | Reset password with link |
| `/reset/done/` | GET | Password reset complete |

---

## 🛠️ **Admin Panel Setup**

### **Create Police Areas**
1. Go to: `/admin/dashboard/area/`
2. Click "Add Area"
3. Enter:
   - Name: (e.g., "North District")
   - Code: (e.g., "ND-001")
   - Description: (optional)
4. Save

### **Create Police Officers**
1. Go to: `/admin/dashboard/policeofficer/`
2. Click "Add Police Officer"
3. Enter:
   - User: (select existing Django user or create new)
   - Officer ID: (unique identifier, e.g., "PL001")
   - Rank: (Constable, Sub Inspector, Inspector, Senior Inspector)
   - Assigned Area: (select from areas)
   - Badge Number: (optional)
   - Phone: (optional)
   - Is Active: (checked)
4. Save

### **View Crime Reports**
1. Go to: `/admin/dashboard/crimereport/`
2. Filter by:
   - Status (Pending, In Progress, etc.)
   - Crime Type
   - Area
   - Priority flag
3. Can view and edit reports from admin panel

---

## 📊 **Crime Type Options**

- Theft
- Assault
- Robbery
- Fraud
- Harassment
- Vandalism
- Accident
- Lost Property
- Kidnapping
- Rape
- Murder
- Other

---

## 📧 **Email Notifications**

### **Current Setup: Console Output**
```
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```
- Emails print in your terminal/console during development

### **When Officer Gets Notified:**
1. Citizen submits crime report
2. Report auto-assigned to officer of that area
3. Notification created in database
4. Email sent (prints to console)
5. Officer sees badge count on dashboard
6. Officer can view in `/police/notifications/`

### **For Production Email:**
Update `settings.py`:
```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'your-smtp-server.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = 'your-email@gmail.com'
EMAIL_HOST_PASSWORD = 'your-app-password'
```

---

## 🎨 **Features Overview**

### **Citizen Features:**
- ✅ Email-based registration
- ✅ Report crimes with full details
- ✅ Upload evidence (photos/videos)
- ✅ Track report status in real-time
- ✅ View officer investigation notes
- ✅ Password reset via email
- ✅ Edit profile
- ✅ View all personal reports (paginated)

### **Police Officer Features:**
- ✅ Officer ID + Password login
- ✅ View all assigned reports
- ✅ Filter reports by status and area
- ✅ Update investigation status
- ✅ Add investigation notes
- ✅ Mark reports as priority
- ✅ Real-time notifications
- ✅ View notification history
- ✅ Mark notifications as read
- ✅ Responsive dashboard with statistics

### **System Features:**
- ✅ Auto-assignment of reports to area officers
- ✅ Role-based access control
- ✅ Session management with auto-logout
- ✅ Secure password handling
- ✅ Confidential data handling
- ✅ Admin panel for management
- ✅ Professional UI with police branding
- ✅ Responsive design (mobile/tablet)
- ✅ Pagination for large datasets
- ✅ Real-time status updates

---

## 🚀 **Testing Checklist**

- [ ] Visit `/landing/` and see role selection boxes
- [ ] Click citizen button → citizen signup page
- [ ] Create citizen account with email
- [ ] Complete citizen profile with area selection
- [ ] Submit crime report with all required fields
- [ ] Upload evidence file (image or video)
- [ ] Verify report shows in citizen dashboard
- [ ] Create police officer in admin panel
- [ ] Login as police officer with Officer ID
- [ ] See pending reports on police dashboard
- [ ] View full report details
- [ ] Update report status and add notes
- [ ] View notifications
- [ ] Check console for email output
- [ ] Test password reset flow
- [ ] Test session logout on browser close

---

## 📝 **Database Query Examples**

### **Get all pending reports for an officer:**
```python
from dashboard.models import CrimeReport

reports = CrimeReport.objects.filter(
    assigned_officer=officer_instance,
    status='PENDING'
).order_by('-reported_date')
```

### **Get all unread notifications:**
```python
from dashboard.models import Notification

notifications = Notification.objects.filter(
    recipient=officer_instance,
    is_read=False
).order_by('-created_at')
```

### **Create a sample area and officer (in Django shell):**
```python
python manage.py shell
>>> from dashboard.models import Area, PoliceOfficer
>>> from django.contrib.auth.models import User
>>> 
>>> # Create area
>>> area = Area.objects.create(name="North District", code="ND-001")
>>> 
>>> # Create user
>>> user = User.objects.create_user(username="officer1", password="SecurePass123")
>>> 
>>> # Create police officer
>>> officer = PoliceOfficer.objects.create(
...     user=user,
...     officer_id="PL001",
...     rank="INSPECTOR",
...     assigned_area=area
... )
```

---

## ✨ **System Is Ready!**

All components are properly configured and ready for use. Start by:

1. Run migrations: `python manage.py migrate`
2. Create admin superuser: `python manage.py createsuperuser`
3. Create police areas and officers in admin panel
4. Start development server: `python manage.py runserver`
5. Visit: `http://127.0.0.1:8000/landing/`

Enjoy your Smart Crime System! 🚔
