<!doctype html>
<html lang="id">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>EduTask — Manajemen Tugas & Pengingat</title>
  <style>
    :root{
      --bg:#f6f7fb; --card:#ffffff; --text:#0f1724; --muted:#6b7280; --accent:#0066ff; --success:#10b981;
    }
    [data-theme="dark"]{
      --bg:#0b1020; --card:#0f1724; --text:#e6eef8; --muted:#94a3b8; --accent:#58a6ff; --success:#34d399;
    }
    *{box-sizing:border-box}
    body{margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,Helvetica,Arial; background:var(--bg); color:var(--text); padding:20px}
    .app{max-width:1000px;margin:0 auto;display:grid;grid-template-columns:1fr 360px;gap:20px}
    header{grid-column:1/-1;display:flex;justify-content:space-between;align-items:center;margin-bottom:6px}
    h1{font-size:1.4rem;margin:0}
    .card{background:var(--card);border-radius:12px;padding:16px;box-shadow:0 6px 18px rgba(11,15,25,0.06)}
    .controls{display:flex;gap:8px;align-items:center}
    button{border:0;background:var(--accent);color:white;padding:8px 12px;border-radius:8px;cursor:pointer}
    button.ghost{background:transparent;color:var(--text);border:1px solid rgba(0,0,0,0.06)}
    .small{padding:6px 8px;font-size:0.9rem}
    form{display:grid;gap:8px}
    input,textarea,select{padding:10px;border-radius:8px;border:1px solid #e6eef8;background:transparent;color:var(--text)}
    .task-list{display:flex;flex-direction:column;gap:8px}
    .task{display:flex;justify-content:space-between;gap:12px;align-items:center;padding:10px;border-radius:8px;border:1px solid rgba(0,0,0,0.04)}
    .meta{font-size:0.85rem;color:var(--muted)}
    .pill{padding:6px 8px;border-radius:999px;font-size:0.8rem}
    .high{background:#ffe9e9;color:#9b1c1c}
    .med{background:#fff7e6;color:#7a4a00}
    .low{background:#eaf9f1;color:#14644d}
    .right-col{display:flex;flex-direction:column;gap:12px}
    .calendar{display:grid;grid-template-columns:repeat(7,1fr);gap:6px}
    .day{min-height:64px;padding:6px;border-radius:8px;border:1px solid rgba(0,0,0,0.03);background:linear-gradient(0deg, rgba(255,255,255,0.02), transparent)}
    .today{outline:2px solid rgba(0,102,255,0.15)}
    .dot{height:8px;width:8px;border-radius:50%;display:inline-block;margin-right:4px}
    .footer-note{font-size:0.85rem;color:var(--muted);margin-top:8px}
    @media (max-width:900px){.app{grid-template-columns:1fr;}.right-col{order:2}.card{margin-bottom:12px}}
  </style>
</head>
<body>
  <div class="app" id="app">
    <header>
      <h1>EduTask — Manajemen Tugas & Pengingat</h1>
      <div class="controls">
        <label style="display:flex;align-items:center;gap:8px">
          <input type="checkbox" id="simpleMode"> Simple Mode
        </label>
        <button id="toggleTheme" class="small ghost">Toggle Dark</button>
      </div>
    </header>

    <!-- left column -->
    <div>
      <div class="card">
        <h3>Tambah / Edit Tugas</h3>
        <form id="taskForm">
          <input type="hidden" id="taskId">
          <input id="title" placeholder="Judul tugas (contoh: PR Matematika)" required>
          <select id="subject">
            <option value="Umum">Umum</option>
            <option value="Matematika">Matematika</option>
            <option value="Bahasa">Bahasa</option>
            <option value="IPA">IPA</option>
            <option value="Lainnya">Lainnya</option>
          </select>
          <textarea id="description" rows="2" placeholder="Deskripsi singkat (opsional)"></textarea>
          <div style="display:flex;gap:8px">
            <input type="datetime-local" id="deadline" required style="flex:1">
            <select id="priority" style="width:120px">
              <option value="high">Prioritas: Tinggi</option>
              <option value="medium">Prioritas: Sedang</option>
              <option value="low">Prioritas: Rendah</option>
            </select>
          </div>
          <div style="display:flex;gap:8px;margin-top:6px">
            <button type="submit">Simpan</button>
            <button type="button" id="clearBtn" class="ghost">Bersihkan</button>
          </div>
        </form>
      </div>

      <div class="card" style="margin-top:12px">
        <h3>Daftar Tugas</h3>
        <div id="tasks" class="task-list"></div>
        <div class="footer-note">Minimal konsultasi: 3x — Jangan lupa commit progres tiap konsultasi.</div>
      </div>
    </div>

    <!-- right column -->
    <aside class="right-col">
      <div class="card">
        <h3>Ringkasan</h3>
        <div id="summary"></div>
      </div>

      <div class="card">
        <h3>Kalender</h3>
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
          <div id="monthLabel"></div>
          <div>
            <button id="prevMonth" class="small ghost">‹</button>
            <button id="nextMonth" class="small ghost">›</button>
          </div>
        </div>
        <div style="display:grid;grid-template-columns:repeat(7,1fr);gap:6px;margin-bottom:6px;font-size:0.8rem;color:var(--muted)">
          <div>Min</div><div>Sen</div><div>Sel</div><div>Rab</div><div>Kam</div><div>Jum</div><div>Sab</div>
        </div>
        <div id="calendar" class="calendar"></div>
      </div>

      <div class="card">
        <h3>Pengaturan Notifikasi</h3>
        <p class="meta">Aplikasi akan mencoba meminta izin notifikasi. Jika tidak diizinkan, pengingat akan muncul sebagai popup di tab ini.</p>
        <div style="display:flex;gap:8px;align-items:center">
          <button id="requestNotif" class="small">Minta Izin Notif</button>
          <div id="notifStatus" class="meta"></div>
        </div>
      </div>
    </aside>
  </div>

  <script>
    // ====== Utilities ======
    const STORAGE_KEY = 'edutask_tasks_v1';
    const THEME_KEY = 'edutask_theme_v1';
    const tasksEl = document.getElementById('tasks');
    const summaryEl = document.getElementById('summary');
    const calendarEl = document.getElementById('calendar');
    const monthLabel = document.getElementById('monthLabel');
    let tasks = [];
    let currentMonth = new Date();

    // load/save
    function load(){
      try{tasks = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');}catch(e){tasks=[]}
      const theme = localStorage.getItem(THEME_KEY) || 'light';
      if(theme==='dark') document.documentElement.setAttribute('data-theme','dark');
    }
    function save(){localStorage.setItem(STORAGE_KEY, JSON.stringify(tasks));renderAll();}

    // ====== Rendering ======
    function renderAll(){renderTasks();renderSummary();renderCalendar();}

    function renderTasks(){
      tasksEl.innerHTML='';
      const simple = document.getElementById('simpleMode').checked;
      let list = tasks.slice().sort((a,b)=> new Date(a.deadline) - new Date(b.deadline));
      if(simple) list = list.filter(t=>!t.done).slice(0,6);
      if(list.length===0){tasksEl.innerHTML='<div class="meta">Belum ada tugas. Tambah tugas untuk mulai.</div>';return}
      list.forEach(t=>{
        const wrap = document.createElement('div');wrap.className='task';
        const left = document.createElement('div');
        left.innerHTML = `<strong>${escapeHTML(t.title)}</strong><div class="meta">${escapeHTML(t.subject)} • ${formatDate(t.deadline)}</div>`;
        const right = document.createElement('div');
        right.style.display='flex';right.style.gap='8px';right.style.alignItems='center';
        const p = document.createElement('span');p.className='pill '+(t.priority==='high'?'high':(t.priority==='medium'?'med':'low'));p.textContent = t.priority.toUpperCase();
        const actions = document.createElement('div');
        actions.innerHTML = `<button class="small" data-id="${t.id}" data-action="edit">Edit</button> <button class="small" data-id="${t.id}" data-action="toggle">${t.done? 'Batal' : 'Selesai'}</button> <button class="small ghost" data-id="${t.id}" data-action="delete">Hapus</button>`;
        right.appendChild(p);right.appendChild(actions);
        if(t.done){wrap.style.opacity=0.6}
        wrap.appendChild(left);wrap.appendChild(right);
        tasksEl.appendChild(wrap);
      });
    }

    function renderSummary(){
      const total = tasks.length;
      const open = tasks.filter(t=>!t.done).length;
      const overdue = tasks.filter(t=>!t.done && new Date(t.deadline) < new Date()).length;
      summaryEl.innerHTML = `<div class="meta">Total: ${total} • Belum selesai: ${open} • Terlambat: ${overdue}</div>`;
    }

    function renderCalendar(){
      calendarEl.innerHTML='';
      const d = new Date(currentMonth.getFullYear(), currentMonth.getMonth(), 1);
      monthLabel.textContent = d.toLocaleString('id-ID',{month:'long', year:'numeric'});
      const startDay = d.getDay(); // 0=Sun
      // days in month
      const daysInMonth = new Date(d.getFullYear(), d.getMonth()+1, 0).getDate();
      // create blanks
      for(let i=0;i<startDay;i++){const el=document.createElement('div');el.className='day';calendarEl.appendChild(el)}
      for(let day=1;day<=daysInMonth;day++){
        const el = document.createElement('div');el.className='day';
        const thisDate = new Date(d.getFullYear(), d.getMonth(), day);
        if(isSameDay(thisDate, new Date())) el.classList.add('today');
        el.innerHTML = `<div style="font-weight:600">${day}</div>`;
        // add dots for tasks
        const tasksOn = tasks.filter(t=>{
          const td = new Date(t.deadline);
          return td.getFullYear()===thisDate.getFullYear() && td.getMonth()===thisDate.getMonth() && td.getDate()===thisDate.getDate();
        });
        tasksOn.slice(0,3).forEach(ts=>{
          const dot = document.createElement('span');dot.className='dot';
          dot.title = ts.title + ' • ' + formatTime(ts.deadline);
          dot.style.background = ts.priority==='high'? 'var(--accent)' : (ts.priority==='medium'? 'var(--muted)' : 'var(--success)');
          el.appendChild(dot);
        });
        calendarEl.appendChild(el);
      }
    }

    // ====== Form & CRUD ======
    document.getElementById('taskForm').addEventListener('submit', e=>{
      e.preventDefault();
      const id = document.getElementById('taskId').value || uid();
      const payload = {
        id,
        title: document.getElementById('title').value.trim(),
        subject: document.getElementById('subject').value,
        description: document.getElementById('description').value.trim(),
        deadline: document.getElementById('deadline').value,
        priority: document.getElementById('priority').value,
        done: false,
        createdAt: new Date().toISOString()
      };
      const exists = tasks.findIndex(t=>t.id===id);
      if(exists>-1) tasks[exists] = {...tasks[exists], ...payload}; else tasks.push(payload);
      save();
      e.target.reset();document.getElementById('taskId').value='';
    });

    document.getElementById('clearBtn').addEventListener('click', ()=>{document.getElementById('taskForm').reset();document.getElementById('taskId').value='';});

    tasksEl.addEventListener('click', ev=>{
      const btn = ev.target.closest('button'); if(!btn) return;
      const id = btn.dataset.id; const action = btn.dataset.action;
      if(action==='edit'){const t = tasks.find(x=>x.id===id); if(t){document.getElementById('taskId').value=t.id;document.getElementById('title').value=t.title;document.getElementById('subject').value=t.subject;document.getElementById('description').value=t.description;document.getElementById('deadline').value=t.deadline;document.getElementById('priority').value=t.priority; window.scrollTo({top:0,behavior:'smooth'})}}
      if(action==='toggle'){tasks = tasks.map(x=> x.id===id? {...x,done: !x.done}:x); save();}
      if(action==='delete'){if(confirm('Hapus tugas ini?')){tasks = tasks.filter(x=>x.id!==id); save();}}
    });

    // ====== Notifications & reminder check ======
    const notifStatus = document.getElementById('notifStatus');
    document.getElementById('requestNotif').addEventListener('click', async ()=>{
      if(!('Notification' in window)){alert('Browser Anda tidak mendukung Notification API.');return}
      const r = await Notification.requestPermission(); notifStatus.textContent = 'Status: ' + r;
    });

    function checkReminders(){
      const now = new Date();
      tasks.forEach(t=>{
        if(t._notified) return; // already notified recently
        if(t.done) return;
        const dl = new Date(t.deadline);
        const diff = dl - now; // ms
        // if deadline passed -> overdue notification once
        if(diff < 0 && !t._overdueNotified){
          notify(`Tugas terlambat: ${t.title}`, `Deadline: ${formatDateTime(t.deadline)}`);
          t._overdueNotified = true; t._notified = true;
        }
        // H-1 (24 hours) reminder
        const oneDay = 24*3600*1000;
        if(diff <= oneDay && diff > 0 && !t._day1Notified){
          notify(`Pengingat H-1: ${t.title}`, `Deadline besok: ${formatDateTime(t.deadline)}`);
          t._day1Notified = true; t._notified = true;
        }
        // 3 hours reminder
        const threeHours = 3*3600*1000;
        if(diff <= threeHours && diff > 0 && !t._3hNotified){
          notify(`Pengingat 3 jam: ${t.title}`, `Deadline: ${formatDateTime(t.deadline)}`);
          t._3hNotified = true; t._notified = true;
        }
      });
      save();
    }

    function notify(title, body){
      if('Notification' in window && Notification.permission === 'granted'){
        new Notification(title, {body, silent:false});
      } else {
        // fallback: small in-app popup
        inAppToast(title + ' — ' + body);
      }
    }

    function inAppToast(msg){
      const el = document.createElement('div');el.textContent = msg;
      Object.assign(el.style,{position:'fixed',right:'20px',bottom:'20px',background:'var(--card)',padding:'12px 16px',borderRadius:'10px',boxShadow:'0 8px 20px rgba(0,0,0,0.12)'});
      document.body.appendChild(el);setTimeout(()=>el.style.opacity='0',3000);setTimeout(()=>el.remove(),3800);
    }

    // run every minute
    setInterval(checkReminders, 60*1000);

    // ====== Helpers ======
    function uid(){return 't_'+Math.random().toString(36).slice(2,9)}
    function formatDate(d){try{return new Date(d).toLocaleDateString('id-ID',{day:'2-digit',month:'short',year:'numeric'})}catch(e){return d}}
    function formatTime(d){try{return new Date(d).toLocaleTimeString('id-ID',{hour:'2-digit',minute:'2-digit'})}catch(e){return d}}
    function formatDateTime(d){return formatDate(d)+ ' ' + formatTime(d)}
    function escapeHTML(s){return (s||'').replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;","\"":"&quot;","'":"&#39;"}[c]))}
    function isSameDay(a,b){return a.getFullYear()===b.getFullYear() && a.getMonth()===b.getMonth() && a.getDate()===b.getDate()}

    // simple theme toggle
    document.getElementById('toggleTheme').addEventListener('click', ()=>{
      const isDark = document.documentElement.getAttribute('data-theme') === 'dark';
      if(isDark){document.documentElement.removeAttribute('data-theme'); localStorage.setItem(THEME_KEY,'light');} else {document.documentElement.setAttribute('data-theme','dark'); localStorage.setItem(THEME_KEY,'dark');}
    });

    // month nav
    document.getElementById('prevMonth').addEventListener('click', ()=>{currentMonth = new Date(currentMonth.getFullYear(), currentMonth.getMonth()-1,1); renderAll();});
    document.getElementById('nextMonth').addEventListener('click', ()=>{currentMonth = new Date(currentMonth.getFullYear(), currentMonth.getMonth()+1,1); renderAll();});

    // persist simple mode
    document.getElementById('simpleMode').addEventListener('change', ()=>renderAll());

    // initial load
    load(); renderAll(); checkReminders();

    // expose for debugging if needed
    window.edutask = {tasks, save, load};
  </script>
</body>
</html>
