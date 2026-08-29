# crossfittracker
JH's CrossFit
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>CrossFit Tracker</title>

  <!-- iOS 홈 화면 전체화면(독립형 앱) 설정 -->
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <meta name="apple-mobile-web-app-title" content="CF Tracker">
  <meta name="mobile-web-app-capable" content="yes">
  <meta name="theme-color" content="#0f172a">
  
  <!-- 내장형 앱 아이콘 (외부 파일 불필요) -->
  <link rel="apple-touch-icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 180 180' width='180' height='180'%3E%3Crect width='180' height='180' rx='40' fill='%230f172a'/%3E%3Cpath d='M35 90h110M50 65v50M40 72v36M130 65v50M140 72v36' stroke='%2338bdf8' stroke-width='10' stroke-linecap='round'/%3E%3C/svg%3E">

  <style>
    :root {
      --bg: #0f172a;
      --card-bg: #1e293b;
      --text: #f8fafc;
      --text-muted: #94a3b8;
      --primary: #38bdf8;
      --lift-color: #f59e0b;
      --wod-color: #ec4899;
      --border: #334155;
    }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      max-width: 480px;
      margin: 0 auto;
      padding: env(safe-area-inset-top, 12px) 12px env(safe-area-inset-bottom, 12px) 12px;
      background: var(--bg);
      color: var(--text);
      -webkit-tap-highlight-color: transparent;
    }
    .tabs { display: flex; gap: 8px; margin-bottom: 12px; margin-top: 6px; }
    .tab-btn {
      flex: 1;
      padding: 14px 0;
      border: 1px solid var(--border);
      background: var(--card-bg);
      color: var(--text-muted);
      border-radius: 12px;
      font-weight: 700;
      font-size: 0.95rem;
      cursor: pointer;
    }
    .tab-btn.active.lift-tab {
      background: rgba(245, 158, 11, 0.15);
      border-color: var(--lift-color);
      color: var(--lift-color);
    }
    .tab-btn.active.wod-tab {
      background: rgba(236, 72, 153, 0.15);
      border-color: var(--wod-color);
      color: var(--wod-color);
    }
    .card {
      background: var(--card-bg);
      padding: 16px;
      border-radius: 14px;
      border: 1px solid var(--border);
      margin-bottom: 12px;
    }
    h2, h3 { margin: 0 0 10px 0; font-size: 1.05rem; }
    label { font-size: 0.8rem; color: var(--text-muted); display: block; margin-top: 8px; margin-bottom: 3px; }
    input, select, button {
      width: 100%;
      padding: 12px;
      border: 1px solid var(--border);
      border-radius: 8px;
      box-sizing: border-box;
      background: #0f172a;
      color: var(--text);
      font-size: 1rem;
      appearance: none;
    }
    select {
      background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='%2394a3b8' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
      background-repeat: no-repeat;
      background-position: right 12px center;
      background-size: 16px;
    }
    .row { display: flex; gap: 8px; }
    .row > div { flex: 1; }
    .submit-btn {
      margin-top: 14px;
      padding: 14px;
      font-weight: 700;
      font-size: 1rem;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      color: #0f172a;
    }
    .submit-btn.lift { background: var(--lift-color); }
    .submit-btn.wod { background: var(--wod-color); color: white; }
    .btn-secondary {
      background: #1e293b;
      color: var(--text);
      border: 1px solid var(--border);
      font-size: 0.85rem;
      padding: 10px;
      font-weight: 600;
      cursor: pointer;
      border-radius: 8px;
    }
    .pr-badge {
      background: rgba(56, 189, 248, 0.15);
      color: var(--primary);
      border: 1px solid rgba(56, 189, 248, 0.3);
      padding: 4px 8px;
      border-radius: 6px;
      font-weight: bold;
      font-size: 0.85rem;
    }
    .badge-rxd { background: #0284c7; color: white; padding: 2px 6px; border-radius: 4px; font-size: 0.72rem; }
    .badge-scaled { background: #64748b; color: white; padding: 2px 6px; border-radius: 4px; font-size: 0.72rem; }
    .badge-custom { background: #8b5cf6; color: white; padding: 2px 6px; border-radius: 4px; font-size: 0.72rem; }
    .history-list { max-height: 200px; overflow-y: auto; margin-top: 10px; }
    .history-item {
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
      padding: 8px 0;
      border-bottom: 1px solid var(--border);
      font-size: 0.85rem;
    }
    .del-btn { background: none; border: none; color: #ef4444; width: auto; margin: 0; padding: 0 4px; cursor: pointer; }
    .hidden { display: none; }
    #canvasContainer { width: 100%; height: 210px; position: relative; margin-top: 10px; }
    canvas { width: 100%; height: 100%; }
  </style>
</head>
<body>

  <!-- 모드 전환 탭 -->
  <div class="tabs">
    <button class="tab-btn active lift-tab" id="liftTabBtn" onclick="switchMode('lift')">🏋️ 1RM 기록</button>
    <button class="tab-btn wod-tab" id="wodTabBtn" onclick="switchMode('wod')">⏱️ Named WOD</button>
  </div>

  <!-- 1. 종목/와드 선택 -->
  <div class="card">
    <div style="display:flex; justify-content:space-between; align-items:center;">
      <h2 id="selectorTitle">1RM 종목</h2>
      <button style="width:auto; padding:4px 8px; font-size:0.75rem; background:transparent; border:1px solid var(--border); color:var(--text-muted);" onclick="toggleAddName()">+ 새 항목 직접 입력</button>
    </div>

    <div id="addCustomArea" class="hidden" style="background:#0b1120; padding:10px; border-radius:8px; margin: 8px 0; border:1px dashed var(--border);">
      <input type="text" id="customNameInput" placeholder="종목/와드 이름 입력">
      <button class="submit-btn" style="background:var(--primary); margin-top:8px; padding:8px;" onclick="saveCustomName()">목록에 추가</button>
    </div>

    <select id="itemSelect" onchange="handleItemChange()"></select>
  </div>

  <!-- 2. 기록 입력 -->
  <div class="card">
    <h2>📝 기록 추가</h2>
    
    <label>운동 날짜</label>
    <input type="date" id="logDate">

    <!-- 1RM 입력 섹션 -->
    <div id="liftInputSection">
      <label>무게 및 단위</label>
      <div class="row">
        <div style="flex:2;">
          <input type="number" id="liftWeight" placeholder="무게" step="0.5" inputmode="decimal">
        </div>
        <div style="flex:1;">
          <select id="liftUnit">
            <option value="kg">kg</option>
            <option value="lb">lb</option>
          </select>
        </div>
      </div>
    </div>

    <!-- WOD 입력 섹션 -->
    <div id="wodInputSection" class="hidden">
      <label>측정 방식</label>
      <select id="wodScoreType" onchange="toggleWodInputType()">
        <option value="time">소요 시간 (For Time)</option>
        <option value="rounds">라운드 / 렙스 (AMRAP)</option>
      </select>

      <div id="wodTimeArea" style="margin-top:6px;">
        <div class="row">
          <div><input type="number" id="wodMinutes" placeholder="분 (Min)" inputmode="numeric"></div>
          <div><input type="number" id="wodSeconds" placeholder="초 (Sec)" inputmode="numeric"></div>
        </div>
      </div>

      <div id="wodRoundArea" class="hidden" style="margin-top:6px;">
        <div class="row">
          <div><input type="number" id="wodRounds" placeholder="라운드 (Rds)" inputmode="numeric"></div>
          <div><input type="number" id="wodExtraReps" placeholder="추가 횟수 (Reps)" inputmode="numeric"></div>
        </div>
      </div>

      <label>수행 방식</label>
      <select id="wodExecutionType">
        <option value="Rx">Rx'd</option>
        <option value="Scaled">Scaled</option>
        <option value="Custom">Custom (커스텀)</option>
      </select>
    </div>

    <!-- 공통 메모/비고란 -->
    <label>비고란 (스케일링/커스텀 내용, 사용 무게, 컨디션 등)</label>
    <input type="text" id="logNotes" placeholder="예: 링딥으로 대체, 75lb 사용, 무릎 통증 등">

    <button id="submitBtn" class="submit-btn lift" onclick="saveRecord()">1RM 기록 저장</button>
  </div>

  <!-- 3. 차트 및 히스토리 -->
  <div class="card">
    <div style="display:flex; justify-content:space-between; align-items:center;">
      <h2 id="chartTitle">성장 곡선</h2>
      <span class="pr-badge" id="prDisplay">PR: -</span>
    </div>
    <div id="canvasContainer">
      <canvas id="progressChart"></canvas>
    </div>

    <h3 style="margin-top: 16px; font-size: 0.9rem; color: var(--text-muted);">기록 히스토리</h3>
    <div class="history-list" id="historyList"></div>
  </div>

  <!-- 4. 데이터 백업 및 복원 관리 -->
  <div class="card">
    <h2>💾 데이터 백업 및 복원</h2>
    <p style="font-size:0.8rem; color:var(--text-muted); margin:0 0 10px 0;">
      브라우저 캐시 삭제 시 데이터가 유실되지 않도록 정기적으로 백업 파일을 보관하세요.
    </p>
    <div class="row">
      <div>
        <button class="btn-secondary" onclick="exportData()">📤 백업 파일 저장</button>
      </div>
      <div>
        <button class="btn-secondary" onclick="document.getElementById('importFileInput').click()">📥 백업 복원</button>
        <input type="file" id="importFileInput" class="hidden" accept=".json" onchange="importData(event)">
      </div>
    </div>
  </div>

  <script>
    let currentMode = 'lift';

    const defaultLifts = ["Back Squat", "Deadlift", "Clean & Jerk", "Snatch", "Strict Press", "Front Squat"];
    const defaultWods = ["Fran", "Grace", "Cindy", "Helen", "Murph", "Diane", "Annie"];

    function loadJSON(key, fallback) {
      try {
        const raw = localStorage.getItem(key);
        return raw ? JSON.parse(raw) : fallback;
      } catch (e) {
        return fallback;
      }
    }

    let customLifts = loadJSON('cf_custom_lifts', defaultLifts);
    let customWods  = loadJSON('cf_custom_wods',  defaultWods);
    let records     = loadJSON('cf_all_records',  []);

    document.getElementById('logDate').valueAsDate = new Date();

    function switchMode(mode) {
      currentMode = mode;
      document.getElementById('liftTabBtn').className = `tab-btn lift-tab ${mode === 'lift' ? 'active' : ''}`;
      document.getElementById('wodTabBtn').className = `tab-btn wod-tab ${mode === 'wod' ? 'active' : ''}`;
      
      const isLift = mode === 'lift';
      document.getElementById('liftInputSection').classList.toggle('hidden', !isLift);
      document.getElementById('wodInputSection').classList.toggle('hidden', isLift);
      
      const submitBtn = document.getElementById('submitBtn');
      if (isLift) {
        submitBtn.className = 'submit-btn lift';
        submitBtn.innerText = '1RM 기록 저장';
        document.getElementById('selectorTitle').innerText = '1RM 종목';
      } else {
        submitBtn.className = 'submit-btn wod';
        submitBtn.innerText = 'WOD 기록 저장';
        document.getElementById('selectorTitle').innerText = 'Named WOD';
      }

      document.getElementById('addCustomArea').classList.add('hidden');
      populateSelect();
    }

    function populateSelect() {
      const select = document.getElementById('itemSelect');
      select.innerHTML = '';
      const list = currentMode === 'lift' ? customLifts : customWods;

      list.forEach(name => {
        const opt = document.createElement('option');
        opt.value = name;
        opt.innerText = name;
        select.appendChild(opt);
      });

      handleItemChange();
    }

    function toggleAddName() {
      document.getElementById('addCustomArea').classList.toggle('hidden');
    }

    function saveCustomName() {
      const name = document.getElementById('customNameInput').value.trim();
      if (!name) return alert('이름을 입력해주세요.');

      if (currentMode === 'lift') {
        if (!customLifts.includes(name)) customLifts.push(name);
        localStorage.setItem('cf_custom_lifts', JSON.stringify(customLifts));
      } else {
        if (!customWods.includes(name)) customWods.push(name);
        localStorage.setItem('cf_custom_wods', JSON.stringify(customWods));
      }

      document.getElementById('customNameInput').value = '';
      toggleAddName();
      populateSelect();
      document.getElementById('itemSelect').value = name;
      handleItemChange();
    }

    function toggleWodInputType() {
      const type = document.getElementById('wodScoreType').value;
      document.getElementById('wodTimeArea').classList.toggle('hidden', type !== 'time');
      document.getElementById('wodRoundArea').classList.toggle('hidden', type !== 'rounds');
    }

    function handleItemChange() {
      updateView();
    }

    function saveRecord() {
      const name = document.getElementById('itemSelect').value;
      const date = document.getElementById('logDate').value;
      const notes = document.getElementById('logNotes').value.trim();

      if (!name || !date) return alert('종목과 날짜를 확인해주세요.');

      let logEntry = {
        id: Date.now(),
        mode: currentMode,
        name: name,
        date: date,
        notes: notes
      };

      if (currentMode === 'lift') {
        const weight = parseFloat(document.getElementById('liftWeight').value);
        const unit = document.getElementById('liftUnit').value;
        if (!weight) return alert('무게를 입력해주세요.');

        logEntry.weight = weight;
        logEntry.unit = unit;
      } else {
        const scoreType = document.getElementById('wodScoreType').value;
        const executionType = document.getElementById('wodExecutionType').value;
        logEntry.scoreType = scoreType;
        logEntry.executionType = executionType;

        if (scoreType === 'time') {
          const m = parseInt(document.getElementById('wodMinutes').value || 0, 10);
          const s = parseInt(document.getElementById('wodSeconds').value || 0, 10);
          const totalSeconds = (m * 60) + s;
          if (totalSeconds <= 0) return alert('시간을 입력해주세요.');
          logEntry.totalSeconds = totalSeconds;
        } else {
          const rds = parseInt(document.getElementById('wodRounds').value || 0, 10);
          const reps = parseInt(document.getElementById('wodExtraReps').value || 0, 10);
          if (rds === 0 && reps === 0) return alert('라운드 또는 횟수를 입력해주세요.');
          logEntry.rounds = rds;
          logEntry.reps = reps;
        }
      }

      records.push(logEntry);
      records.sort((a, b) => new Date(a.date) - new Date(b.date));
      localStorage.setItem('cf_all_records', JSON.stringify(records));

      document.getElementById('liftWeight').value = '';
      document.getElementById('wodMinutes').value = '';
      document.getElementById('wodSeconds').value = '';
      document.getElementById('wodRounds').value = '';
      document.getElementById('wodExtraReps').value = '';
      document.getElementById('logNotes').value = '';

      updateView();
    }

    function deleteRecord(id) {
      if (!confirm('이 기록을 삭제하시겠습니까?')) return;
      records = records.filter(r => r.id !== id);
      localStorage.setItem('cf_all_records', JSON.stringify(records));
      updateView();
    }

    function formatLiftScore(r) {
      return `${r.weight} ${r.unit}`;
    }

    function formatWodScore(r) {
      if (r.scoreType === 'time') {
        const m = Math.floor(r.totalSeconds / 60);
        const s = r.totalSeconds % 60;
        return `${m}분 ${s < 10 ? '0' + s : s}초`;
      } else {
        return `${r.rounds}R + ${r.reps}`;
      }
    }

    function drawNativeChart(canvas, labels, values, color, isTime) {
      const dpr = window.devicePixelRatio || 1;
      const rect = canvas.getBoundingClientRect();
      canvas.width = rect.width * dpr;
      canvas.height = rect.height * dpr;
      
      const ctx = canvas.getContext('2d');
      ctx.scale(dpr, dpr);
      ctx.clearRect(0, 0, rect.width, rect.height);

      if (values.length === 0) {
        ctx.fillStyle = '#64748b';
        ctx.font = '13px sans-serif';
        ctx.textAlign = 'center';
        ctx.fillText('기록된 데이터가 없습니다', rect.width / 2, rect.height / 2);
        return;
      }

      const padLeft = 46;
      const padRight = 20;
      const padTop = 20;
      const padBottom = 30;
      const chartW = rect.width - padLeft - padRight;
      const chartH = rect.height - padTop - padBottom;

      let minV = Math.min(...values);
      let maxV = Math.max(...values);
      if (minV === maxV) { minV -= 5; maxV += 5; }
      const range = (maxV - minV) || 1;

      ctx.strokeStyle = '#334155';
      ctx.lineWidth = 1;
      ctx.fillStyle = '#94a3b8';
      ctx.font = '10px sans-serif';
      ctx.textAlign = 'right';

      const gridSteps = 3;
      for (let i = 0; i <= gridSteps; i++) {
        const yVal = minV + (range / gridSteps) * i;
        const yPos = padTop + chartH - (i / gridSteps) * chartH;
        
        ctx.beginPath();
        ctx.moveTo(padLeft, yPos);
        ctx.lineTo(rect.width - padRight, yPos);
        ctx.stroke();

        const labelText = isTime 
          ? `${Math.floor(yVal/60)}m${Math.round(yVal%60)}s`
          : Math.round(yVal);
        ctx.fillText(labelText, padLeft - 6, yPos + 3);
      }

      const points = values.map((val, idx) => {
        const x = values.length === 1 
          ? padLeft + chartW / 2 
          : padLeft + (idx / (values.length - 1)) * chartW;
        const y = padTop + chartH - ((val - minV) / range) * chartH;
        return { x, y, val, label: labels[idx] };
      });

      ctx.strokeStyle = color;
      ctx.lineWidth = 2.5;
      ctx.beginPath();
      points.forEach((pt, i) => {
        if (i === 0) ctx.moveTo(pt.x, pt.y);
        else ctx.lineTo(pt.x, pt.y);
      });
      ctx.stroke();

      ctx.fillStyle = color;
      ctx.textAlign = 'center';
      points.forEach((pt) => {
        ctx.beginPath();
        ctx.arc(pt.x, pt.y, 4.5, 0, Math.PI * 2);
        ctx.fill();

        ctx.fillStyle = '#94a3b8';
        const dateStr = pt.label.slice(5);
        ctx.fillText(dateStr, pt.x, rect.height - 10);
        ctx.fillStyle = color;
      });
    }

    function updateView() {
      const selectedName = document.getElementById('itemSelect').value;
      if (!selectedName) return;

      const filtered = records.filter(r => r.mode === currentMode && r.name === selectedName);
      document.getElementById('chartTitle').innerText = `${selectedName} 성장 곡선`;

      let prText = '-';
      if (filtered.length > 0) {
        if (currentMode === 'lift') {
          const maxVal = Math.max(...filtered.map(r => r.weight));
          const match = filtered.find(r => r.weight === maxVal);
          prText = `${maxVal} ${match.unit}`;
        } else {
          const rxLogs = filtered.filter(r => (r.executionType || (r.isRxd ? 'Rx' : 'Scaled')) === 'Rx');
          const targetPool = rxLogs.length > 0 ? rxLogs : filtered;
          const tag = rxLogs.length > 0 ? '(Rx)' : `(${targetPool[0].executionType || 'Scaled'})`;

          if (targetPool[0].scoreType === 'time') {
            const minTime = Math.min(...targetPool.map(r => r.totalSeconds));
            const m = Math.floor(minTime / 60);
            const s = minTime % 60;
            prText = `${m}분 ${s < 10 ? '0' + s : s}초 ${tag}`;
          } else {
            const maxRound = Math.max(...targetPool.map(r => (r.rounds * 1000) + r.reps));
            const best = targetPool.find(r => (r.rounds * 1000) + r.reps === maxRound);
            prText = `${best.rounds}R + ${best.reps} ${tag}`;
          }
        }
      }
      document.getElementById('prDisplay').innerText = `PR: ${prText}`;

      const historyList = document.getElementById('historyList');
      historyList.innerHTML = '';
      [...filtered].reverse().forEach(r => {
        const item = document.createElement('div');
        item.className = 'history-item';
        
        const scoreStr = currentMode === 'lift' ? formatLiftScore(r) : formatWodScore(r);
        
        let badge = '';
        if (currentMode === 'wod') {
          const exec = r.executionType || (r.isRxd ? 'Rx' : 'Scaled');
          const badgeClass = exec === 'Rx' ? 'badge-rxd' : exec === 'Custom' ? 'badge-custom' : 'badge-scaled';
          badge = `<span class="${badgeClass}">${exec}</span>`;
        }

        item.innerHTML = `
          <div>
            <div>
              <strong>${r.date}</strong> : <span style="font-weight:600; color:var(--text);">${scoreStr}</span> ${badge}
            </div>
            ${r.notes ? `<div style="color:var(--text-muted); font-size:0.78rem; margin-top:2px;">📝 ${r.notes}</div>` : ''}
          </div>
          <button class="del-btn" onclick="deleteRecord(${r.id})">✕</button>
        `;
        historyList.appendChild(item);
      });

      const canvas = document.getElementById('progressChart');
      const chartData = filtered.map(r => {
        if (currentMode === 'lift') return r.weight;
        return r.scoreType === 'time' ? r.totalSeconds : (r.rounds * 100) + r.reps;
      });
      const chartLabels = filtered.map(r => r.date);
      const isTimeScore = currentMode === 'wod' && filtered[0]?.scoreType === 'time';
      const lineColor = currentMode === 'lift' ? '#f59e0b' : '#ec4899';

      drawNativeChart(canvas, chartLabels, chartData, lineColor, isTimeScore);
    }

    async function exportData() {
      const payload = { records, customLifts, customWods, exportDate: new Date().toISOString() };
      const json = JSON.stringify(payload, null, 2);
      const name = `crossfit_backup_${new Date().toISOString().slice(0, 10)}.json`;
      const file = new File([json], name, { type: 'application/json' });

      if (navigator.canShare && navigator.canShare({ files: [file] })) {
        try { await navigator.share({ files: [file] }); return; } catch (e) {}
      }
      const url = URL.createObjectURL(file);
      const a = document.createElement('a');
      a.href = url; a.download = name;
      document.body.appendChild(a); a.click(); a.remove();
      URL.revokeObjectURL(url);
    }

    function importData(event) {
      const file = event.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = function(e) {
        try {
          const imported = JSON.parse(e.target.result);
          if (imported.records && Array.isArray(imported.records)) {
            if (confirm(`백업 파일에서 ${imported.records.length}개의 기록을 불러오시겠습니까? (기존 데이터와 병합/대체됩니다)`)) {
              records = imported.records;
              if (imported.customLifts) customLifts = imported.customLifts;
              if (imported.customWods) customWods = imported.customWods;

              localStorage.setItem('cf_all_records', JSON.stringify(records));
              localStorage.setItem('cf_custom_lifts', JSON.stringify(customLifts));
              localStorage.setItem('cf_custom_wods', JSON.stringify(customWods));

              populateSelect();
              updateView();
              alert('데이터가 성공적으로 복원되었습니다.');
            }
          } else {
            alert('올바른 백업 파일 형식이 아닙니다.');
          }
        } catch (err) {
          alert('파일을 읽는 도중 오류가 발생했습니다.');
        }
      };
      reader.readAsText(file);
      event.target.value = '';
    }

    window.addEventListener('resize', () => {
      updateView();
    });

    switchMode('lift');
  </script>
</body>
</html>
