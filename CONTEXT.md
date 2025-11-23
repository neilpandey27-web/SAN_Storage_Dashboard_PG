# SAN Storage Dashboard - Comprehensive Application Context

## 📋 Executive Summary

The SAN Storage Dashboard is an enterprise-grade web application designed to visualize and analyze SAN (Storage Area Network) capacity across multiple storage systems. It provides a four-level drill-down interface for IT administrators to monitor storage allocation, utilization, and available buffer capacity across pools, child pools, tenants, and individual volumes.

**Version**: 5.7 (Latest: v5.8-branch)
**GitHub**: https://github.com/neilpandey27-web/SAN_Storage_Dashboard_PG

---

## 🎯 Business Purpose & Problem Statement

### Problem
Organizations with large-scale SAN infrastructure face challenges:
- **Visibility Gap**: Difficult to track capacity utilization across multiple storage systems
- **Manual Analysis**: Excel-based reporting is time-consuming and error-prone
- **Capacity Planning**: Hard to identify where storage is allocated vs. actually utilized
- **Tenant Accountability**: No clear view of which tenants consume the most storage
- **Buffer Management**: Unclear view of available capacity for future allocation

### Solution
The SAN Storage Dashboard provides:
- **Real-time Visualization**: Interactive charts showing utilization distribution
- **Four-Level Drill-Down**: Navigate from pools → child pools → tenants → volumes
- **Accurate Metrics**: Clear distinction between Total Capacity, Allocated, Utilized, and Available Buffer
- **Excel Import**: Simple data ingestion from existing storage reports
- **Multi-Unit Support**: View data in GB, TB, or PB
- **Top Tenant Tracking**: Identify highest consumers instantly

---

## 👥 Target Users & Use Cases

### Primary Users
1. **SAN Storage Administrators**
   - Monitor overall storage health
   - Identify capacity bottlenecks
   - Plan capacity expansions
   - Generate executive reports

2. **IT Operations Teams**
   - Track tenant storage consumption
   - Allocate storage to new projects
   - Identify underutilized capacity
   - Optimize storage distribution

3. **IT Managers/Directors**
   - View high-level capacity metrics
   - Track storage costs per tenant
   - Make budget decisions
   - Review storage utilization trends

### Key Use Cases

#### Use Case 1: Daily Capacity Monitoring
**Actor**: SAN Administrator  
**Goal**: Check current storage utilization across all systems  
**Flow**:
1. Login to dashboard
2. View summary metrics (Total SAN Capacity: 8,000 TB)
3. Check Available Buffer (805.50 TB)
4. Review donut chart for pool distribution
5. Check Top 10 Tenants bar chart for high consumers

**Value**: Quick daily health check in under 2 minutes

#### Use Case 2: Tenant Capacity Investigation
**Actor**: IT Operations Engineer  
**Goal**: Investigate why "Engineering" tenant is using excessive storage  
**Flow**:
1. Login to dashboard
2. View Top 10 Tenants bar chart
3. Click "Engineering" pool in pools table
4. Drill down to child pools (HST-Pool, AIX-Pool, LINUX-Pool)
5. Click child pool to view tenants
6. Click "Engineering" tenant to view all volumes
7. Identify specific volumes consuming most space

**Value**: Root cause analysis in 5 minutes vs. 30+ minutes with Excel

#### Use Case 3: Capacity Planning for New Project
**Actor**: IT Manager  
**Goal**: Determine if sufficient capacity exists for new 100TB project  
**Flow**:
1. Login to dashboard
2. Check Available Buffer (805.50 TB) ✓ Sufficient
3. Drill down to desired pool (e.g., A9K-A1)
4. Check child pool availability (e.g., LINUX-Pool)
5. Note unutilized capacity in target location

**Value**: Instant capacity validation vs. days of email coordination

#### Use Case 4: Executive Reporting
**Actor**: IT Director  
**Goal**: Prepare monthly capacity report for CIO  
**Flow**:
1. Login to dashboard
2. Toggle to PB view for executive summary
3. Capture summary metrics:
   - Total SAN Capacity: 8.00 PB
   - Available Buffer: 0.81 PB
   - Avg Utilization: 86.96%
4. Export Top 10 Tenants chart
5. Review pool-level distribution

**Value**: Professional report in 10 minutes vs. hours of manual data aggregation

#### Use Case 5: Data Refresh After Storage Expansion
**Actor**: SAN Administrator  
**Goal**: Update dashboard after adding new storage arrays  
**Flow**:
1. Export latest capacity report from storage management software to Excel
2. Login to dashboard as admin
3. Navigate to Upload section
4. Upload new DATA_NEW.xlsx file
5. Verify new Total SAN Capacity increased
6. Confirm Available Buffer updated

**Value**: Dashboard reflects current state within minutes of hardware changes

---

## 🏗️ Technical Architecture

### System Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                         Browser                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │         React Frontend (Port 3000)                    │  │
│  │  • Carbon Design System UI                            │  │
│  │  • Apache ECharts Visualizations                      │  │
│  │  • Axios HTTP Client                                  │  │
│  │  • Session-based Authentication                       │  │
│  └────────────────────┬──────────────────────────────────┘  │
└────────────────────────┼──────────────────────────────────┬─┘
                         │ HTTP/REST API                     │
                         ▼                                   │
┌─────────────────────────────────────────────────────────┐  │
│        Django Backend (Port 8000)                        │  │
│  ┌──────────────────────────────────────────────────┐   │  │
│  │   REST API Layer (Django REST Framework)         │   │  │
│  │   • /api/login/    - Authentication               │   │  │
│  │   • /api/logout/   - Session termination          │   │  │
│  │   • /api/dashboard/ - Capacity data (4 levels)    │   │  │
│  │   • /api/upload/   - Excel file ingestion         │   │  │
│  │   • /api/check-auth/ - Auth verification          │   │  │
│  └────────────────────┬─────────────────────────────┘   │  │
│                       │                                   │  │
│  ┌────────────────────▼─────────────────────────────┐   │  │
│  │   Business Logic Layer (analytics/views.py)      │   │  │
│  │   • Data aggregation (pools, child pools, etc.)  │   │  │
│  │   • Lab Engineering filtering                     │   │  │
│  │   • Capacity calculations                         │   │  │
│  │   • Tenant extraction from volume names           │   │  │
│  └────────────────────┬─────────────────────────────┘   │  │
│                       │                                   │  │
│  ┌────────────────────▼─────────────────────────────┐   │  │
│  │   Data Access Layer (Django ORM)                 │   │  │
│  │   • StorageData model (volumes table)            │   │  │
│  │   • User model (authentication)                   │   │  │
│  └────────────────────┬─────────────────────────────┘   │  │
└────────────────────────┼──────────────────────────────┬──┘  │
                         ▼                              │     │
┌─────────────────────────────────────────────────────┐ │     │
│      PostgreSQL Database                            │ │     │
│  • storage_data table (volumes)                     │ │     │
│  • auth_user table (users)                          │ │     │
│  • django_session table (sessions)                  │ │     │
└─────────────────────────────────────────────────────┘ │     │
                                                        │     │
                                                        │     │
┌─────────────────────────────────────────────────────┐ │     │
│      Excel Data Source (DATA_NEW.xlsx)              │ │     │
│  • Exported from SAN management software            │◄┘     │
│  • Uploaded by admin users via UI                   │       │
└─────────────────────────────────────────────────────┘       │
                                                              │
                                                              │
┌──────────────────────────────────────────────────────────┐ │
│      Docker Containers                                    │ │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────┐ │ │
│  │   frontend     │  │    backend     │  │     db     │ │ │
│  │  (React/Nginx) │  │    (Django)    │  │ (Postgres) │ │ │
│  └────────────────┘  └────────────────┘  └────────────┘ │ │
└──────────────────────────────────────────────────────────┘ │
                                                             │
                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack

#### Frontend
- **React 18.2.0**: UI framework with hooks and functional components
- **Carbon Design System 1.0**: IBM's enterprise UI component library
- **Apache ECharts 5.4.3**: Advanced data visualization library
- **Axios 1.6.5**: HTTP client for API communication
- **React Scripts 5.0.1**: Build tooling and development server

#### Backend
- **Django 4.x**: Python web framework
- **Django REST Framework**: RESTful API toolkit
- **PostgreSQL**: Production database
- **Pandas**: Excel file parsing and data manipulation
- **Python 3.x**: Server-side language

#### Infrastructure
- **Docker & Docker Compose**: Containerization and orchestration
- **Nginx**: Static file serving (frontend production)
- **Gunicorn**: WSGI HTTP server (backend production)

---

## 📊 Data Model & Architecture

### Database Schema

#### StorageData Model (Primary Entity)
```python
class StorageData(models.Model):
    id = AutoField(primary_key=True)
    volume = CharField(max_length=255)           # Volume name (e.g., "ENGR_server01_vol1")
    pool = CharField(max_length=255)             # Parent pool/System (e.g., "A9K-A1", "V7K-R3")
    child_pool = CharField(max_length=255)       # Child pool (e.g., "HST-Pool", "Lab Engineering")
    volume_size_gb = DecimalField(max_digits=12) # Allocated capacity in GB
    utilized_gb = DecimalField(max_digits=12)    # Used capacity in GB
    left_gb = DecimalField(max_digits=12)        # Unutilized capacity in GB (volume_size - utilized)
    created_at = DateTimeField(auto_now_add=True)
```

**Indexes**: 
- Primary key on `id`
- Recommended: Composite index on `(pool, child_pool)` for drill-down performance

#### User Model (Django Built-in)
```python
# Django's auth.User model
username = CharField(max_length=150, unique=True)
password = CharField(max_length=128)  # Hashed
is_staff = BooleanField(default=False) # Admin flag for upload permission
```

### Data Hierarchy & Relationships

#### Excel to Database Mapping
| Excel Column          | Database Field  | Purpose                          |
|-----------------------|-----------------|----------------------------------|
| System                | `pool`          | Parent storage system identifier |
| Pool                  | `child_pool`    | Child pool within system         |
| Volume                | `volume`        | Individual volume name           |
| Volume Size (GB)      | `volume_size_gb`| Allocated capacity               |
| Written by Host (%)   | (calculated)    | Used to compute `utilized_gb`    |

#### Four-Level Hierarchy
```
Level 1: Pools (Parent Systems)
    ├── A9K-A1 (1,600 TB)
    ├── A9K-A2 (1,600 TB)
    ├── V7K-R3 (1,600 TB)
    ├── V7K-R4 (1,600 TB)
    └── FS92K-A1 (1,600 TB)
        │
        └── Level 2: Child Pools
                ├── HST-Pool (500 TB)
                ├── AIX-Pool (300 TB)
                ├── LINUX-Pool (400 TB)
                └── Lab Engineering (400 TB) ← Excluded from "Allocated"
                    │
                    └── Level 3: Tenants (extracted from volume names)
                            ├── ENGR (Engineering - 150 TB)
                            ├── SALES (Sales - 120 TB)
                            ├── HR (Human Resources - 80 TB)
                            └── Buffer ← Excluded from display
                                │
                                └── Level 4: Volumes
                                        ├── ENGR_server01_vol1 (5 TB)
                                        ├── ENGR_server02_vol2 (8 TB)
                                        └── ENGR_backup_vol3 (12 TB)
```

### Capacity Calculation Logic

#### Key Terminology
1. **Total SAN Capacity**: Sum of ALL volumes including Lab Engineering
   ```python
   total_capacity = Σ(all volumes.volume_size_gb) / 1000  # In TB
   # Result: 8,000.00 TB
   ```

2. **Allocated**: Capacity assigned to active tenants (EXCLUDES Lab Engineering)
   ```python
   allocated = Σ(volumes where child_pool NOT IN ['Lab Engineering', 'buffer']).volume_size_gb / 1000
   # Result: 7,194.50 TB
   ```

3. **Utilized**: Actual storage consumed by tenants (based on "Written by Host %")
   ```python
   utilized = Σ(volumes where child_pool NOT 'Lab Engineering').utilized_gb / 1000
   # Result: 6,253.84 TB
   ```

4. **Unutilized**: Allocated but not yet used
   ```python
   unutilized = allocated - utilized
   # Result: 940.66 TB
   ```

5. **Available Buffer**: Unallocated capacity (includes Lab Engineering)
   ```python
   available_buffer = total_capacity - allocated
   # Result: 805.50 TB
   ```

6. **Average Utilization**: Percentage of allocated capacity being used
   ```python
   avg_utilization = (utilized / allocated) * 100
   # Result: 86.96%
   ```

#### Lab Engineering Filtering Rules
**Purpose**: "Lab Engineering" and "Buffer" are placeholders for unallocated capacity, NOT real tenants.

**Backend Filtering**:
```python
def is_lab_engineering(value):
    """Returns True if value represents unallocated capacity"""
    if not value:
        return True
    stripped = value.strip().lower()
    return stripped in ['lab engineering', 'buffer', '']
```

**Applied at**:
- **Pool level**: Exclude child pools named "Lab Engineering" from display tables
- **Child pool level**: Skip Lab Engineering when calculating pool aggregates
- **Tenant level**: Exclude tenants named "Buffer" or "Lab Engineering"
- **Top 10 Tenants**: Filter out unallocated placeholders

**NOT applied at**:
- **Total SAN Capacity calculation**: Lab Engineering IS included (represents physical capacity)
- **Available Buffer calculation**: Lab Engineering capacity = unallocated space

---

## 🔄 Data Flow & Processes

### 1. Data Upload Flow
```
Admin User → Upload Excel → Backend Validates → Parse with Pandas → Transform Data → Bulk Insert DB → Success Response
```

**Steps**:
1. Admin uploads `DATA_NEW.xlsx` via Upload component
2. Backend receives file in `/api/upload/` endpoint
3. Pandas reads Excel, validates required columns:
   - Volume, System, Pool, Volume Size (GB), Written by Host (%)
4. Calculate `utilized_gb` and `left_gb`:
   ```python
   df['Utilized GB'] = df['Written by Host (%)'] * df['Volume Size (GB)']
   df['Left GB'] = df['Volume Size (GB)'] - df['Utilized GB']
   ```
5. Filter out negative sizes (data quality check)
6. Delete existing data: `StorageData.objects.all().delete()`
7. Bulk insert new data: `StorageData.objects.bulk_create(storage_data)`
8. Verify totals match: Compare DB sum to Excel sum

### 2. Dashboard Data Retrieval Flow
```
User Selects Filter → Frontend Requests API → Backend Aggregates Data → Calculate Metrics → Return JSON → Frontend Renders Charts
```

**API Endpoint**: `GET /api/dashboard/?pool={pool}&child_pool={child_pool}&tenant={tenant}`

**Level 1 (Pools - No Parameters)**:
1. Calculate `total_capacity_tb` (ALL volumes including Lab Engineering)
2. Get distinct pools from database
3. For each pool:
   - Filter volumes: EXCLUDE child_pool IN ['Lab Engineering', 'buffer']
   - Aggregate: `SUM(volume_size_gb)`, `SUM(utilized_gb)`, `SUM(left_gb)`
   - Calculate `avg_util = utilized / allocated`
4. Extract top 10 tenants (by utilized_gb, excluding Lab Engineering)
5. Return JSON with `pools`, `top_tenants`, `total_capacity_tb`

**Level 2 (Child Pools - pool parameter)**:
1. Filter: `StorageData.objects.filter(pool=pool_name)`
2. Calculate `total_capacity_tb` (includes Lab Engineering child pool)
3. Get distinct child_pools, skip if `is_lab_engineering(cpname)`
4. Aggregate per child pool
5. Return JSON with `data`, `total_capacity_tb`, `breadcrumb`

**Level 3 (Tenants - pool + child_pool parameters)**:
1. Filter: `StorageData.objects.filter(pool=pool_name, child_pool=child_pool_name)`
2. Calculate `total_capacity_gb` (includes Buffer tenant capacity)
3. Extract tenant from volume name: `volume.split('_')[0]`
4. Skip if `is_lab_engineering(tenant)`
5. Aggregate per tenant
6. Return JSON with `data`, `total_capacity_gb`, `breadcrumb`

**Level 4 (Volumes - all parameters)**:
1. Filter: `volume__startswith=tenant_name`
2. Return individual volume records
3. Include `written_by_host_percent` (decimal ratio)

### 3. Drill-Down Navigation Flow
```
User Clicks Pool → Update Filter State → Fetch Child Pools → Render Table → Click Child Pool → Fetch Tenants → etc.
```

**State Management**:
```javascript
const [filter, setFilter] = useState({})
// Level 1: filter = {}
// Level 2: filter = { pool: "A9K-A1" }
// Level 3: filter = { pool: "A9K-A1", child_pool: "HST-Pool" }
// Level 4: filter = { pool: "A9K-A1", child_pool: "HST-Pool", tenant: "ENGR" }
```

**Navigation Handlers**:
- `handleDrillDown(type, value)`: Add filter parameter, trigger API call
- `handleBack()`: Remove last filter parameter, navigate up one level

### 4. Authentication Flow
```
User Login → POST /api/login/ → Django Auth → Create Session → Set Cookie → Subsequent Requests Include Cookie → Session Validated
```

**Login Process**:
1. User enters username/password in Login component
2. Frontend: `POST /api/login/` with credentials
3. Backend: `authenticate(request, username, password)`
4. If valid: `login(request, user)` creates session
5. Django returns session cookie in response headers
6. Browser stores cookie, includes in all subsequent requests
7. Backend validates session via `@permission_classes([IsAuthenticated])`

**Logout Process**:
1. User clicks Logout button
2. Frontend: `POST /api/logout/`
3. Backend: `logout(request)` destroys session
4. Frontend redirects to login page

---

## 🎨 User Interface Components

### Dashboard Layout
```
┌──────────────────────────────────────────────────────────────┐
│ [Back] [Refresh]  Level: pools | Pool: A9K-A1  [GB TB PB] [Logout] │
├──────────────────────────────────────────────────────────────┤
│                     SUMMARY TABLE                            │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ Total SAN   │ Available  │ Allocated │ Utilized │ Unutilized │ Avg Util │
│ │ 8,000.00 TB │ 805.50 TB  │ 7,194.50 TB │ 6,253.84 TB │ 940.66 TB │ 86.96% │
│ └──────────────────────────────────────────────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│            DONUT CHART               │     BAR CHART         │
│  ┌──────────────────────────┐        │  ┌─────────────────┐ │
│  │  Pool Utilization        │        │  │ Top 10 Tenants  │ │
│  │  Distribution            │        │  │ by Utilization  │ │
│  │                          │        │  │                 │ │
│  │   [Inner: Pool Breakdown]│        │  │  █ ENGR 150 TB  │ │
│  │   [Outer: Util/Unutil]   │        │  │  █ SALES 120 TB │ │
│  │                          │        │  │  █ HR 80 TB     │ │
│  └──────────────────────────┘        │  └─────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│                    DATA TABLE (Pools)                        │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ Pool    │ Allocated │ Utilized │ Unutilized │ Avg Util % │ │
│ │ A9K-A1  │ 1,438.90  │ 1,250.77 │ 188.13     │ 86.96%     │ │
│ │ A9K-A2  │ 1,438.90  │ 1,250.77 │ 188.13     │ 86.96%     │ │
│ │ V7K-R3  │ 1,438.90  │ 1,250.77 │ 188.13     │ 86.96%     │ │
│ └──────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘
```

### Component Breakdown

#### 1. Summary Table
- **Purpose**: Display high-level capacity metrics at a glance
- **Columns**: Total SAN Capacity, Available Buffer, Allocated, Utilized, Unutilized, Avg Utilization
- **Data Source**: Aggregated from backend response
- **Responsive**: Values update based on selected unit (GB/TB/PB)
- **Styling**: Large bold fonts (18px, font-weight: 600) for easy readability

#### 2. Donut Chart (Double Ring)
- **Library**: Apache ECharts
- **Purpose**: Visual breakdown of capacity distribution
- **Outer Ring**: Overall Utilized (blue) vs. Unutilized (gray)
- **Inner Ring**: Individual pool/child pool/tenant breakdown (10 distinct colors)
- **Interaction**: Hover for tooltips showing exact values and percentages
- **Label Strategy**:
  - Outside labels with connector lines
  - Collision detection (`hideOverlap: true`)
  - Minimum angle threshold (hide segments < 3-5 degrees)
- **Color Palette**:
  - Outer Utilized: `#0f62fe` (IBM Blue)
  - Outer Unutilized: `#e0e0e0` (Gray)
  - Inner Ring: Purple, Pink, Red, Green, Yellow, Teal, Magenta, Brown, Light Blue, Rose

#### 3. Bar Chart (Top 10 Tenants)
- **Library**: Apache ECharts
- **Purpose**: Identify highest storage consumers
- **Display**: Top 10 tenants by utilized capacity
- **Interaction**: Hover for exact values
- **Filtering**: Excludes Lab Engineering and Buffer tenants
- **Data Labels**: Values shown above each bar (16px, bold)
- **Axes**: 
  - X-axis: Tenant names (rotated 45° for readability)
  - Y-axis: Capacity in selected unit (GB/TB/PB)

#### 4. Data Table
- **Library**: Carbon Design System DataTable
- **Purpose**: Detailed list with drill-down capability
- **Features**:
  - Clickable rows (cursor changes to pointer)
  - Hover highlighting (background color change)
  - Bordered cells for clear data separation
  - Centered text alignment
  - Responsive to unit changes
- **Columns**: Vary by level (pools, child pools, tenants, volumes)
- **Navigation**: Click any row to drill down to next level

#### 5. Navigation Bar
- **Back Button**: Navigate up one level (hidden at pools level)
- **Refresh Button**: Reload current level data
- **Breadcrumb**: Show current location (e.g., "Level: tenants | Pool: A9K-A1 > HST-Pool")
- **Unit Toggle**: GB / TB / PB radio button group
- **Logout Button**: End session and return to login

#### 6. Upload Component (Admin Only)
- **Purpose**: Allow admins to upload new Excel data
- **File Input**: Standard file input (v5.7.2: replaced Carbon FileUploader due to UI bug)
- **Validation**: File type check, size limits
- **Feedback**: Success/error messages after upload
- **Access Control**: Only visible to `is_staff` users

---

## 🔐 Security & Authentication

### Authentication Model
- **Type**: Django session-based authentication
- **Cookie**: `sessionid` cookie stored in browser
- **Duration**: Default Django session timeout (2 weeks, configurable)
- **Storage**: Sessions stored in `django_session` database table

### Authorization Rules
| Endpoint        | Authentication | Authorization                          |
|-----------------|----------------|----------------------------------------|
| `/api/login/`   | None (public)  | N/A                                    |
| `/api/logout/`  | Required       | Any authenticated user                 |
| `/api/dashboard/` | Required     | Any authenticated user                 |
| `/api/upload/`  | Required       | `is_staff=True` users only             |
| `/api/check-auth/` | Required    | Any authenticated user                 |

### Security Measures
1. **Password Hashing**: Django's PBKDF2 algorithm with SHA256 hash
2. **CSRF Protection**: Django CSRF middleware (with custom exemption for session auth in DRF)
3. **SQL Injection Prevention**: Django ORM parameterized queries
4. **XSS Prevention**: React auto-escapes user input
5. **HTTPS**: Recommended for production deployment (not enforced in dev)

### User Roles
1. **Regular User** (`is_staff=False`):
   - View dashboard
   - Navigate all drill-down levels
   - Change unit display
   - Cannot upload data

2. **Admin User** (`is_staff=True`):
   - All regular user permissions
   - Upload new Excel data
   - Replace entire dataset

---

## 🚀 Deployment & Operations

### Development Environment
```bash
# Backend
cd backend
python manage.py runserver 0.0.0.0:8000

# Frontend
cd frontend
npm start  # Runs on http://localhost:3000
```

### Production Deployment (Docker)
```yaml
# docker-compose.yml
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: storage_analytics
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  backend:
    build: ./backend
    command: gunicorn backend.wsgi:application --bind 0.0.0.0:8000
    environment:
      DATABASE_URL: postgresql://postgres:password@db:5432/storage_analytics
    depends_on:
      - db

  frontend:
    build: ./frontend
    depends_on:
      - backend

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - frontend
      - backend
```

### Database Backup & Restore
**Backup Scripts**:
- `backup_database.sh`: Automated PostgreSQL dump
- `check_database.sh`: Verify database health
- `restore_database.sh`: Restore from backup

**Backup Strategy**:
```bash
# Daily automated backup
./backup_database.sh

# Verify backup integrity
./check_database.sh

# Restore if needed
./restore_database.sh backup_YYYY-MM-DD.sql
```

---

## 📈 Key Metrics & Performance

### Application Metrics
- **Total SAN Capacity**: 8,000.00 TB (8 PB)
- **Available Buffer**: 805.50 TB
- **Allocated Capacity**: 7,194.50 TB (89.93% of total)
- **Utilized Capacity**: 6,253.84 TB (86.96% of allocated)
- **Average Utilization**: 86.96%
- **Pools**: 5 parent systems
- **Child Pools**: ~25-30 (varies by upload)
- **Tenants**: ~100-200 (extracted from volume names)
- **Volumes**: ~10,000-50,000 (varies by organization size)

### Performance Characteristics
- **API Response Time**: <500ms for pools level (typical)
- **Database Query Time**: <200ms for aggregations (with proper indexes)
- **Frontend Rendering**: <100ms for chart generation
- **Excel Upload Time**: ~5-30 seconds (depends on file size)
- **Concurrent Users**: Tested with 10-50 simultaneous users
- **Database Size**: ~50-500 MB (depends on volume count)

---

## 🐛 Known Issues & Limitations

### Current Limitations
1. **Single Excel Upload**: Replaces entire dataset (no incremental updates)
2. **No Historical Tracking**: Only current snapshot, no time-series data
3. **No Export Feature**: Cannot export filtered views back to Excel
4. **Limited Alerting**: No threshold-based capacity warnings
5. **No Tenant Hierarchy**: Flat tenant structure (no parent-child tenants)
6. **Manual Tenant Extraction**: Relies on volume naming convention (`TENANT_server_vol`)

### Fixed Issues (Version History)
- **v5.7.2**: Fixed black circle artifact in upload section (replaced Carbon FileUploader)
- **v5.7.1**: Fixed black background in donut chart center (transparent backgroundColor)
- **v5.7.0**: Fixed Available Buffer = 0.00 TB (corrected Lab Engineering filtering)
- **v5.6.0**: Fixed Buffer tenant appearing in Top 10 chart
- **v5.5.0**: Fixed calculation discrepancies between Total and Allocated

---

## 🔮 Future Enhancement Roadmap

### Short-Term Enhancements (3-6 months)
1. **Capacity Alerts**:
   - Email notifications when Available Buffer < threshold (e.g., 10%)
   - Tenant-level utilization alerts (e.g., tenant using > 80%)

2. **Export Functionality**:
   - Export current view to Excel
   - Export filtered data (e.g., only one pool's child pools)
   - PDF report generation for executives

3. **Search & Filter**:
   - Search volumes by name
   - Filter by utilization percentage range
   - Filter by capacity range

4. **User Management UI**:
   - Admin panel to create/modify users
   - Password reset functionality
   - Role management (viewer vs. admin)

### Medium-Term Enhancements (6-12 months)
1. **Historical Trending**:
   - Store snapshots over time
   - Line charts showing capacity growth
   - Predict when capacity will be exhausted

2. **Advanced Analytics**:
   - Identify underutilized storage (allocated but <20% used)
   - Tenant growth rate analysis
   - Capacity planning recommendations

3. **Multi-Datacenter Support**:
   - Support multiple separate SAN environments
   - Datacenter-level drill-down (DC → Pools → Child Pools → Tenants → Volumes)
   - Cross-datacenter capacity comparison

4. **API Enhancements**:
   - RESTful API documentation (Swagger/OpenAPI)
   - Programmatic data upload via API
   - Webhook notifications for capacity events

### Long-Term Enhancements (12+ months)
1. **Real-Time Data Integration**:
   - Direct integration with SAN management APIs (NetApp, EMC, Pure Storage)
   - Automated data refresh (hourly/daily)
   - Eliminate manual Excel uploads

2. **Advanced Visualizations**:
   - Heatmaps showing utilization patterns
   - Sankey diagrams for capacity flow
   - Treemaps for hierarchical capacity view

3. **Machine Learning**:
   - Predict future capacity needs based on historical trends
   - Anomaly detection (unusual storage growth)
   - Automatic tenant categorization

4. **Mobile Support**:
   - Responsive mobile UI
   - Native iOS/Android apps
   - Push notifications for capacity alerts

---

## 📚 Documentation Index

### Existing Documentation Files
1. **README.md**: Current status, features, deployment instructions
2. **BRANCHING_STRATEGY.md**: Git workflow and version control practices
3. **CREATE_USERS.md**: User creation and management guide
4. **DATABASE_PERSISTENCE_GUIDE.md**: Database backup and recovery procedures
5. **FOLLOW_THESE_STEPS.md**: Quick start guide for new developers
6. **QUICK_START.md**: Fast deployment instructions
7. **V5_ECHARTS_INSTALLATION_INSTRUCTIONS.md**: ECharts integration guide

### Related Scripts
- `backup_database.sh`: Database backup automation
- `check_database.sh`: Database health verification
- `restore_database.sh`: Database recovery
- `start.sh`: Application startup script

---

## 🤝 Contributing & Development

### Development Workflow
1. Clone repository from GitHub
2. Create feature branch from `main`
3. Make changes and test locally
4. Commit with descriptive message
5. Push to GitHub
6. Create pull request for review
7. Merge to `main` after approval
8. Tag releases (e.g., v5.7, v5.8)

### Code Standards
- **Frontend**: ESLint rules from create-react-app
- **Backend**: PEP 8 Python style guide
- **Git Commits**: Descriptive messages with version tags
- **Branch Naming**: `vX.Y-branch` for version branches, descriptive names for features

### Testing Checklist
- [ ] Test all four drill-down levels
- [ ] Verify Lab Engineering filtering
- [ ] Check capacity calculations (Total, Allocated, Utilized, Available Buffer)
- [ ] Test unit conversion (GB/TB/PB)
- [ ] Verify Top 10 Tenants excludes Buffer
- [ ] Test upload with sample data
- [ ] Verify authentication (login/logout)
- [ ] Check admin-only upload access

---

## 📞 Support & Contact

### Repository
- **GitHub**: https://github.com/neilpandey27-web/SAN_Storage_Dashboard_PG
- **Current Branch**: v5.8-branch
- **Latest Release**: v5.8

### Key Contacts
- **Primary Developer**: neilpandey27-web (GitHub)
- **Project Type**: Enterprise SAN Storage Analytics Dashboard

### Getting Help
1. Check existing documentation files (README.md, QUICK_START.md, etc.)
2. Review GitHub Issues for known problems
3. Consult CONTEXT.md (this file) for architectural questions
4. Contact repository maintainer for critical issues

---

## 📝 Change Log

### Version 5.8 (Current)
- Pushed to GitHub with v5.8 tag
- Merged to main branch
- All previous features stable

### Version 5.7.2
- **Fix**: Removed black circle artifact in upload section
- **Change**: Replaced Carbon Design FileUploader with standard HTML input element
- **Reason**: Carbon component had rendering bug

### Version 5.7.1
- **Fix**: Removed black circle in donut chart center
- **Change**: Set donut chart `backgroundColor: 'transparent'`
- **Impact**: Cleaner visual appearance

### Version 5.7.0
- **Major Fix**: Available Buffer calculation
- **Root Cause**: Lab Engineering not filtered at child_pool level
- **Solution**: Backend filters Lab Engineering at child_pool level, not pool level
- **Impact**: Available Buffer now shows correct 805.50 TB (was 0.00 TB)
- **Related**: Removed Buffer tenant from Top 10 Tenants chart

### Version 5.6.0
- **Fix**: Buffer tenant appearing in Top 10 Tenants
- **Change**: Added `is_lab_engineering()` function to filter placeholder tenants
- **Impact**: Top 10 chart now only shows real tenants

---

**End of Context Document**

*This document provides a comprehensive overview of the SAN Storage Dashboard application, suitable for new developers, system administrators, and stakeholders. For specific technical details, refer to the codebase and related documentation files.*

---

**Document Version**: 1.0  
**Last Updated**: 2025-11-23  
**Maintained By**: Project Team  
**Next Review**: 2025-12-23
