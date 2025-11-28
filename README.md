<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Student Birthday Reminder</title>
  <style>
    body {
      margin: 0;
      font-family: Arial, sans-serif;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      background: linear-gradient(135deg, #1e3c72, #2a5298);
      color: #333;
    }

    .card {
      background: #ffffff;
      padding: 20px 30px;
      border-radius: 12px;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.25);
      max-width: 650px;
      width: 90%;
    }

    h1 {
      text-align: center;
      margin-top: 0;
      color: #1e3c72;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
      font-size: 14px;
    }

    th, td {
      border: 1px solid #ddd;
      padding: 8px;
      text-align: left;
    }

    th {
      background-color: #f2f2f2;
    }

    .alert {
      margin-top: 15px;
      padding: 10px 12px;
      border-radius: 6px;
      border-left: 6px solid #ff0000;
      background-color: #ffe5e5;
      color: #b30000;
      font-weight: bold;
    }

    .info {
      margin-top: 10px;
      padding: 8px 10px;
      border-radius: 6px;
      border-left: 4px solid #2a5298;
      background-color: #e7f0ff;
      font-size: 14px;
    }

    .highlight-row {
      background-color: #fff9c4;
    }
  </style>
</head>
<body>
<div class="card">
  <h1>Student Birthday Reminder</h1>

  <!-- Table of students -->
  <table id="studentsTable">
    <thead>
      <tr>
        <th>Student Name</th>
        <th>Date of Birth (DD-MM-YYYY)</th>
      </tr>
    </thead>
    <tbody>
      <!-- Filled by JavaScript -->
    </tbody>
  </table>

  <!-- Red alert for today birthdays -->
  <div id="todayBirthdays" class="alert" style="display:none;"></div>

  <!-- Path line of next student birthday -->
  <div id="nextBirthday" class="info"></div>
</div>

<script>
  // List of students with DOB (you can add/remove students here)
  const students = [
    { name: "Mukesh", dob: "2005-07-15" },
    { name: "Ravi",   dob: "2004-11-28" },
    { name: "Anitha", dob: "2005-01-10" },
    { name: "Rahul",  dob: "2004-03-02" },
    { name: "Priya",  dob: "2005-12-05" }
  ];

  function formatDate(dateObj) {
    const d = String(dateObj.getDate()).padStart(2, "0");
    const m = String(dateObj.getMonth() + 1).padStart(2, "0");
    const y = dateObj.getFullYear();
    return `${d}-${m}-${y}`;
  }

  function daysBetween(d1, d2) {
    const oneDayMs = 24 * 60 * 60 * 1000;
    // Remove time part
    const a = new Date(d1.getFullYear(), d1.getMonth(), d1.getDate());
    const b = new Date(d2.getFullYear(), d2.getMonth(), d2.getDate());
    return Math.round((b - a) / oneDayMs);
  }

  function init() {
    const tbody = document.querySelector("#studentsTable tbody");
    tbody.innerHTML = "";
    students.forEach((s) => {
      const d = new Date(s.dob);
      const tr = document.createElement("tr");
      tr.setAttribute("data-name", s.name);
      tr.setAttribute("data-month", d.getMonth());
      tr.setAttribute("data-day", d.getDate());

      tr.innerHTML = `
        <td>${s.name}</td>
        <td>${String(d.getDate()).padStart(2, "0")}-${String(d.getMonth() + 1).padStart(2, "0")}-${d.getFullYear()}</td>
      `;
      tbody.appendChild(tr);
    });

    checkBirthdays();
  }

  function checkBirthdays() {
    const today = new Date();
    const todayDay = today.getDate();
    const todayMonth = today.getMonth();

    const rows = document.querySelectorAll("#studentsTable tbody tr");
    const todayNames = [];
    const candidates = [];

    rows.forEach((row, index) => {
      const name = row.getAttribute("data-name");
      const month = parseInt(row.getAttribute("data-month"), 10);
      const day = parseInt(row.getAttribute("data-day"), 10);

      // Today birthday?
      if (day === todayDay && month === todayMonth) {
        todayNames.push(name);
      }

      // build next birthday date for this student
      let nextDate = new Date(today.getFullYear(), month, day);
      if (nextDate < today) {
        nextDate.setFullYear(today.getFullYear() + 1);
      }
      candidates.push({ name, rowIndex: index, date: nextDate });
    });

    // Show red alert for today's birthdays
    const alertDiv = document.getElementById("todayBirthdays");
    if (todayNames.length > 0) {
      alertDiv.style.display = "block";
      alertDiv.textContent =
        "🎉 RED ALERT: Today is birthday of " + todayNames.join(", ") + "!";
    } else {
      alertDiv.style.display = "none";
    }

    // Compute next upcoming birthday
    let next = candidates[0];
    candidates.forEach(c => {
      if (c.date < next.date) {
        next = c;
      }
    });

    const daysLeft = daysBetween(today, next.date);
    const nextDiv = document.getElementById("nextBirthday");
    nextDiv.textContent =
      `Next student birthday: ${next.name} on ${formatDate(next.date)} ` +
      `(only ${daysLeft} day(s) left).`;

    // Highlight that student's row
    rows.forEach(r => r.classList.remove("highlight-row"));
    rows[next.rowIndex].classList.add("highlight-row");
  }

  window.onload = init;
</script>
</body>
</html>
