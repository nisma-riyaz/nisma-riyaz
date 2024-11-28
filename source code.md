import streamlit as st import hashlib import mysql.connector 
 
# MySQL database connection db = mysql.connector.connect(     host="localhost",     user="root",  # your MySQL username     password="Ishwarya12@",  # your MySQL password     database="hospital_db" 
) 
cursor = db.cursor() 
 
# Function to hash passwords def hash_password(password):     return hashlib.sha256(password.encode()).hexdigest() 
 
# Registration Function def register_user(full_name, dob, username, password):     hashed_password = hash_password(password)     with open("users.txt", "a") as f: 
        f.write(f"{full_name},{dob},{username},{hashed_password}\n")     st.success("Registration successful!") 
 
# Login Function def login_user(username, password): 
    hashed_password = hash_password(password)     with open("users.txt", "r") as f: 
        users = f.readlines()         for user in users: 
            stored_name, stored_dob, stored_username, stored_password = user.strip().split(",")             if username == stored_username and hashed_password == stored_password: 
                st.success(f"Welcome back, {stored_name}!") 
                return True     st.error("Login failed! Check your credentials.")     return False 
 
# Function to show available doctors def show_doctors():     cursor.execute("SELECT id, name, department FROM doctors")     doctors = cursor.fetchall()     if doctors: 
        st.write("Available Doctors:")         for doc in doctors: 
            st.write(f"ID: {doc[0]} | Dr. {doc[1]} - {doc[2]}") 
    else: 
        st.write("No doctors available.") 
 
# Function to schedule an appointment def schedule_appointment(patient_name, doctor_id, appointment_date): 
    try: 
        cursor.execute("INSERT INTO appointments (patient_name, doctor_id, appointment_date) VALUES (%s, %s, %s)",  
                       (patient_name, doctor_id, appointment_date))         db.commit() 
        st.success(f"Appointment successfully scheduled with Doctor ID {doctor_id} on 
{appointment_date}.")     except Exception as e: 
        st.error(f"Error: {str(e)}") 
 
# Streamlit interface def main():     st.title("Hospital Appointment Management System (HAMS) Chatbot")     page_element=""" 
    <style> 
    [data-testid="stAppViewContainer"]{ 
       background-image: 	url("https://th.bing.com/th/id/OIP.nAGy6kph0x_9XpRBcQPyAHaEe?rs=1&pid=ImgDetMain"); 
 
       background-size: cover; 
    } 
    </style> 
    """ 
 
    st.markdown(page_element, unsafe_allow_html=True) 
 
    menu = ["Home", "Register", "Login", "Schedule Appointment", "View Doctors"]     choice = st.sidebar.selectbox("Menu", menu) 
 
    if choice == "Home": 
        st.subheader("Welcome to the Hospital Appointment Management System!") 
 
    elif choice == "Register": 
        st.subheader("User Registration")         full_name = st.text_input("Full Name")         dob = st.text_input("Date of Birth (YYYY-MM-DD)")         username = st.text_input("Username")         password = st.text_input("Password", type="password")         if st.button("Register"): 
            register_user(full_name, dob, username, password) 
 
    elif choice == "Login": 
        st.subheader("User Login")         username = st.text_input("Username")         password = st.text_input("Password", type="password")         if st.button("Login"):             if login_user(username, password):                 st.success(f"Welcome {username}!") 
 
    elif choice == "Schedule Appointment":         st.subheader("Schedule an Appointment")         patient_name = st.text_input("Patient Name")         doctor_id = st.text_input("Doctor ID")         appointment_date = st.date_input("Appointment Date")         if st.button("Schedule"): 
            schedule_appointment(patient_name, doctor_id, appointment_date) 
 
    elif choice == "View Doctors": 
        st.subheader("Available Doctors")         show_doctors() 
 
if __name__ == "__main__": 
    main() 
 
SQL CODE 
CREATE DATABASE hospital_db; 
USE hospital_db; 
 
CREATE TABLE doctors (     id INT AUTO_INCREMENT PRIMARY KEY,     name VARCHAR(100),     department VARCHAR(50) 
); 
 
CREATE TABLE appointments (     id INT AUTO_INCREMENT PRIMARY KEY,     patient_name VARCHAR(100),     doctor_id INT,     appointment_date DATE, 
    FOREIGN KEY (doctor_id) REFERENCES doctors(id) 
); 
 
INSERT INTO doctors (name, department) VALUES  
('Dr. Smith', 'Cardiology'), 
('Dr. Johnson', 'Cardiology'), 
('Dr. Patel', 'Pediatrics'), 
('Dr. Gupta', 'Pediatrics'), 
('Dr. Wilson', 'Orthopedics'), 
('Dr. Lee', 'Orthopedics'); 
 
INSERT INTO appointments (patient_name, doctor_id, appointment_date) VALUES 
('Arun Vijay', 1, '2024-09-20'),  -- Dr. Smith in Cardiology 
('Ravi Kumar', 3, '2024-09-21'),  -- Dr. Patel in Pediatrics 
('Shalini Mehra', 5, '2024-09-22'),  -- Dr. Wilson in Orthopedics 
('Priya Reddy', 2, '2024-09-25'),  -- Dr. Johnson in Cardiology 
('Mohan Das', 4, '2024-09-24'),  -- Dr. Gupta in Pediatrics 
('Anjali Sharma', 6, '2024-09-23');  -- Dr. Lee in Orthopedics 
 
select * from doctors 



