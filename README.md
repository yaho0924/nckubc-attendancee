<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
  <title>點名系統</title>
  <style>
    :root {
      --primary: #1e3a8a;
      --bg: #f8fafc;
      --card-bg: #ffffff;
      --border: #cbd5e1;
    }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background-color: var(--bg);
      color: #0f172a;
      margin: 0;
      padding: 12px;
    }
    .container {
      max-width: 650px;
      margin: 0 auto;
    }
    h1 {
      color: var(--primary);
      text-align: center;
      margin: 10px 0 20px 0;
      font-size: 22px;
    }
    .card {
      background: var(--card-bg);
      border-radius: 12px;
      padding: 16px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.05);
      margin-bottom: 16px;
    }
    .card-title {
      font-size: 15px;
      font-weight: bold;
      margin-bottom: 12px;
      color: var(--primary);
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .event-info-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 10px;
      margin-bottom: 12px;
    }
    .form-group {
      display: flex;
      flex-direction: column;
      gap: 4px;
    }
    .form-group label {
      font-size: 12px;
      font-weight: bold;
      color: #475569;
    }
    .add-form {
      display: flex;
      gap: 8px;
      margin-bottom: 12px;
    }
    input[type="text"], input[type="number"], input[type="date"], textarea, select {
      padding: 8px 10px;
      border: 1px solid var(--border);
      border-radius: 6px;
      font-size: 14px;
      box-sizing: border-box;
      width: 100%;
    }
    .input-number { width: 80px; }
    textarea {
      height: 90px;
      margin-bottom: 8px;
      font-family: inherit;
    }
    button {
      background: var(--primary);
      color: white;
      border: none;
      padding: 10px 14px;
      border-radius: 6px;
      cursor: pointer;
      font-weight: bold;
      font-size: 14px;
      white-space: nowrap;
    }
    .btn-secondary { background: #475569; }
    .btn-export {
      background: #16a34a;
      width: 100%;
      padding: 14px;
      font-size: 16px;
      margin-top: 10px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
    }
    th, td {
      padding: 8px 4px;
      text-align: left;
      border-bottom: 1px solid var(--border);
      font-size: 14px;
      vertical-align: middle;
    }
    th { background: #f1f5f9; }
    .roster-tags {
      display: flex;
      flex-wrap: wrap;
      gap: 6px;
    }
    .roster-tag {
      background: #e2e8f0;
      padding: 6px 10px;
      border-radius: 20px;
      font-size: 13px;
      display: flex;
      align-items: center;
      gap: 6px;
    }
    .roster-tag button {
      background: none;
      color: #64748b;
      border: none;
      padding: 0;
      font-size: 14px;
    }
    .empty-tip {
      color: #94a3b8;
      font-size: 13px;
      text-align: center;
      padding: 12px 0;
    }
    .batch-box {
      background: #f1f5f9;
      padding: 12px;
      border-radius: 8px;
      margin-bottom: 12px;
      display: none;
    }
    .dropdown-container {
      position: relative;
      width: 100%;
    }
    .search-input {
      width: 100%;
      background: #fff;
    }
    .dropdown-list {
      position: absolute;
      top: 100%;
      left: 0;
      right: 0;
      max-height: 180px;
      overflow-y: auto;
      background: white;
      border: 1px solid var(--border);
      border-radius: 6px;
      box-shadow: 0 4px 6px rgba(0,0,0,0.1);
      z-index: 100;
      display: none;
    }
    .dropdown-item {
      padding: 8px 10px;
      cursor: pointer;
      font-size: 14px;
    }
    .dropdown-item:hover {
      background: #eff6ff;
      color: #2563eb;
    }
  </style>
</head>
<body>

<div class="container">
  <h1>📋 點名系統</h1>

  <!-- 1. 人員名冊管理 -->
  <div class="card">
    <div class="card-title">
      <span>👥 名冊管理</span>
      <div>
        <button class="btn-secondary" onclick="toggleBatchBox()" style="font-size:12px; padding:4px 8px;">臨時批次貼上</button>
      </div>
    </div>

    <!-- 批次複製貼上區塊 -->
    <div class="batch-box" id="batchBox">
      <textarea id="batchText" placeholder="請貼上名單資料（每行一位）&#10;範例：&#10;#1 黃昱綸&#10;- 吳尚霖"></textarea>
      <button onclick="importBatchText()" style="width:100%; background:#2563eb;">確認匯入名單</button>
    </div>

    <!-- 單一新增區塊 -->
    <div class="add-form">
      <input type="text" id="rosterNumber" class="input-number" placeholder="背號" />
      <input type="text" id="rosterName" placeholder="姓名..." />
      <button onclick="addRosterPlayer()">臨時新增</button>
    </div>
    <div class="roster-tags" id="rosterList"></div>
  </div>

  <!-- 2. 今日點名紀錄 -->
  <div class="card">
    <div class="card-title">
      <span>📝 點名紀錄</span>
      <button onclick="addAttendanceRow()" style="background:#2563eb; font-size:12px; padding:6px 10px;">+ 新增列</button>
    </div>

    <div class="event-info-grid">
      <div class="form-group">
        <label>活動日期</label>
        <input type="date" id="eventDate" />
      </div>
      <div class="form-group">
        <label>活動名稱</label>
        <input type="text" id="eventName" placeholder="例如：例行練球 / 友誼賽" value="例行練球" />
      </div>
    </div>

    <table>
      <thead>
        <tr>
          <th>球員搜尋（背號/姓名）</th>
          <th style="width: 80px;">投票</th>
          <th style="width: 90px;">實際情況</th>
          <th style="width: 32px;"></th>
        </tr>
      </thead>
      <tbody id="attendanceBody"></tbody>
    </table>
  </div>

  <button class="btn-export" onclick="exportToExcel()">📊 匯出 Excel 報表</button>
</div>

<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<script>
  // 📌 寫死固定名冊（包含張永宸）
  var fixedRoster = [
    { id: 1, number: "1", name: "黃昱綸" },
    { id: 2, number: "2", name: "陳聲皓" },
    { id: 3, number: "3", name: "許昭威" },
    { id: 4, number: "4", name: "蔡朋峻" },
    { id: 5, number: "8", name: "黃子承" },
    { id: 6, number: "9", name: "謝翔聿" },
    { id: 7, number: "12", name: "張永宸" },
    { id: 8, number: "16", name: "謝竺庭" },
    { id: 9, number: "17", name: "王昱峻" },
    { id: 10, number: "19", name: "葉詠翔" },
    { id: 11, number: "18", name: "顏辰峻" },
    { id: 12, number: "20", name: "趙昱齊" },
    { id: 13, number: "22", name: "張冠瑀" },
    { id: 14, number: "26", name: "許恒嘉" },
    { id: 15, number: "27", name: "詹閔任" },
    { id: 16, number: "28", name: "黃家庠" },
    { id: 17, number: "30", name: "余書農（教練）" },
    { id: 18, number: "31", name: "林塏翔" },
    { id: 19, number: "33", name: "張晉緁" },
    { id: 20, number: "35", name: "王學睿" },
    { id: 21, number: "40", name: "林建曄" },
    { id: 22, number: "45", name: "黃尹昌" },
    { id: 23, number: "46", name: "黄昱睿" },
    { id: 24, number: "47", name: "林禹潼" },
    { id: 25, number: "51", name: "賴沂磊" },
    { id: 26, number: "52", name: "楊睿升" },
    { id: 27, number: "55", name: "王祥予" },
    { id: 28, number: "58", name: "王昱峻" },
    { id: 29, number: "60", name: "鍾旻劭" },
    { id: 30, number: "63", name: "卓廷典" },
    { id: 31, number: "64", name: "蔡閎堯" },
    { id: 32, number: "69", name: "林正展" },
    { id: 33, number: "72", name: "尤允礽" },
    { id: 34, number: "6", name: "王奐詠" },
    { id: 35, number: "53", name: "羅傑羽" },
    { id: 36, number: "80", name: "羅博强" },
    { id: 37, number: "82", name: "黃楷彤" },
    { id: 38, number: "87", name: "林洺賢" },
    { id: 39, number: "88", name: "楊曜亘" },
    { id: 40, number: "90", name: "李珦琳" },
    { id: 41, number: "91", name: "李有佳" },
    { id: 42, number: "95", name: "歐陽仲恩" },
    { id: 43, number: "98", name: "黃中彥" },
    { id: 44, number: "99", name: "楊承曄" },
    { id: 45, number: "", name: "吳尚霖" },
    { id: 46, number: "", name: "許宏倫" },
    { id: 47, number: "", name: "張探遠" },
    { id: 48, number: "", name: "李品鋐" },
    { id: 49, number: "", name: "高子淳" },
    { id: 50, number: "", name: "范譽" },
    { id: 51, number: "", name: "謝安綸" },
    { id: 52, number: "", name: "黃振淩" },
    { id: 53, number: "", name: "葉宸銨" },
    { id: 54, number: "", name: "蔡文偉" },
    { id: 55, number: "", name: "黃聖翔" },
    { id: 56, number: "", name: "張淳嘉" },
    { id: 57, number: "", name: "李秉軒" },
    { id: 58, number: "", name: "蔡政哲" },
    { id: 59, number: "", name: "蔡益宸" },
    { id: 60, number: "", name: "高睿陽" },
    { id: 61, number: "", name: "王曦賢" },
    { id: 62, number: "", name: "呂承恩" }
  ];

  var roster = JSON.parse(JSON.stringify(fixedRoster));
  var attendanceList = [];

  function init() {
    document.getElementById('eventDate').value = new Date().toISOString().split('T')[0];
    renderRoster();
  }

  function toggleBatchBox() {
    var box = document.getElementById('batchBox');
    box.style.display = box.style.display === 'block' ? 'none' : 'block';
  }

  function importBatchText() {
    var textEl = document.getElementById('batchText');
    var text = textEl.value.trim();
    if (!text) return alert('請先貼上資料！');

    var lines = text.split('\n');
    var addedCount = 0;

    lines.forEach(function(line) {
      var trimmed = line.trim().replace(/✕/g, '');
      if (!trimmed) return;

      var parts = trimmed.split(/[\s\t,]+/);
      var number = '';
      var name = '';

      if (parts.length >= 2) {
        number = parts[0].replace('#', '').replace('-', '').trim();
        name = parts.slice(1).join(' ').trim();
      } else {
        name = parts[0].replace(/^[-#]/, '').trim();
      }

      if (name) {
        roster.push({ id: Date.now() + Math.random(), number: number, name: name });
        addedCount++;
      }
    });

    renderRoster();
    textEl.value = '';
    toggleBatchBox();
    alert('成功匯入 ' + addedCount + ' 位人員！');
  }

  function addRosterPlayer() {
    var numInput = document.getElementById('rosterNumber');
    var nameInput = document.getElementById('rosterName');
    var number = numInput.value.trim();
    var name = nameInput.value.trim();

    if (name) {
      roster.push({ id: Date.now(), number: number, name: name });
      numInput.value = '';
      nameInput.value = '';
      renderRoster();
    } else {
      alert('請輸入姓名！');
    }
  }

  function deleteRosterPlayer(index) {
    roster.splice(index, 1);
    renderRoster();
  }

  function renderRoster() {
    var listEl = document.getElementById('rosterList');
    listEl.innerHTML = '';

    if (roster.length === 0) {
      listEl.innerHTML = '<div class="empty-tip">尚無名單。</div>';
    } else {
      roster.forEach(function(player, index) {
        var tag = document.createElement('div');
        tag.className = 'roster-tag';
        var numStr = player.number ? '#' + player.number + ' ' : '';
        tag.innerHTML = '<span>' + numStr + player.name + '</span>' +
                        '<button onclick="deleteRosterPlayer(' + index + ')">✕</button>';
        listEl.appendChild(tag);
      });
    }
    renderAttendanceTable();
  }

  function renderAttendanceTable() {
    var tbody = document.getElementById('attendanceBody');
    tbody.innerHTML = '';

    if (attendanceList.length === 0) {
      tbody.innerHTML = '<tr><td colspan="4" class="empty-tip">點擊「+ 新增列」開始點名。</td></tr>';
      return;
    }

    attendanceList.forEach(function(item, index) {
      var tr = document.createElement('tr');
      var currentPlayer = roster.find(function(p) { return Number(p.id) === Number(item.playerId); }) || { number: '', name: '' };
      var currentLabel = currentPlayer.name 
        ? (currentPlayer.number ? '#' + currentPlayer.number + ' ' + currentPlayer.name : currentPlayer.name) 
        : '';

      tr.innerHTML = `
        <td>
          <div class="dropdown-container">
            <input 
              type="text" 
              class="search-input" 
              placeholder="搜尋背號或姓名..." 
              value="${currentLabel}" 
              onfocus="showDropdown(${index})" 
              oninput="filterDropdown(${index}, this.value)"
            />
            <div class="dropdown-list" id="dropdown-${index}"></div>
          </div>
        </td>
        <td>
          <select onchange="attendanceList[${index}].vote = this.value;">
            <option value="出席" ${item.vote === '出席' ? 'selected' : ''}>出席</option>
            <option value="晚到" ${item.vote === '晚到' ? 'selected' : ''}>晚到</option>
            <option value="不到" ${item.vote === '不到' ? 'selected' : ''}>不到</option>
          </select>
        </td>
        <td>
          <select onchange="attendanceList[${index}].actual = this.value;">
            <option value="準時到" ${item.actual === '準時到' ? 'selected' : ''}>準時到</option>
            <option value="遲到" ${item.actual === '遲到' ? 'selected' : ''}>遲到</option>
            <option value="未到" ${item.actual === '未到' ? 'selected' : ''}>未到</option>
          </select>
        </td>
        <td><button style="background:#ef4444; padding:4px 6px; font-size:12px;" onclick="deleteAttendanceRow(${index})">✕</button></td>
      `;
      tbody.appendChild(tr);
    });
  }

  function showDropdown(index) {
    closeAllDropdowns();
    var dropdown = document.getElementById('dropdown-' + index);
    filterDropdown(index, '');
    if (dropdown) dropdown.style.display = 'block';
  }

  function filterDropdown(index, query) {
    var dropdown = document.getElementById('dropdown-' + index);
    if (!dropdown) return;
    dropdown.innerHTML = '';
    var q = query.toLowerCase().replace('#', '').trim();

    var filtered = roster.filter(function(p) { 
      return p.name.toLowerCase().indexOf(q) !== -1 || (p.number && p.number.toString().indexOf(q) !== -1);
    });

    if (filtered.length === 0) {
      dropdown.innerHTML = '<div class="dropdown-item" style="color:#94a3b8;">無符合人員</div>';
    } else {
      filtered.forEach(function(p) {
        var item = document.createElement('div');
        item.className = 'dropdown-item';
        item.innerText = p.number ? '#' + p.number + ' ' + p.name : p.name;
        item.onclick = function() { selectPlayer(index, p); };
        dropdown.appendChild(item);
      });
    }
  }

  function selectPlayer(index, player) {
    attendanceList[index].playerId = player.id;
    renderAttendanceTable();
  }

  function closeAllDropdowns() {
    var lists = document.querySelectorAll('.dropdown-list');
    lists.forEach(function(el) { el.style.display = 'none'; });
  }

  document.addEventListener('click', function(e) {
    if (!e.target.closest('.dropdown-container')) {
      closeAllDropdowns();
    }
  });

  function addAttendanceRow() {
    var defaultPlayerId = roster.length > 0 ? roster[0].id : '';
    attendanceList.push({ playerId: defaultPlayerId, vote: '出席', actual: '準時到' });
    renderAttendanceTable();
  }

  function deleteAttendanceRow(index) {
    attendanceList.splice(index, 1);
    renderAttendanceTable();
  }

  function exportToExcel() {
    if (attendanceList.length === 0) return alert('尚無點名紀錄！');

    var dateVal = document.getElementById('eventDate').value || '未填日期';
    var nameVal = document.getElementById('eventName').value || '未填活動';

    var statusOrder = { '準時到': 1, '遲到': 2, '未到': 3 };

    var formattedData = attendanceList.map(function(item) {
      var player = roster.find(function(p) { return Number(p.id) === Number(item.playerId); }) || { number: '', name: '未選擇' };
      return {
        "活動日期": dateVal,
        "活動名稱": nameVal,
        "背號": player.number ? '#' + player.number : '-',
        "姓名": player.name,
        "原投票狀況": item.vote,
        "實際到場狀況": item.actual
      };
    }).sort(function(a, b) {
      return (statusOrder[a.實際到場狀況] || 99) - (statusOrder[b.實際到場狀況] || 99);
    });

    var worksheet = XLSX.utils.json_to_sheet(formattedData);

    worksheet['!cols'] = [
      { wch: 14 },
      { wch: 16 },
      { wch: 10 },
      { wch: 16 },
      { wch: 12 },
      { wch: 14 }
    ];

    var workbook = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(workbook, worksheet, "點名紀錄");

    XLSX.writeFile(workbook, dateVal + '_' + nameVal + '_點名紀錄.xlsx');
  }

  window.onload = init;
</script>

</body>
</html># nckubc-attendancee
