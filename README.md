# smart_voting_system_using_facial_recognition
A Flask-based **academic demonstration project** for voter registration,
OTP verification, face registration and verification, administrator
approval, and electronic vote recording.

> **Important:** This project is for learning and demonstration only. It
> is not certified or suitable for conducting a real public election. A
> production voting system requires independent security, privacy,
> accessibility, legal, and election-integrity reviews.

## Features

### Voter

-   Register with personal and constituency details.
-   Validate age (18+), mobile number, Aadhaar number, and CAPTCHA.
-   Upload Voter ID and Aadhaar documents.
-   Request an OTP by SMS or voice call through the configured 2Factor
    API integration.
-   Upload a face photo or capture one using the browser camera.
-   Check registration approval status.
-   Verify a registered face before voting.
-   Select a candidate and submit one vote.
-   View candidate vote totals through the results page, where enabled.

### Administrator

-   Sign in to the admin area.
-   Review pending voter registrations and uploaded documents.
-   Approve or reject registrations.
-   View approved voters and voting totals.
-   View vote counts and charts.
-   Export dashboard data to Excel or PDF (browser-side libraries).
-   Delete a recorded vote through the dashboard.

## Technology stack

-   **Backend:** Python, Flask, Flask-SQLAlchemy
-   **Database:** SQLite
-   **Frontend:** HTML, CSS, JavaScript, Bootstrap 5
-   **Face recognition:** `face_recognition` (uses dlib)
-   **Image processing:** Pillow and NumPy
-   **OTP integration:** 2Factor API via Requests
-   **Dashboard charts/export:** Chart.js, SheetJS (XLSX), jsPDF

## Project structure

The supplied ZIP contains the Python application and HTML templates at
its root. The application configures Flask to look for templates in a
folder named `templates`, so arrange the files like this before running:

``` text
voting-project/
├── voting.py
├── requirements.txt
├── templates/
│   ├── admin.html
│   ├── admin_dashboard.html
│   ├── admin_login.html
│   ├── base.html
│   ├── face_verify.html
│   ├── index.html
│   ├── pending_voters.html
│   ├── register.html
│   ├── registered_wait.html
│   ├── results.html
│   ├── save_face.html
│   ├── track_status.html
│   ├── tracking.html
│   ├── upload_face.html
│   ├── verify_face.html
│   ├── verify_otp.html
│   └── vote.html
└── static/
    ├── css/              # Add the CSS files referenced by templates
    ├── js/               # Add gov.js if used
    ├── images/           # Add referenced images/backgrounds
    ├── symbols/          # Add candidate symbols and logo assets
    ├── uploads/
    │   ├── ids/          # Created automatically
    │   └── aadhaar/      # Created automatically
    └── voter_photos/     # Created automatically
```

The ZIP provided for this README does not include the `static/` assets.
Add the referenced stylesheets, logos, candidate symbols, and background
images, or remove/update those references in the templates. The
application creates its upload directories when it starts.

## Requirements

-   Python 3.9 or newer (a compatible Python version for your installed
    `face_recognition`/dlib build is required).
-   pip
-   A working webcam for live face capture and verification.
-   Internet access for Bootstrap/CDN assets and the OTP provider.
-   A 2Factor account and valid API credentials if you want to test OTP
    delivery.

Create a `requirements.txt` file with:

``` text
Flask
Flask-SQLAlchemy
Pillow
numpy
face-recognition
requests
```

`face-recognition` depends on **dlib**, which can be difficult to
install on some Windows systems. Install the required C++ build tools or
use a compatible prebuilt environment if pip cannot build it. The
project does not include a pinned, tested dependency lockfile.

## Setup and run

### 1. Extract and arrange the files

Extract `voting.zip`. Create a folder named `templates` and move all the
supplied `.html` files into it. Keep `voting.py` in the project root.
Add the required `static` assets as described above.

### 2. Create a virtual environment

**Windows (PowerShell):**

``` powershell
py -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**

``` bat
py -m venv venv
venv\Scripts\activate
```

**Linux/macOS:**

``` bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

``` bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configure secrets and OTP credentials

Do not publish API keys or secret keys in source control. The supplied
`voting.py` currently contains a hard-coded OTP API key and a fallback
Flask secret. Replace this configuration with environment variables
before testing or sharing the project.

For example, update the application to read the OTP credential from an
environment variable such as `TWOFACTOR_API_KEY`, and set a strong
random `FLASK_SECRET`. Then configure them in your terminal before
starting the app.

**Windows PowerShell:**

``` powershell
$env:FLASK_SECRET = "replace-with-a-long-random-secret"
$env:TWOFACTOR_API_KEY = "your-provider-api-key"
```

**Linux/macOS:**

``` bash
export FLASK_SECRET="replace-with-a-long-random-secret"
export TWOFACTOR_API_KEY="your-provider-api-key"
```

The source currently uses a variable named `FAST2SMS_API_KEY` for the
2Factor request URL. Make sure the code and environment variable name
are made consistent when you update it. Never commit real credentials.

### 5. Start the application

From the project root, with the virtual environment activated:

``` bash
python voting.py
```

The application initializes its SQLite tables with `db.create_all()` and
starts Flask's development server. Open:

``` text
http://127.0.0.1:5000/
```

Use the Flask development server only for local testing. Do not expose
it as a production service.

## Main application pages

  Page / route         Purpose
  -------------------- -----------------------------------------
  `/`                  Home page
  `/register`          Voter registration
  `/verify_otp`        OTP verification
  `/save_face`         Save uploaded/captured face
  `/track`             Track registration approval
  `/face_verify`       Start face verification
  `/verify`            Face verification endpoint (POST JSON)
  `/vote`              Candidate selection and vote submission
  `/admin`             Administrator login
  `/admin/dashboard`   Admin dashboard
  `/admin/logout`      Admin logout

## Database overview

The application defines two SQLAlchemy models:

-   **Voter:** stores registration details, document filenames, face
    encoding, approval status, and whether the voter has voted.
-   **Vote:** stores the voter reference and selected candidate.

The SQLite database is configured as `voting.db` under Flask's instance
database location. It is created automatically when `voting.py` is run.

## Admin login

The supplied source code currently checks these demonstration
credentials:

``` text
Username: admin
Password: admin123
```

Change this immediately before any shared demonstration. The credentials
are hard-coded in the current source and are not appropriate for a real
deployment.

## Important implementation and security notes

-   This is a prototype, not a secure election platform. Do not use real
    voter information or real election data.
-   The code handles highly sensitive identifiers and identity
    documents. Use synthetic test data only, restrict access, encrypt
    data at rest and in transit, define retention/deletion rules, and
    obtain appropriate consent.
-   Replace the hard-coded admin credentials, API key, and fallback
    Flask secret. Use secure password hashing and environment-based
    configuration.
-   Add CSRF protection, rate limiting, secure session-cookie settings,
    strict upload validation, and authorization checks to every
    administrative action.
-   OTP delivery depends on a valid provider account, API configuration,
    network access, and provider rules. Test with authorized numbers
    only.
-   Browser camera access normally requires `localhost` or HTTPS and
    user permission.
-   Face matching can produce false matches and false rejections. The
    current tolerance value is a prototype setting, not a validated
    security threshold.
-   The current code stores face encodings using SQLAlchemy's
    `PickleType`; do not load untrusted serialized data.
-   The vote page's JavaScript sends a request with `fetch()` and
    displays a success message without checking the server response.
    Verify the backend response before treating a vote as recorded.
-   Add database transactions, audit logging, automated tests, and
    independent security review before considering any broader use.

## Troubleshooting

**`TemplateNotFound` error** - Confirm that all HTML files are inside
the `templates/` directory beside `voting.py`.

**Missing CSS, images, or candidate symbols** - The supplied ZIP
contains HTML and Python files but no static asset directory. Add the
referenced files under `static/` and check their paths.

**Camera does not open** - Allow camera permission in the browser and
use `http://127.0.0.1:5000` or HTTPS. Check that another application is
not using the webcam.

**Face recognition installation fails** - Check Python and dlib
compatibility, install the platform's C++ build tools, and follow the
installation instructions for `face_recognition` and dlib.

**OTP is not received** - Verify the provider API key, endpoint/account
configuration, phone number, network connection, and provider response
printed by the application.

**Database changes are not visible** - Stop the app and confirm you are
using the expected SQLite database file. Back up the database before
deleting or recreating it.

## License

No license file was included in the supplied project. Add a license
before distributing or reusing the code.
