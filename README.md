<p align="center">
  <img src="./img.png" alt="Secure Campus Hub Banner" width="100%">
</p>

# Secure Campus Hub 🛡️

## Basic Details

### Team Members
- Member 1: Sanjana S - SCMS School of Engineering & Technology, Karukutty

### Hosted Project Link
https://secure-campus-app-eb6pjweparpreznpa3fmdl.streamlit.app/

### Project Description
Secure Campus Hub is a comprehensive digital solution designed to streamline hostel and campus management. It facilitates seamless communication and service requests between students, wardens, and the principal, ensuring efficient administration and a better campus experience.

### The Problem statement
Managing campus facilities such as hostel complaints, leave requests, medicine inventory, parking slots, and service queues manually is inefficient, time-consuming, and prone to errors. There is often a lack of transparency and real-time tracking for students and administrators.

### The Solution
We have built a role-based web application using Streamlit that centralizes all these operations. Students can easily log complaints, apply for leave, and request resources. Wardens have a powerful dashboard to manage these requests and monitor inventory (like medicine expiry alerts). The Principal has a high-level view to oversee critical issues.

---

## Technical Details

### Technologies/Components Used

**For Software:**
- **Languages used:** Python
- **Frameworks used:** Streamlit
- **Libraries used:** Pandas
- **Tools used:** VS Code, Git

---

## Features

### 🎓 Student Portal
- **Complaint Management:** Log complaints regarding Academics, Mess, Security, or Infrastructure directly to the Warden or Principal. Track complaint status in real-time.
- **MediScan & Request:** View available medicine stock in the clinic. Request medicines and report symptoms.
- **Queue Tokens:** Generate digital tokens for campus services (Library, Canteen, Mess, Office) to avoid standing in long lines.
- **Parking Management:** Request parking slots for vehicles.
- **Leave Management:** Apply for leave with dates and reasons. Cancel leave requests if plans change.

### 👮 Warden Dashboard
- **Expiry Alert System:** Automated alerts for expired and near-expiry medicines to ensure safety.
- **Leave & Parking Approval:** Review and approve/reject student leave and parking requests.
- **Inventory Management:** Add, update, or delete medicine stock. View low-stock alerts.
- **Request fulfillment:** Update status of medicine requests and queue tokens.

### 🎓 Principal Dashboard
- **High-Level Overview:** View complaints escalated to the Principal.
- **Resolution:** Review and resolve critical complaints with official responses.

---

## Implementation

### Installation
```bash
# Clone the repository
git clone [repo-link]

# Install dependencies
pip install -r requirements.txt.txt
```

### Run
```bash
# Start the application
streamlit run app.py.py
```

### Credentials (Demo)
*The system uses a simple CSV-based authentication. You can sign up as a new user or use existing credentials found in `users_db.csv` after the first run.*

---
**System Architecture:**
Values entered by users are stored in local CSV files (`users_db.csv`, `hostel_data.csv`, etc.) which act as a lightweight database. Streamlit serves the frontend and handles logic, reading/writing to these files directly.


## Made with ❤️ at TinkerHub
