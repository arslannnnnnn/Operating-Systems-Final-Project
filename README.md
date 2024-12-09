# Operating-Systems-Final-Project
This is the final project of the Operating Systems class, and here I am simply creating and running a mini-web application, using docker, as it's built within in a container, that runs python and flask. It just shows the timetable based on the level.

Read the code, as there are steps with follow along.

*1. Start PostgreSQL in Docker**

```bash
docker pull postgres:latest (Downloaded postgres)

docker run --name webster-db -e POSTGRES_USER=Arslan -e POSTGRES_PASSWORD=Arslan2006@ -d -p 5432:5432 postgres:latest (created the container)

docker ps (checked if it was working)

docker exec -it webster-db psql -U Arslan (ran the psql database)
```

### 2. **Set Up the PostgreSQL Database and Tables**

```sql (created the database, by pasting in information that is also personalized to my own classes)
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




```

--Intermediate step, created a file called webster_timetable, created two subfiles, venv and templates, copied Step 4 Python Code into "app.py" and put it in venv, then I got the index.html and timetable.html code from Step 5, and created the files and put them insdie templates folder.

### 3. **Install Python and Flask Dependencies**

```bash
# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows use venv\Scripts\activate

# Install Flask and pg8000 for database connection
pip install flask pg8000
```

### 4. **Create Flask Application**

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

### 5. **HTML Templates for Flask**

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

--changed directory of where venv was running to venv folder inside webster_timetable--

### 6. **Running the Flask Application**

```bash
# Make sure you're in the directory with your Flask app
# Run the Flask app
python app.py
```
-ran the code-

### 7. **Accessing the Application** (went to this address to check if it was working)

- Navigate to `http://127.0.0.1:5000/` in your web browser to see the application in action.

(finished by taking screenshots of what I had, deactivated the venv, and stopped docker). 

### 8. **Stop the Docker Container**

```bash
# Stop the PostgreSQL Docker container after you're done
docker stop webster-db
```

