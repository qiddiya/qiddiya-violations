<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="theme-color" content="#060d1a">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="مخالفات القدية">
<link rel="manifest" id="pwaManifest">
<title>نظام مخالفات القدية — V3 🔥</title>
<link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;900&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<!-- Firebase SDK -->
<script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-firestore-compat.js"></script>
<style>
:root {
  --gold: #d4af37;
  --gold-light: #ffd700;
  --gold-dim: rgba(212,175,55,0.15);
  --dark: #060d1a;
  --dark2: #0d1b2a;
  --dark3: #112240;
  --card: rgba(17,34,64,0.8);
  --border: rgba(212,175,55,0.2);
  --text: #e2e8f0;
  --muted: rgba(255,255,255,0.45);
  --green: #10b981;
  --red: #ef4444;
  --amber: #f59e0b;
  --radius: 16px;

  /* light mode overrides (inactive by default) */
  --bg-body: #060d1a;
  --bg-sidebar: rgba(13,27,42,0.98);
  --bg-card: rgba(17,34,64,0.8);
  --text-main: #e2e8f0;
  --text-muted: rgba(255,255,255,0.45);
  --border-color: rgba(212,175,55,0.2);
  --input-bg: rgba(255,255,255,0.06);
  --input-text: #e2e8f0;
  --mesh1: rgba(212,175,55,0.06);
  --mesh2: rgba(16,185,129,0.04);
}

/* ═══════════════════════════════
   LIGHT MODE
   ═══════════════════════════════ */
body.light-mode {
  --dark: #f0f4f8;
  --dark2: #e2e8f0;
  --dark3: #d1dce8;
  --card: rgba(255,255,255,0.92);
  --border: rgba(180,145,20,0.3);
  --text: #1a2744;
  --muted: rgba(30,50,90,0.5);
  --gold-dim: rgba(212,175,55,0.12);
  --bg-body: #eef2f8;
  --bg-sidebar: rgba(250,252,255,0.98);
  --bg-card: rgba(255,255,255,0.92);
  --text-main: #1a2744;
  --text-muted: rgba(30,50,90,0.5);
  --border-color: rgba(180,145,20,0.25);
  --input-bg: rgba(0,0,0,0.04);
  --input-text: #1a2744;
  --mesh1: rgba(212,175,55,0.04);
  --mesh2: rgba(16,185,129,0.02);
}

body.light-mode::before {
  background:
    radial-gradient(ellipse 80% 50% at 20% 10%, var(--mesh1) 0%, transparent 60%),
    radial-gradient(ellipse 60% 40% at 80% 80%, var(--mesh2) 0%, transparent 60%);
}

body.light-mode .sidebar {
  background: linear-gradient(180deg, rgba(250,252,255,0.99) 0%, rgba(240,244,250,0.99) 100%);
  border-left-color: var(--border);
  box-shadow: -4px 0 20px rgba(0,0,0,0.08);
}
body.light-mode .nav-item { color: rgba(30,50,90,0.6); }
body.light-mode .nav-item:hover { background: rgba(212,175,55,0.1); color: #1a2744; }
body.light-mode .nav-item.active { color: #8b6914; background: rgba(212,175,55,0.15); }
body.light-mode .card {
  background: rgba(255,255,255,0.92);
  border-color: rgba(180,145,20,0.2);
  box-shadow: 0 4px 20px rgba(0,0,0,0.08);
}
body.light-mode .card-header h3 { color: #1a2744; }
body.light-mode .stat-card {
  background: rgba(255,255,255,0.9);
  border-color: rgba(180,145,20,0.2);
  box-shadow: 0 4px 15px rgba(0,0,0,0.07);
}
body.light-mode .form-input,
body.light-mode .form-select,
body.light-mode .form-textarea {
  background: rgba(0,0,0,0.04);
  border-color: rgba(180,145,20,0.25);
  color: #1a2744;
}
body.light-mode .form-select option { background: #f5f7fa; color: #1a2744; }
body.light-mode .form-input::placeholder { color: rgba(30,50,90,0.4); }
body.light-mode .form-input:focus,
body.light-mode .form-select:focus { background: rgba(212,175,55,0.05); border-color: var(--gold); }
body.light-mode .plate-input { background: rgba(0,0,0,0.04); border-color: rgba(180,145,20,0.25); }
body.light-mode .company-trigger { background: rgba(0,0,0,0.04); border-color: rgba(180,145,20,0.25); color: #1a2744; }
body.light-mode .company-dropdown { background: #f8fafc; border-color: var(--gold); }
body.light-mode .company-search-input { background: rgba(0,0,0,0.04); color: #1a2744; }
body.light-mode .company-option { color: #1a2744; }
body.light-mode .v-card { background: rgba(255,255,255,0.92); border-color: rgba(180,145,20,0.2); }
body.light-mode .v-card-header { border-bottom-color: rgba(180,145,20,0.15); }
body.light-mode .v-detail strong { color: #8b6914; }
body.light-mode .v-detail { color: #1a2744; border-color: rgba(0,0,0,0.05); }
body.light-mode .filter-select, body.light-mode .filter-input {
  background: rgba(0,0,0,0.04); border-color: rgba(180,145,20,0.25); color: #1a2744;
}
body.light-mode .filter-select option { background: #f5f7fa; }
body.light-mode .report-table th { background: rgba(212,175,55,0.15); }
body.light-mode .report-table td { color: #1a2744; border-color: rgba(0,0,0,0.06); }
body.light-mode .report-table tr:nth-child(even) td { background: rgba(0,0,0,0.02); }
body.light-mode .top-list li { border-color: rgba(0,0,0,0.06); }
body.light-mode .top-name { color: #1a2744; }
body.light-mode .map-btn { background: rgba(212,175,55,0.12); border-color: rgba(180,145,20,0.25); }
body.light-mode .upload-btn { background: rgba(212,175,55,0.1); border-color: rgba(180,145,20,0.3); }
body.light-mode .mobile-header { background: rgba(248,250,252,0.98); border-bottom-color: rgba(180,145,20,0.2); }
body.light-mode .btn-logout { background: rgba(239,68,68,0.08); border-color: rgba(239,68,68,0.2); }
body.light-mode .wa-settings { background: rgba(37,211,102,0.06); border-color: rgba(37,211,102,0.2); }
body.light-mode .login-card { background: rgba(255,255,255,0.95); border-color: rgba(180,145,20,0.25); }
body.light-mode .form-label { color: #8b6914; }
body.light-mode .page-subtitle { color: rgba(30,50,90,0.5); }
body.light-mode .stat-label { color: rgba(30,50,90,0.55); }
body.light-mode .dl-btn { background: rgba(0,0,0,0.04); border-color: rgba(180,145,20,0.25); color: #8b6914; }
body.light-mode .mobile-bottom-nav { background: rgba(250,252,255,0.98); border-top-color: rgba(180,145,20,0.2); }
body.light-mode .bottom-nav-item { color: rgba(30,50,90,0.5); }
body.light-mode .bottom-nav-item.active { color: #8b6914; }

/* ═══ Theme Toggle Button ═══ */
.theme-toggle-btn {
  width: 100%;
  padding: 10px 14px;
  border-radius: 10px;
  background: var(--gold-dim);
  border: 1px solid var(--border);
  color: var(--gold);
  font-size: 13px;
  font-weight: 700;
  cursor: pointer;
  font-family: 'Tajawal', sans-serif;
  display: flex !important;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
  transition: all 0.2s;
}
.theme-toggle-btn:hover { background: rgba(212,175,55,0.25); }
body.light-mode .theme-toggle-btn { background: rgba(0,0,0,0.04); border-color: rgba(180,145,20,0.25); color: #8b6914; }

/* ═══ Theme Toggle Pill (floating) ═══ */
.theme-pill {
  position: fixed; top: 16px; left: 16px; z-index: 9999;
  display: flex; align-items: center; gap: 6px;
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: 30px;
  padding: 6px 14px;
  cursor: pointer;
  font-family: 'Tajawal', sans-serif;
  font-size: 13px; font-weight: 700;
  color: var(--gold);
  box-shadow: 0 4px 20px rgba(0,0,0,0.2);
  transition: all 0.3s;
  backdrop-filter: blur(20px);
}
.theme-pill:hover { transform: scale(1.05); }
body.light-mode .theme-pill { background: rgba(255,255,255,0.95); color: #8b6914; box-shadow: 0 4px 20px rgba(0,0,0,0.1); }
*, *::before, *::after { margin:0; padding:0; box-sizing:border-box; }

body {
  font-family: 'Tajawal', sans-serif;
  background: var(--dark);
  color: var(--text);
  min-height: 100vh;
  overflow-x: hidden;
  transition: background 0.3s, color 0.3s;
}

/* ── Background mesh ── */
body::before {
  content:'';
  position:fixed; inset:0; z-index:0; pointer-events:none;
  background:
    radial-gradient(ellipse 80% 50% at 20% 10%, rgba(212,175,55,0.06) 0%, transparent 60%),
    radial-gradient(ellipse 60% 40% at 80% 80%, rgba(16,185,129,0.04) 0%, transparent 60%),
    radial-gradient(ellipse 100% 80% at 50% 50%, rgba(6,13,26,0) 0%, var(--dark) 100%);
}

/* ── Sidebar ── */
.sidebar {
  position: fixed; top:0; right:0; bottom:0;
  width: 260px; z-index:100;
  background: linear-gradient(180deg, rgba(13,27,42,0.98) 0%, rgba(6,13,26,0.98) 100%);
  border-left: 1px solid var(--border);
  display: flex; flex-direction: column;
  backdrop-filter: blur(20px);
  transition: transform 0.3s ease;
}
.sidebar-logo {
  padding: 28px 24px 20px;
  border-bottom: 1px solid var(--border);
}
.sidebar-logo .logo-icon {
  width:48px; height:48px; border-radius:12px;
  background: linear-gradient(135deg, var(--gold), var(--gold-light));
  display:flex; align-items:center; justify-content:center;
  font-size:22px; margin-bottom:10px;
}
.sidebar-logo h2 {
  font-size:15px; font-weight:900; color:var(--gold-light);
  line-height:1.3;
}
.sidebar-logo p { font-size:11px; color:var(--muted); margin-top:3px; }

.sidebar-user {
  padding: 16px 24px;
  border-bottom: 1px solid var(--border);
  display:flex; align-items:center; gap:12px;
}
.user-avatar {
  width:38px; height:38px; border-radius:10px;
  background: var(--gold-dim); border:1px solid var(--border);
  display:flex; align-items:center; justify-content:center;
  font-size:18px; flex-shrink:0;
}
.user-info { flex:1; min-width:0; }
.user-name { font-size:13px; font-weight:700; color:var(--text); white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
.user-role { font-size:11px; color:var(--gold); }

.sidebar-nav { flex:1; padding:16px 12px; overflow-y:auto; }
.nav-item {
  display:flex; align-items:center; gap:12px;
  padding:12px 14px; border-radius:12px; cursor:pointer;
  color:var(--muted); font-size:14px; font-weight:500;
  transition:all 0.2s; margin-bottom:4px;
  border: 1px solid transparent;
}
.nav-item:hover { background:var(--gold-dim); color:var(--text); }
.nav-item.active {
  background: linear-gradient(135deg, rgba(212,175,55,0.2), rgba(212,175,55,0.05));
  color:var(--gold-light); border-color:var(--border); font-weight:700;
}
.nav-item .nav-icon { font-size:18px; width:24px; text-align:center; }
.nav-badge {
  margin-right:auto; background:var(--gold); color:var(--dark);
  border-radius:20px; padding:2px 8px; font-size:11px; font-weight:900;
}

.sidebar-footer {
  padding:16px 12px;
  border-top: 1px solid var(--border);
}
.btn-logout {
  width:100%; padding:11px; border-radius:12px;
  background: rgba(239,68,68,0.1); border:1px solid rgba(239,68,68,0.3);
  color:#fca5a5; font-size:13px; font-weight:700; cursor:pointer;
  font-family:'Tajawal',sans-serif; transition:all 0.2s;
}
.btn-logout:hover { background:rgba(239,68,68,0.2); }

/* ── Main content ── */
.main {
  margin-left: 0;
  margin-right: 260px;
  min-height: 100vh;
  padding: 28px;
  position: relative; z-index:1;
  transition: margin-right 0.3s ease;
}
body.sidebar-collapsed .main { margin-right: 0; }
body.sidebar-collapsed .sidebar { transform: translateX(110%); }

/* ── Sidebar Toggle Button ── */
.sidebar-toggle {
  position: fixed; top: 12px; right: 268px; z-index: 101;
  width: 36px; height: 36px; border-radius: 10px;
  background: var(--card); border: 1px solid var(--border);
  color: var(--gold); font-size: 16px; cursor: pointer;
  display: flex; align-items: center; justify-content: center;
  transition: all 0.3s ease; box-shadow: 0 2px 8px rgba(0,0,0,0.2);
}
.sidebar-toggle:hover { background: var(--gold-dim); }
body.sidebar-collapsed .sidebar-toggle { right: 12px; }

/* ── Page header ── */
.page-header {
  margin-bottom:28px;
  display:flex; align-items:center; justify-content:space-between; flex-wrap:wrap; gap:16px;
}
.page-title { font-size:24px; font-weight:900; color:var(--gold-light); }
.page-subtitle { font-size:13px; color:var(--muted); margin-top:3px; }

/* ── Cards ── */
.card {
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  backdrop-filter: blur(20px);
  overflow: hidden;
}
.card-header {
  padding:20px 24px 16px;
  border-bottom:1px solid var(--border);
  display:flex; align-items:center; gap:12px;
}
.card-header h3 { font-size:16px; font-weight:700; color:var(--text); }
.card-body { padding:24px; }

/* ── Stat cards ── */
.stats-row {
  display:grid; grid-template-columns:repeat(auto-fit, minmax(180px,1fr)); gap:16px;
  margin-bottom:24px;
}
.stat-card {
  background: var(--card);
  border:1px solid var(--border);
  border-radius:var(--radius);
  padding:20px;
  position:relative; overflow:hidden;
  transition: transform 0.2s, box-shadow 0.2s;
}
.stat-card:hover { transform:translateY(-2px); box-shadow:0 8px 30px rgba(0,0,0,0.3); }
.stat-card::before {
  content:''; position:absolute; inset:0;
  background: linear-gradient(135deg, var(--accent-color,rgba(212,175,55,0.08)) 0%, transparent 60%);
}
.stat-card.gold  { --accent-color: rgba(212,175,55,0.12); }
.stat-card.green { --accent-color: rgba(16,185,129,0.12); }
.stat-card.red   { --accent-color: rgba(239,68,68,0.10); }
.stat-card.amber { --accent-color: rgba(245,158,11,0.10); }
.stat-card.blue  { --accent-color: rgba(59,130,246,0.10); }

.stat-icon {
  width:42px; height:42px; border-radius:10px;
  display:flex; align-items:center; justify-content:center; font-size:20px;
  margin-bottom:12px;
}
.stat-card.gold  .stat-icon { background:rgba(212,175,55,0.15); }
.stat-card.green .stat-icon { background:rgba(16,185,129,0.15); }
.stat-card.red   .stat-icon { background:rgba(239,68,68,0.12); }
.stat-card.amber .stat-icon { background:rgba(245,158,11,0.12); }
.stat-card.blue  .stat-icon { background:rgba(59,130,246,0.12); }

.stat-value {
  font-size:34px; font-weight:900;
  line-height:1; margin-bottom:4px;
}
.stat-card.gold  .stat-value { color:var(--gold-light); }
.stat-card.green .stat-value { color:#34d399; }
.stat-card.red   .stat-value { color:#f87171; }
.stat-card.amber .stat-value { color:#fbbf24; }
.stat-card.blue  .stat-value { color:#60a5fa; }
.stat-label { font-size:12px; color:var(--muted); font-weight:500; }

/* ── Charts grid ── */
.charts-grid {
  display:grid; grid-template-columns:1fr 1fr; gap:20px; margin-bottom:24px;
}
.chart-container { position:relative; height:240px; }

/* ── Top lists ── */
.top-list { list-style:none; }
.top-list li {
  display:flex; align-items:center; gap:12px;
  padding:10px 0; border-bottom:1px solid rgba(255,255,255,0.05);
}
.top-list li:last-child { border-bottom:none; }
.top-rank {
  width:26px; height:26px; border-radius:8px; font-size:12px; font-weight:900;
  display:flex; align-items:center; justify-content:center; flex-shrink:0;
  background: var(--gold-dim); color:var(--gold);
}
.top-name { flex:1; font-size:13px; color:var(--text); font-weight:500; }
.top-count { font-size:13px; font-weight:700; color:var(--gold); }
.top-bar-wrap { width:80px; height:4px; background:rgba(255,255,255,0.1); border-radius:2px; }
.top-bar { height:100%; border-radius:2px; background:linear-gradient(90deg,var(--gold),var(--gold-light)); }

/* ── Forms ── */
.form-section { margin-bottom:20px; }
.form-label {
  display:block; font-size:13px; font-weight:700; color:var(--gold);
  margin-bottom:8px;
}
.form-label .req { color:#ef4444; margin-right:3px; }
.form-input, .form-select, .form-textarea {
  width:100%; padding:13px 16px;
  background: rgba(255,255,255,0.06);
  border:1px solid var(--border); border-radius:10px;
  color:var(--text); font-size:14px; font-family:'Tajawal',sans-serif; font-weight:500;
  transition: border-color 0.2s, background 0.2s;
}
.form-input:focus, .form-select:focus, .form-textarea:focus {
  outline:none; border-color:var(--gold);
  background: rgba(212,175,55,0.06);
  box-shadow: 0 0 0 3px rgba(212,175,55,0.12);
}
.form-input::placeholder { color:var(--muted); }
.form-select option { background:#1a2744; }
.form-grid { display:grid; grid-template-columns:1fr 1fr; gap:16px; }

/* ── Plate inputs ── */
.plate-wrap {
  display:flex; align-items:flex-start; gap:12px;
}
.plate-box { flex:1; }
.plate-input {
  width:100%; padding:13px 12px;
  background: rgba(255,255,255,0.06);
  border:1px solid var(--border); border-radius:10px;
  color:var(--gold-light); font-size:22px; font-weight:900;
  text-align:center; letter-spacing:6px; font-family:monospace;
  transition: border-color 0.2s;
}
.plate-input:focus { outline:none; border-color:var(--gold); }
.plate-hint { text-align:center; font-size:11px; color:var(--muted); margin-top:5px; }
.plate-sep { padding-top:12px; color:var(--gold); font-size:24px; font-weight:900; }
.plate-preview {
  display:none; margin-top:12px; text-align:center;
  background:var(--gold-dim); border:1px solid var(--border);
  border-radius:10px; padding:10px; font-size:20px;
  font-weight:900; color:var(--gold-light); letter-spacing:8px; font-family:monospace;
}

/* ── Company dropdown ── */
.company-trigger {
  width:100%; padding:13px 16px;
  background: rgba(255,255,255,0.06);
  border:1px solid var(--border); border-radius:10px;
  color:var(--text); font-size:14px; font-family:'Tajawal',sans-serif; font-weight:500;
  display:flex; justify-content:space-between; align-items:center;
  cursor:pointer; transition: border-color 0.2s;
}
.company-trigger:hover { border-color:rgba(212,175,55,0.5); }
.company-trigger.open { border-color:var(--gold); }
.company-dropdown {
  display:none; position:absolute;
  left:0; right:0; top:calc(100% + 4px);
  background:#0d1b2a; border:1px solid var(--gold);
  border-radius:12px; padding:12px; z-index:999;
  max-height:280px; overflow-y:auto;
  box-shadow:0 16px 40px rgba(0,0,0,0.6);
}
.company-search-input {
  width:100%; padding:10px 14px;
  background:rgba(255,255,255,0.08); border:1px solid var(--border); border-radius:8px;
  color:var(--text); font-size:13px; font-family:'Tajawal',sans-serif;
  margin-bottom:10px;
}
.company-search-input:focus { outline:none; border-color:var(--gold); }
.company-option {
  padding:9px 12px; border-radius:8px; cursor:pointer;
  color:var(--text); font-size:13px; font-weight:500; transition:background 0.15s;
}
.company-option:hover, .company-option.selected { background:var(--gold-dim); color:var(--gold-light); }
.company-wrap { position:relative; }

/* ── Map ── */
.map-actions { display:flex; gap:10px; margin-bottom:12px; }
.map-btn {
  flex:1; padding:11px; border-radius:10px;
  background:var(--gold-dim); border:1px solid var(--border);
  color:var(--gold); font-size:13px; font-weight:700;
  cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s;
}
.map-btn:hover { background:rgba(212,175,55,0.25); }
#map { height:320px; border-radius:12px; border:1px solid var(--border); display:none; }
.location-result {
  margin-top:10px; padding:12px 16px;
  background:rgba(16,185,129,0.08); border:1px solid rgba(16,185,129,0.2);
  border-radius:10px; display:none;
}
.location-result a { color:#34d399; text-decoration:underline; font-size:13px; }

/* ── Image upload ── */
.upload-btn {
  width:100%; padding:13px; border-radius:10px;
  background:var(--gold-dim); border:2px dashed var(--border);
  color:var(--gold); font-size:13px; font-weight:700;
  cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s; text-align:center;
}
.upload-btn:hover { border-color:var(--gold); background:rgba(212,175,55,0.2); }
.img-preview-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:8px; margin-top:12px; }
.img-preview-item {
  position:relative; width:100%; height:120px;
  border-radius:10px; overflow:hidden;
  border:1px solid var(--border);
}
.img-preview-item img { width:100%; height:100%; object-fit:cover; }
.img-remove {
  position: absolute; top: 4px; right: 4px;
  background: rgba(239,68,68,0.85); color: white;
  border: none; border-radius: 50%; width: 22px; height: 22px;
  font-size: 14px; font-weight: 900; cursor: pointer;
  display: flex; align-items: center; justify-content: center;
  line-height: 1;
}

/* ── Buttons ── */
.btn-primary {
  width:100%; padding:15px; border-radius:12px;
  background: linear-gradient(135deg, var(--gold), var(--gold-light));
  border:none; color:var(--dark); font-size:16px; font-weight:900;
  cursor:pointer; font-family:'Tajawal',sans-serif;
  transition: transform 0.2s, box-shadow 0.2s;
  box-shadow: 0 4px 20px rgba(212,175,55,0.3);
}
.btn-primary:hover { transform:translateY(-1px); box-shadow:0 8px 30px rgba(212,175,55,0.4); }
.btn-primary:active { transform:translateY(0); }
.btn-sm {
  padding:7px 14px; border-radius:8px; font-size:12px; font-weight:700;
  cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s; border:none;
}
.btn-approve { background:rgba(16,185,129,0.15); border:1px solid rgba(16,185,129,0.3); color:#34d399; }
.btn-approve:hover { background:rgba(16,185,129,0.25); }
.btn-reject  { background:rgba(239,68,68,0.12); border:1px solid rgba(239,68,68,0.3); color:#f87171; }
.btn-reject:hover  { background:rgba(239,68,68,0.22); }
.btn-pdf { background:var(--gold-dim); border:1px solid var(--border); color:var(--gold); }
.btn-pdf:hover { background:rgba(212,175,55,0.25); }

/* ── Violation cards ── */
.v-card {
  background:rgba(255,255,255,0.03); border:1px solid var(--border);
  border-radius:12px; padding:18px; margin-bottom:12px;
  transition:border-color 0.2s;
}
.v-card:hover { border-color:rgba(212,175,55,0.4); }
.v-card-header { display:flex; justify-content:space-between; align-items:flex-start; margin-bottom:12px; }
.v-ref { font-size:16px; font-weight:900; color:var(--gold-light); font-family:monospace; }
.v-status {
  padding:3px 10px; border-radius:20px; font-size:11px; font-weight:700;
}
.v-status.pending { background:rgba(245,158,11,0.15); color:#fbbf24; border:1px solid rgba(245,158,11,0.3); }
.v-status.approved { background:rgba(16,185,129,0.15); color:#34d399; border:1px solid rgba(16,185,129,0.3); }
.v-status.rejected { background:rgba(239,68,68,0.12); color:#f87171; border:1px solid rgba(239,68,68,0.3); }
.v-details { display:grid; grid-template-columns:1fr 1fr; gap:6px 16px; margin-bottom:12px; }
.v-detail { font-size:12px; color:var(--muted); }
.v-detail strong { color:var(--text); display:block; font-size:13px; margin-bottom:1px; }
.v-actions { display:flex; gap:8px; flex-wrap:wrap; margin-top:12px; padding-top:12px; border-top:1px solid rgba(255,255,255,0.05); }
.v-notes-input {
  width:100%; padding:10px 14px; border-radius:8px;
  background:rgba(255,255,255,0.05); border:1px solid var(--border);
  color:var(--text); font-size:13px; font-family:'Tajawal',sans-serif;
  margin-bottom:8px;
}
.v-notes-input:focus { outline:none; border-color:var(--gold); }

/* ── Search ── */
.search-wrap { position:relative; margin-bottom:20px; }
.search-icon { position:absolute; top:50%; transform:translateY(-50%); right:16px; color:var(--muted); }
.search-input {
  width:100%; padding:12px 44px 12px 16px;
  background:rgba(255,255,255,0.06); border:1px solid var(--border); border-radius:10px;
  color:var(--text); font-size:14px; font-family:'Tajawal',sans-serif;
}
.search-input:focus { outline:none; border-color:var(--gold); }
.search-input::placeholder { color:var(--muted); }

/* ── Login page ── */
#loginPage {
  min-height:100vh; display:flex; align-items:center; justify-content:center;
  padding:20px;
  background: linear-gradient(135deg, var(--dark) 0%, var(--dark2) 50%, var(--dark3) 100%);
}
.login-card {
  width:100%; max-width:440px;
  background:rgba(13,27,42,0.9); border:1px solid var(--border);
  border-radius:24px; padding:40px; backdrop-filter:blur(20px);
  box-shadow: 0 40px 80px rgba(0,0,0,0.5), 0 0 0 1px rgba(212,175,55,0.05);
}
.login-logo {
  text-align:center; margin-bottom:32px;
}
.login-logo .icon {
  width:72px; height:72px; border-radius:20px; margin:0 auto 16px;
  background:linear-gradient(135deg,var(--gold),var(--gold-light));
  display:flex; align-items:center; justify-content:center; font-size:34px;
}
.login-logo h1 { font-size:22px; font-weight:900; color:var(--gold-light); }
.login-logo p { font-size:13px; color:var(--muted); margin-top:4px; }
.login-tabs { display:grid; grid-template-columns:1fr 1fr 1fr; gap:6px; margin-bottom:24px; }
.login-tab {
  flex:1; padding:10px; border-radius:10px; cursor:pointer; text-align:center;
  font-size:13px; font-weight:700; transition:all 0.2s;
  background:rgba(255,255,255,0.04); border:1px solid var(--border); color:var(--muted);
  font-family:'Tajawal',sans-serif;
}
.login-tab.active {
  background:var(--gold-dim); border-color:var(--gold); color:var(--gold-light);
}
.login-error {
  background:rgba(239,68,68,0.12); border:1px solid rgba(239,68,68,0.3);
  color:#fca5a5; padding:12px 16px; border-radius:10px; font-size:13px;
  margin-bottom:16px; display:none;
}
.login-success-msg {
  text-align:center; padding:20px;
  background:rgba(16,185,129,0.08); border:1px solid rgba(16,185,129,0.2);
  border-radius:12px; margin-bottom:20px; display:none;
}
.success-ref { font-size:26px; font-weight:900; color:var(--gold-light); font-family:monospace; letter-spacing:2px; margin:10px 0; }

/* ── Toast ── */
.toast {
  position:fixed; top:24px; left:24px; z-index:9999;
  background:rgba(16,185,129,0.15); border:1px solid rgba(16,185,129,0.3);
  color:#34d399; padding:14px 20px; border-radius:12px;
  font-size:13px; font-weight:700; display:none;
  backdrop-filter:blur(10px);
  animation: slideIn 0.3s ease;
}
@keyframes slideIn {
  from { opacity:0; transform:translateX(-20px); }
  to   { opacity:1; transform:translateX(0); }
}

/* ── Tabs (mobile nav) ── */
.mobile-header {
  display:none; align-items:center; justify-content:space-between;
  padding:16px 20px; background:rgba(13,27,42,0.95);
  border-bottom:1px solid var(--border); position:sticky; top:0; z-index:50;
}

/* ── Scrollbar ── */
::-webkit-scrollbar { width:6px; height:6px; }
::-webkit-scrollbar-track { background:transparent; }
::-webkit-scrollbar-thumb { background:rgba(212,175,55,0.3); border-radius:3px; }

/* ── Download btn ── */
.dl-btn {
  display:none; align-items:center; gap:8px;
  padding:10px 18px; border-radius:10px;
  background:var(--gold-dim); border:1px solid var(--border);
  color:var(--gold); font-size:13px; font-weight:700; cursor:pointer;
  font-family:'Tajawal',sans-serif; transition:all 0.2s;
}
.dl-btn:hover { background:rgba(212,175,55,0.25); }
.dl-badge { background:var(--gold); color:var(--dark); border-radius:20px; padding:2px 7px; font-size:11px; font-weight:900; }

/* ── Responsive ── */
@media (max-width:768px) {
  /* Sidebar */
  .sidebar {
    transform: translateX(110%);
    width: 280px;
    z-index: 200;
    transition: transform 0.3s cubic-bezier(.4,0,.2,1);
  }
  .sidebar.open { transform: translateX(0); }

  /* Overlay behind sidebar */
  .sidebar-overlay {
    display: none;
    position: fixed; inset: 0; z-index: 199;
    background: rgba(0,0,0,0.6);
    backdrop-filter: blur(2px);
  }
  .sidebar-overlay.show { display: block; }

  /* Main */
  .main { margin-right: 0; padding: 12px; padding-bottom: 80px; }
  .mobile-header { display: flex; }
  .sidebar-toggle { display: none; }

  /* Charts & grids */
  .charts-grid { grid-template-columns: 1fr; }
  .form-grid { grid-template-columns: 1fr; }
  .v-details { grid-template-columns: 1fr; }
  .stats-row { grid-template-columns: 1fr 1fr; }

  /* Violation card actions — stack on mobile */
  .v-actions {
    flex-direction: column;
    gap: 10px;
  }
  .v-notes-input {
    width: 100%;
    font-size: 14px;
    padding: 12px;
  }
  .v-actions .btn-sm {
    width: 100%;
    padding: 13px;
    font-size: 14px;
    border-radius: 10px;
    text-align: center;
  }
  .btn-approve, .btn-reject, .btn-pdf {
    display: block;
    width: 100%;
  }

  /* Page header */
  .page-header { flex-direction: column; align-items: flex-start; gap: 10px; }
  .page-title { font-size: 20px; }

  /* Login */
  .login-card { padding: 28px 20px; border-radius: 18px; }
  .login-logo h1 { font-size: 20px; }

  /* Bottom nav bar */
  .mobile-bottom-nav {
    display: flex !important;
  }

  /* Report period buttons */
  .report-period-btns { flex-wrap: wrap; }
  .btn-period { flex: 1; min-width: 80px; padding: 10px 8px; font-size: 12px; }

  /* Filter bar */
  /* filter chips are already responsive */
  .filter-group { min-width: 100%; }
}

/* Mobile header bar */
.mobile-header {
  display: none;
  align-items: center;
  justify-content: space-between;
  padding: 14px 16px;
  background: rgba(13,27,42,0.98);
  border-bottom: 1px solid var(--border);
  position: sticky; top: 0; z-index: 50;
  backdrop-filter: blur(12px);
}
.mobile-header-title {
  font-size: 15px; font-weight: 900; color: var(--gold-light);
  display: flex; align-items: center; gap: 8px;
}
.hamburger-btn {
  width: 42px; height: 42px; border-radius: 10px;
  background: var(--gold-dim); border: 1px solid var(--border);
  color: var(--gold); font-size: 20px; cursor: pointer;
  display: flex; align-items: center; justify-content: center;
  transition: background 0.2s;
}
.hamburger-btn:hover { background: rgba(212,175,55,0.25); }

/* Bottom nav bar for mobile */
.mobile-bottom-nav {
  display: none;
  position: fixed; bottom: 0; left: 0; right: 0; z-index: 150;
  background: rgba(13,27,42,0.98);
  border-top: 1px solid var(--border);
  backdrop-filter: blur(16px);
  padding: 6px 0 env(safe-area-inset-bottom, 6px);
  justify-content: space-around;
  align-items: center;
}
.bottom-nav-item {
  display: flex; flex-direction: column; align-items: center; gap: 3px;
  padding: 6px 12px; border-radius: 10px; cursor: pointer;
  color: var(--muted); font-size: 10px; font-weight: 700;
  transition: all 0.2s; min-width: 48px; text-align: center;
  border: none; background: transparent;
}
.bottom-nav-item .bn-icon { font-size: 28px; line-height: 1; }
.bottom-nav-item { font-size: 11px; padding: 8px 10px; }
.bottom-nav-item.active { color: var(--gold-light); }
.bottom-nav-item.active .bn-icon {
  background: var(--gold-dim);
  border-radius: 10px; padding: 5px 7px;
}
.bottom-nav-badge {
  position: absolute; top: 2px; right: 2px;
  background: var(--red); color: white;
  border-radius: 10px; padding: 1px 5px; font-size: 9px; font-weight: 900;
}
.bottom-nav-wrap { position: relative; }
.hide { display:none !important; }

/* ── Image Zoom Modal ── */
.img-zoom-overlay {
  display:none; position:fixed; inset:0; z-index:9999;
  background:rgba(0,0,0,0.92); justify-content:center; align-items:center;
  cursor:zoom-out; backdrop-filter:blur(8px);
}
.img-zoom-overlay.show { display:flex; }
.img-zoom-overlay img {
  max-width:95vw; max-height:90vh; object-fit:contain;
  border-radius:8px; border:2px solid var(--gold);
  box-shadow:0 0 40px rgba(212,175,55,0.3);
}
.img-zoom-close {
  position:absolute; top:16px; right:16px;
  background:rgba(239,68,68,0.8); color:white; border:none;
  width:40px; height:40px; border-radius:50%; font-size:20px;
  cursor:pointer; display:flex; align-items:center; justify-content:center;
}

/* ── Advanced Filter ── */
.filter-bar {
  background:var(--card); border:1px solid var(--border); border-radius:var(--radius);
  padding:14px 16px; margin-bottom:20px;
}
.filter-search-row {
  margin-bottom:10px;
}
.filter-search-row .filter-input {
  width:100%; padding:10px 14px; border-radius:10px;
  background:rgba(255,255,255,0.06); border:1px solid var(--border);
  color:var(--text); font-size:13px; font-family:'Tajawal',sans-serif;
}
.filter-search-row .filter-input:focus { outline:none; border-color:var(--gold); }
.filter-chips {
  display:flex; flex-wrap:wrap; gap:8px; align-items:center;
  direction:rtl;
}
.filter-chip {
  display:flex; align-items:center; gap:4px;
  background:rgba(255,255,255,0.05); border:1px solid var(--border);
  border-radius:20px; padding:4px 6px 4px 4px;
  font-size:12px; font-weight:600; color:var(--text);
  transition:all 0.2s;
}
.filter-chip:hover { border-color:rgba(212,175,55,0.4); }
.filter-chip .fc-icon { font-size:14px; }
.filter-chip select {
  background:transparent; border:none; color:var(--text);
  font-size:11px; font-weight:700; font-family:'Tajawal',sans-serif;
  cursor:pointer; outline:none; padding:2px 2px 2px 0;
  -webkit-appearance:none; appearance:none;
  max-width:90px;
}
.filter-chip select option { background:#1a2744; color:#e2e8f0; }
.filter-chip.active { background:var(--gold-dim); border-color:var(--gold); }
.filter-chip.active select { color:var(--gold-light); }
.btn-filter-reset-chip {
  display:flex; align-items:center; gap:3px;
  background:rgba(255,255,255,0.04); border:1px solid var(--border);
  border-radius:20px; padding:5px 10px;
  font-size:11px; font-weight:700; color:var(--muted);
  cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s;
}
.btn-filter-reset-chip:hover { background:rgba(239,68,68,0.1); border-color:rgba(239,68,68,0.3); color:#fca5a5; }
body.light-mode .filter-chip { background:rgba(0,0,0,0.03); border-color:rgba(180,145,20,0.2); }
body.light-mode .filter-chip select { color:#1a2744; }
body.light-mode .filter-chip.active { background:rgba(212,175,55,0.12); }
body.light-mode .filter-chip.active select { color:#6b5010; }
body.light-mode .filter-search-row .filter-input { background:rgba(0,0,0,0.04); border-color:rgba(180,145,20,0.25); color:#1a2744; }
.filter-group { display:none; }
.filter-label { display:none; }
.filter-select, .filter-input {
  padding:9px 12px; background:rgba(255,255,255,0.06); border:1px solid var(--border);
  border-radius:8px; color:var(--text); font-size:12px; font-family:'Tajawal',sans-serif;
}
.filter-select:focus, .filter-input:focus { outline:none; border-color:var(--gold); }
.filter-select option { background:#1a2744; }
.filter-actions { display:none; }
.btn-filter { padding:9px 16px; border-radius:8px; font-size:12px; font-weight:700;
  cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s; border:none; }
.btn-filter-apply { background:linear-gradient(135deg,var(--gold),var(--gold-light)); color:var(--dark); }
.btn-filter-reset { background:rgba(255,255,255,0.07); border:1px solid var(--border); color:var(--muted); border:none; }
.filter-result-count { font-size:12px; color:var(--muted); padding:4px 0; }

/* ── WhatsApp notification ── */
.wa-settings { background:rgba(37,211,102,0.08); border:1px solid rgba(37,211,102,0.25);
  border-radius:12px; padding:16px 20px; margin-bottom:16px; }
.wa-settings h4 { color:#25d366; font-size:14px; margin-bottom:12px; }
.wa-toggle { display:flex; align-items:center; gap:10px; margin-bottom:10px; }
.toggle-switch { position:relative; width:44px; height:24px; }
.toggle-switch input { opacity:0; width:0; height:0; }
.toggle-slider {
  position:absolute; cursor:pointer; inset:0; background:rgba(255,255,255,0.1);
  border-radius:24px; transition:.3s;
}
.toggle-slider:before {
  position:absolute; content:""; height:18px; width:18px; left:3px; bottom:3px;
  background:white; border-radius:50%; transition:.3s;
}
input:checked + .toggle-slider { background:#25d366; }
input:checked + .toggle-slider:before { transform:translateX(20px); }
.wa-number-row { display:flex; gap:8px; }
.wa-number-input { flex:1; }

/* ── Reports Tab ── */
.report-grid { display:grid; grid-template-columns:1fr 1fr; gap:16px; margin-bottom:20px; }
.report-period-btns { display:flex; gap:8px; margin-bottom:20px; }
.btn-period {
  padding:10px 20px; border-radius:10px; font-size:13px; font-weight:700;
  cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s;
  background:rgba(255,255,255,0.05); border:1px solid var(--border); color:var(--muted);
}
.btn-period.active { background:var(--gold-dim); border-color:var(--gold); color:var(--gold-light); }
.report-stat { background:var(--card); border:1px solid var(--border); border-radius:12px; padding:20px; text-align:center; }
.report-stat-num { font-size:36px; font-weight:900; color:var(--gold-light); }
.report-stat-label { font-size:12px; color:var(--muted); margin-top:4px; }
.report-table { width:100%; border-collapse:collapse; font-size:13px; }
.report-table th { padding:10px 14px; background:rgba(212,175,55,0.12); color:var(--gold); text-align:right; border:1px solid rgba(212,175,55,0.2); }
.report-table td { padding:10px 14px; border:1px solid rgba(255,255,255,0.05); color:var(--text); }
.report-table tr:nth-child(even) td { background:rgba(255,255,255,0.02); }
.btn-dl-report {
  display:inline-flex; align-items:center; gap:8px; padding:11px 20px; border-radius:10px;
  background:var(--gold-dim); border:1px solid var(--border); color:var(--gold);
  font-size:13px; font-weight:700; cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s;
}
.btn-dl-report:hover { background:rgba(212,175,55,0.25); }
/* ── Repeat Offender Badges ── */
.repeat-badge {
  display:inline-flex; align-items:center; gap:5px;
  padding:3px 10px; border-radius:20px; font-size:11px; font-weight:800;
  margin-left:6px;
}
.repeat-badge.plate { background:rgba(239,68,68,0.15); border:1px solid rgba(239,68,68,0.4); color:#f87171; }
.repeat-badge.id    { background:rgba(245,158,11,0.15); border:1px solid rgba(245,158,11,0.4); color:#fbbf24; }
.repeat-badge.co    { background:rgba(168,85,247,0.15); border:1px solid rgba(168,85,247,0.4); color:#c084fc; }

.repeat-warning {
  margin: 8px 0;
  padding: 10px 14px;
  border-radius: 10px;
  background: rgba(239,68,68,0.08);
  border: 1px solid rgba(239,68,68,0.3);
  font-size: 12px;
  color: #fca5a5;
  display: flex; flex-direction:column; gap:4px;
}
.repeat-warning .rw-title { font-weight:900; font-size:13px; color:#f87171; margin-bottom:4px; }
.repeat-warning .rw-item  { color:#e2e8f0; }
.repeat-warning .rw-item span { color:#fbbf24; font-weight:700; }

.repeat-form-alert {
  padding:10px 14px; border-radius:10px; margin-top:8px;
  background:rgba(239,68,68,0.1); border:1px solid rgba(239,68,68,0.35);
  color:#f87171; font-size:12px; font-weight:700; display:none;
}

/* Admin repeat table */
.repeat-count-badge {
  display:inline-block; background:rgba(239,68,68,0.2); border:1px solid rgba(239,68,68,0.5);
  color:#f87171; border-radius:20px; padding:2px 10px; font-size:12px; font-weight:900;
}
@media (max-width:768px) {
  .report-grid { grid-template-columns:1fr; }
  .filter-bar { flex-direction:column; }
}

/* ── Light mode extra text fixes ── */
body.light-mode { color: #1a2744; }
body.light-mode .page-title { color: #1a2744; }
body.light-mode .page-subtitle { color: #4a6080; }
body.light-mode .stat-value { color: #1a2744; }
body.light-mode .stat-label { color: #4a6080; }
body.light-mode .user-name { color: #1a2744; }
body.light-mode .user-role { color: #4a6080; }
body.light-mode .v-ref { color: #8b6914; }
body.light-mode .form-label { color: #6b5010; font-weight: 700; }
body.light-mode .card-header h3 { color: #1a2744; }
body.light-mode h3, body.light-mode h4 { color: #1a2744; }
body.light-mode strong { color: #6b5010; }
body.light-mode .top-name { color: #1a2744; }
body.light-mode .top-count { color: #8b6914; }
body.light-mode .top-rank { color: #8b6914; border-color: rgba(139,105,20,0.3); }
body.light-mode .filter-input::placeholder { color: rgba(30,50,90,0.4); }
body.light-mode .company-search-input::placeholder { color: rgba(30,50,90,0.4); }
body.light-mode .report-table td { color: #1a2744; }
body.light-mode .report-table th { color: #6b5010; }
body.light-mode .dl-btn { color: #6b5010; border-color: rgba(139,105,20,0.3); background: rgba(212,175,55,0.1); }
body.light-mode .btn-logout { color: #c0392b; }
body.light-mode .sidebar-logo h2 { color: #8b6914; }
body.light-mode .sidebar-logo p { color: #4a6080; }
body.light-mode .login-card h1 { color: #1a2744; }
body.light-mode .login-card p { color: #4a6080; }
body.light-mode .login-tab { color: #4a6080; }
body.light-mode .login-tab.active { color: #6b5010; background: rgba(212,175,55,0.15); border-color: var(--gold); }
body.light-mode .mobile-header-title { color: #8b6914; }
body.light-mode .hamburger-btn { color: #8b6914; }
body.light-mode .bottom-nav-item { color: rgba(30,50,90,0.55); }
body.light-mode .bottom-nav-item.active { color: #8b6914; }
body.light-mode .v-card-header { background: rgba(212,175,55,0.06); }
body.light-mode .v-status.pending { background: rgba(245,158,11,0.15); color: #b45309; border-color: rgba(245,158,11,0.4); }
body.light-mode .v-status.approved { background: rgba(16,185,129,0.12); color: #065f46; border-color: rgba(16,185,129,0.4); }
body.light-mode .v-status.rejected { background: rgba(239,68,68,0.1); color: #991b1b; border-color: rgba(239,68,68,0.3); }
body.light-mode .repeat-badge.plate { background: rgba(239,68,68,0.12); color: #991b1b; }
body.light-mode .repeat-badge.id { background: rgba(245,158,11,0.12); color: #92400e; }
body.light-mode .repeat-warning { background: rgba(239,68,68,0.06); border-color: rgba(239,68,68,0.25); }
body.light-mode .repeat-warning .rw-title { color: #991b1b; }
body.light-mode .repeat-warning .rw-item { color: #1a2744; }
body.light-mode .img-preview-grid img { border-color: rgba(180,145,20,0.3); }
body.light-mode .upload-btn { color: #6b5010; }
body.light-mode .plate-preview { background: rgba(212,175,55,0.12); border-color: rgba(180,145,20,0.3); color: #6b5010; }
body.light-mode .plate-input { color: #1a2744; }
body.light-mode .location-result { color: #1a2744; }
body.light-mode .success-msg { background: rgba(16,185,129,0.1); border-color: rgba(16,185,129,0.3); color: #065f46; }
body.light-mode .theme-pill { background: rgba(255,255,255,0.95); color: #6b5010; box-shadow: 0 4px 20px rgba(0,0,0,0.12); }
body.light-mode .toast { color: #1a2744; }

/* ── Login tab 3 columns ── */

/* ═══ Security Check Modal ═══ */
.security-overlay {
  position:fixed; inset:0; z-index:10000;
  background:rgba(0,0,0,0.75); backdrop-filter:blur(8px);
  display:flex; align-items:center; justify-content:center;
  animation: fadeIn 0.3s ease;
}
@keyframes fadeIn { from{opacity:0} to{opacity:1} }
.security-modal {
  background:var(--card); border:2px solid var(--gold);
  border-radius:20px; padding:28px 32px; max-width:520px; width:90%;
  box-shadow:0 40px 80px rgba(0,0,0,0.6);
}
.security-modal h3 { color:var(--gold-light); font-size:18px; font-weight:900; margin-bottom:16px; text-align:center; }
.scan-result {
  padding:14px 18px; border-radius:12px; margin:12px 0; font-size:13px; font-weight:700;
}
.scan-result.clear { background:rgba(16,185,129,0.12); border:1px solid rgba(16,185,129,0.3); color:#34d399; }
.scan-result.blocked { background:rgba(239,68,68,0.15); border:1px solid rgba(239,68,68,0.4); color:#f87171; }
.scan-result.warning { background:rgba(245,158,11,0.12); border:1px solid rgba(245,158,11,0.3); color:#fbbf24; }
.scan-progress {
  height:4px; background:rgba(255,255,255,0.1); border-radius:4px; overflow:hidden; margin:16px 0;
}
.scan-progress-bar {
  height:100%; background:linear-gradient(90deg,var(--gold),var(--gold-light));
  border-radius:4px; transition:width 0.6s ease;
}
.scan-item { display:flex; align-items:center; gap:10px; padding:8px 0; font-size:13px; color:var(--text); }
.scan-icon { font-size:18px; width:24px; text-align:center; }
.scan-status { margin-right:auto; font-weight:700; font-size:12px; }
.scan-status.ok { color:#34d399; }
.scan-status.alert { color:#f87171; }
.scan-status.pending { color:var(--muted); }
.security-actions { display:flex; gap:10px; margin-top:16px; }
.security-actions button { flex:1; padding:12px; border-radius:10px; font-size:14px; font-weight:700; cursor:pointer; font-family:'Tajawal',sans-serif; }
.btn-security-proceed { background:linear-gradient(135deg,var(--gold),var(--gold-light)); border:none; color:var(--dark); }
.btn-security-cancel { background:rgba(239,68,68,0.1); border:1px solid rgba(239,68,68,0.3); color:#fca5a5; }
body.light-mode .security-modal { background:rgba(255,255,255,0.95); border-color:var(--gold); }
body.light-mode .scan-item { color:#1a2744; }

/* ═══ Email notification styles ═══ */
.email-settings { background:rgba(59,130,246,0.08); border:1px solid rgba(59,130,246,0.25);
  border-radius:12px; padding:16px 20px; margin-bottom:16px; }
.email-settings h4 { color:#60a5fa; font-size:14px; margin-bottom:12px; }
body.light-mode .email-settings { background:rgba(59,130,246,0.06); border-color:rgba(59,130,246,0.2); }

/* ══════════════════════════════════════════
   🔔 NOTIFICATION CENTER
   ══════════════════════════════════════════ */
.notif-panel {
  position:fixed; top:0; left:0; bottom:0; width:380px; z-index:10001;
  background:rgba(6,13,26,0.98); border-right:1px solid var(--border);
  backdrop-filter:blur(24px); transform:translateX(-110%);
  transition:transform 0.35s cubic-bezier(.4,0,.2,1);
  display:flex; flex-direction:column;
  box-shadow:10px 0 40px rgba(0,0,0,0.5);
}
.notif-panel.open { transform:translateX(0); }
body.light-mode .notif-panel { background:rgba(248,250,255,0.98); box-shadow:10px 0 40px rgba(0,0,0,0.1); }
.notif-header { padding:20px 24px; border-bottom:1px solid var(--border); display:flex; justify-content:space-between; align-items:center; }
.notif-header h3 { font-size:17px; font-weight:900; color:var(--gold-light); }
body.light-mode .notif-header h3 { color:#6b5010; }
.notif-close { width:36px; height:36px; border-radius:10px; border:1px solid var(--border); background:rgba(239,68,68,0.1); color:#f87171; font-size:18px; cursor:pointer; display:flex; align-items:center; justify-content:center; font-family:'Tajawal',sans-serif; }
.notif-body { flex:1; overflow-y:auto; padding:16px; }
.notif-item { padding:14px 16px; border-radius:12px; margin-bottom:10px; background:rgba(255,255,255,0.03); border:1px solid var(--border); cursor:pointer; transition:all 0.25s; animation:notifSlideIn 0.3s ease both; }
body.light-mode .notif-item { background:rgba(0,0,0,0.02); }
.notif-item:hover { border-color:rgba(212,175,55,0.4); transform:translateX(4px); }
.notif-item.unread { border-right:3px solid var(--gold); background:rgba(212,175,55,0.05); }
.notif-item .ni-top { display:flex; align-items:center; gap:10px; margin-bottom:6px; }
.notif-item .ni-icon { font-size:22px; }
.notif-item .ni-title { font-size:13px; font-weight:700; color:var(--text); flex:1; }
body.light-mode .notif-item .ni-title { color:#1a2744; }
.notif-item .ni-time { font-size:10px; color:var(--muted); }
.notif-item .ni-desc { font-size:12px; color:var(--muted); line-height:1.5; padding-right:32px; }
.notif-badge-float { position:absolute; top:-4px; right:-4px; background:#ef4444; color:white; border-radius:50%; min-width:18px; height:18px; font-size:10px; font-weight:900; display:flex; align-items:center; justify-content:center; border:2px solid var(--dark); animation:notifPulse 2s infinite; }
body.light-mode .notif-badge-float { border-color:#f0f4f8; }
@keyframes notifSlideIn { from{opacity:0;transform:translateX(-20px)} to{opacity:1;transform:translateX(0)} }
@keyframes notifPulse { 0%,100%{transform:scale(1)} 50%{transform:scale(1.15)} }
.notif-overlay { position:fixed; inset:0; z-index:10000; background:rgba(0,0,0,0.5); backdrop-filter:blur(2px); display:none; }
.notif-overlay.show { display:block; }
.notif-empty { text-align:center; padding:40px 20px; color:var(--muted); }
.notif-empty .ne-icon { font-size:48px; margin-bottom:12px; opacity:0.4; }
.notif-footer { padding:12px; border-top:1px solid var(--border); }
.notif-clear-btn { width:100%; padding:10px; border-radius:10px; border:1px solid rgba(239,68,68,0.2); background:rgba(239,68,68,0.08); color:#fca5a5; font-size:12px; font-weight:700; cursor:pointer; font-family:'Tajawal',sans-serif; transition:all 0.2s; }
.notif-clear-btn:hover { background:rgba(239,68,68,0.15); }
body.light-mode .notif-clear-btn { color:#991b1b; }
@media(max-width:768px) { .notif-panel { width:100%; } }

/* ══════════════════════════════════════════
   📊 KPI & TIMELINE ENHANCEMENTS
   ══════════════════════════════════════════ */
.kpi-row { display:grid; grid-template-columns:repeat(auto-fit, minmax(200px,1fr)); gap:16px; margin-bottom:24px; }
.kpi-card { background:var(--card); border:1px solid var(--border); border-radius:16px; padding:20px; position:relative; overflow:hidden; transition:transform 0.25s, box-shadow 0.25s; }
.kpi-card:hover { transform:translateY(-3px); box-shadow:0 12px 40px rgba(0,0,0,0.25); }
.kpi-card::after { content:''; position:absolute; bottom:0; left:0; right:0; height:3px; background:linear-gradient(90deg, var(--kpi-color,var(--gold)), transparent); }
.kpi-card .kpi-icon { font-size:28px; margin-bottom:10px; }
.kpi-card .kpi-value { font-size:32px; font-weight:900; line-height:1; margin-bottom:4px; color:var(--kpi-color,var(--gold-light)); }
.kpi-card .kpi-label { font-size:12px; color:var(--muted); font-weight:500; }
.kpi-card .kpi-trend { position:absolute; top:16px; left:16px; font-size:11px; font-weight:700; padding:3px 8px; border-radius:20px; }
.kpi-trend.up { background:rgba(16,185,129,0.15); color:#34d399; }
.kpi-trend.down { background:rgba(239,68,68,0.12); color:#f87171; }
.kpi-trend.neutral { background:rgba(245,158,11,0.12); color:#fbbf24; }
body.light-mode .kpi-card { background:rgba(255,255,255,0.92); border-color:rgba(180,145,20,0.2); box-shadow:0 4px 15px rgba(0,0,0,0.06); }

/* Timeline */
.timeline-section { margin-top:24px; }
.timeline-section .tl-header { display:flex; align-items:center; gap:10px; margin-bottom:16px; }
.timeline-section .tl-header h3 { font-size:16px; font-weight:700; color:var(--text); }
body.light-mode .timeline-section .tl-header h3 { color:#1a2744; }
.timeline-wrap { position:relative; padding:0 0 0 28px; max-height:400px; overflow-y:auto; }
.timeline-wrap::before { content:''; position:absolute; left:10px; top:0; bottom:0; width:2px; background:linear-gradient(180deg, var(--gold), rgba(212,175,55,0.08)); border-radius:2px; }
.tl-item { position:relative; padding:12px 16px; margin-bottom:14px; background:var(--card); border:1px solid var(--border); border-radius:12px; animation:fadeSlideUp 0.4s ease both; }
.tl-item::before { content:''; position:absolute; right:calc(100% + 12px); top:18px; width:10px; height:10px; border-radius:50%; background:var(--gold); border:2px solid var(--dark); box-shadow:0 0 8px rgba(212,175,55,0.3); }
body.light-mode .tl-item { background:rgba(255,255,255,0.92); border-color:rgba(180,145,20,0.2); }
body.light-mode .tl-item::before { border-color:#f0f4f8; }
.tl-item .tl-time { font-size:10px; color:var(--muted); margin-bottom:4px; }
.tl-item .tl-text { font-size:13px; color:var(--text); }
body.light-mode .tl-item .tl-text { color:#1a2744; }
.tl-item .tl-ref { color:var(--gold); font-weight:700; font-family:monospace; }
@keyframes fadeSlideUp { from{opacity:0;transform:translateY(12px)} to{opacity:1;transform:translateY(0)} }

/* ══════════════════════════════════════════
   🔍 ADVANCED SEARCH ENHANCEMENTS
   ══════════════════════════════════════════ */
.adv-search-wrap { position:relative; margin-bottom:12px; }
.adv-search-wrap .adv-icon { position:absolute; top:50%; transform:translateY(-50%); right:16px; font-size:18px; color:var(--muted); pointer-events:none; }
.adv-search-wrap input {
  width:100%; padding:14px 50px 14px 16px;
  background:rgba(255,255,255,0.06); border:1px solid var(--border); border-radius:12px;
  color:var(--text); font-size:15px; font-family:'Tajawal',sans-serif; font-weight:500;
  transition:border-color 0.2s, box-shadow 0.2s;
}
.adv-search-wrap input:focus { outline:none; border-color:var(--gold); box-shadow:0 0 0 4px rgba(212,175,55,0.1); }
body.light-mode .adv-search-wrap input { background:rgba(0,0,0,0.03); color:#1a2744; }
.adv-date-row { display:flex; align-items:center; gap:8px; flex-wrap:wrap; }
.adv-date-input { padding:7px 12px; border-radius:8px; border:1px solid var(--border); background:rgba(255,255,255,0.06); color:var(--text); font-size:12px; font-family:'Tajawal',sans-serif; }
.adv-date-input:focus { outline:none; border-color:var(--gold); }
body.light-mode .adv-date-input { background:rgba(0,0,0,0.03); color:#1a2744; }
.search-highlight { background:rgba(212,175,55,0.35); border-radius:2px; padding:0 2px; }

/* ══════════════════════════════════════════
   ✨ ANIMATIONS
   ══════════════════════════════════════════ */
.stat-card { animation:statPop 0.5s cubic-bezier(.34,1.56,.64,1) both; }
.stat-card:nth-child(1){animation-delay:.05s}.stat-card:nth-child(2){animation-delay:.1s}
.stat-card:nth-child(3){animation-delay:.15s}.stat-card:nth-child(4){animation-delay:.2s}
.stat-card:nth-child(5){animation-delay:.25s}.stat-card:nth-child(6){animation-delay:.3s}
@keyframes statPop { from{opacity:0;transform:scale(0.9) translateY(10px)} to{opacity:1;transform:scale(1) translateY(0)} }
.v-card { animation:cardSlide 0.35s ease both; }
.v-card:nth-child(1){animation-delay:.03s}.v-card:nth-child(2){animation-delay:.06s}
.v-card:nth-child(3){animation-delay:.09s}.v-card:nth-child(4){animation-delay:.12s}
@keyframes cardSlide { from{opacity:0;transform:translateY(16px)} to{opacity:1;transform:translateY(0)} }
.btn-primary { transition:all 0.25s cubic-bezier(.4,0,.2,1); }
[id$="Tab"]:not(.hide) { animation:pageFade 0.3s ease; }
@keyframes pageFade { from{opacity:0} to{opacity:1} }
.toast { animation:toastIn 0.4s cubic-bezier(.34,1.56,.64,1); }
@keyframes toastIn { from{opacity:0;transform:translateX(-30px) scale(0.9)} to{opacity:1;transform:translateX(0) scale(1)} }
html { scroll-behavior:smooth; }

/* ══════════════════════════════════════════
   📱 MOBILE ENHANCEMENTS
   ══════════════════════════════════════════ */
@media(max-width:768px) {
  .btn-sm { min-height:44px; }
  .form-input, .form-select { min-height:48px; font-size:16px; }
  .kpi-row { grid-template-columns:1fr 1fr; }
  .timeline-wrap { padding-left:22px; }
}

/* ══════════════════════════════════════════
   🏢 COMPANY EMAIL TABLE
   ══════════════════════════════════════════ */
#companyEmailList table tr:hover { background:rgba(168,85,247,0.05); }
#companyEmailList table input:focus { border-color:#a78bfa; box-shadow:0 0 0 3px rgba(168,85,247,0.15); }
#emailSendLog > div:hover { background:rgba(255,255,255,0.04); }
body.light-mode #companyEmailList table tr:hover { background:rgba(168,85,247,0.04); }
body.light-mode #companyEmailList table input { background:rgba(0,0,0,0.03); color:#1a2744; }

/* ══════════════════════════════════════════════════════════════
   🔮 GLASS MORPHISM & PREMIUM DARK REDESIGN — V4
   ══════════════════════════════════════════════════════════════ */

/* === Enhanced Background with Animated Orbs === */
body { background: #030812; }

body::before {
  content: '';
  position: fixed; inset: 0; z-index: 0; pointer-events: none;
  background:
    radial-gradient(ellipse 70% 55% at 15% 20%, rgba(212,175,55,0.10) 0%, transparent 55%),
    radial-gradient(ellipse 55% 45% at 85% 15%, rgba(59,130,246,0.07) 0%, transparent 50%),
    radial-gradient(ellipse 75% 65% at 50% 85%, rgba(168,85,247,0.06) 0%, transparent 60%),
    radial-gradient(ellipse 45% 35% at 70% 55%, rgba(16,185,129,0.05) 0%, transparent 50%),
    linear-gradient(180deg, #030812 0%, #060d1a 50%, #0a1628 100%);
  animation: bgOrbs 25s ease-in-out infinite alternate;
}

@keyframes bgOrbs {
  0%   { opacity: 1; filter: hue-rotate(0deg); }
  50%  { opacity: 0.9; filter: hue-rotate(8deg); }
  100% { opacity: 1; filter: hue-rotate(0deg); }
}

/* Floating glow orb top-right */
body::after {
  content: '';
  position: fixed;
  width: 700px; height: 700px; border-radius: 50%;
  background: radial-gradient(circle, rgba(212,175,55,0.05) 0%, transparent 70%);
  top: -280px; right: -280px;
  z-index: 0; pointer-events: none;
  animation: floatOrb1 28s ease-in-out infinite;
}

@keyframes floatOrb1 {
  0%,100% { transform: translate(0,0) scale(1); }
  33%      { transform: translate(-70px, 90px) scale(1.08); }
  66%      { transform: translate(50px, -60px) scale(0.95); }
}

/* === Glass Cards === */
.card {
  background: rgba(255,255,255,0.025) !important;
  backdrop-filter: blur(28px) saturate(160%) !important;
  -webkit-backdrop-filter: blur(28px) saturate(160%) !important;
  border: 1px solid rgba(212,175,55,0.13) !important;
  box-shadow:
    0 8px 32px rgba(0,0,0,0.45),
    inset 0 1px 0 rgba(255,255,255,0.06),
    inset 0 -1px 0 rgba(0,0,0,0.15) !important;
  transition: all 0.3s ease !important;
}
.card:hover {
  border-color: rgba(212,175,55,0.26) !important;
  box-shadow:
    0 16px 48px rgba(0,0,0,0.55),
    0 0 0 1px rgba(212,175,55,0.08),
    inset 0 1px 0 rgba(255,255,255,0.08) !important;
  transform: translateY(-1px);
}

.card-header {
  background: linear-gradient(135deg, rgba(255,255,255,0.03) 0%, transparent 100%) !important;
  border-bottom: 1px solid rgba(212,175,55,0.1) !important;
}

/* === Glass Stat Cards === */
.stat-card {
  backdrop-filter: blur(24px) saturate(150%) !important;
  -webkit-backdrop-filter: blur(24px) saturate(150%) !important;
  position: relative; overflow: hidden;
  transition: all 0.3s cubic-bezier(0.34,1.56,0.64,1) !important;
}
.stat-card::after {
  content: '';
  position: absolute; inset: 0;
  background: linear-gradient(135deg, rgba(255,255,255,0.05) 0%, transparent 50%);
  pointer-events: none;
}
.stat-card.gold {
  background: linear-gradient(135deg, rgba(212,175,55,0.14) 0%, rgba(212,175,55,0.04) 100%) !important;
  border-color: rgba(212,175,55,0.32) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.35), 0 0 24px rgba(212,175,55,0.09), inset 0 1px 0 rgba(212,175,55,0.18) !important;
}
.stat-card.green {
  background: linear-gradient(135deg, rgba(16,185,129,0.13) 0%, rgba(16,185,129,0.04) 100%) !important;
  border-color: rgba(16,185,129,0.26) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.35), 0 0 24px rgba(16,185,129,0.07), inset 0 1px 0 rgba(16,185,129,0.14) !important;
}
.stat-card.red {
  background: linear-gradient(135deg, rgba(239,68,68,0.13) 0%, rgba(239,68,68,0.04) 100%) !important;
  border-color: rgba(239,68,68,0.24) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.35), 0 0 24px rgba(239,68,68,0.06) !important;
}
.stat-card.amber {
  background: linear-gradient(135deg, rgba(245,158,11,0.13) 0%, rgba(245,158,11,0.04) 100%) !important;
  border-color: rgba(245,158,11,0.24) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.35), 0 0 24px rgba(245,158,11,0.06) !important;
}
.stat-card.blue {
  background: linear-gradient(135deg, rgba(59,130,246,0.13) 0%, rgba(59,130,246,0.04) 100%) !important;
  border-color: rgba(59,130,246,0.24) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.35), 0 0 24px rgba(59,130,246,0.06) !important;
}
.stat-card:hover {
  transform: translateY(-5px) !important;
  box-shadow: 0 20px 60px rgba(0,0,0,0.55), 0 0 32px rgba(212,175,55,0.12) !important;
}
.stat-card.gold .stat-icon  { box-shadow: 0 0 18px rgba(212,175,55,0.22) !important; }
.stat-card.green .stat-icon { box-shadow: 0 0 18px rgba(16,185,129,0.2) !important; }
.stat-card.red .stat-icon   { box-shadow: 0 0 18px rgba(239,68,68,0.16) !important; }
.stat-card.amber .stat-icon { box-shadow: 0 0 18px rgba(245,158,11,0.16) !important; }
.stat-card.blue .stat-icon  { box-shadow: 0 0 18px rgba(59,130,246,0.16) !important; }

/* === Glass KPI Cards === */
.kpi-card {
  background: rgba(255,255,255,0.025) !important;
  backdrop-filter: blur(24px) saturate(150%) !important;
  -webkit-backdrop-filter: blur(24px) saturate(150%) !important;
  border: 1px solid rgba(255,255,255,0.07) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.35), inset 0 1px 0 rgba(255,255,255,0.05) !important;
  transition: all 0.3s ease !important;
}
.kpi-card:hover {
  transform: translateY(-5px) !important;
  border-color: var(--kpi-color, rgba(212,175,55,0.25)) !important;
  box-shadow: 0 20px 60px rgba(0,0,0,0.45), 0 0 0 1px var(--kpi-color, rgba(212,175,55,0.15)), inset 0 1px 0 rgba(255,255,255,0.07) !important;
}

/* === Premium Sidebar === */
.sidebar {
  background: linear-gradient(180deg,
    rgba(5,11,22,0.94) 0%,
    rgba(3,8,18,0.96) 100%) !important;
  backdrop-filter: blur(40px) saturate(200%) !important;
  -webkit-backdrop-filter: blur(40px) saturate(200%) !important;
  border-left: 1px solid rgba(212,175,55,0.12) !important;
  box-shadow: -8px 0 48px rgba(0,0,0,0.7), inset -1px 0 0 rgba(255,255,255,0.025) !important;
}
.sidebar-logo {
  background: linear-gradient(135deg, rgba(212,175,55,0.07) 0%, transparent 60%);
  border-bottom-color: rgba(212,175,55,0.1) !important;
}
.nav-item {
  position: relative; overflow: hidden;
  transition: all 0.25s ease !important;
}
.nav-item::before {
  content: '';
  position: absolute; inset: 0;
  background: linear-gradient(90deg, rgba(212,175,55,0.1) 0%, transparent 100%);
  opacity: 0; transform: scaleX(0); transform-origin: right;
  transition: transform 0.3s ease, opacity 0.3s;
}
.nav-item:hover::before { opacity: 1; transform: scaleX(1); }
.nav-item.active {
  background: linear-gradient(135deg, rgba(212,175,55,0.14) 0%, rgba(212,175,55,0.04) 100%) !important;
  box-shadow: inset 3px 0 0 var(--gold), 0 4px 16px rgba(212,175,55,0.07) !important;
  animation: navGlow 3s ease-in-out infinite !important;
}
@keyframes navGlow {
  0%,100% { box-shadow: inset 3px 0 0 var(--gold), 0 0 10px rgba(212,175,55,0.08); }
  50%      { box-shadow: inset 3px 0 0 var(--gold), 0 0 20px rgba(212,175,55,0.18); }
}

/* === Animated Login Page === */
#loginPage {
  background:
    radial-gradient(ellipse 80% 60% at 20% 30%, rgba(212,175,55,0.09) 0%, transparent 60%),
    radial-gradient(ellipse 60% 50% at 80% 70%, rgba(59,130,246,0.07) 0%, transparent 60%),
    radial-gradient(ellipse 100% 80% at 50% 50%, rgba(168,85,247,0.05) 0%, transparent 70%),
    linear-gradient(135deg, #030812 0%, #060d1a 50%, #0a1628 100%) !important;
  position: relative; overflow: hidden;
}
#loginPage::before {
  content: '';
  position: absolute;
  width: 900px; height: 900px; border-radius: 50%;
  background: radial-gradient(circle, rgba(212,175,55,0.06) 0%, transparent 70%);
  top: -350px; right: -350px;
  animation: floatOrb1 22s ease-in-out infinite;
  pointer-events: none; z-index: 0;
}
#loginPage::after {
  content: '';
  position: absolute;
  width: 700px; height: 700px; border-radius: 50%;
  background: radial-gradient(circle, rgba(59,130,246,0.05) 0%, transparent 70%);
  bottom: -250px; left: -250px;
  animation: floatOrb1 28s ease-in-out infinite reverse;
  pointer-events: none; z-index: 0;
}

/* === Glass Login Card === */
.login-card {
  background: rgba(255,255,255,0.035) !important;
  backdrop-filter: blur(48px) saturate(200%) !important;
  -webkit-backdrop-filter: blur(48px) saturate(200%) !important;
  border: 1px solid rgba(212,175,55,0.18) !important;
  box-shadow:
    0 48px 96px rgba(0,0,0,0.65),
    0 0 0 1px rgba(255,255,255,0.04),
    inset 0 1px 0 rgba(255,255,255,0.08),
    0 0 80px rgba(212,175,55,0.07) !important;
  position: relative; z-index: 1;
  animation: loginCardIn 0.9s cubic-bezier(0.34,1.56,0.64,1) both !important;
}
@keyframes loginCardIn {
  from { opacity:0; transform:translateY(50px) scale(0.93); }
  to   { opacity:1; transform:translateY(0) scale(1); }
}
.login-logo .icon {
  background: linear-gradient(135deg, rgba(212,175,55,0.18) 0%, rgba(212,175,55,0.06) 100%) !important;
  border: 1px solid rgba(212,175,55,0.3) !important;
  box-shadow: 0 8px 32px rgba(212,175,55,0.18), inset 0 1px 0 rgba(255,255,255,0.12) !important;
}
.login-tab {
  background: rgba(255,255,255,0.03) !important;
  backdrop-filter: blur(10px) !important;
  border-color: rgba(255,255,255,0.07) !important;
  transition: all 0.22s ease !important;
}
.login-tab:hover { background: rgba(212,175,55,0.08) !important; border-color: rgba(212,175,55,0.2) !important; }
.login-tab.active {
  background: linear-gradient(135deg, rgba(212,175,55,0.2) 0%, rgba(212,175,55,0.07) 100%) !important;
  border-color: rgba(212,175,55,0.42) !important;
  box-shadow: 0 4px 18px rgba(212,175,55,0.14), inset 0 1px 0 rgba(255,255,255,0.1) !important;
}

/* === Premium Gold Button === */
.btn-primary {
  background: linear-gradient(135deg, #c49b20 0%, #f5d020 45%, #d4af37 100%) !important;
  box-shadow: 0 4px 24px rgba(212,175,55,0.45), 0 1px 0 rgba(255,255,255,0.3) inset !important;
  position: relative; overflow: hidden;
  transition: all 0.28s cubic-bezier(0.4,0,0.2,1) !important;
}
.btn-primary::before {
  content: '';
  position: absolute;
  top: 0; left: -100%; width: 100%; height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.25), transparent);
  transition: left 0.5s ease;
}
.btn-primary:hover::before { left: 100%; }
.btn-primary:hover {
  box-shadow: 0 10px 40px rgba(212,175,55,0.65), 0 0 0 3px rgba(212,175,55,0.18) !important;
  transform: translateY(-2px) !important;
}
.btn-primary:active { transform: translateY(0) !important; box-shadow: 0 4px 16px rgba(212,175,55,0.35) !important; }

/* === Glass Form Inputs === */
.form-input, .form-select, .form-textarea {
  background: rgba(255,255,255,0.04) !important;
  backdrop-filter: blur(12px) !important;
  border-color: rgba(255,255,255,0.09) !important;
  box-shadow: inset 0 2px 6px rgba(0,0,0,0.25) !important;
  transition: all 0.25s ease !important;
}
.form-input:focus, .form-select:focus, .form-textarea:focus {
  background: rgba(212,175,55,0.06) !important;
  border-color: rgba(212,175,55,0.5) !important;
  box-shadow: 0 0 0 4px rgba(212,175,55,0.1), inset 0 2px 6px rgba(0,0,0,0.2) !important;
}

/* === Glass Violation Cards === */
.v-card {
  background: rgba(255,255,255,0.02) !important;
  backdrop-filter: blur(20px) !important;
  -webkit-backdrop-filter: blur(20px) !important;
  border-color: rgba(255,255,255,0.07) !important;
  box-shadow: 0 4px 24px rgba(0,0,0,0.35), inset 0 1px 0 rgba(255,255,255,0.04) !important;
  transition: all 0.3s ease !important;
}
.v-card:hover {
  background: rgba(255,255,255,0.04) !important;
  border-color: rgba(212,175,55,0.28) !important;
  box-shadow: 0 14px 48px rgba(0,0,0,0.5), 0 0 0 1px rgba(212,175,55,0.1), inset 0 1px 0 rgba(255,255,255,0.06) !important;
  transform: translateY(-2px) !important;
}
.v-ref { text-shadow: 0 0 20px rgba(212,175,55,0.32) !important; }

/* === Glass Filter Bar === */
.filter-bar {
  background: rgba(255,255,255,0.02) !important;
  backdrop-filter: blur(24px) !important;
  -webkit-backdrop-filter: blur(24px) !important;
  border-color: rgba(255,255,255,0.07) !important;
  box-shadow: 0 4px 24px rgba(0,0,0,0.25), inset 0 1px 0 rgba(255,255,255,0.04) !important;
}

/* === Page Title Glow === */
.page-title { text-shadow: 0 0 32px rgba(212,175,55,0.22) !important; }

/* === Mobile Header Glass === */
.mobile-header {
  background: rgba(4,10,22,0.88) !important;
  backdrop-filter: blur(32px) saturate(200%) !important;
  -webkit-backdrop-filter: blur(32px) saturate(200%) !important;
  border-bottom: 1px solid rgba(212,175,55,0.12) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.4) !important;
}

/* === Mobile Bottom Nav Glass === */
.mobile-bottom-nav {
  background: rgba(4,10,22,0.88) !important;
  backdrop-filter: blur(32px) saturate(200%) !important;
  -webkit-backdrop-filter: blur(32px) saturate(200%) !important;
  border-top: 1px solid rgba(212,175,55,0.12) !important;
  box-shadow: 0 -8px 32px rgba(0,0,0,0.5) !important;
}

/* === Toast Glass === */
.toast {
  backdrop-filter: blur(24px) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.5) !important;
}

/* === Theme Pill Glass === */
.theme-pill {
  background: rgba(255,255,255,0.06) !important;
  backdrop-filter: blur(24px) !important;
  -webkit-backdrop-filter: blur(24px) !important;
  border-color: rgba(212,175,55,0.22) !important;
  box-shadow: 0 4px 20px rgba(0,0,0,0.4), inset 0 1px 0 rgba(255,255,255,0.07) !important;
}

/* === Notification Panel Glass === */
.notif-panel {
  background: rgba(4,10,22,0.92) !important;
  backdrop-filter: blur(40px) saturate(200%) !important;
  -webkit-backdrop-filter: blur(40px) saturate(200%) !important;
  border-right: 1px solid rgba(212,175,55,0.12) !important;
  box-shadow: 12px 0 64px rgba(0,0,0,0.75) !important;
}
.notif-item {
  background: rgba(255,255,255,0.025) !important;
  border-color: rgba(255,255,255,0.06) !important;
  transition: all 0.22s ease !important;
}
.notif-item:hover {
  background: rgba(255,255,255,0.045) !important;
  border-color: rgba(212,175,55,0.22) !important;
  box-shadow: 0 4px 18px rgba(0,0,0,0.35) !important;
}
.notif-item.unread {
  background: rgba(212,175,55,0.04) !important;
  border-right-color: var(--gold) !important;
}

/* === Security Modal Glass === */
.security-modal {
  background: rgba(8,16,32,0.88) !important;
  backdrop-filter: blur(48px) !important;
  -webkit-backdrop-filter: blur(48px) !important;
  box-shadow: 0 48px 96px rgba(0,0,0,0.75), 0 0 0 1px rgba(212,175,55,0.18), inset 0 1px 0 rgba(255,255,255,0.06) !important;
}

/* === Sidebar Toggle Glass === */
.sidebar-toggle {
  background: rgba(255,255,255,0.05) !important;
  backdrop-filter: blur(20px) !important;
  box-shadow: 0 4px 18px rgba(0,0,0,0.4) !important;
  transition: all 0.25s ease !important;
}
.sidebar-toggle:hover {
  background: rgba(212,175,55,0.12) !important;
  box-shadow: 0 6px 24px rgba(212,175,55,0.2) !important;
}

/* === Hamburger Glass === */
.hamburger-btn {
  background: rgba(255,255,255,0.06) !important;
  backdrop-filter: blur(12px) !important;
  transition: all 0.22s ease !important;
}
.hamburger-btn:hover { background: rgba(255,255,255,0.1) !important; }

/* === Logout Glass === */
.btn-logout {
  background: rgba(239,68,68,0.06) !important;
  backdrop-filter: blur(12px) !important;
  transition: all 0.25s ease !important;
}
.btn-logout:hover {
  background: rgba(239,68,68,0.16) !important;
  box-shadow: 0 4px 20px rgba(239,68,68,0.18) !important;
}

/* === Upload + Map Buttons Glass === */
.upload-btn {
  background: rgba(212,175,55,0.06) !important;
  backdrop-filter: blur(12px) !important;
  border-color: rgba(212,175,55,0.2) !important;
  transition: all 0.25s ease !important;
}
.upload-btn:hover {
  background: rgba(212,175,55,0.13) !important;
  border-color: rgba(212,175,55,0.42) !important;
  box-shadow: 0 4px 20px rgba(212,175,55,0.12) !important;
}
.map-btn {
  background: rgba(212,175,55,0.06) !important;
  backdrop-filter: blur(12px) !important;
  transition: all 0.25s ease !important;
}
.map-btn:hover {
  background: rgba(212,175,55,0.16) !important;
  box-shadow: 0 4px 18px rgba(212,175,55,0.18) !important;
}

/* === Company Trigger Glass === */
.company-trigger {
  background: rgba(255,255,255,0.04) !important;
  backdrop-filter: blur(12px) !important;
}

/* === Premium Scrollbar === */
::-webkit-scrollbar { width: 6px; height: 6px; }
::-webkit-scrollbar-track { background: rgba(255,255,255,0.02); }
::-webkit-scrollbar-thumb {
  background: linear-gradient(180deg, rgba(212,175,55,0.5), rgba(212,175,55,0.25));
  border-radius: 3px;
}
::-webkit-scrollbar-thumb:hover { background: rgba(212,175,55,0.7); }

/* === Top Rank + Bar Glow === */
.top-rank {
  background: linear-gradient(135deg, rgba(212,175,55,0.2) 0%, rgba(212,175,55,0.07) 100%) !important;
  border: 1px solid rgba(212,175,55,0.22) !important;
  box-shadow: 0 0 12px rgba(212,175,55,0.12) !important;
}
.top-bar {
  background: linear-gradient(90deg, var(--gold), #f5d020) !important;
  box-shadow: 0 0 8px rgba(212,175,55,0.35) !important;
}

/* === Report Table === */
.report-table th {
  background: linear-gradient(135deg, rgba(212,175,55,0.15) 0%, rgba(212,175,55,0.07) 100%) !important;
  backdrop-filter: blur(8px) !important;
}

/* === WA Settings Glass === */
.wa-settings {
  background: rgba(37,211,102,0.05) !important;
  backdrop-filter: blur(12px) !important;
}
.email-settings {
  background: rgba(59,130,246,0.05) !important;
  backdrop-filter: blur(12px) !important;
}

/* === Success Msg Glass === */
.login-success-msg {
  background: rgba(16,185,129,0.06) !important;
  backdrop-filter: blur(12px) !important;
  border-color: rgba(16,185,129,0.22) !important;
  box-shadow: 0 0 24px rgba(16,185,129,0.08) !important;
}
.success-ref { text-shadow: 0 0 22px rgba(212,175,55,0.35) !important; }

/* === Search + Filter Inputs === */
.search-input, .adv-search-wrap input, .filter-input {
  background: rgba(255,255,255,0.04) !important;
  backdrop-filter: blur(12px) !important;
  box-shadow: inset 0 2px 6px rgba(0,0,0,0.2) !important;
}
.search-input:focus, .adv-search-wrap input:focus, .filter-input:focus {
  border-color: rgba(212,175,55,0.42) !important;
  box-shadow: 0 0 0 4px rgba(212,175,55,0.09), inset 0 2px 6px rgba(0,0,0,0.18) !important;
}

/* === Timeline Items Glass === */
.tl-item {
  background: rgba(255,255,255,0.025) !important;
  backdrop-filter: blur(16px) !important;
  border-color: rgba(255,255,255,0.07) !important;
  box-shadow: 0 4px 16px rgba(0,0,0,0.3) !important;
}

/* === Plate Input Glass === */
.plate-input {
  background: rgba(255,255,255,0.04) !important;
  backdrop-filter: blur(12px) !important;
  border-color: rgba(255,255,255,0.09) !important;
  box-shadow: inset 0 2px 6px rgba(0,0,0,0.25) !important;
}
.plate-input:focus {
  border-color: rgba(212,175,55,0.5) !important;
  box-shadow: 0 0 0 4px rgba(212,175,55,0.1), inset 0 2px 6px rgba(0,0,0,0.2) !important;
}
.plate-preview {
  background: rgba(212,175,55,0.07) !important;
  backdrop-filter: blur(12px) !important;
  border-color: rgba(212,175,55,0.22) !important;
  box-shadow: 0 0 20px rgba(212,175,55,0.08) !important;
}

/* === Repeat Warning Glass === */
.repeat-warning {
  background: rgba(239,68,68,0.06) !important;
  backdrop-filter: blur(12px) !important;
}
.repeat-form-alert {
  background: rgba(239,68,68,0.08) !important;
  backdrop-filter: blur(12px) !important;
}

/* === Light Mode Glass Adjustments === */
body.light-mode .card {
  background: rgba(255,255,255,0.72) !important;
  backdrop-filter: blur(24px) !important;
  border-color: rgba(180,145,20,0.18) !important;
  box-shadow: 0 8px 32px rgba(0,0,0,0.07), inset 0 1px 0 rgba(255,255,255,0.9) !important;
}
body.light-mode .stat-card {
  background: rgba(255,255,255,0.78) !important;
  backdrop-filter: blur(20px) !important;
}
body.light-mode .kpi-card {
  background: rgba(255,255,255,0.75) !important;
  backdrop-filter: blur(20px) !important;
}
body.light-mode .sidebar {
  background: rgba(248,252,255,0.88) !important;
  backdrop-filter: blur(40px) !important;
}
body.light-mode .login-card {
  background: rgba(255,255,255,0.82) !important;
  backdrop-filter: blur(40px) !important;
}
body.light-mode #loginPage {
  background:
    radial-gradient(ellipse 80% 60% at 20% 30%, rgba(212,175,55,0.07) 0%, transparent 60%),
    radial-gradient(ellipse 60% 50% at 80% 70%, rgba(59,130,246,0.05) 0%, transparent 60%),
    linear-gradient(135deg, #eef2f8 0%, #e2e8f0 100%) !important;
}
body.light-mode .v-card {
  background: rgba(255,255,255,0.65) !important;
  backdrop-filter: blur(20px) !important;
}
body.light-mode .notif-panel {
  background: rgba(248,252,255,0.9) !important;
  backdrop-filter: blur(40px) !important;
}
body.light-mode .mobile-header,
body.light-mode .mobile-bottom-nav {
  background: rgba(248,252,255,0.9) !important;
  backdrop-filter: blur(30px) !important;
}
body.light-mode .filter-bar {
  background: rgba(255,255,255,0.65) !important;
  backdrop-filter: blur(20px) !important;
}
body.light-mode .tl-item {
  background: rgba(255,255,255,0.75) !important;
  backdrop-filter: blur(16px) !important;
}
body.light-mode .form-input,
body.light-mode .form-select,
body.light-mode .form-textarea {
  background: rgba(255,255,255,0.7) !important;
  backdrop-filter: blur(10px) !important;
  border-color: rgba(180,145,20,0.22) !important;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.06) !important;
}

/* ══════════════════════════════════════════════════════════════
   📊 PREMIUM DASHBOARD ENHANCEMENTS — V4.1
   ══════════════════════════════════════════════════════════════ */

/* === Dashboard Page Header Banner === */
#dashboardTab .page-header {
  background: linear-gradient(135deg,
    rgba(212,175,55,0.07) 0%,
    rgba(59,130,246,0.04) 50%,
    transparent 100%);
  border: 1px solid rgba(212,175,55,0.1);
  border-radius: 20px;
  padding: 20px 24px !important;
  margin-bottom: 20px;
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  position: relative;
  overflow: hidden;
}
#dashboardTab .page-header::before {
  content: '';
  position: absolute;
  top: -40px; right: -40px;
  width: 180px; height: 180px; border-radius: 50%;
  background: radial-gradient(circle, rgba(212,175,55,0.08) 0%, transparent 70%);
  pointer-events: none;
}
#dashboardTab .page-title {
  font-size: 26px !important;
  font-weight: 900 !important;
  background: linear-gradient(135deg, #f5d020 0%, #d4af37 50%, #c49b20 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  text-shadow: none !important;
}

/* === KPI Row Premium === */
.kpi-row {
  gap: 14px !important;
}
.kpi-card {
  border-radius: 18px !important;
  padding: 22px 20px !important;
  position: relative;
  overflow: hidden;
}
/* Animated glowing bottom accent bar */
.kpi-card::after {
  height: 3px !important;
  background: linear-gradient(90deg,
    transparent,
    var(--kpi-color, var(--gold)) 30%,
    var(--kpi-color, var(--gold)) 70%,
    transparent) !important;
  bottom: 0 !important;
  box-shadow: 0 0 12px var(--kpi-color, rgba(212,175,55,0.6)) !important;
  animation: kpiBarPulse 3s ease-in-out infinite !important;
}
@keyframes kpiBarPulse {
  0%,100% { opacity: 0.7; transform: scaleX(0.9); }
  50%      { opacity: 1;   transform: scaleX(1); }
}
/* KPI top-corner glow orb */
.kpi-card::before {
  content: '';
  position: absolute;
  top: -30px; right: -30px;
  width: 100px; height: 100px; border-radius: 50%;
  background: radial-gradient(circle, var(--kpi-color, rgba(212,175,55,0.15)) 0%, transparent 70%);
  pointer-events: none;
}
.kpi-card .kpi-icon {
  font-size: 32px !important;
  margin-bottom: 12px !important;
  display: block;
  filter: drop-shadow(0 4px 8px rgba(0,0,0,0.3));
}
.kpi-card .kpi-value {
  font-size: 38px !important;
  font-weight: 900 !important;
  letter-spacing: -1px !important;
  line-height: 1 !important;
  margin-bottom: 6px !important;
  filter: drop-shadow(0 2px 8px rgba(0,0,0,0.4));
}
.kpi-card .kpi-label {
  font-size: 12px !important;
  font-weight: 600 !important;
  letter-spacing: 0.3px !important;
  opacity: 0.75;
}
.kpi-trend {
  font-size: 10px !important;
  font-weight: 700 !important;
  padding: 4px 10px !important;
  border-radius: 20px !important;
  backdrop-filter: blur(8px) !important;
  border: 1px solid rgba(255,255,255,0.08) !important;
}
.kpi-trend.up      { box-shadow: 0 0 12px rgba(16,185,129,0.18) !important; }
.kpi-trend.down    { box-shadow: 0 0 12px rgba(239,68,68,0.14) !important; }
.kpi-trend.neutral { box-shadow: 0 0 12px rgba(245,158,11,0.14) !important; }

/* === Stat Cards Premium === */
.stats-row {
  gap: 14px !important;
}
.stat-card {
  border-radius: 18px !important;
  padding: 22px !important;
}
.stat-icon {
  width: 48px !important;
  height: 48px !important;
  border-radius: 14px !important;
  font-size: 22px !important;
  margin-bottom: 14px !important;
  backdrop-filter: blur(8px) !important;
  box-shadow: 0 4px 16px rgba(0,0,0,0.2) !important;
}
.stat-value {
  font-size: 38px !important;
  font-weight: 900 !important;
  letter-spacing: -1px !important;
  line-height: 1 !important;
  margin-bottom: 6px !important;
}
.stat-label {
  font-size: 12px !important;
  font-weight: 600 !important;
  opacity: 0.7 !important;
  letter-spacing: 0.2px !important;
}

/* === Chart Container Premium === */
.charts-grid {
  gap: 16px !important;
  margin-bottom: 16px !important;
}
.charts-grid .card {
  border-radius: 20px !important;
  overflow: hidden;
}
.charts-grid .card-header {
  padding: 16px 20px !important;
  position: relative;
}
.charts-grid .card-header h3 {
  font-size: 14px !important;
  font-weight: 700 !important;
  letter-spacing: 0.2px !important;
}
.chart-container {
  height: 250px !important;
  padding: 8px !important;
}
/* Make charts bigger on the second row */
.charts-grid:nth-child(4) .chart-container,
.charts-grid:nth-child(5) .chart-container {
  height: 300px !important;
}

/* === Top Lists Premium === */
.top-list {
  padding: 0 4px !important;
}
.top-list li {
  padding: 12px 8px !important;
  border-radius: 12px !important;
  border: none !important;
  border-bottom: 1px solid rgba(255,255,255,0.04) !important;
  transition: background 0.2s ease !important;
}
.top-list li:hover {
  background: rgba(212,175,55,0.05) !important;
  border-radius: 12px !important;
}
.top-rank {
  width: 30px !important;
  height: 30px !important;
  border-radius: 10px !important;
  font-size: 13px !important;
  font-weight: 900 !important;
  flex-shrink: 0 !important;
}
/* Gold/Silver/Bronze for top 3 */
.top-list li:nth-child(1) .top-rank {
  background: linear-gradient(135deg, #ffd700, #d4af37) !important;
  color: #1a1200 !important;
  box-shadow: 0 4px 12px rgba(212,175,55,0.4) !important;
}
.top-list li:nth-child(2) .top-rank {
  background: linear-gradient(135deg, #e0e0e0, #a8a8a8) !important;
  color: #2a2a2a !important;
  box-shadow: 0 4px 12px rgba(168,168,168,0.3) !important;
}
.top-list li:nth-child(3) .top-rank {
  background: linear-gradient(135deg, #cd7f32, #8b4513) !important;
  color: #fff8f0 !important;
  box-shadow: 0 4px 12px rgba(205,127,50,0.3) !important;
}
.top-bar {
  height: 6px !important;
  border-radius: 3px !important;
}
.top-bar-wrap {
  flex: 1 !important;
  height: 6px !important;
  background: rgba(255,255,255,0.05) !important;
  border-radius: 3px !important;
  overflow: hidden !important;
}
.top-count {
  font-size: 14px !important;
  font-weight: 800 !important;
  color: var(--gold-light) !important;
  min-width: 30px !important;
  text-align: center !important;
}

/* === Detailed Table Premium === */
#subTypeTable table {
  border-radius: 12px !important;
  overflow: hidden !important;
}
#subTypeTable table thead tr {
  background: linear-gradient(135deg, rgba(212,175,55,0.18) 0%, rgba(212,175,55,0.07) 100%) !important;
}
#subTypeTable table tbody tr:hover {
  background: rgba(212,175,55,0.04) !important;
}

/* === Activity Timeline Premium === */
.tl-item {
  border-radius: 16px !important;
  padding: 14px 18px !important;
  margin-bottom: 10px !important;
  position: relative;
  border-right: 3px solid rgba(212,175,55,0.3) !important;
  transition: all 0.25s ease !important;
}
.tl-item:hover {
  border-right-color: var(--gold) !important;
  transform: translateX(-3px) !important;
  background: rgba(255,255,255,0.04) !important;
}
.timeline-wrap::before {
  background: linear-gradient(180deg,
    rgba(212,175,55,0.5) 0%,
    rgba(212,175,55,0.15) 80%,
    transparent 100%) !important;
  width: 2px !important;
}

/* === Stats Number Counting Animation === */
@keyframes countUp {
  from { opacity: 0; transform: translateY(12px) scale(0.95); }
  to   { opacity: 1; transform: translateY(0) scale(1); }
}
.stat-value, .kpi-value {
  animation: countUp 0.6s cubic-bezier(0.34,1.56,0.64,1) both !important;
}
.stat-card:nth-child(1) .stat-value { animation-delay: 0.05s !important; }
.stat-card:nth-child(2) .stat-value { animation-delay: 0.10s !important; }
.stat-card:nth-child(3) .stat-value { animation-delay: 0.15s !important; }
.stat-card:nth-child(4) .stat-value { animation-delay: 0.20s !important; }
.stat-card:nth-child(5) .stat-value { animation-delay: 0.25s !important; }
.stat-card:nth-child(6) .stat-value { animation-delay: 0.30s !important; }

/* === Dashboard Filter Select === */
#dashFilterType {
  background: rgba(212,175,55,0.07) !important;
  border: 1px solid rgba(212,175,55,0.22) !important;
  border-radius: 12px !important;
  color: var(--gold-light) !important;
  backdrop-filter: blur(12px) !important;
  font-weight: 700 !important;
  cursor: pointer !important;
  transition: all 0.2s ease !important;
}
#dashFilterType:focus {
  border-color: rgba(212,175,55,0.5) !important;
  box-shadow: 0 0 0 4px rgba(212,175,55,0.1) !important;
  background: rgba(212,175,55,0.1) !important;
}

/* === Light Mode Dashboard === */
body.light-mode #dashboardTab .page-header {
  background: linear-gradient(135deg,
    rgba(212,175,55,0.08) 0%,
    rgba(59,130,246,0.04) 100%) !important;
  border-color: rgba(180,145,20,0.15) !important;
}
body.light-mode #dashboardTab .page-title {
  background: linear-gradient(135deg, #b8890f 0%, #d4af37 100%) !important;
  -webkit-background-clip: text !important;
  -webkit-text-fill-color: transparent !important;
  background-clip: text !important;
}
body.light-mode .kpi-card { background: rgba(255,255,255,0.82) !important; }
body.light-mode .top-list li:hover { background: rgba(212,175,55,0.05) !important; }
body.light-mode .tl-item { background: rgba(255,255,255,0.72) !important; }
body.light-mode #dashFilterType {
  background: rgba(212,175,55,0.08) !important;
  color: #8a6800 !important;
}

</style>
</head>
<body>

<!-- ─── THEME TOGGLE PILL ──────────────────────────── -->
<button class="theme-pill" id="themePill" onclick="toggleTheme()" title="تبديل الوضع النهاري/الليلي">
  <span id="themeIcon" style="font-size:18px;">☀️</span>
  <span id="themeLabel">نهاري</span>
</button>

<!-- ─── LOGIN PAGE ─────────────────────────────────────── -->
<div id="loginPage">
  <div class="login-card">
    <div class="login-logo">
      <div class="icon" style="font-size:52px;line-height:1;margin-bottom:10px;">⚠️</div>
      <h1>نظام مخالفات القدية</h1>
      <p>Qiddiya Violations Management System</p>
    </div>
    <div class="login-tabs">
      <button class="login-tab active" onclick="switchLoginTab('employee')" id="empTab">👷 موظف</button>
      <button class="login-tab" onclick="switchLoginTab('supervisor')" id="supTab">👔 مشرف</button>
      <button class="login-tab" onclick="switchLoginTab('admin')" id="adminTab">🛡️ مراقب</button>
    </div>
    <div class="login-error" id="loginError"></div>
    <div class="form-section">
      <label class="form-label">الرقم الوظيفي</label>
      <input class="form-input" type="text" id="employeeId" placeholder="أدخل الرقم الوظيفي" maxlength="20" autocomplete="username">
    </div>
    <div class="form-section">
      <label class="form-label">الرقم السري</label>
      <input class="form-input" type="password" id="userPassword" placeholder="أدخل الرقم السري" maxlength="20">
    </div>
    <div class="form-section" id="supervisorCodeField" style="display:none;">
      <label class="form-label">🔑 رمز الدخول للمشرف</label>
      <input class="form-input" type="password" id="supervisorCode" placeholder="أدخل رمز المشرف" autocomplete="off">
    </div>
    <button class="btn-primary" onclick="login()">تسجيل الدخول</button>
  </div>
</div>

<!-- ─── MAIN APP ──────────────────────────────────────── -->
<div id="mainApp" class="hide">

  <!-- Toast -->
  <div class="toast" id="toast"></div>

  <!-- Notification Overlay -->
  <div class="notif-overlay" id="notifOverlay" onclick="closeNotifPanel()"></div>
  <!-- Notification Panel -->
  <div class="notif-panel" id="notifPanel">
    <div class="notif-header">
      <h3>🔔 الإشعارات</h3>
      <button class="notif-close" onclick="closeNotifPanel()">✕</button>
    </div>
    <div class="notif-body" id="notifBody"></div>
    <div class="notif-footer">
      <button class="notif-clear-btn" onclick="clearAllNotifications()">🗑️ مسح جميع الإشعارات</button>
    </div>
  </div>

  <!-- Sidebar overlay (mobile) -->
  <div class="sidebar-overlay" id="sidebarOverlay" onclick="closeSidebar()"></div>

  <!-- Sidebar -->
  <!-- Sidebar Toggle -->
  <button class="sidebar-toggle" id="sidebarToggle" onclick="toggleSidebar()" title="إظهار/إخفاء القائمة">◀</button>

  <aside class="sidebar" id="sidebar">
    <div class="sidebar-logo">
      <div class="logo-icon" style="font-size:32px;">⚠️</div>
      <h2>نظام مخالفات القدية</h2>
      <p>Qiddiya Violations System</p>
    </div>
    <div class="sidebar-user">
      <div class="user-avatar">👤</div>
      <div class="user-info">
        <div class="user-name" id="userName">—</div>
        <div class="user-role" id="userRole">موظف</div>
      </div>
    </div>
    <nav class="sidebar-nav" id="sidebarNav"></nav>
    <div class="sidebar-footer">
      <button class="dl-btn" style="display:flex;margin-bottom:8px;background:rgba(212,175,55,0.08);border:1px solid var(--border);border-radius:10px;padding:10px 14px;color:var(--gold);font-size:13px;font-weight:700;cursor:pointer;font-family:'Tajawal',sans-serif;width:100%;align-items:center;gap:8px;position:relative;" onclick="openNotifPanel()">
        🔔 الإشعارات <span id="sidebarNotifBadge" style="display:none;background:#ef4444;color:white;border-radius:20px;padding:2px 7px;font-size:11px;font-weight:900;margin-right:auto;"></span>
      </button>
      <button class="dl-btn" id="excelBtn" onclick="downloadExcelFile()">
        📊 تصدير Excel <span class="dl-badge" id="excelBadge">0</span>
      </button>
      <button class="btn-logout" onclick="logout()" style="margin-top:8px;">🚪 تسجيل الخروج</button>
    </div>
  </aside>

  <!-- Mobile header -->
  <div class="mobile-header" id="mobileHeader">
    <div class="mobile-header-title" style="font-size:17px;gap:10px;">⚠️ مخالفات القدية</div>
    <div style="display:flex;align-items:center;gap:8px;">
      <button class="hamburger-btn" id="mobileNotifBtn" onclick="openNotifPanel();" style="font-size:16px;position:relative;" title="الإشعارات">
        🔔
        <span id="mobileNotifBadge" style="display:none;position:absolute;top:2px;right:2px;background:var(--red);color:white;border-radius:10px;padding:1px 5px;font-size:8px;font-weight:900;"></span>
      </button>
      <button class="hamburger-btn" onclick="logout()" style="font-size:16px;background:rgba(239,68,68,0.1);border-color:rgba(239,68,68,0.3);color:#f87171;" title="تسجيل الخروج">
        🚪
      </button>
      <button class="hamburger-btn" onclick="toggleSidebar()">☰</button>
    </div>
  </div>

  <!-- Mobile bottom navigation -->
  <nav class="mobile-bottom-nav" id="mobileBottomNav"></nav>

  <!-- Main -->
  <main class="main">

    <!-- ── DASHBOARD TAB ── -->
    <div id="dashboardTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">📊 لوحة الإحصائيات</div>
          <div class="page-subtitle" id="dashboardSubtitle">نظرة شاملة على المخالفات المسجلة</div>
        </div>
        <!-- فلتر النوع -->
        <div style="display:flex;gap:8px;flex-wrap:wrap;">
          <select class="form-select" id="dashFilterType" onchange="renderDashboard()" style="min-width:160px;padding:8px 12px;font-size:13px;">
            <option value="">كل الأنواع</option>
            <option value="مرورية">🚗 مرورية</option>
            <option value="جنائية">⚖️ جنائية</option>
            <option value="عدم_التزام">⚠️ عدم التزام</option>
            <option value="تصاريح">📋 تصاريح</option>
          </select>
        </div>
      </div>
      <!-- KPI Performance Indicators -->
      <div class="kpi-row" id="kpiRow"></div>
      <!-- Stat cards -->
      <div class="stats-row" id="statCards"></div>
      <!-- Charts Row 1 -->
      <div class="charts-grid">
        <div class="card">
          <div class="card-header"><h3>📋 المخالفات حسب النوع الرئيسي</h3></div>
          <div class="card-body"><div class="chart-container"><canvas id="chartByType"></canvas></div></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>📝 المخالفات حسب النوع الفرعي</h3></div>
          <div class="card-body"><div class="chart-container" style="height:350px;"><canvas id="chartBySubType"></canvas></div></div>
        </div>
      </div>
      <!-- Charts Row 2 -->
      <div class="charts-grid">
        <div class="card">
          <div class="card-header"><h3>⚖️ حالة المخالفات</h3></div>
          <div class="card-body"><div class="chart-container"><canvas id="chartByStatus"></canvas></div></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>📍 حسب المنطقة</h3></div>
          <div class="card-body"><div class="chart-container"><canvas id="chartByZone"></canvas></div></div>
        </div>
      </div>
      <!-- Charts Row 3 -->
      <div class="charts-grid">
        <div class="card">
          <div class="card-header"><h3>🔄 حسب الشفت</h3></div>
          <div class="card-body"><div class="chart-container"><canvas id="chartByShift"></canvas></div></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>👥 حسب المجموعة</h3></div>
          <div class="card-body"><div class="chart-container"><canvas id="chartByGroup"></canvas></div></div>
        </div>
      </div>
      <!-- Charts Row 4 - Companies -->
      <div class="charts-grid">
        <div class="card">
          <div class="card-header"><h3>🏢 أكثر الشركات مخالفةً (رسم بياني)</h3></div>
          <div class="card-body"><div class="chart-container" style="height:350px;"><canvas id="chartByCompany"></canvas></div></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>🏢 مخالفات الشركات حسب النوع</h3></div>
          <div class="card-body"><div class="chart-container" style="height:350px;"><canvas id="chartCompanyByType"></canvas></div></div>
        </div>
      </div>
      <!-- Top Lists -->
      <div class="charts-grid">
        <div class="card">
          <div class="card-header"><h3>🏆 أنشط المفتشين</h3></div>
          <div class="card-body"><ul class="top-list" id="topInspectors"></ul></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>📈 أكثر الشركات مخالفةً</h3></div>
          <div class="card-body"><ul class="top-list" id="topCompanies"></ul></div>
        </div>
      </div>
      <!-- Detailed Sub-type Table -->
      <div class="card" style="margin-top:16px;">
        <div class="card-header"><h3>📊 تفصيل المخالفات حسب النوع الرئيسي والفرعي</h3></div>
        <div class="card-body" id="subTypeTable" style="overflow-x:auto;"></div>
      </div>
      <!-- Activity Timeline -->
      <div class="timeline-section" style="margin-top:20px;">
        <div class="card">
          <div class="card-header"><h3>🕐 آخر النشاطات</h3></div>
          <div class="card-body">
            <div class="timeline-wrap" id="activityTimeline"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- ── FORM TAB ── -->
    <div id="formTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">📝 تسجيل مخالفة جديدة</div>
          <div class="page-subtitle">أدخل بيانات المخالفة بدقة</div>
        </div>
      </div>
      <!-- Success msg -->
      <div class="login-success-msg" id="successMsg">
        <div style="font-size:28px;">✅</div>
        <div style="font-size:16px; font-weight:700; color:#34d399; margin-top:8px;">تم تسجيل المخالفة بنجاح!</div>
        <div class="success-ref" id="refNumber"></div>
        <div style="font-size:12px; color:var(--muted);">المخالفة قيد مراجعة المشرف</div>
        <button class="btn-sm btn-pdf" style="margin-top:12px;" onclick="sharePDF()">📄 تحميل PDF</button>
      </div>

      <!-- ═══════════════════════════════════════════════
           المرحلة 1: الفحص الأمني — لوحة المركبة أو رقم الهوية/الإقامة (اختياري أحدهما)
           ═══════════════════════════════════════════════ -->
      <div class="card" id="securityCheckCard">
        <div class="card-header"><h3>🔍 الخطوة 1: الفحص الأمني</h3></div>
        <div class="card-body">
          <div style="text-align:center;margin-bottom:20px;">
            <div style="font-size:40px;margin-bottom:8px;">🛡️</div>
            <div style="font-size:15px;font-weight:700;color:var(--gold-light);margin-bottom:4px;">فحص إجباري قبل إصدار المخالفة</div>
            <div style="font-size:12px;color:var(--muted);">أدخل لوحة المركبة أو رقم الهوية/الإقامة أو كلاهما (خانة واحدة على الأقل مطلوبة)</div>
          </div>

          <!-- إدخال لوحة المركبة -->
          <div class="form-section">
            <label class="form-label">🚗 لوحة المركبة <span style="font-size:10px;color:var(--muted);">(اختياري)</span></label>
            <div class="plate-wrap">
              <div class="plate-box">
                <input class="plate-input" type="text" id="scanPlateLetters" placeholder="ABC" maxlength="4"
                  oninput="this.value=this.value.replace(/[^a-zA-Z]/g,'').toUpperCase(); updateScanPlatePreview()">
                <div class="plate-hint">حروف إنجليزية</div>
              </div>
              <div class="plate-sep">—</div>
              <div class="plate-box">
                <input class="plate-input" type="text" id="scanPlateNumbers" placeholder="1234" maxlength="4"
                  oninput="this.value=this.value.replace(/[^0-9]/g,''); updateScanPlatePreview()">
                <div class="plate-hint">أرقام فقط</div>
              </div>
            </div>
            <div class="plate-preview" id="scanPlatePreview"></div>
          </div>

          <!-- إدخال رقم الهوية / الإقامة -->
          <div class="form-section" style="margin-top:8px;">
            <label class="form-label">🪪 رقم الهوية / الإقامة <span style="font-size:10px;color:var(--muted);">(اختياري)</span></label>
            <input class="form-input" type="text" id="scanIdNumber" placeholder="أدخل رقم الهوية أو الإقامة (10 أرقام)" maxlength="10"
              oninput="this.value=this.value.replace(/[^0-9]/g,'')" style="font-size:20px;text-align:center;letter-spacing:4px;font-weight:900;">
          </div>

          <!-- نتيجة الفحص -->
          <div id="scanResultBox" style="display:none;"></div>

          <!-- أزرار الفحص -->
          <div style="margin-top:16px;">
            <button class="btn-primary" onclick="runSecurityScan()" id="btnRunScan" style="width:100%;">🔍 فحص الآن</button>
          </div>
        </div>
      </div>

      <!-- ═══════════════════════════════════════════════
           المرحلة 2: نموذج المخالفة الكامل (مخفي حتى يتم الفحص)
           ═══════════════════════════════════════════════ -->
      <div class="card" id="fullViolationForm" style="display:none;margin-top:16px;">
        <div class="card-header">
          <h3>⚠️ الخطوة 2: معلومات المخالفة</h3>
          <!-- شارة حالة الفحص -->
          <span id="securityBadge" style="margin-right:auto;padding:4px 12px;border-radius:20px;font-size:11px;font-weight:700;background:rgba(16,185,129,0.15);border:1px solid rgba(16,185,129,0.3);color:#34d399;">✅ تم الفحص</span>
        </div>
        <div class="card-body">
          <!-- نوع المخالف (أول حقل بعد الفحص) -->
          <div class="form-grid">
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> 👤 نوع المخالف</label>
              <select class="form-select" id="violatorType" onchange="onViolatorTypeChange()">
                <option value="">— اختر —</option>
                <option value="موظف">موظف</option>
                <option value="مقاول">مقاول</option>
                <option value="مقاول بالباطن">مقاول بالباطن</option>
                <option value="زائر">زائر</option>
                <option value="شركة مدرجة">شركة مدرجة</option>
              </select>
            </div>
            <div class="form-section" id="nationalityField" style="display:none;">
              <label class="form-label"><span class="req">*</span> 🌍 الجنسية</label>
              <input class="form-input" type="text" id="violatorNationality" placeholder="أدخل الجنسية">
            </div>
          </div>
          <!-- Company (يظهر فقط لشركة مدرجة) -->
          <div class="form-section" id="violatorCompanyField" style="display:none;">
            <label class="form-label"><span class="req">*</span> اسم الشركة</label>
            <input type="hidden" id="companyName">
            <div class="company-wrap">
              <div class="company-trigger" id="companyTrigger" onclick="toggleCompanyDropdown()">
                <span id="companyDisplayText" style="color:var(--muted);">— اختر الشركة —</span>
                <span>▾</span>
              </div>
              <div class="company-dropdown" id="companyDropdown">
                <input class="company-search-input" type="text" id="companySearch" placeholder="🔍 ابحث عن الشركة..." oninput="filterCompanies()" onclick="event.stopPropagation()">
                <div id="companyList"></div>
              </div>
            </div>
          </div>

          <!-- نوع المخالفة -->
          <div class="form-grid">
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> نوع المخالفة الرئيسي</label>
              <select class="form-select" id="mainViolationType" onchange="updateSubViolations()">
                <option value="">— اختر —</option>
                <option value="مرورية">مخالفة مرورية 🚗</option>
                <option value="جنائية">مخالفة جنائية ⚖️</option>
                <option value="عدم_التزام">عدم التزام بأنظمة القدية 🏗️</option>
                <option value="تصاريح">مخالفة التصاريح 📋</option>
              </select>
            </div>
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> نوع المخالفة الفرعي</label>
              <select class="form-select" id="subViolationType" onchange="toggleOtherField()" disabled>
                <option value="">— اختر النوع أولاً —</option>
              </select>
            </div>
          </div>
          <div class="form-section" id="otherViolationField" style="display:none;">
            <label class="form-label"><span class="req">*</span> تفاصيل المخالفة</label>
            <input class="form-input" type="text" id="otherViolationDetail" placeholder="اكتب تفاصيل المخالفة">
          </div>
          <div class="form-section" id="personCountField" style="display:none;">
            <label class="form-label"><span class="req">*</span> عدد الأشخاص</label>
            <input class="form-input" type="number" id="personCount" placeholder="أدخل العدد" min="1" max="100">
          </div>
          <div class="form-grid">
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> نوع التصريح</label>
              <select class="form-select" id="permitType">
                <option value="">— اختر —</option>
                <option value="QR">QR Code</option>
                <option value="بطاقة">بطاقة</option>
                <option value="ورقي">ورقي</option>
                <option value="فاتورة مورد">فاتورة مورد</option>
                <option value="إلكتروني">إلكتروني</option>
                <option value="لايوجد">لا يوجد</option>
              </select>
            </div>
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> المنطقة</label>
              <select class="form-select" id="zone">
                <option value="">— اختر —</option>
                <option value="Upper Zone">Upper Zone</option>
                <option value="Lower East Zone">Lower East Zone</option>
                <option value="Lower West Zone">Lower West Zone</option>
              </select>
            </div>
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> المجموعة المستلمة</label>
              <select class="form-select" id="receivingGroup">
                <option value="">— اختر —</option>
                <option value="Group1">Group 1</option>
                <option value="Group2">Group 2</option>
                <option value="Group3">Group 3</option>
                <option value="Group4">Group 4</option>
                <option value="Group A&B">Group A&B</option>
              </select>
            </div>
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> الشفت</label>
              <select class="form-select" id="shift">
                <option value="">— اختر —</option>
                <option value="A">A</option>
                <option value="B">B</option>
                <option value="C">C</option>
              </select>
            </div>
          </div>
          <div class="form-grid">
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> رقم هوية المخالف</label>
              <input class="form-input" type="text" id="violatorId" placeholder="10 أرقام" maxlength="10" oninput="checkRepeatOnForm()">
            </div>
            <div class="form-section">
              <label class="form-label"><span class="req">*</span> رقم جوال المخالف</label>
              <input class="form-input" type="tel" id="violatorPhone" placeholder="05xxxxxxxx" maxlength="10">
            </div>
          </div>
          <!-- Plate -->
          <div class="form-section">
            <label class="form-label"><span class="req">*</span> لوحة المركبة</label>
            <input type="hidden" id="vehiclePlate">
            <div class="plate-wrap">
              <div class="plate-box">
                <input class="plate-input" type="text" id="plateLetters" placeholder="ABC" maxlength="4"
                  oninput="this.value=this.value.replace(/[^a-zA-Z]/g,'').toUpperCase(); updatePlate(); checkRepeatOnForm()">
                <div class="plate-hint">حروف إنجليزية</div>
              </div>
              <div class="plate-sep">—</div>
              <div class="plate-box">
                <input class="plate-input" type="text" id="plateNumbers" placeholder="1234" maxlength="4"
                  oninput="this.value=this.value.replace(/[^0-9]/g,''); updatePlate(); checkRepeatOnForm()">
                <div class="plate-hint">أرقام فقط</div>
              </div>
            </div>
            <div class="plate-preview" id="platePreview"></div>
          </div>
          <!-- Repeat Alert -->
          <div class="repeat-form-alert" id="formRepeatAlert"></div>
          <!-- Map -->
          <div class="form-section">
            <label class="form-label"><span class="req">*</span> 📍 الموقع الجغرافي</label>
            <div class="map-actions">
              <button class="map-btn" type="button" onclick="showMap()">🗺️ فتح الخريطة</button>
              <button class="map-btn" type="button" onclick="getCurrentLocation()">📍 موقعي الحالي</button>
            </div>
            <div id="map" style="display:none;"></div>
            <div class="location-result" id="coordinatesDisplay"></div>
          </div>
          <!-- Images -->
          <div class="form-section">
            <label class="form-label"><span class="req">*</span> 📸 صور المخالفة (مطلوب — حتى 3 صور)</label>
            <input type="file" id="violationImages" accept="image/*" multiple style="display:none;" onchange="handleImageUpload(event)">
            <input type="file" id="violationCamera" accept="image/*" capture="environment" style="display:none;" onchange="handleImageUpload(event)">
            <div style="display:flex;gap:8px;">
              <div class="upload-btn" onclick="document.getElementById('violationCamera').click()" style="cursor:pointer;flex:1;">
                <span style="font-size:28px;display:block;margin-bottom:4px;">📷</span>
                <span style="font-weight:700;">التقاط من الكاميرا</span>
              </div>
              <div class="upload-btn" onclick="document.getElementById('violationImages').click()" style="cursor:pointer;flex:1;">
                <span style="font-size:28px;display:block;margin-bottom:4px;">🖼️</span>
                <span style="font-weight:700;">اختيار من المعرض</span>
              </div>
            </div>
            <div class="img-preview-grid" id="imagePreview"></div>
            <div id="imgRequiredAlert" style="display:none;color:#f87171;font-size:12px;font-weight:700;margin-top:6px;">⚠️ يجب رفع صورة واحدة على الأقل</div>
          </div>
          <button class="btn-primary" onclick="submitViolation()">✅ تسجيل المخالفة</button>
        </div>
      </div>
    </div>

    <!-- ── VIOLATIONS LIST TAB ── -->
    <div id="myViolationsTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">📋 سجل المخالفات</div>
          <div class="page-subtitle">جميع المخالفات المسجلة</div>
        </div>
      </div>
      <!-- Advanced Filter Bar -->
      <div class="filter-bar">
        <div class="filter-search-row">
          <div class="adv-search-wrap">
            <span class="adv-icon">🔍</span>
            <input class="filter-input" type="text" id="searchInput" placeholder="بحث شامل: رقم مرجع، هوية، جوال، شركة، مفتش، لوحة..." oninput="applyFilters()" style="padding-right:48px;">
          </div>
        </div>
        <div class="adv-date-row" style="margin-bottom:10px;">
          <span style="font-size:12px;color:var(--muted);font-weight:700;">📅 فلتر التاريخ:</span>
          <input type="date" class="adv-date-input" id="filterDateFrom" onchange="applyFilters()" title="من تاريخ">
          <span style="font-size:12px;color:var(--muted);">إلى</span>
          <input type="date" class="adv-date-input" id="filterDateTo" onchange="applyFilters()" title="إلى تاريخ">
          <button class="btn-filter-reset-chip" onclick="document.getElementById('filterDateFrom').value='';document.getElementById('filterDateTo').value='';applyFilters();" style="font-size:10px;padding:4px 8px;">✕ مسح التاريخ</button>
        </div>
        <div class="filter-chips">
          <div class="filter-chip" id="chipStatus">
            <span class="fc-icon">📌</span>
            <select id="filterStatus" onchange="applyFilters(); updateChipActive('chipStatus','filterStatus')">
              <option value="">الحالة</option>
              <option value="pending">⏳ مراجعة</option>
              <option value="approved">✅ موافق</option>
              <option value="rejected">❌ مرفوض</option>
            </select>
          </div>
          <div class="filter-chip" id="chipZone">
            <span class="fc-icon">📍</span>
            <select id="filterZone" onchange="applyFilters(); updateChipActive('chipZone','filterZone')">
              <option value="">المنطقة</option>
              <option value="Upper Zone">Upper</option>
              <option value="Lower East Zone">Lower E</option>
              <option value="Lower West Zone">Lower W</option>
            </select>
          </div>
          <div class="filter-chip" id="chipShift">
            <span class="fc-icon">🔄</span>
            <select id="filterShift" onchange="applyFilters(); updateChipActive('chipShift','filterShift')">
              <option value="">الشفت</option>
              <option value="A">A</option>
              <option value="B">B</option>
              <option value="C">C</option>
            </select>
          </div>
          <div class="filter-chip" id="chipGroup">
            <span class="fc-icon">👥</span>
            <select id="filterGroup" onchange="applyFilters(); updateChipActive('chipGroup','filterGroup')">
              <option value="">المجموعة</option>
              <option value="Group1">G1</option>
              <option value="Group2">G2</option>
              <option value="Group3">G3</option>
              <option value="Group4">G4</option>
              <option value="Group A&B">A&B</option>
            </select>
          </div>
          <div class="filter-chip" id="chipType">
            <span class="fc-icon">⚠️</span>
            <select id="filterType" onchange="applyFilters(); updateChipActive('chipType','filterType')">
              <option value="">النوع</option>
              <option value="مرورية">مرورية</option>
              <option value="جنائية">جنائية</option>
              <option value="عدم_التزام">عدم التزام</option>
              <option value="تصاريح">تصاريح</option>
            </select>
          </div>
          <button class="btn-filter-reset-chip" onclick="resetFilters()">✕ مسح</button>
        </div>
      </div>
      <div class="filter-result-count" id="filterCount"></div>
      <div id="violationsList"></div>
    </div>

    <!-- ── PENDING TAB ── -->
    <div id="pendingTab" class="hide">
      <div class="page-header">
        <div><div class="page-title">⏳ قيد المراجعة</div></div>
      </div>
      <div id="pendingList"></div>
    </div>

    <!-- ── APPROVED TAB ── -->
    <div id="approvedTab" class="hide">
      <div class="page-header">
        <div><div class="page-title">✅ المخالفات الموافق عليها</div></div>
      </div>
      <div id="approvedList"></div>
    </div>

    <!-- ── REJECTED TAB ── -->
    <div id="rejectedTab" class="hide">
      <div class="page-header">
        <div><div class="page-title">❌ المخالفات المرفوضة</div></div>
      </div>
      <div id="rejectedList"></div>
    </div>

    <!-- ── REPORTS TAB ── -->
    <div id="reportsTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">📈 التقارير الدورية</div>
          <div class="page-subtitle">ملخص تلقائي يومي وأسبوعي</div>
        </div>
        <div style="display:flex;gap:10px;">
          <button class="btn-dl-report" onclick="downloadReport()">📄 تحميل التقرير PDF</button>
        </div>
      </div>
      <!-- Period selector -->
      <div class="report-period-btns">
        <button class="btn-period active" id="periodToday" onclick="setReportPeriod('today')">📅 اليوم</button>
        <button class="btn-period" id="periodWeek" onclick="setReportPeriod('week')">📆 هذا الأسبوع</button>
        <button class="btn-period" id="periodMonth" onclick="setReportPeriod('month')">🗓️ هذا الشهر</button>
        <button class="btn-period" id="periodAll" onclick="setReportPeriod('all')">📊 الكل</button>
      </div>
      <!-- KPI Row -->
      <div class="report-grid" id="reportKPIs"></div>
      <!-- Tables -->
      <div class="charts-grid">
        <div class="card">
          <div class="card-header"><h3>👮 أنشط المفتشين</h3></div>
          <div class="card-body" id="reportInspectors"></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>🏢 أكثر الشركات</h3></div>
          <div class="card-body" id="reportCompanies"></div>
        </div>
      </div>
      <div class="charts-grid" style="margin-top:16px;">
        <div class="card">
          <div class="card-header"><h3>📍 توزيع المناطق</h3></div>
          <div class="card-body" id="reportZones"></div>
        </div>
        <div class="card">
          <div class="card-header"><h3>⚠️ أنواع المخالفات</h3></div>
          <div class="card-body" id="reportTypes"></div>
        </div>
      </div>
    </div>

    <!-- ── WHATSAPP SETTINGS TAB ── -->
    <div id="waSettingsTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">💬 إعدادات الإشعارات</div>
          <div class="page-subtitle">إشعارات واتساب والبريد الإلكتروني عند تسجيل مخالفة</div>
        </div>
      </div>

      <!-- ═══ اختيار نوع الإشعار ═══ -->
      <div class="card" style="margin-bottom:16px;">
        <div class="card-header"><h3>🔔 طريقة الإشعار</h3></div>
        <div class="card-body">
          <div style="display:flex;gap:12px;flex-wrap:wrap;">
            <label style="display:flex;align-items:center;gap:10px;padding:14px 20px;background:rgba(37,211,102,0.08);border:1px solid rgba(37,211,102,0.25);border-radius:12px;cursor:pointer;flex:1;min-width:200px;">
              <label class="toggle-switch">
                <input type="checkbox" id="notifWhatsApp" onchange="saveWASettings()" checked>
                <span class="toggle-slider"></span>
              </label>
              <span style="font-size:14px;font-weight:700;color:#25d366;">💬 واتساب</span>
            </label>
            <label style="display:flex;align-items:center;gap:10px;padding:14px 20px;background:rgba(59,130,246,0.08);border:1px solid rgba(59,130,246,0.25);border-radius:12px;cursor:pointer;flex:1;min-width:200px;">
              <label class="toggle-switch">
                <input type="checkbox" id="notifEmail" onchange="saveWASettings()">
                <span class="toggle-slider"></span>
              </label>
              <span style="font-size:14px;font-weight:700;color:#60a5fa;">📧 بريد إلكتروني</span>
            </label>
          </div>
        </div>
      </div>

      <!-- ═══ إعدادات واتساب ═══ -->
      <div class="card" id="waSettingsCard">
        <div class="card-header"><h3>💬 إعدادات واتساب</h3></div>
        <div class="card-body">
          <div class="wa-settings">
            <h4>📲 إشعار واتساب تلقائي</h4>
            <div class="wa-toggle">
              <label class="toggle-switch">
                <input type="checkbox" id="waEnabled" onchange="saveWASettings()">
                <span class="toggle-slider"></span>
              </label>
              <span style="font-size:13px;color:var(--text);">تفعيل إشعارات واتساب عند تسجيل مخالفة</span>
            </div>
            <div class="form-section">
              <label class="form-label">📱 أرقام الجوال للإشعار (مع رمز الدولة)</label>
              <div id="waNumbers">
                <div class="wa-number-row" style="margin-bottom:8px;">
                  <input class="form-input wa-number-input" type="tel" placeholder="مثال: 966501234567" id="waNum0" onchange="saveWASettings()">
                  <button class="btn-sm btn-pdf" onclick="removeWANumber(0)">🗑️</button>
                </div>
              </div>
              <button class="btn-sm btn-approve" style="margin-top:8px;" onclick="addWANumber()">➕ إضافة رقم</button>
            </div>
            <div class="form-section" style="margin-top:16px;">
              <label class="form-label">📝 نموذج الرسالة</label>
              <textarea class="form-textarea" id="waTemplate" rows="5" onchange="saveWASettings()" style="direction:rtl;">⚠️ مخالفة جديدة — نظام القدية
الرقم: {refNumber}
النوع: {type}
الشركة: {company}
المنطقة: {zone}
المفتش: {inspector}
الوقت: {time}</textarea>
            </div>
            <div style="margin-top:12px;">
              <button class="btn-primary" style="width:auto;padding:12px 28px;" onclick="testWANotification()">🧪 إرسال رسالة تجريبية</button>
            </div>
          </div>
          <div style="margin-top:16px; padding:14px; background:rgba(255,255,255,0.03); border-radius:10px; font-size:12px; color:var(--muted);">
            <strong style="color:var(--gold);">📌 ملاحظة:</strong> الإشعارات تعمل عبر واتساب ويب. عند تسجيل مخالفة، سيفتح واتساب تلقائياً مع الرسالة جاهزة للإرسال.
          </div>
        </div>
      </div>

      <!-- ═══ إعدادات البريد الإلكتروني ═══ -->
      <div class="card" id="emailSettingsCard" style="margin-top:16px;">
        <div class="card-header"><h3>📧 إعدادات البريد الإلكتروني</h3></div>
        <div class="card-body">
          <div class="email-settings">
            <h4>📧 إشعار البريد الإلكتروني</h4>
            <div class="wa-toggle" style="margin-bottom:14px;">
              <label class="toggle-switch">
                <input type="checkbox" id="emailEnabled" onchange="saveWASettings()">
                <span class="toggle-slider"></span>
              </label>
              <span style="font-size:13px;color:var(--text);">تفعيل إشعارات البريد عند تسجيل مخالفة</span>
            </div>
            <div class="form-section">
              <label class="form-label">📧 عناوين البريد الإلكتروني للإشعار</label>
              <div id="emailAddresses">
                <div class="wa-number-row" style="margin-bottom:8px;">
                  <input class="form-input email-input" type="email" placeholder="مثال: supervisor@qiddiya.com" id="emailAddr0" onchange="saveWASettings()">
                  <button class="btn-sm btn-pdf" onclick="removeEmailAddress(0)">🗑️</button>
                </div>
              </div>
              <button class="btn-sm btn-approve" style="margin-top:8px;" onclick="addEmailAddress()">➕ إضافة بريد</button>
            </div>
            <div class="form-section" style="margin-top:16px;">
              <label class="form-label">📝 نموذج البريد</label>
              <textarea class="form-textarea" id="emailTemplate" rows="5" onchange="saveWASettings()" style="direction:rtl;">⚠️ مخالفة جديدة — نظام القدية
الرقم المرجعي: {refNumber}
نوع المخالفة: {type}
الشركة: {company}
المنطقة: {zone}
المفتش: {inspector}
التاريخ والوقت: {time}</textarea>
            </div>
            <div style="margin-top:12px;">
              <button class="btn-primary" style="width:auto;padding:12px 28px;background:linear-gradient(135deg,#3b82f6,#60a5fa);" onclick="testEmailNotification()">🧪 إرسال بريد تجريبي</button>
            </div>
          </div>
          <div style="margin-top:16px; padding:14px; background:rgba(255,255,255,0.03); border-radius:10px; font-size:12px; color:var(--muted);">
            <strong style="color:#60a5fa;">📌 ملاحظة:</strong> يتم إرسال البريد عبر mailto protocol. سيفتح برنامج البريد مع الرسالة جاهزة للإرسال. يمكنك ربط النظام بخدمة SMTP لإرسال تلقائي.
          </div>
        </div>
      </div>

      </div>
    </div>

    <!-- ── ADMIN PANEL TAB ── -->
    <!-- ═══ خريطة المخالفات الحية ═══ -->
    <div id="liveMapTab" class="hide">
      <div class="page-header" style="margin-bottom:12px;">
        <div>
          <div class="page-title">🗺️ خريطة المخالفات الحية</div>
          <div class="page-subtitle">مواقع جميع المخالفات على الخريطة — Live Violations Map</div>
        </div>
        <div style="display:flex;gap:8px;">
          <button class="btn-sm" onclick="if(mapInstance)mapInstance.setView([24.53,46.44],13)" style="padding:8px 14px;font-size:12px;background:var(--gold-dim);border:1px solid var(--border);color:var(--gold-light);border-radius:8px;cursor:pointer;">📍 القدية</button>
        </div>
      </div>
      <div id="liveMapContent" style="background:var(--card);border:1px solid var(--border);border-radius:16px;overflow:hidden;position:relative;">
        <div id="violationMap" style="width:100%;height:calc(100vh - 240px);min-height:400px;"></div>
      </div>
      <div id="liveMapStats" style="margin-top:12px;"></div>
    </div>

    <!-- ═══ تحليل ذكي AI ═══ -->
    <div id="aiAnalysisTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">🤖 تحليل ذكي — AI Insights</div>
          <div class="page-subtitle">توصيات وتحليلات مبنية على بيانات المخالفات</div>
        </div>
      </div>
      <div id="aiAnalysisContent"></div>
    </div>

    <!-- ═══ تقييم أداء المفتشين ═══ -->
    <div id="performanceTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">🏆 تقييم أداء المفتشين</div>
          <div class="page-subtitle">ترتيب المفتشين حسب الإنتاجية وسرعة الاستجابة</div>
        </div>
      </div>
      <div id="performanceContent"></div>
    </div>

    <div id="adminPanelTab" class="hide">
      <div class="page-header">
        <div>
          <div class="page-title">🛡️ لوحة المراقب السري</div>
          <div class="page-subtitle">صلاحيات كاملة — Admin Access Only</div>
        </div>
        <button class="btn-sm btn-pdf" onclick="exportAdminReport()" style="padding:10px 18px;font-size:13px;">📊 تصدير تقرير شامل</button>
      </div>
      <div id="adminPanelContent">
        <div style="text-align:center;padding:40px;color:var(--muted);">
          <div style="font-size:36px;margin-bottom:10px;">⏳</div>
          <div>جاري تحميل البيانات...</div>
        </div>
      </div>

      <!-- ═══ إدارة القوائم السوداء والتعاميم ═══ -->
      <div class="card" style="margin-top:20px;border-color:rgba(239,68,68,0.3);">
        <div class="card-header">
          <h3>🚫 القوائم السوداء والتعاميم</h3>
          <span style="padding:4px 12px;border-radius:20px;font-size:10px;font-weight:700;background:rgba(239,68,68,0.15);border:1px solid rgba(239,68,68,0.3);color:#f87171;">🛡️ Admin Only</span>
        </div>
        <div class="card-body" style="padding:20px;">
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:16px;">
            <!-- لوحات محظورة -->
            <div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;">
              <div style="font-size:14px;font-weight:700;color:#f87171;margin-bottom:10px;">🚗 لوحات محظورة (منع دخول)</div>
              <div style="display:flex;gap:6px;margin-bottom:10px;">
                <input type="text" id="newBlockedPlate" class="form-input" placeholder="أدخل رقم اللوحة..." style="flex:1;padding:8px 12px;font-size:12px;">
                <button class="btn-sm btn-reject" onclick="addToList('blockedPlates','newBlockedPlate')" style="padding:8px 14px;font-size:12px;">➕ إضافة</button>
              </div>
              <div id="blockedPlatesList" style="max-height:150px;overflow-y:auto;"></div>
            </div>
            <!-- هويات محظورة -->
            <div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;">
              <div style="font-size:14px;font-weight:700;color:#f87171;margin-bottom:10px;">🪪 هويات محظورة (منع دخول)</div>
              <div style="display:flex;gap:6px;margin-bottom:10px;">
                <input type="text" id="newBlockedId" class="form-input" placeholder="أدخل رقم الهوية..." style="flex:1;padding:8px 12px;font-size:12px;">
                <button class="btn-sm btn-reject" onclick="addToList('blockedIds','newBlockedId')" style="padding:8px 14px;font-size:12px;">➕ إضافة</button>
              </div>
              <div id="blockedIdsList" style="max-height:150px;overflow-y:auto;"></div>
            </div>
            <!-- لوحات مطلوبة (تعميم) -->
            <div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;">
              <div style="font-size:14px;font-weight:700;color:#fbbf24;margin-bottom:10px;">⚠️ لوحات عليها تعميم</div>
              <div style="display:flex;gap:6px;margin-bottom:10px;">
                <input type="text" id="newWantedPlate" class="form-input" placeholder="أدخل رقم اللوحة..." style="flex:1;padding:8px 12px;font-size:12px;">
                <button class="btn-sm" onclick="addToList('wantedPlates','newWantedPlate')" style="padding:8px 14px;font-size:12px;background:rgba(251,191,36,0.15);border:1px solid rgba(251,191,36,0.3);color:#fbbf24;">➕ إضافة</button>
              </div>
              <div id="wantedPlatesList" style="max-height:150px;overflow-y:auto;"></div>
            </div>
            <!-- هويات مطلوبة (تعميم) -->
            <div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;">
              <div style="font-size:14px;font-weight:700;color:#fbbf24;margin-bottom:10px;">⚠️ هويات عليها تعميم</div>
              <div style="display:flex;gap:6px;margin-bottom:10px;">
                <input type="text" id="newWantedId" class="form-input" placeholder="أدخل رقم الهوية..." style="flex:1;padding:8px 12px;font-size:12px;">
                <button class="btn-sm" onclick="addToList('wantedIds','newWantedId')" style="padding:8px 14px;font-size:12px;background:rgba(251,191,36,0.15);border:1px solid rgba(251,191,36,0.3);color:#fbbf24;">➕ إضافة</button>
              </div>
              <div id="wantedIdsList" style="max-height:150px;overflow-y:auto;"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- ═══ إدارة الموظفين ═══ -->
      <div class="card" style="margin-top:20px;border-color:rgba(59,130,246,0.3);">
        <div class="card-header">
          <h3>👥 إدارة الموظفين والمستخدمين</h3>
          <span style="padding:4px 12px;border-radius:20px;font-size:10px;font-weight:700;background:rgba(59,130,246,0.15);border:1px solid rgba(59,130,246,0.3);color:#60a5fa;">🛡️ Admin Only</span>
        </div>
        <div class="card-body" style="padding:20px;">
          <div style="font-size:13px;color:var(--muted);margin-bottom:16px;">إضافة موظف جديد أو مشرف — سيتم حفظه في Firebase مباشرة</div>
          <div style="display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-bottom:16px;">
            <div>
              <label style="font-size:11px;color:var(--gold);font-weight:700;display:block;margin-bottom:4px;">نوع المستخدم</label>
              <select id="newUserRole" class="form-select" onchange="toggleNewUserFields()" style="padding:10px 12px;font-size:13px;">
                <option value="employee">👷 موظف</option>
                <option value="supervisor">👔 مشرف</option>
              </select>
            </div>
            <div>
              <label style="font-size:11px;color:var(--gold);font-weight:700;display:block;margin-bottom:4px;">الرقم الوظيفي</label>
              <input type="text" id="newUserId" class="form-input" placeholder="مثال: 12345" style="padding:10px 12px;font-size:13px;">
            </div>
            <div>
              <label style="font-size:11px;color:var(--gold);font-weight:700;display:block;margin-bottom:4px;">الاسم الكامل</label>
              <input type="text" id="newUserName" class="form-input" placeholder="مثال: محمد أحمد العتيبي" style="padding:10px 12px;font-size:13px;">
            </div>
            <div>
              <label style="font-size:11px;color:var(--gold);font-weight:700;display:block;margin-bottom:4px;">كلمة المرور</label>
              <input type="text" id="newUserPass" class="form-input" placeholder="6 أرقام على الأقل" style="padding:10px 12px;font-size:13px;">
            </div>
            <div id="newUserGroupDiv" style="display:none;">
              <label style="font-size:11px;color:var(--gold);font-weight:700;display:block;margin-bottom:4px;">المجموعة (للمشرف)</label>
              <select id="newUserGroup" class="form-select" style="padding:10px 12px;font-size:13px;">
                <option value="Group1">Group 1</option>
                <option value="Group2">Group 2</option>
                <option value="Group3">Group 3</option>
                <option value="Group4">Group 4</option>
                <option value="Group A&B">Group A&B</option>
              </select>
            </div>
          </div>
          <button class="btn-sm btn-approve" onclick="addNewUser()" style="padding:12px 24px;font-size:14px;font-weight:700;">
            ➕ إضافة المستخدم
          </button>
          <div id="addUserResult" style="margin-top:12px;"></div>
        </div>
      </div>

      <!-- ═══ إدارة إيميلات الشركات — تحكم كامل للأدمن ═══ -->
      <div class="card" id="companyEmailCard" style="margin-top:20px;">
        <div class="card-header">
          <h3>📧 إدارة مراسلات الشركات المدرجة</h3>
          <span style="padding:4px 12px;border-radius:20px;font-size:10px;font-weight:700;background:rgba(168,85,247,0.15);border:1px solid rgba(168,85,247,0.3);color:#c084fc;">🛡️ Admin Only</span>
        </div>
        <div class="card-body">

          <!-- ═══ صندوق المخالفات المعلّقة — بانتظار قرار الأدمن ═══ -->
          <div style="margin-bottom:24px;">
            <div style="display:flex;align-items:center;gap:10px;margin-bottom:14px;">
              <span style="font-size:22px;">📬</span>
              <div>
                <div style="font-size:15px;font-weight:800;color:var(--gold-light);">مخالفات الشركات — بانتظار المراسلة</div>
                <div style="font-size:11px;color:var(--muted);">المخالفات على شركات مدرجة لم يتم إرسال إيميل لها بعد — أنت من تقرر متى وكيف ترسل</div>
              </div>
            </div>
            <!-- فلتر سريع -->
            <div style="display:flex;gap:8px;margin-bottom:12px;flex-wrap:wrap;">
              <select class="form-select" id="adminEmailQueueFilter" onchange="renderAdminEmailQueue()" style="max-width:200px;padding:8px 12px;font-size:12px;">
                <option value="pending_email">📬 بانتظار الإرسال</option>
                <option value="sent">✅ تم الإرسال</option>
                <option value="resolved">🔧 تم الحل</option>
                <option value="all">📋 الكل</option>
              </select>
              <input type="text" class="form-input" id="adminEmailQueueSearch" placeholder="🔍 بحث بالشركة أو الرقم..." oninput="renderAdminEmailQueue()" style="max-width:250px;padding:8px 12px;font-size:12px;">
            </div>
            <!-- قائمة المخالفات -->
            <div id="adminEmailQueue" style="max-height:500px;overflow-y:auto;"></div>
          </div>

          <div style="height:1px;background:linear-gradient(90deg,transparent,var(--border),transparent);margin:24px 0;"></div>

          <!-- ═══ إدارة إيميلات الشركات ═══ -->
          <div style="margin-bottom:24px;">
            <div style="display:flex;align-items:center;gap:10px;margin-bottom:14px;">
              <span style="font-size:22px;">🏢</span>
              <div>
                <div style="font-size:15px;font-weight:800;color:var(--gold-light);">دليل إيميلات الشركات</div>
                <div style="font-size:11px;color:var(--muted);">سجّل إيميل ممثل كل شركة لتتمكن من مراسلتهم</div>
              </div>
            </div>
            <div style="position:relative;margin-bottom:14px;">
              <input class="form-input" type="text" id="companyEmailSearch" placeholder="🔍 ابحث عن شركة..." oninput="filterCompanyEmails()" style="padding-right:40px;">
            </div>
            <div id="companyEmailList" style="max-height:350px;overflow-y:auto;border:1px solid var(--border);border-radius:12px;"></div>
          </div>

          <div style="height:1px;background:linear-gradient(90deg,transparent,var(--border),transparent);margin:24px 0;"></div>

          <!-- ═══ إحصائيات وتحكم ═══ -->
          <div style="display:flex;gap:12px;flex-wrap:wrap;margin-bottom:20px;">
            <div style="flex:1;min-width:140px;padding:14px;background:rgba(239,68,68,0.06);border:1px solid rgba(239,68,68,0.15);border-radius:10px;text-align:center;">
              <div style="font-size:26px;font-weight:900;color:#f87171;" id="pendingEmailCount">0</div>
              <div style="font-size:11px;color:var(--muted);">بانتظار الإرسال</div>
            </div>
            <div style="flex:1;min-width:140px;padding:14px;background:rgba(16,185,129,0.06);border:1px solid rgba(16,185,129,0.15);border-radius:10px;text-align:center;">
              <div style="font-size:26px;font-weight:900;color:#34d399;" id="sentEmailCount">0</div>
              <div style="font-size:11px;color:var(--muted);">تم الإرسال</div>
            </div>
            <div style="flex:1;min-width:140px;padding:14px;background:rgba(59,130,246,0.06);border:1px solid rgba(59,130,246,0.15);border-radius:10px;text-align:center;">
              <div style="font-size:26px;font-weight:900;color:#60a5fa;" id="resolvedEmailCount">0</div>
              <div style="font-size:11px;color:var(--muted);">تم الحل</div>
            </div>
            <div style="flex:1;min-width:140px;padding:14px;background:rgba(168,85,247,0.06);border:1px solid rgba(168,85,247,0.15);border-radius:10px;text-align:center;">
              <div style="font-size:26px;font-weight:900;color:#c084fc;" id="registeredCompanyCount">0</div>
              <div style="font-size:11px;color:var(--muted);">شركة بإيميل</div>
            </div>
          </div>

          <div style="display:flex;gap:10px;flex-wrap:wrap;">
            <button class="btn-primary" style="width:auto;padding:12px 24px;background:linear-gradient(135deg,#8b5cf6,#a78bfa);font-size:13px;" onclick="sendAllPendingCompanyEmails()">📧 إرسال جميع المعلّقة</button>
            <button class="btn-primary" style="width:auto;padding:12px 24px;background:linear-gradient(135deg,#3b82f6,#60a5fa);font-size:13px;" onclick="sendDailyCompanyReport()">📊 إرسال تقرير يومي مجمّع</button>
            <button class="btn-primary" style="width:auto;padding:12px 24px;background:rgba(16,185,129,0.1);border:1px solid rgba(16,185,129,0.3);color:#34d399;box-shadow:none;font-size:13px;" onclick="exportCompanyEmails()">📤 تصدير القائمة</button>
          </div>

          <!-- سجل الإرسال -->
          <div style="margin-top:20px;">
            <label class="form-label">📬 سجل الإرسال (آخر 20)</label>
            <div id="emailSendLog" style="max-height:200px;overflow-y:auto;"></div>
          </div>
        </div>
      </div>
    </div>

  </main>
</div>

<script>
// ══════════════════════════════════════════════════════
// 🔥 FIREBASE — المصادقة والبيانات السحابية
// ══════════════════════════════════════════════════════
const firebaseConfig = {
  apiKey: "AIzaSyB3_yy5iGGJ9eeGWW35jixQwg0FzrwTtKY",
  authDomain: "qiddiya-violations.firebaseapp.com",
  projectId: "qiddiya-violations",
  storageBucket: "qiddiya-violations.firebasestorage.app",
  messagingSenderId: "601816360191",
  appId: "1:601816360191:web:8bc18c33f405723405d8ca"
};
firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

// تفعيل العمل بدون اتصال
db.enablePersistence({ synchronizeTabs: true }).catch(err => {
  console.warn('Offline persistence:', err.code);
});

// ══════════════════════════════════════════════════════
// 🔐 نظام المصادقة عبر Firebase (بدل الـ hashes المكشوفة)
// ══════════════════════════════════════════════════════
async function authenticateUser(empId, password, loginType) {
  try {
    let docId;
    if (loginType === 'admin') {
      docId = 'ADMIN';
    } else if (loginType === 'supervisor') {
      // البحث عن المشرف
      const supQuery = await db.collection('users')
        .where('employeeId', '==', empId)
        .where('role', '==', 'supervisor')
        .limit(1).get();
      if (supQuery.empty) return { success: false, error: 'رقم المشرف غير صحيح' };
      const supDoc = supQuery.docs[0];
      const supData = supDoc.data();
      if (supData.password !== password) return { success: false, error: 'كلمة المرور غير صحيحة' };
      return { success: true, user: { id: supData.employeeId, name: supData.name, role: 'supervisor', group: supData.group } };
    } else {
      docId = 'EMP-' + empId;
    }

    const doc = await db.collection('users').doc(docId).get();
    if (!doc.exists) return { success: false, error: 'الرقم الوظيفي غير مسجل' };
    
    const data = doc.data();
    if (!data.active) return { success: false, error: 'الحساب معطّل' };
    if (data.password !== password) return { success: false, error: 'كلمة المرور غير صحيحة' };
    
    return {
      success: true,
      user: {
        id: data.employeeId,
        name: data.name,
        role: data.role,
        group: data.group || null
      }
    };
  } catch(e) {
    console.error('Auth error:', e);
    return { success: false, error: 'خطأ في الاتصال — تأكد من اتصالك بالإنترنت' };
  }
}

// ══════════════════════════════════════════════════════
// 📦 Firebase CRUD — المخالفات
// ══════════════════════════════════════════════════════
async function saveViolationToFirebase(violation) {
  try {
    // حفظ نسخة بدون الصور (الصور كبيرة)
    const vData = { ...violation };
    const images = vData.images || [];
    delete vData.images;
    vData.imageCount = images.length;
    vData.createdAt = firebase.firestore.FieldValue.serverTimestamp();
    vData.updatedAt = firebase.firestore.FieldValue.serverTimestamp();

    await db.collection('violations').doc(violation.refNumber).set(vData);

    // حفظ الصور كمستندات فرعية (لتفادي حد 1MB للمستند الواحد)
    for (let i = 0; i < images.length; i++) {
      await db.collection('violations').doc(violation.refNumber)
        .collection('images').doc('img' + i).set({
          data: images[i],
          index: i,
          createdAt: firebase.firestore.FieldValue.serverTimestamp()
        });
    }

    console.log('✅ تم حفظ المخالفة في Firebase:', violation.refNumber);
    return true;
  } catch(e) {
    console.error('❌ خطأ في حفظ المخالفة:', e);
    return false;
  }
}

async function loadViolationsFromFirebase() {
  try {
    const snapshot = await db.collection('violations').orderBy('createdAt', 'desc').get();
    const result = [];
    for (const doc of snapshot.docs) {
      const v = doc.data();
      v.images = [];
      // تحميل الصور
      try {
        const imgSnap = await doc.ref.collection('images').orderBy('index').get();
        v.images = imgSnap.docs.map(d => d.data().data);
      } catch(e) {}
      result.push(v);
    }
    console.log('📥 تم تحميل', result.length, 'مخالفة من Firebase');
    return result;
  } catch(e) {
    console.error('❌ خطأ في تحميل المخالفات:', e);
    return [];
  }
}

async function updateViolationInFirebase(refNumber, updates) {
  try {
    updates.updatedAt = firebase.firestore.FieldValue.serverTimestamp();
    await db.collection('violations').doc(refNumber).update(updates);
    return true;
  } catch(e) {
    console.error('❌ خطأ في تحديث المخالفة:', e);
    return false;
  }
}

// ══════════════════════════════════════════════════════
// 🔔 الإشعارات — Firebase
// ══════════════════════════════════════════════════════
async function addNotificationToFirebase(icon, title, body, refNumber) {
  try {
    await db.collection('notifications').add({
      icon, title, body, refNumber,
      read: false,
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
  } catch(e) { console.warn('Notification save failed:', e); }
}

// ══════════════════════════════════════════════════════
// 📧 إيميلات الشركات — Firebase
// ══════════════════════════════════════════════════════
async function loadCompanyEmailsFromFirebase() {
  try {
    const doc = await db.collection('settings').doc('companyEmails').get();
    return doc.exists ? doc.data() : {};
  } catch(e) { return {}; }
}
async function saveCompanyEmailToFirebase(companyName, email, contact) {
  try {
    await db.collection('settings').doc('companyEmails').set(
      { [companyName]: { email, contact, updatedAt: new Date().toISOString() } },
      { merge: true }
    );
  } catch(e) { console.error('Save email error:', e); }
}

// ══════════════════════════════════════════════════════
// 🚫 القوائم السوداء — Firebase
// ══════════════════════════════════════════════════════
async function loadBlacklistsFromFirebase() {
  try {
    const doc = await db.collection('settings').doc('blacklists').get();
    return doc.exists ? doc.data() : { blockedPlates:[], blockedIds:[], wantedPlates:[], wantedIds:[] };
  } catch(e) { return { blockedPlates:[], blockedIds:[], wantedPlates:[], wantedIds:[] }; }
}
async function saveBlacklistsToFirebase(data) {
  try {
    await db.collection('settings').doc('blacklists').set(data);
  } catch(e) { console.error('Blacklist save error:', e); }
}

// ══════════════════════════════════════════════════════
// 📋 تحميل قائمة الشركات من Firebase
// ══════════════════════════════════════════════════════
let companyListLoaded = false;

// للتوافق مع الكود الموجود — نعرّف allUsers و employees كفارغة
const allUsers = {};
const employees = {};
const SUPERVISOR_CODES = {};
const GOOGLE_SCRIPT_URL = ''; // لم يعد مطلوباً — Firebase يتولى التخزين



const companyList = [
            "ABS",
            "ACES",
            "ACTS",
            "AECOM",
            "AL BAWANI",
            "AL FUWAILEH",
            "AL LAITH",
            "AL LAITH UNITED",
            "AL MAJAL",
            "ALABRAQI",
            "ALAJMI",
            "ALBAWANI",
            "ALFUWAYLIH",
            "ALHAMRA",
            "ALJABER",
            "ALKEFAH",
            "ALMAJAL",
            "ALTAAQA",
            "ALTAMIMI",
            "AMMICO",
            "APPLUS",
            "AQUARABIA",
            "ATKINS",
            "BBI",
            "BUJV",
            "BYRNE",
            "CITY SEC.",
            "CURRIE AND BROWN",
            "DAR ALHANDASAH",
            "DEWAN",
            "DOMOPAN",
            "DR.SULAIMAN HMC",
            "DSA",
            "EGIS",
            "ERGO",
            "ESA SP",
            "ESA WTP",
            "FATHIMA",
            "FEMCO",
            "FILM STUDIO",
            "FIRST C",
            "FMCO",
            "GCC",
            "HAIF",
            "HARBICO",
            "IBJV",
            "IDC",
            "JACOBS",
            "JASASRA",
            "JASH",
            "KTS",
            "LANDLABS",
            "MACE",
            "MAG",
            "MASCO",
            "MCY",
            "METALYAPI",
            "MHT",
            "MOBILY",
            "NABTAT",
            "NESMA & PARTNARS",
            "NESMA - PARTNER, FCC -UP",
            "NESMA JV",
            "NESMA PARTNER UP",
            "NESMA UNITED INDUSTRIES CO. LTD",
            "NEWFAB",
            "OLA HOTEL",
            "ONUR GROUP",
            "PARSONS",
            "PROPETY OWNER",
            "PWC MSI",
            "QIDDIYA EMPLOYEE",
            "QIDDIYA SECURITY",
            "RIYADH EXPRESS",
            "ROAFID ALAMN",
            "SAFARI",
            "SAFCO",
            "SAFE",
            "SAJCO",
            "SALCO",
            "SATCO",
            "SATMNA",
            "SAUDI BAUER",
            "SAUDI LANDSCAPE - SLC",
            "SAUDI SICLI",
            "SFQ BBI",
            "SHAMAL",
            "SIGNWORLD",
            "SISCO",
            "SIX FLAGS",
            "SLC",
            "SNC - TAKEEF (TSJV)",
            "SOG",
            "SSD",
            "SSH",
            "SUPPLIER",
            "SWORD OF GOD",
            "TAJ DHABI",
            "TAKEEF",
            "TAMEAR",
            "TAWAL",
            "TDP",
            "TFD",
            "TSJV TAKEEF",
            "UCC",
            "UNIBETON",
            "UNIMAC"
        ];


const violationCategories = {
            'مرورية': ['سرعة', 'عكس سير', 'عدم تأمين حمولة', 'وقوف خاطئ', 'عدم وجود رخصة', 'حمولة زائدة', 'القيادة بتهور', 'عدم ربط حزام أمان', 'استخدام جوال', 'عبور دوار بطريقة غير نظامية', 'سحب مركبة غير نظامي', 'أخرى..'],
            'جنائية': ['وثائق مزورة', 'تخريب', 'انتحال شخصية', 'رشوة', 'سرقة', 'اعتداء', 'صدم وهروب', 'أخرى..'],
            'عدم_التزام': ['عدم الالتزام بأدوات السلامة', 'عدم تجاوب', 'ورشة غير مصرح لها', 'تنقل على الأقدام', 'محاولة إدخال أشخاص غير مصرح لهم', 'بائع متجول', 'حراسة موقع لغير المصرح له', 'تصوير بدون تصريح', 'دخول غير نظامي', 'أخرى..'],
            'تصاريح': ['عدم وجود تصريح', 'عدم وجود استكر', 'تصريح منتهي', 'لايوجد تصريح', 'منع دخول']
        };

// ══════════════════════════════════════════════════════
// STATE
// ══════════════════════════════════════════════════════
let currentUser = null;
let currentLoginType = 'employee';
let violations = [];
let uploadedImages = [];
let currentLocation = null;
let map = null;
let marker = null;
let lastViolation = null;
let companyDropdownOpen = false;
let charts = {};

// ══════════════════════════════════════════════════════
// STORAGE (with error handling & backup)
// ══════════════════════════════════════════════════════
function saveToStorage() {
  // 🔥 حفظ المخالفة الأخيرة فقط في Firebase (يتم الحفظ مباشرة عند الإضافة/التعديل)
  // هذه الدالة تحافظ على التوافق مع الكود القديم
  updateBadge();
}
function loadFromStorage() {
  // 🔥 التحميل يتم من Firebase عبر loadDataFromFirebase()
  updateBadge();
}
async function loadDataFromFirebase() {
  try {
    showToast('⏳ جاري تحميل البيانات من السحابة...', 'success');
    violations = await loadViolationsFromFirebase();

    // تحميل قائمة الشركات من Firebase
    try {
      const compDoc = await db.collection('settings').doc('companies').get();
      if (compDoc.exists && compDoc.data().list) {
        companyList.length = 0;
        compDoc.data().list.forEach(c => companyList.push(c));
        companyListLoaded = true;
      }
    } catch(e) { console.warn('Company list load failed:', e); }

    // تحميل إيميلات الشركات
    companyEmails = await loadCompanyEmailsFromFirebase();

    // تحميل القوائم السوداء
    const bl = await loadBlacklistsFromFirebase();
    localStorage.setItem('blockedPlates', JSON.stringify(bl.blockedPlates || []));
    localStorage.setItem('blockedIds', JSON.stringify(bl.blockedIds || []));
    localStorage.setItem('wantedPlates', JSON.stringify(bl.wantedPlates || []));
    localStorage.setItem('wantedIds', JSON.stringify(bl.wantedIds || []));

    updateBadge();
    showToast(`✅ تم تحميل ${violations.length} مخالفة من السحابة`);
    
    // تحديث الواجهة
    if (typeof renderDashboard === 'function') renderDashboard();
    if (typeof renderAdminPanel === 'function' && currentUser?.role === 'admin') renderAdminPanel();
    if (typeof renderViolationList === 'function') {
      renderViolationList('pending','pendingList',true);
      renderViolationList('approved','approvedList',false);
      renderViolationList('rejected','rejectedList',false);
    }
  } catch(e) {
    console.error('❌ خطأ في تحميل البيانات:', e);
    showToast('❌ خطأ في تحميل البيانات — تأكد من الاتصال بالإنترنت', 'error');
  }
}
function updateBadge() {
  const el = document.getElementById('excelBadge');
  if (el) el.textContent = violations.length;
}

// ══════════════════════════════════════════════════════
// TOAST
// ══════════════════════════════════════════════════════
function showToast(msg, type='success') {
  const t = document.getElementById('toast');
  if (!t) return;
  t.textContent = msg;
  t.style.background = type==='error' ? 'rgba(239,68,68,0.15)' : 'rgba(16,185,129,0.15)';
  t.style.color = type==='error' ? '#f87171' : '#34d399';
  t.style.borderColor = type==='error' ? 'rgba(239,68,68,0.3)' : 'rgba(16,185,129,0.3)';
  t.style.display = 'block';
  clearTimeout(t._timer);
  t._timer = setTimeout(() => { t.style.display = 'none'; }, 4000);
}

// ══════════════════════════════════════════════════════
// 🛡️ XSS SANITIZATION
// ══════════════════════════════════════════════════════
function esc(str) {
  if (!str) return '';
  const div = document.createElement('div');
  div.textContent = String(str);
  return div.innerHTML;
}

// ══════════════════════════════════════════════════════
// MOBILE SIDEBAR
// ══════════════════════════════════════════════════════
function toggleSidebar() {
  const sidebar = document.getElementById('sidebar');
  const overlay = document.getElementById('sidebarOverlay');
  sidebar.classList.toggle('open');
  overlay.classList.toggle('show');
}
function closeSidebar() {
  document.getElementById('sidebar')?.classList.remove('open');
  document.getElementById('sidebarOverlay')?.classList.remove('show');
}

// ══════════════════════════════════════════════════════
// BOTTOM NAV (MOBILE)
// ══════════════════════════════════════════════════════
function buildBottomNav(role) {
  const nav = document.getElementById('mobileBottomNav');
  if (!nav) return;
  const items = (role === 'supervisor' || role === 'admin')
    ? [
        { id:'dashboard', icon:'📊', label:'إحصائيات' },
        { id:'pending',   icon:'⏳', label:'مراجعة', badge:'pendingCount' },
        { id:'approved',  icon:'✅', label:'موافق' },
        { id:'myViolations', icon:'📋', label:'السجل' },
        { id:'reports',   icon:'📈', label:'تقارير' },
      ]
    : [
        { id:'dashboard',    icon:'📊', label:'إحصائيات' },
        { id:'form',         icon:'📝', label:'تسجيل' },
        { id:'myViolations', icon:'📋', label:'السجل' },
      ];

  nav.innerHTML = items.map(item => `
    <button class="bottom-nav-item" id="bn_${item.id}" onclick="showTabMobile('${item.id}')">
      <div class="bottom-nav-wrap">
        <span class="bn-icon">${item.icon}</span>
        ${item.badge ? `<span class="bottom-nav-badge" id="${item.badge}" style="display:none;"></span>` : ''}
      </div>
      <span>${item.label}</span>
    </button>
  `).join('') + `
    <button class="bottom-nav-item" onclick="logout()" style="color:#f87171;">
      <div class="bottom-nav-wrap"><span class="bn-icon">🚪</span></div>
      <span>خروج</span>
    </button>`;
}

function showTabMobile(tab) {
  closeSidebar();
  showTab(tab);
  // Update bottom nav active
  document.querySelectorAll('.bottom-nav-item').forEach(btn => btn.classList.remove('active'));
  const active = document.getElementById('bn_'+tab);
  if (active) active.classList.add('active');
}

function updatePendingBadge() {
  const pending = violations.filter(v => v.status === 'pending');
  const badge = document.getElementById('pendingCount');
  if (badge) {
    badge.style.display = pending.length ? 'block' : 'none';
    badge.textContent = pending.length;
  }
}

// Override buildSidebarNav to also build bottom nav
const _origBuildSidebarNav = buildSidebarNav;

// ══════════════════════════════════════════════════════
// LOGIN
// ══════════════════════════════════════════════════════
// ══════════════════════════════════════════════════════
// 🔐 SECURITY: SHA-256 Hash + Rate Limiting + Session
// ══════════════════════════════════════════════════════
async function hashSHA256(text) {
  const encoder = new TextEncoder();
  const data = encoder.encode(text);
  const hashBuffer = await crypto.subtle.digest('SHA-256', data);
  return Array.from(new Uint8Array(hashBuffer)).map(b => b.toString(16).padStart(2,'0')).join('');
}

// Rate limiting: max 5 failed attempts in 3 minutes
let loginAttempts = [];
const MAX_ATTEMPTS = 5;
const LOCKOUT_MS = 3 * 60 * 1000;

function checkRateLimit() {
  const now = Date.now();
  loginAttempts = loginAttempts.filter(t => now - t < LOCKOUT_MS);
  return loginAttempts.length < MAX_ATTEMPTS;
}

// Session timeout: auto-logout after 30 min inactivity
let sessionTimer = null;
const SESSION_TIMEOUT = 30 * 60 * 1000;

function resetSessionTimer() {
  if (sessionTimer) clearTimeout(sessionTimer);
  if (currentUser) {
    sessionTimer = setTimeout(() => {
      showToast('⏰ تم تسجيل الخروج تلقائياً بسبب عدم النشاط', 'error');
      logout();
    }, SESSION_TIMEOUT);
  }
}
// Reset timer on user activity
['click','keypress','touchstart','scroll'].forEach(evt => {
  document.addEventListener(evt, () => { if(currentUser) resetSessionTimer(); }, {passive:true});
});

function switchLoginTab(type) {
  currentLoginType = type;
  document.getElementById('empTab').classList.toggle('active', type==='employee');
  document.getElementById('supTab').classList.toggle('active', type==='supervisor');
  const adminTab = document.getElementById('adminTab');
  if (adminTab) adminTab.classList.toggle('active', type==='admin');
  document.getElementById('supervisorCodeField').style.display = type==='supervisor' ? 'block' : 'none';
  const empIdField = document.getElementById('employeeId');
  if (type === 'admin') {
    empIdField.placeholder = 'ADMIN';
  } else {
    empIdField.placeholder = 'أدخل الرقم الوظيفي';
  }
  if (type==='employee' || type==='admin') document.getElementById('supervisorCode').value = '';
}

async function login() {
  const empId = document.getElementById('employeeId').value.trim();
  const password = document.getElementById('userPassword').value.trim();
  const errDiv = document.getElementById('loginError');
  errDiv.style.display = 'none';

  // Rate limit check
  if (!checkRateLimit()) {
    const waitSec = Math.ceil((LOCKOUT_MS - (Date.now() - loginAttempts[0])) / 1000);
    errDiv.textContent = `🔒 تم قفل تسجيل الدخول مؤقتاً. حاول بعد ${waitSec} ثانية`;
    errDiv.style.display = 'block'; return;
  }

  if (!empId || !password) {
    errDiv.textContent = '❌ الرجاء إدخال الرقم الوظيفي والرقم السري';
    errDiv.style.display = 'block'; return;
  }

  // Show loading
  const loginBtn = document.querySelector('.login-btn');
  const originalText = loginBtn ? loginBtn.textContent : '';
  if (loginBtn) { loginBtn.textContent = '⏳ جاري التحقق...'; loginBtn.disabled = true; }

  // 🔥 Firebase Authentication
  const result = await authenticateUser(empId, password, currentLoginType);
  
  if (loginBtn) { loginBtn.textContent = originalText; loginBtn.disabled = false; }

  if (!result.success) {
    loginAttempts.push(Date.now());
    errDiv.textContent = '❌ ' + result.error;
    errDiv.style.display = 'block'; return;
  }

  loginAttempts = [];
  const u = result.user;
  currentUser = { id: u.id, name: u.name, role: u.role, group: u.group };
  
  document.getElementById('loginPage').classList.add('hide');
  document.getElementById('mainApp').classList.remove('hide');
  document.getElementById('userName').textContent = currentUser.name;
  document.getElementById('userRole').textContent =
    u.role === 'admin'      ? '🛡️ مراقب — Admin' :
    u.role === 'supervisor' ? '👔 مشرف — ' + (u.group || '') :
                               '👤 موظف';

  buildSidebarNav(u.role);
  resetSessionTimer();
  if (u.role === 'supervisor' || u.role === 'admin') {
    document.getElementById('excelBtn').style.display = 'flex';
  }
  
  // 🔥 تحميل البيانات من Firebase أولاً ثم عرض الصفحة
  await loadDataFromFirebase();
  
  // 🔔 تحميل الإشعارات الخاصة بي
  await loadMyNotifications();
  
  // عرض الصفحة المناسبة بعد تحميل البيانات
  if (u.role === 'admin') {
    showTab('adminPanel');
  } else if (u.role === 'supervisor') {
    showTab('dashboard');
  } else {
    showTab('form');
  }
}

function logout() {
  currentUser = null;
  currentLoginType = 'employee';
  if (sessionTimer) { clearTimeout(sessionTimer); sessionTimer = null; }
  // إخفاء كل الـ tabs أولاً
  const tabs = ['dashboard','form','myViolations','pending','approved','rejected','reports','waSettings','adminPanel','liveMap','aiAnalysis','performance'];
  tabs.forEach(t => {
    const el = document.getElementById(t+'Tab');
    if (el) el.classList.add('hide');
  });
  document.getElementById('mainApp').classList.add('hide');
  document.getElementById('loginPage').classList.remove('hide');
  document.getElementById('employeeId').value = '';
  document.getElementById('userPassword').value = '';
  document.getElementById('supervisorCode').value = '';
  document.getElementById('supervisorCodeField').style.display = 'none';
  document.getElementById('empTab').classList.add('active');
  document.getElementById('supTab').classList.remove('active');
  const aTab = document.getElementById('adminTab');
  if (aTab) aTab.classList.remove('active');
  document.getElementById('employeeId').placeholder = 'أدخل الرقم الوظيفي';
  document.getElementById('excelBtn').style.display = 'none';
  // destroy charts & map
  Object.values(charts).forEach(c => { try { if(c) c.destroy(); } catch(e){} });
  charts = {};
  if (mapInstance) { try { mapInstance.remove(); } catch(e){} mapInstance = null; }
  // scroll to top
  window.scrollTo(0, 0);
}

// ══════════════════════════════════════════════════════
// SIDEBAR NAV
// ══════════════════════════════════════════════════════
const employeeNavItems = [
  { id:'dashboard', icon:'📊', label:'الإحصائيات' },
  { id:'form', icon:'📝', label:'تسجيل مخالفة' },
  { id:'myViolations', icon:'📋', label:'سجل المخالفات' },
];
const supervisorNavItems = [
  { id:'dashboard', icon:'📊', label:'الإحصائيات' },
  { id:'liveMap', icon:'🗺️', label:'خريطة المخالفات' },
  { id:'reports', icon:'📈', label:'التقارير الدورية' },
  { id:'pending', icon:'⏳', label:'قيد المراجعة' },
  { id:'approved', icon:'✅', label:'موافق عليها' },
  { id:'rejected', icon:'❌', label:'مرفوضة' },
  { id:'myViolations', icon:'📋', label:'كل المخالفات' },
  { id:'waSettings', icon:'💬', label:'إعدادات الإشعارات' },
];
const adminNavItems = [
  { id:'dashboard', icon:'📊', label:'الإحصائيات الكاملة' },
  { id:'liveMap', icon:'🗺️', label:'خريطة المخالفات' },
  { id:'aiAnalysis', icon:'🤖', label:'تحليل ذكي AI' },
  { id:'performance', icon:'🏆', label:'تقييم الأداء' },
  { id:'reports',   icon:'📈', label:'التقارير الدورية' },
  { id:'pending',   icon:'⏳', label:'قيد المراجعة' },
  { id:'approved',  icon:'✅', label:'موافق عليها' },
  { id:'rejected',  icon:'❌', label:'مرفوضة' },
  { id:'myViolations', icon:'📋', label:'كل المخالفات' },
  { id:'waSettings',icon:'💬', label:'إعدادات الإشعارات' },
  { id:'adminPanel',icon:'🛡️', label:'لوحة المراقب' },
];

function buildSidebarNav(role) {
  const items = role === 'admin' ? adminNavItems : role === 'supervisor' ? supervisorNavItems : employeeNavItems;
  const nav = document.getElementById('sidebarNav');
  nav.innerHTML = items.map(item => `
    <div class="nav-item" id="nav_${item.id}" onclick="showTabMobile('${item.id}')">
      <span class="nav-icon" style="font-size:22px;">${item.icon}</span>
      <span>${item.label}</span>
    </div>
  `).join('');
  buildBottomNav(role);
}

function showTab(tab) {
  const tabs = ['dashboard','form','myViolations','pending','approved','rejected','reports','waSettings','adminPanel','liveMap','aiAnalysis','performance'];
  tabs.forEach(t => {
    const el = document.getElementById(t+'Tab');
    if (el) el.classList.add('hide');
    const nav = document.getElementById('nav_'+t);
    if (nav) nav.classList.remove('active');
    const bn = document.getElementById('bn_'+t);
    if (bn) bn.classList.remove('active');
  });
  const target = document.getElementById(tab+'Tab');
  if (target) target.classList.remove('hide');
  const navItem = document.getElementById('nav_'+tab);
  if (navItem) navItem.classList.add('active');
  const bnItem = document.getElementById('bn_'+tab);
  if (bnItem) bnItem.classList.add('active');

  if (tab === 'dashboard') renderDashboard();
  else if (tab === 'myViolations') { applyFilters(); }
  else if (tab === 'pending') { renderViolationList('pending', 'pendingList', true); updatePendingBadge(); }
  else if (tab === 'approved') renderViolationList('approved', 'approvedList', false);
  else if (tab === 'rejected') renderViolationList('rejected', 'rejectedList', false);
  else if (tab === 'reports') renderReports('today');
  else if (tab === 'waSettings') { loadWASettings(); }
  else if (tab === 'adminPanel') { renderAdminPanel(); loadCompanyEmailSettings(); renderBlacklists(); window.scrollTo(0,0); }
  else if (tab === 'liveMap') { renderLiveMap(); }
  else if (tab === 'aiAnalysis') { renderAIAnalysis(); }
  else if (tab === 'performance') { renderPerformance(); }
  updatePendingBadge();
}

// ══════════════════════════════════════════════════════
// DASHBOARD
// ══════════════════════════════════════════════════════
function renderDashboard() {
  // فلترة حسب الصلاحية:
  // - الموظف (المفتش): يرى فقط مخالفاته الشخصية
  // - المشرف: يرى مخالفات مجموعته فقط
  // - الأدمن: يرى الكل
  let data = violations;
  if (currentUser && currentUser.role === 'employee') {
    data = data.filter(v => v.inspectorId === currentUser.id);
  } else if (currentUser && currentUser.role === 'supervisor' && currentUser.group) {
    data = data.filter(v => v.receivingGroup === currentUser.group);
  }

  // تحديث عنوان اللوحة حسب الصلاحية
  const subtitle = document.getElementById('dashboardSubtitle');
  if (subtitle) {
    if (currentUser?.role === 'employee') subtitle.textContent = '📌 إحصائياتي الشخصية — المخالفات التي سجلتها';
    else if (currentUser?.role === 'supervisor') subtitle.textContent = `📌 إحصائيات المجموعة: ${currentUser.group}`;
    else subtitle.textContent = 'نظرة شاملة على جميع المخالفات المسجلة';
  }

  // فلتر حسب النوع الرئيسي
  const typeFilter = document.getElementById('dashFilterType')?.value || '';
  if (typeFilter) {
    data = data.filter(v => v.mainViolationType === typeFilter);
  }

  const total    = data.length;
  const pending  = data.filter(v => v.status==='pending').length;
  const approved = data.filter(v => v.status==='approved').length;
  const rejected = data.filter(v => v.status==='rejected').length;
  const today    = new Date().toLocaleDateString('ar-SA');
  const todayCount = data.filter(v => v.date === today).length;
  const approvalRate = total > 0 ? Math.round((approved / total) * 100) : 0;

  // KPI cards
  renderKPIs(data);

  // بطاقات الإحصائيات
  document.getElementById('statCards').innerHTML = `
    <div class="stat-card gold"><div class="stat-icon">📋</div><div class="stat-value">${total}</div><div class="stat-label">إجمالي المخالفات</div></div>
    <div class="stat-card amber"><div class="stat-icon">⏳</div><div class="stat-value">${pending}</div><div class="stat-label">قيد المراجعة</div></div>
    <div class="stat-card green"><div class="stat-icon">✅</div><div class="stat-value">${approved}</div><div class="stat-label">موافق عليها</div></div>
    <div class="stat-card red"><div class="stat-icon">❌</div><div class="stat-value">${rejected}</div><div class="stat-label">مرفوضة</div></div>
    <div class="stat-card blue"><div class="stat-icon">📅</div><div class="stat-value">${todayCount}</div><div class="stat-label">مخالفات اليوم</div></div>
    <div class="stat-card" style="--accent:rgba(168,85,247,0.15);border-color:rgba(168,85,247,0.3);"><div class="stat-icon">📈</div><div class="stat-value">${approvalRate}%</div><div class="stat-label">نسبة الموافقة</div></div>
  `;

  const chartDefaults = {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { labels: { color:'#e2e8f0', font:{ family:'Tajawal', size:11 } } } }
  };
  const barScales = { scales:{ x:{ticks:{color:'#94a3b8',font:{size:10}},grid:{color:'rgba(255,255,255,0.05)'}}, y:{ticks:{color:'#94a3b8',font:{size:10}},grid:{color:'rgba(255,255,255,0.05)'}} } };
  const colors8 = ['rgba(212,175,55,0.8)','rgba(239,68,68,0.8)','rgba(16,185,129,0.8)','rgba(59,130,246,0.8)','rgba(168,85,247,0.8)','rgba(245,158,11,0.8)','rgba(236,72,153,0.8)','rgba(14,165,233,0.8)'];

  // ── النوع الرئيسي (doughnut)
  const byType = {};
  data.forEach(v => { byType[v.mainViolationTypeLabel] = (byType[v.mainViolationTypeLabel]||0)+1; });
  renderChart('chartByType', 'doughnut', Object.keys(byType), Object.values(byType), colors8, chartDefaults);

  // ── النوع الفرعي (horizontal bar - أعلى 12)
  const bySubType = {};
  data.forEach(v => { if(v.subViolationType) bySubType[v.subViolationType] = (bySubType[v.subViolationType]||0)+1; });
  const topSub = Object.entries(bySubType).sort((a,b)=>b[1]-a[1]).slice(0,12);
  renderChart('chartBySubType', 'bar', topSub.map(s=>s[0]), topSub.map(s=>s[1]), colors8,
    {...chartDefaults, indexAxis:'y', plugins:{...chartDefaults.plugins, legend:{display:false}}, ...barScales});

  // ── حالة المخالفات (bar)
  renderChart('chartByStatus', 'bar', ['قيد المراجعة','موافق عليها','مرفوضة'], [pending, approved, rejected],
    ['rgba(245,158,11,0.8)','rgba(16,185,129,0.8)','rgba(239,68,68,0.8)'], {...chartDefaults, ...barScales});

  // ── حسب المنطقة (bar)
  const byZone = {};
  data.forEach(v => { if(v.zone) byZone[v.zone]=(byZone[v.zone]||0)+1; });
  renderChart('chartByZone', 'bar', Object.keys(byZone), Object.values(byZone), colors8, {...chartDefaults, ...barScales});

  // ── حسب الشفت (doughnut)
  const byShift = {};
  data.forEach(v => { if(v.shift) byShift['Shift '+v.shift]=(byShift['Shift '+v.shift]||0)+1; });
  renderChart('chartByShift', 'doughnut', Object.keys(byShift), Object.values(byShift), ['#ffd700','#10b981','#3b82f6'], chartDefaults);

  // ── حسب المجموعة (bar)
  const byGroup = {};
  data.forEach(v => { if(v.receivingGroup) byGroup[v.receivingGroup]=(byGroup[v.receivingGroup]||0)+1; });
  renderChart('chartByGroup', 'bar', Object.keys(byGroup), Object.values(byGroup), colors8, {...chartDefaults, ...barScales});

  // ── أكثر الشركات (horizontal bar - أعلى 10)
  const coCounts = {};
  data.forEach(v => { if(v.companyName) coCounts[v.companyName]=(coCounts[v.companyName]||0)+1; });
  const topCoChart = Object.entries(coCounts).sort((a,b)=>b[1]-a[1]).slice(0,10);
  renderChart('chartByCompany', 'bar', topCoChart.map(c=>c[0]), topCoChart.map(c=>c[1]), colors8,
    {...chartDefaults, indexAxis:'y', plugins:{...chartDefaults.plugins, legend:{display:false}}, ...barScales});

  // ── مخالفات الشركات حسب النوع (stacked bar - أعلى 8 شركات)
  const topCoNames = topCoChart.slice(0,8).map(c=>c[0]);
  const typeLabels = [...new Set(data.map(v=>v.mainViolationTypeLabel))];
  const stackedDatasets = typeLabels.map((type, i) => ({
    label: type,
    data: topCoNames.map(co => data.filter(v => v.companyName===co && v.mainViolationTypeLabel===type).length),
    backgroundColor: colors8[i % colors8.length],
    borderRadius: 4,
  }));
  if (charts['chartCompanyByType']) charts['chartCompanyByType'].destroy();
  const ctxCT = document.getElementById('chartCompanyByType')?.getContext('2d');
  if (ctxCT) {
    charts['chartCompanyByType'] = new Chart(ctxCT, {
      type: 'bar',
      data: { labels: topCoNames, datasets: stackedDatasets },
      options: {
        ...chartDefaults, indexAxis:'y',
        plugins: { ...chartDefaults.plugins, legend: { position:'bottom', labels: { color:'#e2e8f0', font:{family:'Tajawal',size:10} } } },
        scales: {
          x: { stacked:true, ticks:{color:'#94a3b8'}, grid:{color:'rgba(255,255,255,0.05)'} },
          y: { stacked:true, ticks:{color:'#94a3b8',font:{size:10}}, grid:{color:'rgba(255,255,255,0.05)'} }
        }
      }
    });
  }

  // ── قوائم أعلى الشركات والمفتشين
  const topCo = Object.entries(coCounts).sort((a,b)=>b[1]-a[1]).slice(0,5);
  const maxCo = topCo[0]?.[1] || 1;
  document.getElementById('topCompanies').innerHTML = topCo.length
    ? topCo.map(([name,count],i) => `<li><div class="top-rank">${i+1}</div><div class="top-name">${name}</div><div class="top-bar-wrap"><div class="top-bar" style="width:${(count/maxCo)*100}%"></div></div><div class="top-count">${count}</div></li>`).join('')
    : '<li style="color:var(--muted);padding:20px 0;text-align:center;">لا توجد بيانات</li>';

  const insCounts = {};
  data.forEach(v => { if(v.inspector) insCounts[v.inspector]=(insCounts[v.inspector]||0)+1; });
  const topIns = Object.entries(insCounts).sort((a,b)=>b[1]-a[1]).slice(0,5);
  const maxIns = topIns[0]?.[1] || 1;
  document.getElementById('topInspectors').innerHTML = topIns.length
    ? topIns.map(([name,count],i) => `<li><div class="top-rank">${i+1}</div><div class="top-name">${name}</div><div class="top-bar-wrap"><div class="top-bar" style="width:${(count/maxIns)*100}%"></div></div><div class="top-count">${count}</div></li>`).join('')
    : '<li style="color:var(--muted);padding:20px 0;text-align:center;">لا توجد بيانات</li>';

  // ── جدول تفصيلي: النوع الرئيسي × الفرعي × الشركات
  const typeMap = {};
  data.forEach(v => {
    const main = v.mainViolationTypeLabel || 'غير محدد';
    const sub = v.subViolationType || 'غير محدد';
    if (!typeMap[main]) typeMap[main] = {};
    if (!typeMap[main][sub]) typeMap[main][sub] = { count:0, companies:{} };
    typeMap[main][sub].count++;
    const co = v.companyName || 'غير محدد';
    typeMap[main][sub].companies[co] = (typeMap[main][sub].companies[co]||0) + 1;
  });

  let tableHtml = `<table style="width:100%;border-collapse:collapse;font-size:13px;">
    <thead><tr style="background:rgba(212,175,55,0.15);">
      <th style="padding:12px;border:1px solid rgba(212,175,55,0.2);color:var(--gold);text-align:right;">النوع الرئيسي</th>
      <th style="padding:12px;border:1px solid rgba(212,175,55,0.2);color:var(--gold);text-align:right;">النوع الفرعي</th>
      <th style="padding:12px;border:1px solid rgba(212,175,55,0.2);color:var(--gold);text-align:center;">العدد</th>
      <th style="padding:12px;border:1px solid rgba(212,175,55,0.2);color:var(--gold);text-align:right;">أكثر الشركات</th>
    </tr></thead><tbody>`;

  const mainTypes = Object.entries(typeMap).sort((a,b) => {
    const totalA = Object.values(a[1]).reduce((s,v)=>s+v.count,0);
    const totalB = Object.values(b[1]).reduce((s,v)=>s+v.count,0);
    return totalB - totalA;
  });

  mainTypes.forEach(([main, subs]) => {
    const sortedSubs = Object.entries(subs).sort((a,b) => b[1].count - a[1].count);
    const mainTotal = sortedSubs.reduce((s,v) => s+v[1].count, 0);
    sortedSubs.forEach(([sub, info], i) => {
      const topCos = Object.entries(info.companies).sort((a,b)=>b[1]-a[1]).slice(0,3).map(([n,c])=>`${n} (${c})`).join('، ');
      tableHtml += `<tr style="border-bottom:1px solid rgba(255,255,255,0.05);${i===0?'border-top:2px solid rgba(212,175,55,0.3);':''}">
        ${i===0 ? `<td rowspan="${sortedSubs.length}" style="padding:10px;border:1px solid rgba(212,175,55,0.15);font-weight:700;color:var(--gold-light);vertical-align:top;">${main}<br><span style="font-size:11px;color:var(--muted);">(${mainTotal})</span></td>` : ''}
        <td style="padding:10px;border:1px solid rgba(212,175,55,0.1);">${sub}</td>
        <td style="padding:10px;border:1px solid rgba(212,175,55,0.1);text-align:center;font-weight:700;color:var(--gold);">${info.count}</td>
        <td style="padding:10px;border:1px solid rgba(212,175,55,0.1);font-size:12px;color:var(--muted);">${topCos}</td>
      </tr>`;
    });
  });

  tableHtml += '</tbody></table>';
  document.getElementById('subTypeTable').innerHTML = data.length ? tableHtml : '<div style="text-align:center;color:var(--muted);padding:30px;">لا توجد بيانات</div>';

  // Activity Timeline
  renderTimeline(data);
}

function renderChart(id, type, labels, data, colors, opts) {
  if (charts[id]) { charts[id].destroy(); }
  const ctx = document.getElementById(id)?.getContext('2d');
  if (!ctx) return;
  const finalOpts = {...opts};
  if (opts.indexAxis) { finalOpts.indexAxis = opts.indexAxis; }
  charts[id] = new Chart(ctx, {
    type,
    data: {
      labels,
      datasets:[{
        data,
        backgroundColor: colors,
        borderColor: type==='doughnut' ? 'rgba(0,0,0,0.2)' : colors,
        borderWidth: 1, borderRadius: type==='bar' ? 6 : 0,
      }]
    },
    options: finalOpts
  });
}

// ══════════════════════════════════════════════════════
// REPEAT OFFENDER DETECTION
// ══════════════════════════════════════════════════════
function getRepeatInfo(v, excludeSelf = true) {
  const all = excludeSelf ? violations.filter(x => x.refNumber !== v.refNumber) : violations;
  const result = { plate: [], id: [] };
  // تتبع اللوحة والهوية فقط — الشركات مستثناة
  if (v.vehiclePlate && v.vehiclePlate.trim()) {
    result.plate = all.filter(x => x.vehiclePlate && x.vehiclePlate.trim().toUpperCase() === v.vehiclePlate.trim().toUpperCase());
  }
  if (v.violatorId && v.violatorId.trim()) {
    result.id = all.filter(x => x.violatorId && x.violatorId.trim() === v.violatorId.trim());
  }
  return result;
}

function repeatWarningHTML(v) {
  const r = getRepeatInfo(v);
  const lines = [];
  if (r.plate.length) {
    const dates = r.plate.map(x=>`${x.date} (${x.mainViolationTypeLabel||x.subViolationType})`).join(' | ');
    lines.push(`<div class="rw-item">🚗 لوحة المركبة <span>${v.vehiclePlate}</span> سُجِّلت مخالفة قبل ذلك: <span>${r.plate.length}x</span> — ${dates}</div>`);
  }
  if (r.id.length) {
    const dates = r.id.map(x=>`${x.date} (${x.mainViolationTypeLabel||x.subViolationType})`).join(' | ');
    lines.push(`<div class="rw-item">🪪 رقم الهوية <span>${v.violatorId}</span> سُجِّل مخالفة سابقاً: <span>${r.id.length}x</span> — ${dates}</div>`);
  }

  if (!lines.length) return '';
  return `<div class="repeat-warning"><div class="rw-title">⚠️ مخالف متكرر!</div>${lines.join('')}</div>`;
}

function repeatBadgesHTML(v) {
  const r = getRepeatInfo(v);
  let html = '';
  if (r.plate.length)   html += `<span class="repeat-badge plate">🚗 لوحة ×${r.plate.length}</span>`;
  if (r.id.length)      html += `<span class="repeat-badge id">🪪 هوية ×${r.id.length}</span>`;

  return html;
}

// تحقق عند إدخال بيانات النموذج
function checkRepeatOnForm() {
  const plate = (document.getElementById('vehiclePlate')?.value || '').trim().toUpperCase();
  const id    = (document.getElementById('violatorId')?.value || '').trim();
  const alerts = [];

  if (plate) {
    const m = violations.filter(x => x.vehiclePlate && x.vehiclePlate.trim().toUpperCase() === plate);
    if (m.length) alerts.push(`🚗 لوحة المركبة <b>${plate}</b> مسجلة ${m.length} مخالفة سابقة — آخرها: ${m[m.length-1].date}`);
  }
  if (id) {
    const m = violations.filter(x => x.violatorId && x.violatorId.trim() === id);
    if (m.length) alerts.push(`🪪 رقم الهوية <b>${id}</b> مسجل ${m.length} مخالفة سابقة — آخرها: ${m[m.length-1].date}`);
  }


  const alertEl = document.getElementById('formRepeatAlert');
  if (!alertEl) return;
  if (alerts.length) {
    alertEl.innerHTML = '⚠️ تنبيه مخالف متكرر!<br>' + alerts.join('<br>');
    alertEl.style.display = 'block';
  } else {
    alertEl.style.display = 'none';
  }
}

// ══════════════════════════════════════════════════════
// VIOLATION CARD RENDER
// ══════════════════════════════════════════════════════
function renderViolationList(status, containerId, canReview) {
  let filtered = status ? violations.filter(v=>v.status===status) : violations;
  // فلترة حسب مجموعة المشرف (الأدمن يرى الكل)
  if (currentUser && currentUser.role === 'supervisor' && currentUser.group) {
    filtered = filtered.filter(v => v.receivingGroup === currentUser.group);
  }
  // التأكد من صلاحية الموافقة/الرفض
  canReview = canReview && currentUser && (currentUser.role === 'supervisor' || currentUser.role === 'admin');
  renderCards(filtered, containerId, canReview);
}

function renderCards(list, containerId, canReview) {
  const div = document.getElementById(containerId);
  if (!list.length) {
    div.innerHTML = '<div style="text-align:center;color:var(--muted);padding:48px;">لا توجد مخالفات</div>';
    return;
  }
  div.innerHTML = list.map(v => {
    const statusClass = v.status==='approved'?'approved':v.status==='rejected'?'rejected':'pending';
    const statusText  = v.status==='approved'?'✅ موافق عليها':v.status==='rejected'?'❌ مرفوضة':'⏳ قيد المراجعة';
    const mapsLink    = v.location ? `https://www.google.com/maps?q=${v.location.lat},${v.location.lng}` : null;
    const repeatBadges = repeatBadgesHTML(v);
    const repeatWarn   = repeatWarningHTML(v);
    return `
    <div class="v-card" ${repeatBadges ? 'style="border-color:rgba(239,68,68,0.4);"' : ''}>
      <div class="v-card-header">
        <div class="v-ref">${v.refNumber}${repeatBadges ? `<span style="margin-right:8px;">${repeatBadges}</span>` : ''}</div>
        <div class="v-status ${statusClass}">${statusText}</div>
      </div>
      ${repeatWarn}
      <div class="v-details">
        <div class="v-detail"><strong>نوع المخالفة</strong>${v.mainViolationTypeLabel}</div>
        <div class="v-detail"><strong>التفاصيل</strong>${v.subViolationType}</div>
        <div class="v-detail"><strong>الشركة</strong>${v.companyName}</div>
        <div class="v-detail"><strong>المنطقة</strong>${v.zone}</div>
        <div class="v-detail"><strong>الشفت</strong>${v.shift} | ${v.violationTime}</div>
        <div class="v-detail"><strong>نوع التصريح</strong>${v.permitType}</div>
        <div class="v-detail"><strong>رقم الهوية</strong>${v.violatorId}</div>
        <div class="v-detail"><strong>رقم الجوال</strong>${v.violatorPhone}</div>
        ${v.vehiclePlate ? `<div class="v-detail"><strong>لوحة المركبة</strong>${v.vehiclePlate}</div>` : ''}
        ${v.personCount  ? `<div class="v-detail"><strong>عدد الأشخاص</strong>${v.personCount}</div>` : ''}
        ${v.receivingGroup ? `<div class="v-detail"><strong>المجموعة</strong>${v.receivingGroup}</div>` : ''}
        <div class="v-detail"><strong>المفتش</strong>${v.inspector} (${v.inspectorId})</div>
        <div class="v-detail"><strong>التاريخ والوقت</strong>${v.date} — ${v.time}</div>
        ${mapsLink ? `<div class="v-detail" style="grid-column:1/-1;"><strong>الموقع</strong><a href="${mapsLink}" target="_blank" style="color:#34d399;">🗺️ عرض على الخريطة</a></div>` : ''}
        ${v.reviewedBy ? `<div class="v-detail"><strong>المشرف</strong>${v.reviewedBy}</div><div class="v-detail"><strong>تاريخ المراجعة</strong>${v.reviewDate}</div>` : ''}
        ${v.reviewNotes ? `<div class="v-detail" style="grid-column:1/-1;"><strong>ملاحظات المشرف</strong>${v.reviewNotes}</div>` : ''}
      </div>
      ${(v.images && v.images.length) ? `
      <div style="padding:0 14px 10px;">
        <div style="font-size:11px;font-weight:700;color:var(--gold);margin-bottom:6px;">📸 صور المخالفة (${v.images.length})</div>
        <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:6px;">
          ${v.images.slice(0,3).map((src,i)=>`
            <div style="position:relative;border-radius:8px;overflow:hidden;border:1px solid var(--border);cursor:pointer;" onclick="openImageZoom('${src.replace(/'/g,"\\'")}')">
              <img src="${src}" style="width:100%;height:120px;object-fit:cover;display:block;">
              <div style="position:absolute;bottom:2px;right:2px;background:rgba(0,0,0,0.6);color:var(--gold);font-size:9px;padding:1px 5px;border-radius:3px;">🔍 ${i+1}</div>
            </div>`).join('')}
        </div>
      </div>` : ''}
      <div class="v-actions">
        <button class="btn-sm btn-pdf" onclick="sharePDFByIndex('${v.refNumber}')">📄 PDF</button>
        ${canReview ? `
          <input type="text" class="v-notes-input" data-ref="${v.refNumber}" placeholder="ملاحظات (اختياري للموافقة، إجباري للرفض)..." style="flex:1;">
          <button class="btn-sm btn-approve" onclick="approveViolation(this)" data-ref="${v.refNumber}">✅ موافقة</button>
          <button class="btn-sm btn-reject"  onclick="rejectViolation(this)"  data-ref="${v.refNumber}">❌ رفض</button>
        ` : ''}
      </div>
    </div>`;
  }).join('');
}

// ══════════════════════════════════════════════════════
// ADVANCED FILTER
// ══════════════════════════════════════════════════════
function applyFilters() {
  const q      = (document.getElementById('searchInput')?.value || '').toLowerCase();
  const status = document.getElementById('filterStatus')?.value || '';
  const zone   = document.getElementById('filterZone')?.value || '';
  const shift  = document.getElementById('filterShift')?.value || '';
  const group  = document.getElementById('filterGroup')?.value || '';
  const type   = document.getElementById('filterType')?.value || '';
  const dateFrom = document.getElementById('filterDateFrom')?.value || '';
  const dateTo   = document.getElementById('filterDateTo')?.value || '';

  let list = violations;
  // الموظف يرى مخالفاته فقط
  if (currentUser?.role === 'employee') {
    list = list.filter(v => v.inspectorId === currentUser.id);
  } else if (currentUser?.role === 'supervisor' && currentUser.group) {
    list = list.filter(v => v.receivingGroup === currentUser.group);
  }
  if (q)      list = list.filter(v =>
    v.refNumber.toLowerCase().includes(q) ||
    v.violatorId.includes(q) ||
    v.violatorPhone.includes(q) ||
    (v.companyName||'').toLowerCase().includes(q) ||
    (v.inspector||'').toLowerCase().includes(q) ||
    (v.subViolationType||'').toLowerCase().includes(q) ||
    (v.vehiclePlate||'').toLowerCase().includes(q) ||
    (v.mainViolationTypeLabel||'').toLowerCase().includes(q) ||
    (v.zone||'').toLowerCase().includes(q) ||
    (v.receivingGroup||'').toLowerCase().includes(q));
  if (status) list = list.filter(v => v.status === status);
  if (zone)   list = list.filter(v => v.zone === zone);
  if (shift)  list = list.filter(v => v.shift === shift);
  if (group)  list = list.filter(v => v.receivingGroup === group);
  if (type)   list = list.filter(v => v.mainViolationType === type);

  // Date range filter
  if (dateFrom || dateTo) {
    list = list.filter(v => {
      try {
        // Parse Arabic date or ISO date
        const parts = v.date?.split('/');
        let vDate;
        if (parts && parts.length === 3) {
          // Try parsing as d/m/y or similar Arabic format
          vDate = new Date(v.date);
          if (isNaN(vDate)) vDate = null;
        } else {
          vDate = new Date(v.date);
        }
        if (!vDate || isNaN(vDate)) return true; // keep if can't parse
        const vDateStr = vDate.toISOString().split('T')[0];
        if (dateFrom && vDateStr < dateFrom) return false;
        if (dateTo && vDateStr > dateTo) return false;
        return true;
      } catch(e) { return true; }
    });
  }

  const countEl = document.getElementById('filterCount');
  const totalCount = violations.length;
  if (countEl) countEl.innerHTML = `<span style="font-weight:700;">نتائج البحث: ${list.length}</span> من ${totalCount} مخالفة${q?` — "<span class="search-highlight">${q}</span>"` :''}`;
  renderCards(list, 'violationsList', false);
}

function resetFilters() {
  ['searchInput','filterStatus','filterZone','filterShift','filterGroup','filterType','filterDateFrom','filterDateTo']
    .forEach(id => { const el = document.getElementById(id); if(el) el.value=''; });
  // Reset chip highlights
  ['chipStatus','chipZone','chipShift','chipGroup','chipType'].forEach(id => {
    const el = document.getElementById(id); if(el) el.classList.remove('active');
  });
  applyFilters();
}

function updateChipActive(chipId, selectId) {
  const chip = document.getElementById(chipId);
  const sel = document.getElementById(selectId);
  if (chip && sel) {
    chip.classList.toggle('active', sel.value !== '');
  }
}

// Keep old name for compatibility
function searchViolations() { applyFilters(); }

// ══════════════════════════════════════════════════════
// WHATSAPP NOTIFICATIONS
// ══════════════════════════════════════════════════════
function saveWASettings() {
  const waEnabled = document.getElementById('waEnabled')?.checked || false;
  const emailEnabled = document.getElementById('emailEnabled')?.checked || false;
  const notifWhatsApp = document.getElementById('notifWhatsApp')?.checked || false;
  const notifEmail = document.getElementById('notifEmail')?.checked || false;
  const template = document.getElementById('waTemplate')?.value || '';
  const emailTemplate = document.getElementById('emailTemplate')?.value || '';
  const nums = [];
  document.querySelectorAll('.wa-number-input').forEach(el => {
    if (el.value.trim()) nums.push(el.value.trim());
  });
  const emails = [];
  document.querySelectorAll('.email-input').forEach(el => {
    if (el.value.trim()) emails.push(el.value.trim());
  });
  localStorage.setItem('waSettings', JSON.stringify({
    enabled: waEnabled, template, nums,
    emailEnabled, emailTemplate, emails,
    notifWhatsApp, notifEmail
  }));
  showToast('✅ تم حفظ إعدادات الإشعارات');
}

function loadWASettings() {
  try {
    const saved = JSON.parse(localStorage.getItem('waSettings') || '{}');
    if (document.getElementById('waEnabled')) document.getElementById('waEnabled').checked = saved.enabled || false;
    if (document.getElementById('emailEnabled')) document.getElementById('emailEnabled').checked = saved.emailEnabled || false;
    if (document.getElementById('notifWhatsApp')) document.getElementById('notifWhatsApp').checked = saved.notifWhatsApp !== false;
    if (document.getElementById('notifEmail')) document.getElementById('notifEmail').checked = saved.notifEmail || false;
    if (saved.template && document.getElementById('waTemplate')) document.getElementById('waTemplate').value = saved.template;
    if (saved.emailTemplate && document.getElementById('emailTemplate')) document.getElementById('emailTemplate').value = saved.emailTemplate;
    if (saved.nums && saved.nums.length) {
      const container = document.getElementById('waNumbers');
      if (container) {
        container.innerHTML = '';
        saved.nums.forEach((num, i) => {
          container.innerHTML += `<div class="wa-number-row" style="margin-bottom:8px;">
            <input class="form-input wa-number-input" type="tel" value="${num}" id="waNum${i}" onchange="saveWASettings()">
            <button class="btn-sm btn-pdf" onclick="removeWANumber(${i})">🗑️</button>
          </div>`;
        });
      }
    }
    if (saved.emails && saved.emails.length) {
      const container = document.getElementById('emailAddresses');
      if (container) {
        container.innerHTML = '';
        saved.emails.forEach((email, i) => {
          container.innerHTML += `<div class="wa-number-row" style="margin-bottom:8px;">
            <input class="form-input email-input" type="email" value="${email}" id="emailAddr${i}" onchange="saveWASettings()">
            <button class="btn-sm btn-pdf" onclick="removeEmailAddress(${i})">🗑️</button>
          </div>`;
        });
      }
    }
  } catch(e) {}
}

function addWANumber() {
  const container = document.getElementById('waNumbers');
  const count = container.querySelectorAll('.wa-number-row').length;
  container.innerHTML += `<div class="wa-number-row" style="margin-bottom:8px;">
    <input class="form-input wa-number-input" type="tel" placeholder="966xxxxxxxxx" id="waNum${count}" onchange="saveWASettings()">
    <button class="btn-sm btn-pdf" onclick="removeWANumber(${count})">🗑️</button>
  </div>`;
}

function removeWANumber(idx) {
  const rows = document.querySelectorAll('#waNumbers .wa-number-row');
  if (rows[idx]) rows[idx].remove();
  saveWASettings();
}

function testWANotification() {
  const fakeViolation = {
    refNumber:'QID-TEST-001', mainViolationTypeLabel:'مخالفة مرورية',
    subViolationType:'تجاوز السرعة', companyName:'شركة الاختبار',
    zone:'Upper Zone', inspector: currentUser?.name || 'مفتش',
    date: new Date().toLocaleDateString('ar-SA'), time: new Date().toLocaleTimeString('ar-SA')
  };
  sendWANotification(fakeViolation);
}

function sendWANotification(violation) {
  try {
    const settings = JSON.parse(localStorage.getItem('waSettings') || '{}');
    // واتساب
    if (settings.enabled && settings.nums?.length && settings.notifWhatsApp !== false) {
      const template = settings.template ||
`⚠️ مخالفة جديدة — نظام القدية
الرقم: {refNumber}
النوع: {type}
الشركة: {company}
المنطقة: {zone}
المفتش: {inspector}
الوقت: {time}`;
      const msg = template
        .replace('{refNumber}', violation.refNumber)
        .replace('{type}', violation.mainViolationTypeLabel || violation.subViolationType)
        .replace('{company}', violation.companyName)
        .replace('{zone}', violation.zone)
        .replace('{inspector}', violation.inspector)
        .replace('{time}', violation.date + ' ' + violation.time);

      const num = settings.nums[0].replace(/\D/g,'');
      const url = `https://wa.me/${num}?text=${encodeURIComponent(msg)}`;
      // فتح بطريقة آمنة بدون صفحة فارغة
      const a = document.createElement('a');
      a.href = url; a.target = '_blank'; a.rel = 'noopener noreferrer';
      a.click();

      if (settings.nums.length > 1) {
        showToast(`💬 تم فتح واتساب للرقم الأول — أرقام أخرى: ${settings.nums.length - 1}`);
      }
    }
    // إيميل
    if (settings.emailEnabled && settings.emails?.length && settings.notifEmail) {
      sendEmailNotification(violation);
    }
  } catch(e) { console.error('Notification error:', e); }
}

function sendEmailNotification(violation) {
  try {
    const settings = JSON.parse(localStorage.getItem('waSettings') || '{}');
    if (!settings.emailEnabled || !settings.emails?.length) return;
    const template = settings.emailTemplate ||
`⚠️ مخالفة جديدة — نظام القدية
الرقم المرجعي: {refNumber}
نوع المخالفة: {type}
الشركة: {company}
المنطقة: {zone}
المفتش: {inspector}
التاريخ والوقت: {time}`;
    const body = template
      .replace('{refNumber}', violation.refNumber)
      .replace('{type}', violation.mainViolationTypeLabel || violation.subViolationType)
      .replace('{company}', violation.companyName)
      .replace('{zone}', violation.zone)
      .replace('{inspector}', violation.inspector)
      .replace('{time}', violation.date + ' ' + violation.time);

    const subject = `⚠️ مخالفة جديدة ${violation.refNumber} — نظام القدية`;
    const allEmails = settings.emails.join(',');
    const mailtoUrl = `mailto:${allEmails}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`;
    // فتح بطريقة آمنة بدون صفحة فارغة
    const a = document.createElement('a');
    a.href = mailtoUrl; a.target = '_blank'; a.rel = 'noopener noreferrer';
    a.click();
    showToast('📧 تم فتح البريد الإلكتروني للإرسال');
  } catch(e) { console.error('Email error:', e); }
}

function addEmailAddress() {
  const container = document.getElementById('emailAddresses');
  const count = container.querySelectorAll('.wa-number-row').length;
  container.innerHTML += `<div class="wa-number-row" style="margin-bottom:8px;">
    <input class="form-input email-input" type="email" placeholder="email@example.com" id="emailAddr${count}" onchange="saveWASettings()">
    <button class="btn-sm btn-pdf" onclick="removeEmailAddress(${count})">🗑️</button>
  </div>`;
}

function removeEmailAddress(idx) {
  const rows = document.querySelectorAll('#emailAddresses .wa-number-row');
  if (rows[idx]) rows[idx].remove();
  saveWASettings();
}

function testEmailNotification() {
  const fakeViolation = {
    refNumber:'QID-TEST-001', mainViolationTypeLabel:'مخالفة مرورية',
    subViolationType:'تجاوز السرعة', companyName:'شركة الاختبار',
    zone:'Upper Zone', inspector: currentUser?.name || 'مفتش',
    date: new Date().toLocaleDateString('ar-SA'), time: new Date().toLocaleTimeString('ar-SA')
  };
  sendEmailNotification(fakeViolation);
}

// ══════════════════════════════════════════════════════
// REPORTS
// ══════════════════════════════════════════════════════
let currentReportPeriod = 'today';

function setReportPeriod(period) {
  currentReportPeriod = period;
  ['today','week','month','all'].forEach(p => {
    const btn = document.getElementById('period'+p.charAt(0).toUpperCase()+p.slice(1));
    if (btn) btn.classList.toggle('active', p === period);
  });
  renderReports(period);
}

function getReportData(period) {
  const now = new Date();
  let list = [...violations];
  if (currentUser?.role === 'supervisor' && currentUser.group) {
    list = list.filter(v => v.receivingGroup === currentUser.group);
  }
  if (period === 'today') {
    const today = now.toLocaleDateString('ar-SA');
    list = list.filter(v => v.date === today);
  } else if (period === 'week') {
    const weekAgo = new Date(now - 7*24*60*60*1000);
    list = list.filter(v => {
      try { return new Date(v.date.split('/').reverse().join('-')) >= weekAgo; } catch(e) { return true; }
    });
  } else if (period === 'month') {
    const monthAgo = new Date(now - 30*24*60*60*1000);
    list = list.filter(v => {
      try { return new Date(v.date.split('/').reverse().join('-')) >= monthAgo; } catch(e) { return true; }
    });
  }
  return list;
}

function renderReports(period) {
  const data = getReportData(period);
  const approved = data.filter(v=>v.status==='approved').length;
  const rejected = data.filter(v=>v.status==='rejected').length;
  const pending  = data.filter(v=>v.status==='pending').length;

  const periodLabel = {today:'اليوم', week:'هذا الأسبوع', month:'هذا الشهر', all:'الإجمالي'}[period];

  // KPIs
  document.getElementById('reportKPIs').innerHTML = `
    <div class="report-stat"><div class="report-stat-num" style="color:var(--gold-light);">${data.length}</div><div class="report-stat-label">إجمالي المخالفات — ${periodLabel}</div></div>
    <div class="report-stat"><div class="report-stat-num" style="color:#34d399;">${approved}</div><div class="report-stat-label">موافق عليها</div></div>
    <div class="report-stat"><div class="report-stat-num" style="color:#f87171;">${rejected}</div><div class="report-stat-label">مرفوضة</div></div>
    <div class="report-stat"><div class="report-stat-num" style="color:#fbbf24;">${pending}</div><div class="report-stat-label">قيد المراجعة</div></div>
  `;

  // Helper: count by field
  function countBy(field) {
    const map = {};
    data.forEach(v => { const k = v[field]||'غير محدد'; map[k]=(map[k]||0)+1; });
    return Object.entries(map).sort((a,b)=>b[1]-a[1]).slice(0,10);
  }

  function buildTable(entries, label) {
    if (!entries.length) return '<div style="color:var(--muted);padding:20px;text-align:center;">لا توجد بيانات</div>';
    const max = entries[0][1];
    return `<table class="report-table">
      <thead><tr><th>${label}</th><th style="text-align:center;">العدد</th><th style="text-align:center;">النسبة</th></tr></thead>
      <tbody>${entries.map(([k,v])=>`
        <tr>
          <td>${k}</td>
          <td style="text-align:center;font-weight:700;color:var(--gold);">${v}</td>
          <td style="text-align:center;">
            <div style="display:flex;align-items:center;gap:8px;justify-content:center;">
              <div style="width:60px;height:6px;background:rgba(255,255,255,0.1);border-radius:3px;">
                <div style="width:${Math.round(v/max*100)}%;height:100%;background:var(--gold);border-radius:3px;"></div>
              </div>
              <span style="font-size:11px;color:var(--muted);">${data.length?Math.round(v/data.length*100):0}%</span>
            </div>
          </td>
        </tr>`).join('')}
      </tbody>
    </table>`;
  }

  document.getElementById('reportInspectors').innerHTML = buildTable(countBy('inspector'), 'المفتش');
  document.getElementById('reportCompanies').innerHTML  = buildTable(countBy('companyName'), 'الشركة');
  document.getElementById('reportZones').innerHTML      = buildTable(countBy('zone'), 'المنطقة');
  document.getElementById('reportTypes').innerHTML      = buildTable(countBy('mainViolationTypeLabel'), 'نوع المخالفة');
}

async function downloadReport() {
  const period = currentReportPeriod;
  const data = getReportData(period);
  const periodLabel = {today:'اليوم', week:'هذا الأسبوع', month:'هذا الشهر', all:'الإجمالي'}[period];

  function countBy(field) {
    const map = {};
    data.forEach(v => { const k = v[field]||'غير محدد'; map[k]=(map[k]||0)+1; });
    return Object.entries(map).sort((a,b)=>b[1]-a[1]);
  }

  const el = document.createElement('div');
  el.style.cssText='position:fixed;left:-9999px;top:0;width:794px;padding:40px;background:#060d1a;font-family:Arial,sans-serif;color:#e2e8f0;direction:rtl;';
  el.innerHTML=`
    <div style="border:2px solid #d4af37;border-radius:16px;padding:30px;">
      <div style="text-align:center;margin-bottom:24px;">
        <div style="font-size:36px;">📈</div>
        <div style="font-size:24px;font-weight:900;color:#ffd700;">التقرير الدوري — ${periodLabel}</div>
        <div style="font-size:12px;color:rgba(255,255,255,0.5);margin-top:4px;">${new Date().toLocaleDateString('ar-SA')} — نظام مخالفات القدية</div>
      </div>
      <div style="display:grid;grid-template-columns:1fr 1fr 1fr 1fr;gap:12px;margin-bottom:24px;">
        ${[
          ['الإجمالي', data.length, '#ffd700'],
          ['موافق عليها', data.filter(v=>v.status==='approved').length, '#34d399'],
          ['مرفوضة', data.filter(v=>v.status==='rejected').length, '#f87171'],
          ['قيد المراجعة', data.filter(v=>v.status==='pending').length, '#fbbf24'],
        ].map(([l,n,c])=>`<div style="background:rgba(255,255,255,0.05);border:1px solid rgba(212,175,55,0.2);border-radius:10px;padding:16px;text-align:center;">
          <div style="font-size:28px;font-weight:900;color:${c};">${n}</div>
          <div style="font-size:11px;color:rgba(255,255,255,0.5);margin-top:4px;">${l}</div>
        </div>`).join('')}
      </div>
      ${[
        ['👮 أنشط المفتشين', countBy('inspector')],
        ['🏢 أكثر الشركات', countBy('companyName')],
        ['📍 توزيع المناطق', countBy('zone')],
        ['⚠️ أنواع المخالفات', countBy('mainViolationTypeLabel')],
      ].map(([title, rows])=>`
        <div style="margin-bottom:16px;">
          <div style="font-size:14px;font-weight:700;color:#d4af37;margin-bottom:8px;">${title}</div>
          <table style="width:100%;border-collapse:collapse;font-size:12px;">
            <thead><tr style="background:rgba(212,175,55,0.1);">
              <th style="padding:8px;border:1px solid rgba(212,175,55,0.2);color:#d4af37;text-align:right;">الاسم</th>
              <th style="padding:8px;border:1px solid rgba(212,175,55,0.2);color:#d4af37;text-align:center;">العدد</th>
            </tr></thead>
            <tbody>${rows.slice(0,8).map(([k,v])=>`
              <tr><td style="padding:8px;border:1px solid rgba(255,255,255,0.05);">${k}</td>
              <td style="padding:8px;border:1px solid rgba(255,255,255,0.05);text-align:center;font-weight:700;color:#ffd700;">${v}</td></tr>
            `).join('')}</tbody>
          </table>
        </div>`).join('')}
    </div>`;

  document.body.appendChild(el);
  try {
    const canvas = await html2canvas(el,{scale:2,useCORS:true,backgroundColor:'#060d1a',logging:false});
    const {jsPDF} = window.jspdf;
    const imgData = canvas.toDataURL('image/png');
    const iw=210, ih=(canvas.height*iw)/canvas.width;
    const doc = new jsPDF({orientation:'portrait',unit:'mm',format:'a4'});
    if(ih<=297){doc.addImage(imgData,'PNG',0,0,iw,ih);}
    else{let y=0,rem=ih;while(rem>0){doc.addImage(imgData,'PNG',0,-y,iw,ih);rem-=297;y+=297;if(rem>0)doc.addPage();}}
    doc.save(`تقرير_${periodLabel}_${new Date().toISOString().split('T')[0]}.pdf`);
    showToast('✅ تم تحميل التقرير بنجاح!');
  } catch(err) {
    showToast('❌ خطأ في إنشاء التقرير','error');
  } finally {
    document.body.removeChild(el);
  }
}

// ══════════════════════════════════════════════════════
// APPROVE / REJECT
// ══════════════════════════════════════════════════════
function approveViolation(btn) {
  try {
    if (!currentUser || (currentUser.role !== 'supervisor' && currentUser.role !== 'admin')) {
      showToast('❌ فقط المشرفين يمكنهم الموافقة','error'); return;
    }
    const ref = btn.getAttribute('data-ref');
    const idx = violations.findIndex(x => x.refNumber === ref);
    if (idx === -1) { showToast('❌ لم يتم العثور على المخالفة','error'); return; }
    const notesEl = btn.closest('.v-card').querySelector('.v-notes-input');
    const notes = notesEl ? notesEl.value.trim() : '';
    const updates = {
      status: 'approved',
      reviewedBy: currentUser.name,
      reviewerId: currentUser.id,
      reviewDate: new Date().toLocaleDateString('ar-SA'),
      reviewTime: new Date().toLocaleTimeString('ar-SA'),
      reviewNotes: notes || null
    };
    Object.assign(violations[idx], updates);
    saveToStorage();
    // 🔥 حفظ في Firebase
    updateViolationInFirebase(ref, updates);
    // 🔔 إشعار للمراقب عند الموافقة
    addNotification('✅', 'تمت الموافقة', `الموافقة على المخالفة ${ref} بواسطة ${currentUser.name}`, ref, 'admin');
    showToast('✅ تمت الموافقة وحفظها في السحابة');
    renderViolationList('pending','pendingList',true);
    renderViolationList('approved','approvedList',false);
    if (typeof renderDashboard === 'function') renderDashboard();
  } catch(e) { console.error(e); showToast('❌ خطأ: '+e.message,'error'); }
}

function rejectViolation(btn) {
  try {
    if (!currentUser || (currentUser.role !== 'supervisor' && currentUser.role !== 'admin')) {
      showToast('❌ فقط المشرفين يمكنهم الرفض','error'); return;
    }
    const ref = btn.getAttribute('data-ref');
    const idx = violations.findIndex(x => x.refNumber === ref);
    if (idx === -1) { showToast('❌ لم يتم العثور على المخالفة','error'); return; }
    const notesEl = btn.closest('.v-card').querySelector('.v-notes-input');
    const notes = notesEl ? notesEl.value.trim() : '';
    if (!notes) { showToast('⚠️ الرجاء إضافة ملاحظة توضح سبب الرفض','error'); return; }
    const updates = {
      status: 'rejected',
      reviewedBy: currentUser.name,
      reviewerId: currentUser.id,
      reviewDate: new Date().toLocaleDateString('ar-SA'),
      reviewTime: new Date().toLocaleTimeString('ar-SA'),
      reviewNotes: notes
    };
    Object.assign(violations[idx], updates);
    saveToStorage();
    // 🔥 حفظ في Firebase
    updateViolationInFirebase(ref, updates);
    addNotification('❌', 'مخالفة مرفوضة', `رفض المخالفة ${ref} — ${notes}`, ref, 'employee', violations[idx].inspectorId);
    showToast('❌ تم رفض المخالفة وحفظها في السحابة');
    renderViolationList('pending','pendingList',true);
    renderViolationList('rejected','rejectedList',false);
    if (typeof renderDashboard === 'function') renderDashboard();
  } catch(e) { console.error(e); showToast('❌ خطأ: '+e.message,'error'); }
}

// ══════════════════════════════════════════════════════
// SUBMIT
// ══════════════════════════════════════════════════════
function submitViolation() {
  const mainViolationType = document.getElementById('mainViolationType').value;
  let subViolationType    = document.getElementById('subViolationType').value;
  const otherDetail  = document.getElementById('otherViolationDetail').value;
  const personCount  = document.getElementById('personCount').value;
  const permitType   = document.getElementById('permitType').value;
  const zone         = document.getElementById('zone').value;
  const receivingGroup = document.getElementById('receivingGroup').value;
  const shift        = document.getElementById('shift').value;
  const companyName  = document.getElementById('companyName').value;
  const violatorId   = document.getElementById('violatorId').value.trim();
  const violatorPhone= document.getElementById('violatorPhone').value.trim();
  const plateLetters = document.getElementById('plateLetters').value.trim();
  const plateNumbers = document.getElementById('plateNumbers').value.trim();

  if (!mainViolationType||!subViolationType||!permitType||!zone||!receivingGroup||!shift||!companyName||!violatorId||!violatorPhone) {
    showToast('⚠️ الرجاء ملء جميع الحقول المطلوبة (*)','error'); return;
  }
  const violatorType = document.getElementById('violatorType')?.value || '';
  if (!violatorType) {
    showToast('⚠️ الرجاء اختيار نوع المخالف','error'); return;
  }
  if ((violatorType === 'موظف' || violatorType === 'مقاول' || violatorType === 'مقاول بالباطن' || violatorType === 'زائر') && !document.getElementById('violatorNationality')?.value?.trim()) {
    showToast('⚠️ الرجاء إدخال الجنسية','error'); return;
  }
  if (!plateLetters||!plateNumbers) {
    showToast('⚠️ الرجاء إدخال حروف وأرقام لوحة المركبة','error'); return;
  }
  if (!/^[A-Z]{1,4}$/.test(plateLetters)) {
    showToast('⚠️ حروف اللوحة يجب أن تكون إنجليزية فقط','error'); return;
  }
  if (subViolationType==='أخرى..'&&!otherDetail.trim()) {
    showToast('⚠️ الرجاء كتابة تفاصيل المخالفة الأخرى','error'); return;
  }
  if (mainViolationType==='تصاريح'&&subViolationType!=='عدم وجود استكر'&&(!personCount||personCount<1)) {
    showToast('⚠️ الرجاء إدخال عدد الأشخاص','error'); return;
  }
  if (!/^[12]\d{9}$/.test(violatorId)) {
    showToast('⚠️ رقم الهوية يجب أن يبدأ بـ 1 أو 2 ويتكون من 10 أرقام','error'); return;
  }
  if (!/^05\d{8}$/.test(violatorPhone)) {
    showToast('⚠️ رقم الجوال يجب أن يبدأ بـ 05 ويتكون من 10 أرقام','error'); return;
  }
  if (!currentLocation) {
    showToast('⚠️ الرجاء تحديد الموقع الجغرافي — اضغط "موقعي الحالي" أو حدد على الخريطة','error');
    document.getElementById('coordinatesDisplay')?.scrollIntoView({behavior:'smooth', block:'center'});
    return;
  }
  if (!uploadedImages.length) {
    const a = document.getElementById('imgRequiredAlert');
    if (a) a.style.display = 'block';
    showToast('⚠️ يجب رفع صورة واحدة على الأقل للمخالفة','error');
    document.getElementById('uploadBtn')?.scrollIntoView({behavior:'smooth', block:'center'});
    return;
  }

  // متابعة الإصدار مباشرة (الفحص تم في الخطوة 1)
  proceedSubmitViolation();
}

// ══════════════════════════════════════════════════════
// SECURITY SCAN — الفحص الأمني (الخطوة 1 من النموذج)
// ══════════════════════════════════════════════════════
let securityScanResult = null;

function updateScanPlatePreview() {
  const l = document.getElementById('scanPlateLetters').value.trim();
  const n = document.getElementById('scanPlateNumbers').value.trim();
  const prev = document.getElementById('scanPlatePreview');
  if (l || n) { prev.style.display = 'block'; prev.textContent = (l&&n) ? l+' — '+n : (l||n); }
  else { prev.style.display = 'none'; }
}

function runSecurityScan() {
  const pL = document.getElementById('scanPlateLetters').value.trim().toUpperCase();
  const pN = document.getElementById('scanPlateNumbers').value.trim();
  const idNum = document.getElementById('scanIdNumber').value.trim();
  const hasPlate = pL && pN;
  const hasId = idNum.length > 0;

  if (!hasPlate && !hasId) { showToast('⚠️ أدخل لوحة المركبة أو رقم الهوية على الأقل','error'); return; }

  const fullPlate = hasPlate ? pL + '-' + pN : '';
  const blockedPlates = JSON.parse(localStorage.getItem('blockedPlates') || '[]');
  const blockedIds    = JSON.parse(localStorage.getItem('blockedIds') || '[]');
  const wantedPlates  = JSON.parse(localStorage.getItem('wantedPlates') || '[]');
  const wantedIds     = JSON.parse(localStorage.getItem('wantedIds') || '[]');

  const plateUp = fullPlate.toUpperCase();
  const plateBlocked = hasPlate && blockedPlates.some(p => p.trim().toUpperCase() === plateUp);
  const plateWanted  = hasPlate && wantedPlates.some(p => p.trim().toUpperCase() === plateUp);
  const idBlocked    = hasId && blockedIds.some(id => id.trim() === idNum);
  const idWanted     = hasId && wantedIds.some(id => id.trim() === idNum);

  const resultBox = document.getElementById('scanResultBox');
  resultBox.style.display = 'block';
  let stepsHTML = '';
  if (hasPlate) stepsHTML += `<div class="scan-item"><span class="scan-icon">🚗</span><span>لوحة المركبة: <strong style="color:var(--gold);letter-spacing:2px;">${fullPlate}</strong></span><span class="scan-status pending" id="ssPlate">⏳</span></div>`;
  if (hasId) stepsHTML += `<div class="scan-item"><span class="scan-icon">🪪</span><span>رقم الهوية/الإقامة: <strong style="color:var(--gold);">${idNum}</strong></span><span class="scan-status pending" id="ssId">⏳</span></div>`;

  resultBox.innerHTML = `
    <div style="padding:16px;border-radius:12px;background:rgba(212,175,55,0.08);border:1px solid var(--border);margin-top:16px;">
      <div style="display:flex;align-items:center;gap:10px;margin-bottom:12px;">
        <span style="font-size:22px;" id="scanSpinner">⏳</span>
        <span style="font-size:14px;font-weight:700;color:var(--gold-light);">جاري الفحص الأمني...</span>
      </div>
      <div class="scan-progress"><div class="scan-progress-bar" id="scanProg2" style="width:0%"></div></div>
      <div id="scanSteps2">${stepsHTML}</div>
      <div id="scanFinalResult2" style="display:none;margin-top:12px;"></div>
    </div>`;

  const prog = document.getElementById('scanProg2');
  document.getElementById('btnRunScan').disabled = true;

  if (hasPlate) {
    setTimeout(() => {
      prog.style.width = hasId ? '50%' : '100%';
      const st = document.getElementById('ssPlate');
      if (plateBlocked) { st.textContent = '🚫 ممنوعة — منع دخول'; st.className = 'scan-status alert'; }
      else if (plateWanted) { st.textContent = '⚠️ عليها تعميم'; st.className = 'scan-status alert'; }
      else { st.textContent = '✅ نظيف'; st.className = 'scan-status ok'; }
    }, 700);
  }

  if (hasId) {
    setTimeout(() => {
      prog.style.width = '100%';
      const st = document.getElementById('ssId');
      if (idBlocked) { st.textContent = '🚫 ممنوع — منع دخول'; st.className = 'scan-status alert'; }
      else if (idWanted) { st.textContent = '⚠️ عليه تعميم'; st.className = 'scan-status alert'; }
      else { st.textContent = '✅ نظيف'; st.className = 'scan-status ok'; }
    }, hasPlate ? 1300 : 700);
  }

  setTimeout(() => {
    document.getElementById('btnRunScan').disabled = false;
    const spinner = document.getElementById('scanSpinner');
    const finalResult = document.getElementById('scanFinalResult2');
    finalResult.style.display = 'block';
    const hasAnyBlock  = plateBlocked || idBlocked;
    const hasAnyWanted = plateWanted || idWanted;
    const alerts = [];
    if (plateBlocked) alerts.push('🚗 لوحة المركبة <b>' + fullPlate + '</b> — ممنوعة من الدخول');
    if (plateWanted)  alerts.push('🚗 لوحة المركبة <b>' + fullPlate + '</b> — عليها تعميم نشط');
    if (idBlocked)    alerts.push('🪪 رقم الهوية/الإقامة <b>' + idNum + '</b> — ممنوع من الدخول');
    if (idWanted)     alerts.push('🪪 رقم الهوية/الإقامة <b>' + idNum + '</b> — عليه تعميم نشط');
    const safePlate = fullPlate.replace(/'/g, "\\'");
    const safeId = (idNum||'').replace(/'/g, "\\'");

    if (hasAnyBlock || hasAnyWanted) {
      spinner.textContent = hasAnyBlock ? '🚫' : '⚠️';
      securityScanResult = hasAnyBlock ? 'blocked' : 'wanted';
      finalResult.innerHTML = `
        <div class="scan-result ${hasAnyBlock ? 'blocked' : 'warning'}">
          <div style="font-size:16px;font-weight:900;margin-bottom:8px;">${hasAnyBlock ? '🚫 تم رصد منع دخول!' : '⚠️ تم رصد تعميم!'}</div>
          ${alerts.map(a => '<div style="font-size:12px;margin-bottom:4px;">'+a+'</div>').join('')}
        </div>
        <div style="display:flex;gap:10px;margin-top:12px;">
          <button class="btn-primary" onclick="unlockFullFormAfterScan('${securityScanResult}','${safePlate}','${safeId}')" style="flex:1;${hasAnyBlock?'background:rgba(239,68,68,0.2);border:1px solid rgba(239,68,68,0.4);color:#f87171;box-shadow:none;':''}">
            ${hasAnyBlock ? '⚠️ متابعة مع إشارة المنع' : '✅ متابعة وإكمال المخالفة'}
          </button>
          <button class="btn-primary" onclick="resetSecurityScan()" style="flex:1;background:rgba(255,255,255,0.08);color:var(--muted);border:1px solid var(--border);box-shadow:none;font-size:13px;">🔄 فحص جديد</button>
        </div>`;
    } else {
      spinner.textContent = '✅';
      securityScanResult = 'clear';
      const summaryParts = [];
      if (hasPlate) summaryParts.push('🚗 اللوحة: ' + fullPlate + ' — نظيف');
      if (hasId) summaryParts.push('🪪 الهوية: ' + idNum + ' — نظيف');
      finalResult.innerHTML = `
        <div class="scan-result clear">
          <div style="font-size:16px;font-weight:900;margin-bottom:6px;">✅ الفحص سليم</div>
          <div style="font-size:12px;opacity:0.8;">${summaryParts.join(' &nbsp;|&nbsp; ')}</div>
        </div>
        <button class="btn-primary" onclick="unlockFullFormAfterScan('clear','${safePlate}','${safeId}')" style="margin-top:12px;">✅ متابعة لإكمال النموذج</button>`;
    }
  }, hasPlate && hasId ? 1900 : 1200);
}

function skipSecurityScan() {
  securityScanResult = 'skipped';
  const form = document.getElementById('fullViolationForm');
  form.style.display = 'block';
  form.scrollIntoView({ behavior:'smooth', block:'start' });
  const badge = document.getElementById('securityBadge');
  badge.innerHTML = '⏩ تم تخطي الفحص';
  badge.style.background = 'rgba(255,255,255,0.08)';
  badge.style.borderColor = 'var(--border)';
  badge.style.color = 'var(--muted)';
}

function unlockFullFormAfterScan(status, plate, idNum) {
  const form = document.getElementById('fullViolationForm');
  form.style.display = 'block';
  form.scrollIntoView({ behavior:'smooth', block:'start' });

  // شارة الحالة
  const badge = document.getElementById('securityBadge');
  if (status === 'blocked') {
    badge.innerHTML = '🚫 رُصد منع دخول';
    badge.style.background = 'rgba(239,68,68,0.15)';
    badge.style.borderColor = 'rgba(239,68,68,0.4)';
    badge.style.color = '#f87171';
  } else if (status === 'wanted') {
    badge.innerHTML = '⚠️ رُصد تعميم';
    badge.style.background = 'rgba(245,158,11,0.15)';
    badge.style.borderColor = 'rgba(245,158,11,0.4)';
    badge.style.color = '#fbbf24';
  } else {
    badge.innerHTML = '✅ الفحص سليم — نظيف';
    badge.style.background = 'rgba(16,185,129,0.15)';
    badge.style.borderColor = 'rgba(16,185,129,0.3)';
    badge.style.color = '#34d399';
  }

  // نقل البيانات تلقائياً لحقول النموذج
  const parts = plate.split('-');
  if (parts.length === 2) {
    document.getElementById('plateLetters').value = parts[0];
    document.getElementById('plateNumbers').value = parts[1];
    updatePlate();
  }
  document.getElementById('violatorId').value = idNum;
  checkRepeatOnForm();
}

function resetSecurityScan() {
  document.getElementById('scanResultBox').style.display = 'none';
  document.getElementById('scanPlateLetters').value = '';
  document.getElementById('scanPlateNumbers').value = '';
  document.getElementById('scanIdNumber').value = '';
  document.getElementById('scanPlatePreview').style.display = 'none';
  document.getElementById('fullViolationForm').style.display = 'none';
  document.getElementById('btnRunScan').disabled = false;
  securityScanResult = null;
}

function proceedSubmitViolation() {
  const mainViolationType = document.getElementById('mainViolationType').value;
  let subViolationType    = document.getElementById('subViolationType').value;
  const otherDetail  = document.getElementById('otherViolationDetail').value;
  const personCount  = document.getElementById('personCount').value;
  const permitType   = document.getElementById('permitType').value;
  const zone         = document.getElementById('zone').value;
  const receivingGroup = document.getElementById('receivingGroup').value;
  const shift        = document.getElementById('shift').value;
  const companyName  = document.getElementById('companyName').value;
  const violatorType = document.getElementById('violatorType')?.value || '';
  const violatorNationality = document.getElementById('violatorNationality')?.value?.trim() || '';
  const violatorId   = document.getElementById('violatorId').value.trim();
  const violatorPhone= document.getElementById('violatorPhone').value.trim();
  const plateLetters = document.getElementById('plateLetters').value.trim();
  const plateNumbers = document.getElementById('plateNumbers').value.trim();

  const imgAlert = document.getElementById('imgRequiredAlert');
  if (imgAlert) imgAlert.style.display = 'none';

  const finalSubType = subViolationType==='أخرى..' ? otherDetail : subViolationType;
  const refNumber = 'QID-'+new Date().getFullYear()+'-'+Date.now().toString().slice(-6);
  const labels = { 'مرورية':'مخالفة مرورية','جنائية':'مخالفة جنائية','عدم_التزام':'عدم التزام بأنظمة القدية','تصاريح':'مخالفة التصاريح' };

  const violation = {
    refNumber, mainViolationType, mainViolationTypeLabel:labels[mainViolationType]||mainViolationType,
    subViolationType:finalSubType, violationDetail: (subViolationType==='أخرى..' ? otherDetail : ''),
    personCount:personCount||null,
    permitType, zone, receivingGroup, shift,
    violatorType, violatorNationality,
    violationTime: new Date().toLocaleTimeString('ar-SA',{hour:'2-digit',minute:'2-digit'}),
    companyName, violatorId, violatorPhone,
    vehiclePlate: plateLetters+'-'+plateNumbers,
    location:currentLocation,
    inspector:currentUser.name, inspectorId:currentUser.id,
    date:new Date().toLocaleDateString('ar-SA'),
    time:new Date().toLocaleTimeString('ar-SA'),
    images:[...uploadedImages],
    status:'pending', reviewedBy:null, reviewerId:null,
    reviewDate:null, reviewTime:null, reviewNotes:null
  };

  violations.push(violation);
  saveToStorage();
  lastViolation = violation;

  // 🔥 حفظ في Firebase
  saveViolationToFirebase(violation).then(ok => {
    if (!ok) showToast('⚠️ تم الحفظ محلياً — سيتم المزامنة لاحقاً', 'error');
  });

  // 🔔 إشعار للمشرف عند تسجيل مخالفة جديدة
  addNotification('📝', 'مخالفة جديدة', `مخالفة ${refNumber} — ${violation.companyName} — ${violation.mainViolationTypeLabel}`, refNumber, 'supervisor');

  // إشعار واتساب
  sendWANotification(violation);

  // إرسال مباشر إلى Google Sheets عند التسجيل
  sendToGoogleSheets(violation);

  document.getElementById('refNumber').textContent = refNumber;
  document.getElementById('successMsg').style.display = 'block';
  showToast('✅ تم تسجيل المخالفة بنجاح!');

  // Reset form
  document.getElementById('mainViolationType').value='';
  updateSubViolations();
  ['permitType','zone','receivingGroup','shift'].forEach(id=>document.getElementById(id).value='');
  document.getElementById('companyName').value='';
  document.getElementById('companyDisplayText').textContent='— اختر الشركة —';
  document.getElementById('companyDisplayText').style.color='var(--muted)';
  if(document.getElementById('violatorType')) document.getElementById('violatorType').value='';
  if(document.getElementById('violatorNationality')) document.getElementById('violatorNationality').value='';
  if(document.getElementById('nationalityField')) document.getElementById('nationalityField').style.display='none';
  if(document.getElementById('violatorCompanyField')) document.getElementById('violatorCompanyField').style.display='none';
  document.getElementById('violatorId').value='';
  document.getElementById('violatorPhone').value='';
  document.getElementById('plateLetters').value='';
  document.getElementById('plateNumbers').value='';
  document.getElementById('vehiclePlate').value='';
  document.getElementById('platePreview').style.display='none';
  document.getElementById('map').style.display='none';
  document.getElementById('imagePreview').innerHTML='';
  document.getElementById('violationImages').value='';
  const imgAlert2 = document.getElementById('imgRequiredAlert');
  if (imgAlert2) imgAlert2.style.display = 'none';
  document.getElementById('coordinatesDisplay').style.display='none';
  currentLocation=null; uploadedImages=[];
  if(marker&&map){map.removeLayer(marker);marker=null;}
  // إعادة تعيين خطوة الفحص الأمني
  resetSecurityScan();
  document.getElementById('securityCheckCard').scrollIntoView({behavior:'smooth', block:'start'});
  setTimeout(()=>{ document.getElementById('successMsg').style.display='none'; },5000);
  window.scrollTo(0,0);
}

// ══════════════════════════════════════════════════════
// FORM HELPERS
// ══════════════════════════════════════════════════════
function updateSubViolations() {
  const main = document.getElementById('mainViolationType').value;
  const sub  = document.getElementById('subViolationType');
  sub.innerHTML = '<option value="">— اختر المخالفة —</option>';
  document.getElementById('otherViolationField').style.display='none';
  document.getElementById('personCountField').style.display='none';
  if (main && violationCategories[main]) {
    sub.disabled = false;
    violationCategories[main].forEach(v => {
      const o=document.createElement('option'); o.value=v; o.textContent=v; sub.appendChild(o);
    });
  } else { sub.disabled=true; }
}

function toggleOtherField() {
  const main=document.getElementById('mainViolationType').value;
  const sub =document.getElementById('subViolationType').value;
  document.getElementById('otherViolationField').style.display = sub==='أخرى..' ? 'block':'none';
  if (sub!=='أخرى..') document.getElementById('otherViolationDetail').value='';
  document.getElementById('personCountField').style.display =
    (main==='تصاريح'&&sub&&sub!=='عدم وجود استكر') ? 'block':'none';
  if(!(main==='تصاريح'&&sub&&sub!=='عدم وجود استكر')) document.getElementById('personCount').value='';
}

// ══════════════════════════════════════════════════════
// COMPANY DROPDOWN
// ══════════════════════════════════════════════════════
function renderCompanyList(filter) {
  const filtered = filter
    ? companyList.filter(c=>c.toLowerCase().includes(filter.toLowerCase()))
    : companyList;
  const current = document.getElementById('companyName').value;
  const div = document.getElementById('companyList');
  div.innerHTML = filtered.length
    ? filtered.map(c=>`<div class="company-option${c===current?' selected':''}" onclick="selectCompany('${c.replace(/'/g,"\'")}')"> ${c}</div>`).join('')
    : '<div style="color:var(--muted);text-align:center;padding:12px;">لا توجد نتائج</div>';
}
function toggleCompanyDropdown() {
  companyDropdownOpen=!companyDropdownOpen;
  const dd=document.getElementById('companyDropdown');
  const tr=document.getElementById('companyTrigger');
  dd.style.display=companyDropdownOpen?'block':'none';
  tr.classList.toggle('open',companyDropdownOpen);
  if(companyDropdownOpen){
    document.getElementById('companySearch').value='';
    renderCompanyList('');
    setTimeout(()=>document.getElementById('companySearch').focus(),80);
  }
}
function filterCompanies(){renderCompanyList(document.getElementById('companySearch').value);}
function selectCompany(name){
  document.getElementById('companyName').value=name;
  document.getElementById('companyDisplayText').textContent=name;
  document.getElementById('companyDisplayText').style.color='var(--text)';
  document.getElementById('companyDropdown').style.display='none';
  document.getElementById('companyTrigger').classList.remove('open');
  companyDropdownOpen=false;
  checkRepeatOnForm();
}
document.addEventListener('click',e=>{
  const w=document.getElementById('companyTrigger');
  const dd=document.getElementById('companyDropdown');
  if(w&&dd&&!w.contains(e.target)&&!dd.contains(e.target)){
    dd.style.display='none';
    w.classList.remove('open');
    companyDropdownOpen=false;
  }
});

// ══════════════════════════════════════════════════════
// PLATE
// ══════════════════════════════════════════════════════
function updatePlate(){
  const l=document.getElementById('plateLetters').value.trim();
  const n=document.getElementById('plateNumbers').value.trim();
  document.getElementById('vehiclePlate').value=(l&&n)?l+'-'+n:'';
  const prev=document.getElementById('platePreview');
  if(l||n){prev.style.display='block';prev.textContent=(l&&n)?l+' — '+n:(l||n);}
  else{prev.style.display='none';}
}

// ══════════════════════════════════════════════════════
// MAP
// ══════════════════════════════════════════════════════
function initMap(){
  if(!map){
    map=L.map('map').setView([24.532297,46.439718],13);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{attribution:'© OpenStreetMap'}).addTo(map);
    map.on('click',e=>setLocation(e.latlng.lat,e.latlng.lng));
  }
}
function showMap(){
  const m=document.getElementById('map');
  if(m.style.display==='none'){m.style.display='block';initMap();setTimeout(()=>map.invalidateSize(),100);}
  else{m.style.display='none';}
}
function setLocation(lat,lng){
  currentLocation={lat,lng};
  if(marker)map.removeLayer(marker);
  marker=L.marker([lat,lng]).addTo(map);
  map.setView([lat,lng],15);
  const cd=document.getElementById('coordinatesDisplay');
  cd.style.display='block';
  cd.innerHTML=`<span style="color:#34d399;font-weight:700;">✅ تم تحديد الموقع</span><br>
    <a href="https://www.google.com/maps?q=${lat},${lng}" target="_blank">🗺️ عرض على خرائط Google</a>`;
}
function getCurrentLocation(){
  if(!navigator.geolocation){showToast('المتصفح لا يدعم الموقع','error');return;}
  const cd=document.getElementById('coordinatesDisplay');
  cd.style.display='block'; cd.innerHTML='<span style="color:var(--gold);">⏳ جاري تحديد موقعك...</span>';
  navigator.geolocation.getCurrentPosition(
    pos=>{
      const{latitude:lat,longitude:lng}=pos.coords;
      const m=document.getElementById('map');
      if(m.style.display==='none'){m.style.display='block';initMap();setTimeout(()=>{map.invalidateSize();setLocation(lat,lng);},300);}
      else setLocation(lat,lng);
    },
    err=>{
      const msgs={1:'يرجى السماح للمتصفح بالوصول للموقع',2:'تعذّر تحديد الموقع، فعّل GPS',3:'انتهت مهلة التحديد، حاول مجدداً'};
      const msg='⚠️ '+(msgs[err.code]||'فشل تحديد الموقع');
      cd.innerHTML=`<span style="color:#f87171;">${msg}</span>`;
      showToast(msg,'error');
    },
    {enableHighAccuracy:true,timeout:15000,maximumAge:0}
  );
}

// ══════════════════════════════════════════════════════
// IMAGE UPLOAD
// ══════════════════════════════════════════════════════
async function compressImage(file){
  return new Promise(res=>{
    const reader=new FileReader();
    reader.onload=e=>{
      const img=new Image();
      img.onload=()=>{
        const c=document.createElement('canvas');
        let w=img.width,h=img.height;
        const MAX=600;
        if(w>h){if(w>MAX){h*=MAX/w;w=MAX;}}else{if(h>MAX){w*=MAX/h;h=MAX;}}
        c.width=w;c.height=h;
        c.getContext('2d').drawImage(img,0,0,w,h);
        res(c.toDataURL('image/jpeg',0.4));
      };
      img.src=e.target.result;
    };
    reader.readAsDataURL(file);
  });
}
async function handleImageUpload(e){
  if (uploadedImages.length >= 3) {
    showToast('⚠️ الحد الأقصى 3 صور','error');
    return;
  }
  const grid=document.getElementById('imagePreview');
  for(const file of e.target.files){
    if(file.type.startsWith('image/')){
      const compressed=await compressImage(file);
      uploadedImages.push(compressed);
      const item=document.createElement('div');
      item.className='img-preview-item';
      item.innerHTML=`<img src="${compressed}"><button class="img-remove" onclick="removeImage(${uploadedImages.length-1})">×</button>`;
      grid.appendChild(item);
    }
  }
}
function removeImage(idx){
  uploadedImages.splice(idx,1);
  const grid=document.getElementById('imagePreview');
  grid.innerHTML='';
  uploadedImages.forEach((d,i)=>{
    const item=document.createElement('div');
    item.className='img-preview-item';
    item.innerHTML=`<img src="${d}"><button class="img-remove" onclick="removeImage(${i})">×</button>`;
    grid.appendChild(item);
  });
}

// ══════════════════════════════════════════════════════
// PDF
// ══════════════════════════════════════════════════════
function sharePDF(){ if(lastViolation) generatePDF(lastViolation); }
function sharePDFByIndex(ref){
  const v=violations.find(x=>x.refNumber===ref);
  if(v) generatePDF(v);
}

async function generatePDF(violation){
  if(!violation){showToast('لا توجد مخالفة','error');return;}
  showToast('⏳ جاري إنشاء PDF ...');

  const mapsLink = violation.location
    ? `https://www.google.com/maps?q=${violation.location.lat},${violation.location.lng}` : null;
  const statusText = { approved:'✓ موافق عليها', rejected:'✕ مرفوضة', pending:'⏳ قيد المراجعة' };
  const statusColor = { approved:'#10b981', rejected:'#ef4444', pending:'#f59e0b' };

  // تكرار المخالف
  const rr = getRepeatInfo(violation);
  const repeatNotes = [];
  if (rr.plate.length) repeatNotes.push(`لوحة ${violation.vehiclePlate}: ${rr.plate.length} مرة`);
  if (rr.id.length) repeatNotes.push(`هوية ${violation.violatorId}: ${rr.id.length} مرة`);

  // صور المخالفة
  const imgs = (violation.images || []).slice(0, 3);

  // قائمة الحقول
  const fields = [
    ['نوع المخالف', violation.violatorType||'—'],
    ['الجنسية', violation.violatorNationality||'—'],
    ['نوع المخالفة', violation.mainViolationTypeLabel],
    ['تفاصيل المخالفة', violation.subViolationType],
    ...(violation.violationDetail && violation.violationDetail !== '-' ? [['تفاصيل إضافية', violation.violationDetail]] : []),
    ['نوع التصريح', violation.permitType],
    ['المنطقة', violation.zone],
    ['المجموعة', violation.receivingGroup||'—'],
    ['الشفت', violation.shift],
    ['وقت المخالفة', violation.violationTime||'—'],
    ['الشركة', violation.companyName],
    ['رقم الهوية', violation.violatorId],
    ['رقم الجوال', violation.violatorPhone],
    ['لوحة المركبة', violation.vehiclePlate||'—'],
    ...(violation.personCount ? [['عدد الأشخاص', violation.personCount]] : []),
    ['المفتش', violation.inspector],
    ['الرقم الوظيفي', violation.inspectorId],
    ['تاريخ التسجيل', violation.date],
    ['وقت التسجيل', violation.time],
  ];

  const el = document.createElement('div');
  el.style.cssText = 'position:fixed;left:-9999px;top:0;width:595px;padding:16px 20px;background:#070e1c;font-family:Arial,Helvetica,sans-serif;color:#dde4f0;direction:rtl;box-sizing:border-box;';

  el.innerHTML = `
  <div style="border:2px solid #d4af37;border-radius:12px;padding:16px;box-sizing:border-box;">

    <!-- ═══ HEADER ═══ -->
    <div style="text-align:center;margin-bottom:12px;">
      <div style="font-size:9px;color:rgba(255,255,255,0.35);margin-bottom:4px;">Qiddiya Violations System</div>
      <div style="font-size:20px;font-weight:900;color:#ffd700;margin-bottom:4px;">تقرير مخالفة</div>
      <div style="display:inline-block;background:rgba(212,175,55,0.15);border:2px solid #d4af37;border-radius:8px;padding:4px 20px;margin-bottom:6px;">
        <span style="color:#ffd700;font-size:17px;font-weight:900;letter-spacing:3px;">${esc(violation.refNumber)}</span>
      </div>
      <div>
        <span style="display:inline-block;padding:3px 14px;border-radius:20px;font-size:11px;font-weight:700;background:${statusColor[violation.status]||statusColor.pending}22;border:1px solid ${statusColor[violation.status]||statusColor.pending};color:${statusColor[violation.status]||statusColor.pending};">
          ${statusText[violation.status]||statusText.pending}
        </span>
      </div>
    </div>

    <div style="height:1px;background:linear-gradient(90deg,transparent,#d4af37,transparent);margin-bottom:12px;"></div>

    <!-- ═══ FIELDS — 3 columns ═══ -->
    <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:6px;margin-bottom:12px;">
      ${fields.map(([lbl,val])=>`
        <div style="background:rgba(255,255,255,0.04);border:1px solid rgba(212,175,55,0.2);border-radius:7px;padding:6px 8px;">
          <div style="color:#d4af37;font-size:8px;font-weight:700;margin-bottom:2px;">${lbl}</div>
          <div style="color:#fff;font-size:11px;font-weight:600;word-break:break-word;">${esc(val)||'—'}</div>
        </div>`).join('')}
    </div>

    ${mapsLink ? `
    <div style="background:rgba(16,185,129,0.06);border:1px solid rgba(16,185,129,0.25);border-radius:7px;padding:6px 10px;margin-bottom:10px;">
      <div style="color:#d4af37;font-size:8px;font-weight:700;margin-bottom:1px;">📍 رابط الموقع</div>
      <div style="color:#34d399;font-size:9px;word-break:break-all;">${mapsLink}</div>
    </div>` : ''}

    ${repeatNotes.length ? `
    <div style="background:rgba(239,68,68,0.08);border:1px solid rgba(239,68,68,0.35);border-radius:7px;padding:6px 10px;margin-bottom:10px;">
      <div style="color:#f87171;font-size:8px;font-weight:900;margin-bottom:2px;">⚠️ مخالف متكرر</div>
      <div style="color:#fca5a5;font-size:9px;">${repeatNotes.join(' | ')}</div>
    </div>` : ''}

    <!-- ═══ صور المخالفة ═══ -->
    ${imgs.length ? `
    <div style="margin-bottom:12px;">
      <div style="font-size:10px;font-weight:700;color:#d4af37;margin-bottom:6px;border-right:3px solid #d4af37;padding-right:8px;">📸 صور المخالفة (${imgs.length})</div>
      <div style="display:flex;gap:8px;justify-content:center;">
        ${imgs.map((src,i)=>`
          <div style="position:relative;width:100px;height:100px;overflow:hidden;border-radius:8px;border:2px solid rgba(212,175,55,0.4);background:#0d1b2a;flex-shrink:0;">
            <img src="${src}" style="width:100px;height:100px;object-fit:cover;display:block;">
            <div style="position:absolute;bottom:3px;right:3px;background:rgba(0,0,0,0.7);color:#d4af37;font-size:7px;padding:2px 5px;border-radius:3px;font-weight:700;">صورة ${i+1}</div>
          </div>`
        ).join('')}
      </div>
    </div>` : '<div style="margin-bottom:12px;padding:8px;background:rgba(239,68,68,0.06);border:1px dashed rgba(239,68,68,0.3);border-radius:8px;text-align:center;color:#f87171;font-size:10px;">⚠️ لم يتم رفع صور للمخالفة</div>'}

    <!-- ═══ بيانات المراجعة ═══ -->
    ${violation.reviewedBy ? `
    <div style="background:rgba(16,185,129,0.06);border:1px solid rgba(16,185,129,0.25);border-radius:7px;padding:8px 10px;margin-bottom:10px;">
      <div style="color:#86efac;font-weight:700;font-size:10px;margin-bottom:4px;">✅ بيانات المراجعة</div>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:4px;font-size:9px;">
        <div><span style="color:#d4af37;">المشرف: </span>${esc(violation.reviewedBy)}</div>
        <div><span style="color:#d4af37;">الرقم: </span>${esc(violation.reviewerId)}</div>
        <div><span style="color:#d4af37;">التاريخ: </span>${esc(violation.reviewDate)}</div>
        <div><span style="color:#d4af37;">الوقت: </span>${esc(violation.reviewTime)}</div>
        ${violation.reviewNotes ? `<div style="grid-column:1/-1;"><span style="color:#d4af37;">الملاحظات: </span>${esc(violation.reviewNotes)}</div>` : ''}
      </div>
    </div>` : ''}

    <!-- ═══ إقرار بصحة المخالفة ═══ -->
    <div style="border:1.5px solid #d4af37;border-radius:10px;padding:10px;background:rgba(212,175,55,0.03);">
      <div style="text-align:center;font-size:11px;font-weight:900;color:#ffd700;margin-bottom:6px;">📜 إقرار بصحة المخالفة</div>
      <div style="font-size:9px;color:#cbd5e1;line-height:1.5;margin-bottom:8px;text-align:justify;">
        نُقِرّ بأن المعلومات الواردة صحيحة ودقيقة، وأن المخالفة رقم <span style="color:#ffd700;font-weight:700;">${esc(violation.refNumber)}</span> قد رُصِدَت وسُجِّلَت وفق الإجراءات المعتمدة في نظام مخالفات مشروع القدية.
      </div>
      <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:8px;">
        <div style="border:1px solid rgba(212,175,55,0.4);border-radius:8px;padding:8px;text-align:center;">
          <div style="font-size:9px;font-weight:700;color:#d4af37;margin-bottom:3px;">👮 المفتش</div>
          <div style="font-size:10px;color:#fff;font-weight:700;">${esc(violation.inspector)||'—'}</div>
          <div style="font-size:8px;color:rgba(255,255,255,0.4);">رقم: ${esc(violation.inspectorId)||'—'}</div>
          <div style="border-top:1px dashed rgba(212,175,55,0.4);margin-top:8px;padding-top:6px;">
            <div style="font-size:7px;color:rgba(255,255,255,0.3);">التوقيع</div>
            <div style="height:20px;border-bottom:1px solid rgba(255,255,255,0.15);margin-top:4px;"></div>
          </div>
        </div>
        <div style="border:1px solid rgba(212,175,55,0.4);border-radius:8px;padding:8px;text-align:center;">
          <div style="font-size:9px;font-weight:700;color:#d4af37;margin-bottom:3px;">👔 المشرف</div>
          <div style="font-size:10px;color:#fff;font-weight:700;">${esc(violation.reviewedBy)||'.............................'}</div>
          <div style="font-size:8px;color:rgba(255,255,255,0.4);">رقم: ${esc(violation.reviewerId)||'...............'}</div>
          <div style="border-top:1px dashed rgba(212,175,55,0.4);margin-top:8px;padding-top:6px;">
            <div style="font-size:7px;color:rgba(255,255,255,0.3);">التوقيع</div>
            <div style="height:20px;border-bottom:1px solid rgba(255,255,255,0.15);margin-top:4px;"></div>
          </div>
        </div>
        <div style="border:1px solid rgba(212,175,55,0.4);border-radius:8px;padding:8px;text-align:center;">
          <div style="font-size:9px;font-weight:700;color:#d4af37;margin-bottom:3px;">🏢 ممثل الشركة</div>
          <div style="font-size:10px;color:#fff;font-weight:700;">${esc(violation.companyName)||'.............................'}</div>
          <div style="font-size:8px;color:rgba(255,255,255,0.4);">الاسم: .............................</div>
          <div style="border-top:1px dashed rgba(212,175,55,0.4);margin-top:8px;padding-top:6px;">
            <div style="font-size:7px;color:rgba(255,255,255,0.3);">التوقيع</div>
            <div style="height:20px;border-bottom:1px solid rgba(255,255,255,0.15);margin-top:4px;"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- Footer -->
    <div style="margin-top:8px;text-align:center;color:rgba(255,255,255,0.25);font-size:7px;">
      نظام مخالفات القدية — Qiddiya Violations System | طُبع: ${new Date().toLocaleDateString('ar-SA')} | وثيقة: ${esc(violation.refNumber)}
    </div>
    <div style="margin-top:3px;text-align:center;padding:3px;border:1px solid rgba(239,68,68,0.25);border-radius:5px;color:rgba(239,68,68,0.4);font-size:6px;font-weight:900;letter-spacing:3px;">
      CONFIDENTIAL — سري — للاستخدام الرسمي فقط
    </div>
  </div>`

  document.body.appendChild(el);
  try {
    const canvas = await html2canvas(el, {
      scale: 2, useCORS: true, allowTaint: true,
      backgroundColor: '#070e1c', logging: false
    });
    const { jsPDF } = window.jspdf;
    const doc = new jsPDF({ orientation: 'portrait', unit: 'mm', format: 'a4' });
    const pageW = 210, pageH = 297;
    const margin = 5;
    const usableW = pageW - (margin * 2);
    const usableH = pageH - (margin * 2);

    const imgW = canvas.width;
    const imgH = canvas.height;
    const contentH = (imgH / imgW) * usableW;

    if (contentH <= usableH) {
      // يتناسب مع صفحة واحدة A4
      doc.addImage(canvas.toDataURL('image/jpeg', 0.92), 'JPEG', margin, margin, usableW, contentH);
    } else {
      // تقسيم على عدة صفحات A4
      const totalPages = Math.ceil(contentH / usableH);
      for (let page = 0; page < totalPages; page++) {
        if (page > 0) doc.addPage();
        const srcY = Math.round((page * usableH / contentH) * imgH);
        const srcH = Math.round((usableH / contentH) * imgH);
        const actualSrcH = Math.min(srcH, imgH - srcY);
        const pageCanvas = document.createElement('canvas');
        pageCanvas.width = imgW;
        pageCanvas.height = actualSrcH;
        const ctx = pageCanvas.getContext('2d');
        ctx.drawImage(canvas, 0, srcY, imgW, actualSrcH, 0, 0, imgW, actualSrcH);
        const pageContentH = (actualSrcH / imgW) * usableW;
        doc.addImage(pageCanvas.toDataURL('image/jpeg', 0.92), 'JPEG', margin, margin, usableW, pageContentH);
      }
    }

    const fileName = `violation_${violation.refNumber}.pdf`;
    const pdfBlob = doc.output('blob');
    const pdfUrl = URL.createObjectURL(pdfBlob);

    // عرض PDF في نافذة معاينة مع أزرار المشاركة والتنزيل
    let overlay = document.getElementById('pdfPreviewOverlay');
    if (!overlay) {
      overlay = document.createElement('div');
      overlay.id = 'pdfPreviewOverlay';
      overlay.style.cssText = 'position:fixed;inset:0;z-index:10000;background:rgba(0,0,0,0.95);display:flex;flex-direction:column;align-items:center;justify-content:center;padding:10px;';
      document.body.appendChild(overlay);
    }
    overlay.style.display = 'flex';

    const canShare = navigator.share && navigator.canShare;
    overlay.innerHTML = `
      <div style="display:flex;gap:10px;margin-bottom:10px;flex-wrap:wrap;justify-content:center;">
        ${canShare ? `<button id="pdfShareBtn" style="padding:10px 24px;border-radius:10px;border:1px solid #d4af37;background:rgba(212,175,55,0.2);color:#ffd700;font-size:14px;font-weight:700;cursor:pointer;">📤 مشاركة</button>` : ''}
        <button id="pdfDownloadBtn" style="padding:10px 24px;border-radius:10px;border:1px solid #34d399;background:rgba(16,185,129,0.15);color:#34d399;font-size:14px;font-weight:700;cursor:pointer;">💾 تحميل</button>
        <button id="pdfCloseBtn" style="padding:10px 24px;border-radius:10px;border:1px solid rgba(255,255,255,0.2);background:rgba(239,68,68,0.15);color:#f87171;font-size:14px;font-weight:700;cursor:pointer;">✕ إغلاق</button>
      </div>
      <iframe src="${pdfUrl}" style="width:100%;max-width:600px;height:calc(100vh - 80px);border:2px solid #d4af37;border-radius:10px;background:white;"></iframe>
    `;

    document.getElementById('pdfCloseBtn').onclick = () => {
      overlay.style.display = 'none';
      URL.revokeObjectURL(pdfUrl);
    };

    document.getElementById('pdfDownloadBtn').onclick = () => {
      const a = document.createElement('a');
      a.href = pdfUrl; a.download = fileName; a.click();
      showToast('✅ تم تحميل PDF');
    };

    if (canShare) {
      document.getElementById('pdfShareBtn').onclick = async () => {
        try {
          const file = new File([pdfBlob], fileName, { type: 'application/pdf' });
          if (navigator.canShare({ files: [file] })) {
            await navigator.share({
              title: `تقرير مخالفة ${violation.refNumber}`,
              text: `مخالفة ${violation.refNumber} — نظام القدية`,
              files: [file]
            });
            showToast('✅ تمت المشاركة بنجاح!');
          }
        } catch(e) {
          if (e.name !== 'AbortError') showToast('❌ خطأ في المشاركة','error');
        }
      };
    }

    showToast('✅ تم إنشاء PDF — يمكنك معاينته ومشاركته');
  } catch(err) {
    console.error(err);
    showToast('❌ خطأ في إنشاء PDF','error');
  } finally {
    document.body.removeChild(el);
  }
}

// ══════════════════════════════════════════════════════
// ADMIN PANEL
// ══════════════════════════════════════════════════════
function renderAdminPanel() {
  try {
  console.log('🛡️ renderAdminPanel called', { user: currentUser?.role, violations: violations.length });
  if (!currentUser || currentUser.role !== 'admin') { console.log('Not admin, skipping'); return; }
  const cont = document.getElementById('adminPanelContent');
  if (!cont) { console.log('adminPanelContent not found'); return; }

  const total    = violations.length;
  const pending  = violations.filter(v=>v.status==='pending').length;
  const approved = violations.filter(v=>v.status==='approved').length;
  const rejected = violations.filter(v=>v.status==='rejected').length;

  // ── تحليل المكررين (لوحة وهوية فقط) ──
  const plateMap = {}, idMap = {};
  violations.forEach(v => {
    if (v.vehiclePlate) { const k=v.vehiclePlate.trim().toUpperCase(); if(k){ plateMap[k]=(plateMap[k]||[]).concat(v); } }
    if (v.violatorId)   { const k=v.violatorId.trim(); if(k){ idMap[k]=(idMap[k]||[]).concat(v); } }
  });
  const repeatPlates = Object.entries(plateMap).filter(([,arr])=>arr.length>1).sort((a,b)=>b[1].length-a[1].length);
  const repeatIds    = Object.entries(idMap).filter(([,arr])=>arr.length>1).sort((a,b)=>b[1].length-a[1].length);

  // إحصائيات المفتشين
  const inspMap = {};
  violations.forEach(v=>{ inspMap[v.inspector]=(inspMap[v.inspector]||0)+1; });
  const topInsp = Object.entries(inspMap).sort((a,b)=>b[1]-a[1]).slice(0,10);

  // إحصائيات المجموعات
  const groupMap = {};
  violations.forEach(v=>{ const g=v.receivingGroup||'غير محدد'; groupMap[g]=(groupMap[g]||0)+1; });

  // قائمة المفتشين — من بيانات المخالفات (بدل allUsers الفارغ)
  const inspSet = {};
  violations.forEach(v => {
    if (v.inspectorId && v.inspector) {
      inspSet[v.inspectorId] = { name: v.inspector, role: 'employee' };
    }
  });
  const allInspList = Object.entries(inspSet).sort((a,b)=>a[1].name.localeCompare(b[1].name));

  // آخر 20 مخالفة
  const recent = [...violations].sort((a,b)=>new Date(b.date+' '+b.time)-new Date(a.date+' '+a.time)).slice(0,20);

  cont.innerHTML = `
    <!-- KPI Cards -->
    <div class="stats-row" style="margin-bottom:24px;">
      <div class="stat-card gold"><div class="stat-icon">📋</div><div class="stat-value">${total}</div><div class="stat-label">إجمالي المخالفات</div></div>
      <div class="stat-card amber"><div class="stat-icon">⏳</div><div class="stat-value">${pending}</div><div class="stat-label">قيد المراجعة</div></div>
      <div class="stat-card green"><div class="stat-icon">✅</div><div class="stat-value">${approved}</div><div class="stat-label">موافق عليها</div></div>
      <div class="stat-card red"><div class="stat-icon">❌</div><div class="stat-value">${rejected}</div><div class="stat-label">مرفوضة</div></div>
      <div class="stat-card blue"><div class="stat-icon">👮</div><div class="stat-value">${allInspList.length}</div><div class="stat-label">إجمالي المفتشين</div></div>
      <div class="stat-card" style="border-color:rgba(239,68,68,0.35);">
        <div class="stat-icon" style="font-size:28px;">🔁</div>
        <div class="stat-value" style="color:#f87171;">${repeatPlates.length + repeatIds.length}</div>
        <div class="stat-label">لوحات وهويات متكررة</div>
      </div>
    </div>

    <!-- ════ قسم المخالفين المتكررين ════ -->
    <div class="card" style="margin-bottom:20px;border-color:rgba(239,68,68,0.4);">
      <div class="card-header" style="background:rgba(239,68,68,0.06);">
        <h3 style="color:#f87171;">🔁 المخالفون المتكررون</h3>
      </div>
      <div class="card-body">

        <!-- مكرر بالهوية -->
        <div style="margin-bottom:18px;">
          <div style="font-size:13px;font-weight:700;color:#fbbf24;margin-bottom:10px;">🪪 مكرر بالهوية / الإقامة (${repeatIds.length} شخص)</div>
          ${repeatIds.length ? `
          <div style="overflow-x:auto;">
          <table class="report-table" style="min-width:600px;">
            <thead><tr><th>رقم الهوية</th><th>الاسم / الشركة</th><th style="text-align:center;">عدد المخالفات</th><th>التواريخ</th></tr></thead>
            <tbody>
              ${repeatIds.slice(0,15).map(([id,arr])=>`
                <tr>
                  <td style="font-family:monospace;color:#fbbf24;">${id}</td>
                  <td>${[...new Set(arr.map(x=>x.companyName))].join('، ')}</td>
                  <td style="text-align:center;"><span class="repeat-count-badge">${arr.length}x</span></td>
                  <td style="font-size:11px;color:var(--muted);">${arr.map(x=>x.date).join(' | ')}</td>
                </tr>`).join('')}
            </tbody>
          </table>
          </div>` : '<div style="color:var(--muted);padding:12px;">لا يوجد</div>'}
        </div>

        <!-- مكرر باللوحة -->
        <div style="margin-bottom:18px;border-top:1px solid var(--border);padding-top:16px;">
          <div style="font-size:13px;font-weight:700;color:#f87171;margin-bottom:10px;">🚗 مكرر بلوحة المركبة (${repeatPlates.length} لوحة)</div>
          ${repeatPlates.length ? `
          <div style="overflow-x:auto;">
          <table class="report-table" style="min-width:600px;">
            <thead><tr><th>اللوحة</th><th>الشركة</th><th style="text-align:center;">عدد المخالفات</th><th>التواريخ</th><th>نوع المخالفة</th></tr></thead>
            <tbody>
              ${repeatPlates.slice(0,15).map(([plate,arr])=>`
                <tr>
                  <td style="font-family:monospace;font-size:15px;font-weight:900;color:#f87171;letter-spacing:3px;">${plate}</td>
                  <td>${[...new Set(arr.map(x=>x.companyName))].join('، ')}</td>
                  <td style="text-align:center;"><span class="repeat-count-badge">${arr.length}x</span></td>
                  <td style="font-size:11px;color:var(--muted);">${arr.map(x=>x.date).join(' | ')}</td>
                  <td style="font-size:11px;color:var(--muted);">${arr.map(x=>x.mainViolationTypeLabel||x.subViolationType).join(' | ')}</td>
                </tr>`).join('')}
            </tbody>
          </table>
          </div>` : '<div style="color:var(--muted);padding:12px;">لا يوجد</div>'}
        </div>



      </div>
    </div>
    <!-- نهاية المخالفين المتكررين -->

    <!-- توزيع المجموعات -->
    <div class="card" style="margin-bottom:20px;">
      <div class="card-header"><h3>👥 توزيع المخالفات حسب المجموعة</h3></div>
      <div class="card-body">
        <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:12px;">
          ${Object.entries(groupMap).map(([g,c])=>`
            <div style="background:var(--gold-dim);border:1px solid var(--border);border-radius:10px;padding:14px;text-align:center;">
              <div style="font-size:11px;color:var(--gold);margin-bottom:6px;">${g}</div>
              <div style="font-size:26px;font-weight:900;color:#ffd700;">${c}</div>
            </div>`).join('')}
        </div>
      </div>
    </div>

    <!-- أنشط المفتشين -->
    <div class="charts-grid" style="margin-bottom:20px;">
      <div class="card">
        <div class="card-header"><h3>🏆 أنشط المفتشين</h3></div>
        <div class="card-body">
          <ul class="top-list">
            ${topInsp.map(([name,count],i)=>`
              <li>
                <div class="top-rank">${i+1}</div>
                <span class="top-name">${name}</span>
                <span class="top-count">${count}</span>
              </li>`).join('')}
          </ul>
        </div>
      </div>
      <div class="card">
        <div class="card-header"><h3>👮 قائمة المفتشين الكاملة (${allInspList.length})</h3></div>
        <div class="card-body" style="overflow-x:auto;max-height:320px;overflow-y:auto;">
          <table class="report-table" style="min-width:400px;">
            <thead><tr><th>#</th><th>الرقم الوظيفي</th><th>الاسم</th><th>المخالفات</th></tr></thead>
            <tbody>
              ${allInspList.map(([id,u],i)=>{
                const count = violations.filter(v=>v.inspectorId===id).length;
                return `<tr>
                  <td>${i+1}</td>
                  <td style="font-family:monospace;color:var(--gold);">${id}</td>
                  <td>${u.name}</td>
                  <td style="text-align:center;"><span style="background:var(--gold-dim);padding:2px 10px;border-radius:20px;color:var(--gold-light);">${count}</span></td>
                </tr>`;
              }).join('')}
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- آخر 20 مخالفة -->
    <div class="card">
      <div class="card-header"><h3>🕐 آخر 20 مخالفة مسجلة</h3></div>
      <div class="card-body" style="overflow-x:auto;">
        <table class="report-table" style="min-width:800px;">
          <thead>
            <tr><th>الرقم</th><th>النوع</th><th>الشركة</th><th>المفتش</th><th>المنطقة</th><th>التاريخ</th><th>الحالة</th><th>مكرر</th><th>PDF</th></tr>
          </thead>
          <tbody>
            ${recent.map(v=>{
              const stMap={pending:'⏳',approved:'✅',rejected:'❌'};
              const r = getRepeatInfo(v);
              const hasRepeat = r.plate.length||r.id.length;
              const repeatCell = hasRepeat
                ? `<span class="repeat-count-badge">${(r.plate.length?'🚗':'')+(r.id.length?'🪪':'')}</span>`
                : '—';
              return `<tr style="${hasRepeat?'background:rgba(239,68,68,0.04);':''}">
                <td style="color:var(--gold);font-size:11px;">${v.refNumber}</td>
                <td>${v.mainViolationTypeLabel||'—'}</td>
                <td>${v.companyName||'—'}</td>
                <td>${v.inspector||'—'}</td>
                <td>${v.zone||'—'}</td>
                <td>${v.date} ${v.time}</td>
                <td>${stMap[v.status]||'—'}</td>
                <td>${repeatCell}</td>
                <td><button class="btn-sm btn-pdf" onclick="generatePDF(violations.find(x=>x.refNumber==='${(v.refNumber||'').replace(/'/g,"\\'")}'))">📄</button></td>
              </tr>`;
            }).join('')}
          </tbody>
        </table>
      </div>
    </div>
  `;
  } catch(e) { 
    console.error('renderAdminPanel error:', e, e.stack);
    const cont = document.getElementById('adminPanelContent');
    if (cont) cont.innerHTML = `
      <div style="padding:30px;text-align:center;">
        <div style="color:#f87171;font-size:14px;margin-bottom:10px;">❌ خطأ في تحميل لوحة المراقب</div>
        <div style="color:var(--muted);font-size:12px;margin-bottom:16px;">${e.message}</div>
        <button onclick="renderAdminPanel()" style="padding:10px 24px;background:rgba(212,175,55,0.2);border:1px solid #d4af37;color:#ffd700;border-radius:8px;cursor:pointer;font-size:13px;">🔄 إعادة المحاولة</button>
      </div>`;
  }
}

// ══════════════════════════════════════════════════════
// 🚫 BLACKLIST MANAGEMENT — القوائم السوداء
// ══════════════════════════════════════════════════════
async function addToList(listName, inputId) {
  const input = document.getElementById(inputId);
  if (!input) return;
  const val = input.value.trim().toUpperCase();
  if (!val) { showToast('⚠️ أدخل القيمة أولاً','error'); return; }
  
  try {
    const doc = await db.collection('settings').doc('blacklists').get();
    const data = doc.exists ? doc.data() : { blockedPlates:[], blockedIds:[], wantedPlates:[], wantedIds:[] };
    if (!data[listName]) data[listName] = [];
    if (data[listName].includes(val)) { showToast('⚠️ موجود مسبقاً','error'); return; }
    data[listName].push(val);
    await db.collection('settings').doc('blacklists').set(data);
    localStorage.setItem(listName, JSON.stringify(data[listName]));
    input.value = '';
    showToast('✅ تمت الإضافة');
    renderBlacklists();
  } catch(e) { showToast('❌ خطأ: ' + e.message,'error'); }
}

async function removeFromList(listName, val) {
  try {
    const doc = await db.collection('settings').doc('blacklists').get();
    const data = doc.exists ? doc.data() : {};
    if (data[listName]) {
      data[listName] = data[listName].filter(v => v !== val);
      await db.collection('settings').doc('blacklists').set(data);
      localStorage.setItem(listName, JSON.stringify(data[listName]));
    }
    showToast('✅ تم الحذف');
    renderBlacklists();
  } catch(e) { showToast('❌ خطأ','error'); }
}

async function renderBlacklists() {
  try {
    const doc = await db.collection('settings').doc('blacklists').get();
    const data = doc.exists ? doc.data() : {};
    const lists = {
      blockedPlates: { el: 'blockedPlatesList', color: '#f87171', icon: '🚗' },
      blockedIds: { el: 'blockedIdsList', color: '#f87171', icon: '🪪' },
      wantedPlates: { el: 'wantedPlatesList', color: '#fbbf24', icon: '🚗' },
      wantedIds: { el: 'wantedIdsList', color: '#fbbf24', icon: '🪪' }
    };
    for (const [key, cfg] of Object.entries(lists)) {
      const el = document.getElementById(cfg.el);
      if (!el) continue;
      const items = data[key] || [];
      if (!items.length) { el.innerHTML = '<div style="color:var(--muted);font-size:12px;text-align:center;padding:10px;">لا توجد سجلات</div>'; continue; }
      el.innerHTML = items.map(v => `
        <div style="display:flex;justify-content:space-between;align-items:center;padding:6px 10px;margin-bottom:4px;background:rgba(0,0,0,0.2);border-radius:8px;border:1px solid rgba(255,255,255,0.05);">
          <span style="font-size:13px;font-weight:600;color:${cfg.color};">${cfg.icon} ${esc(v)}</span>
          <button onclick="removeFromList('${key}','${esc(v)}')" style="background:rgba(239,68,68,0.15);border:1px solid rgba(239,68,68,0.3);color:#f87171;padding:3px 10px;border-radius:6px;cursor:pointer;font-size:11px;">🗑️</button>
        </div>
      `).join('');
    }
  } catch(e) { console.warn('Render blacklists error:', e); }
}

// ══════════════════════════════════════════════════════
// 👥 EMPLOYEE MANAGEMENT — إدارة الموظفين
// ══════════════════════════════════════════════════════
function toggleNewUserFields() {
  const role = document.getElementById('newUserRole').value;
  document.getElementById('newUserGroupDiv').style.display = role === 'supervisor' ? 'block' : 'none';
}

async function addNewUser() {
  const role = document.getElementById('newUserRole').value;
  const empId = document.getElementById('newUserId').value.trim();
  const name = document.getElementById('newUserName').value.trim();
  const pass = document.getElementById('newUserPass').value.trim();
  const group = document.getElementById('newUserGroup')?.value || '';
  const resultDiv = document.getElementById('addUserResult');

  if (!empId || !name || !pass) {
    resultDiv.innerHTML = '<div style="color:#f87171;font-size:13px;">❌ جميع الحقول مطلوبة</div>';
    return;
  }
  if (pass.length < 6) {
    resultDiv.innerHTML = '<div style="color:#f87171;font-size:13px;">❌ كلمة المرور لازم 6 خانات على الأقل</div>';
    return;
  }

  try {
    const docId = role === 'supervisor' ? 'SUP-' + empId : 'EMP-' + empId;
    
    // تحقق إذا موجود
    const existing = await db.collection('users').doc(docId).get();
    if (existing.exists) {
      resultDiv.innerHTML = '<div style="color:#f87171;font-size:13px;">❌ هذا الرقم الوظيفي مسجّل مسبقاً</div>';
      return;
    }

    const userData = {
      employeeId: role === 'supervisor' ? 'SUP-' + empId : empId,
      name: name,
      password: pass,
      role: role,
      active: true,
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    };
    if (role === 'supervisor') userData.group = group;

    await db.collection('users').doc(docId).set(userData);
    
    resultDiv.innerHTML = `<div style="color:#34d399;font-size:13px;padding:10px;background:rgba(16,185,129,0.1);border-radius:8px;">
      ✅ تم إضافة ${role === 'supervisor' ? 'المشرف' : 'الموظف'} بنجاح!<br>
      <span style="color:#ffd700;">الرقم: ${userData.employeeId} | الاسم: ${name} | كلمة المرور: ${pass}</span>
    </div>`;
    
    // مسح الحقول
    document.getElementById('newUserId').value = '';
    document.getElementById('newUserName').value = '';
    document.getElementById('newUserPass').value = '';
    showToast('✅ تم إضافة المستخدم بنجاح');
  } catch(e) {
    resultDiv.innerHTML = `<div style="color:#f87171;font-size:13px;">❌ خطأ: ${e.message}</div>`;
  }
}

// ══════════════════════════════════════════════════════
// ◀ SIDEBAR TOGGLE — طي القائمة الجانبية
// ══════════════════════════════════════════════════════
function toggleSidebar() {
  document.body.classList.toggle('sidebar-collapsed');
  const btn = document.getElementById('sidebarToggle');
  if (btn) btn.textContent = document.body.classList.contains('sidebar-collapsed') ? '▶' : '◀';
  // تحديث الخريطة إذا مفتوحة
  setTimeout(() => { if (mapInstance) mapInstance.invalidateSize(); }, 350);
}

// ══════════════════════════════════════════════════════
// 🗺️ LIVE MAP — خريطة المخالفات الحية
// ══════════════════════════════════════════════════════
let mapInstance = null;
function renderLiveMap() {
  const mapEl = document.getElementById('violationMap');
  const statsEl = document.getElementById('liveMapStats');
  if (!mapEl) return;

  const located = violations.filter(v => v.location && v.location.lat && v.location.lng);
  
  if (mapInstance) { mapInstance.remove(); mapInstance = null; }
  
  // Default to Qiddiya coordinates
  const defaultLat = 24.53, defaultLng = 46.44;
  mapInstance = L.map('violationMap').setView([defaultLat, defaultLng], 13);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap', maxZoom: 19
  }).addTo(mapInstance);

  const statusIcons = {
    pending: '🟡', approved: '🟢', rejected: '🔴'
  };
  const statusLabels = {
    pending: 'قيد المراجعة', approved: 'موافق عليها', rejected: 'مرفوضة'
  };

  let bounds = [];
  located.forEach(v => {
    const lat = parseFloat(v.location.lat);
    const lng = parseFloat(v.location.lng);
    if (isNaN(lat) || isNaN(lng)) return;
    bounds.push([lat, lng]);
    
    const icon = L.divIcon({
      html: `<div style="font-size:24px;text-shadow:0 2px 8px rgba(0,0,0,0.5);">${statusIcons[v.status]||'🟡'}</div>`,
      className: 'map-marker-icon', iconSize: [30, 30], iconAnchor: [15, 15]
    });
    
    const marker = L.marker([lat, lng], { icon }).addTo(mapInstance);
    marker.bindPopup(`
      <div style="direction:rtl;font-family:Arial;min-width:200px;">
        <div style="font-weight:900;color:#d4af37;font-size:14px;margin-bottom:6px;">${v.refNumber}</div>
        <div style="font-size:12px;margin-bottom:3px;">📋 ${v.mainViolationTypeLabel||''} — ${v.subViolationType||''}</div>
        <div style="font-size:12px;margin-bottom:3px;">🏢 ${v.companyName||'—'}</div>
        <div style="font-size:12px;margin-bottom:3px;">📍 ${v.zone||'—'} | ${v.shift||'—'}</div>
        <div style="font-size:12px;margin-bottom:3px;">👮 ${v.inspector||'—'}</div>
        <div style="font-size:12px;margin-bottom:3px;">📅 ${v.date} ${v.time}</div>
        <div style="font-size:11px;margin-top:4px;padding:3px 8px;border-radius:10px;display:inline-block;
          background:${v.status==='approved'?'#0d3326':v.status==='rejected'?'#2d1212':'#2a1f00'};
          color:${v.status==='approved'?'#34d399':v.status==='rejected'?'#f87171':'#fbbf24'};">
          ${statusLabels[v.status]||'قيد المراجعة'}
        </div>
      </div>
    `, { maxWidth: 280 });
  });

  if (bounds.length > 0) {
    mapInstance.fitBounds(bounds, { padding: [30, 30] });
  }

  // إحصائيات أسفل الخريطة
  const zones = {};
  located.forEach(v => { const z = v.zone||'غير محدد'; zones[z] = (zones[z]||0)+1; });
  
  statsEl.innerHTML = `
    <div class="stats-row" style="margin-top:16px;">
      <div class="stat-card gold"><div class="stat-icon">📍</div><div class="stat-value">${located.length}</div><div class="stat-label">مخالفة بموقع GPS</div></div>
      <div class="stat-card amber"><div class="stat-icon">🚫</div><div class="stat-value">${violations.length - located.length}</div><div class="stat-label">بدون موقع</div></div>
      ${Object.entries(zones).map(([z,c]) => `
        <div class="stat-card"><div class="stat-icon">📍</div><div class="stat-value">${c}</div><div class="stat-label">${z}</div></div>
      `).join('')}
    </div>
  `;

  setTimeout(() => { if (mapInstance) mapInstance.invalidateSize(); }, 300);
}

// ══════════════════════════════════════════════════════
// 🤖 AI ANALYSIS — تحليل ذكي
// ══════════════════════════════════════════════════════
function renderAIAnalysis() {
  const cont = document.getElementById('aiAnalysisContent');
  if (!cont || !violations.length) {
    if (cont) cont.innerHTML = '<div style="text-align:center;padding:60px;color:var(--muted);">⚠️ لا توجد بيانات كافية للتحليل — سجّل مخالفات أولاً</div>';
    return;
  }

  // تحليل أوقات الذروة
  const hourMap = {};
  violations.forEach(v => {
    const h = v.time ? parseInt(v.time.split(':')[0]) : -1;
    if (h >= 0) hourMap[h] = (hourMap[h]||0)+1;
  });
  const peakHours = Object.entries(hourMap).sort((a,b) => b[1]-a[1]).slice(0,3);
  
  // تحليل المناطق الأكثر خطورة
  const zoneMap = {};
  violations.forEach(v => { const z = v.zone||'غير محدد'; zoneMap[z] = (zoneMap[z]||0)+1; });
  const hotZones = Object.entries(zoneMap).sort((a,b) => b[1]-a[1]);

  // تحليل الأيام
  const dayMap = {};
  violations.forEach(v => {
    if (v.date) {
      try {
        const d = new Date(v.date.replace(/[٠-٩]/g, d => '٠١٢٣٤٥٦٧٨٩'.indexOf(d)));
        const day = ['الأحد','الاثنين','الثلاثاء','الأربعاء','الخميس','الجمعة','السبت'][d.getDay()];
        if (day) dayMap[day] = (dayMap[day]||0)+1;
      } catch(e) {}
    }
  });
  const busyDays = Object.entries(dayMap).sort((a,b) => b[1]-a[1]).slice(0,3);

  // تحليل الشركات المتكررة
  const compMap = {};
  violations.forEach(v => { if (v.companyName) compMap[v.companyName] = (compMap[v.companyName]||0)+1; });
  const topCompanies = Object.entries(compMap).sort((a,b) => b[1]-a[1]).slice(0,5);

  // تحليل نسب الرفض
  const rejected = violations.filter(v => v.status === 'rejected').length;
  const approved = violations.filter(v => v.status === 'approved').length;
  const total = violations.length;
  const rejectRate = total ? ((rejected/total)*100).toFixed(1) : 0;
  const approvalRate = total ? ((approved/total)*100).toFixed(1) : 0;

  // مؤقت الاستجابة
  const responseTimes = [];
  violations.forEach(v => {
    if (v.date && v.time && v.reviewDate && v.reviewTime) {
      try {
        const created = new Date(v.date + ' ' + v.time);
        const reviewed = new Date(v.reviewDate + ' ' + v.reviewTime);
        const diffMin = (reviewed - created) / 60000;
        if (diffMin > 0 && diffMin < 10000) responseTimes.push(diffMin);
      } catch(e) {}
    }
  });
  const avgResponse = responseTimes.length ? (responseTimes.reduce((a,b) => a+b, 0) / responseTimes.length) : 0;

  // نوع المخالفة الأكثر
  const typeMap = {};
  violations.forEach(v => { const t = v.mainViolationTypeLabel||'غير محدد'; typeMap[t] = (typeMap[t]||0)+1; });
  const topType = Object.entries(typeMap).sort((a,b) => b[1]-a[1])[0];

  cont.innerHTML = `
    <!-- بطاقات التحليل الرئيسية -->
    <div class="stats-row" style="margin-bottom:24px;">
      <div class="stat-card gold"><div class="stat-icon" style="font-size:32px;">🤖</div><div class="stat-value">${total}</div><div class="stat-label">إجمالي البيانات المحللة</div></div>
      <div class="stat-card green"><div class="stat-icon">✅</div><div class="stat-value">${approvalRate}%</div><div class="stat-label">نسبة الموافقة</div></div>
      <div class="stat-card red"><div class="stat-icon">❌</div><div class="stat-value">${rejectRate}%</div><div class="stat-label">نسبة الرفض</div></div>
      <div class="stat-card blue"><div class="stat-icon">⏱️</div><div class="stat-value">${avgResponse > 0 ? Math.round(avgResponse) + ' د' : '—'}</div><div class="stat-label">متوسط وقت المراجعة</div></div>
    </div>

    <!-- التوصيات الذكية -->
    <div class="card" style="margin-bottom:20px;border-color:rgba(168,85,247,0.4);background:linear-gradient(135deg,rgba(168,85,247,0.05),rgba(59,130,246,0.05));">
      <div class="card-header" style="background:rgba(168,85,247,0.08);">
        <h3 style="color:#c084fc;">🧠 توصيات ذكية مبنية على البيانات</h3>
      </div>
      <div class="card-body" style="padding:20px;">
        <div style="display:grid;gap:16px;">
          
          ${peakHours.length ? `<div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;display:flex;gap:12px;align-items:flex-start;">
            <div style="font-size:28px;">🕐</div>
            <div>
              <div style="font-weight:800;color:var(--gold-light);margin-bottom:4px;">أوقات الذروة</div>
              <div style="font-size:13px;color:var(--text);">أكثر المخالفات تُسجّل في الساعة: ${peakHours.map(([h,c]) => `<span style="color:#fbbf24;font-weight:700;">${h}:00</span> (${c} مخالفة)`).join(' ، ')}</div>
              <div style="font-size:12px;color:#c084fc;margin-top:6px;font-weight:600;">💡 التوصية: ركّز الدوريات الأمنية في هذه الأوقات</div>
            </div>
          </div>` : ''}

          ${hotZones.length ? `<div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;display:flex;gap:12px;align-items:flex-start;">
            <div style="font-size:28px;">🔥</div>
            <div>
              <div style="font-weight:800;color:var(--gold-light);margin-bottom:4px;">المنطقة الأكثر مخالفات</div>
              <div style="font-size:13px;color:var(--text);">${hotZones.map(([z,c]) => `<span style="color:#f87171;font-weight:700;">${z}</span>: ${c} مخالفة`).join(' | ')}</div>
              <div style="font-size:12px;color:#c084fc;margin-top:6px;font-weight:600;">💡 التوصية: زيادة عدد المفتشين في ${hotZones[0]?.[0] || 'المنطقة'}</div>
            </div>
          </div>` : ''}

          ${topType ? `<div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;display:flex;gap:12px;align-items:flex-start;">
            <div style="font-size:28px;">⚠️</div>
            <div>
              <div style="font-weight:800;color:var(--gold-light);margin-bottom:4px;">النوع الأكثر تكراراً</div>
              <div style="font-size:13px;color:var(--text);"><span style="color:#fbbf24;font-weight:700;">${topType[0]}</span> — ${topType[1]} مخالفة (${((topType[1]/total)*100).toFixed(0)}% من الإجمالي)</div>
              <div style="font-size:12px;color:#c084fc;margin-top:6px;font-weight:600;">💡 التوصية: تعزيز التوعية بخصوص ${topType[0]}</div>
            </div>
          </div>` : ''}

          ${topCompanies.length ? `<div style="background:var(--card-hover);border:1px solid var(--border);border-radius:12px;padding:16px;display:flex;gap:12px;align-items:flex-start;">
            <div style="font-size:28px;">🏢</div>
            <div>
              <div style="font-weight:800;color:var(--gold-light);margin-bottom:4px;">الشركات الأكثر مخالفة</div>
              <div style="font-size:13px;color:var(--text);">${topCompanies.slice(0,3).map(([c,n]) => `<span style="color:#f87171;font-weight:700;">${c}</span> (${n})`).join(' ، ')}</div>
              <div style="font-size:12px;color:#c084fc;margin-top:6px;font-weight:600;">💡 التوصية: جدولة اجتماع مع هذه الشركات لمناقشة المخالفات</div>
            </div>
          </div>` : ''}

          ${parseFloat(rejectRate) > 20 ? `<div style="background:rgba(239,68,68,0.06);border:1px solid rgba(239,68,68,0.3);border-radius:12px;padding:16px;display:flex;gap:12px;align-items:flex-start;">
            <div style="font-size:28px;">📛</div>
            <div>
              <div style="font-weight:800;color:#f87171;margin-bottom:4px;">نسبة رفض مرتفعة!</div>
              <div style="font-size:13px;color:var(--text);">نسبة الرفض ${rejectRate}% — أعلى من المعدل الطبيعي (20%)</div>
              <div style="font-size:12px;color:#c084fc;margin-top:6px;font-weight:600;">💡 التوصية: تدريب المفتشين على تسجيل المخالفات بشكل أدق</div>
            </div>
          </div>` : `<div style="background:rgba(16,185,129,0.06);border:1px solid rgba(16,185,129,0.3);border-radius:12px;padding:16px;display:flex;gap:12px;align-items:flex-start;">
            <div style="font-size:28px;">✨</div>
            <div>
              <div style="font-weight:800;color:#34d399;margin-bottom:4px;">جودة تسجيل ممتازة!</div>
              <div style="font-size:13px;color:var(--text);">نسبة الرفض ${rejectRate}% فقط — المفتشون يسجّلون المخالفات بدقة عالية</div>
            </div>
          </div>`}

        </div>
      </div>
    </div>
  `;
}

// ══════════════════════════════════════════════════════
// 🏆 PERFORMANCE — تقييم أداء المفتشين
// ══════════════════════════════════════════════════════
function renderPerformance() {
  const cont = document.getElementById('performanceContent');
  if (!cont) return;
  if (!violations.length) {
    cont.innerHTML = '<div style="text-align:center;padding:60px;color:var(--muted);">⚠️ لا توجد بيانات كافية — سجّل مخالفات أولاً</div>';
    return;
  }

  // حساب إحصائيات كل مفتش
  const inspectors = {};
  violations.forEach(v => {
    if (!v.inspectorId) return;
    if (!inspectors[v.inspectorId]) {
      inspectors[v.inspectorId] = {
        name: v.inspector || v.inspectorId,
        total: 0, approved: 0, rejected: 0, pending: 0,
        responseTimes: [], zones: new Set(), shifts: new Set()
      };
    }
    const insp = inspectors[v.inspectorId];
    insp.total++;
    if (v.status === 'approved') insp.approved++;
    else if (v.status === 'rejected') insp.rejected++;
    else insp.pending++;
    if (v.zone) insp.zones.add(v.zone);
    if (v.shift) insp.shifts.add(v.shift);
    
    // وقت الاستجابة
    if (v.date && v.time && v.reviewDate && v.reviewTime) {
      try {
        const diff = (new Date(v.reviewDate+' '+v.reviewTime) - new Date(v.date+' '+v.time)) / 60000;
        if (diff > 0 && diff < 10000) insp.responseTimes.push(diff);
      } catch(e) {}
    }
  });

  // حساب النقاط والنجوم
  const ranked = Object.entries(inspectors).map(([id, insp]) => {
    const approvalRate = insp.total ? (insp.approved / insp.total) : 0;
    const avgResponse = insp.responseTimes.length ? insp.responseTimes.reduce((a,b)=>a+b,0)/insp.responseTimes.length : 999;
    
    // النقاط: مخالفات (40%) + نسبة موافقة (40%) + سرعة (20%)
    const volumeScore = Math.min(insp.total / 10, 10) * 4; // حتى 40
    const qualityScore = approvalRate * 40; // حتى 40
    const speedScore = avgResponse < 999 ? Math.max(0, (1 - avgResponse/480)) * 20 : 0; // حتى 20
    const totalScore = volumeScore + qualityScore + speedScore;
    const stars = Math.min(5, Math.max(0, Math.round(totalScore / 20)));
    
    return {
      id, ...insp,
      approvalRate: (approvalRate * 100).toFixed(0),
      avgResponse: insp.responseTimes.length ? Math.round(insp.responseTimes.reduce((a,b)=>a+b,0)/insp.responseTimes.length) : null,
      totalScore: totalScore.toFixed(1),
      stars
    };
  }).sort((a, b) => b.totalScore - a.totalScore);

  const medals = ['🥇', '🥈', '🥉'];
  const starStr = n => '★'.repeat(n) + '☆'.repeat(5-n);

  cont.innerHTML = `
    <div class="stats-row" style="margin-bottom:24px;">
      <div class="stat-card gold"><div class="stat-icon">👮</div><div class="stat-value">${ranked.length}</div><div class="stat-label">إجمالي المفتشين النشطين</div></div>
      <div class="stat-card green"><div class="stat-icon">🏆</div><div class="stat-value">${ranked.filter(r=>r.stars>=4).length}</div><div class="stat-label">أداء ممتاز (4+ نجوم)</div></div>
      <div class="stat-card amber"><div class="stat-icon">📊</div><div class="stat-value">${ranked.filter(r=>r.stars>=2&&r.stars<4).length}</div><div class="stat-label">أداء جيد</div></div>
      <div class="stat-card red"><div class="stat-icon">⚠️</div><div class="stat-value">${ranked.filter(r=>r.stars<2).length}</div><div class="stat-label">يحتاج تحسين</div></div>
    </div>

    <!-- أفضل 3 مفتشين -->
    ${ranked.length >= 3 ? `
    <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:16px;margin-bottom:24px;">
      ${ranked.slice(0,3).map((r, i) => `
        <div class="card" style="text-align:center;padding:24px;border-color:${i===0?'rgba(255,215,0,0.5)':i===1?'rgba(192,192,192,0.5)':'rgba(205,127,50,0.5)'};">
          <div style="font-size:48px;margin-bottom:8px;">${medals[i]}</div>
          <div style="font-size:16px;font-weight:900;color:var(--gold-light);margin-bottom:4px;">${r.name}</div>
          <div style="font-size:12px;color:var(--muted);margin-bottom:8px;">${r.id}</div>
          <div style="font-size:22px;color:#fbbf24;letter-spacing:4px;margin-bottom:8px;">${starStr(r.stars)}</div>
          <div style="font-size:24px;font-weight:900;color:var(--gold-light);">${r.totalScore} نقطة</div>
          <div style="font-size:12px;color:var(--muted);margin-top:8px;">${r.total} مخالفة | ${r.approvalRate}% موافقة${r.avgResponse ? ' | '+r.avgResponse+' د' : ''}</div>
        </div>
      `).join('')}
    </div>` : ''}

    <!-- جدول كل المفتشين -->
    <div class="card">
      <div class="card-header"><h3>📊 ترتيب جميع المفتشين</h3></div>
      <div class="card-body" style="overflow-x:auto;">
        <table class="report-table">
          <thead>
            <tr><th>#</th><th>المفتش</th><th>الرقم</th><th>التقييم</th><th>النقاط</th><th>المخالفات</th><th>موافق</th><th>مرفوض</th><th>نسبة الموافقة</th><th>متوسط المراجعة</th></tr>
          </thead>
          <tbody>
            ${ranked.map((r, i) => `
              <tr style="${i < 3 ? 'background:rgba(212,175,55,0.04);' : ''}">
                <td style="font-weight:900;color:var(--gold);">${i < 3 ? medals[i] : i+1}</td>
                <td style="font-weight:700;">${r.name}</td>
                <td style="font-family:monospace;font-size:11px;color:var(--muted);">${r.id}</td>
                <td style="color:#fbbf24;letter-spacing:2px;font-size:16px;">${starStr(r.stars)}</td>
                <td style="font-weight:900;color:var(--gold-light);">${r.totalScore}</td>
                <td>${r.total}</td>
                <td style="color:#34d399;">${r.approved}</td>
                <td style="color:#f87171;">${r.rejected}</td>
                <td><span style="padding:2px 10px;border-radius:20px;font-size:11px;background:${parseInt(r.approvalRate)>=80?'rgba(16,185,129,0.15);color:#34d399':parseInt(r.approvalRate)>=50?'rgba(251,191,36,0.15);color:#fbbf24':'rgba(239,68,68,0.15);color:#f87171'};">${r.approvalRate}%</span></td>
                <td>${r.avgResponse ? r.avgResponse + ' دقيقة' : '—'}</td>
              </tr>
            `).join('')}
          </tbody>
        </table>
      </div>
    </div>
  `;
}

// ══════════════════════════════════════════════════════
// 🔔 NOTIFICATION SOUND — صوت الإشعار
// ══════════════════════════════════════════════════════
function playNotificationSound() {
  try {
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();
    osc.connect(gain);
    gain.connect(ctx.destination);
    osc.type = 'sine';
    // لحن إشعار مميز: 3 نغمات صاعدة
    const now = ctx.currentTime;
    osc.frequency.setValueAtTime(523, now);      // C5
    osc.frequency.setValueAtTime(659, now + 0.12); // E5
    osc.frequency.setValueAtTime(784, now + 0.24); // G5
    gain.gain.setValueAtTime(0.3, now);
    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.5);
    osc.start(now);
    osc.stop(now + 0.5);
  } catch(e) { console.warn('Sound error:', e); }
}

function exportAdminReport() {
  if (!violations.length) { showToast('لا توجد مخالفات','error'); return; }
  const data = violations.map((v,i)=>({
    'م':i+1,'الرقم المرجعي':v.refNumber,
    'النوع الرئيسي':v.mainViolationTypeLabel,'المخالفة':v.subViolationType,
    'نوع التصريح':v.permitType,'المنطقة':v.zone,'المجموعة':v.receivingGroup||'—',
    'الشفت':v.shift,'الوقت':v.violationTime,'الشركة':v.companyName,
    'رقم الهوية':v.violatorId,'رقم الجوال':v.violatorPhone,
    'المفتش':v.inspector,'الرقم الوظيفي':v.inspectorId,
    'التاريخ':v.date,'الساعة':v.time,
    'الحالة':v.status==='pending'?'قيد المراجعة':v.status==='approved'?'موافق عليها':'مرفوضة',
    'المشرف':v.reviewedBy||'—','ملاحظات':v.reviewNotes||'—'
  }));
  const ws=XLSX.utils.json_to_sheet(data);
  const wb=XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb,ws,'التقرير الشامل');
  XLSX.writeFile(wb,`ADMIN_Report_${new Date().toISOString().split('T')[0]}.xlsx`);
  showToast(`✅ تم تصدير ${violations.length} مخالفة — Admin Report`);
}

// ══════════════════════════════════════════════════════
// GOOGLE SHEETS
// ══════════════════════════════════════════════════════
async function sendToGoogleSheets(v){
  // 🔥 لم نعد نحتاج Google Sheets — Firebase يتولى كل شيء
  // هذه الدالة موجودة للتوافق مع الكود القديم
  console.log('ℹ️ Firebase يتولى حفظ المخالفة:', v.refNumber);
}

// ══════════════════════════════════════════════════════
// EXCEL EXPORT
// ══════════════════════════════════════════════════════
function downloadExcelFile(){
  let exportList = violations;
  if (currentUser && currentUser.role === 'supervisor' && currentUser.group) {
    exportList = exportList.filter(v => v.receivingGroup === currentUser.group);
  }
  if(!exportList.length){showToast('لا توجد مخالفات للتصدير','error');return;}
  const data=exportList.map((v,i)=>({
    'م':i+1,'الرقم المرجعي':v.refNumber,
    'نوع المخالف':v.violatorType||'—','الجنسية':v.violatorNationality||'—',
    'اسم الشركة':v.companyName||'—',
    'النوع الرئيسي':v.mainViolationTypeLabel,'المخالفة':v.subViolationType,
    'تفاصيل المخالفة':v.violationDetail||'—',
    'عدد الأشخاص':v.personCount||'—','نوع التصريح':v.permitType,
    'المنطقة':v.zone,'المجموعة المستلمة':v.receivingGroup||'—',
    'الشفت':v.shift,
    'رقم الهوية':v.violatorId,
    'رقم الجوال':v.violatorPhone,'لوحة المركبة':v.vehiclePlate||'—',
    'رابط الموقع':v.location?`https://www.google.com/maps?q=${v.location.lat},${v.location.lng}`:'—',
    'المفتش':v.inspector,'الرقم الوظيفي':v.inspectorId,
    'التاريخ':v.date,'الساعة':v.time,
    'الحالة':v.status==='pending'?'قيد المراجعة':v.status==='approved'?'موافق عليها':'مرفوضة',
    'المشرف':v.reviewedBy||'—','رقم المشرف':v.reviewerId||'—',
    'تاريخ المراجعة':v.reviewDate||'—','وقت المراجعة':v.reviewTime||'—',
    'ملاحظات المشرف':v.reviewNotes||'—'
  }));
  const ws=XLSX.utils.json_to_sheet(data);
  const wb=XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb,ws,'المخالفات');
  XLSX.writeFile(wb,`مخالفات_القدية_${new Date().toISOString().split('T')[0]}.xlsx`);
  showToast(`✅ تم تصدير ${violations.length} مخالفة!`);
}

// ══════════════════════════════════════════════════════
// THEME TOGGLE
// ══════════════════════════════════════════════════════
function toggleTheme() {
  const isLight = document.body.classList.toggle('light-mode');
  const icon  = document.getElementById('themeIcon');
  const label = document.getElementById('themeLabel');
  if (isLight) {
    icon.textContent  = '🌙';
    label.textContent = 'ليلي';
  } else {
    icon.textContent  = '☀️';
    label.textContent = 'نهاري';
  }
  localStorage.setItem('theme', isLight ? 'light' : 'dark');
}

// ══════════════════════════════════════════════════════
// 🔔 NOTIFICATION SYSTEM — Firebase + Role-Based
// ══════════════════════════════════════════════════════
let notifications = [];

// حفظ إشعار في Firebase مع تحديد المستلم
async function addNotification(icon, title, desc, refNumber, targetRole, targetId) {
  // targetRole: 'supervisor' | 'admin' | 'employee'
  // targetId: رقم وظيفي محدد (اختياري — للمفتش عند الرفض)
  if (!currentUser) return;
  try {
    await db.collection('notifications').add({
      icon, title, desc, refNumber,
      targetRole: targetRole || 'admin',
      targetId: targetId || null,
      senderName: currentUser.name,
      senderId: currentUser.id,
      senderRole: currentUser.role,
      read: false,
      createdAt: firebase.firestore.FieldValue.serverTimestamp(),
      timestamp: Date.now()
    });
  } catch(e) { console.warn('Notification save failed:', e); }
  // تحديث محلي
  await loadMyNotifications();
  // 🔔 صوت إشعار
  playNotificationSound();
}

// تحميل الإشعارات الخاصة بي من Firebase
async function loadMyNotifications() {
  if (!currentUser) return;
  try {
    const myRole = currentUser.role;
    const myId = currentUser.id;
    
    // جلب الإشعارات الموجهة لدوري أو لي شخصياً
    const snap = await db.collection('notifications')
      .orderBy('timestamp', 'desc')
      .limit(50)
      .get();
    
    notifications = [];
    snap.forEach(doc => {
      const n = doc.data();
      n.docId = doc.id;
      // فلترة: أعرض فقط الإشعارات الموجّهة لي
      const isForMyRole = (n.targetRole === myRole);
      const isForMe = (n.targetId === myId);
      const isForAllSupervisors = (n.targetRole === 'supervisor' && myRole === 'supervisor');
      const isForAdmin = (n.targetRole === 'admin' && myRole === 'admin');
      
      if (isForMe || isForMyRole || isForAdmin) {
        notifications.push({
          id: doc.id,
          icon: n.icon,
          title: n.title,
          desc: n.desc,
          refNumber: n.refNumber,
          time: n.timestamp ? new Date(n.timestamp).toLocaleTimeString('ar-SA',{hour:'2-digit',minute:'2-digit'}) : '',
          date: n.timestamp ? new Date(n.timestamp).toLocaleDateString('ar-SA') : '',
          read: n.read || false,
          timestamp: n.timestamp
        });
      }
    });
    
    updateNotifBadges();
    renderNotifPanel();
  } catch(e) { console.warn('Load notifications error:', e); }
}

function updateNotifBadges() {
  const unread = notifications.filter(n => !n.read).length;
  // sidebar badge
  const sb = document.getElementById('sidebarNotifBadge');
  if (sb) { sb.style.display = unread ? 'inline-block' : 'none'; sb.textContent = unread; }
  // mobile badge
  const mb = document.getElementById('mobileNotifBadge');
  if (mb) { mb.style.display = unread ? 'inline-block' : 'none'; mb.textContent = unread; }
}

function openNotifPanel() {
  document.getElementById('notifPanel').classList.add('open');
  document.getElementById('notifOverlay').classList.add('show');
  // mark all read
  notifications.forEach(n => {
    n.read = true;
    // تحديث في Firebase
    try { db.collection('notifications').doc(n.id).update({ read: true }); } catch(e) {}
  });
  updateNotifBadges();
  renderNotifPanel();
}

function closeNotifPanel() {
  document.getElementById('notifPanel').classList.remove('open');
  document.getElementById('notifOverlay').classList.remove('show');
}

function renderNotifPanel() {
  const body = document.getElementById('notifBody');
  if (!body) return;
  if (!notifications.length) {
    body.innerHTML = '<div class="notif-empty"><div class="ne-icon">🔕</div><div style="font-size:14px;font-weight:700;">لا توجد إشعارات</div><div style="font-size:12px;margin-top:4px;">ستظهر الإشعارات هنا عند تسجيل أو مراجعة مخالفة</div></div>';
    return;
  }
  body.innerHTML = notifications.slice(0,30).map(n => `
    <div class="notif-item ${n.read?'':'unread'}" onclick="${n.refNumber?`closeNotifPanel();showTab('myViolations')`:''}" style="animation-delay:0s;">
      <div class="ni-top">
        <span class="ni-icon">${n.icon}</span>
        <span class="ni-title">${n.title}</span>
        <span class="ni-time">${n.time}</span>
      </div>
      <div class="ni-desc">${n.desc}</div>
    </div>
  `).join('');
}

function clearAllNotifications() {
  // حذف من Firebase
  notifications.forEach(n => {
    try { db.collection('notifications').doc(n.id).delete(); } catch(e) {}
  });
  notifications = [];
  updateNotifBadges();
  renderNotifPanel();
  showToast('✅ تم مسح جميع الإشعارات');
}

// ══════════════════════════════════════════════════════
// 📊 KPI DASHBOARD ENHANCEMENTS
// ══════════════════════════════════════════════════════
function renderKPIs(data) {
  const kpiRow = document.getElementById('kpiRow');
  if (!kpiRow) return;

  const total = data.length;
  const approved = data.filter(v=>v.status==='approved').length;
  const rejected = data.filter(v=>v.status==='rejected').length;
  const pending = data.filter(v=>v.status==='pending').length;

  // avg response time (mockup based on data)
  const reviewed = data.filter(v=>v.reviewDate);
  const avgResponseHours = reviewed.length ? Math.round(Math.random()*12+2) : 0;

  // today vs yesterday
  const today = new Date();
  const todayStr = today.toLocaleDateString('ar-SA');
  const todayCount = data.filter(v=>v.date===todayStr).length;

  // this week
  const weekAgo = new Date(today.getTime() - 7*24*60*60*1000);
  const weekCount = data.filter(v => {
    try { const d = new Date(v.date); return d >= weekAgo; } catch(e) { return false; }
  }).length || todayCount;

  // top company
  const coCounts = {};
  data.forEach(v=>{if(v.companyName)coCounts[v.companyName]=(coCounts[v.companyName]||0)+1;});
  const topCo = Object.entries(coCounts).sort((a,b)=>b[1]-a[1])[0];

  // approval rate
  const approvalRate = total > 0 ? Math.round((approved/total)*100) : 0;

  kpiRow.innerHTML = `
    <div class="kpi-card" style="--kpi-color:#ffd700;">
      <div class="kpi-icon">📅</div>
      <div class="kpi-value">${todayCount}</div>
      <div class="kpi-label">مخالفات اليوم</div>
      <div class="kpi-trend ${todayCount>3?'up':todayCount>0?'neutral':'down'}">${todayCount>3?'📈 نشط':'📊 طبيعي'}</div>
    </div>
    <div class="kpi-card" style="--kpi-color:#34d399;">
      <div class="kpi-icon">✅</div>
      <div class="kpi-value">${approvalRate}%</div>
      <div class="kpi-label">نسبة الموافقة</div>
      <div class="kpi-trend ${approvalRate>=70?'up':approvalRate>=50?'neutral':'down'}">${approvalRate>=70?'↑ ممتاز':approvalRate>=50?'→ متوسط':'↓ منخفض'}</div>
    </div>
    <div class="kpi-card" style="--kpi-color:#fbbf24;">
      <div class="kpi-icon">⏱️</div>
      <div class="kpi-value">${pending}</div>
      <div class="kpi-label">بانتظار المراجعة</div>
      <div class="kpi-trend ${pending>10?'down':pending>5?'neutral':'up'}">${pending>10?'⚠️ تراكم':pending>0?'⏳ عادي':'✅ صفر'}</div>
    </div>
    <div class="kpi-card" style="--kpi-color:#60a5fa;">
      <div class="kpi-icon">📆</div>
      <div class="kpi-value">${weekCount}</div>
      <div class="kpi-label">مخالفات الأسبوع</div>
    </div>
    ${topCo ? `<div class="kpi-card" style="--kpi-color:#c084fc;">
      <div class="kpi-icon">🏢</div>
      <div class="kpi-value" style="font-size:18px;">${topCo[0].length>18?topCo[0].slice(0,18)+'..':topCo[0]}</div>
      <div class="kpi-label">أكثر شركة مخالفة (${topCo[1]})</div>
    </div>` : ''}
  `;
}

// ══════════════════════════════════════════════════════
// 🕐 ACTIVITY TIMELINE
// ══════════════════════════════════════════════════════
function renderTimeline(data) {
  const tl = document.getElementById('activityTimeline');
  if (!tl) return;
  const recent = [...data].sort((a,b) => (b.timestamp||0) - (a.timestamp||0) || b.refNumber?.localeCompare(a.refNumber)).slice(0,10);
  if (!recent.length) {
    tl.innerHTML = '<div style="text-align:center;padding:20px;color:var(--muted);">لا توجد نشاطات بعد</div>';
    return;
  }
  tl.innerHTML = recent.map((v,i) => {
    const statusIcon = v.status==='approved'?'✅':v.status==='rejected'?'❌':'⏳';
    const statusText = v.status==='approved'?'تمت الموافقة':v.status==='rejected'?'مرفوضة':'قيد المراجعة';
    const actionText = v.reviewedBy
      ? `${statusIcon} ${statusText} بواسطة ${v.reviewedBy}`
      : `📝 تم التسجيل بواسطة ${v.inspector}`;
    return `<div class="tl-item" style="animation-delay:${i*0.05}s">
      <div class="tl-time">${v.reviewDate||v.date} — ${v.reviewTime||v.time}</div>
      <div class="tl-text">${actionText} — <span class="tl-ref">${v.refNumber}</span></div>
    </div>`;
  }).join('');
}

// ══════════════════════════════════════════════════════
// 🔍 ADVANCED SEARCH (Date Range)
// ══════════════════════════════════════════════════════
const _origApplyFilters = typeof applyFilters === 'function' ? applyFilters : null;

// ══════════════════════════════════════════════════════
// 🏢 COMPANY EMAIL — ADMIN CONTROLLED SYSTEM
// ══════════════════════════════════════════════════════
let companyEmails = JSON.parse(localStorage.getItem('qdy_companyEmails') || '{}');
let emailSendLog = JSON.parse(localStorage.getItem('qdy_emailSendLog') || '[]');
// emailStatus per violation: { refNumber: 'pending_email' | 'sent' | 'resolved' }
let violationEmailStatus = JSON.parse(localStorage.getItem('qdy_violationEmailStatus') || '{}');

function saveCompanyEmailSettings() {
  localStorage.setItem('qdy_companyEmails', JSON.stringify(companyEmails));
}

function loadCompanyEmailSettings() {
  renderCompanyEmailList();
  renderAdminEmailQueue();
  updateAdminEmailStats();
  renderEmailSendLog();
}

// ── صندوق المخالفات بانتظار قرار الأدمن ──
function renderAdminEmailQueue() {
  const container = document.getElementById('adminEmailQueue');
  if (!container) return;

  const filter = document.getElementById('adminEmailQueueFilter')?.value || 'pending_email';
  const search = (document.getElementById('adminEmailQueueSearch')?.value || '').toLowerCase();

  // مخالفات الشركات المدرجة فقط
  let list = violations.filter(v => v.violatorType === 'شركة مدرجة' && v.companyName);

  // تحديد حالة الإيميل لكل مخالفة
  list.forEach(v => {
    if (!violationEmailStatus[v.refNumber]) violationEmailStatus[v.refNumber] = 'pending_email';
  });

  // فلترة
  if (filter !== 'all') {
    list = list.filter(v => violationEmailStatus[v.refNumber] === filter);
  }
  if (search) {
    list = list.filter(v =>
      v.refNumber.toLowerCase().includes(search) ||
      (v.companyName||'').toLowerCase().includes(search) ||
      (v.violatorId||'').includes(search)
    );
  }

  // ترتيب: الأحدث أولاً
  list.sort((a,b) => (b.timestamp||0) - (a.timestamp||0));

  if (!list.length) {
    container.innerHTML = `<div style="text-align:center;padding:30px;color:var(--muted);">
      <div style="font-size:36px;margin-bottom:8px;opacity:0.4;">${filter==='pending_email'?'✅':'📭'}</div>
      <div style="font-size:13px;">${filter==='pending_email'?'لا توجد مخالفات معلّقة — ممتاز!':'لا توجد نتائج'}</div>
    </div>`;
    updateAdminEmailStats();
    return;
  }

  container.innerHTML = list.map(v => {
    const status = violationEmailStatus[v.refNumber] || 'pending_email';
    const hasEmail = companyEmails[v.companyName]?.email?.trim();
    const statusColors = {
      pending_email: { bg:'rgba(245,158,11,0.08)', border:'rgba(245,158,11,0.25)', color:'#fbbf24', icon:'📬', label:'بانتظار الإرسال' },
      sent:          { bg:'rgba(16,185,129,0.08)', border:'rgba(16,185,129,0.25)', color:'#34d399', icon:'✅', label:'تم الإرسال' },
      resolved:      { bg:'rgba(59,130,246,0.08)', border:'rgba(59,130,246,0.25)', color:'#60a5fa', icon:'🔧', label:'تم الحل' }
    };
    const st = statusColors[status] || statusColors.pending_email;
    const approvalStatus = v.status==='approved'?'✅ موافق عليها':v.status==='rejected'?'❌ مرفوضة':'⏳ قيد المراجعة';

    return `<div style="padding:14px 16px;border:1px solid ${st.border};background:${st.bg};border-radius:12px;margin-bottom:10px;">
      <div style="display:flex;justify-content:space-between;align-items:flex-start;gap:10px;flex-wrap:wrap;">
        <div style="flex:1;min-width:200px;">
          <div style="display:flex;align-items:center;gap:8px;margin-bottom:6px;">
            <span style="font-family:monospace;font-weight:900;color:var(--gold);font-size:14px;">${esc(v.refNumber)}</span>
            <span style="padding:2px 8px;border-radius:10px;font-size:10px;font-weight:700;background:${st.bg};border:1px solid ${st.border};color:${st.color};">${st.icon} ${st.label}</span>
            <span style="font-size:10px;color:var(--muted);">${approvalStatus}</span>
          </div>
          <div style="font-size:13px;font-weight:700;color:var(--text);margin-bottom:3px;">🏢 ${esc(v.companyName)}</div>
          <div style="font-size:11px;color:var(--muted);line-height:1.6;">
            ⚠️ ${esc(v.mainViolationTypeLabel)} — ${esc(v.subViolationType)}<br>
            📍 ${esc(v.zone)} | 🔄 ${esc(v.shift)} | 👮 ${esc(v.inspector)} | 📅 ${esc(v.date)} ${esc(v.time)}
          </div>
          ${hasEmail ? `<div style="font-size:11px;color:#c084fc;margin-top:4px;">📧 ${esc(companyEmails[v.companyName].email)}</div>` :
            `<div style="font-size:11px;color:#f87171;margin-top:4px;">⚠️ لا يوجد إيميل مسجل لهذه الشركة</div>`}
        </div>
        <div style="display:flex;flex-direction:column;gap:6px;min-width:140px;">
          ${status === 'pending_email' ? `
            <button class="btn-sm btn-approve" style="font-size:11px;padding:8px 14px;" onclick="adminSendEmail('${esc(v.refNumber)}')" ${!hasEmail?'disabled style="opacity:0.4;font-size:11px;padding:8px 14px;cursor:not-allowed;" title="سجّل إيميل الشركة أولاً"':''}>📧 إرسال للشركة</button>
            <button class="btn-sm btn-pdf" style="font-size:11px;padding:8px 14px;" onclick="adminMarkResolved('${esc(v.refNumber)}')">🔧 تم الحل</button>
          ` : status === 'sent' ? `
            <button class="btn-sm btn-pdf" style="font-size:11px;padding:8px 14px;" onclick="adminMarkResolved('${esc(v.refNumber)}')">🔧 تم الحل</button>
            <button class="btn-sm" style="font-size:11px;padding:8px 14px;background:rgba(245,158,11,0.1);border:1px solid rgba(245,158,11,0.3);color:#fbbf24;cursor:pointer;" onclick="adminResetStatus('${esc(v.refNumber)}')">↩️ إعادة</button>
          ` : `
            <button class="btn-sm" style="font-size:11px;padding:8px 14px;background:rgba(245,158,11,0.1);border:1px solid rgba(245,158,11,0.3);color:#fbbf24;cursor:pointer;" onclick="adminResetStatus('${esc(v.refNumber)}')">↩️ إعادة للمعلّقة</button>
          `}
          <button class="btn-sm btn-pdf" style="font-size:11px;padding:8px 14px;" onclick="sharePDFByIndex('${esc(v.refNumber)}')">📄 PDF</button>
        </div>
      </div>
    </div>`;
  }).join('');

  updateAdminEmailStats();
}

// ── إرسال إيميل يدوي من الأدمن ──
function adminSendEmail(refNumber) {
  const v = violations.find(x => x.refNumber === refNumber);
  if (!v) { showToast('❌ المخالفة غير موجودة', 'error'); return; }

  const co = v.companyName;
  const data = companyEmails[co];
  if (!data?.email?.trim()) {
    showToast('⚠️ سجّل إيميل الشركة أولاً من دليل الإيميلات أدناه', 'error');
    return;
  }

  const email = data.email.trim();
  const contactName = data.contactName || 'ممثل الشركة';
  const mapsLink = v.location ? `https://www.google.com/maps?q=${v.location.lat},${v.location.lng}` : '';

  const subject = `⚠️ إشعار مخالفة — ${co} — ${v.refNumber}`;
  const body = `
السيد/ة ${contactName}
ممثل شركة ${co}

السلام عليكم ورحمة الله وبركاته،

نحيطكم علماً بتسجيل مخالفة على شركتكم في مشروع القدية:

━━━━━━━━━━━━━━━━━━━━━━━━━
📋 الرقم المرجعي: ${v.refNumber}
⚠️ نوع المخالفة: ${v.mainViolationTypeLabel}
📝 التفاصيل: ${v.subViolationType}
📍 المنطقة: ${v.zone}
👥 المجموعة: ${v.receivingGroup || '—'}
🔄 الشفت: ${v.shift}
🪪 رقم هوية المخالف: ${v.violatorId}
📱 رقم الجوال: ${v.violatorPhone}
🚗 لوحة المركبة: ${v.vehiclePlate || '—'}
👮 المفتش: ${v.inspector}
📅 التاريخ: ${v.date}
⏰ الوقت: ${v.time}
📌 حالة المخالفة: ${v.status==='approved'?'موافق عليها':v.status==='rejected'?'مرفوضة':'قيد المراجعة'}
${mapsLink ? '📍 الموقع: ' + mapsLink : ''}
━━━━━━━━━━━━━━━━━━━━━━━━━

المطلوب: مراجعة المخالفة واتخاذ الإجراءات التصحيحية.

مع أطيب التحيات،
إدارة مشروع القدية
Qiddiya Violations Management
`.trim();

  sendCompanyEmailViaGAS(email, subject, body, v.refNumber, co);
  violationEmailStatus[refNumber] = 'sent';
  localStorage.setItem('qdy_violationEmailStatus', JSON.stringify(violationEmailStatus));
  addNotification('📧', 'تم الإرسال', `تم إرسال إيميل المخالفة ${refNumber} لشركة ${co}`, refNumber, 'admin');
  showToast(`📧 تم إرسال الإيميل لـ ${email}`);
  renderAdminEmailQueue();
}

// ── تحديث الحالة ──
function adminMarkResolved(refNumber) {
  violationEmailStatus[refNumber] = 'resolved';
  localStorage.setItem('qdy_violationEmailStatus', JSON.stringify(violationEmailStatus));
  addNotification('🔧', 'تم الحل', `تم تحديث المخالفة ${refNumber} كـ "تم الحل"`, refNumber, 'admin');
  showToast('🔧 تم تحديث الحالة — تم الحل');
  renderAdminEmailQueue();
}

function adminResetStatus(refNumber) {
  violationEmailStatus[refNumber] = 'pending_email';
  localStorage.setItem('qdy_violationEmailStatus', JSON.stringify(violationEmailStatus));
  showToast('↩️ تم إعادة المخالفة للمعلّقة');
  renderAdminEmailQueue();
}

// ── إرسال جميع المعلّقة دفعة واحدة ──
function sendAllPendingCompanyEmails() {
  const pending = violations.filter(v =>
    v.violatorType === 'شركة مدرجة' &&
    v.companyName &&
    violationEmailStatus[v.refNumber] === 'pending_email' &&
    companyEmails[v.companyName]?.email?.trim()
  );
  if (!pending.length) {
    showToast('✅ لا توجد مخالفات معلّقة بإيميلات مسجلة', 'error');
    return;
  }
  if (!confirm(`سيتم إرسال ${pending.length} إيميل — متأكد؟`)) return;
  pending.forEach(v => adminSendEmail(v.refNumber));
  showToast(`📧 تم إرسال ${pending.length} إيميل`);
}

// ── إحصائيات ──
function updateAdminEmailStats() {
  const companyViolations = violations.filter(v => v.violatorType === 'شركة مدرجة' && v.companyName);
  companyViolations.forEach(v => {
    if (!violationEmailStatus[v.refNumber]) violationEmailStatus[v.refNumber] = 'pending_email';
  });

  const pending = companyViolations.filter(v => violationEmailStatus[v.refNumber] === 'pending_email').length;
  const sent = companyViolations.filter(v => violationEmailStatus[v.refNumber] === 'sent').length;
  const resolved = companyViolations.filter(v => violationEmailStatus[v.refNumber] === 'resolved').length;
  const registered = Object.values(companyEmails).filter(d => d.email?.trim()).length;

  const el1 = document.getElementById('pendingEmailCount'); if(el1) el1.textContent = pending;
  const el2 = document.getElementById('sentEmailCount'); if(el2) el2.textContent = sent;
  const el3 = document.getElementById('resolvedEmailCount'); if(el3) el3.textContent = resolved;
  const el4 = document.getElementById('registeredCompanyCount'); if(el4) el4.textContent = registered;
}

// ── إرسال عبر Google Apps Script ──
function sendCompanyEmailViaGAS(to, subject, body, refNumber, companyName) {
  // 🔥 إرسال الإيميل يحتاج backend — يمكن ربطه لاحقاً مع Firebase Cloud Functions
  showToast('📧 تم تسجيل طلب إرسال الإيميل — يتطلب إعداد Cloud Functions', 'error');
  // تسجيل في Firebase للمتابعة
  try {
    db.collection('emailQueue').add({
      to, subject, body, refNumber, companyName,
      status: 'pending',
      createdAt: firebase.firestore.FieldValue.serverTimestamp()
    });
  } catch(e) { console.warn('Email queue save failed:', e); }
}

// ── جدول إيميلات الشركات ──
function renderCompanyEmailList(filter) {
  const container = document.getElementById('companyEmailList');
  if (!container) return;
  let companies = companyList;
  if (filter) companies = companies.filter(c => c.toLowerCase().includes(filter.toLowerCase()));
  if (!companies.length) { container.innerHTML = '<div style="text-align:center;padding:20px;color:var(--muted);">لا توجد نتائج</div>'; return; }

  container.innerHTML = `<table style="width:100%;border-collapse:collapse;font-size:12px;">
    <thead><tr style="background:rgba(168,85,247,0.1);position:sticky;top:0;z-index:1;">
      <th style="padding:10px 12px;text-align:right;color:#c084fc;font-weight:700;border-bottom:1px solid var(--border);width:30%;">الشركة</th>
      <th style="padding:10px 12px;text-align:right;color:#c084fc;font-weight:700;border-bottom:1px solid var(--border);width:30%;">إيميل الممثل</th>
      <th style="padding:10px 12px;text-align:right;color:#c084fc;font-weight:700;border-bottom:1px solid var(--border);width:20%;">اسم الممثل</th>
      <th style="padding:10px 12px;text-align:center;color:#c084fc;font-weight:700;border-bottom:1px solid var(--border);width:10%;">مخالفات</th>
      <th style="padding:10px 12px;text-align:center;color:#c084fc;font-weight:700;border-bottom:1px solid var(--border);width:10%;">حالة</th>
    </tr></thead><tbody>
    ${companies.map(co => {
      const d = companyEmails[co] || {};
      const has = d.email?.trim();
      const vCount = violations.filter(v => v.companyName === co).length;
      return `<tr style="border-bottom:1px solid rgba(255,255,255,0.03);${has?'background:rgba(16,185,129,0.03);':''}">
        <td style="padding:8px 12px;font-weight:600;color:var(--text);">${esc(co)}</td>
        <td style="padding:6px 8px;"><input type="email" class="form-input" style="padding:8px 10px;font-size:12px;min-height:36px;" placeholder="email@company.com" value="${esc(d.email||'')}" onchange="updateCompanyEmail('${esc(co)}','email',this.value)"></td>
        <td style="padding:6px 8px;"><input type="text" class="form-input" style="padding:8px 10px;font-size:12px;min-height:36px;" placeholder="اسم الممثل" value="${esc(d.contactName||'')}" onchange="updateCompanyEmail('${esc(co)}','contactName',this.value)"></td>
        <td style="padding:8px;text-align:center;font-weight:700;color:${vCount>0?'#f87171':'var(--muted)'};">${vCount}</td>
        <td style="padding:8px;text-align:center;">${has?'<span style="color:#34d399;" title="مسجل">✅</span>':'<span style="color:var(--muted);" title="غير مسجل">⬜</span>'}</td>
      </tr>`;
    }).join('')}</tbody></table>`;
}

function filterCompanyEmails() { renderCompanyEmailList(document.getElementById('companyEmailSearch')?.value); }
function updateCompanyEmail(company, field, value) {
  if (!companyEmails[company]) companyEmails[company] = {};
  companyEmails[company][field] = value.trim();
  saveCompanyEmailSettings();
  updateAdminEmailStats();
}

// ── تقرير يومي مجمّع ──
function sendDailyCompanyReport() {
  const todayStr = new Date().toLocaleDateString('ar-SA');
  const todayViolations = violations.filter(v => v.date === todayStr && v.violatorType === 'شركة مدرجة');
  if (!todayViolations.length) { showToast('📊 لا توجد مخالفات شركات مدرجة اليوم', 'error'); return; }
  const byCompany = {};
  todayViolations.forEach(v => { if (!byCompany[v.companyName]) byCompany[v.companyName] = []; byCompany[v.companyName].push(v); });
  let sent = 0, skipped = 0;
  Object.entries(byCompany).forEach(([co, vList]) => {
    const d = companyEmails[co];
    if (!d?.email?.trim()) { skipped++; return; }
    const subject = `📊 تقرير مخالفات يومي — ${co} — ${todayStr} (${vList.length} مخالفة)`;
    let body = `السيد/ة ${d.contactName||'ممثل الشركة'}\nممثل شركة ${co}\n\nالسلام عليكم،\n\nتقرير مخالفات شركتكم اليوم ${todayStr}:\n\nإجمالي: ${vList.length} مخالفة\n${'═'.repeat(40)}\n`;
    vList.forEach((v,i) => { body += `\n— المخالفة ${i+1} —\n📋 ${v.refNumber} | ⚠️ ${v.mainViolationTypeLabel} — ${v.subViolationType}\n📍 ${v.zone} | 🪪 ${v.violatorId} | 🚗 ${v.vehiclePlate||'—'} | ⏰ ${v.time}\n`; });
    body += `\n${'═'.repeat(40)}\nالمطلوب: مراجعة المخالفات واتخاذ الإجراءات.\n\nإدارة مشروع القدية`;
    sendCompanyEmailViaGAS(d.email.trim(), subject, body, 'DAILY_'+todayStr, co);
    vList.forEach(v => { violationEmailStatus[v.refNumber] = 'sent'; });
    sent++;
  });
  localStorage.setItem('qdy_violationEmailStatus', JSON.stringify(violationEmailStatus));
  if (sent) { showToast(`📊 تم إرسال التقرير اليومي إلى ${sent} شركة`); addNotification('📊','تقرير يومي',`تم إرسال ${sent} تقرير يومي`,null,'admin'); }
  else showToast('⚠️ لا توجد شركات بإيميل مسجل','error');
  renderAdminEmailQueue();
}

// ── سجل الإرسال ──
function logEmailSend(company, email, ref, type, status) {
  emailSendLog.unshift({ company, email, ref, type, status, time:new Date().toLocaleTimeString('ar-SA',{hour:'2-digit',minute:'2-digit'}), date:new Date().toLocaleDateString('ar-SA'), timestamp:Date.now() });
  if (emailSendLog.length > 100) emailSendLog = emailSendLog.slice(0,100);
  localStorage.setItem('qdy_emailSendLog', JSON.stringify(emailSendLog));
  renderEmailSendLog();
}
function renderEmailSendLog() {
  const el = document.getElementById('emailSendLog');
  if (!el) return;
  if (!emailSendLog.length) { el.innerHTML = '<div style="text-align:center;padding:16px;color:var(--muted);font-size:12px;">لا يوجد سجل بعد</div>'; return; }
  el.innerHTML = emailSendLog.slice(0,20).map(e => {
    const si = e.status==='success'?'✅':e.status==='error'?'❌':'⏳';
    return `<div style="display:flex;align-items:center;gap:10px;padding:8px 12px;border-bottom:1px solid rgba(255,255,255,0.03);font-size:11px;">
      <span>${si}</span><span style="color:var(--muted);">${e.time}</span>
      <span style="color:var(--text);font-weight:600;flex:1;">${esc(e.company)}</span>
      <span style="color:var(--muted);direction:ltr;font-size:10px;">${esc(e.email)}</span>
      <span style="font-size:10px;color:var(--gold);">${esc(e.ref)}</span>
    </div>`;
  }).join('');
}

// ── تصدير ──
function exportCompanyEmails() {
  const data = companyList.map(co => ({ 'الشركة':co, 'الإيميل':companyEmails[co]?.email||'', 'اسم الممثل':companyEmails[co]?.contactName||'', 'عدد المخالفات':violations.filter(v=>v.companyName===co).length }));
  const ws = XLSX.utils.json_to_sheet(data);
  const wb = XLSX.utils.book_new();
  XLSX.utils.book_append_sheet(wb, ws, 'إيميلات الشركات');
  XLSX.writeFile(wb, `company_emails_${new Date().toISOString().split('T')[0]}.xlsx`);
  showToast('✅ تم التصدير');
}

// ══════════════════════════════════════════════════════
// 📱 PWA SUPPORT
// ══════════════════════════════════════════════════════
function initPWA() {
  // Dynamic manifest
  const manifest = {
    name: 'نظام مخالفات القدية',
    short_name: 'مخالفات القدية',
    description: 'Qiddiya Violations Management System',
    start_url: window.location.href,
    display: 'standalone',
    background_color: '#060d1a',
    theme_color: '#d4af37',
    orientation: 'portrait-primary',
    icons: [{
      src: 'data:image/svg+xml,' + encodeURIComponent('<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><rect width="100" height="100" rx="20" fill="#0d1b2a"/><text x="50" y="65" text-anchor="middle" font-size="50">⚠️</text></svg>'),
      sizes: '192x192', type: 'image/svg+xml'
    }]
  };
  const blob = new Blob([JSON.stringify(manifest)], {type:'application/json'});
  const link = document.getElementById('pwaManifest');
  if (link) link.href = URL.createObjectURL(blob);
}

// ══════════════════════════════════════════════════════
// INIT
// ══════════════════════════════════════════════════════
window.onload = () => {
  loadFromStorage();
  initPWA();
  updateNotifBadges();
  renderNotifPanel();
  // استعادة الثيم المحفوظ
  if (localStorage.getItem('theme') === 'light') {
    document.body.classList.add('light-mode');
    document.getElementById('themeIcon').textContent  = '🌙';
    document.getElementById('themeLabel').textContent = 'ليلي';
  }
};
document.addEventListener('keypress', e => {
  if (e.key === 'Enter' && document.getElementById('mainApp').classList.contains('hide')) login();
});
</script>
<!-- Image Zoom Modal -->
<div class="img-zoom-overlay" id="imgZoomOverlay" onclick="closeImageZoom()">
  <button class="img-zoom-close" onclick="closeImageZoom()">✕</button>
  <img id="imgZoomImg" src="" alt="zoom">
</div>

<script>
function openImageZoom(src) {
  document.getElementById('imgZoomImg').src = src;
  document.getElementById('imgZoomOverlay').classList.add('show');
}
function closeImageZoom() {
  document.getElementById('imgZoomOverlay').classList.remove('show');
  document.getElementById('imgZoomImg').src = '';
}

function onViolatorTypeChange() {
  const type = document.getElementById('violatorType').value;
  const natField = document.getElementById('nationalityField');
  const compField = document.getElementById('violatorCompanyField');
  if (type === 'موظف' || type === 'مقاول' || type === 'مقاول بالباطن' || type === 'زائر') {
    natField.style.display = 'block';
    compField.style.display = 'none';
    document.getElementById('companyName').value = type;
    document.getElementById('companyDisplayText').textContent = type;
  } else if (type === 'شركة مدرجة') {
    natField.style.display = 'none';
    compField.style.display = 'block';
    document.getElementById('companyName').value = '';
    document.getElementById('companyDisplayText').textContent = '— اختر الشركة —';
    document.getElementById('companyDisplayText').style.color = 'var(--muted)';
  } else {
    natField.style.display = 'none';
    compField.style.display = 'none';
  }
}
</script>
</body>
</html>
