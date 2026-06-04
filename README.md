<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>MMO Store – Sản Phẩm Số</title>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#0f1117;--bg2:#161b27;--bg3:#1e2535;--card:#1a2035;--border:#2a3450;
  --primary:#3b82f6;--primary-dark:#2563eb;--primary-light:#60a5fa;
  --text:#e2e8f0;--muted:#8892a4;--green:#34d399;--red:#f87171;
  --orange:#fb923c;--yellow:#fbbf24;--radius:10px;--radius-sm:6px;
  scrollbar-width:thin;scrollbar-color:var(--border) transparent;
}
*::-webkit-scrollbar{width:6px;height:6px}
*::-webkit-scrollbar-thumb{background:var(--border);border-radius:3px}
html,body{height:100%;background:var(--bg);color:var(--text);font-family:'Inter',-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;font-size:15px;line-height:1.6}
a{color:inherit;text-decoration:none}
button{cursor:pointer;font-family:inherit;font-size:inherit;border:none;outline:none}

/* ── Layout ── */
#app{min-height:100vh;display:flex;flex-direction:column}
#navbar{position:sticky;top:0;z-index:100;background:var(--bg2);border-bottom:1px solid var(--border);height:54px;display:flex;align-items:center;padding:0 20px;gap:12px;flex-shrink:0}
.nav-brand{display:flex;align-items:center;gap:7px;font-weight:800;font-size:1.05rem;color:var(--primary);white-space:nowrap}
.nav-links{display:flex;gap:4px;flex:1}
.nav-right{display:flex;align-items:center;gap:8px;margin-left:auto}
.nav-balance{background:rgba(52,211,153,.1);color:var(--green);font-weight:700;font-size:.82rem;padding:4px 10px;border-radius:20px;font-variant-numeric:tabular-nums;white-space:nowrap}
nav-btn,.nav-btn{background:none;color:var(--muted);padding:5px 10px;border-radius:var(--radius-sm);font-size:.88rem;font-weight:500;transition:all .15s;white-space:nowrap}
.nav-btn:hover,.nav-btn.active{background:rgba(59,130,246,.12);color:var(--primary-light)}
.hamburger{display:none;background:none;color:var(--muted);padding:6px;border-radius:var(--radius-sm)}
.hamburger:hover{color:var(--text)}
#mobile-menu{display:none;position:fixed;inset:54px 0 0 0;background:var(--bg2);z-index:99;overflow-y:auto;border-top:1px solid var(--border)}
#mobile-menu.open{display:flex;flex-direction:column}
.mobile-menu-inner{padding:12px;display:flex;flex-direction:column;gap:4px}
.mobile-bal{padding:10px 12px;border-bottom:1px solid var(--border);font-weight:700;color:var(--green)}
#page{flex:1;width:100%;max-width:1100px;margin:0 auto;padding:28px 20px}
footer{border-top:1px solid var(--border);padding:18px 20px;text-align:center;font-size:.78rem;color:var(--muted)}

/* ── Overlay/Drawer ── */
.overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.65);z-index:200;align-items:center;justify-content:center;padding:16px}
.overlay.open{display:flex}
.modal{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);width:100%;max-width:480px;max-height:90vh;overflow-y:auto;padding:24px;position:relative}
.modal-title{font-size:1.05rem;font-weight:700;margin-bottom:16px}
.modal-close{position:absolute;top:14px;right:14px;background:none;color:var(--muted);font-size:1.2rem;width:28px;height:28px;border-radius:50%;display:flex;align-items:center;justify-content:center}
.modal-close:hover{background:var(--bg3);color:var(--text)}

/* ── Buttons ── */
.btn{display:inline-flex;align-items:center;justify-content:center;gap:6px;padding:9px 18px;border-radius:var(--radius-sm);font-weight:600;font-size:.9rem;transition:all .15s;white-space:nowrap;border:none}
.btn-sm{padding:5px 12px;font-size:.82rem;border-radius:var(--radius-sm)}
.btn-primary{background:var(--primary);color:#fff}.btn-primary:hover{background:var(--primary-dark)}
.btn-secondary{background:var(--bg3);color:var(--text);border:1px solid var(--border)}.btn-secondary:hover{background:var(--border)}
.btn-danger{background:rgba(248,113,113,.15);color:var(--red);border:1px solid rgba(248,113,113,.3)}.btn-danger:hover{background:rgba(248,113,113,.25)}
.btn-success{background:rgba(52,211,153,.15);color:var(--green);border:1px solid rgba(52,211,153,.3)}.btn-success:hover{background:rgba(52,211,153,.25)}
.btn-ghost{background:none;color:var(--muted)}.btn-ghost:hover{background:var(--bg3);color:var(--text)}
.btn:disabled{opacity:.5;cursor:not-allowed}
.btn-full{width:100%}

/* ── Forms ── */
.form-group{margin-bottom:14px}
.form-label{display:block;font-size:.82rem;color:var(--muted);margin-bottom:5px;font-weight:500}
.form-input,.form-textarea,.form-select{width:100%;background:var(--bg3);border:1px solid var(--border);border-radius:var(--radius-sm);color:var(--text);padding:9px 12px;font-size:.95rem;font-family:inherit;transition:border-color .15s;outline:none}
.form-input:focus,.form-textarea:focus,.form-select:focus{border-color:var(--primary)}
.form-textarea{resize:vertical;min-height:80px}
.form-select option{background:var(--bg2)}
.quick-amounts{display:flex;gap:6px;flex-wrap:wrap;margin-top:6px}

/* ── Cards ── */
.card{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);overflow:hidden}
.card-body{padding:18px}
.card-header{padding:14px 18px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;gap:8px}
.card-title{font-weight:700;font-size:.95rem}

/* ── Stat cards ── */
.stats-grid{display:grid;grid-template-columns:repeat(2,1fr);gap:14px;margin-bottom:24px}
.stat-card{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);padding:16px}
.stat-icon{width:36px;height:36px;border-radius:8px;display:flex;align-items:center;justify-content:center;font-size:1.1rem;margin-bottom:10px}
.stat-val{font-size:1.4rem;font-weight:800;font-variant-numeric:tabular-nums}
.stat-lbl{font-size:.75rem;color:var(--muted);text-transform:uppercase;letter-spacing:.06em;margin-top:2px}

/* ── Product Grid ── */
.products-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(190px,1fr));gap:14px}
.product-card{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);overflow:hidden;cursor:pointer;transition:transform .2s,border-color .2s;display:flex;flex-direction:column}
.product-card:hover{transform:translateY(-3px);border-color:var(--primary)}
.product-thumb{aspect-ratio:16/9;overflow:hidden;background:var(--bg3);width:100%}
.product-thumb img{width:100%;height:100%;object-fit:cover;transition:transform .3s}
.product-card:hover .product-thumb img{transform:scale(1.05)}
.product-info{padding:10px 12px;flex:1;display:flex;flex-direction:column}
.product-cat{font-size:.72rem;color:var(--primary-light);font-weight:600;text-transform:uppercase;letter-spacing:.05em;margin-bottom:4px}
.product-name{font-size:.9rem;font-weight:600;line-height:1.4;flex:1;overflow:hidden;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical}
.product-price{font-size:1rem;font-weight:800;color:var(--primary);margin-top:8px;font-variant-numeric:tabular-nums}

/* ── Tables ── */
.table-wrap{overflow-x:auto}
table{width:100%;border-collapse:collapse;white-space:nowrap;font-size:.88rem}
th{text-align:left;padding:8px 14px;font-size:.75rem;color:var(--muted);font-weight:600;text-transform:uppercase;letter-spacing:.05em;border-bottom:1px solid var(--border)}
td{padding:10px 14px;border-bottom:1px solid var(--border)}
tr:last-child td{border-bottom:none}
tr:hover td{background:rgba(255,255,255,.02)}
td .actions{display:flex;gap:4px;justify-content:flex-end}

/* ── Badges ── */
.badge{display:inline-flex;align-items:center;gap:4px;padding:3px 9px;border-radius:20px;font-size:.75rem;font-weight:600;border:1px solid transparent;white-space:nowrap}
.badge-pending{background:rgba(251,191,36,.1);color:var(--yellow);border-color:rgba(251,191,36,.3)}
.badge-approved,.badge-completed{background:rgba(52,211,153,.1);color:var(--green);border-color:rgba(52,211,153,.3)}
.badge-rejected,.badge-cancelled{background:rgba(248,113,113,.1);color:var(--red);border-color:rgba(248,113,113,.3)}
.badge-refunded{background:rgba(96,165,250,.1);color:var(--primary-light);border-color:rgba(96,165,250,.3)}
.badge-locked{background:rgba(248,113,113,.1);color:var(--red);border-color:rgba(248,113,113,.3)}
.badge-active{background:rgba(52,211,153,.1);color:var(--green);border-color:rgba(52,211,153,.3)}
.badge-admin{background:rgba(59,130,246,.1);color:var(--primary-light);border-color:rgba(59,130,246,.3)}

/* ── Admin Sidebar layout ── */
.admin-wrap{display:flex;gap:0;min-height:100%}
.admin-sidebar{width:200px;flex-shrink:0;background:var(--bg2);border-right:1px solid var(--border);border-radius:var(--radius) 0 0 var(--radius);padding:12px 8px}
.admin-content{flex:1;min-width:0;padding:0 0 0 20px}
.admin-nav-item{display:flex;align-items:center;gap:8px;padding:9px 12px;border-radius:var(--radius-sm);font-size:.88rem;font-weight:500;color:var(--muted);cursor:pointer;transition:all .15s;border:none;background:none;width:100%;text-align:left}
.admin-nav-item:hover{background:rgba(59,130,246,.08);color:var(--text)}
.admin-nav-item.active{background:rgba(59,130,246,.15);color:var(--primary-light);border-left:2px solid var(--primary)}

/* ── Misc ── */
.page-title{font-size:1.4rem;font-weight:800;margin-bottom:20px}
.section-title{font-size:1.05rem;font-weight:700;margin-bottom:14px}
.divider{border:none;border-top:1px solid var(--border);margin:16px 0}
.empty-state{text-align:center;padding:52px 20px;color:var(--muted)}
.empty-state .icon{font-size:2.5rem;margin-bottom:10px}
.empty-state p{font-size:.9rem}
.hero{text-align:center;padding:56px 20px;background:radial-gradient(ellipse at center,rgba(59,130,246,.08) 0%,transparent 70%);border-radius:var(--radius);margin-bottom:28px;border:1px solid var(--border)}
.hero h1{font-size:2rem;font-weight:800;line-height:1.25;margin-bottom:10px}
.hero h1 span{color:var(--primary)}
.hero p{color:var(--muted);max-width:440px;margin:0 auto 20px}
.hero-btns{display:flex;gap:10px;justify-content:center;flex-wrap:wrap}
.features-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:14px;margin-bottom:28px}
.feature-card{background:var(--card);border:1px solid var(--border);border-radius:var(--radius);padding:16px;display:flex;gap:12px}
.feature-icon{width:36px;height:36px;border-radius:8px;background:rgba(59,130,246,.12);display:flex;align-items:center;justify-content:center;flex-shrink:0;font-size:1rem}
.feature-title{font-weight:600;font-size:.9rem;margin-bottom:3px}
.feature-desc{font-size:.8rem;color:var(--muted);line-height:1.5}
.wallet-hero{background:linear-gradient(135deg,rgba(59,130,246,.2),rgba(59,130,246,.05));border:1px solid rgba(59,130,246,.3);border-radius:var(--radius);padding:28px;text-align:center;margin-bottom:20px}
.wallet-balance{font-size:2.5rem;font-weight:900;color:var(--green);margin:6px 0 16px;font-variant-numeric:tabular-nums}
.tx-list,.order-list{display:flex;flex-direction:column}
.tx-item,.order-item{display:flex;align-items:center;gap:12px;padding:11px 0;border-bottom:1px solid var(--border)}
.tx-item:last-child,.order-item:last-child{border-bottom:none}
.tx-icon,.order-icon{width:36px;height:36px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:1rem;flex-shrink:0}
.tx-info,.order-info{flex:1;min-width:0}
.tx-desc,.order-name{font-weight:600;font-size:.88rem;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.tx-date,.order-date{font-size:.78rem;color:var(--muted)}
.tx-amt{font-weight:800;font-size:.95rem;white-space:nowrap;font-variant-numeric:tabular-nums}
.bank-info-row{display:flex;justify-content:space-between;align-items:center;background:var(--bg3);border-radius:var(--radius-sm);padding:8px 12px;margin-bottom:7px;font-size:.88rem}
.bank-info-row span:first-child{color:var(--muted)}
.bank-info-row span:last-child{font-weight:600;font-variant-numeric:tabular-nums}
.qr-wrap{text-align:center;margin:14px 0}
.qr-wrap img{width:170px;height:170px;border-radius:10px;background:#fff;padding:8px;object-fit:contain}
.profile-avatar{width:64px;height:64px;border-radius:50%;background:rgba(59,130,246,.2);display:flex;align-items:center;justify-content:center;font-size:1.6rem;font-weight:800;color:var(--primary);margin-bottom:10px}
.tabs{display:flex;gap:4px;border-bottom:1px solid var(--border);margin-bottom:16px}
.tab-btn{padding:8px 16px;font-size:.88rem;font-weight:600;color:var(--muted);background:none;border:none;border-bottom:2px solid transparent;cursor:pointer;transition:all .15s}
.tab-btn.active{color:var(--primary);border-bottom-color:var(--primary)}
.tab-btn:hover:not(.active){color:var(--text)}
.tab-badge{background:var(--yellow);color:#000;font-size:.7rem;font-weight:700;padding:1px 6px;border-radius:10px;margin-left:4px}
.spinner{display:inline-block;width:16px;height:16px;border:2px solid rgba(255,255,255,.3);border-top-color:#fff;border-radius:50%;animation:spin .7s linear infinite;vertical-align:middle}
@keyframes spin{to{transform:rotate(360deg)}}
.toast-container{position:fixed;bottom:20px;left:50%;transform:translateX(-50%);z-index:1000;display:flex;flex-direction:column;gap:8px;pointer-events:none;min-width:240px}
.toast{background:var(--bg2);border:1px solid var(--border);border-radius:8px;padding:10px 16px;font-size:.88rem;font-weight:500;box-shadow:0 4px 20px rgba(0,0,0,.4);animation:slideUp .25s ease;pointer-events:all}
.toast.success{border-color:rgba(52,211,153,.5);color:var(--green)}
.toast.error{border-color:rgba(248,113,113,.5);color:var(--red)}
@keyframes slideUp{from{opacity:0;transform:translateY(12px)}to{opacity:1;transform:translateY(0)}}
.filter-row{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:18px;align-items:center}
.filter-row input{flex:1;min-width:180px}
.cat-btn{background:var(--bg3);border:1px solid var(--border);color:var(--muted);padding:6px 12px;border-radius:20px;font-size:.82rem;cursor:pointer;transition:all .15s}
.cat-btn:hover,.cat-btn.active{background:rgba(59,130,246,.15);border-color:var(--primary);color:var(--primary-light)}
.product-detail{display:grid;grid-template-columns:1fr 1fr;gap:28px;align-items:start}
.product-detail-img{aspect-ratio:16/9;border-radius:var(--radius);overflow:hidden;background:var(--bg3)}
.product-detail-img img{width:100%;height:100%;object-fit:cover}
.product-detail-price{font-size:2rem;font-weight:900;color:var(--primary);margin:10px 0 6px;font-variant-numeric:tabular-nums}
.product-detail-desc{color:var(--muted);font-size:.9rem;line-height:1.7;margin-bottom:16px}
.balance-alert{background:rgba(251,191,36,.08);border:1px solid rgba(251,191,36,.3);border-radius:var(--radius-sm);padding:10px 14px;font-size:.85rem;color:var(--yellow);margin-bottom:14px;display:flex;align-items:center;gap:8px}
.info-row{display:flex;align-items:center;gap:10px;padding:6px 0;font-size:.88rem}
.info-row span:first-child{color:var(--muted);width:120px;flex-shrink:0}
.section-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px}
.notification-dot{width:8px;height:8px;border-radius:50%;background:var(--yellow);margin-left:6px;display:inline-block}

/* ── Responsive ── */
@media(max-width:768px){
  .hamburger{display:flex}
  .nav-links,.nav-right{display:none}
  .stats-grid{grid-template-columns:1fr 1fr}
  .products-grid{grid-template-columns:repeat(auto-fill,minmax(150px,1fr))}
  .product-detail{grid-template-columns:1fr}
  .admin-sidebar{display:none}
  .admin-content{padding:0}
  #page{padding:18px 14px}
}
@media(max-width:480px){
  .hero h1{font-size:1.45rem}
  .wallet-balance{font-size:2rem}
  .stats-grid{grid-template-columns:1fr 1fr}
}
</style>
</head>
<body>
<div id="app">

<!-- NAVBAR -->
<nav id="navbar">
  <div class="nav-brand">🛒 <span>MMO Store</span></div>
  <div class="nav-links">
    <button class="nav-btn" onclick="navigate('home')">Trang chủ</button>
    <button class="nav-btn" onclick="navigate('products')">Sản phẩm</button>
  </div>
  <div class="nav-right">
    <span class="nav-balance" id="nav-balance" style="display:none"></span>
    <button class="nav-btn" id="nav-wallet" style="display:none" onclick="navigate('wallet')">💳 Ví</button>
    <button class="nav-btn" id="nav-orders" style="display:none" onclick="navigate('orders')">📦 Đơn hàng</button>
    <button class="nav-btn" id="nav-profile" style="display:none" onclick="navigate('profile')">👤 Hồ sơ</button>
    <button class="nav-btn" id="nav-admin" style="display:none" onclick="navigate('admin')">🛡️ Quản trị</button>
    <button class="btn btn-secondary btn-sm" id="nav-login" onclick="navigate('login')">Đăng nhập</button>
    <button class="btn btn-primary btn-sm" id="nav-register" onclick="navigate('register')">Đăng ký</button>
    <button class="btn btn-ghost btn-sm" id="nav-logout" style="display:none" onclick="handleLogout()">Đăng xuất</button>
  </div>
  <button class="hamburger" id="hamburger" onclick="toggleMobileMenu()">☰</button>
</nav>

<!-- MOBILE MENU -->
<div id="mobile-menu">
  <div class="mobile-bal" id="mobile-balance" style="display:none"></div>
  <div class="mobile-menu-inner">
    <button class="nav-btn" style="text-align:left" onclick="navigate('home');closeMobile()">🏠 Trang chủ</button>
    <button class="nav-btn" style="text-align:left" onclick="navigate('products');closeMobile()">🛍 Sản phẩm</button>
    <button class="nav-btn" id="mb-wallet" style="display:none;text-align:left" onclick="navigate('wallet');closeMobile()">💳 Ví tiền</button>
    <button class="nav-btn" id="mb-orders" style="display:none;text-align:left" onclick="navigate('orders');closeMobile()">📦 Đơn hàng</button>
    <button class="nav-btn" id="mb-profile" style="display:none;text-align:left" onclick="navigate('profile');closeMobile()">👤 Hồ sơ</button>
    <button class="nav-btn" id="mb-admin" style="display:none;text-align:left" onclick="navigate('admin');closeMobile()">🛡️ Quản trị</button>
    <button class="btn btn-secondary btn-full" id="mb-login" onclick="navigate('login');closeMobile()">Đăng nhập</button>
    <button class="btn btn-primary btn-full" id="mb-register" onclick="navigate('register');closeMobile()" style="margin-top:4px">Đăng ký</button>
    <button class="btn btn-ghost btn-full" id="mb-logout" style="display:none;margin-top:4px" onclick="handleLogout();closeMobile()">Đăng xuất</button>
  </div>
</div>

<!-- PAGE CONTENT -->
<div id="page"></div>

<footer>© 2025 MMO Store – Sản phẩm số chất lượng</footer>
</div>

<!-- TOAST CONTAINER -->
<div class="toast-container" id="toasts"></div>

<!-- CONFIRM MODAL -->
<div class="overlay" id="confirm-overlay">
  <div class="modal" style="max-width:360px">
    <div class="modal-title" id="confirm-title">Xác nhận</div>
    <p id="confirm-msg" style="color:var(--muted);margin-bottom:20px;font-size:.9rem"></p>
    <div style="display:flex;gap:8px;justify-content:flex-end">
      <button class="btn btn-secondary" onclick="closeConfirm()">Huỷ</button>
      <button class="btn btn-danger" id="confirm-ok">Đồng ý</button>
    </div>
  </div>
</div>

<script>
// ═══════════════════════════════════════════════
//  SUPABASE INIT
// ═══════════════════════════════════════════════
const { createClient } = supabase;
const sb = createClient(
  'https://jegmljostladbpcykwyt.supabase.co',
  'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImplZ21sam9zdGxhZGJwY3lrd3l0Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODA0OTE3MTgsImV4cCI6MjA5NjA2NzcxOH0.y-SSLyTIIi1WIsO1zwz8u3p-Kk6cTXFVGEDmHM5kn28'
);

// ═══════════════════════════════════════════════
//  STATE
// ═══════════════════════════════════════════════
let currentUser = null;
let currentProfile = null;
let currentPage = 'home';
let currentProductId = null;
let adminTab = 'dashboard';

// ═══════════════════════════════════════════════
//  UTILS
// ═══════════════════════════════════════════════
const fmtVND = n => new Intl.NumberFormat('vi-VN',{style:'currency',c
