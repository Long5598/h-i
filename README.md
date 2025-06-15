<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Lịch Galaxy</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: radial-gradient(circle at top left, #0f0c29, #302b63, #24243e);
      color: #fff;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      padding: 2rem;
    }

    h1 {
      font-size: 2rem;
      margin-bottom: 1rem;
    }

    #calendar {
      background: rgba(255, 255, 255, 0.08);
      border-radius: 1rem;
      padding: 2rem;
      box-shadow: 0 0 30px rgba(0, 255, 255, 0.2);
      max-width: 400px;
      width: 100%;
    }

    label, textarea, button, input {
      width: 100%;
      margin-top: 0.5rem;
      font-size: 1rem;
    }

    input, textarea {
      padding: 0.5rem;
      border-radius: 0.5rem;
      border: none;
      background: #ffffff22;
      color: white;
      resize: vertical;
    }

    button {
      margin-top: 1rem;
      background: #6a11cb;
      background: linear-gradient(to right, #6a11cb, #2575fc);
      color: white;
      border: none;
      padding: 0.75rem;
      border-radius: 0.5rem;
      cursor: pointer;
      font-weight: bold;
      transition: background 0.3s;
    }

    button:hover {
      background: linear-gradient(to right, #2575fc, #6a11cb);
    }

    #saveStatus {
      margin-top: 1rem;
      font-weight: bold;
      color: #0f0;
      display: none;
    }

    h2 {
      margin-top: 2rem;
      color: #0ff;
      text-shadow: 0 0 10px #0ff;
    }

    table.calendar-table {
      margin-top: 1rem;
      border-collapse: collapse;
      width: 100%;
      color: #fff;
      max-width: 420px;
      box-shadow: 0 0 15px rgba(0, 255, 255, 0.2);
      background: rgba(255,255,255,0.05);
      border-radius: 10px;
      overflow: hidden;
    }

    table.calendar-table th,
    table.calendar-table td {
      border: 1px solid rgba(255,255,255,0.2);
      padding: 10px;
      text-align: center;
      cursor: pointer;
      transition: background 0.3s;
    }

    table.calendar-table th {
      background-color: rgba(255,255,255,0.1);
    }

    td.marked {
      background-color: rgba(0, 255, 255, 0.3);
      font-weight: bold;
      color: #0ff;
    }

    td.selected {
      outline: 2px solid #ff0;
    }

    td:hover {
      background-color: rgba(255, 255, 255, 0.1);
    }
  </style>
</head>
<body>
  <h1>📅 Lịch Galaxy</h1>
  <div id="calendar">
    <label for="date">Chọn ngày:</label>
    <input type="date" id="date">

    <label for="task">Nội dung công việc:</label>
    <textarea id="task" rows="4" placeholder="Nhập nội dung..."></textarea>

    <button onclick="saveTask()">💾 Lưu công việc</button>
    <div id="saveStatus">✅ Đã lưu!</div>
  </div>

  <h2 id="monthTitle"></h2>
  <table class="calendar-table" id="monthCalendar"></table>

  <audio id="clickSound" src="https://www.soundjay.com/buttons/sounds/button-29.mp3"></audio>

  <script>
    let currentMonth = new Date().getMonth();
    let currentYear = new Date().getFullYear();
    let selectedDateStr = null;

    function saveTask() {
      const date = document.getElementById('date').value;
      const task = document.getElementById('task').value;
      const status = document.getElementById('saveStatus');
      const sound = document.getElementById('clickSound');

      if (!date) {
        alert("Vui lòng chọn ngày!");
        return;
      }

      localStorage.setItem(`task-${date}`, task);
      sound.play();
      status.style.display = 'block';
      setTimeout(() => {
        status.style.display = 'none';
      }, 2000);
      selectedDateStr = date;
      const selectedDate = new Date(date);
      renderCalendar(selectedDate.getMonth(), selectedDate.getFullYear());
    }

    function renderCalendar(month = currentMonth, year = currentYear) {
      currentMonth = month;
      currentYear = year;
      const firstDay = new Date(year, month, 1);
      const lastDay = new Date(year, month + 1, 0);
      const calendar = document.getElementById('monthCalendar');
      const title = document.getElementById('monthTitle');
      title.innerText = `Tháng ${month + 1} / ${year}`;

      const days = ['CN', 'T2', 'T3', 'T4', 'T5', 'T6', 'T7'];
      let html = '<tr>' + days.map(d => `<th>${d}</th>`).join('') + '</tr><tr>';

      let dayIndex = 0;
      let firstDayOfWeek = firstDay.getDay();
      for (let i = 0; i < firstDayOfWeek; i++) {
        html += '<td></td>'; dayIndex++;
      }

      for (let d = 1; d <= lastDay.getDate(); d++) {
        const date = new Date(year, month, d);
        const dateStr = date.toISOString().split('T')[0];
        const saved = localStorage.getItem(`task-${dateStr}`);
        let classes = saved ? 'marked' : '';
        if (selectedDateStr === dateStr) classes += ' selected';
        html += `<td onclick="selectDate('${dateStr}')" class="${classes.trim()}">${d}</td>`;
        dayIndex++;
        if (dayIndex % 7 === 0) html += '</tr><tr>';
      }

      while (dayIndex % 7 !== 0) {
        html += '<td></td>'; dayIndex++;
      }

      html += '</tr>';
      calendar.innerHTML = html;
    }

    function selectDate(dateStr) {
      const dateInput = document.getElementById('date');
      dateInput.value = dateStr;
      selectedDateStr = dateStr;
      const saved = localStorage.getItem(`task-${dateStr}`);
      document.getElementById('task').value = saved || '';
      const selected = new Date(dateStr);
      renderCalendar(selected.getMonth(), selected.getFullYear());
    }

    window.onload = function () {
      const today = new Date();
      document.getElementById('date').valueAsDate = today;
      selectedDateStr = today.toISOString().split('T')[0];
      renderCalendar(today.getMonth(), today.getFullYear());
    }
  </script>
</body>
</html>
