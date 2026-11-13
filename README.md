<!DOCTYPE html>
<html>
<head>
    <title>Student Management System</title>

    <meta name="viewport"
          content="width=device-width, initial-scale=1.0">

    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f1f5f9;
            margin: 0;
            padding: 15px;
        }

        h1 {
            text-align: center;
            color: #1e40af;
        }

        .container {
            max-width: 700px;
            margin: auto;
        }

        .form-box, .table-box {
            background: white;
            padding: 18px;
            margin-top: 20px;
            border-radius: 12px;
            box-shadow: 0 2px 8px #0001;
        }

        input {
            width: 100%;
            padding: 12px;
            margin: 8px 0;
            box-sizing: border-box;
            border: 1px solid #ccc;
            border-radius: 6px;
        }

        button {
            padding: 10px 14px;
            border: none;
            border-radius: 6px;
            color: white;
            cursor: pointer;
            margin: 4px 2px;
        }

        .save {
            background: #2563eb;
            width: 100%;
        }

        .edit {
            background: #d97706;
        }

        .delete {
            background: #dc2626;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 12px;
            font-size: 13px;
        }

        th, td {
            padding: 9px 5px;
            border: 1px solid #ddd;
            text-align: left;
            overflow-wrap: anywhere;
        }

        th {
            background: #dbeafe;
        }

        .message {
            text-align: center;
            color: green;
        }
    </style>
</head>

<body>

<div class="container">

    <h1>Student Management System</h1>
    <p class="message" id="message"></p>

    <div class="form-box">

        <h2>Add Student</h2>

        <form id="studentForm">

            <input type="text"
                   id="name"
                   placeholder="Enter Student Name"
                   required>

            <input type="email"
                   id="email"
                   placeholder="Enter Email"
                   required>

            <input type="text"
                   id="course"
                   placeholder="Enter Course"
                   required>

            <input type="tel"
                   id="phone"
                   placeholder="Enter Phone Number"
                   required>

            <button class="save" type="submit">
                Add Student
            </button>

        </form>
    </div>

    <div class="table-box">

        <h2>Student Records</h2>

        <input type="text"
               id="search"
               placeholder="Search student..."
               oninput="displayStudents()">

        <div style="overflow-x:auto">

            <table>
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Name</th>
                        <th>Email</th>
                        <th>Course</th>
                        <th>Phone</th>
                        <th>Actions</th>
                    </tr>
                </thead>

                <tbody id="studentList">
                </tbody>
            </table>

        </div>

        <p id="empty"></p>

    </div>

</div>

<script>

    let students = JSON.parse(
        localStorage.getItem("students")
    ) || [];

    let editId = null;

    const form = document.getElementById("studentForm");

    // SAVE STUDENT - CREATE

    form.addEventListener("submit", function(event) {

        event.preventDefault();

        const student = {
            id: editId || Date.now(),
            name: document.getElementById("name").value.trim(),
            email: document.getElementById("email").value.trim(),
            course: document.getElementById("course").value.trim(),
            phone: document.getElementById("phone").value.trim()
        };

        if (editId !== null) {

            students = students.map(s =>
                s.id === editId ? student : s
            );

            editId = null;
            showMessage("Student updated successfully!");

        } else {

            students.push(student);
            showMessage("Student added successfully!");

        }

        saveData();
        form.reset();
        document.querySelector(".save").textContent =
            "Add Student";

        displayStudents();

    });

    // READ STUDENTS

    function displayStudents() {

        const list = document.getElementById("studentList");
        const search = document.getElementById("search")
                            .value.toLowerCase();

        list.innerHTML = "";

        const filtered = students.filter(s =>
            s.name.toLowerCase().includes(search) ||
            s.email.toLowerCase().includes(search)
        );

        filtered.forEach(s => {

            const row = document.createElement("tr");

            const values = [
                s.id,
                s.name,
                s.email,
                s.course,
                s.phone
            ];

            values.forEach(value => {
                const cell = document.createElement("td");
                cell.textContent = value;
                row.appendChild(cell);
            });

            const actionCell = document.createElement("td");

            const editButton = document.createElement("button");
            editButton.textContent = "Edit";
            editButton.className = "edit";
            editButton.onclick = () => editStudent(s.id);

            const deleteButton = document.createElement("button");
            deleteButton.textContent = "Delete";
            deleteButton.className = "delete";
            deleteButton.onclick = () => deleteStudent(s.id);

            actionCell.append(editButton, deleteButton);
            row.appendChild(actionCell);

            list.appendChild(row);

        });

        document.getElementById("empty").textContent =
            filtered.length === 0
            ? "No student records found."
            : "Total Students: " + students.length;

    }

    // UPDATE STUDENT

    function editStudent(id) {

        const student = students.find(s => s.id === id);

        if (!student) return;

        document.getElementById("name").value = student.name;
        document.getElementById("email").value = student.email;
        document.getElementById("course").value = student.course;
        document.getElementById("phone").value = student.phone;

        editId = id;

        document.querySelector(".save").textContent =
            "Update Student";

        window.scrollTo(0, 0);

    }

    // DELETE STUDENT

    function deleteStudent(id) {

        if (!confirm("Are you sure you want to delete this student?")) {
            return;
        }

        students = students.filter(s => s.id !== id);

        saveData();
        displayStudents();

        showMessage("Student deleted successfully!");

    }

    // STORE DATA

    function saveData() {

        localStorage.setItem(
            "students",
            JSON.stringify(students)
        );

    }

    function showMessage(text) {

        document.getElementById("message").textContent = text;

    }

    // DISPLAY INITIAL RECORDS

    displayStudents();

</script>

</body>
</html>
