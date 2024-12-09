# Operating-Systems-Final-Project
This is the final project of the Operating Systems class, and here I am simply creating and running a mini-web application, using docker, as it's built within in a container, that runs python and flask. It just shows the timetable based on the level.

Steps taken:
*1. Installed Docker and Started PostgreSQL in Docker**

```bash
docker pull postgres:latest

docker run --name webster-db -e POSTGRES_USER=Arslan -e POSTGRES_PASSWORD=Arslan2006@ -d -p 5432:5432 postgres:latest (MODIFIED/PERSONALIZED THIS)

docker ps

docker exec -it webster-db psql -U Arslan (open psql to write data inside)
```

### 2. **Set Up and Personalize the PostgreSQL Database and Tables**

```sql
psql -U arslan (launched psql and wrote the following personalized data inside)
CREATE TABLE Timetable (
    course_id SERIAL PRIMARY KEY,
    course_name VARCHAR(255),
    day VARCHAR(50),
    time VARCHAR(50),
    room VARCHAR(50),
    level INT
);


INSERT INTO Timetable (course_name, day, time, room, level) VALUES
('Computer Programming II', 'Friday', '9:00 AM', 'Room 114', 2),
('Computer Languages', 'Thursday', '9:00 AM', 'Room 115', 1),
('Operating Systems', 'Monday', '4:30 PM', 'Room 304', 2),
('Introduction to Sustainability', 'Friday', '4:30 PM', 'Room 408', 1),
('Art Appreciation', 'Friday', '11:30 AM', 'Room 306', 2),
('Discrete Mathematics', 'Thursday', '2:00 PM', 'Room 403', 1);


CREATE TABLE Students (
    student_id SERIAL PRIMARY KEY,
    name VARCHAR(255),
    level INT
);

-- Insert Sample Students Data
INSERT INTO Students (name, level) VALUES
('Arslan', 1),

```
STEP IN BETWEEN: ---Before proceeding what I did was I created a directory for the app.py, which had the python code for the flask, and in the same folder created the html responsible for the website layout---

### 3. **Installed Python and Flask Dependencies** 

```bash
# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows use venv\Scripts\activate

# Install Flask and pg8000 for database connection
pip install flask pg8000
```

### 4. **Create Flask Application** (Pasted this into app.py)

```python
from flask import Flask, render_template, request
import pg8000

app = Flask(__name__)

# Home route to get user input for level
@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        level = request.form["level"]
        return render_template("timetable.html", level=level)
    return render_template("index.html")

# Timetable route to fetch timetable data based on the level
@app.route("/timetable", methods=["GET"])
def timetable():
    level = request.args.get("level")
    
    # Connect to PostgreSQL using pg8000
    conn = pg8000.connect(user="Arslan", password="Arslan2006@", host="localhost", port=5432, database="postgres")
    cur = conn.cursor()
    
    # Fetch timetable for the selected level
    query = f"SELECT * FROM Timetable WHERE level = {level};"
    cur.execute(query)
    rows = cur.fetchall()
    
    message = "No data found for this level." if not rows else None
    return render_template("timetable.html", data=rows, message=message)

# Run Flask app
if __name__ == "__main__":
    app.run(debug=True)
```

### 5. **HTML Templates for Flask** (Uploaded it to the same folder as app.py, so its in the same directory)

#### **`index.html` (Form for Level Input)**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>University Timetable</title>
</head>
<body>
    <h1>Welcome, Arslan!</h1>
    <h2>Webster Timetable</h2>
    <form method="POST">
        <label for="level">Enter Level:</label>
        <input type="number" id="level" name="level" required>
        <button type="submit">Submit</button>
    </form>
</body>
</html>
```
#### **`timetable.html` (Displaying Timetable)**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Timetable</title>
</head>
<body>
    <h2>Timetable for Level {{ level }}</h2>
    {% if data %}
    <table border="1">
        <thead>
            <tr>
                <th>Course ID</th>
                <th>Course Name</th>
                <th>Day</th>
                <th>Time</th> webs
                <th>Room</th>
            </tr>
        </thead>
        <tbody>
            {% for row in data %}
            <tr>
                <td>{{ row[0] }}</td>
                <td>{{ row[1] }}</td>
                <td>{{ row[2] }}</td>
                <td>{{ row[3] }}</td>
                <td>{{ row[4] }}</td>
            </tr>
            {% endfor %}
        </tbody>
    </table>
    {% else %}
    <p>{{ message }}</p>
    {% endif %}
    <br>
    <a href="/">Go Back</a>
</body>
</html>
```

### 6. **Running the Flask Application** (ran this project inside the venv with the correct directory)

```bash
# Make sure you're in the directory with your Flask app
# Run the Flask app
python app.py
```

### 7. **Accessing the Application** (pasted it inside Chrome and checked to see its functionability)

- Navigate to `http://127.0.0.1:5000/` in your web browser to see the application in action.

### 8. **Stop the Docker Container**

```bash
# Stop the PostgreSQL Docker container after you're done
docker stop webster-db
```

