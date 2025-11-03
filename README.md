# ======================================================
# CAMPUS COURSE REGISTRATION SYSTEM – PHASE 1
# Name: Ashwin Raj
# Enrollment No.: 2402130002
# Course: BSc in Business Analytics
# ======================================================

# ---------- DATA STORAGE (In-Memory) ----------
students = {}
courses = {}
student_ids = set()

# ---------- SAMPLE DATA ----------
def preload_data():
    # --- Sample Students ---
    students["S001"] = {"name": "Riya", "courses": ["C101"]}
    students["S002"] = {"name": "Aman", "courses": []}
    students["S003"] = {"name": "Kiran", "courses": []}
    student_ids.update(["S001", "S002", "S003"])

    # --- Sample Courses ---
    courses["C101"] = {
        "name": "Problem Solving and Programming",
        "capacity": 2,
        "prerequisites": (),
        "registered": ["S001"],
        "waitlist": []
    }
    courses["C102"] = {
        "name": "Advanced Python Programming",
        "capacity": 2,
        "prerequisites": ("C101",),
        "registered": [],
        "waitlist": []
    }
    courses["C103"] = {
        "name": "Mathematics",
        "capacity": 3,
        "prerequisites": (),
        "registered": [],
        "waitlist": []
    }

# ---------- LOGIN ----------
def login():
    print("===== Campus Course Registration System =====")
    password = input("Enter password: ")
    if password == "admin123":
        print("Access granted!\n")
        preload_data()
        main_menu()
    else:
        print("Access denied. Program will exit.")

# ---------- MAIN MENU ----------
def main_menu():
    while True:
        print("\n==== MAIN MENU ====")
        print("1. Student Management")
        print("2. Course Management")
        print("3. Registration Management")
        print("4. Reports & Searches")
        print("5. Exit")

        choice = input("Enter your choice: ")

        if choice == "1":
            student_menu()
        elif choice == "2":
            course_menu()
        elif choice == "3":
            registration_menu()
        elif choice == "4":
            reports_menu()
        elif choice == "5":
            print("Thank you for using the system!")
            break
        else:
            print("Invalid choice. Please enter 1–5.")

# ---------- STUDENT MANAGEMENT ----------
def student_menu():
    print("\n--- Student Management ---")
    print("1. Add Student")
    print("2. Modify Student")
    print("3. Delete Student")
    print("4. View All Students")
    choice = input("Enter your choice: ")

    if choice == "1":
        add_student()
    elif choice == "2":
        modify_student()
    elif choice == "3":
        delete_student()
    elif choice == "4":
        view_students()
    else:
        print("Invalid option.")

def add_student():
    sid = input("Enter Student ID: ").upper()
    if sid in student_ids:
        print("Student ID already exists.")
    else:
        name = input("Enter Student Name: ").title()
        students[sid] = {"name": name, "courses": []}
        student_ids.add(sid)
        print("Student added successfully.")

def modify_student():
    sid = input("Enter Student ID to modify: ").upper()
    if sid in students:
        new_name = input("Enter new name: ").title()
        students[sid]["name"] = new_name
        print("Student name updated.")
    else:
        print("Student not found.")

def delete_student():
    sid = input("Enter Student ID to delete: ").upper()
    if sid in students:
        del students[sid]
        student_ids.remove(sid)
        print("Student deleted successfully.")
    else:
        print("Student not found.")

def view_students():
    print("\n--- List of Students ---")
    for sid, data in students.items():
        print(sid, "-", data["name"], "→", data["courses"])

# ---------- COURSE MANAGEMENT ----------
def course_menu():
    print("\n--- Course Management ---")
    print("1. Add Course")
    print("2. Modify Course")
    print("3. Delete Course")
    print("4. View All Courses")
    choice = input("Enter your choice: ")

    if choice == "1":
        add_course()
    elif choice == "2":
        modify_course()
    elif choice == "3":
        delete_course()
    elif choice == "4":
        view_courses()
    else:
        print("Invalid option.")

def add_course():
    code = input("Enter Course Code: ").upper()
    if code in courses:
        print("Course already exists.")
    else:
        name = input("Enter Course Name: ").title()
        capacity = int(input("Enter Course Capacity: "))
        prereq = input("Enter prerequisites (comma separated or 'none'): ")
        if prereq.lower() == "none":
            prereq_tuple = ()
        else:
            prereq_tuple = tuple(prereq.split(","))
        courses[code] = {
            "name": name,
            "capacity": capacity,
            "prerequisites": prereq_tuple,
            "registered": [],
            "waitlist": []
        }
        print("Course added successfully.")

def modify_course():
    code = input("Enter Course Code to modify: ").upper()
    if code in courses:
        new_capacity = int(input("Enter new capacity: "))
        courses[code]["capacity"] = new_capacity
        print("Course capacity updated.")
    else:
        print("Course not found.")

def delete_course():
    code = input("Enter Course Code to delete: ").upper()
    if code in courses:
        del courses[code]
        print("Course deleted.")
    else:
        print("Course not found.")

def view_courses():
    print("\n--- List of Courses ---")
    for code, c in courses.items():
        print(code, "-", c["name"], "| Capacity:", c["capacity"], "| Prereq:", c["prerequisites"])

# ---------- REGISTRATION MANAGEMENT ----------
def registration_menu():
    print("\n--- Registration Management ---")
    print("1. Register Student for Course")
    print("2. Drop Course")
    choice = input("Enter your choice: ")

    if choice == "1":
        register_student()
    elif choice == "2":
        drop_course()
    else:
        print("Invalid option.")

def register_student():
    sid = input("Enter Student ID: ").upper()
    code = input("Enter Course Code: ").upper()

    if sid not in students or code not in courses:
        print("Invalid student or course.")
    else:
        course = courses[code]
        prereq_met = True
        for prereq in course["prerequisites"]:
            if prereq not in students[sid]["courses"]:
                prereq_met = False
        if prereq_met == False:
            print("Student has not completed prerequisites.")
        else:
            if len(course["registered"]) < course["capacity"]:
                course["registered"].append(sid)
                students[sid]["courses"].append(code)
                print("Student registered successfully.")
            else:
                course["waitlist"].append(sid)
                print("Course is full. Student added to waitlist.")

def drop_course():
    sid = input("Enter Student ID: ").upper()
    code = input("Enter Course Code: ").upper()

    if sid not in students or code not in courses:
        print("Invalid student or course.")
    else:
        course = courses[code]
        if sid in course["registered"]:
            course["registered"].remove(sid)
            students[sid]["courses"].remove(code)
            print("Student dropped from course.")
            if len(course["waitlist"]) > 0:
                next_sid = course["waitlist"].pop(0)
                course["registered"].append(next_sid)
                students[next_sid]["courses"].append(code)
                print("Next student from waitlist promoted.")
        else:
            print("Student not registered for this course.")

# ---------- REPORTS & SEARCHES ----------
def reports_menu():
    print("\n--- Reports and Searches ---")
    print("1. Report by Course")
    print("2. Report by Student")
    print("3. Search Course")
    print("4. Calculate Available Seats")
    choice = input("Enter your choice: ")

    if choice == "1":
        report_by_course()
    elif choice == "2":
        report_by_student()
    elif choice == "3":
        search_course()
    elif choice == "4":
        calculate_available_seats()
    else:
        print("Invalid option.")

def report_by_course():
    code = input("Enter Course Code: ").upper()
    if code in courses:
        c = courses[code]
        print("\nCourse:", c["name"])
        print("Registered:", c["registered"])
        print("Waitlist:", c["waitlist"])
    else:
        print("Course not found.")

def report_by_student():
    sid = input("Enter Student ID: ").upper()
    if sid in students:
        s = students[sid]
        print("\nStudent:", s["name"])
        print("Registered Courses:", s["courses"])
    else:
        print("Student not found.")

def search_course():
    keyword = input("Enter Course Name or Code: ").lower()
    found = False
    for code, c in courses.items():
        if keyword in c["name"].lower() or keyword == code.lower():
            print(code, "-", c["name"])
            found = True
    if not found:
        print("No matching course found.")

def calculate_available_seats():
    code = input("Enter Course Code: ").upper()
    if code in courses:
        c = courses[code]
        available = c["capacity"] - len(c["registered"])
        print("Available Seats:", available)
    else:
        print("Course not found.")

# ---------- START PROGRAM ----------
if __name__ == "__main__":
    login()
