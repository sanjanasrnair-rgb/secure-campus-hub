<p align="center">
  <img src="./img.png" alt="Project Banner" width="100%">
</p>

SCANNER-CAMPUS-HUB 🎯

The main aim of this project is to provide a centralized digital platform to manage different campus services like:

Complaint Management

Leave Application

Medicine Requests

Parking Slot Booking

Queue Token Generation

This system helps in reducing manual work and improves communication between students and college authorities.

individual project

### Hosted Project Link
https://secure-campus-app-eb6pjweparpreznpa3fmdl.streamlit.app/

### Project Description
Secure Campus Hub is a web-based smart campus management system developed using Streamlit that integrates essential student services into a single digital platform. It allows students to submit complaints, request medicines, apply for leave, generate queue tokens and book parking slots online. The system enables wardens and principals to monitor, manage and respond to these requests efficiently.

### The Problem statement
In college campuses, services like complaint submission, leave application, medicine requests, parking slot allocation and queue management are handled manually. This leads to delays, miscommunication, lack of transparency and inefficient management of campus resources.

### The Solution
Secure Campus Hub provides a centralized web-based platform developed using Streamlit where students can digitally submit requests and track their status. Wardens and principals can review, approve or manage these requests through dedicated dashboards, ensuring faster response, better communication and efficient resource management within the campus.

---

## Technical Details
Technical Details

Frontend: Streamlit User Interface

Backend: Python

Framework Used: Streamlit

Database: CSV Files

Libraries Used: Pandas, Datetime, OS

Programming Language: Python

Architecture: Role-Based Login System (Student / Warden / Principal)

Data Storage: Local File Storage using CSV

Platform: Web Application

### Technologies/Components Used
Technologies/Components Used

Programming Language: Python

Framework: Streamlit

Libraries Used: Pandas, Datetime, OS

Database: CSV Files

Frontend: Streamlit UI

Backend: Python Logic

Platform: Web-Based Application

IDE Used: VS Code / Any Python IDE

System Components:

Complaint Management Module

Leave Management Module

Medicine Request & Stock Module

Parking Management Module

Queue Token System

Role-Based Login System

**For Software:**
Languages used: Python

Frameworks used: Streamlit

Libraries used: Pandas, Datetime, OS

Tools used: VS Code, Git, Web Browser (Chrome)

**For Hardware:**

Main Components:

Laptop / Desktop Computer

Keyboard & Mouse

Internet Connection

Specifications:

Minimum 4GB RAM

Intel i3 Processor or above

500GB Hard Disk

Windows 10 / Linux / macOS

Python Installed

Tools Required:

Web Browser (Chrome / Edge)

Python Environment

Code Editor (VS Code)
import streamlit as st
import pandas as pd
from datetime import datetime, date, timedelta
import os

# --- 1. PAGE CONFIG ---
st.set_page_config(page_title="Secure Campus Portal", page_icon="🛡️", layout="wide")

# --- 2. THEME & STYLING ---
def set_professional_theme():
    st.markdown("""
        <style>
        .stApp { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); background-attachment: fixed; }
        [data-testid="stSidebar"] { background-color: #7f1d1d !important; }
        [data-testid="stSidebar"] .stMarkdown, [data-testid="stSidebar"] label, 
        [data-testid="stSidebar"] p, [data-testid="stSidebar"] span { color: #ffffff !important; }
        h1, h2, h3, p, span, label, .stSubheader { color: #f8fafc !important; }
        div[data-baseweb="input"] > div, div[data-baseweb="select"] > div, textarea {
            background-color: #991b1b !important; color: white !important; border: 1px solid #ef4444 !important;
        }
        input, select, textarea { color: white !important; -webkit-text-fill-color: white !important; }
        .stDataFrame { background-color: white !important; border-radius: 8px; }
        .stButton>button { border-radius: 6px; background-color: #3b82f6 !important; color: white !important; font-weight: bold; width: 100%; }
        </style>
    """, unsafe_allow_html=True)

set_professional_theme()

# --- 3. DATA INITIALIZATION ---
FILES = {
    "users": "users_db.csv",
    "hostel": "hostel_data.csv",
    "meds": "medicines.csv",
    "med_req": "medicine_requests.csv",
    "parking": "parking_data.csv",
    "queue": "queue_data.csv",
    "leave": "leave_data.csv"
}

def init_files():
    for key, path in FILES.items():
        if not os.path.exists(path):
            if key == "users":
                pd.DataFrame(columns=["Institution", "Username", "Password", "Role"]).to_csv(path, index=False)
            elif key == "hostel":
                pd.DataFrame(columns=["ID", "Institution", "User", "Target", "Category", "Description", "Status", "Admin_Message", "Timestamp"]).to_csv(path, index=False)
            elif key == "meds":
                pd.DataFrame(columns=["Medicine_Name", "Category", "Stock_Count", "Manufacture_Date", "Expiry_Date"]).to_csv(path, index=False)
            elif key == "med_req":
                pd.DataFrame(columns=["ID", "User", "Medicine_Type", "Details", "Status", "Warden_Note", "Timestamp"]).to_csv(path, index=False)
            elif key == "parking":
                pd.DataFrame(columns=["ID", "User", "Vehicle_No", "Slot", "Status", "Timestamp"]).to_csv(path, index=False)
            elif key == "queue":
                pd.DataFrame(columns=["ID", "User", "Location", "Token", "Status", "Timestamp"]).to_csv(path, index=False)
            elif key == "leave":
                pd.DataFrame(columns=["ID", "User", "Reason", "From_Date", "To_Date", "Status", "Timestamp"]).to_csv(path, index=False)

init_files()

# --- 4. HELPERS ---
def highlight_meds(row):
    try:
        exp_date = pd.to_datetime(row['Expiry_Date']).date()
        is_expired = exp_date < date.today()
    except: is_expired = False
    if row['Stock_Count'] < 5 or is_expired:
        return ['background-color: #fee2e2; color: #b91c1c'] * len(row)
    return [''] * len(row)

# --- 5. STUDENT VIEW ---
def student_view():
    st.title(f"🏫 {st.session_state.inst_name.upper()} Student Portal")
    if st.sidebar.button("Logout"): 
        for key in list(st.session_state.keys()): del st.session_state[key]
        st.rerun()
    
    menu = st.sidebar.radio("Navigation", ["📝 Complaints", "🚑 MediScan & Med-Request", "🎟️ Queue Tickets", "🚗 Parking", "🏠 Leave Management"])
    
    if menu == "📝 Complaints":
        t1, t2 = st.tabs(["New Complaint", "Track History"])
        with t1:
            with st.form("c_form"):
                target = st.selectbox("Direct To", ["Warden", "Principal", "Both"])
                cat = st.selectbox("Category", ["Academics", "Mess", "Security", "Infrastructure"])
                desc = st.text_area("Details")
                if st.form_submit_button("Submit"):
                    df = pd.read_csv(FILES["hostel"])
                    new_entry = {"ID": len(df)+1, "Institution": st.session_state.inst_name, "User": st.session_state.username, "Target": target, "Category": cat, "Description": desc, "Status": "Pending", "Admin_Message": "Waiting...", "Timestamp": datetime.now()}
                    pd.concat([df, pd.DataFrame([new_entry])], ignore_index=True).to_csv(FILES["hostel"], index=False)
                    st.success("Complaint Logged!")
        with t2:
            df_h = pd.read_csv(FILES["hostel"])
            st.dataframe(df_h[df_h['User'] == st.session_state.username], use_container_width=True)

    elif menu == "🚑 MediScan & Med-Request":
        t1, t2 = st.tabs(["Stock & Request", "My Med Requests"])
        with t1:
            st.subheader("Available Clinic Stock")
            m_df = pd.read_csv(FILES["meds"])
            selected_med = st.selectbox("Quick Select Available Medicine", ["-- Select --"] + m_df["Medicine_Name"].fillna("Unknown").astype(str).tolist())
            st.dataframe(m_df.style.apply(highlight_meds, axis=1), use_container_width=True)
            st.markdown("---")
            st.subheader("Request Medicine")
            with st.form("med_req_form"):
                default_val = "" if selected_med == "-- Select --" else selected_med
                m_type = st.text_input("Medicine Needed", value=default_val)
                m_det = st.text_area("Symptoms")
                if st.form_submit_button("Send Request"):
                    df = pd.read_csv(FILES["med_req"])
                    new_r = {"ID": len(df)+1, "User": st.session_state.username, "Medicine_Type": m_type, "Details": m_det, "Status": "Pending", "Warden_Note": "No updates yet", "Timestamp": datetime.now()}
                    pd.concat([df, pd.DataFrame([new_r])], ignore_index=True).to_csv(FILES["med_req"], index=False)
                    st.success("Request sent!")
        with t2:
            df_r = pd.read_csv(FILES["med_req"])
            st.dataframe(df_r[df_r['User'] == st.session_state.username], use_container_width=True)

    elif menu == "🎟️ Queue Tickets":
        st.subheader("Generate Token")
        with st.form("queue_form"):
            loc = st.selectbox("Select Service", ["Library", "Canteen", "Mess", "Office"])
            if st.form_submit_button("Generate Token"):
                df = pd.read_csv(FILES["queue"])
                token = f"{loc[:3].upper()}-{len(df)+101}"
                new_q = {"ID": len(df)+1, "User": st.session_state.username, "Location": loc, "Token": token, "Status": "In Queue", "Timestamp": datetime.now()}
                pd.concat([df, pd.DataFrame([new_q])], ignore_index=True).to_csv(FILES["queue"], index=False)
                st.success(f"Your Token: {token}")
        df_q = pd.read_csv(FILES["queue"])
        st.dataframe(df_q[df_q['User'] == st.session_state.username], use_container_width=True)

    elif menu == "🚗 Parking":
        st.subheader("Request Parking Slot")
        with st.form("park_form"):
            v_no = st.text_input("Enter Vehicle Number")
            if st.form_submit_button("Request Slot"):
                df = pd.read_csv(FILES["parking"])
                new_p = {"ID": len(df)+1, "User": st.session_state.username, "Vehicle_No": v_no, "Slot": "Awaiting Approval", "Status": "Pending", "Timestamp": datetime.now()}
                pd.concat([df, pd.DataFrame([new_p])], ignore_index=True).to_csv(FILES["parking"], index=False)
                st.success("Request sent to Warden!")
        df_p = pd.read_csv(FILES["parking"])
        st.dataframe(df_p[df_p['User'] == st.session_state.username], use_container_width=True)

    elif menu == "🏠 Leave Management":
        t1, t2 = st.tabs(["Apply for Leave", "My Status & Cancellations"])
        with t1:
            with st.form("leave_form"):
                reason = st.text_input("Reason for Leave")
                f_date = st.date_input("From Date")
                t_date = st.date_input("To Date")
                if st.form_submit_button("Apply to Warden"):
                    df = pd.read_csv(FILES["leave"])
                    new_l = {"ID": len(df)+1, "User": st.session_state.username, "Reason": reason, "From_Date": f_date, "To_Date": t_date, "Status": "Pending", "Timestamp": datetime.now()}
                    pd.concat([df, pd.DataFrame([new_l])], ignore_index=True).to_csv(FILES["leave"], index=False)
                    st.success("Leave request submitted.")
        with t2:
            df_l = pd.read_csv(FILES["leave"])
            user_leaves = df_l[df_l['User'] == st.session_state.username]
            st.dataframe(user_leaves, use_container_width=True)
            st.markdown("---")
            st.subheader("Cancel Leave")
            with st.form("cancel_leave_form"):
                cancel_id = st.number_input("Enter Leave ID to Cancel", min_value=1)
                if st.form_submit_button("Confirm Cancellation"):
                    if not user_leaves[user_leaves['ID'] == cancel_id].empty:
                        df_l.loc[df_l['ID'] == cancel_id, "Status"] = "Cancelled by Student"
                        df_l.to_csv(FILES["leave"], index=False)
                        st.warning("Trip cancelled.")
                        st.rerun()
                    else: st.error("Invalid ID")

# --- 6. PRINCIPAL VIEW ---
def principal_view():
    st.title(f"🎓 Principal Dashboard")
    if st.sidebar.button("Logout"): 
        for key in list(st.session_state.keys()): del st.session_state[key]
        st.rerun()
    df = pd.read_csv(FILES["hostel"])
    p_view = df[(df['Institution'] == st.session_state.inst_name) & (df['Target'].isin(["Principal", "Both"]))]
    st.dataframe(p_view, use_container_width=True)
    with st.form("p_resolve"):
        cid = st.number_input("Complaint ID", min_value=1)
        stat = st.selectbox("Action", ["Resolved ✅", "Rejected ❌"])
        ans = st.text_area("Response")
        if st.form_submit_button("Submit"):
            # REPLACED WRONG LOGIC WITH CORRECT SINGLE-COLUMN UPDATE:
            df.loc[df['ID'] == cid, "Status"] = stat
            df.loc[df['ID'] == cid, "Admin_Message"] = f"PRINCIPAL: {ans}"
            
            df.to_csv(FILES["hostel"], index=False)
            st.rerun()

# --- 7. WARDEN VIEW ---
def warden_view():
    st.title(f"👮 Warden Management")
    if st.sidebar.button("Logout"): 
        for key in list(st.session_state.keys()): del st.session_state[key]
        st.rerun()

    # --- EXPIRY ALERT SYSTEM ---
    m_df = pd.read_csv(FILES["meds"])
    today = date.today()
    warning_threshold = today + timedelta(days=30)
    
    expired_list = []
    near_expiry_list = []
    
    for _, row in m_df.iterrows():
        try:
            exp_date = pd.to_datetime(row['Expiry_Date']).date()
            if exp_date < today:
                expired_list.append(str(row['Medicine_Name']))
            elif exp_date <= warning_threshold:
                near_expiry_list.append(f"{row['Medicine_Name']} (Exp: {row['Expiry_Date']})")
        except: continue
        
    if expired_list:
        st.error(f"🚨 **EXPIRED MEDICINES:** {', '.join(expired_list)}. Please remove from stock immediately!")
    if near_expiry_list:
        st.warning(f"⚠️ **EXPIRING SOON (Within 30 Days):** {', '.join(near_expiry_list)}")

    tabs = st.tabs(["🏠 Leaves & Complaints", "🚗 Parking Management", "💊 Medicine Stock & Requests", "🎟️ Queues"])
    
    with tabs[0]:
        st.subheader("Leave Applications")
        df_l = pd.read_csv(FILES["leave"])
        st.dataframe(df_l, use_container_width=True)
        with st.form("l_up"):
            lid = st.number_input("Leave ID", min_value=1)
            ls = st.selectbox("Status", ["Approved ✅", "Denied ❌"])
            if st.form_submit_button("Update Leave"):
                df_l.loc[df_l['ID'] == lid, "Status"] = ls
                df_l.to_csv(FILES["leave"], index=False)
                st.rerun()

    with tabs[1]:
        st.subheader("Parking Requests")
        df_p = pd.read_csv(FILES["parking"])
        st.dataframe(df_p, use_container_width=True)
        with st.form("p_manage"):
            pid = st.number_input("Parking Request ID", min_value=1)
            slot_no = st.text_input("Assign Slot (e.g., A-101)")
            p_stat = st.selectbox("Decision", ["Request Accepted ✅", "Rejected ❌"])
            if st.form_submit_button("Update Parking Status"):
                # REPLACED WRONG LOGIC WITH CORRECT SINGLE-COLUMN UPDATE:
                df_p.loc[df_p['ID'] == pid, "Slot"] = slot_no
                df_p.loc[df_p['ID'] == pid, "Status"] = p_stat
                
                df_p.to_csv(FILES["parking"], index=False)
                st.success("Student updated!")
                st.rerun()

    with tabs[2]:
        low_stock_items = m_df[m_df['Stock_Count'] < 5]['Medicine_Name'].tolist()
        if low_stock_items:
            items_str = ", ".join([str(x) for x in low_stock_items])
            st.markdown(f"📦 **LOW STOCK ALERT:** The following items have less than 5 units left: `{items_str}`")
            
        st.subheader("1. Update Medicine Stock")
        with st.form("stock_update_form"):
            col1, col2 = st.columns(2)
            CAT_OPTIONS = ["General", "First Aid", "Antibiotics", "Painkillers", "Fever", "Other"]
            with col1:
                m_name = st.text_input("Medicine Name")
                m_cat = st.selectbox("Category", CAT_OPTIONS)
                m_stock = st.number_input("Stock Count", min_value=0)
            with col2:
                m_mfg = st.date_input("Manufacturing Date")
                m_exp = st.date_input("Expiry Date")
            if st.form_submit_button("Add/Update Medicine"):
                if m_name in m_df['Medicine_Name'].astype(str).values:
                    m_df.loc[m_df['Medicine_Name'].astype(str) == m_name, ['Category', 'Stock_Count', 'Manufacture_Date', 'Expiry_Date']] = [m_cat, m_stock, m_mfg, m_exp]
                else:
                    new_med = {"Medicine_Name": m_name, "Category": m_cat, "Stock_Count": m_stock, "Manufacture_Date": m_mfg, "Expiry_Date": m_exp}
                    m_df = pd.concat([m_df, pd.DataFrame([new_med])], ignore_index=True)
                m_df.to_csv(FILES["meds"], index=False)
                st.success(f"Stock updated!")
                st.rerun()
        
        st.markdown("---")
        st.subheader("2. Current Inventory & Management")
        c1, c2 = st.columns([1, 1])
        with c1:
            search_query = st.text_input("🔍 Search Medicine Name...", "").strip().lower()
        with c2:
            cat_filter = st.selectbox("📁 Filter by Category", ["All"] + CAT_OPTIONS)

        filtered_df = m_df.copy()
        if search_query:
            filtered_df = filtered_df[filtered_df['Medicine_Name'].astype(str).str.lower().str.contains(search_query, na=False)]
        if cat_filter != "All":
            filtered_df = filtered_df[filtered_df['Category'] == cat_filter]
        st.dataframe(filtered_df.style.apply(highlight_meds, axis=1), use_container_width=True)

        with st.expander("🗑️ Delete Medicine from Stock"):
            med_to_del = st.selectbox("Select Medicine to Remove Permanentally", ["-- Choose --"] + m_df['Medicine_Name'].astype(str).tolist())
            if st.button("Confirm Delete"):
                if med_to_del != "-- Choose --":
                    m_df = m_df[m_df['Medicine_Name'].astype(str) != med_to_del]
                    m_df.to_csv(FILES["meds"], index=False)
                    st.warning(f"{med_to_del} removed from database.")
                    st.rerun()
        
        st.markdown("---")
        st.subheader("3. Student Medicine Requests")
        df_mr = pd.read_csv(FILES["med_req"])
        st.dataframe(df_mr, use_container_width=True)
        
        with st.form("mr_up"):
            rid = st.number_input("Request ID", min_value=1)
            rs = st.selectbox("Status", ["Medicine Ready ✅", "Unavailable ❌"])
            if st.form_submit_button("Update Request Status"):
                if rs == "Medicine Ready ✅":
                    req_row = df_mr[df_mr['ID'] == rid]
                    if not req_row.empty:
                        med_name_requested = str(req_row.iloc[0]['Medicine_Type'])
                        if med_name_requested in m_df['Medicine_Name'].astype(str).values:
                            current_stock = m_df.loc[m_df['Medicine_Name'].astype(str) == med_name_requested, 'Stock_Count'].values[0]
                            if current_stock > 0:
                                m_df.loc[m_df['Medicine_Name'].astype(str) == med_name_requested, 'Stock_Count'] = current_stock - 1
                                m_df.to_csv(FILES["meds"], index=False)
                                st.info(f"Stock for {med_name_requested} decreased by 1.")
                            else:
                                st.error("Stock is already 0! Cannot issue.")
                
                df_mr.loc[df_mr['ID'] == rid, "Status"] = rs
                df_mr.to_csv(FILES["med_req"], index=False)
                st.rerun()

    with tabs[3]:
        df_q = pd.read_csv(FILES["queue"])
        st.dataframe(df_q, use_container_width=True)
        with st.form("q_up"):
            qid = st.number_input("Queue ID", min_value=1)
            qs = st.selectbox("Update", ["Finished", "Cancelled"])
            if st.form_submit_button("Update Queue"):
                df_q.loc[df_q['ID'] == qid, "Status"] = qs
                df_q.to_csv(FILES["queue"], index=False)
                st.rerun()

# --- 8. AUTH ---
if 'logged_in' not in st.session_state: st.session_state.logged_in = False
if not st.session_state.logged_in:
    st.markdown("<h1 style='text-align: center;'>🛡️ Secure Campus Hub</h1>", unsafe_allow_html=True)
    t1, t2 = st.tabs(["🔑 Login", "📝 Sign Up"])
    with t1:
        inst = st.text_input("Institution").strip().lower()
        user = st.text_input("Username").strip().lower()
        pw = st.text_input("Password", type="password")
        role = st.selectbox("Role", ["Student", "Warden", "Principal"])
        if st.button("Login"):
            udf = pd.read_csv(FILES["users"])
            match = udf[(udf['Institution']==inst) & (udf['Username']==user) & (udf['Password'].astype(str)==pw) & (udf['Role']==role)]
            if not match.empty:
                st.session_state.update({"logged_in": True, "username": user, "inst_name": inst, "user_role": role})
                st.rerun()
            else: st.error("Invalid Login")
    with t2:
        ni = st.text_input("New Inst").strip().lower()
        nu = st.text_input("New User").strip().lower()
        np = st.text_input("New Pwd", type="password")
        nr = st.selectbox("Register As", ["Student", "Warden", "Principal"])
        if st.button("Register"):
            udf = pd.read_csv(FILES["users"])
            pd.concat([udf, pd.DataFrame([{"Institution": ni, "Username": nu, "Password": np, "Role": nr}])], ignore_index=True).to_csv(FILES["users"], index=False)
            st.success("Account Created!")
else:
    if st.session_state.user_role == "Student": student_view()
    elif st.session_state.user_role == "Warden": warden_view()
    elif st.session_state.user_role == "Principal": principal_view()

## Features
Feature 1: Complaint Management System
Allows students to submit complaints digitally and track their status in real-time.

Feature 2: Leave Management System
Enables students to apply for leave online and receive approval or rejection from the warden.

Feature 3: Medicine Request & Stock Monitoring
Students can request medicines while wardens can manage stock and receive expiry alerts.

Feature 4: Smart Queue & Parking Management
Provides digital token generation for campus services and online parking slot requests.
---

## Implementation
The Secure Campus Hub is implemented as a web-based application using Python and the Streamlit framework.

The system uses a role-based login mechanism where users log in as a Student, Warden or Principal. Based on the selected role, the respective dashboard is displayed with different functionalities.

Students can submit complaints, apply for leave, request medicines, generate queue tokens and request parking slots through online forms. All the submitted data is stored in CSV files which act as a lightweight database.

Wardens can view and manage leave applications, parking requests and medicine stock details. They also receive alerts for low stock and expired medicines and can update the request status accordingly.

Principals can view complaints directed to them and take necessary actions by resolving or rejecting the issues.

The application dynamically reads and updates the stored data, ensuring real-time tracking and efficient management of campus services.
### For Software:
1. Install Python on the system.
2. Install required libraries such as Streamlit and Pandas using pip.
3. Write the application code using Python in any IDE like VS Code.
4. Create CSV files to store user data and request details.
5. Design the user interface using the **Streamlit** framework.
6. Run the application using the Streamlit command in the terminal.
7. Access the web application through a browser for further use.
#### Installation
pip install streamlit
pip install pandas


#### Run
streamlit run app.py


### For Hardware:

#### Components Required
[List all components needed with specifications]

#### Circuit Setup
[Explain how to set up the circuit]

---

## Project Documentation

### For Software:

#### Screenshots (Add at least 3)

![Screenshot1](Add screenshot 1 here with proper name)
*Add caption explaining what this shows*

![Screenshot2](Add screenshot 2 here with proper name)
*Add caption explaining what this shows*

![Screenshot3](Add screenshot 3 here with proper name)
*Add caption explaining what this shows*

#### Diagrams

**System Architecture:**

![Architecture Diagram](docs/architecture.png)
*Explain your system architecture - components, data flow, tech stack interaction*

**Application Workflow:**

![Workflow](docs/workflow.png)
*Add caption explaining your workflow*

---

### For Hardware:

#### Schematic & Circuit

![Circuit](Add your circuit diagram here)
*Add caption explaining connections*

![Schematic](Add your schematic diagram here)
*Add caption explaining the schematic*

#### Build Photos

![Team](Add photo of your team here)

![Components](Add photo of your components here)
*List out all components shown*

![Build](Add photos of build process here)
*Explain the build steps*

![Final](Add photo of final product here)
*Explain the final build*

---

## Additional Documentation

### For Web Projects with Backend:

#### API Documentation

**Base URL:** `https://api.yourproject.com`

##### Endpoints

**GET /api/endpoint**
- **Description:** [What it does]
- **Parameters:**
  - `param1` (string): [Description]
  - `param2` (integer): [Description]
- **Response:**
```json
{
  "status": "success",
  "data": {}
}
```

**POST /api/endpoint**
- **Description:** [What it does]
- **Request Body:**
```json
{
  "field1": "value1",
  "field2": "value2"
}
```
- **Response:**
```json
{
  "status": "success",
  "message": "Operation completed"
}
```

[Add more endpoints as needed...]

---

### For Mobile Apps:

#### App Flow Diagram

![App Flow](docs/app-flow.png)
*Explain the user flow through your application*

#### Installation Guide

**For Android (APK):**
1. Download the APK from [Release Link]
2. Enable "Install from Unknown Sources" in your device settings:
   - Go to Settings > Security
   - Enable "Unknown Sources"
3. Open the downloaded APK file
4. Follow the installation prompts
5. Open the app and enjoy!

**For iOS (IPA) - TestFlight:**
1. Download TestFlight from the App Store
2. Open this TestFlight link: [Your TestFlight Link]
3. Click "Install" or "Accept"
4. Wait for the app to install
5. Open the app from your home screen

**Building from Source:**
```bash
# For Android
flutter build apk
# or
./gradlew assembleDebug

# For iOS
flutter build ios
# or
xcodebuild -workspace App.xcworkspace -scheme App -configuration Debug
```

---

### For HardwaNot Applicable — This project is a software-based web application and does not involve any physical hardware components or embedded systems. All functionalities are implemented digitally using Python and a web framework.re Projects:


#### Bill of Materials (BOM)
Bill of Materials (BOM)
Component	Quantity	Specifications	Price	Link/Source
Laptop/Desktop	1	Minimum 4GB RAM, Intel i3 or above	₹30,000+	Personal Device
Internet Connection	1	Minimum 10 Mbps Speed	As per ISP	WiFi Provider
Storage (HDD/SSD)	1	Minimum 500GB	Included	System Default
Keyboard & Mouse	1	Standard Input Devices	Included	System Default

**Total Estimated Cost:** Depends on availability of personal computer system.

#### Assembly Instructions
Ensure the system has Python installed.

Install required software libraries like Streamlit and Pandas.

Connect to an internet connection.

Run the application using the terminal.

Open the application in a web browser to access the Secure Campus Hub portal.

**Step 1: Prepare Components**
1. Gather all components listed in the BOM
2. Check component specifications
3. Prepare your workspace
![Step 1](images/assembly-step1.jpg)
*Caption: All components laid out*

**Step 2: Build the Power Supply**
1. Connect the power rails on the breadboard
2. Connect Arduino 5V to breadboard positive rail
3. Connect Arduino GND to breadboard negative rail
![Step 2](images/assembly-step2.jpg)
*Caption: Power connections completed*

**Step 3: Add Components**
1. Place LEDs on breadboard
2. Connect resistors in series with LEDs
3. Connect LED cathodes to GND
4. Connect LED anodes to Arduino digital pins (2-6)
![Step 3](images/assembly-step3.jpg)
*Caption: LED circuit assembled*

**Step 4: [Continue for all steps...]**

**Final Assembly:**
![Final Build](images/final-build.jpg)
*Caption: Completed project ready for testing*

---

### For Scripts/CLI Tools:

#### Command Reference

**Basic Usage:**
```bash
python script.py [options] [arguments]
```

**Available Commands:**
- `command1 [args]` - Description of what command1 does
- `command2 [args]` - Description of what command2 does
- `command3 [args]` - Description of what command3 does

**Options:**
- `-h, --help` - Show help message and exit
- `-v, --verbose` - Enable verbose output
- `-o, --output FILE` - Specify output file path
- `-c, --config FILE` - Specify configuration file
- `--version` - Show version information

**Examples:**

```bash
# Example 1: Basic usage
python script.py input.txt

# Example 2: With verbose output
python script.py -v input.txt

# Example 3: Specify output file
python script.py -o output.txt input.txt

# Example 4: Using configuration
python script.py -c config.json --verbose input.txt
```

#### Demo Output

**Example 1: Basic Processing**

**Input:**
```
This is a sample input file
with multiple lines of text
for demonstration purposes
```

**Command:**
```bash
python script.py sample.txt
```

**Output:**
```
Processing: sample.txt
Lines processed: 3
Characters counted: 86
Status: Success
Output saved to: output.txt
```

**Example 2: Advanced Usage**

**Input:**
```json
{
  "name": "test",
  "value": 123
}
```

**Command:**
```bash
python script.py -v --format json data.json
```

**Output:**
```
[VERBOSE] Loading configuration...
[VERBOSE] Parsing JSON input...
[VERBOSE] Processing data...
{
  "status": "success",
  "processed": true,
  "result": {
    "name": "test",
    "value": 123,
    "timestamp": "2024-02-07T10:30:00"
  }
}
[VERBOSE] Operation completed in 0.23s
```

---

## Project Demo

### Video
https://1drv.ms/v/c/1210a04471f139b1/IQDBVJ9qnn9pQoVt1Nhg-2HEAa1qbRtVSvQqRB2bwxTRDb8?e=tkPDfE


The video demonstrates the working of the Secure Campus Hub web application by showing the complete user flow of the system. It starts with the login process where users log in as Student, Warden or Principal. After login, the role-based dashboard is displayed.

The video shows how a student can submit complaints, apply for leave, request medicines, generate queue tokens and request parking slots. It also demonstrates how these requests are stored and tracked in the system.

Further, the video highlights the warden dashboard where leave requests, parking approvals and medicine stock are managed along with expiry alerts. Finally, it shows the principal dashboard where complaints can be reviewed and resolved.

Overall, the video explains the key features, working process and technical functionality of the Secure Campus Hub system.

### Additional Demos
[Add any extra demo materials/links - Live site, APK download, online demo, etc.]

---

## AI Tools Used (Optional - For Transparency Bonus)

If you used AI tools during development, document them here for transparency:

**Tool Used:** **ChatGPT**
**Purpose:** Used for code assistance, debugging support, UI design suggestions and documentation preparation during the development of the Secure Campus Hub project.


Purpose:

Code generation for backend logic

Debugging assistance for application errors

UI design suggestions for Streamlit interface

Documentation and report preparation

Feature implementation support

Key Prompts Used:

"Create a role-based login system using Python and Streamlit"

"Build a complaint management system using CSV database"

"Add medicine expiry alert system in Streamlit"

"Implement leave request approval by warden"

"Generate queue token system for students"

Percentage of AI-generated code: Approximately 30–40%

Human Contributions:

System architecture design and planning

Implementation of custom campus modules

Integration of complaint, leave, medicine and parking features

Testing and validation of system

UI/UX customization and styling decisions

*Note: Proper documentation of AI usage demonstrates transparency and earns bonus points in evaluation!*

---

## Team Contributions
no team

individual
---

## License

This project is licensed under the [LICENSE_NAME] License - see the [LICENSE](LICENSE) file for details.

###License Used

MIT License (Permissive, widely used)

This project is released under the MIT License, which allows users to freely use, modify and distribute the software with proper attribution to the original developer.

---

Made with ❤️ at TinkerHub
