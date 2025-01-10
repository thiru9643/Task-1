Attendance-System/
│
├── app.py              # Main Flask application
├── requirements.txt    # Dependencies
├── firebase_config.json  # Firebase Admin SDK JSON file
└── templates/
    └── index.html       # Frontend for marking attendance

    from flask import Flask, request, render_template
import firebase_admin
from firebase_admin import credentials, firestore
import datetime

# Initialize Flask App
app = Flask(__name__)

# Initialize Firebase Admin SDK
cred = credentials.Certificate("firebase_config.json")
firebase_admin.initialize_app(cred)
db = firestore.client()

# Home Route
@app.route('/')
def index():
    return render_template('index.html')

# Mark Attendance Route
@app.route('/mark_attendance', methods=['POST'])
def mark_attendance():
    name = request.form['name']
    student_id = request.form['student_id']

    if name and student_id:
        attendance_ref = db.collection('attendance').document(student_id)
        attendance_ref.set({
            'name': name,
            'student_id': student_id,
            'timestamp': datetime.datetime.now()
        })
        return "Attendance marked successfully!"
    else:
        return "Please enter all details!"

# View Attendance Route
@app.route('/view_attendance')
def view_attendance():
    attendance_ref = db.collection('attendance').stream()
    attendance_list = []
    for record in attendance_ref:
        attendance_list.append(record.to_dict())
    return {'attendance_records': attendance_list}

if __name__ == '__main__':
    app.run(debug=True)
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cloud Att
       endance System</title>
</head>
<body>
    <h1>Mark Attendance</h1>
    <form action="/mark_attendance" method="POST">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name" required><br><br>

        <label for="student_id">Student ID:</label><br>
        <input type="text" id="student_id" name="student_id" required><br><br>

        <input type="submit" value="Mark Attendance">
    </form>
</body>
</html>
Flask==2.3.2
firebase-admin==6.1.0
pip install -r requirements.txt
python app.py

