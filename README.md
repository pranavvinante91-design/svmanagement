<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>S.V. Vidhyalay — Student Management System</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&family=DM+Sans:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-database-compat.js"></script>
<style>
:root{
  --navy:#0f1729;--navy2:#1a2540;--navy3:#243152;
  --blue:#2563eb;--blue-light:#3b82f6;
  --amber:#f59e0b;--amber-light:#fbbf24;
  --emerald:#10b981;--rose:#f43f5e;--violet:#8b5cf6;--cyan:#06b6d4;
  --white:#fff;--gray-50:#f8fafc;--gray-100:#f1f5f9;--gray-200:#e2e8f0;
  --gray-300:#cbd5e1;--gray-400:#94a3b8;--gray-500:#64748b;
  --gray-600:#475569;--gray-700:#334155;
  --sidebar-w:248px;--topbar-h:64px;--radius:12px;
  --shadow:0 4px 24px rgba(0,0,0,.08);--shadow-lg:0 8px 40px rgba(0,0,0,.14);
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{font-family:'DM Sans',sans-serif;background:#f0f4f8;color:var(--gray-700);min-height:100vh;overflow-x:hidden}

/* LOGIN */
#loginScreen{position:fixed;inset:0;z-index:1000;background:linear-gradient(135deg,#0a0f1e 0%,#1a2a4a 50%,#0d1f3c 100%);display:flex;align-items:center;justify-content:center;animation:fadeIn .4s ease}
#loginScreen.hidden{display:none}
.login-wrap{display:flex;gap:40px;align-items:center;max-width:900px;width:95%}
.login-brand{flex:1;text-align:center;color:white;display:none}
@media(min-width:700px){.login-brand{display:block}}
.login-brand img{width:130px;height:130px;border-radius:24px;object-fit:cover;box-shadow:0 16px 48px rgba(0,0,0,.5);margin-bottom:20px}
.login-brand h1{font-family:'Playfair Display',serif;font-size:32px;margin-bottom:8px}
.login-brand p{color:rgba(255,255,255,.45);font-size:14px;line-height:1.6}
.login-box{background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.12);border-radius:24px;padding:40px 36px;width:min(380px,100%);backdrop-filter:blur(20px);box-shadow:0 20px 80px rgba(0,0,0,.4);animation:slideUp .5s ease}
.login-logo-mobile{text-align:center;margin-bottom:24px;display:block}
@media(min-width:700px){.login-logo-mobile{display:none}}
.login-logo-mobile img{width:64px;height:64px;border-radius:16px;object-fit:cover}
.login-logo-mobile h2{color:white;font-family:'Playfair Display',serif;font-size:22px;margin-top:10px}
.login-tabs{display:flex;gap:4px;background:rgba(0,0,0,.25);border-radius:10px;padding:4px;margin-bottom:24px}
.login-tab{flex:1;padding:8px 4px;border:none;background:transparent;color:rgba(255,255,255,.45);border-radius:7px;cursor:pointer;font-family:'DM Sans',sans-serif;font-size:12px;font-weight:500;transition:all .2s}
.login-tab.active{background:var(--blue);color:white;box-shadow:0 4px 12px rgba(37,99,235,.4)}
.lf{margin-bottom:14px}
.lf label{display:block;color:rgba(255,255,255,.55);font-size:11px;font-weight:600;margin-bottom:5px;text-transform:uppercase;letter-spacing:.5px}
.lf input{width:100%;background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.12);border-radius:9px;padding:11px 14px;color:white;font-family:'DM Sans',sans-serif;font-size:14px;outline:none;transition:border-color .2s}
.lf input:focus{border-color:var(--blue-light)}
.lf input::placeholder{color:rgba(255,255,255,.2)}
.login-btn{width:100%;padding:13px;background:linear-gradient(135deg,var(--blue),#1d4ed8);border:none;border-radius:10px;color:white;font-family:'DM Sans',sans-serif;font-size:15px;font-weight:600;cursor:pointer;transition:all .2s;box-shadow:0 4px 16px rgba(37,99,235,.4);margin-top:6px}
.login-btn:hover{transform:translateY(-1px);box-shadow:0 8px 24px rgba(37,99,235,.5)}
.login-err{background:rgba(244,63,94,.15);border:1px solid rgba(244,63,94,.3);border-radius:8px;padding:9px 14px;color:#fca5a5;font-size:12px;margin-top:10px;display:none;text-align:center}

/* APP */
#appShell{display:none;height:100vh}
#appShell.visible{display:flex}

/* SIDEBAR */
.sidebar{width:var(--sidebar-w);height:100vh;position:fixed;left:0;top:0;background:var(--navy);z-index:100;display:flex;flex-direction:column;box-shadow:4px 0 20px rgba(0,0,0,.15)}
.sidebar-header{padding:18px 16px 14px;border-bottom:1px solid rgba(255,255,255,.06)}
.sidebar-logo{display:flex;align-items:center;gap:10px}
.sidebar-logo img{width:40px;height:40px;border-radius:10px;object-fit:cover;flex-shrink:0}
.sidebar-logo-text .name{color:white;font-family:'Playfair Display',serif;font-size:15px;line-height:1.2}
.sidebar-logo-text .sub{color:rgba(255,255,255,.4);font-size:10px;font-weight:500}
.sidebar-user{margin-top:12px;background:rgba(37,99,235,.12);border:1px solid rgba(37,99,235,.2);border-radius:9px;padding:8px 12px;display:flex;align-items:center;gap:8px}
.user-dot{width:8px;height:8px;border-radius:50%;background:var(--emerald);box-shadow:0 0 6px var(--emerald);flex-shrink:0}
.user-name{color:rgba(255,255,255,.8);font-size:12px;font-weight:500}
.user-id{color:rgba(255,255,255,.35);font-size:10px;font-family:'JetBrains Mono',monospace}
.sidebar-nav{flex:1;padding:10px 8px;overflow-y:auto}
.nav-label{color:rgba(255,255,255,.2);font-size:9px;font-weight:700;text-transform:uppercase;letter-spacing:1px;padding:8px 10px 3px}
.nav-item{display:flex;align-items:center;gap:10px;padding:9px 11px;border-radius:9px;cursor:pointer;color:rgba(255,255,255,.45);font-size:12.5px;font-weight:500;transition:all .18s;margin-bottom:1px}
.nav-item:hover{background:rgba(255,255,255,.05);color:rgba(255,255,255,.75)}
.nav-item.active{background:linear-gradient(135deg,rgba(37,99,235,.28),rgba(37,99,235,.12));color:var(--blue-light);border:1px solid rgba(37,99,235,.22)}
.nav-item .ni{font-size:15px;width:18px;text-align:center;flex-shrink:0}
.sidebar-footer{padding:14px;border-top:1px solid rgba(255,255,255,.06)}
.logout-btn{width:100%;padding:9px;background:rgba(244,63,94,.1);border:1px solid rgba(244,63,94,.2);border-radius:9px;color:#fca5a5;font-family:'DM Sans',sans-serif;font-size:12px;font-weight:500;cursor:pointer;transition:all .2s}
.logout-btn:hover{background:rgba(244,63,94,.2)}

/* MAIN */
.main-area{margin-left:var(--sidebar-w);flex:1;display:flex;flex-direction:column;min-height:100vh}
.topbar{height:var(--topbar-h);background:white;border-bottom:1px solid var(--gray-200);display:flex;align-items:center;justify-content:space-between;padding:0 24px;position:sticky;top:0;z-index:50;box-shadow:0 1px 4px rgba(0,0,0,.04)}
.topbar-left{display:flex;align-items:center;gap:14px}
.topbar-title{font-family:'Playfair Display',serif;font-size:19px;color:var(--navy)}
.topbar-right{display:flex;align-items:center;gap:12px}
.topbar-date{font-size:11px;color:var(--gray-400);font-family:'JetBrains Mono',monospace}
.topbar-save{display:flex;align-items:center;gap:5px;font-size:11px;color:var(--emerald);font-weight:500}
.topbar-avatar{width:34px;height:34px;border-radius:9px;background:linear-gradient(135deg,var(--blue),var(--amber));display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:13px;cursor:pointer}
/* CLOUD SYNC */
.cloud-btn{display:inline-flex;align-items:center;gap:5px;padding:6px 12px;border-radius:8px;border:1.5px solid var(--gray-200);background:white;color:var(--gray-600);font-family:'DM Sans',sans-serif;font-size:11px;font-weight:600;cursor:pointer;transition:all .2s;white-space:nowrap}
.cloud-btn:hover{border-color:var(--blue);color:var(--blue);background:rgba(37,99,235,.04)}
.cloud-btn.connected{border-color:rgba(16,185,129,.4);color:var(--emerald);background:rgba(16,185,129,.06)}
.cloud-btn.syncing{border-color:rgba(37,99,235,.4);color:var(--blue);background:rgba(37,99,235,.06);animation:cloudPulse 1.2s infinite}
.cloud-btn.error{border-color:rgba(244,63,94,.4);color:var(--rose);background:rgba(244,63,94,.06)}
@keyframes cloudPulse{0%,100%{opacity:1}50%{opacity:.5}}
.cloud-dot{width:7px;height:7px;border-radius:50%;background:currentColor;flex-shrink:0}
.cloud-setup-card{background:linear-gradient(135deg,rgba(37,99,235,.08),rgba(139,92,246,.08));border:1.5px solid rgba(37,99,235,.2);border-radius:14px;padding:20px;margin-bottom:18px}
.cloud-step{display:flex;gap:12px;align-items:flex-start;margin-bottom:14px}
.cloud-step-num{width:26px;height:26px;border-radius:50%;background:var(--blue);color:white;font-size:11px;font-weight:700;display:flex;align-items:center;justify-content:center;flex-shrink:0;margin-top:2px}
.cloud-step-text{font-size:12.5px;color:var(--gray-700);line-height:1.6}
.cloud-step-text b{color:var(--navy)}
.cloud-step-text code{background:var(--gray-100);padding:1px 5px;border-radius:4px;font-family:'JetBrains Mono',monospace;font-size:11px}
.content-area{flex:1;padding:24px;overflow-y:auto;position:relative}
/* Logo watermark behind all dashboard content */
.content-area::before{content:'';position:fixed;top:var(--topbar-h);left:var(--sidebar-w);right:0;bottom:0;background-image:var(--logo-watermark-url);background-repeat:no-repeat;background-position:center center;background-size:260px 260px;opacity:.045;pointer-events:none;z-index:0}
.content-area > *{position:relative;z-index:1}
/* Logo in sec-hero */
.sec-hero{background:linear-gradient(135deg,var(--navy) 0%,var(--navy2) 100%);border-radius:16px;padding:22px 26px;color:white;margin-bottom:22px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;position:relative;overflow:hidden}
.sec-hero::before{content:'';position:absolute;right:-10px;top:50%;transform:translateY(-50%);width:110px;height:110px;background-image:var(--logo-watermark-url);background-size:contain;background-repeat:no-repeat;background-position:center;opacity:.13;pointer-events:none}
.sec-hero h2{font-family:'Playfair Display',serif;font-size:21px;margin-bottom:3px}
.sec-hero p{color:rgba(255,255,255,.45);font-size:12px}

/* STAT GRID */
.stat-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:14px;margin-bottom:20px}
.stat-card{background:white;border-radius:var(--radius);padding:18px;box-shadow:var(--shadow);border:1px solid var(--gray-100);display:flex;align-items:center;gap:14px;transition:transform .2s}
.stat-card:hover{transform:translateY(-2px)}
.stat-icon{width:46px;height:46px;border-radius:12px;display:flex;align-items:center;justify-content:center;font-size:20px;flex-shrink:0}
.stat-info h3{font-size:22px;font-weight:700;color:var(--navy)}
.stat-info p{font-size:10px;color:var(--gray-400);font-weight:600;text-transform:uppercase;letter-spacing:.4px}

/* CARD */
.card{background:white;border-radius:var(--radius);padding:22px;box-shadow:var(--shadow);border:1px solid var(--gray-100);margin-bottom:18px}
.card-hdr{display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:8px}
.card-title{font-family:'Playfair Display',serif;font-size:16px;color:var(--navy)}
.card-sub{font-size:11px;color:var(--gray-400);margin-top:1px}

/* BUTTONS */
.btn{display:inline-flex;align-items:center;gap:5px;padding:8px 16px;border-radius:8px;border:none;cursor:pointer;font-family:'DM Sans',sans-serif;font-size:12.5px;font-weight:500;transition:all .18s;text-decoration:none}
.btn-primary{background:var(--blue);color:white}.btn-primary:hover{background:#1d4ed8;transform:translateY(-1px)}
.btn-success{background:var(--emerald);color:white}.btn-success:hover{background:#059669}
.btn-danger{background:var(--rose);color:white}.btn-danger:hover{background:#e11d48}
.btn-warning{background:var(--amber);color:white}.btn-warning:hover{background:#d97706}
.btn-violet{background:var(--violet);color:white}.btn-violet:hover{background:#7c3aed}
.btn-cyan{background:var(--cyan);color:white}.btn-cyan:hover{background:#0891b2}
.btn-outline{background:transparent;border:1px solid var(--gray-200);color:var(--gray-600)}.btn-outline:hover{background:var(--gray-50)}
.btn-sm{padding:6px 12px;font-size:12px;border-radius:7px}
.btn-xs{padding:4px 9px;font-size:11px;border-radius:6px}

/* TABLE - NEW DESIGN */
.tbl-wrap{overflow-x:auto;border-radius:10px;border:1px solid var(--gray-200)}
table{width:100%;border-collapse:collapse}
thead tr{background:linear-gradient(135deg,var(--navy),var(--navy2))}
th{padding:11px 14px;text-align:left;font-size:11px;font-weight:600;color:rgba(255,255,255,.8);text-transform:uppercase;letter-spacing:.5px;white-space:nowrap}
td{padding:11px 14px;border-bottom:1px solid var(--gray-100);font-size:12.5px;color:var(--gray-700);vertical-align:middle}
tbody tr:last-child td{border-bottom:none}
tbody tr:hover td{background:#f8faff}
tbody tr:nth-child(even) td{background:#fafbff}
tbody tr:nth-child(even):hover td{background:#f4f7ff}

/* FORM */
.form-grid{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.fg{margin-bottom:14px}
.fg label{display:block;font-size:11px;font-weight:600;color:var(--gray-600);margin-bottom:5px;text-transform:uppercase;letter-spacing:.4px}
.fc{width:100%;border:1.5px solid var(--gray-200);border-radius:8px;padding:9px 12px;font-family:'DM Sans',sans-serif;font-size:13px;color:var(--gray-700);background:var(--gray-50);transition:all .2s;outline:none}
.fc:focus{border-color:var(--blue);background:white;box-shadow:0 0 0 3px rgba(37,99,235,.08)}
textarea.fc{resize:vertical;min-height:80px}
.form-full{grid-column:1/-1}

/* BADGE */
.badge{display:inline-flex;align-items:center;padding:3px 9px;border-radius:20px;font-size:10.5px;font-weight:600;white-space:nowrap}
.b-blue{background:rgba(37,99,235,.1);color:var(--blue)}
.b-green{background:rgba(16,185,129,.1);color:var(--emerald)}
.b-red{background:rgba(244,63,94,.1);color:var(--rose)}
.b-amber{background:rgba(245,158,11,.12);color:#b45309}
.b-violet{background:rgba(139,92,246,.1);color:var(--violet)}
.b-gray{background:var(--gray-100);color:var(--gray-500)}
.b-cyan{background:rgba(6,182,212,.1);color:var(--cyan)}
.b-navy{background:rgba(15,23,41,.08);color:var(--navy)}

/* SEARCH */
.search-row{display:flex;gap:8px;margin-bottom:14px;flex-wrap:wrap;align-items:center}
.si-wrap{position:relative;flex:1;min-width:180px}
.si-wrap span{position:absolute;left:10px;top:50%;transform:translateY(-50%);font-size:13px;color:var(--gray-400)}
.si{width:100%;padding:8px 12px 8px 32px;border:1.5px solid var(--gray-200);border-radius:8px;font-family:'DM Sans',sans-serif;font-size:12.5px;color:var(--gray-700);background:var(--gray-50);outline:none;transition:border-color .2s}
.si:focus{border-color:var(--blue);background:white}

/* MODAL */
.mo{position:fixed;inset:0;background:rgba(0,0,0,.55);z-index:200;display:flex;align-items:center;justify-content:center;backdrop-filter:blur(4px);animation:fadeIn .2s ease}
.mo.hidden{display:none}
.mo-box{background:white;border-radius:20px;padding:28px;width:min(580px,96vw);max-height:90vh;overflow-y:auto;box-shadow:var(--shadow-lg);animation:slideUp .3s ease}
.mo-hdr{display:flex;align-items:center;justify-content:space-between;margin-bottom:22px}
.mo-title{font-family:'Playfair Display',serif;font-size:19px;color:var(--navy)}
.mo-close{width:30px;height:30px;border-radius:7px;background:var(--gray-100);border:none;cursor:pointer;display:flex;align-items:center;justify-content:center;font-size:15px;color:var(--gray-500);transition:all .2s}
.mo-close:hover{background:var(--gray-200)}

/* PROGRESS */
.pb{height:8px;background:var(--gray-100);border-radius:99px;overflow:hidden}
.pf{height:100%;border-radius:99px;background:linear-gradient(90deg,var(--blue),var(--emerald));transition:width .4s ease}

/* INFO ROW */
.ir{display:flex;justify-content:space-between;align-items:center;padding:9px 0;border-bottom:1px solid var(--gray-100);font-size:12.5px}
.ir:last-child{border-bottom:none}
.ir-label{color:var(--gray-400);font-weight:500}
.ir-val{color:var(--navy);font-weight:600}

/* RECEIPT */
.receipt{background:white;border:2px solid var(--navy);border-radius:16px;padding:24px;max-width:500px}
.receipt-header{text-align:center;border-bottom:2px dashed var(--gray-300);padding-bottom:16px;margin-bottom:16px}
.receipt-header img{width:56px;height:56px;border-radius:12px;object-fit:cover;margin-bottom:8px}
.receipt-body .rr{display:flex;justify-content:space-between;padding:7px 0;border-bottom:1px solid var(--gray-100);font-size:12.5px}
.receipt-body .rr:last-child{border-bottom:none}
.receipt-footer{text-align:center;margin-top:16px;padding-top:14px;border-top:2px dashed var(--gray-300);font-size:11px;color:var(--gray-400)}

/* PROFILE HERO */
.ph{background:linear-gradient(135deg,var(--navy),var(--navy2));border-radius:16px;padding:24px;color:white;margin-bottom:18px;display:flex;gap:20px;align-items:center}
.ph-avatar{width:68px;height:68px;border-radius:16px;background:linear-gradient(135deg,var(--blue),var(--amber));display:flex;align-items:center;justify-content:center;font-size:26px;font-weight:700;flex-shrink:0}
.ph-info h2{font-size:20px;font-weight:700;margin-bottom:3px}
.ph-info p{color:rgba(255,255,255,.45);font-size:12px;margin-bottom:8px}
.ph-tags{display:flex;flex-wrap:wrap;gap:5px}
.ph-tag{background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:5px;padding:2px 9px;font-size:11px;font-weight:500}

/* TWO / THREE COL */
.two-col{display:grid;grid-template-columns:1fr 1fr;gap:18px}
.three-col{display:grid;grid-template-columns:1fr 1fr 1fr;gap:18px}
@media(max-width:900px){.two-col,.three-col,.form-grid{grid-template-columns:1fr}}

/* HW TRACK */
.hw-pipeline{display:flex;align-items:center;gap:0;margin:10px 0}
.hw-step{flex:1;text-align:center;position:relative}
.hw-step-dot{width:30px;height:30px;border-radius:50%;margin:0 auto 5px;display:flex;align-items:center;justify-content:center;font-size:12px;font-weight:700;border:2px solid var(--gray-200);background:white;color:var(--gray-400);transition:all .3s}
.hw-step-dot.done{background:var(--emerald);border-color:var(--emerald);color:white}
.hw-step-dot.active{background:var(--blue);border-color:var(--blue);color:white}
.hw-step-label{font-size:10px;font-weight:600;text-transform:uppercase;letter-spacing:.3px;color:var(--gray-400)}
.hw-step-label.done,.hw-step-label.active{color:var(--navy)}
.hw-line{flex:1;height:2px;background:var(--gray-200);margin-top:-20px;position:relative;z-index:0}
.hw-line.done{background:var(--emerald)}

/* EMPTY STATE */
.empty{text-align:center;padding:40px 20px;color:var(--gray-400)}
.empty .ei{font-size:44px;margin-bottom:10px}
.empty p{font-size:13px}


/* ANNOUNCEMENTS */
.ann-card{border-radius:12px;padding:16px;margin-bottom:12px;border-left:4px solid;position:relative;overflow:hidden;transition:all .2s}
.ann-card:hover{transform:translateX(3px)}
.ann-badge{display:inline-flex;align-items:center;gap:4px;padding:3px 10px;border-radius:20px;font-size:10.5px;font-weight:700;text-transform:uppercase;letter-spacing:.4px}
.ann-type-event{background:rgba(37,99,235,.07);border-color:var(--blue)}
.ann-type-holiday{background:rgba(16,185,129,.07);border-color:var(--emerald)}
.ann-type-exam{background:rgba(139,92,246,.07);border-color:var(--violet)}
.ann-type-fee{background:rgba(245,158,11,.08);border-color:var(--amber)}
.ann-type-general{background:rgba(100,116,139,.06);border-color:var(--gray-400)}
.ann-type-urgent{background:rgba(244,63,94,.07);border-color:var(--rose)}
.ann-pin{position:absolute;top:10px;right:10px;font-size:14px}
.ann-ticker{background:linear-gradient(135deg,var(--navy),var(--navy2));color:white;padding:10px 20px;display:flex;align-items:center;gap:12px;border-radius:10px;margin-bottom:16px;overflow:hidden}
.ann-ticker-label{background:var(--rose);padding:3px 10px;border-radius:5px;font-size:11px;font-weight:700;flex-shrink:0}
.ann-ticker-text{font-size:12.5px;white-space:nowrap;animation:marquee 20s linear infinite}
@keyframes marquee{0%{transform:translateX(0)}100%{transform:translateX(-50%)}}


/* PHOTO AVATAR */
.photo-avatar{width:100%;height:100%;border-radius:inherit;object-fit:cover}
.profile-photo{border-radius:14px;object-fit:cover;border:3px solid rgba(255,255,255,.25);box-shadow:0 8px 24px rgba(0,0,0,.25)}

/* NOTIF */
.notif{position:fixed;top:74px;right:20px;z-index:999;background:var(--navy);color:white;padding:12px 18px;border-radius:11px;font-size:12.5px;box-shadow:var(--shadow-lg);display:flex;align-items:center;gap:8px;animation:slideLeft .3s ease;max-width:280px}

/* UPLOAD ZONE */
.upload-zone{border:2px dashed var(--gray-300);border-radius:10px;padding:20px;text-align:center;cursor:pointer;transition:all .2s;background:var(--gray-50)}
.upload-zone:hover{border-color:var(--blue);background:rgba(37,99,235,.03)}
.upload-zone input{display:none}

/* AUTOSAVE INDICATOR */
.save-dot{width:8px;height:8px;border-radius:50%;background:var(--emerald);display:inline-block;animation:pulse 2s infinite}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}

/* GRADE */
.grade{display:inline-flex;align-items:center;justify-content:center;width:30px;height:30px;border-radius:7px;font-weight:700;font-size:12px}
.gA{background:rgba(16,185,129,.15);color:var(--emerald)}
.gB{background:rgba(37,99,235,.12);color:var(--blue)}
.gC{background:rgba(245,158,11,.12);color:var(--amber)}
.gD{background:rgba(244,63,94,.1);color:var(--rose)}
.gF{background:rgba(100,116,139,.1);color:var(--gray-500)}

/* FEES CARD */
.fee-card{background:white;border-radius:12px;border:1px solid var(--gray-200);padding:16px;margin-bottom:12px}
.fee-card-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:10px}
.fee-receipt-badge{background:linear-gradient(135deg,var(--navy),var(--navy2));color:white;padding:4px 12px;border-radius:6px;font-family:'JetBrains Mono',monospace;font-size:11px;font-weight:600}

/* DOWNLOAD BTN */
.dl-btn{display:inline-flex;align-items:center;gap:5px;padding:7px 14px;background:linear-gradient(135deg,var(--emerald),#059669);color:white;border:none;border-radius:8px;font-family:'DM Sans',sans-serif;font-size:12px;font-weight:500;cursor:pointer;transition:all .2s;text-decoration:none}
.dl-btn:hover{transform:translateY(-1px);box-shadow:0 4px 12px rgba(16,185,129,.3)}

@keyframes fadeIn{from{opacity:0}to{opacity:1}}
@keyframes slideUp{from{opacity:0;transform:translateY(14px)}to{opacity:1;transform:translateY(0)}}
@keyframes slideLeft{from{opacity:0;transform:translateX(16px)}to{opacity:1;transform:translateX(0)}}
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:var(--gray-200);border-radius:99px}

/* ═══════════════════════════════════════
   RESPONSIVE MEDIA QUERIES
═══════════════════════════════════════ */

/* ── Tablet (768px–1023px) ─────────────── */
@media(max-width:1023px){
  :root{--sidebar-w:220px}
  .stat-grid{grid-template-columns:repeat(auto-fill,minmax(160px,1fr))}
  .form-grid{grid-template-columns:1fr}
  .form-full{grid-column:1}
  .topbar-date{display:none}
  .topbar{padding:0 16px}
  .content-area{padding:16px}
}

/* ── Mobile (<768px) ───────────────────── */
@media(max-width:767px){
  :root{--sidebar-w:0px;--topbar-h:56px}

  /* Hide sidebar by default on mobile */
  .sidebar{
    transform:translateX(-100%);
    transition:transform .28s cubic-bezier(.4,0,.2,1);
    width:270px !important;
    z-index:300;
  }
  .sidebar.open{transform:translateX(0)}

  /* Overlay when sidebar open */
  .sidebar-overlay{
    display:none;position:fixed;inset:0;
    background:rgba(0,0,0,.5);z-index:299;
    backdrop-filter:blur(2px);
  }
  .sidebar-overlay.show{display:block}

  /* Main area takes full width */
  .main-area{margin-left:0 !important}

  /* Topbar mobile */
  .topbar{padding:0 12px;gap:8px}
  .topbar-left{gap:8px;flex:1;min-width:0}
  .topbar-title{font-size:15px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;max-width:160px}
  .topbar-date{display:none}
  .topbar-right{gap:6px}
  .cloud-btn{padding:5px 8px;font-size:10px}
  #cloudBtnLabel{display:none}
  .cloud-dot{display:inline-block}
  .topbar-save{display:none}

  /* Hamburger button */
  .hamburger-btn{
    display:flex !important;
    align-items:center;justify-content:center;
    width:36px;height:36px;border-radius:9px;
    background:var(--gray-100);border:none;
    cursor:pointer;font-size:18px;flex-shrink:0;
    color:var(--navy);transition:background .2s;
  }
  .hamburger-btn:hover{background:var(--gray-200)}

  /* Content */
  .content-area{padding:12px}

  /* Cards */
  .card{padding:14px}
  .card-hdr{flex-direction:column;align-items:flex-start;gap:10px}
  .sec-hero{padding:16px 18px;border-radius:12px}
  .sec-hero h2{font-size:17px}

  /* Stats grid — 2 col on mobile */
  .stat-grid{grid-template-columns:1fr 1fr;gap:10px}
  .stat-card{padding:12px;gap:10px}
  .stat-icon{width:36px;height:36px;font-size:16px;border-radius:9px}
  .stat-info h3{font-size:18px}
  .stat-info p{font-size:9px}

  /* Form */
  .form-grid{grid-template-columns:1fr !important}
  .form-full{grid-column:1 !important}
  .fg{margin-bottom:12px}

  /* Tables — horizontal scroll */
  .tbl-wrap{overflow-x:auto;-webkit-overflow-scrolling:touch;border-radius:8px}
  table{min-width:480px}

  /* Buttons */
  .btn{font-size:12px;padding:7px 13px}
  .btn-sm{font-size:11px;padding:6px 10px}
  .btn-xs{font-size:10px;padding:4px 8px}
  .dl-btn{font-size:11px;padding:6px 11px}

  /* Modal */
  .mo-box{
    width:96vw !important;
    max-height:90vh;
    overflow-y:auto;
    margin:0 2vw;
    border-radius:16px;
  }

  /* Notifications */
  .notif{right:10px;top:64px;max-width:calc(100vw - 20px);font-size:12px}

  /* Search row */
  .search-row{flex-direction:column}
  .si-wrap{min-width:unset;width:100%}
}

/* ── Small Mobile (<480px) ─────────────── */
@media(max-width:479px){
  .stat-grid{grid-template-columns:1fr 1fr}
  .topbar-title{max-width:120px;font-size:14px}
  .sec-hero{padding:14px 15px}
  .sec-hero h2{font-size:15px}
  .sec-hero p{font-size:11px}
  .content-area{padding:10px}
  .card{padding:12px;border-radius:10px}
  /* ID cards grid */
  .id-card{width:100% !important;height:auto !important}
}

/* ── Hamburger only shows on mobile ──────── */
.hamburger-btn{display:none}
</style>

</head>
<body>

<!-- LOGIN -->
<div id="loginScreen">
  <div class="login-wrap">
    <div class="login-brand">
      <img id="brandLogo" src="" alt="SV Vidhyalay">
      <h1>S.V. Vidhyalay</h1>
      <p>Empowering education through<br>smart school management</p>
    </div>
    <div class="login-box">
      <div class="login-logo-mobile">
        <img id="brandLogoMobile" src="" alt="SV">
        <h2>S.V. Vidhyalay</h2>
      </div>
      <div class="login-tabs">
        <button class="login-tab active" onclick="setRole('admin')">👨‍💼 Admin</button>
        <button class="login-tab" onclick="setRole('teacher')">👨‍🏫 Teacher</button>
        <button class="login-tab" onclick="setRole('student')">👨‍🎓 Student</button>
      </div>
      <div class="lf"><label>User ID</label><input id="loginId" type="text" placeholder="Enter your ID" onkeydown="if(event.key==='Enter')document.getElementById('loginPass').focus()"/></div>
      <div class="lf"><label>Password</label><input id="loginPass" type="password" placeholder="Enter password" onkeydown="if(event.key==='Enter')doLogin()"/></div>
      <button class="login-btn" onclick="doLogin()">Sign In →</button>
      <div class="login-err" id="loginErr">Invalid credentials. Please try again.</div>
      <div style="margin-top:14px;background:rgba(255,255,255,.05);border-radius:9px;padding:10px 12px;font-size:11px;color:rgba(255,255,255,.35);font-family:'JetBrains Mono',monospace">
        Admin: admin / admin123
      </div>
    </div>
  </div>
</div>

<!-- APP SHELL -->
<div id="appShell">
  <!-- Mobile sidebar overlay -->
  <div class="sidebar-overlay" id="sidebarOverlay" onclick="closeSidebar()"></div>
  <nav class="sidebar" id="sidebar">
    <div class="sidebar-header">
      <div class="sidebar-logo">
        <img id="sidebarLogo" src="" alt="SV">
        <div class="sidebar-logo-text">
          <div class="name">S.V. Vidhyalay</div>
          <div class="sub">School Management</div>
        </div>
        <!-- Mobile close X -->
        <button onclick="closeSidebar()" style="display:none;margin-left:auto;background:none;border:none;color:rgba(255,255,255,.5);font-size:18px;cursor:pointer;padding:4px;line-height:1;flex-shrink:0" id="sidebarCloseBtn">✕</button>
      </div>
      <div class="sidebar-user">
        <div class="user-dot"></div>
        <div>
          <div class="user-name" id="sbRoleName">Admin</div>
          <div class="user-id" id="sbRoleId">admin</div>
        </div>
      </div>
    </div>
    <div class="sidebar-nav" id="sidebarNav"></div>
    <div class="sidebar-footer">
      <button class="logout-btn" onclick="doLogout()">⬅ Sign Out</button>
    </div>
  </nav>
  <div class="main-area">
    <div class="topbar">
      <div class="topbar-left">
        <!-- Hamburger (mobile only) -->
        <button class="hamburger-btn" id="hamburgerBtn" onclick="openSidebar()" title="Menu">☰</button>
        <div class="topbar-title" id="topbarTitle">Dashboard</div>
      </div>
      <div class="topbar-right">
        <div class="topbar-save" id="saveIndicator" style="display:none">
          <span class="save-dot"></span> Auto-saved
        </div>
        <button class="cloud-btn" id="cloudBtn" onclick="openCloudPanel()" title="Cloud Sync">
          <span class="cloud-dot"></span>
          <span id="cloudBtnLabel">☁️ Cloud Sync</span>
        </button>
        <div class="topbar-date" id="topbarDate"></div>
        <div class="topbar-avatar" id="topbarAvatar">A</div>
      </div>
    </div>
    <div class="content-area" id="contentArea"></div>
  </div>
</div>

<!-- MODAL -->
<div class="mo hidden" id="moOverlay">
  <div class="mo-box" id="moBox">
    <div class="mo-hdr">
      <div class="mo-title" id="moTitle">Modal</div>
      <button class="mo-close" onclick="closeMo()">✕</button>
    </div>
    <div id="moBody"></div>
  </div>
</div>

<script>
// ═══════════════════════════════════════════════
//  LOGO
// ═══════════════════════════════════════════════
const LOGO_URI = 'data:image/jpeg;base64,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AAAAQQn18888887BR88+9t8G/wDvPNW639n02az1n1Sl+denrpSkPafPPPOlvPfPoQdfvPPPPOS0KAAAAAAAAAAAAAAAAAAAAAAAAAIAev8AzzzZx/8A8/8APBjny/fvPG7VfOLNHIXPUelzOiC/PvSW7ct+NaKa/g9PNPrZuvfPP3hcAAAAAAAAAAAAAAAAAAAAAAAAAAAAKUf/AMJz76uDn6yy0Wr37L3wzzz+/wCMc8s99N99fctO/D+m57uIWHj/AKB9OXPfvR/v6QWAAAAAAAAAAAAAAAAAAAAAAAAAAAAPgh6jvfPeLWBvfbv7wLClvf3vPPPPf/PfPPPPMvvPPbzvb/rvyHq2f+LSHf8A/wA99uLmUwAAAAAAAAAAAAAAAAAAAAAAAAAAAAFmwd18888nQk8y198/9+888/8AuM9eLNxyEABC+3BMd9P/AL3zzzXQ5P8AjG9898888Ys3esAAAAAAAAAAAAAAAAAAAAAAAAAAAA4Ef++8888mXzBT999+++94f7/+9Ex8117zz7/9t5MtKZDo8898889O5S0888888+/slVAAAAAAAAAAAAAAAAAAAAAAAAAQY/L89888889fdu88898/63mozg5k2HOM+q9tN9HmYi78/wAWQZF8vv8A7zzzzzzzzzzzzz/5leSgAAAAAAAAAAAAAAAAAAAAABQBfzzzzzzzz3377zzxxpAT74hfY0X2zzzzzzzzz777zz615JnXprh7i93zzzzzzzzzzz3z6+ggAAAAAAAAAAAAAAAAAAAAA2Jz/wB888888888/wDP+Pok3vpSbXXPfPvPPv8A/wA88899999++++8tu2ICNX/ANP/ADzzzzzzzzzz/wD6aABAAAAAAAAAAAAAAAAAAAxuQ18888888888++impAeKutc8888888888888888888888888889udyS/4+++88888888889GxgBAAAAAAAAAAAAAAAAAABBFG28988888888l/Pv+sMe88++9/8APPPPPPPPPPPPPPPPPPPPv/ffffJapcVX9ffPPPPPPPPKnDQAQAAAAAAAAAAAAAAAAAAAAF8tn/PPPPPPN2ScKffPPPPPfZv/ALzzzzzzzz0773zzzzzzz/l377zzzz0NkFabX3zzzzz+q6MAAAAAAAAAAAAAAAAAAAAAABC4gX7Tzzzjqn7x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';
document.querySelectorAll('#brandLogo,#brandLogoMobile,#sidebarLogo').forEach(el => el.src = LOGO_URI);
// Apply logo watermark to CSS variable for background use
document.documentElement.style.setProperty('--logo-watermark-url', `url("${LOGO_URI}")`);

// ═══════════════════════════════════════════════
//  DATA STORE — starts empty (admin populates)
// ═══════════════════════════════════════════════
const DEFAULT_STORE = {
  admin: { id:'admin', pass:'admin123' },
  classes: [],
  announcements: [],
  salaries: [],
  whatsappLog: [],
  whatsappGroups: [],
  transactions: [],
  teacherAttendance: {},
  stock: [],
  certificates: [],
  slcIssued: [],
  slcCounter: 1,
  timetable: {},
  examSchedule: [],
  transCategories: { income:['Tuition Fees','Admission Fee','Library Fine','Transport Fee','Donation','Event Income','Exam Fee','Other Income'], expense:['Teacher Salary','Staff Salary','Maintenance','Stationery','Electricity','Water','Books & Supplies','Events','Rent','Equipment','Cleaning','Other Expense'] },
  teachers: [],
  students: [],
  books: [],
  issuedBooks: [],
  fees: [],
  feeStructures: {}, // studentId -> { amount, term, description, installments:[] }
  feeInstallments: {}, // studentId -> [{no,label,amount,dueDate,paidDate,status}]
  grNoCounter: 1,
  attendance: {},
  exams: [],
  examResults: [],
  homework: [],
  hwSubmissions: {}, // key: hwId_studentId -> { status, file, fileName, fileData, comment, teacherNote }
  receiptCounter: 1000,
  nextIds: { teacher: 1, student: 1, book: 1, exam: 1, hw: 1, issue: 1, fee: 1 },
  classes: [],        // { id, name, standard, section, teacher, capacity, year }
  admissions: [],      // Full admission records with photo etc.
  complaints: []       // { id, from, fromName, fromRole, subject, category, description, status, priority, adminNote, createdAt, resolvedAt }
};

let store = loadStore();

function loadStore() {
  try {
    const saved = localStorage.getItem('sv_vidhyalay_sms');
    if (saved) {
      const d = JSON.parse(saved);
      // Migrate: ensure new fields exist
      if (!d.classes) d.classes = [];
      if (!d.announcements) d.announcements = [];
      if (!d.feeStructures) d.feeStructures = {};
      if (!d.feeInstallments) d.feeInstallments = {};
      if (!d.grNoCounter) d.grNoCounter = 1;
      if (!d.complaints) d.complaints = [];
      // Migrate: add grNo to existing students without one
      if (d.students) d.students.forEach(s => { if (!s.grNo) { s.grNo = 'GR-' + String(d.grNoCounter++).padStart(4,'0'); } });
      if (!d.salaries) d.salaries = [];
      if (!d.whatsappLog) d.whatsappLog = [];
      if (!d.whatsappGroups) d.whatsappGroups = [];
      if (!d.transactions) d.transactions = [];
      if (!d.teacherAttendance) d.teacherAttendance = {};
      if (!d.stock) d.stock = [];
      if (!d.certificates) d.certificates = [];
      if (!d.slcIssued) d.slcIssued = [];
      if (!d.slcCounter) d.slcCounter = 1;
      if (!d.timetable) d.timetable = {};
      if (!d.examSchedule) d.examSchedule = [];
      if (!d.transCategories) d.transCategories = { income:['Tuition Fees','Admission Fee','Library Fine','Transport Fee','Donation','Event Income','Exam Fee','Other Income'], expense:['Teacher Salary','Staff Salary','Maintenance','Stationery','Electricity','Water','Books & Supplies','Events','Rent','Equipment','Cleaning','Other Expense'] };
      return d;
    }
  } catch(e) {}
  return JSON.parse(JSON.stringify(DEFAULT_STORE));
}

let saveTimer = null;
function saveStore() {
  try {
    localStorage.setItem('sv_vidhyalay_sms', JSON.stringify(store));
    const ind = document.getElementById('saveIndicator');
    if (ind) { ind.style.display='flex'; clearTimeout(saveTimer); saveTimer = setTimeout(()=>{ ind.style.display='none'; }, 2500); }
  } catch(e) { console.warn('Storage full:', e); }
}

// Auto-save every 10s
setInterval(() => { if (currentUser) saveStore(); }, 10000);

// ═══════════════════════════════════════════════
//  ☁️  CLOUD SYNC — Firebase Realtime Database
// ═══════════════════════════════════════════════
let _fbApp = null, _fbDb = null, _cloudCfg = null, _cloudStatus = 'off'; // off | connecting | connected | error
let _cloudSyncTimer = null, _realtimeUnsub = null;

function loadCloudCfg() {
  try { return JSON.parse(localStorage.getItem('sv_cloud_cfg')||'null'); } catch(e){return null;}
}
function saveCloudCfg(cfg) {
  localStorage.setItem('sv_cloud_cfg',JSON.stringify(cfg));
}

function setCloudStatus(status, label) {
  _cloudStatus = status;
  const btn = document.getElementById('cloudBtn');
  const lbl = document.getElementById('cloudBtnLabel');
  if (!btn) return;
  btn.className = 'cloud-btn ' + (status==='connected'?'connected':status==='syncing'?'syncing':status==='error'?'error':'');
  const icons = {off:'☁️ Cloud Sync', connecting:'⏳ Connecting…', connected:'☁️ Synced', syncing:'🔄 Syncing…', error:'⚠️ Sync Error'};
  lbl.textContent = label || icons[status] || '☁️ Cloud Sync';
}

async function initCloud(cfg) {
  try {
    setCloudStatus('connecting');
    // Tear down old instance
    if (_realtimeUnsub) { _realtimeUnsub(); _realtimeUnsub=null; }
    if (_fbApp) { try{await _fbApp.delete();}catch(e){} _fbApp=null; _fbDb=null; }

    _fbApp = firebase.initializeApp(cfg, 'sv_vidhyalay_'+Date.now());
    _fbDb  = firebase.database(_fbApp);
    _cloudCfg = cfg;

    // Test write
    await _fbDb.ref('_ping').set(Date.now());

    // Pull latest from cloud into local
    const snap = await _fbDb.ref('sms_data').once('value');
    if (snap.exists()) {
      const cloudData = snap.val();
      // Merge: cloud wins for data, but keep local admin pass if cloud has none
      store = {...store, ...cloudData};
      localStorage.setItem('sv_vidhyalay_sms', JSON.stringify(store));
      notify('☁️ Data loaded from cloud!', '☁️');
    } else {
      // First time — push local data to cloud
      await cloudPush();
      notify('☁️ Local data uploaded to cloud!', '☁️');
    }

    // Subscribe to real-time changes
    _realtimeUnsub = _fbDb.ref('sms_data').on('value', snap => {
      if (!snap.exists()) return;
      const remote = snap.val();
      // Only update if remote is newer
      if (remote._lastSaved && remote._lastSaved > (store._lastSaved||0)) {
        store = {...store, ...remote};
        localStorage.setItem('sv_vidhyalay_sms', JSON.stringify(store));
        setCloudStatus('connected','☁️ Live Synced');
        // Re-render current section
        if (currentUser && currentSection) {
          try { loadSection(currentSection); } catch(e){}
        }
      }
    });

    setCloudStatus('connected');
    saveCloudCfg(cfg);
    return true;
  } catch(err) {
    console.error('Cloud init error:', err);
    setCloudStatus('error');
    return false;
  }
}

async function cloudPush() {
  if (!_fbDb) return;
  try {
    setCloudStatus('syncing');
    store._lastSaved = Date.now();
    localStorage.setItem('sv_vidhyalay_sms', JSON.stringify(store));
    await _fbDb.ref('sms_data').set(store);
    setCloudStatus('connected');
  } catch(err) {
    console.error('Cloud push error:', err);
    setCloudStatus('error');
  }
}

async function cloudPull() {
  if (!_fbDb) return;
  try {
    setCloudStatus('syncing');
    const snap = await _fbDb.ref('sms_data').once('value');
    if (snap.exists()) {
      store = {...store, ...snap.val()};
      localStorage.setItem('sv_vidhyalay_sms', JSON.stringify(store));
      notify('☁️ Pulled latest data from cloud!','☁️');
      if (currentUser && currentSection) { try{loadSection(currentSection);}catch(e){} }
    }
    setCloudStatus('connected');
  } catch(err) {
    setCloudStatus('error');
  }
}

function disconnectCloud() {
  if (_realtimeUnsub) { try{_realtimeUnsub();}catch(e){} _realtimeUnsub=null; }
  if (_fbApp) { try{_fbApp.delete();}catch(e){} _fbApp=null; _fbDb=null; }
  _cloudCfg=null; _cloudStatus='off';
  localStorage.removeItem('sv_cloud_cfg');
  setCloudStatus('off');
  notify('Cloud sync disconnected.','🔌');
}

// Hook into saveStore — push to cloud on every save
const _origSaveStore = saveStore;
// ── Cloud-aware save ──
function saveStoreWithCloud() {
  try {
    store._lastSaved = Date.now();
    localStorage.setItem('sv_vidhyalay_sms', JSON.stringify(store));
    const ind = document.getElementById('saveIndicator');
    if (ind) { ind.style.display='flex'; clearTimeout(_cloudSyncTimer); _cloudSyncTimer = setTimeout(()=>{ ind.style.display='none'; }, 2500); }
    // Debounced cloud push
    if (_fbDb) {
      clearTimeout(saveStoreWithCloud._debounce);
      saveStoreWithCloud._debounce = setTimeout(()=>cloudPush(), 1500);
    }
  } catch(e) { console.warn('Storage full:', e); }
}
saveStoreWithCloud._debounce = null;
// Override global saveStore
window.saveStore = saveStoreWithCloud;
// Alias
function saveStore() { saveStoreWithCloud(); }

// Auto-reconnect on load
(function(){
  const cfg = loadCloudCfg();
  if (cfg) setTimeout(()=>initCloud(cfg), 800);
})();

// ── Cloud Settings Panel ──────────────────────────────────────────────────────
function openCloudPanel() {
  const cfg = loadCloudCfg();
  const connected = _cloudStatus === 'connected' || _cloudStatus === 'syncing';
  openMo('☁️ Cloud Storage Settings', `
    ${connected ? `
    <div style="background:rgba(16,185,129,.08);border:1.5px solid rgba(16,185,129,.25);border-radius:10px;padding:14px 16px;margin-bottom:16px;display:flex;align-items:center;gap:10px">
      <span style="font-size:20px">✅</span>
      <div>
        <div style="font-weight:700;color:var(--emerald);font-size:13px">Connected to Firebase</div>
        <div style="font-size:11px;color:var(--gray-500);margin-top:2px">Project: <b>${_cloudCfg?.projectId||'—'}</b> · Real-time sync active</div>
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-bottom:18px;flex-wrap:wrap">
      <button class="btn btn-primary btn-sm" onclick="cloudPull();closeMo()">⬇️ Pull from Cloud</button>
      <button class="btn btn-success btn-sm" onclick="cloudPush();closeMo()">⬆️ Push to Cloud</button>
      <button class="btn btn-danger btn-sm" onclick="disconnectCloud();closeMo()">🔌 Disconnect</button>
    </div>
    <hr style="border:none;border-top:1px solid var(--gray-100);margin-bottom:16px">
    <div style="font-size:12px;color:var(--gray-500);margin-bottom:10px">Update Firebase config:</div>
    ` : `
    <div class="cloud-setup-card">
      <div style="font-weight:700;color:var(--navy);font-size:13px;margin-bottom:12px">🚀 How to set up free cloud storage:</div>
      <div class="cloud-step"><div class="cloud-step-num">1</div><div class="cloud-step-text">Go to <b>console.firebase.google.com</b> → Create a new project (free)</div></div>
      <div class="cloud-step"><div class="cloud-step-num">2</div><div class="cloud-step-text">Click <b>Realtime Database</b> → Create database → Start in <b>test mode</b></div></div>
      <div class="cloud-step"><div class="cloud-step-num">3</div><div class="cloud-step-text">Go to <b>Project Settings</b> → Your apps → Add Web app → Copy the <code>firebaseConfig</code></div></div>
      <div class="cloud-step"><div class="cloud-step-num">4</div><div class="cloud-step-text">Paste each value below and click <b>Connect & Sync</b></div></div>
    </div>
    `}
    <div class="form-grid">
      <div class="fg"><label>API Key *</label><input class="fc" id="fb_apiKey" placeholder="AIzaSy..." value="${cfg?.apiKey||''}"></div>
      <div class="fg"><label>Project ID *</label><input class="fc" id="fb_projectId" placeholder="my-school-app" value="${cfg?.projectId||''}"></div>
      <div class="fg"><label>Database URL *</label><input class="fc" id="fb_dbUrl" placeholder="https://xxx-default-rtdb.firebaseio.com" value="${cfg?.databaseURL||''}"></div>
      <div class="fg"><label>Auth Domain</label><input class="fc" id="fb_authDomain" placeholder="xxx.firebaseapp.com" value="${cfg?.authDomain||''}"></div>
      <div class="fg"><label>Storage Bucket</label><input class="fc" id="fb_storageBucket" placeholder="xxx.appspot.com" value="${cfg?.storageBucket||''}"></div>
      <div class="fg"><label>Messaging Sender ID</label><input class="fc" id="fb_messagingSenderId" placeholder="123456789" value="${cfg?.messagingSenderId||''}"></div>
      <div class="fg form-full"><label>App ID</label><input class="fc" id="fb_appId" placeholder="1:123:web:abc" value="${cfg?.appId||''}"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px;flex-wrap:wrap">
      <button class="btn btn-primary" onclick="connectCloud()">☁️ ${connected?'Update &':''} Connect & Sync</button>
      ${connected?'':''}
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
    <div id="cloudConnMsg" style="margin-top:10px;font-size:12px;display:none"></div>
  `);
}

async function connectCloud() {
  const cfg = {
    apiKey:            document.getElementById('fb_apiKey').value.trim(),
    projectId:         document.getElementById('fb_projectId').value.trim(),
    databaseURL:       document.getElementById('fb_dbUrl').value.trim(),
    authDomain:        document.getElementById('fb_authDomain').value.trim(),
    storageBucket:     document.getElementById('fb_storageBucket').value.trim(),
    messagingSenderId: document.getElementById('fb_messagingSenderId').value.trim(),
    appId:             document.getElementById('fb_appId').value.trim(),
  };
  if (!cfg.apiKey || !cfg.projectId || !cfg.databaseURL) {
    const msg = document.getElementById('cloudConnMsg');
    msg.style.display='block'; msg.style.color='var(--rose)'; msg.textContent='⚠️ API Key, Project ID and Database URL are required.'; return;
  }
  const msg = document.getElementById('cloudConnMsg');
  msg.style.display='block'; msg.style.color='var(--blue)'; msg.textContent='⏳ Connecting to Firebase…';
  const ok = await initCloud(cfg);
  if (ok) {
    msg.style.color='var(--emerald)'; msg.textContent='✅ Connected! Data synced to cloud.';
    setTimeout(()=>closeMo(), 1500);
    notify('☁️ Cloud sync connected!','☁️');
  } else {
    msg.style.color='var(--rose)'; msg.textContent='❌ Connection failed. Check your config and Database URL.';
  }
}

// ═══════════════════════════════════════════════
//  HELPERS
// ═══════════════════════════════════════════════
function genId(type) {
  const prefixes = { teacher:'T', student:'S', book:'B', exam:'E', hw:'H', issue:'I', fee:'F' };
  const n = store.nextIds[type]++;
  saveStore();
  return prefixes[type] + String(n).padStart(3,'0');
}

function genReceipt() {
  const yr = new Date().getFullYear();
  const n = ++store.receiptCounter;
  return `RCP-${yr}-${String(n).padStart(4,'0')}`;
}

function getGrade(marks, max) {
  const p = (marks/max)*100;
  if (p>=90) return 'A+';
  if (p>=80) return 'A';
  if (p>=70) return 'B+';
  if (p>=60) return 'B';
  if (p>=50) return 'C';
  if (p>=40) return 'D';
  return 'F';
}
function gradeClass(g) {
  if (g.startsWith('A')) return 'gA';
  if (g.startsWith('B')) return 'gB';
  if (g==='C') return 'gC';
  if (g==='D') return 'gD';
  return 'gF';
}

function attStats(sid) {
  const r = store.attendance[sid]||{};
  const total=Object.keys(r).length, present=Object.values(r).filter(v=>v==='P').length;
  return {total,present,pct:total?Math.round((present/total)*100):0};
}

function fmtDate(d) {
  if(!d) return '—';
  return new Date(d).toLocaleDateString('en-IN',{day:'numeric',month:'short',year:'numeric'});
}

function area() { return document.getElementById('contentArea'); }

// ── Mobile sidebar toggle ────────────────────────────────────────────────────
function openSidebar() {
  document.getElementById('sidebar').classList.add('open');
  document.getElementById('sidebarOverlay').classList.add('show');
  const closeBtn = document.getElementById('sidebarCloseBtn');
  if (closeBtn) closeBtn.style.display = 'block';
  document.body.style.overflow = 'hidden';
}
function closeSidebar() {
  document.getElementById('sidebar').classList.remove('open');
  document.getElementById('sidebarOverlay').classList.remove('show');
  const closeBtn = document.getElementById('sidebarCloseBtn');
  if (closeBtn) closeBtn.style.display = 'none';
  document.body.style.overflow = '';
}
// Show/hide hamburger based on screen size
function updateHamburger() {
  const btn = document.getElementById('hamburgerBtn');
  if (!btn) return;
  btn.style.display = window.innerWidth <= 767 ? 'flex' : 'none';
  if (window.innerWidth > 767) closeSidebar();
}
window.addEventListener('resize', updateHamburger);
updateHamburger();

function notify(msg, icon='✅') {
  const el = document.createElement('div');
  el.className = 'notif';
  el.innerHTML = `<span>${icon}</span><span>${msg}</span>`;
  document.body.appendChild(el);
  setTimeout(()=>el.remove(), 3000);
}

function openMo(title, body) {
  document.getElementById('moTitle').textContent = title;
  document.getElementById('moBody').innerHTML = body;
  document.getElementById('moOverlay').classList.remove('hidden');
}
function closeMo() { document.getElementById('moOverlay').classList.add('hidden'); }
document.getElementById('moOverlay').addEventListener('click', function(e){ if(e.target===this) closeMo(); });

// ═══════════════════════════════════════════════
//  AUTH
// ═══════════════════════════════════════════════
let currentRole = 'admin';
let currentUser = null;
let currentSection = '';

function setRole(r) {
  currentRole = r;
  document.querySelectorAll('.login-tab').forEach((t,i) => {
    t.classList.toggle('active', ['admin','teacher','student'][i]===r);
  });
  document.getElementById('loginId').value='';
  document.getElementById('loginPass').value='';
  document.getElementById('loginErr').style.display='none';
}

function doLogin() {
  const id = document.getElementById('loginId').value.trim();
  const pass = document.getElementById('loginPass').value.trim();
  let user = null;

  if (currentRole==='admin') {
    if (id===store.admin.id && pass===store.admin.pass)
      user = { id, name:'Administrator', role:'admin' };
  } else if (currentRole==='teacher') {
    const t = store.teachers.find(x=>x.id===id&&x.pass===pass);
    if (t) user = {...t, role:'teacher'};
  } else {
    const s = store.students.find(x=>x.id===id&&x.pass===pass);
    if (s) user = {...s, role:'student'};
  }

  if (!user) { document.getElementById('loginErr').style.display='block'; return; }
  currentUser = user;
  document.getElementById('loginScreen').classList.add('hidden');
  document.getElementById('appShell').classList.add('visible');
  initApp();
}

function doLogout() {
  saveStore();
  currentUser = null;
  document.getElementById('loginScreen').classList.remove('hidden');
  document.getElementById('appShell').classList.remove('visible');
  document.getElementById('loginId').value='';
  document.getElementById('loginPass').value='';
  setRole('admin');
}

// ═══════════════════════════════════════════════
//  NAV DEFINITIONS
// ═══════════════════════════════════════════════
const navDefs = {
  admin: [
    {id:'home',icon:'🏠',label:'Dashboard'},
    {id:'admission',icon:'📋',label:'Admission Form'},
    {id:'classes',icon:'🏫',label:'Classes & Standards'},
    {id:'students',icon:'👨‍🎓',label:'Students'},
    {id:'teachers',icon:'👨‍🏫',label:'Teachers'},
    {id:'library',icon:'📚',label:'Library'},
    {id:'fees',icon:'💳',label:'Fees Management'},
    {id:'reports',icon:'📊',label:'Reports'},
    {id:'credentials',icon:'🔑',label:'Credentials'},
    {id:'salary',icon:'💼',label:'Teacher Salary'},
    {id:'whatsapp',icon:'💬',label:'WhatsApp Alerts'},
    {id:'finance',icon:'📒',label:'Income & Expense'},
    {id:'mysalary',icon:'💼',label:'My Salary'},
    {id:'teacheratt',icon:'🗓️',label:'Teacher Attendance'},
    {id:'stock',icon:'📦',label:'Stock & Stationery'},
    {id:'certificates',icon:'🏆',label:'Certificates'},
    {id:'slc',icon:'📜',label:'Leaving Certificate'},
    {id:'idcard',icon:'🪪',label:'ID Card Generator'},
    {id:'timetable',icon:'📅',label:'Timetable'},
    {id:'examschedule',icon:'📝',label:'Exam Schedule'},
    {id:'examtimetable',icon:'🗓️',label:'Exam Timetable'},
    {id:'announcements',icon:'📢',label:'Announcements'},
    {id:'complaints',icon:'📣',label:'Complaints'},
    {id:'cloudsync',icon:'☁️',label:'Cloud Storage'},
  ],
  teacher: [
    {id:'home',icon:'🏠',label:'Dashboard'},
    {id:'myprofile',icon:'👤',label:'My Profile'},
    {id:'attendance',icon:'📋',label:'Attendance'},
    {id:'exams',icon:'📝',label:'Exams'},
    {id:'homework',icon:'📖',label:'Homework'},
    {id:'classstudents',icon:'👥',label:'My Students'},
    {id:'timetable',icon:'📅',label:'My Timetable'},
    {id:'examschedule',icon:'📝',label:'Exam Schedule'},
    {id:'examtimetable',icon:'🗓️',label:'Exam Timetable'},
    {id:'announcements',icon:'📢',label:'Announcements'},
    {id:'mycomplaints',icon:'📣',label:'My Complaints'},
  ],
  student: [
    {id:'profile',icon:'👤',label:'My Profile'},
    {id:'attendance',icon:'📋',label:'Attendance'},
    {id:'exams',icon:'📝',label:'Exams'},
    {id:'homework',icon:'📖',label:'Homework'},
    {id:'library',icon:'📚',label:'Library'},
    {id:'fees',icon:'💰',label:'My Fees'},
    {id:'hwtrack',icon:'📌',label:'HW Tracker'},
    {id:'timetable',icon:'📅',label:'My Timetable'},
    {id:'examschedule',icon:'📝',label:'Exam Schedule'},
    {id:'examtimetable',icon:'🗓️',label:'Exam Timetable'},
    {id:'announcements',icon:'📢',label:'Announcements'},
    {id:'mycomplaints',icon:'📣',label:'My Complaints'},
  ],
};

function initApp() {
  const r = currentUser.role;
  // Refresh user from store for latest data
  if (r === 'student') { const fresh = store.students.find(x=>x.id===currentUser.id); if(fresh) currentUser = {...currentUser,...fresh,role:'student'}; }
  if (r === 'teacher') { const fresh = store.teachers.find(x=>x.id===currentUser.id); if(fresh) currentUser = {...currentUser,...fresh,role:'teacher'}; }
  document.getElementById('sbRoleName').textContent = r==='admin'?'👨‍💼 Administrator':r==='teacher'?`👨‍🏫 ${currentUser.name?.split(' ')[0]||'Teacher'}`:`👨‍🎓 ${currentUser.name?.split(' ')[0]||'Student'}`;
  document.getElementById('sbRoleId').textContent = currentUser.id;
  // Show photo in avatar if available
  const avatarEl = document.getElementById('topbarAvatar');
  if (currentUser.photo) {
    avatarEl.innerHTML = '<img src="'+currentUser.photo+'" style="width:100%;height:100%;border-radius:9px;object-fit:cover">';
    avatarEl.style.padding = '0';
  } else {
    avatarEl.textContent = (currentUser.name||'A')[0].toUpperCase();
  }
  document.getElementById('topbarDate').textContent = new Date().toLocaleDateString('en-IN',{weekday:'short',day:'numeric',month:'short',year:'numeric'});

  const nav = document.getElementById('sidebarNav');
  nav.innerHTML = '<div class="nav-label">Navigation</div>';
  navDefs[r].forEach(item => {
    const el = document.createElement('div');
    el.className = 'nav-item'; el.dataset.sec = item.id;
    el.innerHTML = `<span class="ni">${item.icon}</span>${item.label}`;
    el.onclick = () => loadSection(item.id);
    nav.appendChild(el);
  });
  loadSection(navDefs[r][0].id);
}

function loadSection(id) {
  currentSection = id;
  // Auto-close sidebar on mobile when nav item tapped
  if (window.innerWidth <= 767) closeSidebar();
  document.querySelectorAll('.nav-item').forEach(el => el.classList.toggle('active', el.dataset.sec===id));
  const nav = navDefs[currentUser.role].find(x=>x.id===id);
  document.getElementById('topbarTitle').textContent = nav ? nav.icon+' '+nav.label : id;
  area().innerHTML = '';

  const role = currentUser.role;
  const map = {
    // admin
    home: role==='admin'?renderAdminHome:role==='teacher'?renderTeacherHome:null,
    admission: renderAdminAdmission,
    classes: renderAdminClasses,
    students: renderAdminStudents,
    teachers: renderAdminTeachers,
    fees: role==='admin'?renderAdminFees:renderStudentFees,
    library: role==='admin'?renderAdminLibrary:renderStudentLibrary,
    reports: renderAdminReports,
    credentials: renderAdminCredentials,
    announcements: role==='admin'?renderAdminAnnouncements:renderViewAnnouncements,
    salary: renderAdminSalary,
    whatsapp: renderAdminWhatsApp,
    finance: renderAdminFinance,
    teacheratt: renderAdminTeacherAtt,
    stock: renderAdminStock,
    certificates: renderAdminCertificates,
    slc: renderAdminSLC,
    idcard: renderAdminIDCard,
    timetable: role==='admin'?renderAdminTimetable:renderViewTimetable,
    examschedule: role==='admin'?renderAdminExamSchedule:renderViewExamSchedule,
    examtimetable: role==='admin'?renderAdminExamTimetable:renderViewExamTimetable,
    cloudsync: renderAdminCloudSync,
    complaints: renderAdminComplaints,
    mycomplaints: renderMyComplaints,
    // teacher / student shared
    attendance: role==='teacher'?renderTeacherAttendance:renderStudentAttendance,
    exams: role==='teacher'?renderTeacherExams:renderStudentExams,
    homework: role==='teacher'?renderTeacherHomework:renderStudentHomework,
    myprofile: renderTeacherProfileFull,
    mysalary: renderTeacherSalaryView,
    classstudents: renderTeacherClassStudents,
    // student
    profile: renderStudentProfileFull,
    hwtrack: renderStudentHWTrack,
  };
  const fn = map[id];
  if (fn) fn();
}

// ═══════════════════════════════════════════════
//  EXCEL EXPORT
// ═══════════════════════════════════════════════
function exportToExcel(data, headers, filename) {
  let csv = headers.join(',') + '\n';
  data.forEach(row => {
    csv += headers.map((h,i) => {
      const val = row[i] !== undefined ? String(row[i]) : '';
      return '"' + val.replace(/"/g, '""') + '"';
    }).join(',') + '\n';
  });
  const blob = new Blob([csv], {type:'text/csv;charset=utf-8;'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = filename + '.csv';
  a.click(); URL.revokeObjectURL(url);
  notify('Downloaded as CSV (Excel compatible)!', '📥');
}

// ═══════════════════════════════════════════════
//  FILE DOWNLOAD HELPER
// ═══════════════════════════════════════════════
function downloadBase64File(data, name, mime) {
  const a = document.createElement('a');
  a.href = `data:${mime};base64,${data}`;
  a.download = name;
  a.click();
}

// ═══════════════════════════════════════════════
//  ADMIN HOME
// ═══════════════════════════════════════════════
function renderAdminHome() {
  const totalS=store.students.length, totalT=store.teachers.length;
  const totalBooks=store.books.reduce((a,b)=>a+b.copies,0);
  const pendFees=store.fees.filter(f=>f.status==='pending').length;
  const collFees=store.fees.filter(f=>f.status==='paid').reduce((a,f)=>a+f.paid,0);
  const issued=store.issuedBooks.filter(b=>b.status==='issued').length;
  const hwPending=Object.values(store.hwSubmissions).filter(s=>s.status==='submitted').length;

  area().innerHTML = `
  <div class="sec-hero">
    <div><h2>Welcome, Administrator! 👋</h2><p>S.V. Vidhyalay — Live Dashboard · ${new Date().toLocaleDateString('en-IN',{weekday:'long',day:'numeric',month:'long',year:'numeric'})}</p></div>
    <button class="dl-btn" onclick="exportAllData()">📥 Export All Data</button>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">👨‍🎓</div><div class="stat-info"><h3>${totalS}</h3><p>Students</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">👨‍🏫</div><div class="stat-info"><h3>${totalT}</h3><p>Teachers</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📚</div><div class="stat-info"><h3>${totalBooks}</h3><p>Library Books</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">💳</div><div class="stat-info"><h3>${pendFees}</h3><p>Pending Fees</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">💰</div><div class="stat-info"><h3>₹${(collFees/1000).toFixed(0)}K</h3><p>Fees Collected</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(6,182,212,.1)">📋</div><div class="stat-info"><h3>${hwPending}</h3><p>HW to Review</p></div></div>
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div><div class="card-title">Recent Students</div></div><button class="btn btn-outline btn-sm" onclick="loadSection('students')">View All</button></div>
      ${totalS===0?`<div class="empty"><div class="ei">👨‍🎓</div><p>No students added yet</p></div>`:`
      <div class="tbl-wrap"><table><thead><tr><th>Name</th><th>ID</th><th>Class</th><th>Attendance</th><th>Fee</th></tr></thead><tbody>
        ${store.students.slice(-5).reverse().map(s=>{const {pct}=attStats(s.id);return`<tr>
          <td><b>${s.name}</b></td>
          <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;background:rgba(37,99,235,.08);padding:2px 7px;border-radius:5px;color:var(--blue)">${s.id}</span></td>
          <td><span class="badge b-blue">${s.class}</span></td>
          <td><div style="display:flex;align-items:center;gap:6px"><div class="pb" style="width:50px"><div class="pf" style="width:${pct}%"></div></div><span style="font-size:11px;font-weight:600;color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}%</span></div></td>
          <td><span class="badge ${s.feeStatus==='paid'?'b-green':'b-red'}">${s.feeStatus||'pending'}</span></td>
        </tr>`}).join('')}
      </tbody></table></div>`}
    </div>
    <div class="card">
      <div class="card-hdr"><div><div class="card-title">Fee Summary</div></div><button class="btn btn-outline btn-sm" onclick="loadSection('fees')">Manage</button></div>
      ${store.fees.length===0?`<div class="empty"><div class="ei">💳</div><p>No fee records yet</p></div>`:`
      ${store.fees.slice(-5).reverse().map(f=>`
        <div class="ir">
          <div><div style="font-weight:600;font-size:12.5px">${f.studentName}</div><div style="font-size:11px;color:var(--gray-400)">${f.receiptNo} · ${fmtDate(f.date)}</div></div>
          <div style="text-align:right"><div style="font-weight:700;color:var(--navy)">₹${f.paid.toLocaleString()}</div><span class="badge ${f.status==='paid'?'b-green':'b-red'}">${f.status}</span></div>
        </div>`).join('')}`}
    </div>
  </div>
  <!-- Admin Announcements Quick View -->
  <div class="card" style="margin-bottom:18px">
    <div class="card-hdr">
      <div><div class="card-title">📢 Announcements</div><div class="card-sub">${(store.announcements||[]).length} total · ${activeAnns().length} active</div></div>
      <div style="display:flex;gap:6px">
        <button class="btn btn-primary btn-sm" onclick="loadSection('announcements')">+ New / Manage</button>
      </div>
    </div>
    ${(store.announcements||[]).length===0?`<div class="empty" style="padding:16px"><div class="ei" style="font-size:28px">📢</div><p>No announcements yet — <span style="color:var(--blue);cursor:pointer;font-weight:500" onclick="loadSection('announcements')">create one</span></p></div>`:annWidgetHTML(3)}
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">Homework Reviews Pending</div></div>
      ${Object.keys(store.hwSubmissions).filter(k=>store.hwSubmissions[k].status==='submitted').length===0
        ?`<div class="empty"><div class="ei">✅</div><p>All caught up!</p></div>`
        :`${Object.keys(store.hwSubmissions).filter(k=>store.hwSubmissions[k].status==='submitted').slice(0,5).map(k=>{
          const [hwId,sid]=k.split('_');
          const hw=store.homework.find(h=>h.id===hwId);
          const s=store.students.find(x=>x.id===sid);
          const sub=store.hwSubmissions[k];
          return`<div class="ir"><div><div style="font-weight:600;font-size:12.5px">${hw?.title||hwId}</div><div style="font-size:11px;color:var(--gray-400)">${s?.name||sid}</div></div><span class="badge b-amber">Awaiting Review</span></div>`;
        }).join('')}`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">Upcoming Exams</div></div>
      ${store.exams.filter(e=>new Date(e.date)>=new Date()).slice(0,4).map(e=>`
        <div class="ir">
          <div><div style="font-weight:600;font-size:12.5px">${e.subject}</div><div style="font-size:11px;color:var(--gray-400)">${e.class} · ${e.type}</div></div>
          <div style="text-align:right"><div style="font-size:12px;font-weight:700;color:var(--blue)">${fmtDate(e.date)}</div></div>
        </div>`).join('')||`<div class="empty"><div class="ei">📝</div><p>No upcoming exams</p></div>`}
    </div>
  </div>`;
}

window.exportAllData = function() {
  const data = store.students.map(s=>{const {pct}=attStats(s.id); return [s.id,s.name,s.class,s.roll,s.email,s.phone,s.feeStatus,pct+'%'];});
  exportToExcel(data,['ID','Name','Class','Roll','Email','Phone','Fee Status','Attendance'],'SV_Students_'+new Date().toISOString().slice(0,10));
};

// ═══════════════════════════════════════════════
//  ADMIN STUDENTS
// ═══════════════════════════════════════════════
function renderAdminStudents() {
  let filter='', filterClass='';
  const render = ()=>{
    let list = store.students.filter(s=>{
      const q=filter.toLowerCase();
      return (!q||s.name.toLowerCase().includes(q)||s.id.toLowerCase().includes(q));
    });
    if(filterClass) list=list.filter(s=>s.class===filterClass);
    const classes=[...new Set(store.students.map(s=>s.class))];

    area().innerHTML=`
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Students Management</div><div style="font-size:12px;color:var(--gray-400)">${store.students.length} enrolled students</div></div>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="dl-btn" onclick="exportStudents()">📥 Export Excel</button>
        <button class="btn btn-primary" onclick="showAddStudent()">+ Add Student</button>
      </div>
    </div>
    <div class="card">
      <div class="search-row">
        <div class="si-wrap"><span>🔍</span><input class="si" placeholder="Search name or ID..." oninput="stuSearch(this.value)" value="${filter}"></div>
        <select class="fc" style="width:130px" onchange="stuFilterClass(this.value)">
          <option value="">All Classes</option>
          ${classes.map(c=>`<option value="${c}" ${c===filterClass?'selected':''}>${c}</option>`).join('')}
        </select>
      </div>
      <div class="tbl-wrap"><table>
        <thead><tr><th>Student</th><th>GR No</th><th>ID</th><th>Class</th><th>Roll</th><th>Phone</th><th>Fee Status</th><th>Attendance</th><th>Actions</th></tr></thead>
        <tbody>
          ${list.length?list.map(s=>{const{pct}=attStats(s.id);return`<tr>
            <td><div style="font-weight:600">${s.name}</div><div style="font-size:11px;color:var(--gray-400)">${s.email||'—'}</div></td>
            <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;background:rgba(139,92,246,.1);padding:2px 8px;border-radius:5px;color:var(--violet)">${s.grNo||'—'}</span></td>
            <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;background:rgba(37,99,235,.08);padding:2px 8px;border-radius:5px;color:var(--blue)">${s.id}</span></td>
            <td><span class="badge b-blue">${s.class}</span></td>
            <td><b>${s.roll||'—'}</b></td>
            <td style="font-size:12px">${s.phone||'—'}</td>
            <td><span class="badge ${s.feeStatus==='paid'?'b-green':'b-red'}">${s.feeStatus||'pending'}</span></td>
            <td><div style="display:flex;align-items:center;gap:6px"><div class="pb" style="width:55px"><div class="pf" style="width:${pct}%"></div></div><span style="font-size:11px;font-weight:700;color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}%</span></div></td>
            <td><div style="display:flex;gap:4px">
              <button class="btn btn-outline btn-xs" onclick="viewStudent('${s.id}')">View</button>
              <button class="btn btn-warning btn-xs" onclick="editStudent('${s.id}')">Edit</button>
              <button class="btn btn-primary btn-xs" onclick="setStudentFee('${s.id}')">Fee</button>
              <button class="btn btn-violet btn-xs" onclick="printAdmissionForm('${s.id}')">🖨️</button>
              <button class="btn btn-danger btn-xs" onclick="deleteStudent('${s.id}')">Del</button>
              <button style="background:#25D366;color:white;border:none;border-radius:6px;padding:4px 8px;font-size:10px;font-weight:600;cursor:pointer" onclick="waSendAbsent('${s.id}','${new Date().toISOString().slice(0,10)}')" title="Send absent alert">💬</button>
            </div></td>
          </tr>`}).join(''):`<tr><td colspan="9"><div class="empty"><div class="ei">🔍</div><p>No students found</p></div></td></tr>`}
        </tbody>
      </table></div>
    </div>`;
  };
  window.stuSearch=(v)=>{filter=v;render();};
  window.stuFilterClass=(v)=>{filterClass=v;render();};
  render();
}

window.exportStudents = function() {
  const data=store.students.map(s=>{const{pct}=attStats(s.id);return[s.id,s.name,s.class,s.roll||'',s.dob||'',s.email||'',s.phone||'',s.parent||'',s.parentPhone||'',s.feeStatus||'pending',pct+'%',s.address||''];});
  exportToExcel(data,['ID','Name','Class','Roll','DOB','Email','Phone','Parent','Parent Phone','Fee Status','Attendance%','Address'],'SV_Students_'+new Date().toISOString().slice(0,10));
};

function showAddStudent() {
  openMo('Add New Student', `
    <div class="form-grid">
      <div class="fg"><label>Full Name *</label><input class="fc" id="ns_name" placeholder="Student full name"></div>
      <div class="fg"><label>Student ID *</label><input class="fc" id="ns_id" placeholder="e.g. S001 (auto if blank)"></div>
      <div class="fg"><label>Password *</label><input class="fc" id="ns_pass" placeholder="Login password"></div>
      <div class="fg"><label>Class *</label><input class="fc" id="ns_class" placeholder="e.g. 10A, 9B"></div>
      <div class="fg"><label>Roll No.</label><input class="fc" id="ns_roll" placeholder="Roll number"></div>
      <div class="fg"><label>Date of Birth</label><input class="fc" id="ns_dob" type="date"></div>
      <div class="fg"><label>Email</label><input class="fc" id="ns_email" placeholder="email@example.com"></div>
      <div class="fg"><label>Phone</label><input class="fc" id="ns_phone" placeholder="Phone number"></div>
      <div class="fg"><label>Parent Name</label><input class="fc" id="ns_parent" placeholder="Parent/Guardian"></div>
      <div class="fg"><label>Parent Phone</label><input class="fc" id="ns_pphone" placeholder="Parent contact"></div>
      <div class="fg form-full"><label>Address</label><input class="fc" id="ns_address" placeholder="Full address"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewStudent()">Save Student</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveNewStudent() {
  const name=document.getElementById('ns_name').value.trim();
  const pass=document.getElementById('ns_pass').value.trim();
  const cls=document.getElementById('ns_class').value.trim();
  if(!name||!pass||!cls){alert('Name, Password & Class are required');return;}
  let id=document.getElementById('ns_id').value.trim()||genId('student');
  if(store.students.find(s=>s.id===id)){alert('ID already exists');return;}
  store.students.push({
    id,pass,name,class:cls,
    roll:document.getElementById('ns_roll').value,
    dob:document.getElementById('ns_dob').value,
    email:document.getElementById('ns_email').value,
    phone:document.getElementById('ns_phone').value,
    parent:document.getElementById('ns_parent').value,
    parentPhone:document.getElementById('ns_pphone').value,
    address:document.getElementById('ns_address').value,
    joined:new Date().toISOString().slice(0,10),
    status:'active',feeStatus:'pending'
  });
  store.attendance[id]={};
  saveStore(); closeMo(); notify('Student added!'); renderAdminStudents();
}

function viewStudent(id) {
  const s=store.students.find(x=>x.id===id); if(!s) return;
  const {total,present,pct}=attStats(id);
  const myFees=store.fees.filter(f=>f.studentId===id);
  const totalPaid=myFees.reduce((a,f)=>a+f.paid,0);
  openMo(`Student — ${s.name}`,`
    <div class="ph" style="margin-bottom:16px">
      <div class="ph-avatar">${s.name[0]}</div>
      <div class="ph-info"><h2>${s.name}</h2><p>${s.email||'No email'}</p>
        <div class="ph-tags"><span class="ph-tag">Class ${s.class}</span><span class="ph-tag">Roll #${s.roll||'—'}</span><span class="ph-tag">${s.feeStatus==='paid'?'✅ Fee Paid':'⚠️ Fee Pending'}</span></div>
      </div>
    </div>
    <div class="ir"><span class="ir-label">Student ID</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace">${s.id}</span></div>
    <div class="ir"><span class="ir-label">Password</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace">${s.pass}</span></div>
    <div class="ir"><span class="ir-label">Date of Birth</span><span class="ir-val">${fmtDate(s.dob)}</span></div>
    <div class="ir"><span class="ir-label">Phone</span><span class="ir-val">${s.phone||'—'}</span></div>
    <div class="ir"><span class="ir-label">Parent</span><span class="ir-val">${s.parent||'—'} (${s.parentPhone||'—'})</span></div>
    <div class="ir"><span class="ir-label">Address</span><span class="ir-val">${s.address||'—'}</span></div>
    <div class="ir"><span class="ir-label">Attendance</span><span class="ir-val" style="color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}% (${present}/${total} days)</span></div>
    <div class="ir"><span class="ir-label">Total Fees Paid</span><span class="ir-val" style="color:var(--emerald)">₹${totalPaid.toLocaleString()}</span></div>
    <div class="ir"><span class="ir-label">Joined</span><span class="ir-val">${fmtDate(s.joined)}</span></div>
  `);
}

function editStudent(id) {
  const s=store.students.find(x=>x.id===id); if(!s) return;
  openMo(`Edit — ${s.name}`,`
    <div class="form-grid">
      <div class="fg"><label>Full Name</label><input class="fc" id="es_name" value="${s.name}"></div>
      <div class="fg"><label>Password</label><input class="fc" id="es_pass" value="${s.pass}"></div>
      <div class="fg"><label>Class</label><input class="fc" id="es_class" value="${s.class}"></div>
      <div class="fg"><label>Roll No.</label><input class="fc" id="es_roll" value="${s.roll||''}"></div>
      <div class="fg"><label>Email</label><input class="fc" id="es_email" value="${s.email||''}"></div>
      <div class="fg"><label>Phone</label><input class="fc" id="es_phone" value="${s.phone||''}"></div>
      <div class="fg"><label>Parent</label><input class="fc" id="es_parent" value="${s.parent||''}"></div>
      <div class="fg"><label>Parent Phone</label><input class="fc" id="es_pphone" value="${s.parentPhone||''}"></div>
      <div class="fg form-full"><label>Address</label><input class="fc" id="es_address" value="${s.address||''}"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveEditStudent('${id}')">Update</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveEditStudent(id) {
  const s=store.students.find(x=>x.id===id); if(!s) return;
  s.name=document.getElementById('es_name').value;
  s.pass=document.getElementById('es_pass').value;
  s.class=document.getElementById('es_class').value;
  s.roll=document.getElementById('es_roll').value;
  s.email=document.getElementById('es_email').value;
  s.phone=document.getElementById('es_phone').value;
  s.parent=document.getElementById('es_parent').value;
  s.parentPhone=document.getElementById('es_pphone').value;
  s.address=document.getElementById('es_address').value;
  saveStore(); closeMo(); notify('Student updated!'); renderAdminStudents();
}

function deleteStudent(id) {
  if(!confirm('Delete this student? All their data will be removed.')) return;
  store.students=store.students.filter(s=>s.id!==id);
  delete store.attendance[id];
  store.fees=store.fees.filter(f=>f.studentId!==id);
  saveStore(); notify('Student deleted.','🗑️'); renderAdminStudents();
}

// ═══════════════════════════════════════════════
//  ADMIN — SET STUDENT FEE
// ═══════════════════════════════════════════════
function setStudentFee(id) {
  const s=store.students.find(x=>x.id===id); if(!s) return;
  const existing=store.feeStructures[id]||{};
  openMo(`Set Fee Structure — ${s.name}`,`
    <div class="fg"><label>Term / Description</label><input class="fc" id="sf_term" value="${existing.term||''}" placeholder="e.g. Q1 2025 Tuition Fee"></div>
    <div class="form-grid">
      <div class="fg"><label>Total Fee Amount (₹)</label><input class="fc" id="sf_amount" type="number" value="${existing.amount||''}" placeholder="15000"></div>
      <div class="fg"><label>Due Date</label><input class="fc" id="sf_due" type="date" value="${existing.dueDate||''}"></div>
    </div>
    <div class="fg"><label>Fee Description / Breakdown</label><textarea class="fc" id="sf_desc" placeholder="Tuition: ₹10000, Transport: ₹3000, Library: ₹2000">${existing.desc||''}</textarea></div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveStudentFeeStructure('${id}')">Set Fee Structure</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveStudentFeeStructure(id) {
  const amount=parseFloat(document.getElementById('sf_amount').value)||0;
  const term=document.getElementById('sf_term').value;
  const due=document.getElementById('sf_due').value;
  const desc=document.getElementById('sf_desc').value;
  store.feeStructures[id]={amount,term,dueDate:due,desc};
  const s=store.students.find(x=>x.id===id);
  if(s) s.feeAmount=amount;
  saveStore(); closeMo(); notify(`Fee structure set for ${s?.name}!`);
}

// ═══════════════════════════════════════════════
//  ADMIN TEACHERS
// ═══════════════════════════════════════════════
function renderAdminTeachers() {
  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Teachers Management</div><div style="font-size:12px;color:var(--gray-400)">${store.teachers.length} teaching staff</div></div>
    <div style="display:flex;gap:8px">
      <button class="dl-btn" onclick="exportTeachers()">📥 Export Excel</button>
      <button class="btn btn-primary" onclick="showAddTeacher()">+ Add Teacher</button>
    </div>
  </div>
  <div class="card">
    ${store.teachers.length===0?`<div class="empty"><div class="ei">👨‍🏫</div><p>No teachers added yet. Click "Add Teacher" to begin.</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Teacher</th><th>ID</th><th>Subject</th><th>Class</th><th>Phone</th><th>Email</th><th>Status</th><th>Actions</th></tr></thead>
      <tbody>
        ${store.teachers.map(t=>`<tr>
          <td><div style="font-weight:600">${t.name}</div><div style="font-size:11px;color:var(--gray-400)">Joined: ${fmtDate(t.joined)}</div></td>
          <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;background:rgba(16,185,129,.08);padding:2px 8px;border-radius:5px;color:var(--emerald)">${t.id}</span></td>
          <td><span class="badge b-violet">${t.subject||'—'}</span></td>
          <td><span class="badge b-blue">${t.class||'—'}</span></td>
          <td style="font-size:12px">${t.phone||'—'}</td>
          <td style="font-size:12px">${t.email||'—'}</td>
          <td><span class="badge b-green">Active</span></td>
          <td><div style="display:flex;gap:4px">
            <button class="btn btn-outline btn-xs" onclick="viewTeacher('${t.id}')">View</button>
            <button class="btn btn-warning btn-xs" onclick="editTeacher('${t.id}')">Edit</button>
            <button class="btn btn-danger btn-xs" onclick="deleteTeacher('${t.id}')">Del</button>
          </div></td>
        </tr>`).join('')}
      </tbody>
    </table></div>`}
  </div>`;
}

window.exportTeachers=function(){
  const data=store.teachers.map(t=>[t.id,t.name,t.subject||'',t.class||'',t.email||'',t.phone||'',fmtDate(t.joined)]);
  exportToExcel(data,['ID','Name','Subject','Class','Email','Phone','Joined'],'SV_Teachers_'+new Date().toISOString().slice(0,10));
};

function showAddTeacher() {
  openMo('Add New Teacher',`
    <div class="form-grid">
      <div class="fg"><label>Full Name *</label><input class="fc" id="nt_name" placeholder="Teacher full name"></div>
      <div class="fg"><label>Teacher ID *</label><input class="fc" id="nt_id" placeholder="e.g. T001 (auto if blank)"></div>
      <div class="fg"><label>Password *</label><input class="fc" id="nt_pass" placeholder="Login password"></div>
      <div class="fg"><label>Subject</label><input class="fc" id="nt_subject" placeholder="Subject taught"></div>
      <div class="fg"><label>Assigned Class</label><input class="fc" id="nt_class" placeholder="e.g. 10A"></div>
      <div class="fg"><label>Email</label><input class="fc" id="nt_email" placeholder="email@school.edu"></div>
      <div class="fg"><label>Phone</label><input class="fc" id="nt_phone" placeholder="Phone number"></div>
      <div class="fg"><label>Joined Date</label><input class="fc" id="nt_joined" type="date"></div>
      <div class="fg"><label>Base Salary (₹)</label><input class="fc" id="nt_salary" type="number" placeholder="Monthly base salary"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewTeacher()">Save Teacher</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveNewTeacher() {
  const name=document.getElementById('nt_name').value.trim();
  const pass=document.getElementById('nt_pass').value.trim();
  if(!name||!pass){alert('Name and Password required');return;}
  let id=document.getElementById('nt_id').value.trim()||genId('teacher');
  if(store.teachers.find(t=>t.id===id)){alert('ID exists');return;}
  const ntSal=document.getElementById('nt_salary');store.teachers.push({id,pass,name,subject:document.getElementById('nt_subject').value,class:document.getElementById('nt_class').value,email:document.getElementById('nt_email').value,phone:document.getElementById('nt_phone').value,joined:document.getElementById('nt_joined').value,status:'active',baseSalary:ntSal&&ntSal.value?parseFloat(ntSal.value)||0:0});
  saveStore(); closeMo(); notify('Teacher added!'); renderAdminTeachers();
}

function viewTeacher(id) {
  const t=store.teachers.find(x=>x.id===id); if(!t) return;
  const myStudents=store.students.filter(s=>s.class===t.class);
  openMo(`Teacher — ${t.name}`,`
    <div class="ph" style="margin-bottom:16px">
      <div class="ph-avatar" style="background:linear-gradient(135deg,var(--emerald),var(--blue))">${t.name[0]}</div>
      <div class="ph-info"><h2>${t.name}</h2><p>${t.email||'—'}</p>
        <div class="ph-tags"><span class="ph-tag">${t.subject||'—'}</span><span class="ph-tag">Class ${t.class||'—'}</span></div>
      </div>
    </div>
    <div class="ir"><span class="ir-label">Teacher ID</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace">${t.id}</span></div>
    <div class="ir"><span class="ir-label">Password</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace">${t.pass}</span></div>
    <div class="ir"><span class="ir-label">Phone</span><span class="ir-val">${t.phone||'—'}</span></div>
    <div class="ir"><span class="ir-label">Joined</span><span class="ir-val">${fmtDate(t.joined)}</span></div>
    <div class="ir"><span class="ir-label">Students in ${t.class||'—'}</span><span class="ir-val">${myStudents.length}</span></div>
    <div style="margin-top:12px;font-size:12px;font-weight:600;color:var(--navy)">Students: </div>
    <div style="display:flex;flex-wrap:wrap;gap:5px;margin-top:6px">
      ${myStudents.map(s=>`<span style="background:var(--gray-100);border-radius:6px;padding:3px 9px;font-size:11px">${s.name}</span>`).join('')||'<span style="color:var(--gray-400);font-size:12px">No students in this class</span>'}
    </div>
  `);
}

function editTeacher(id) {
  const t=store.teachers.find(x=>x.id===id); if(!t) return;
  openMo(`Edit — ${t.name}`,`
    <div class="form-grid">
      <div class="fg"><label>Full Name</label><input class="fc" id="et_name" value="${t.name}"></div>
      <div class="fg"><label>Password</label><input class="fc" id="et_pass" value="${t.pass}"></div>
      <div class="fg"><label>Subject</label><input class="fc" id="et_subject" value="${t.subject||''}"></div>
      <div class="fg"><label>Class</label><input class="fc" id="et_class" value="${t.class||''}"></div>
      <div class="fg"><label>Email</label><input class="fc" id="et_email" value="${t.email||''}"></div>
      <div class="fg"><label>Phone</label><input class="fc" id="et_phone" value="${t.phone||''}"></div>
      <div class="fg"><label>Base Salary (₹)</label><input class="fc" id="et_salary" type="number" value="${t.baseSalary||''}" placeholder="Monthly base salary"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveEditTeacher('${id}')">Update</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function saveEditTeacher(id) {
  const t=store.teachers.find(x=>x.id===id); if(!t) return;
  t.name=document.getElementById('et_name').value;
  t.pass=document.getElementById('et_pass').value;
  t.subject=document.getElementById('et_subject').value;
  t.class=document.getElementById('et_class').value;
  t.email=document.getElementById('et_email').value;
  t.phone=document.getElementById('et_phone').value;
  const sal=document.getElementById('et_salary');if(sal&&sal.value)t.baseSalary=parseFloat(sal.value)||0;
  saveStore(); closeMo(); notify('Teacher updated!'); renderAdminTeachers();
}
function deleteTeacher(id) {
  if(!confirm('Delete this teacher?')) return;
  store.teachers=store.teachers.filter(t=>t.id!==id);
  saveStore(); notify('Teacher deleted.','🗑️'); renderAdminTeachers();
}

// ═══════════════════════════════════════════════
//  ADMIN LIBRARY
// ═══════════════════════════════════════════════
function renderAdminLibrary() {
  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Library Management</div></div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="btn btn-primary" onclick="showAddBook()">+ Add Book</button>
      <button class="btn btn-success" onclick="showIssueBook()">📤 Issue Book</button>
      <button class="dl-btn" onclick="exportLibrary()">📥 Export</button>
    </div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">📚</div><div class="stat-info"><h3>${store.books.length}</h3><p>Book Titles</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>${store.books.reduce((a,b)=>a+b.available,0)}</h3><p>Available</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📖</div><div class="stat-info"><h3>${store.issuedBooks.filter(b=>b.status==='issued').length}</h3><p>Issued</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">⚠️</div><div class="stat-info"><h3>${store.issuedBooks.filter(b=>b.status==='issued'&&new Date(b.dueDate)<new Date()).length}</h3><p>Overdue</p></div></div>
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">Book Catalog</div></div>
      ${store.books.length===0?`<div class="empty"><div class="ei">📚</div><p>No books added yet</p></div>`:`
      <div class="tbl-wrap"><table>
        <thead><tr><th>Title</th><th>Author</th><th>Category</th><th>Avail</th><th>Act</th></tr></thead>
        <tbody>${store.books.map(b=>`<tr>
          <td><b>${b.title}</b><div style="font-size:10px;color:var(--gray-400)">${b.id}</div></td>
          <td style="font-size:12px">${b.author||'—'}</td>
          <td><span class="badge b-blue">${b.category||'—'}</span></td>
          <td><b style="color:${b.available>0?'var(--emerald)':'var(--rose)'}">${b.available}</b><span style="color:var(--gray-400);font-size:10px">/${b.copies}</span></td>
          <td><button class="btn btn-danger btn-xs" onclick="deleteBook('${b.id}')">Del</button></td>
        </tr>`).join('')}</tbody>
      </table></div>`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">Issued Records</div></div>
      ${store.issuedBooks.length===0?`<div class="empty"><div class="ei">📖</div><p>No books issued yet</p></div>`:`
      <div class="tbl-wrap"><table>
        <thead><tr><th>Book</th><th>Student</th><th>Due</th><th>Status</th><th>Act</th></tr></thead>
        <tbody>${store.issuedBooks.map(ib=>{const ov=ib.status==='issued'&&new Date(ib.dueDate)<new Date();return`<tr>
          <td style="font-size:12px;font-weight:500">${ib.bookTitle}</td>
          <td style="font-size:12px">${ib.studentName}</td>
          <td style="font-size:11px;color:${ov?'var(--rose)':'inherit'};font-weight:${ov?700:400}">${ib.dueDate}</td>
          <td><span class="badge ${ib.status==='issued'?(ov?'b-red':'b-amber'):'b-green'}">${ib.status==='issued'?(ov?'Overdue':'Issued'):'Returned'}</span></td>
          <td>${ib.status==='issued'?`<button class="btn btn-success btn-xs" onclick="returnBook('${ib.id}')">Return</button>`:''}</td>
        </tr>`}).join('')}</tbody>
      </table></div>`}
    </div>
  </div>`;
}

window.exportLibrary=function(){
  const data=store.books.map(b=>[b.id,b.title,b.author||'',b.category||'',b.copies,b.available,b.copies-b.available]);
  exportToExcel(data,['ID','Title','Author','Category','Total','Available','Issued'],'SV_Library_'+new Date().toISOString().slice(0,10));
};

function showAddBook(){
  openMo('Add New Book',`
    <div class="form-grid">
      <div class="fg"><label>Book ID</label><input class="fc" id="nb_id" placeholder="Auto if blank"></div>
      <div class="fg"><label>Title *</label><input class="fc" id="nb_title" placeholder="Book title"></div>
      <div class="fg"><label>Author</label><input class="fc" id="nb_author" placeholder="Author"></div>
      <div class="fg"><label>Category</label><select class="fc" id="nb_cat"><option>Textbook</option><option>Reference</option><option>Literature</option><option>Biography</option><option>Science</option><option>Other</option></select></div>
      <div class="fg"><label>Total Copies</label><input class="fc" id="nb_copies" type="number" value="5"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewBook()">Add Book</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function saveNewBook(){
  const title=document.getElementById('nb_title').value.trim();
  if(!title){alert('Title required');return;}
  const id=document.getElementById('nb_id').value.trim()||genId('book');
  const copies=parseInt(document.getElementById('nb_copies').value)||1;
  store.books.push({id,title,author:document.getElementById('nb_author').value,category:document.getElementById('nb_cat').value,copies,available:copies});
  saveStore(); closeMo(); notify('Book added!'); renderAdminLibrary();
}
function deleteBook(id){if(!confirm('Delete this book?'))return;store.books=store.books.filter(b=>b.id!==id);saveStore();notify('Book deleted.','🗑️');renderAdminLibrary();}
function showIssueBook(){
  if(!store.books.filter(b=>b.available>0).length){alert('No books available to issue');return;}
  if(!store.students.length){alert('No students added yet');return;}
  openMo('Issue Book',`
    <div class="fg"><label>Select Book</label><select class="fc" id="ib_book">${store.books.filter(b=>b.available>0).map(b=>`<option value="${b.id}">${b.title} (${b.available} available)</option>`).join('')}</select></div>
    <div class="fg"><label>Select Student</label><select class="fc" id="ib_student">${store.students.map(s=>`<option value="${s.id}">${s.name} (${s.id})</option>`).join('')}</select></div>
    <div class="fg"><label>Due Date</label><input class="fc" id="ib_due" type="date"></div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-success" onclick="issueBook()">Issue</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function issueBook(){
  const bookId=document.getElementById('ib_book').value;
  const studentId=document.getElementById('ib_student').value;
  const due=document.getElementById('ib_due').value;
  if(!due){alert('Set due date');return;}
  const book=store.books.find(b=>b.id===bookId);
  const student=store.students.find(s=>s.id===studentId);
  const id=genId('issue');
  store.issuedBooks.push({id,bookId,bookTitle:book.title,studentId,studentName:student.name,issueDate:new Date().toISOString().slice(0,10),dueDate:due,returnDate:null,status:'issued'});
  book.available--;
  saveStore(); closeMo(); notify('Book issued!'); renderAdminLibrary();
}
function returnBook(id){
  const rec=store.issuedBooks.find(b=>b.id===id); if(!rec) return;
  rec.status='returned'; rec.returnDate=new Date().toISOString().slice(0,10);
  const book=store.books.find(b=>b.id===rec.bookId);
  if(book) book.available++;
  saveStore(); notify('Book returned!'); renderAdminLibrary();
}

// ═══════════════════════════════════════════════
//  ADMIN FEES MANAGEMENT
// ═══════════════════════════════════════════════
const FEE_METHODS = ['Cash','Online Transfer','UPI','Cheque','NEFT','RTGS','Demand Draft','Credit Card','Debit Card','Net Banking'];

function renderAdminFees() {
  const total=store.fees.reduce((a,f)=>a+f.amount,0);
  const coll=store.fees.reduce((a,f)=>a+f.paid,0);
  const pend=total-coll;
  const classes=[...new Set(store.students.map(s=>s.class))];

  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Fee Management</div></div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="dl-btn" onclick="exportFees()">📥 Export Excel</button>
      <button class="btn btn-primary" onclick="showRecordFee()">+ Record Payment</button>
      <button class="btn btn-violet" onclick="showSetBulkFee()">⚙️ Set Bulk Fee</button>
    </div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">💰</div><div class="stat-info"><h3>₹${total.toLocaleString()}</h3><p>Total Dues</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>₹${coll.toLocaleString()}</h3><p>Collected</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">⏳</div><div class="stat-info"><h3>₹${pend.toLocaleString()}</h3><p>Pending</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📊</div><div class="stat-info"><h3>${total?Math.round((coll/total)*100):0}%</h3><p>Collection Rate</p></div></div>
  </div>

  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr"><div class="card-title">Fee Structures Set by Admin</div><button class="btn btn-primary btn-sm" onclick="showSetBulkFee()">+ Set Fee</button></div>
    ${Object.keys(store.feeStructures).length===0?`<div class="empty"><div class="ei">⚙️</div><p>No fee structures set. Use "Set Bulk Fee" or click "Fee" on each student.</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Student</th><th>GR No</th><th>Term</th><th>Amount</th><th>Due Date</th><th>Installments</th><th>Description</th><th>Action</th></tr></thead>
      <tbody>${Object.entries(store.feeStructures).map(([sid,fs])=>{
        const s=store.students.find(x=>x.id===sid);
        const inst=store.feeInstallments[sid]||[];
        const paidInst=inst.filter(i=>i.status==='paid').length;
        return`<tr>
          <td><b>${s?.name||sid}</b><div style="font-size:11px;color:var(--gray-400)">${sid}</div></td>
          <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;background:rgba(139,92,246,.1);padding:2px 8px;border-radius:5px;color:var(--violet)">${s?.grNo||'—'}</span></td>
          <td>${fs.term||'—'}</td>
          <td style="font-weight:700;color:var(--navy)">₹${(fs.amount||0).toLocaleString()}</td>
          <td>${fmtDate(fs.dueDate)}</td>
          <td>${inst.length?`<span class="badge ${paidInst===inst.length?'b-green':'b-blue'}">${paidInst}/${inst.length} paid</span><button class="btn btn-outline btn-xs" style="margin-left:4px" onclick="viewInstallments('${sid}')">View</button>`:`<button class="btn btn-outline btn-xs" onclick="setupInstallments('${sid}')">+ Setup</button>`}</td>
          <td style="font-size:11px;color:var(--gray-500);max-width:130px">${fs.desc||'—'}</td>
          <td><div style="display:flex;gap:4px">
            <button class="btn btn-primary btn-xs" onclick="recordFeeForStudent('${sid}')">Pay</button>
            ${inst.length?`<button class="btn btn-violet btn-xs" onclick="viewInstallments('${sid}')">📅</button>`:''}
          </div></td>
        </tr>`;
      }).join('')}</tbody>
    </table></div>`}
  </div>

  <div class="card">
    <div class="card-hdr"><div class="card-title">Payment Records</div></div>
    ${store.fees.length===0?`<div class="empty"><div class="ei">💳</div><p>No payments recorded yet</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Receipt No.</th><th>Student</th><th>GR No</th><th>Term</th><th>Amount</th><th>Paid</th><th>Balance</th><th>Method</th><th>Date</th><th>Status</th><th>Actions</th></tr></thead>
      <tbody>${store.fees.map(f=>{
        const stud=store.students.find(x=>x.id===f.studentId);
        return`<tr>
        <td><span class="fee-receipt-badge">${f.receiptNo||'—'}</span></td>
        <td><b>${f.studentName}</b><div style="font-size:10px;color:var(--gray-400)">${f.studentId}</div></td>
        <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--violet)">${stud?.grNo||'—'}</span></td>
        <td style="font-size:12px">${f.term||'—'}</td>
        <td>₹${f.amount.toLocaleString()}</td>
        <td style="color:var(--emerald);font-weight:700">₹${f.paid.toLocaleString()}</td>
        <td style="color:${f.amount-f.paid>0?'var(--rose)':'var(--gray-400)'};font-weight:700">₹${(f.amount-f.paid).toLocaleString()}</td>
        <td><span class="badge b-cyan">${f.method||'—'}</span></td>
        <td style="font-size:11px">${fmtDate(f.date)}</td>
        <td><span class="badge ${f.status==='paid'?'b-green':'b-red'}">${f.status}</span></td>
        <td><div style="display:flex;gap:4px">
          <button class="btn btn-outline btn-xs" onclick="viewReceipt('${f.id}')">Receipt</button>
          ${f.status!=='paid'?`<button class="btn btn-success btn-xs" onclick="markFeePaid('${f.id}')">Paid</button>`:''}
        </div></td>
      </tr>`;}).join('')}</tbody>
    </table></div>`}
  </div>`;
}

window.exportFees=function(){
  const data=store.fees.map(f=>[f.receiptNo||'',f.studentName,f.studentId,f.term||'',f.amount,f.paid,f.amount-f.paid,f.method||'',fmtDate(f.date),f.status]);
  exportToExcel(data,['Receipt No','Student Name','Student ID','Term','Total Amount','Paid','Balance','Method','Date','Status'],'SV_Fees_'+new Date().toISOString().slice(0,10));
};

function showRecordFee() {
  if(!store.students.length){alert('No students found');return;}
  openMo('Record Fee Payment',`
    <div class="fg"><label>Select Student</label><select class="fc" id="rf_student">${store.students.map(s=>`<option value="${s.id}">${s.name} (${s.id})</option>`).join('')}</select></div>
    <div class="form-grid">
      <div class="fg"><label>Term / Description</label><input class="fc" id="rf_term" placeholder="e.g. Q1 2025 Fees"></div>
      <div class="fg"><label>Total Fee Amount (₹)</label><input class="fc" id="rf_amount" type="number" placeholder="15000" oninput="autofillFeeAmount()"></div>
      <div class="fg"><label>Amount Paid (₹)</label><input class="fc" id="rf_paid" type="number" placeholder="15000"></div>
      <div class="fg"><label>Payment Method</label><select class="fc" id="rf_method">${FEE_METHODS.map(m=>`<option>${m}</option>`).join('')}</select></div>
      <div class="fg"><label>Payment Date</label><input class="fc" id="rf_date" type="date" value="${new Date().toISOString().slice(0,10)}"></div>
      <div class="fg"><label>Reference / Cheque No.</label><input class="fc" id="rf_ref" placeholder="Ref number (optional)"></div>
    </div>
    <div style="background:rgba(37,99,235,.06);border-radius:8px;padding:10px 14px;margin-bottom:14px;font-size:12px;color:var(--gray-600)">
      🎫 Receipt number will be auto-generated: <b style="font-family:'JetBrains Mono',monospace;color:var(--navy)">RCP-${new Date().getFullYear()}-${String(store.receiptCounter+1).padStart(4,'0')}</b>
    </div>
    <div style="display:flex;gap:8px">
      <button class="btn btn-success" onclick="recordFee()">Record & Generate Receipt</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

window.autofillFeeAmount=function(){
  const sid=document.getElementById('rf_student')?.value;
  if(sid){const fs=store.feeStructures[sid];if(fs){document.getElementById('rf_amount').value=fs.amount;document.getElementById('rf_term').value=fs.term||'';}}
};

function recordFeeForStudent(sid) {
  const s=store.students.find(x=>x.id===sid); if(!s) return;
  const fs=store.feeStructures[sid]||{};
  openMo(`Record Payment — ${s.name}`,`
    <div class="form-grid">
      <div class="fg"><label>Term</label><input class="fc" id="rf_term" value="${fs.term||''}"></div>
      <div class="fg"><label>Total Amount</label><input class="fc" id="rf_amount" type="number" value="${fs.amount||0}"></div>
      <div class="fg"><label>Amount Paid (₹)</label><input class="fc" id="rf_paid" type="number" placeholder="${fs.amount||0}"></div>
      <div class="fg"><label>Payment Method</label><select class="fc" id="rf_method">${FEE_METHODS.map(m=>`<option>${m}</option>`).join('')}</select></div>
      <div class="fg"><label>Payment Date</label><input class="fc" id="rf_date" type="date" value="${new Date().toISOString().slice(0,10)}"></div>
      <div class="fg"><label>Reference No.</label><input class="fc" id="rf_ref" placeholder="Optional"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-success" onclick="recordFeeForStudentSave('${sid}')">Record Payment</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function recordFeeForStudentSave(sid) {
  const s=store.students.find(x=>x.id===sid); if(!s) return;
  const amount=parseFloat(document.getElementById('rf_amount').value)||0;
  const paid=parseFloat(document.getElementById('rf_paid').value)||0;
  const term=document.getElementById('rf_term').value;
  const method=document.getElementById('rf_method').value;
  const date=document.getElementById('rf_date').value;
  const ref=document.getElementById('rf_ref').value;
  const receiptNo=genReceipt();
  const id=genId('fee');
  store.fees.push({id,studentId:sid,studentName:s.name,term,amount,paid,method,date,ref,receiptNo,status:paid>=amount?'paid':'pending',notes:''});
  if(paid>=amount) s.feeStatus='paid';
  saveStore(); closeMo(); notify(`Payment recorded! Receipt: ${receiptNo}`,'🧾');
  viewReceipt(id);
  renderAdminFees();
}

function recordFee() {
  const sid=document.getElementById('rf_student').value;
  const s=store.students.find(x=>x.id===sid); if(!s) return;
  const amount=parseFloat(document.getElementById('rf_amount').value)||0;
  const paid=parseFloat(document.getElementById('rf_paid').value)||0;
  const term=document.getElementById('rf_term').value;
  const method=document.getElementById('rf_method').value;
  const date=document.getElementById('rf_date').value;
  const ref=document.getElementById('rf_ref').value;
  const receiptNo=genReceipt();
  const id=genId('fee');
  store.fees.push({id,studentId:sid,studentName:s.name,term,amount,paid,method,date,ref,receiptNo,status:paid>=amount?'paid':'pending'});
  if(paid>=amount) s.feeStatus='paid';
  saveStore(); closeMo(); notify(`Payment recorded! Receipt: ${receiptNo}`,'🧾');
  viewReceipt(id);
  renderAdminFees();
}

function markFeePaid(id) {
  const f=store.fees.find(x=>x.id===id); if(!f) return;
  f.paid=f.amount; f.status='paid'; f.date=f.date||new Date().toISOString().slice(0,10);
  const s=store.students.find(x=>x.id===f.studentId);
  if(s) s.feeStatus='paid';
  saveStore(); notify('Marked as paid!'); renderAdminFees();
}

function viewReceipt(fid) {
  const f=store.fees.find(x=>x.id===fid); if(!f) return;
  const s=store.students.find(x=>x.id===f.studentId)||{};
  openMo('Fee Receipt',`
    <div class="receipt">
      <div class="receipt-header">
        <img src="${LOGO_URI}" alt="Logo">
        <div style="font-family:'Playfair Display',serif;font-size:20px;color:var(--navy);margin-bottom:3px">S.V. Vidhyalay</div>
        <div style="font-size:12px;color:var(--gray-500)">Fee Payment Receipt</div>
        <div style="margin-top:10px;display:inline-block;background:linear-gradient(135deg,var(--navy),var(--navy2));color:white;padding:5px 16px;border-radius:6px;font-family:'JetBrains Mono',monospace;font-size:13px;font-weight:700">${f.receiptNo}</div>
      </div>
      <div class="receipt-body">
        <div class="rr"><span style="color:var(--gray-500)">Student Name</span><b>${f.studentName}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">GR Number</span><span style="font-family:'JetBrains Mono',monospace;color:var(--violet);font-weight:700">${s.grNo||'—'}</span></div>
        <div class="rr"><span style="color:var(--gray-500)">Student ID</span><span style="font-family:'JetBrains Mono',monospace">${f.studentId}</span></div>
        <div class="rr"><span style="color:var(--gray-500)">Class</span>${s.class||'—'}</div>
        <div class="rr"><span style="color:var(--gray-500)">Term</span>${f.term||'—'}</div>
        <div class="rr"><span style="color:var(--gray-500)">Total Fee</span><b>₹${f.amount.toLocaleString()}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Amount Paid</span><b style="color:var(--emerald)">₹${f.paid.toLocaleString()}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Balance</span><b style="color:${f.amount-f.paid>0?'var(--rose)':'var(--emerald)'}">₹${(f.amount-f.paid).toLocaleString()}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Payment Method</span><span class="badge b-cyan">${f.method||'—'}</span></div>
        <div class="rr"><span style="color:var(--gray-500)">Payment Date</span>${fmtDate(f.date)}</div>
        ${f.ref?`<div class="rr"><span style="color:var(--gray-500)">Reference No.</span>${f.ref}</div>`:''}
        <div class="rr"><span style="color:var(--gray-500)">Status</span><span class="badge ${f.status==='paid'?'b-green':'b-red'}">${f.status.toUpperCase()}</span></div>
      </div>
      <div class="receipt-footer">
        <div>Generated: ${new Date().toLocaleString('en-IN')}</div>
        <div style="margin-top:4px">S.V. Vidhyalay — Official Receipt · Thank you!</div>
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-top:16px">
      <button class="btn btn-primary" onclick="printReceipt()">🖨️ Print Receipt</button>
      <button class="btn btn-outline" onclick="closeMo()">Close</button>
    </div>
  `);
}

window.printReceipt=function(){window.print();};

function showSetBulkFee() {
  if(!store.students.length){alert('No students added yet');return;}
  const classes=[...new Set(store.students.map(s=>s.class))];
  openMo('Set Fee for All / Class',`
    <div class="fg"><label>Apply to</label>
      <select class="fc" id="bf_target">
        <option value="all">All Students</option>
        ${classes.map(c=>`<option value="${c}">Class ${c}</option>`).join('')}
      </select>
    </div>
    <div class="form-grid">
      <div class="fg"><label>Term</label><input class="fc" id="bf_term" placeholder="e.g. Q1 2025"></div>
      <div class="fg"><label>Fee Amount (₹)</label><input class="fc" id="bf_amount" type="number" placeholder="15000"></div>
      <div class="fg"><label>Due Date</label><input class="fc" id="bf_due" type="date"></div>
    </div>
    <div class="fg"><label>Fee Breakdown</label><textarea class="fc" id="bf_desc" placeholder="Tuition: ₹10000, Others: ₹5000"></textarea></div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveBulkFee()">Apply Fee Structure</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function saveBulkFee(){
  const target=document.getElementById('bf_target').value;
  const term=document.getElementById('bf_term').value;
  const amount=parseFloat(document.getElementById('bf_amount').value)||0;
  const due=document.getElementById('bf_due').value;
  const desc=document.getElementById('bf_desc').value;
  const students=target==='all'?store.students:store.students.filter(s=>s.class===target);
  students.forEach(s=>{store.feeStructures[s.id]={amount,term,dueDate:due,desc};s.feeAmount=amount;});
  saveStore(); closeMo(); notify(`Fee structure set for ${students.length} students!`);
  renderAdminFees();
}

// ═══════════════════════════════════════════════
//  ADMIN REPORTS
// ═══════════════════════════════════════════════
function renderAdminReports() {
  area().innerHTML=`
  <div style="margin-bottom:18px"><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Analytics & Reports</div></div>
  <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:16px;margin-bottom:24px">
    ${[
      {icon:'📊',color:'rgba(37,99,235,.1)',title:'Attendance Report',desc:'View attendance percentages for all students',fn:'showAttReport'},
      {icon:'💰',color:'rgba(16,185,129,.1)',title:'Fee Collection Report',desc:'Track fee collection, pending amounts & receipts',fn:'showFeeReport'},
      {icon:'📝',color:'rgba(139,92,246,.1)',title:'Exam Performance',desc:'Grade distribution and subject-wise analysis',fn:'showExamReport'},
      {icon:'📚',color:'rgba(245,158,11,.1)',title:'Library Report',desc:'Book utilization and overdue records',fn:'showLibReport'},
    ].map(r=>`
      <div style="background:white;border-radius:12px;padding:22px;border:1px solid var(--gray-200);box-shadow:var(--shadow);cursor:pointer;transition:all .2s" onclick="${r.fn}()" onmouseover="this.style.transform='translateY(-3px)'" onmouseout="this.style.transform=''">
        <div style="width:46px;height:46px;border-radius:12px;background:${r.color};display:flex;align-items:center;justify-content:center;font-size:20px;margin-bottom:12px">${r.icon}</div>
        <div style="font-weight:700;font-size:14px;color:var(--navy);margin-bottom:5px">${r.title}</div>
        <div style="font-size:12px;color:var(--gray-400)">${r.desc}</div>
      </div>`).join('')}
  </div>
  <div id="reportOut"></div>`;
}

window.showAttReport=function(){
  const data=store.students.map(s=>{const{total,present,pct}=attStats(s.id);return[s.id,s.name,s.class,present,total,pct+'%',pct>=75?'Good':'Low'];});
  let html=`<div class="card"><div class="card-hdr"><div class="card-title">📊 Attendance Report</div><button class="dl-btn" onclick="exportToExcel(${JSON.stringify(store.students.map(s=>{const{total,present,pct}=attStats(s.id);return[s.id,s.name,s.class,present,total,pct+'%',pct>=75?'Good':'Low'];}))},['ID','Name','Class','Present','Total','Percentage','Status'],'SV_Attendance')">📥 Export</button></div>
  <div class="tbl-wrap"><table><thead><tr><th>Student</th><th>ID</th><th>Class</th><th>Present</th><th>Total</th><th>Percentage</th><th>Status</th></tr></thead><tbody>
  ${store.students.map(s=>{const{total,present,pct}=attStats(s.id);return`<tr><td><b>${s.name}</b></td><td style="font-family:'JetBrains Mono',monospace;font-size:11px">${s.id}</td><td>${s.class}</td><td>${present}</td><td>${total}</td><td><div style="display:flex;align-items:center;gap:7px"><div class="pb" style="width:80px"><div class="pf" style="width:${pct}%;background:${pct>=75?'linear-gradient(90deg,var(--blue),var(--emerald))':'linear-gradient(90deg,var(--amber),var(--rose))'}"></div></div><b style="color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}%</b></div></td><td><span class="badge ${pct>=75?'b-green':'b-red'}">${pct>=75?'Good':'Low'}</span></td></tr>`;}).join('')}
  </tbody></table></div></div>`;
  document.getElementById('reportOut').innerHTML=html;
};
window.showFeeReport=function(){
  const total=store.fees.reduce((a,f)=>a+f.amount,0);
  const coll=store.fees.reduce((a,f)=>a+f.paid,0);
  document.getElementById('reportOut').innerHTML=`<div class="card"><div class="card-hdr"><div class="card-title">💰 Fee Collection Report</div><button class="dl-btn" onclick="exportFees()">📥 Export</button></div>
  <div class="two-col" style="margin-bottom:16px">
    <div style="background:rgba(16,185,129,.08);border:1px solid rgba(16,185,129,.2);border-radius:10px;padding:16px;text-align:center"><div style="font-size:26px;font-weight:700;color:var(--emerald)">₹${coll.toLocaleString()}</div><div style="font-size:11px;color:var(--gray-400)">Collected</div></div>
    <div style="background:rgba(244,63,94,.08);border:1px solid rgba(244,63,94,.2);border-radius:10px;padding:16px;text-align:center"><div style="font-size:26px;font-weight:700;color:var(--rose)">₹${(total-coll).toLocaleString()}</div><div style="font-size:11px;color:var(--gray-400)">Pending</div></div>
  </div>
  <div class="tbl-wrap"><table><thead><tr><th>Receipt</th><th>Student</th><th>Term</th><th>Total</th><th>Paid</th><th>Balance</th><th>Method</th><th>Status</th></tr></thead><tbody>
  ${store.fees.map(f=>`<tr><td class="fee-receipt-badge" style="font-size:10px">${f.receiptNo||'—'}</td><td><b>${f.studentName}</b></td><td>${f.term||'—'}</td><td>₹${f.amount.toLocaleString()}</td><td style="color:var(--emerald);font-weight:700">₹${f.paid.toLocaleString()}</td><td style="color:${f.amount-f.paid>0?'var(--rose)':'var(--gray-400)'};font-weight:700">₹${(f.amount-f.paid).toLocaleString()}</td><td><span class="badge b-cyan">${f.method||'—'}</span></td><td><span class="badge ${f.status==='paid'?'b-green':'b-red'}">${f.status}</span></td></tr>`).join('')}
  </tbody></table></div></div>`;
};
window.showExamReport=function(){
  const grades={};
  store.examResults.forEach(r=>{grades[r.grade]=(grades[r.grade]||0)+1;});
  document.getElementById('reportOut').innerHTML=`<div class="card"><div class="card-hdr"><div class="card-title">📝 Exam Performance Report</div></div>
  <div style="display:flex;gap:10px;flex-wrap:wrap;margin-bottom:18px">${Object.entries(grades).map(([g,c])=>`<div style="background:white;border:1px solid var(--gray-200);border-radius:9px;padding:10px 16px;text-align:center"><div class="grade ${gradeClass(g)}" style="margin:0 auto 6px">${g}</div><div style="font-size:18px;font-weight:700;color:var(--navy)">${c}</div><div style="font-size:10px;color:var(--gray-400)">students</div></div>`).join('')}</div>
  <div class="tbl-wrap"><table><thead><tr><th>Student</th><th>Exam</th><th>Subject</th><th>Marks</th><th>Grade</th></tr></thead><tbody>
  ${store.examResults.map(r=>{const ex=store.exams.find(e=>e.id===r.examId);const st=store.students.find(s=>s.id===r.studentId);return`<tr><td><b>${st?.name||r.studentId}</b></td><td>${ex?.type||'—'}</td><td>${ex?.subject||'—'}</td><td><b>${r.marks}</b>/${ex?.maxMarks||100}</td><td><span class="badge ${gradeClass(r.grade).replace('g','b-').replace('A','green').replace('B','blue').replace('C','amber').replace('D','red').replace('F','gray')}">${r.grade}</span></td></tr>`;}).join('')}
  </tbody></table></div></div>`;
};
window.showLibReport=function(){
  document.getElementById('reportOut').innerHTML=`<div class="card"><div class="card-hdr"><div class="card-title">📚 Library Report</div><button class="dl-btn" onclick="exportLibrary()">📥 Export</button></div>
  <div class="tbl-wrap"><table><thead><tr><th>Title</th><th>Author</th><th>Category</th><th>Total</th><th>Available</th><th>Issued</th><th>Utilization</th></tr></thead><tbody>
  ${store.books.map(b=>{const iss=b.copies-b.available;const ut=Math.round((iss/b.copies)*100)||0;return`<tr><td><b>${b.title}</b></td><td style="font-size:12px">${b.author||'—'}</td><td><span class="badge b-blue">${b.category||'—'}</span></td><td>${b.copies}</td><td style="color:var(--emerald);font-weight:700">${b.available}</td><td style="color:var(--amber);font-weight:700">${iss}</td><td><div style="display:flex;align-items:center;gap:6px"><div class="pb" style="width:60px"><div class="pf" style="width:${ut}%"></div></div><span style="font-size:11px">${ut}%</span></div></td></tr>`;}).join('')}
  </tbody></table></div></div>`;
};

// ═══════════════════════════════════════════════
//  ADMIN CREDENTIALS
// ═══════════════════════════════════════════════
function renderAdminCredentials() {
  area().innerHTML=`
  <div style="margin-bottom:18px"><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">🔑 Credentials Management</div><div style="font-size:12px;color:var(--gray-400)">Manage all login IDs and passwords</div></div>
  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr"><div class="card-title">🔒 Admin Login</div></div>
    <div class="form-grid">
      <div class="fg"><label>Admin ID</label><input class="fc" id="ai_id" value="${store.admin.id}"></div>
      <div class="fg"><label>Admin Password</label><input class="fc" id="ai_pass" value="${store.admin.pass}"></div>
    </div>
    <button class="btn btn-primary" onclick="saveAdminCred()">Update Admin Credentials</button>
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">👨‍🏫 Teacher Credentials</div></div>
      ${store.teachers.length===0?`<div class="empty"><div class="ei">👨‍🏫</div><p>No teachers yet</p></div>`:`
      <div class="tbl-wrap"><table>
        <thead><tr><th>Name</th><th>ID</th><th>Password</th><th>Action</th></tr></thead>
        <tbody>${store.teachers.map(t=>`<tr>
          <td>${t.name}</td>
          <td style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--emerald)">${t.id}</td>
          <td style="font-family:'JetBrains Mono',monospace;font-size:11px">${t.pass}</td>
          <td><button class="btn btn-warning btn-xs" onclick="editTeacherCred('${t.id}')">Edit</button></td>
        </tr>`).join('')}</tbody>
      </table></div>`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">👨‍🎓 Student Credentials</div></div>
      ${store.students.length===0?`<div class="empty"><div class="ei">👨‍🎓</div><p>No students yet</p></div>`:`
      <div class="tbl-wrap"><table>
        <thead><tr><th>Name</th><th>ID</th><th>Password</th><th>Action</th></tr></thead>
        <tbody>${store.students.map(s=>`<tr>
          <td>${s.name}</td>
          <td style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--blue)">${s.id}</td>
          <td style="font-family:'JetBrains Mono',monospace;font-size:11px">${s.pass}</td>
          <td><button class="btn btn-warning btn-xs" onclick="editStudentCred('${s.id}')">Edit</button></td>
        </tr>`).join('')}</tbody>
      </table></div>`}
    </div>
  </div>`;
}
function saveAdminCred(){store.admin.id=document.getElementById('ai_id').value;store.admin.pass=document.getElementById('ai_pass').value;saveStore();notify('Admin credentials updated!');}
function editTeacherCred(id){
  const t=store.teachers.find(x=>x.id===id); if(!t) return;
  openMo(`Edit Teacher Credentials — ${t.name}`,`
    <div class="fg"><label>Teacher ID</label><input class="fc" id="tc_id" value="${t.id}"></div>
    <div class="fg"><label>Password</label><input class="fc" id="tc_pass" value="${t.pass}"></div>
    <div style="display:flex;gap:8px;margin-top:14px"><button class="btn btn-primary" onclick="saveTeacherCred('${t.id}')">Save</button><button class="btn btn-outline" onclick="closeMo()">Cancel</button></div>
  `);
}
function saveTeacherCred(oldId){
  const t=store.teachers.find(x=>x.id===oldId);
  t.id=document.getElementById('tc_id').value;
  t.pass=document.getElementById('tc_pass').value;
  saveStore(); closeMo(); notify('Updated!'); renderAdminCredentials();
}
function editStudentCred(id){
  const s=store.students.find(x=>x.id===id); if(!s) return;
  openMo(`Edit Student Credentials — ${s.name}`,`
    <div class="fg"><label>Student ID</label><input class="fc" id="sc_id" value="${s.id}"></div>
    <div class="fg"><label>Password</label><input class="fc" id="sc_pass" value="${s.pass}"></div>
    <div style="display:flex;gap:8px;margin-top:14px"><button class="btn btn-primary" onclick="saveStudentCred('${s.id}')">Save</button><button class="btn btn-outline" onclick="closeMo()">Cancel</button></div>
  `);
}
function saveStudentCred(oldId){
  const s=store.students.find(x=>x.id===oldId);
  s.id=document.getElementById('sc_id').value;
  s.pass=document.getElementById('sc_pass').value;
  saveStore(); closeMo(); notify('Updated!'); renderAdminCredentials();
}

// ═══════════════════════════════════════════════
//  TEACHER HOME
// ═══════════════════════════════════════════════
function renderTeacherHome() {
  const t=currentUser;
  const myStudents=t.class ? store.students.filter(s=>s.class===t.class) : [];
  const myExams=store.exams.filter(e=>e.teacher===t.id);
  const myHW=store.homework.filter(h=>h.assignedBy===t.id);
  const pendingReviews=Object.keys(store.hwSubmissions).filter(k=>{
    const[hwId]=k.split('_');
    const hw=store.homework.find(h=>h.id===hwId);
    return hw?.assignedBy===t.id && store.hwSubmissions[k].status==='submitted';
  }).length;

  const annList=activeAnns();
  const urgentAnns=annList.filter(a=>a.type==='urgent'||a.pinned).slice(0,2);
  area().innerHTML=`
  ${urgentAnns.length?`<div class="ann-ticker"><span class="ann-ticker-label">📢 NOTICE</span><div class="ann-ticker-text">${urgentAnns.map(a=>a.title+' — '+a.details.substring(0,80)+'...  &nbsp;&nbsp;&nbsp; 🔸 &nbsp;&nbsp;&nbsp; ').join('')}${urgentAnns.map(a=>a.title+' — '+a.details.substring(0,80)+'...  &nbsp;&nbsp;&nbsp; 🔸 &nbsp;&nbsp;&nbsp; ').join('')}</div></div>`:''}
  <div class="sec-hero">
    <div><h2>Welcome, ${t.name?.split(' ')[0]}! 👩‍🏫</h2><p>${t.subject||'Teacher'} · Class ${t.class||'—'} · S.V. Vidhyalay</p></div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">👨‍🎓</div><div class="stat-info"><h3>${myStudents.length}</h3><p>My Students</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">📝</div><div class="stat-info"><h3>${myExams.length}</h3><p>Exams</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📖</div><div class="stat-info"><h3>${myHW.length}</h3><p>Assignments</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">📋</div><div class="stat-info"><h3>${pendingReviews}</h3><p>Pending Reviews</p></div></div>
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">My Students (${t.class||'—'})</div><button class="btn btn-primary btn-sm" onclick="loadSection('attendance')">Take Attendance</button></div>
      ${myStudents.length===0?`<div class="empty"><div class="ei">👨‍🎓</div><p>No students in your class</p></div>`:`
      ${myStudents.map(s=>{const{pct}=attStats(s.id);return`<div class="ir"><div><div style="font-weight:600;font-size:13px">${s.name}</div><div style="font-size:11px;color:var(--gray-400)">Roll #${s.roll||'—'}</div></div><div style="display:flex;align-items:center;gap:7px"><div class="pb" style="width:50px"><div class="pf" style="width:${pct}%"></div></div><b style="font-size:11px;color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}%</b></div></div>`;}).join('')}`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">HW Pending Review</div><button class="btn btn-outline btn-sm" onclick="loadSection('homework')">Manage HW</button></div>
      ${pendingReviews===0?`<div class="empty"><div class="ei">✅</div><p>All reviewed!</p></div>`:`
      ${Object.keys(store.hwSubmissions).filter(k=>{const[hwId]=k.split('_');const hw=store.homework.find(h=>h.id===hwId);return hw?.assignedBy===t.id&&store.hwSubmissions[k].status==='submitted';}).slice(0,5).map(k=>{
        const[hwId,sid]=k.split('_');const hw=store.homework.find(h=>h.id===hwId);const st=store.students.find(x=>x.id===sid);const sub=store.hwSubmissions[k];
        return`<div class="ir"><div><div style="font-weight:600;font-size:12.5px">${hw?.title||hwId}</div><div style="font-size:11px;color:var(--gray-400)">${st?.name||sid} · ${sub.fileName||'No file'}</div></div><div style="display:flex;gap:4px"><button class="btn btn-success btn-xs" onclick="quickAccept('${k}')">✅</button><button class="btn btn-danger btn-xs" onclick="quickReject('${k}')">❌</button></div></div>`;
      }).join('')}`}
    </div>
  </div>
  <div class="card">
    <div class="card-hdr">
      <div><div class="card-title">📢 Latest Announcements</div><div class="card-sub">${activeAnns().length} active for you</div></div>
      <button class="btn btn-outline btn-sm" onclick="loadSection('announcements')">View All</button>
    </div>
    ${annWidgetHTML(4)}
  </div>`;
}

window.quickAccept=function(k){store.hwSubmissions[k]={...store.hwSubmissions[k],status:'accepted'};saveStore();notify('Homework accepted!');renderTeacherHome();};
window.quickReject=function(k){store.hwSubmissions[k]={...store.hwSubmissions[k],status:'rejected'};saveStore();notify('Homework rejected.','❌');renderTeacherHome();};

// ═══════════════════════════════════════════════
//  TEACHER ATTENDANCE
// ═══════════════════════════════════════════════
function renderTeacherAttendance() {
  const t=currentUser;
  const myStudents=store.students.filter(s=>s.class===t.class);
  const today=new Date().toISOString().slice(0,10);
  let selDate=today;

  const render=()=>{
    area().innerHTML=`
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Attendance — Class ${t.class||'—'}</div></div>
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <input type="date" class="fc" style="width:155px" value="${selDate}" onchange="attDateCh(this.value)">
        <button class="btn btn-success btn-sm" onclick="markAllAtt('P','${selDate}')">✅ All Present</button>
        <button class="btn btn-danger btn-sm" onclick="markAllAtt('A','${selDate}')">❌ All Absent</button>
        <button class="btn btn-primary" onclick="saveAtt('${selDate}')">💾 Save</button>
        <button class="dl-btn" onclick="exportAttendance()">📥 Export</button>
      </div>
    </div>
    <div class="card">
      ${myStudents.length===0?`<div class="empty"><div class="ei">👨‍🎓</div><p>No students in your class</p></div>`:`
      <div class="tbl-wrap"><table>
        <thead><tr><th>Student</th><th>Roll</th><th>Overall Att.</th><th>Status for ${selDate}</th></tr></thead>
        <tbody>${myStudents.map(s=>{
          const status=(store.attendance[s.id]||{})[selDate]||null;
          const{pct}=attStats(s.id);
          return`<tr>
            <td><div style="display:flex;align-items:center;gap:10px"><div style="width:30px;height:30px;border-radius:8px;background:linear-gradient(135deg,var(--blue),var(--amber));display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:12px">${s.name[0]}</div><div><div style="font-weight:600">${s.name}</div></div></div></td>
            <td>${s.roll||'—'}</td>
            <td><div style="display:flex;align-items:center;gap:6px"><div class="pb" style="width:55px"><div class="pf" style="width:${pct}%"></div></div><b style="font-size:11px;color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}%</b></div></td>
            <td>
              <div id="att_${s.id}" onclick="toggleAtt('${s.id}','${selDate}')" style="display:inline-flex;align-items:center;padding:6px 16px;border-radius:8px;cursor:pointer;font-weight:600;font-size:12px;transition:all .15s;user-select:none;${status==='P'?'background:rgba(16,185,129,.12);color:var(--emerald)':status==='A'?'background:rgba(244,63,94,.1);color:var(--rose)':'background:var(--gray-100);color:var(--gray-400)'}">
                ${status==='P'?'✅ Present':status==='A'?'❌ Absent':'⚪ Not Marked'}
              </div>
            </td>
          </tr>`;
        }).join('')}</tbody>
      </table></div>
      <div style="margin-top:16px;padding:14px;background:var(--gray-50);border-radius:9px;display:flex;gap:20px;flex-wrap:wrap">
        <div><span style="font-size:20px;font-weight:700;color:var(--emerald)">${myStudents.filter(s=>(store.attendance[s.id]||{})[selDate]==='P').length}</span><span style="font-size:12px;color:var(--gray-400);margin-left:4px">Present</span></div>
        <div><span style="font-size:20px;font-weight:700;color:var(--rose)">${myStudents.filter(s=>(store.attendance[s.id]||{})[selDate]==='A').length}</span><span style="font-size:12px;color:var(--gray-400);margin-left:4px">Absent</span></div>
        <div><span style="font-size:20px;font-weight:700;color:var(--gray-400)">${myStudents.filter(s=>!(store.attendance[s.id]||{})[selDate]).length}</span><span style="font-size:12px;color:var(--gray-400);margin-left:4px">Unmarked</span></div>
      </div>`}
    </div>`;
  };

  window.attDateCh=(d)=>{selDate=d;render();};
  window.toggleAtt=(sid,date)=>{
    if(!store.attendance[sid]) store.attendance[sid]={};
    const cur=store.attendance[sid][date];
    store.attendance[sid][date]=cur==='P'?'A':'P';
    const el=document.getElementById(`att_${sid}`);
    if(el){const v=store.attendance[sid][date];el.style.cssText=`display:inline-flex;align-items:center;padding:6px 16px;border-radius:8px;cursor:pointer;font-weight:600;font-size:12px;transition:all .15s;user-select:none;${v==='P'?'background:rgba(16,185,129,.12);color:var(--emerald)':'background:rgba(244,63,94,.1);color:var(--rose)'}`;el.textContent=v==='P'?'✅ Present':'❌ Absent';}
  };
  window.markAllAtt=(status,date)=>{myStudents.forEach(s=>{if(!store.attendance[s.id])store.attendance[s.id]={};store.attendance[s.id][date]=status;});render();};
  window.saveAtt=(date)=>{saveStore();notify(`Attendance saved for ${date}!`);};
  window.exportAttendance=function(){
    const dates=[...new Set(myStudents.flatMap(s=>Object.keys(store.attendance[s.id]||{})))].sort();
    const headers=['Student','ID',...dates];
    const data=myStudents.map(s=>[s.name,s.id,...dates.map(d=>(store.attendance[s.id]||{})[d]||'—')]);
    exportToExcel(data,headers,'SV_Attendance_'+selDate);
  };
  render();
}

// ═══════════════════════════════════════════════
//  TEACHER EXAMS
// ═══════════════════════════════════════════════
function renderTeacherExams() {
  const t=currentUser;
  const myExams=store.exams.filter(e=>e.teacher===t.id);
  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Exams Management</div></div>
    <div style="display:flex;gap:8px">
      <button class="dl-btn" onclick="exportExamResults()">📥 Export Results</button>
      <button class="btn btn-primary" onclick="showAddExam()">+ Schedule Exam</button>
    </div>
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">Scheduled Exams</div></div>
      ${myExams.length===0?`<div class="empty"><div class="ei">📝</div><p>No exams scheduled</p></div>`:`
      <div class="tbl-wrap"><table><thead><tr><th>Subject</th><th>Type</th><th>Date</th><th>Class</th><th>Marks</th><th>Actions</th></tr></thead>
      <tbody>${myExams.map(e=>`<tr>
        <td><b>${e.subject}</b></td>
        <td><span class="badge b-violet">${e.type}</span></td>
        <td style="font-weight:600;font-size:12px">${fmtDate(e.date)}</td>
        <td>${e.class}</td>
        <td>${e.maxMarks}</td>
        <td><div style="display:flex;gap:4px">
          <button class="btn btn-primary btn-xs" onclick="showEnterResults('${e.id}')">Results</button>
          <button class="btn btn-danger btn-xs" onclick="deleteExam('${e.id}')">Del</button>
        </div></td>
      </tr>`).join('')}</tbody></table></div>`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">Results Entered</div></div>
      ${store.examResults.filter(r=>myExams.find(e=>e.id===r.examId)).length===0?`<div class="empty"><div class="ei">📊</div><p>No results entered</p></div>`:`
      <div class="tbl-wrap"><table><thead><tr><th>Student</th><th>Exam</th><th>Marks</th><th>Grade</th></tr></thead>
      <tbody>${store.examResults.filter(r=>myExams.find(e=>e.id===r.examId)).map(r=>{
        const ex=store.exams.find(e=>e.id===r.examId);const st=store.students.find(s=>s.id===r.studentId);
        return`<tr><td><b>${st?.name||r.studentId}</b></td><td style="font-size:12px">${ex?.subject} — ${ex?.type}</td><td><b>${r.marks}</b>/${ex?.maxMarks}</td><td><span class="badge ${gradeClass(r.grade).replace('g','b-').replace('A','green').replace('B','blue').replace('C','amber').replace('D','red').replace('F','gray')}">${r.grade}</span></td></tr>`;
      }).join('')}</tbody></table></div>`}
    </div>
  </div>`;
}

window.exportExamResults=function(){
  const t=currentUser;
  const myExams=store.exams.filter(e=>e.teacher===t.id);
  const data=store.examResults.filter(r=>myExams.find(e=>e.id===r.examId)).map(r=>{
    const ex=store.exams.find(e=>e.id===r.examId);const st=store.students.find(s=>s.id===r.studentId);
    return[st?.id||r.studentId,st?.name||r.studentId,st?.class||'',ex?.subject||'',ex?.type||'',ex?.date||'',r.marks,ex?.maxMarks||100,r.grade];
  });
  exportToExcel(data,['Student ID','Student Name','Class','Subject','Exam Type','Date','Marks','Max Marks','Grade'],'SV_ExamResults_'+new Date().toISOString().slice(0,10));
};

function showAddExam() {
  const t=currentUser;
  openMo('Schedule New Exam',`
    <div class="form-grid">
      <div class="fg"><label>Subject</label><input class="fc" id="ne_sub" value="${t.subject||''}"></div>
      <div class="fg"><label>Exam Type</label><select class="fc" id="ne_type"><option>Unit Test</option><option>Mid Term</option><option>Final Exam</option><option>Quiz</option><option>Assignment</option></select></div>
      <div class="fg"><label>Date</label><input class="fc" id="ne_date" type="date"></div>
      <div class="fg"><label>Class</label><input class="fc" id="ne_class" value="${t.class||''}"></div>
      <div class="fg"><label>Max Marks</label><input class="fc" id="ne_marks" type="number" value="100"></div>
      <div class="fg"><label>Duration</label><input class="fc" id="ne_dur" value="3 hrs"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewExam()">Schedule</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveNewExam() {
  const t=currentUser;
  const id=genId('exam');
  store.exams.push({id,subject:document.getElementById('ne_sub').value,type:document.getElementById('ne_type').value,date:document.getElementById('ne_date').value,class:document.getElementById('ne_class').value,maxMarks:parseInt(document.getElementById('ne_marks').value)||100,duration:document.getElementById('ne_dur').value,teacher:t.id});
  saveStore(); closeMo(); notify('Exam scheduled!'); renderTeacherExams();
}

function deleteExam(id){if(!confirm('Delete exam?'))return;store.exams=store.exams.filter(e=>e.id!==id);saveStore();notify('Deleted.','🗑️');renderTeacherExams();}

function showEnterResults(examId) {
  const ex=store.exams.find(e=>e.id===examId);
  const students=store.students.filter(s=>s.class===ex.class);
  openMo(`Enter Results — ${ex.subject} (${ex.type})`,`
    <div style="background:rgba(37,99,235,.06);border-radius:8px;padding:10px 14px;margin-bottom:16px;font-size:12px">
      <b>${ex.subject}</b> · ${ex.type} · Max: <b>${ex.maxMarks}</b> · Date: ${fmtDate(ex.date)}
    </div>
    ${students.length===0?`<div class="empty"><div class="ei">👨‍🎓</div><p>No students in class ${ex.class}</p></div>`:`
    ${students.map(s=>{
      const ex_=store.examResults.find(r=>r.examId===examId&&r.studentId===s.id);
      return`<div style="display:flex;align-items:center;justify-content:space-between;padding:9px 0;border-bottom:1px solid var(--gray-100)">
        <span style="font-weight:500;font-size:13px">${s.name}</span>
        <div style="display:flex;align-items:center;gap:7px">
          <input type="number" min="0" max="${ex.maxMarks}" value="${ex_?.marks||''}" class="fc" style="width:75px" id="res_${s.id}" placeholder="marks">
          <span style="font-size:11px;color:var(--gray-400)">/${ex.maxMarks}</span>
        </div>
      </div>`;
    }).join('')}
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveResults('${examId}')">Save Results</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>`}
  `);
}

function saveResults(examId) {
  const ex=store.exams.find(e=>e.id===examId);
  const students=store.students.filter(s=>s.class===ex.class);
  students.forEach(s=>{
    const inp=document.getElementById(`res_${s.id}`);
    if(!inp||inp.value==='') return;
    const marks=parseInt(inp.value);
    const grade=getGrade(marks,ex.maxMarks);
    const ex_=store.examResults.find(r=>r.examId===examId&&r.studentId===s.id);
    if(ex_){ex_.marks=marks;ex_.grade=grade;}
    else store.examResults.push({examId,studentId:s.id,marks,grade});
  });
  saveStore(); closeMo(); notify('Results saved!'); renderTeacherExams();
}

// ═══════════════════════════════════════════════
//  TEACHER HOMEWORK — with accept/reject + view/download
// ═══════════════════════════════════════════════
function renderTeacherHomework() {
  const t=currentUser;
  const myHW=store.homework.filter(h=>h.assignedBy===t.id);

  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Homework Management</div></div>
    <button class="btn btn-primary" onclick="showAddHW()">+ Assign Homework</button>
  </div>
  ${myHW.length===0?`<div class="card"><div class="empty"><div class="ei">📖</div><p>No homework assigned yet</p></div></div>`:`
  ${myHW.map(hw=>{
    const myStudents=store.students.filter(s=>s.class===hw.class);
    const submitted=myStudents.filter(s=>{const sub=store.hwSubmissions[`${hw.id}_${s.id}`];return sub&&sub.status!=='pending';}).length;
    const pendingReview=myStudents.filter(s=>store.hwSubmissions[`${hw.id}_${s.id}`]?.status==='submitted').length;
    return`
    <div class="card" style="margin-bottom:14px">
      <div style="display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:12px;flex-wrap:wrap;gap:8px">
        <div>
          <div style="font-weight:700;font-size:15px;color:var(--navy)">${hw.title}</div>
          <div style="display:flex;gap:6px;margin-top:6px;flex-wrap:wrap">
            <span class="badge b-violet">${hw.subject}</span><span class="badge b-blue">Class ${hw.class}</span>
            <span class="badge b-amber">Due: ${fmtDate(hw.dueDate)}</span>
            ${pendingReview>0?`<span class="badge b-red">${pendingReview} need review</span>`:''}
          </div>
        </div>
        <div style="text-align:right">
          <div style="font-size:12px;font-weight:600;color:var(--gray-600)">${submitted}/${myStudents.length} submitted</div>
          <div style="display:flex;gap:5px;margin-top:6px">
            <button class="btn btn-primary btn-xs" onclick="viewHWSubmissions('${hw.id}')">📋 View Submissions</button>
            <button class="btn btn-danger btn-xs" onclick="deleteHW('${hw.id}')">Del</button>
          </div>
        </div>
      </div>
      <div style="font-size:12.5px;color:var(--gray-500);margin-bottom:10px;line-height:1.5">${hw.desc||'—'}</div>
      <div style="display:flex;align-items:center;gap:8px">
        <div class="pb" style="flex:1"><div class="pf" style="width:${myStudents.length?Math.round((submitted/myStudents.length)*100):0}%"></div></div>
        <span style="font-size:11px;font-weight:600;color:var(--gray-500)">${myStudents.length?Math.round((submitted/myStudents.length)*100):0}%</span>
      </div>
    </div>`;
  }).join('')}`}`;
}

function viewHWSubmissions(hwId) {
  const hw=store.homework.find(h=>h.id===hwId);
  const students=store.students.filter(s=>s.class===hw.class);
  openMo(`Submissions — ${hw.title}`,`
    <div style="margin-bottom:14px;padding:10px 14px;background:rgba(37,99,235,.06);border-radius:8px;font-size:12px">
      <b>${hw.subject}</b> · Class ${hw.class} · Due: ${fmtDate(hw.dueDate)}
    </div>
    <div class="tbl-wrap"><table>
      <thead><tr><th>Student</th><th>File Uploaded</th><th>Status</th><th>Teacher Note</th><th>Actions</th></tr></thead>
      <tbody id="hwSubBody">
        ${students.map(s=>{
          const key=`${hwId}_${s.id}`;
          const sub=store.hwSubmissions[key]||{status:'pending'};
          return`<tr id="hwrow_${s.id}">
            <td><b>${s.name}</b></td>
            <td>${sub.fileName?`<span style="font-size:11px;color:var(--blue);cursor:pointer" onclick="teacherViewFile('${key}')" title="Click to view/download">📎 ${sub.fileName}</span>`:'<span style="color:var(--gray-400);font-size:11px">No file</span>'}</td>
            <td><span class="badge ${sub.status==='accepted'?'b-green':sub.status==='rejected'?'b-red':sub.status==='submitted'?'b-amber':'b-gray'}">${sub.status||'pending'}</span></td>
            <td style="font-size:11px;color:var(--gray-500);max-width:120px">${sub.teacherNote||'—'}</td>
            <td>
              <div style="display:flex;gap:4px;flex-wrap:wrap">
                ${sub.fileData?`<button class="btn btn-cyan btn-xs" onclick="teacherViewFile('${key}')">👁 View</button>`:''}
                ${sub.status==='submitted'?`
                  <button class="btn btn-success btn-xs" onclick="acceptHW('${key}')">✅ Accept</button>
                  <button class="btn btn-danger btn-xs" onclick="rejectHW('${key}')">❌ Reject</button>
                `:''}
                ${sub.status==='accepted'?`<span style="color:var(--emerald);font-size:11px">✅ Accepted</span>`:''}
                ${sub.status==='rejected'?`<span style="color:var(--rose);font-size:11px">❌ Rejected</span>`:''}
              </div>
            </td>
          </tr>`;
        }).join('')}
      </tbody>
    </table></div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="dl-btn" onclick="exportHWSubmissions('${hwId}')">📥 Export List</button>
      <button class="btn btn-outline" onclick="closeMo()">Close</button>
    </div>
  `);
}

window.acceptHW=function(key){
  const note=prompt('Add a note for student (optional):','Good work!');
  store.hwSubmissions[key]={...store.hwSubmissions[key],status:'accepted',teacherNote:note||''};
  saveStore(); notify('Homework accepted!');
  // refresh modal row
  const[hwId,sid]=key.split('_');viewHWSubmissions(hwId);
};

window.rejectHW=function(key){
  const note=prompt('Reason for rejection:','Please redo and resubmit.');
  store.hwSubmissions[key]={...store.hwSubmissions[key],status:'rejected',teacherNote:note||''};
  saveStore(); notify('Homework rejected.','❌');
  const[hwId,sid]=key.split('_');viewHWSubmissions(hwId);
};

window.teacherViewFile=function(key){
  const sub=store.hwSubmissions[key];
  if(!sub?.fileData){alert('No file available');return;}
  const isImg=['jpg','jpeg','png','gif','webp'].some(ext=>sub.fileName?.toLowerCase().endsWith(ext));
  const isPdf=sub.fileName?.toLowerCase().endsWith('.pdf');
  openMo(`📎 ${sub.fileName}`,`
    <div style="margin-bottom:14px;font-size:12px;color:var(--gray-500)">Submitted by student · File: ${sub.fileName}</div>
    ${isImg?`<img src="data:${sub.fileType};base64,${sub.fileData}" style="max-width:100%;border-radius:10px;margin-bottom:14px">`:`
    <div style="background:var(--gray-50);border:1px solid var(--gray-200);border-radius:10px;padding:16px;text-align:center;margin-bottom:14px">
      <div style="font-size:32px;margin-bottom:8px">${isPdf?'📄':'📎'}</div>
      <div style="font-weight:600;font-size:13px">${sub.fileName}</div>
      <div style="font-size:11px;color:var(--gray-400);margin-top:4px">${sub.fileSize||''}</div>
    </div>`}
    <div style="display:flex;gap:8px">
      <button class="dl-btn" onclick="downloadBase64File('${sub.fileData}','${sub.fileName}','${sub.fileType||'application/octet-stream'}')">📥 Download</button>
      <button class="btn btn-outline" onclick="closeMo()">Close</button>
    </div>
  `);
};

window.exportHWSubmissions=function(hwId){
  const hw=store.homework.find(h=>h.id===hwId);
  const students=store.students.filter(s=>s.class===hw.class);
  const data=students.map(s=>{const sub=store.hwSubmissions[`${hwId}_${s.id}`]||{};return[s.id,s.name,s.class,sub.status||'pending',sub.fileName||'No file',sub.teacherNote||''];});
  exportToExcel(data,['Student ID','Name','Class','Status','File','Teacher Note'],'SV_HW_'+hw.title.replace(/\s+/g,'_'));
};

function showAddHW() {
  const t=currentUser;
  openMo('Assign Homework',`
    <div class="fg"><label>Title *</label><input class="fc" id="nh_title" placeholder="Homework title"></div>
    <div class="form-grid">
      <div class="fg"><label>Subject</label><input class="fc" id="nh_sub" value="${t.subject||''}"></div>
      <div class="fg"><label>Class</label><input class="fc" id="nh_class" value="${t.class||''}"></div>
      <div class="fg"><label>Due Date</label><input class="fc" id="nh_due" type="date"></div>
    </div>
    <div class="fg"><label>Instructions / Description</label><textarea class="fc" id="nh_desc" placeholder="Detailed instructions..."></textarea></div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewHW()">Assign</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function saveNewHW(){
  const t=currentUser;
  const title=document.getElementById('nh_title').value.trim();
  if(!title){alert('Title required');return;}
  const id=genId('hw');
  store.homework.push({id,title,subject:document.getElementById('nh_sub').value,class:document.getElementById('nh_class').value,dueDate:document.getElementById('nh_due').value,desc:document.getElementById('nh_desc').value,assignedBy:t.id,status:'active',createdAt:new Date().toISOString().slice(0,10)});
  saveStore(); closeMo(); notify('Homework assigned!'); renderTeacherHomework();
}
function deleteHW(id){if(!confirm('Delete homework?'))return;store.homework=store.homework.filter(h=>h.id!==id);saveStore();notify('Deleted.','🗑️');renderTeacherHomework();}

// ═══════════════════════════════════════════════
//  TEACHER CLASS STUDENTS LIST
// ═══════════════════════════════════════════════
function renderTeacherClassStudents() {
  const t=currentUser;
  const classes=[...new Set(store.students.filter(s=>!t.class||s.class===t.class).map(s=>s.class))];
  let filterCls=t.class||'';
  const render=()=>{
    const list=filterCls?store.students.filter(s=>s.class===filterCls):store.students;
    area().innerHTML=`
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">Class Student List</div></div>
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <select class="fc" style="width:140px" onchange="clsFilter(this.value)">
          <option value="">All Classes</option>
          ${[...new Set(store.students.map(s=>s.class))].map(c=>`<option value="${c}" ${c===filterCls?'selected':''}>${c}</option>`).join('')}
        </select>
        <button class="dl-btn" onclick="exportClassList('${filterCls}')">📥 Export Class List</button>
      </div>
    </div>
    ${list.length===0?`<div class="card"><div class="empty"><div class="ei">👨‍🎓</div><p>No students found</p></div></div>`:`
    <div class="card">
      <div class="tbl-wrap"><table>
        <thead><tr><th>#</th><th>Student</th><th>ID</th><th>Class</th><th>Roll</th><th>Phone</th><th>Parent</th><th>Attendance</th><th>Fee Status</th></tr></thead>
        <tbody>
          ${list.map((s,i)=>{const{pct}=attStats(s.id);return`<tr>
            <td>${i+1}</td>
            <td><b>${s.name}</b><div style="font-size:11px;color:var(--gray-400)">${s.email||'—'}</div></td>
            <td style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--blue)">${s.id}</td>
            <td><span class="badge b-blue">${s.class}</span></td>
            <td>${s.roll||'—'}</td>
            <td style="font-size:12px">${s.phone||'—'}</td>
            <td style="font-size:12px">${s.parent||'—'}<div style="font-size:10px;color:var(--gray-400)">${s.parentPhone||''}</div></td>
            <td><div style="display:flex;align-items:center;gap:6px"><div class="pb" style="width:50px"><div class="pf" style="width:${pct}%"></div></div><b style="font-size:11px;color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct}%</b></div></td>
            <td><span class="badge ${s.feeStatus==='paid'?'b-green':'b-red'}">${s.feeStatus||'pending'}</span></td>
          </tr>`;}).join('')}
        </tbody>
      </table></div>
    </div>`}`;
  };
  window.clsFilter=(v)=>{filterCls=v;render();};
  window.exportClassList=(cls)=>{
    const list_=cls?store.students.filter(s=>s.class===cls):store.students;
    const data=list_.map((s,i)=>[i+1,s.id,s.name,s.class,s.roll||'',s.phone||'',s.email||'',s.parent||'',s.parentPhone||'',s.feeStatus||'pending']);
    exportToExcel(data,['#','ID','Name','Class','Roll','Phone','Email','Parent','Parent Phone','Fee Status'],'SV_ClassList_'+(cls||'All'));
  };
  render();
}

// ═══════════════════════════════════════════════
//  STUDENT ATTENDANCE
// ═══════════════════════════════════════════════
function renderStudentAttendance() {
  const s=currentUser;
  const recs=store.attendance[s.id]||{};
  const dates=Object.keys(recs).sort().reverse();
  const{total,present,pct}=attStats(s.id);
  area().innerHTML=`
  <div class="sec-hero">
    <div><h2>📋 My Attendance</h2><p>Your school attendance records</p></div>
    <div style="text-align:right;color:white"><div style="font-size:36px;font-weight:700">${pct}%</div><div style="font-size:12px;opacity:.5">${present}/${total} days present</div></div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>${present}</h3><p>Present</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">❌</div><div class="stat-info"><h3>${total-present}</h3><p>Absent</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">📅</div><div class="stat-info"><h3>${total}</h3><p>Total Days</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(${pct>=75?'16,185,129':'244,63,94'},.1)">${pct>=75?'🏆':'⚠️'}</div><div class="stat-info"><h3 style="color:${pct>=75?'var(--emerald)':'var(--rose)'}">${pct>=75?'Good':'Low'}</h3><p>Status</p></div></div>
  </div>
  <div class="card">
    <div style="margin-bottom:8px;display:flex;justify-content:space-between;font-size:12.5px"><span>Overall: <b>${pct}%</b></span><span style="color:var(--gray-400)">Minimum: <b>75%</b></span></div>
    <div class="pb" style="height:12px"><div class="pf" style="width:${pct}%;background:${pct>=75?'linear-gradient(90deg,var(--blue),var(--emerald))':'linear-gradient(90deg,var(--amber),var(--rose))'}"></div></div>
  </div>
  <div class="card">
    <div class="card-hdr"><div class="card-title">Attendance Records</div></div>
    ${dates.length?`<div class="tbl-wrap"><table><thead><tr><th>Date</th><th>Day</th><th>Status</th></tr></thead><tbody>
    ${dates.map(d=>`<tr><td style="font-family:'JetBrains Mono',monospace;font-size:12px">${d}</td><td style="font-size:12px;color:var(--gray-500)">${new Date(d).toLocaleDateString('en-IN',{weekday:'long'})}</td><td><span class="badge ${recs[d]==='P'?'b-green':'b-red'}">${recs[d]==='P'?'✅ Present':'❌ Absent'}</span></td></tr>`).join('')}
    </tbody></table></div>`:`<div class="empty"><div class="ei">📅</div><p>No attendance records yet</p></div>`}
  </div>`;
}

// ═══════════════════════════════════════════════
//  STUDENT EXAMS
// ═══════════════════════════════════════════════
function renderStudentExams() {
  const s=currentUser;
  const upcoming=store.exams.filter(e=>e.class===s.class&&new Date(e.date)>=new Date());
  const myRes=store.examResults.filter(r=>r.studentId===s.id);
  area().innerHTML=`
  <div class="sec-hero"><div><h2>📝 Exams</h2><p>Upcoming exams and your results</p></div></div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">Upcoming Exams</div><span class="badge b-amber">${upcoming.length} scheduled</span></div>
      ${upcoming.length?upcoming.map(e=>`
        <div style="border:1px solid var(--gray-200);border-radius:10px;padding:14px;margin-bottom:10px">
          <div style="display:flex;justify-content:space-between;align-items:flex-start">
            <div><div style="font-weight:700;font-size:14px;color:var(--navy)">${e.subject}</div>
              <div style="margin-top:6px;display:flex;gap:5px;flex-wrap:wrap"><span class="badge b-violet">${e.type}</span><span class="badge b-blue">${e.class}</span></div>
            </div>
            <div style="text-align:right"><div style="font-weight:700;font-size:16px;color:var(--blue)">${fmtDate(e.date)}</div><div style="font-size:11px;color:var(--gray-400)">${e.duration||'—'}</div></div>
          </div>
          <div style="margin-top:8px;font-size:12px;color:var(--gray-500)">Max marks: <b>${e.maxMarks}</b></div>
        </div>`).join(''):`<div class="empty"><div class="ei">📅</div><p>No upcoming exams</p></div>`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">My Results</div></div>
      ${myRes.length?myRes.map(r=>{const ex=store.exams.find(e=>e.id===r.examId);const pct_=ex?Math.round((r.marks/ex.maxMarks)*100):0;return`
        <div style="border:1px solid var(--gray-200);border-radius:10px;padding:14px;margin-bottom:10px">
          <div style="display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:8px">
            <div><div style="font-weight:700;font-size:14px">${ex?.subject||r.examId}</div><div style="font-size:12px;color:var(--gray-400)">${ex?.type||'—'} · ${fmtDate(ex?.date)}</div></div>
            <div class="grade ${gradeClass(r.grade)}" style="width:38px;height:38px;font-size:15px">${r.grade}</div>
          </div>
          <div style="display:flex;justify-content:space-between;font-size:12.5px;margin-bottom:6px"><span>Score: <b>${r.marks}/${ex?.maxMarks||100}</b></span><b style="color:${pct_>=75?'var(--emerald)':'var(--rose)'}">${pct_}%</b></div>
          <div class="pb"><div class="pf" style="width:${pct_}%;background:${pct_>=75?'linear-gradient(90deg,var(--blue),var(--emerald))':'linear-gradient(90deg,var(--amber),var(--rose))'}"></div></div>
        </div>`}).join(''):`<div class="empty"><div class="ei">📊</div><p>No results yet</p></div>`}
    </div>
  </div>`;
}

// ═══════════════════════════════════════════════
//  STUDENT HOMEWORK — with file upload
// ═══════════════════════════════════════════════
function renderStudentHomework() {
  const s=currentUser;
  const myHW=store.homework.filter(h=>h.class===s.class);
  area().innerHTML=`
  <div class="sec-hero"><div><h2>📖 Homework</h2><p>Assignments and submissions</p></div></div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">⏳</div><div class="stat-info"><h3>${myHW.filter(h=>!(store.hwSubmissions[`${h.id}_${s.id}`]?.status)||store.hwSubmissions[`${h.id}_${s.id}`].status==='pending').length}</h3><p>Pending</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📤</div><div class="stat-info"><h3>${myHW.filter(h=>store.hwSubmissions[`${h.id}_${s.id}`]?.status==='submitted').length}</h3><p>Submitted</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>${myHW.filter(h=>store.hwSubmissions[`${h.id}_${s.id}`]?.status==='accepted').length}</h3><p>Accepted</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">❌</div><div class="stat-info"><h3>${myHW.filter(h=>store.hwSubmissions[`${h.id}_${s.id}`]?.status==='rejected').length}</h3><p>Rejected</p></div></div>
  </div>
  <div class="card">
    ${myHW.length===0?`<div class="empty"><div class="ei">🎉</div><p>No homework assigned yet</p></div>`:`
    ${myHW.map(h=>{
      const key=`${h.id}_${s.id}`;
      const sub=store.hwSubmissions[key]||{status:'pending'};
      const overdue=new Date(h.dueDate)<new Date()&&sub.status==='pending';
      const statusColor=sub.status==='accepted'?'b-green':sub.status==='rejected'?'b-red':sub.status==='submitted'?'b-amber':'b-gray';
      const statusLabel=sub.status==='accepted'?'✅ Accepted':sub.status==='rejected'?'❌ Rejected':sub.status==='submitted'?'📤 Submitted':overdue?'⚠️ Overdue':'⏳ Pending';
      return`
      <div style="border:1.5px solid ${sub.status==='accepted'?'rgba(16,185,129,.25)':sub.status==='rejected'?'rgba(244,63,94,.25)':overdue?'rgba(244,63,94,.2)':'var(--gray-200)'};border-radius:12px;padding:16px;margin-bottom:12px;background:${sub.status==='accepted'?'rgba(16,185,129,.02)':sub.status==='rejected'?'rgba(244,63,94,.02)':'white'}">
        <div style="display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:10px;flex-wrap:wrap;gap:6px">
          <div>
            <div style="font-weight:700;font-size:14px;color:var(--navy)">${h.title}</div>
            <div style="display:flex;gap:5px;margin-top:6px;flex-wrap:wrap">
              <span class="badge b-violet">${h.subject}</span>
              <span class="badge ${statusColor}">${statusLabel}</span>
              ${overdue?'':''}
            </div>
          </div>
          <div style="text-align:right"><div style="font-size:12px;font-weight:600;color:${overdue?'var(--rose)':'var(--navy)'}">Due: ${fmtDate(h.dueDate)}</div></div>
        </div>
        <div style="font-size:12.5px;color:var(--gray-500);margin-bottom:12px;line-height:1.5">${h.desc||'—'}</div>
        ${sub.teacherNote?`<div style="background:${sub.status==='accepted'?'rgba(16,185,129,.08)':'rgba(244,63,94,.08)'};border-radius:7px;padding:8px 12px;font-size:12px;margin-bottom:10px">💬 Teacher: <i>${sub.teacherNote}</i></div>`:''}
        ${sub.fileName?`<div style="font-size:12px;color:var(--blue);margin-bottom:10px">📎 Submitted: ${sub.fileName}</div>`:''}
        ${(sub.status==='pending'||sub.status==='rejected')?`
          <div>
            <label class="upload-zone" for="hw_file_${h.id}">
              <input type="file" id="hw_file_${h.id}" onchange="previewHWFile('${h.id}','${s.id}',this)">
              <div style="font-size:22px;margin-bottom:6px">📎</div>
              <div style="font-size:12.5px;font-weight:600;color:var(--gray-700)">Click to upload file</div>
              <div style="font-size:11px;color:var(--gray-400);margin-top:3px">PDF, DOC, Image, any format</div>
            </label>
            <div id="hw_preview_${h.id}" style="margin-top:8px"></div>
            <button class="btn btn-primary btn-sm" style="margin-top:8px" onclick="submitHW('${h.id}','${s.id}')">📤 Submit Homework</button>
          </div>
        `:''}
        ${sub.status==='submitted'?`<div style="color:var(--amber);font-size:12.5px;font-weight:500">⏳ Submitted — awaiting teacher review</div>`:''}
        ${sub.status==='accepted'?`<div style="color:var(--emerald);font-size:12.5px;font-weight:500">✅ Homework accepted by teacher!</div>`:''}
      </div>`;
    }).join('')}`}
  </div>`;
}

// Store pending file data before submission
const pendingFiles = {};

window.previewHWFile=function(hwId, sid, input){
  const file=input.files[0]; if(!file) return;
  const preview=document.getElementById(`hw_preview_${hwId}`);
  preview.innerHTML=`<div style="background:rgba(37,99,235,.06);border-radius:7px;padding:8px 12px;font-size:12px;display:flex;align-items:center;gap:8px">
    <span>📎</span><span style="font-weight:500">${file.name}</span><span style="color:var(--gray-400)">(${(file.size/1024).toFixed(1)} KB)</span>
  </div>`;
  // Read file as base64
  const reader=new FileReader();
  reader.onload=function(e){
    const b64=e.target.result.split(',')[1];
    pendingFiles[hwId]={fileName:file.name,fileData:b64,fileType:file.type,fileSize:(file.size/1024).toFixed(1)+' KB'};
  };
  reader.readAsDataURL(file);
};

window.submitHW=function(hwId, sid){
  const key=`${hwId}_${sid}`;
  const fileInfo=pendingFiles[hwId]||{};
  store.hwSubmissions[key]={
    status:'submitted',
    fileName:fileInfo.fileName||null,
    fileData:fileInfo.fileData||null,
    fileType:fileInfo.fileType||null,
    fileSize:fileInfo.fileSize||null,
    submittedAt:new Date().toISOString(),
    teacherNote:''
  };
  delete pendingFiles[hwId];
  saveStore(); notify('Homework submitted!'); renderStudentHomework();
};

// ═══════════════════════════════════════════════
//  STUDENT LIBRARY
// ═══════════════════════════════════════════════
function renderStudentLibrary() {
  const s=currentUser;
  const myBooks=store.issuedBooks.filter(b=>b.studentId===s.id);
  area().innerHTML=`
  <div class="sec-hero"><div><h2>📚 Library</h2><p>Your issued books and catalog</p></div></div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">My Issued Books</div></div>
      ${myBooks.length?myBooks.map(b=>{const ov=b.status==='issued'&&new Date(b.dueDate)<new Date();return`
        <div style="border:1px solid ${ov?'rgba(244,63,94,.3)':'var(--gray-200)'};border-radius:10px;padding:14px;margin-bottom:10px">
          <div style="font-weight:700;font-size:14px;margin-bottom:6px">${b.bookTitle}</div>
          <span class="badge ${b.status==='issued'?(ov?'b-red':'b-amber'):'b-green'}">${b.status==='issued'?(ov?'Overdue':'Issued'):'Returned'}</span>
          <div style="margin-top:8px;font-size:12px;color:var(--gray-500);display:flex;gap:14px;flex-wrap:wrap">
            <span>📅 Issued: ${b.issueDate}</span>
            <span style="color:${ov?'var(--rose)':'inherit'}">⏰ Due: ${b.dueDate}</span>
            ${b.returnDate?`<span>✅ Returned: ${b.returnDate}</span>`:''}
          </div>
        </div>`}).join(''):`<div class="empty"><div class="ei">📚</div><p>No books currently issued</p></div>`}
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">Library Catalog</div></div>
      ${store.books.length?`<div class="tbl-wrap"><table><thead><tr><th>Title</th><th>Author</th><th>Available</th></tr></thead><tbody>
      ${store.books.map(b=>`<tr><td><b>${b.title}</b><div style="font-size:10px;color:var(--gray-400)">${b.category||''}</div></td><td style="font-size:12px">${b.author||'—'}</td><td><b style="color:${b.available>0?'var(--emerald)':'var(--rose)'}">${b.available}</b><span style="color:var(--gray-400);font-size:10px">/${b.copies}</span></td></tr>`).join('')}
      </tbody></table></div>`:`<div class="empty"><div class="ei">📚</div><p>No books in catalog</p></div>`}
    </div>
  </div>`;
}

// ═══════════════════════════════════════════════
//  STUDENT FEES DASHBOARD
// ═══════════════════════════════════════════════
function renderStudentFees() {
  const s=currentUser;
  const myFees=store.fees.filter(f=>f.studentId===s.id);
  const totalAmount=myFees.reduce((a,f)=>a+f.amount,0);
  const totalPaid=myFees.reduce((a,f)=>a+f.paid,0);
  const totalPending=totalAmount-totalPaid;
  const fs=store.feeStructures[s.id]||{};

  area().innerHTML=`
  <div class="sec-hero">
    <div><h2>💰 My Fee Details</h2><p>Complete fee records and payment history</p></div>
    <div style="text-align:right;color:white">
      <div style="font-size:12px;opacity:.5">Total Paid</div>
      <div style="font-size:30px;font-weight:700">₹${totalPaid.toLocaleString()}</div>
    </div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">💰</div><div class="stat-info"><h3>₹${totalAmount.toLocaleString()}</h3><p>Total Fees</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>₹${totalPaid.toLocaleString()}</h3><p>Paid</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">⏳</div><div class="stat-info"><h3 style="color:${totalPending>0?'var(--rose)':'var(--emerald)'}">₹${totalPending.toLocaleString()}</h3><p>Balance Due</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📊</div><div class="stat-info"><h3 style="color:${s.feeStatus==='paid'?'var(--emerald)':'var(--rose)'}">${s.feeStatus==='paid'?'Paid':'Pending'}</h3><p>Status</p></div></div>
  </div>

  ${fs.amount?`
  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr"><div class="card-title">Fee Structure Set by Admin</div></div>
    <div class="two-col">
      <div>
        <div class="ir"><span class="ir-label">Term</span><span class="ir-val">${fs.term||'—'}</span></div>
        <div class="ir"><span class="ir-label">Total Fee Amount</span><span class="ir-val" style="font-size:18px;color:var(--navy)">₹${(fs.amount||0).toLocaleString()}</span></div>
        <div class="ir"><span class="ir-label">Due Date</span><span class="ir-val" style="color:${fs.dueDate&&new Date(fs.dueDate)<new Date()?'var(--rose)':'var(--navy)'}">${fmtDate(fs.dueDate)}</span></div>
      </div>
      <div>
        <div style="font-size:12px;font-weight:600;color:var(--navy);margin-bottom:6px">Fee Breakdown:</div>
        <div style="font-size:12.5px;color:var(--gray-600);line-height:1.7;white-space:pre-wrap">${fs.desc||'No breakdown provided'}</div>
      </div>
    </div>
  </div>`:''}

  <div class="card">
    <div class="card-hdr"><div class="card-title">Payment History</div></div>
    ${myFees.length===0?`<div class="empty"><div class="ei">💳</div><p>No payment records yet</p></div>`:`
    ${myFees.map(f=>`
      <div class="fee-card">
        <div class="fee-card-header">
          <div>
            <div style="font-weight:700;font-size:14px;color:var(--navy)">${f.term||'Fee Payment'}</div>
            <div style="font-size:11px;color:var(--gray-400);margin-top:2px">${fmtDate(f.date)}</div>
          </div>
          <div style="display:flex;align-items:center;gap:8px">
            <span class="fee-receipt-badge">${f.receiptNo||'—'}</span>
            <span class="badge ${f.status==='paid'?'b-green':'b-red'}">${f.status}</span>
          </div>
        </div>
        <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(140px,1fr));gap:10px;font-size:12.5px">
          <div><div style="color:var(--gray-400);font-size:10px;text-transform:uppercase;letter-spacing:.4px;margin-bottom:2px">Total Fee</div><div style="font-weight:700;color:var(--navy)">₹${f.amount.toLocaleString()}</div></div>
          <div><div style="color:var(--gray-400);font-size:10px;text-transform:uppercase;letter-spacing:.4px;margin-bottom:2px">Amount Paid</div><div style="font-weight:700;color:var(--emerald)">₹${f.paid.toLocaleString()}</div></div>
          <div><div style="color:var(--gray-400);font-size:10px;text-transform:uppercase;letter-spacing:.4px;margin-bottom:2px">Balance</div><div style="font-weight:700;color:${f.amount-f.paid>0?'var(--rose)':'var(--emerald)'}">₹${(f.amount-f.paid).toLocaleString()}</div></div>
          <div><div style="color:var(--gray-400);font-size:10px;text-transform:uppercase;letter-spacing:.4px;margin-bottom:2px">Method</div><span class="badge b-cyan">${f.method||'—'}</span></div>
          ${f.ref?`<div><div style="color:var(--gray-400);font-size:10px;text-transform:uppercase;letter-spacing:.4px;margin-bottom:2px">Reference</div><div style="font-size:12px;font-family:'JetBrains Mono',monospace">${f.ref}</div></div>`:''}
        </div>
        <div style="margin-top:10px">
          <button class="btn btn-outline btn-xs" onclick="viewReceipt('${f.id}')">🧾 View Receipt</button>
        </div>
      </div>`).join('')}`}
  </div>`;
}

// ═══════════════════════════════════════════════
//  STUDENT HW TRACKER
// ═══════════════════════════════════════════════
function renderStudentHWTrack() {
  const s=currentUser;
  const myHW=store.homework.filter(h=>h.class===s.class);
  area().innerHTML=`
  <div class="sec-hero"><div><h2>📌 Homework Tracker</h2><p>Track your assignment pipeline status</p></div></div>
  <div class="card">
    ${myHW.length===0?`<div class="empty"><div class="ei">🎉</div><p>No homework assigned yet</p></div>`:`
    ${myHW.map(h=>{
      const key=`${h.id}_${s.id}`;
      const sub=store.hwSubmissions[key]||{status:'pending'};
      const rawStatus=sub.status||'pending';
      const stageIdx=rawStatus==='pending'?0:rawStatus==='submitted'?1:rawStatus==='accepted'||rawStatus==='rejected'?2:0;
      const steps=['📋 Assigned','📤 Submitted',rawStatus==='rejected'?'❌ Rejected':'✅ Reviewed'];
      const overdue=new Date(h.dueDate)<new Date()&&rawStatus==='pending';
      return`
      <div style="margin-bottom:22px;padding-bottom:22px;border-bottom:1px solid var(--gray-100)">
        <div style="display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:14px;flex-wrap:wrap;gap:8px">
          <div>
            <div style="font-weight:700;font-size:14px;color:var(--navy)">${h.title}</div>
            <div style="display:flex;gap:5px;margin-top:5px;flex-wrap:wrap">
              <span class="badge b-violet">${h.subject}</span>
              ${overdue?'<span class="badge b-red">⚠️ Overdue</span>':''}
              ${rawStatus==='accepted'?'<span class="badge b-green">✅ Accepted</span>':''}
              ${rawStatus==='rejected'?'<span class="badge b-red">❌ Rejected — Resubmit</span>':''}
            </div>
          </div>
          <div style="font-size:12px;color:var(--gray-400)">Due: <b style="color:${overdue?'var(--rose)':'var(--navy)'}">${fmtDate(h.dueDate)}</b></div>
        </div>
        <div style="display:flex;align-items:center;margin-bottom:10px">
          ${steps.map((label,i)=>`
            <div style="flex:1;text-align:center;position:relative;z-index:1">
              <div style="width:32px;height:32px;border-radius:50%;margin:0 auto 5px;display:flex;align-items:center;justify-content:center;font-size:13px;font-weight:700;border:2px solid ${i<=stageIdx?(i===stageIdx&&rawStatus==='rejected'?'var(--rose)':'var(--blue)'):'var(--gray-200)'};background:${i<stageIdx?(rawStatus==='rejected'&&i===1?'var(--amber)':'var(--emerald)'):i===stageIdx?(rawStatus==='rejected'?'var(--rose)':'var(--blue)'):'white'};color:${i<=stageIdx?'white':'var(--gray-300)'}">${i<=stageIdx?'✓':i+1}</div>
              <div style="font-size:10px;font-weight:600;text-transform:uppercase;letter-spacing:.3px;color:${i<=stageIdx?'var(--navy)':'var(--gray-300)'}">${label}</div>
            </div>
            ${i<steps.length-1?`<div style="flex:.5;height:2px;background:${i<stageIdx?'var(--emerald)':'var(--gray-200)'};margin-bottom:16px"></div>`:''}`
          ).join('')}
        </div>
        ${sub.teacherNote?`<div style="background:${rawStatus==='accepted'?'rgba(16,185,129,.08)':'rgba(244,63,94,.08)'};border-radius:7px;padding:8px 12px;font-size:12px;margin-top:8px">💬 Teacher note: <i>${sub.teacherNote}</i></div>`:''}
        ${sub.fileName?`<div style="font-size:12px;color:var(--blue);margin-top:6px">📎 File: ${sub.fileName}</div>`:''}
        ${rawStatus==='pending'||rawStatus==='rejected'?`
          <div style="margin-top:10px;display:flex;gap:8px">
            <button class="btn btn-primary btn-sm" onclick="loadSection('homework')">📤 ${rawStatus==='rejected'?'Resubmit':'Submit Now'}</button>
          </div>`:''}
      </div>`;
    }).join('')}`}
  </div>`;
}


// ═══════════════════════════════════════════════
//  ADMIN — CLASSES & STANDARDS MANAGEMENT
// ═══════════════════════════════════════════════
function renderAdminClasses() {
  area().innerHTML = `
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">🏫 Classes & Standards</div><div style="font-size:12px;color:var(--gray-400)">${(store.classes||[]).length} classes configured</div></div>
    <button class="btn btn-primary" onclick="showAddClass()">+ Add Class / Standard</button>
  </div>
  <div class="stat-grid">
    ${(store.classes||[]).map(cls => {
      const count = store.students.filter(s => s.class === cls.name).length;
      const teacher = store.teachers.find(t => t.class === cls.name);
      return `
      <div style="background:white;border-radius:14px;padding:20px;border:1px solid var(--gray-200);box-shadow:var(--shadow);position:relative;overflow:hidden">
        <div style="position:absolute;top:0;right:0;width:60px;height:60px;background:linear-gradient(135deg,rgba(37,99,235,.08),rgba(37,99,235,.02));border-radius:0 14px 0 40px;display:flex;align-items:flex-start;justify-content:flex-end;padding:10px;font-size:20px">${cls.icon||'🏫'}</div>
        <div style="font-family:'Playfair Display',serif;font-size:20px;color:var(--navy);margin-bottom:4px">${cls.name}</div>
        <div style="font-size:12px;color:var(--gray-400);margin-bottom:10px">${cls.section?'Section '+cls.section:''} ${cls.medium?'· '+cls.medium+' Medium':''}</div>
        <div style="display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px">
          <span class="badge b-blue">👨‍🎓 ${count} Students</span>
          ${teacher ? `<span class="badge b-green">👨‍🏫 ${teacher.name.split(' ')[0]}</span>` : '<span class="badge b-gray">No Teacher</span>'}
        </div>
        <div style="font-size:11px;color:var(--gray-400)">${cls.subjects||'No subjects listed'}</div>
        <div style="display:flex;gap:5px;margin-top:12px">
          <button class="btn btn-warning btn-xs" onclick="editClass('${cls.id}')">Edit</button>
          <button class="btn btn-primary btn-xs" onclick="viewClassStudents('${cls.name}')">Students</button>
          <button class="btn btn-danger btn-xs" onclick="deleteClass('${cls.id}')">Delete</button>
        </div>
      </div>`;
    }).join('') || `<div style="grid-column:1/-1"><div class="empty"><div class="ei">🏫</div><p>No classes added yet. Click "Add Class / Standard" to get started.</p></div></div>`}
  </div>`;
}

function showAddClass() {
  const teachers = store.teachers.map(t => `<option value="${t.id}">${t.name} (${t.id})</option>`).join('');
  openMo('Add Class / Standard', `
    <div class="form-grid">
      <div class="fg"><label>Class Name / Standard *</label><input class="fc" id="nc_name" placeholder="e.g. 10, 11, 12, Class 5"></div>
      <div class="fg"><label>Section</label><input class="fc" id="nc_section" placeholder="e.g. A, B, Science, Commerce"></div>
      <div class="fg"><label>Medium of Instruction</label><select class="fc" id="nc_medium"><option value="">Select</option><option>English</option><option>Gujarati</option><option>Hindi</option><option>Semi-English</option></select></div>
      <div class="fg"><label>Class Teacher</label><select class="fc" id="nc_teacher"><option value="">None</option>${teachers}</select></div>
      <div class="fg"><label>Display Icon</label><input class="fc" id="nc_icon" placeholder="🏫" maxlength="2" value="🏫"></div>
      <div class="fg"><label>Capacity</label><input class="fc" id="nc_cap" type="number" placeholder="40"></div>
      <div class="fg form-full"><label>Subjects (comma-separated)</label><input class="fc" id="nc_subjects" placeholder="Math, Science, English, Gujarati, History"></div>
      <div class="fg form-full"><label>Description / Notes</label><textarea class="fc" id="nc_desc" placeholder="Additional notes about this class..."></textarea></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewClass()">Add Class</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveNewClass() {
  const name = document.getElementById('nc_name').value.trim();
  if (!name) { alert('Class name is required'); return; }
  if (!store.classes) store.classes = [];
  const id = 'CLS' + Date.now();
  const teacherId = document.getElementById('nc_teacher').value;
  // Assign teacher to this class
  if (teacherId) {
    const t = store.teachers.find(x => x.id === teacherId);
    if (t) t.class = name;
  }
  store.classes.push({
    id, name,
    section: document.getElementById('nc_section').value,
    medium: document.getElementById('nc_medium').value,
    teacher: teacherId,
    icon: document.getElementById('nc_icon').value || '🏫',
    capacity: parseInt(document.getElementById('nc_cap').value) || 40,
    subjects: document.getElementById('nc_subjects').value,
    desc: document.getElementById('nc_desc').value,
    createdAt: new Date().toISOString().slice(0, 10)
  });
  saveStore(); closeMo(); notify('Class added!'); renderAdminClasses();
}

function editClass(id) {
  if (!store.classes) return;
  const cls = store.classes.find(c => c.id === id); if (!cls) return;
  const teachers = store.teachers.map(t => `<option value="${t.id}" ${t.id===cls.teacher?'selected':''}>${t.name} (${t.id})</option>`).join('');
  openMo(`Edit — ${cls.name}`, `
    <div class="form-grid">
      <div class="fg"><label>Class Name *</label><input class="fc" id="ec_name" value="${cls.name}"></div>
      <div class="fg"><label>Section</label><input class="fc" id="ec_section" value="${cls.section||''}"></div>
      <div class="fg"><label>Medium</label><select class="fc" id="ec_medium"><option value="">Select</option><option ${cls.medium==='English'?'selected':''}>English</option><option ${cls.medium==='Gujarati'?'selected':''}>Gujarati</option><option ${cls.medium==='Hindi'?'selected':''}>Hindi</option><option ${cls.medium==='Semi-English'?'selected':''}>Semi-English</option></select></div>
      <div class="fg"><label>Class Teacher</label><select class="fc" id="ec_teacher"><option value="">None</option>${teachers}</select></div>
      <div class="fg"><label>Icon</label><input class="fc" id="ec_icon" value="${cls.icon||'🏫'}" maxlength="2"></div>
      <div class="fg"><label>Capacity</label><input class="fc" id="ec_cap" type="number" value="${cls.capacity||40}"></div>
      <div class="fg form-full"><label>Subjects</label><input class="fc" id="ec_subjects" value="${cls.subjects||''}"></div>
      <div class="fg form-full"><label>Notes</label><textarea class="fc" id="ec_desc">${cls.desc||''}</textarea></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveEditClass('${id}')">Update</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

function saveEditClass(id) {
  const cls = store.classes.find(c => c.id === id); if (!cls) return;
  const oldName = cls.name;
  cls.name = document.getElementById('ec_name').value;
  cls.section = document.getElementById('ec_section').value;
  cls.medium = document.getElementById('ec_medium').value;
  cls.icon = document.getElementById('ec_icon').value;
  cls.capacity = parseInt(document.getElementById('ec_cap').value) || 40;
  cls.subjects = document.getElementById('ec_subjects').value;
  cls.desc = document.getElementById('ec_desc').value;
  const teacherId = document.getElementById('ec_teacher').value;
  cls.teacher = teacherId;
  if (teacherId) { const t = store.teachers.find(x => x.id === teacherId); if (t) t.class = cls.name; }
  // Update students if name changed
  if (oldName !== cls.name) store.students.filter(s => s.class === oldName).forEach(s => s.class = cls.name);
  saveStore(); closeMo(); notify('Class updated!'); renderAdminClasses();
}

function deleteClass(id) {
  if (!confirm('Delete this class? Students will remain but class assignment may be lost.')) return;
  store.classes = (store.classes || []).filter(c => c.id !== id);
  saveStore(); notify('Class deleted.', '🗑️'); renderAdminClasses();
}

function viewClassStudents(className) {
  const students = store.students.filter(s => s.class === className);
  openMo(`Students — ${className}`, `
    <div style="margin-bottom:14px"><span class="badge b-blue">${students.length} students</span></div>
    ${students.length === 0 ? `<div class="empty"><div class="ei">👨‍🎓</div><p>No students in this class</p></div>` : `
    <div class="tbl-wrap"><table>
      <thead><tr><th>Photo</th><th>Name</th><th>ID</th><th>Roll</th><th>Attendance</th><th>Fee</th></tr></thead>
      <tbody>${students.map(s => {
        const {pct} = attStats(s.id);
        return `<tr>
          <td>${s.photo ? `<img src="${s.photo}" style="width:32px;height:32px;border-radius:8px;object-fit:cover">` : `<div style="width:32px;height:32px;border-radius:8px;background:linear-gradient(135deg,var(--blue),var(--amber));display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:13px">${s.name[0]}</div>`}</td>
          <td><b>${s.name}</b></td>
          <td style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--blue)">${s.id}</td>
          <td>${s.roll || '—'}</td>
          <td><b style="color:${pct >= 75 ? 'var(--emerald)' : 'var(--rose)'}">${pct}%</b></td>
          <td><span class="badge ${s.feeStatus === 'paid' ? 'b-green' : 'b-red'}">${s.feeStatus || 'pending'}</span></td>
        </tr>`;
      }).join('')}</tbody>
    </table></div>`}
    <div style="margin-top:14px"><button class="dl-btn" onclick="exportToExcel(${JSON.stringify(students.map(s=>{const{pct}=attStats(s.id);return[s.id,s.name,s.class,s.roll||'',s.phone||'',pct+'%',s.feeStatus||'pending'];}))},['ID','Name','Class','Roll','Phone','Attendance','Fee Status'],'SV_Class_${className}')">📥 Export</button></div>
  `);
}

// ═══════════════════════════════════════════════
//  ADMIN — STUDENT ADMISSION FORM
// ═══════════════════════════════════════════════
function renderAdminAdmission() {
  const classes = store.classes || [];
  const classOptions = classes.map(c => `<option value="${c.name}">${c.name}${c.section ? ' - ' + c.section : ''}</option>`).join('');

  area().innerHTML = `
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📋 Student Admission Form</div><div style="font-size:12px;color:var(--gray-400)">Complete admission process for new students</div></div>
    <button class="dl-btn" onclick="exportStudents()">📥 Export All Students</button>
  </div>

  <!-- Watermark wrapper -->
  <div style="position:relative">
    <!-- Logo watermark -->
    <div style="position:fixed;top:50%;left:calc(var(--sidebar-w) + (100vw - var(--sidebar-w))/2);transform:translate(-50%,-50%);width:320px;height:320px;background-image:url('${LOGO_URI}');background-size:contain;background-repeat:no-repeat;background-position:center;opacity:.07;pointer-events:none;z-index:0"></div>

  <form onsubmit="return false" id="admissionForm" style="position:relative;z-index:1">
    <!-- Photo Upload -->
    <div class="card" style="margin-bottom:16px">
      <div class="card-hdr"><div><div class="card-title">📷 Student Photo</div><div class="card-sub">Upload student passport-size photo</div></div></div>
      <div style="display:flex;align-items:center;gap:24px;flex-wrap:wrap">
        <div id="photoPreviewWrap" style="width:110px;height:130px;border-radius:14px;border:2px dashed var(--gray-300);overflow:hidden;flex-shrink:0;display:flex;align-items:center;justify-content:center;background:var(--gray-50);cursor:pointer" onclick="document.getElementById('admPhoto').click()">
          <div id="photoPlaceholder" style="text-align:center;color:var(--gray-400)"><div style="font-size:32px">📷</div><div style="font-size:10px;margin-top:4px">Click to upload</div></div>
          <img id="photoPreview" src="" style="display:none;width:100%;height:100%;object-fit:cover">
        </div>
        <div>
          <input type="file" id="admPhoto" accept="image/*" style="display:none" onchange="previewAdmPhoto(this)">
          <button type="button" class="btn btn-outline" onclick="document.getElementById('admPhoto').click()">📁 Choose Photo</button>
          <div style="font-size:11px;color:var(--gray-400);margin-top:6px">JPG, PNG, JPEG • Max 5MB<br>Passport-size preferred (3.5 × 4.5 cm)</div>
          <button type="button" class="btn btn-danger btn-xs" style="margin-top:8px" onclick="clearAdmPhoto()">✕ Remove</button>
        </div>
      </div>
    </div>

    <!-- Section 1: Personal Information -->
    <div class="card" style="margin-bottom:16px">
      <div class="card-hdr"><div><div class="card-title">👤 Personal Information</div><div class="card-sub">Basic personal details of the student</div></div></div>
      <div class="form-grid">
        <div class="fg"><label>Full Name *</label><input class="fc" id="ad_name" placeholder="Student's full name" required></div>
        <div class="fg"><label>Date of Birth *</label><input class="fc" id="ad_dob" type="date" required></div>
        <div class="fg"><label>Gender *</label>
          <select class="fc" id="ad_gender">
            <option value="">Select Gender</option>
            <option>Male</option><option>Female</option><option>Other</option>
          </select>
        </div>
        <div class="fg"><label>Blood Group</label>
          <select class="fc" id="ad_blood">
            <option value="">Select</option>
            <option>A+</option><option>A-</option><option>B+</option><option>B-</option><option>O+</option><option>O-</option><option>AB+</option><option>AB-</option>
          </select>
        </div>
        <div class="fg"><label>Mobile Number *</label><input class="fc" id="ad_phone" placeholder="Student mobile number" type="tel"></div>
        <div class="fg"><label>Email Address</label><input class="fc" id="ad_email" placeholder="student@email.com" type="email"></div>
        <div class="fg"><label>Religion</label><input class="fc" id="ad_religion" placeholder="Religion"></div>
        <div class="fg"><label>Category</label>
          <select class="fc" id="ad_category">
            <option value="">Select</option><option>General</option><option>OBC</option><option>SC</option><option>ST</option><option>EWS</option>
          </select>
        </div>
        <div class="fg"><label>Nationality</label><input class="fc" id="ad_nationality" value="Indian" placeholder="Nationality"></div>
        <div class="fg"><label>Aadhar Number</label><input class="fc" id="ad_aadhar" placeholder="XXXX XXXX XXXX" maxlength="14"></div>
        <div class="fg form-full"><label>Permanent Address *</label><textarea class="fc" id="ad_address" placeholder="House no., Street, City, State, PIN Code" style="min-height:70px"></textarea></div>
        <div class="fg form-full"><label>Current Address (if different)</label><textarea class="fc" id="ad_curr_address" placeholder="Leave blank if same as permanent address" style="min-height:70px"></textarea></div>
      </div>
    </div>

    <!-- Section 2: Parent / Guardian -->
    <div class="card" style="margin-bottom:16px">
      <div class="card-hdr"><div><div class="card-title">👨‍👩‍👧 Parent / Guardian Details</div></div></div>
      <div class="form-grid">
        <div class="fg"><label>Father's Name</label><input class="fc" id="ad_father" placeholder="Father's full name"></div>
        <div class="fg"><label>Father's Occupation</label><input class="fc" id="ad_father_occ" placeholder="Occupation"></div>
        <div class="fg"><label>Mother's Name</label><input class="fc" id="ad_mother" placeholder="Mother's full name"></div>
        <div class="fg"><label>Mother's Occupation</label><input class="fc" id="ad_mother_occ" placeholder="Occupation"></div>
        <div class="fg"><label>Guardian Name (if applicable)</label><input class="fc" id="ad_guardian" placeholder="Guardian name"></div>
        <div class="fg"><label>Relationship</label><input class="fc" id="ad_guardian_rel" placeholder="e.g. Uncle, Grandparent"></div>
        <div class="fg"><label>Parent/Guardian Phone *</label><input class="fc" id="ad_parent_phone" placeholder="Emergency contact number"></div>
        <div class="fg"><label>Parent Email</label><input class="fc" id="ad_parent_email" placeholder="parent@email.com"></div>
        <div class="fg"><label>Annual Family Income</label>
          <select class="fc" id="ad_income">
            <option value="">Select</option><option>Below ₹1 Lakh</option><option>₹1-2.5 Lakh</option><option>₹2.5-5 Lakh</option><option>₹5-10 Lakh</option><option>Above ₹10 Lakh</option>
          </select>
        </div>
      </div>
    </div>

    <!-- Section 3: Academic History -->
    <div class="card" style="margin-bottom:16px">
      <div class="card-hdr"><div><div class="card-title">🎓 Academic History</div><div class="card-sub">Previous educational background</div></div></div>
      <div class="form-grid">
        <div class="fg"><label>Previous School Name</label><input class="fc" id="ad_prev_school" placeholder="Name of last attended school"></div>
        <div class="fg"><label>Previous School Board</label>
          <select class="fc" id="ad_board">
            <option value="">Select Board</option><option>GSEB</option><option>CBSE</option><option>ICSE</option><option>IB</option><option>State Board</option><option>Other</option>
          </select>
        </div>
        <div class="fg"><label>Last Class Passed</label><input class="fc" id="ad_last_class" placeholder="e.g. Standard 9, Class X"></div>
        <div class="fg"><label>Year of Passing</label><input class="fc" id="ad_pass_year" type="number" placeholder="2024" min="1990" max="2030"></div>
        <div class="fg"><label>Marks / Percentage Obtained</label><input class="fc" id="ad_marks" placeholder="e.g. 78%, 450/500, 8.5 CGPA"></div>
        <div class="fg"><label>Grade / Division</label>
          <select class="fc" id="ad_division">
            <option value="">Select</option><option>Distinction (75%+)</option><option>First Division (60-75%)</option><option>Second Division (45-60%)</option><option>Third Division (33-45%)</option><option>Pass (33%+)</option>
          </select>
        </div>
        <div class="fg form-full"><label>Extra-curricular Activities / Achievements</label><textarea class="fc" id="ad_achievements" placeholder="Sports, cultural activities, awards, etc."></textarea></div>
        <div class="fg"><label>Transfer Certificate (TC) Number</label><input class="fc" id="ad_tc" placeholder="TC No. (if applicable)"></div>
        <div class="fg"><label>TC Date</label><input class="fc" id="ad_tc_date" type="date"></div>
      </div>

      <!-- Document Upload -->
      <div style="margin-top:16px;padding-top:14px;border-top:1px solid var(--gray-100)">
        <div style="font-size:12px;font-weight:700;color:var(--navy);margin-bottom:10px">📎 Upload Qualification Documents</div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px">
          ${['Marksheet / Report Card', 'Transfer Certificate', 'Birth Certificate', 'Aadhar Card'].map((doc, i) => `
          <label class="upload-zone" for="admdoc_${i}" style="padding:12px;text-align:left;display:flex;align-items:center;gap:10px">
            <input type="file" id="admdoc_${i}" onchange="handleDocUpload(${i}, this)" accept=".pdf,.jpg,.jpeg,.png">
            <span style="font-size:20px">📄</span>
            <div>
              <div style="font-size:12px;font-weight:600;color:var(--gray-700)">${doc}</div>
              <div id="admdoc_name_${i}" style="font-size:10px;color:var(--gray-400)">Click to upload</div>
            </div>
          </label>`).join('')}
        </div>
      </div>
    </div>

    <!-- Section 4: Admission Details -->
    <div class="card" style="margin-bottom:16px">
      <div class="card-hdr"><div><div class="card-title">📚 Admission Details</div><div class="card-sub">New admission configuration</div></div></div>
      <div class="form-grid">
        <div class="fg"><label>Student ID *</label><input class="fc" id="ad_id" placeholder="Leave blank for auto-generate"></div>
        <div class="fg"><label>GR Number</label><input class="fc" id="ad_grno" placeholder="Auto-generated if blank" style="font-family:'JetBrains Mono',monospace"></div>
        <div class="fg"><label>Login Password *</label><input class="fc" id="ad_pass" placeholder="Set login password" required></div>
        <div class="fg"><label>Desired Class / Standard *</label>
          <select class="fc" id="ad_class">
            <option value="">Select Class</option>
            ${classOptions}
            <option value="__custom__">Enter manually...</option>
          </select>
        </div>
        <div class="fg" id="ad_class_custom_wrap" style="display:none"><label>Enter Class Manually</label><input class="fc" id="ad_class_custom" placeholder="e.g. 10A, Class 5"></div>
        <div class="fg"><label>Department / Stream</label>
          <select class="fc" id="ad_dept">
            <option value="">Select (if applicable)</option>
            <option>Science</option><option>Commerce</option><option>Arts</option><option>General</option><option>Vocational</option>
          </select>
        </div>
        <div class="fg"><label>Roll Number</label><input class="fc" id="ad_roll" placeholder="Assign roll number"></div>
        <div class="fg"><label>Admission Date</label><input class="fc" id="ad_date" type="date" value="${new Date().toISOString().slice(0,10)}"></div>
        <div class="fg"><label>Transport Required?</label>
          <select class="fc" id="ad_transport">
            <option value="no">No</option><option value="yes">Yes</option>
          </select>
        </div>
        <div class="fg"><label>Hostel Required?</label>
          <select class="fc" id="ad_hostel">
            <option value="no">No</option><option value="yes">Yes</option>
          </select>
        </div>
        <div class="fg form-full"><label>Additional Notes / Special Requirements</label><textarea class="fc" id="ad_notes" placeholder="Any medical conditions, special needs, notes..."></textarea></div>
      </div>
    </div>

    <!-- Submit -->
    <div class="card" style="border:2px solid rgba(37,99,235,.15);background:rgba(37,99,235,.02)">
      <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:10px">
        <div>
          <div style="font-weight:600;font-size:14px;color:var(--navy)">Ready to submit admission?</div>
          <div style="font-size:12px;color:var(--gray-400);margin-top:3px">Student will be added to the system and can login with their ID & password</div>
        </div>
        <div style="display:flex;gap:8px;flex-wrap:wrap">
          <button type="button" class="btn btn-outline" onclick="resetAdmissionForm()">🔄 Reset Form</button>
          <button type="button" class="btn btn-success" style="padding:11px 24px;font-size:14px" onclick="submitAdmissionForm()">✅ Submit Admission</button>
        </div>
      </div>
    </div>
  </form>
  </div>`; <!-- end watermark wrapper -->

  // Handle class dropdown
  document.getElementById('ad_class').addEventListener('change', function() {
    const wrap = document.getElementById('ad_class_custom_wrap');
    wrap.style.display = this.value === '__custom__' ? 'block' : 'none';
  });
}

// Document upload handler
const admDocuments = {};
window.handleDocUpload = function(idx, input) {
  const file = input.files[0]; if (!file) return;
  const label = document.getElementById(`admdoc_name_${idx}`);
  label.textContent = file.name;
  label.style.color = 'var(--blue)';
  const reader = new FileReader();
  reader.onload = e => { admDocuments[idx] = { name: file.name, data: e.target.result.split(',')[1], type: file.type }; };
  reader.readAsDataURL(file);
};

let admPhotoData = null;
window.previewAdmPhoto = function(input) {
  const file = input.files[0]; if (!file) return;
  if (file.size > 5 * 1024 * 1024) { alert('Photo size must be less than 5MB'); return; }
  const reader = new FileReader();
  reader.onload = e => {
    admPhotoData = e.target.result; // full data URL
    document.getElementById('photoPreview').src = admPhotoData;
    document.getElementById('photoPreview').style.display = 'block';
    document.getElementById('photoPlaceholder').style.display = 'none';
  };
  reader.readAsDataURL(file);
};

window.clearAdmPhoto = function() {
  admPhotoData = null;
  document.getElementById('admPhoto').value = '';
  document.getElementById('photoPreview').style.display = 'none';
  document.getElementById('photoPlaceholder').style.display = 'block';
};

window.resetAdmissionForm = function() {
  if (!confirm('Reset the form? All entered data will be lost.')) return;
  admPhotoData = null;
  Object.keys(admDocuments).forEach(k => delete admDocuments[k]);
  renderAdminAdmission();
};

window.submitAdmissionForm = function() {
  const name = document.getElementById('ad_name').value.trim();
  const dob = document.getElementById('ad_dob').value;
  const phone = document.getElementById('ad_phone').value.trim();
  const pass = document.getElementById('ad_pass').value.trim();
  const address = document.getElementById('ad_address').value.trim();
  const gender = document.getElementById('ad_gender').value;

  const clsSelect = document.getElementById('ad_class').value;
  const cls = clsSelect === '__custom__' ? document.getElementById('ad_class_custom').value.trim() : clsSelect;

  if (!name) { alert('Full name is required'); document.getElementById('ad_name').focus(); return; }
  if (!dob) { alert('Date of birth is required'); return; }
  if (!pass) { alert('Login password is required'); return; }
  if (!cls) { alert('Please select a class'); return; }

  let id = document.getElementById('ad_id').value.trim() || genId('student');
  if (store.students.find(s => s.id === id)) { alert(`Student ID "${id}" already exists. Please enter a different ID.`); return; }

  const grNoInput = document.getElementById('ad_grno').value.trim();
  const grNo = grNoInput || ('GR-' + String(store.grNoCounter || 1).padStart(4,'0'));
  store.grNoCounter = (store.grNoCounter || 1) + 1;

  const student = {
    id, pass, name, grNo,
    dob, gender,
    blood: document.getElementById('ad_blood').value,
    phone, email: document.getElementById('ad_email').value,
    religion: document.getElementById('ad_religion').value,
    category: document.getElementById('ad_category').value,
    nationality: document.getElementById('ad_nationality').value,
    aadhar: document.getElementById('ad_aadhar').value,
    address, currAddress: document.getElementById('ad_curr_address').value,
    father: document.getElementById('ad_father').value,
    fatherOcc: document.getElementById('ad_father_occ').value,
    mother: document.getElementById('ad_mother').value,
    motherOcc: document.getElementById('ad_mother_occ').value,
    guardian: document.getElementById('ad_guardian').value,
    guardianRel: document.getElementById('ad_guardian_rel').value,
    parent: document.getElementById('ad_father').value || document.getElementById('ad_guardian').value,
    parentPhone: document.getElementById('ad_parent_phone').value,
    parentEmail: document.getElementById('ad_parent_email').value,
    income: document.getElementById('ad_income').value,
    prevSchool: document.getElementById('ad_prev_school').value,
    prevBoard: document.getElementById('ad_board').value,
    lastClass: document.getElementById('ad_last_class').value,
    passYear: document.getElementById('ad_pass_year').value,
    prevMarks: document.getElementById('ad_marks').value,
    division: document.getElementById('ad_division').value,
    achievements: document.getElementById('ad_achievements').value,
    tc: document.getElementById('ad_tc').value,
    tcDate: document.getElementById('ad_tc_date').value,
    class: cls,
    dept: document.getElementById('ad_dept').value,
    roll: document.getElementById('ad_roll').value,
    admDate: document.getElementById('ad_date').value,
    transport: document.getElementById('ad_transport').value,
    hostel: document.getElementById('ad_hostel').value,
    notes: document.getElementById('ad_notes').value,
    joined: document.getElementById('ad_date').value || new Date().toISOString().slice(0,10),
    status: 'active',
    feeStatus: 'pending',
    photo: admPhotoData || null,
    documents: {...admDocuments}
  };

  store.students.push(student);
  store.attendance[id] = {};
  admPhotoData = null;
  Object.keys(admDocuments).forEach(k => delete admDocuments[k]);
  saveStore();

  // Show success modal
  openMo('✅ Admission Successful!', `
    <div style="text-align:center;padding:10px 0">
      ${student.photo ? `<img src="${student.photo}" style="width:90px;height:110px;border-radius:14px;object-fit:cover;border:3px solid var(--emerald);margin-bottom:14px">` : `<div style="width:70px;height:70px;border-radius:50%;background:linear-gradient(135deg,var(--blue),var(--amber));display:flex;align-items:center;justify-content:center;color:white;font-size:28px;font-weight:700;margin:0 auto 14px">${name[0]}</div>`}
      <div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy);margin-bottom:4px">${name}</div>
      <div style="font-size:13px;color:var(--gray-500);margin-bottom:20px">Admission successful · Class ${cls}</div>
      <div style="background:var(--gray-50);border-radius:12px;padding:16px;text-align:left;margin-bottom:16px">
        <div class="ir"><span class="ir-label">Student ID</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace;color:var(--blue);font-size:16px">${id}</span></div>
        <div class="ir"><span class="ir-label">GR Number</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace;color:var(--violet)">${grNo}</span></div>
        <div class="ir"><span class="ir-label">Password</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace">${pass}</span></div>
        <div class="ir"><span class="ir-label">Class</span><span class="ir-val">${cls}</span></div>
        <div class="ir"><span class="ir-label">Admission Date</span><span class="ir-val">${fmtDate(student.admDate)}</span></div>
      </div>
      <div style="font-size:12px;color:var(--gray-400)">Student can now login with ID: <b>${id}</b> and their password</div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="closeMo();renderAdminAdmission()">+ New Admission</button>
      <button class="btn btn-violet" onclick="printAdmissionForm('${id}')">🖨️ Print Admission Form</button>
      <button class="btn btn-outline" onclick="closeMo();loadSection('students')">View All Students</button>
    </div>
  `);
  notify(`${name} admitted successfully!`);
};

// ═══════════════════════════════════════════════
//  UPDATED STUDENT PROFILE — with photo
// ═══════════════════════════════════════════════
function renderStudentProfileFull() {
  const s = store.students.find(x => x.id === currentUser.id) || currentUser;
  const {total, present, pct} = attStats(s.id);
  const myRes = store.examResults.filter(r => r.studentId === s.id);
  const myFees = store.fees.filter(f => f.studentId === s.id);
  const totalPaid = myFees.reduce((a, f) => a + f.paid, 0);
  const myBooks = store.issuedBooks.filter(b => b.studentId === s.id && b.status === 'issued');
  const myHW = store.homework.filter(h => h.class === s.class);
  const pendingHW = myHW.filter(h => !(store.hwSubmissions[`${h.id}_${s.id}`]?.status) || store.hwSubmissions[`${h.id}_${s.id}`].status === 'pending').length;

  area().innerHTML = `
  <!-- Profile Hero -->
  <div style="background:linear-gradient(135deg,var(--navy),var(--navy2));border-radius:18px;padding:28px;color:white;margin-bottom:20px;position:relative;overflow:hidden">
    <div style="position:absolute;top:-30px;right:-30px;width:160px;height:160px;border-radius:50%;background:rgba(255,255,255,.03)"></div>
    <div style="position:absolute;bottom:-50px;left:20%;width:200px;height:200px;border-radius:50%;background:rgba(255,255,255,.02)"></div>
    <div style="display:flex;align-items:center;gap:22px;flex-wrap:wrap;position:relative">
      <!-- Photo -->
      <div style="flex-shrink:0">
        ${s.photo ? `<img src="${s.photo}" style="width:100px;height:120px;border-radius:16px;object-fit:cover;border:3px solid rgba(255,255,255,.3);box-shadow:0 8px 24px rgba(0,0,0,.3)">` :
          `<div style="width:90px;height:90px;border-radius:18px;background:linear-gradient(135deg,var(--blue),var(--amber));display:flex;align-items:center;justify-content:center;font-size:34px;font-weight:700;border:3px solid rgba(255,255,255,.2)">${s.name[0]}</div>`}
      </div>
      <div style="flex:1;min-width:200px">
        <div style="font-family:'Playfair Display',serif;font-size:24px;margin-bottom:4px">${s.name}</div>
        <div style="color:rgba(255,255,255,.5);font-size:12.5px;margin-bottom:12px">${s.email || s.phone || 'S.V. Vidhyalay'}</div>
        <div style="display:flex;flex-wrap:wrap;gap:6px">
          <span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px">🎓 Class ${s.class}</span>
          <span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px">📋 Roll #${s.roll || '—'}</span>
          <span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px;font-family:'JetBrains Mono',monospace">ID: ${s.id}</span>
          ${s.dept ? `<span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px">📚 ${s.dept}</span>` : ''}
          <span style="background:${s.feeStatus==='paid'?'rgba(16,185,129,.25)':'rgba(244,63,94,.25)'};border:1px solid ${s.feeStatus==='paid'?'rgba(16,185,129,.4)':'rgba(244,63,94,.4)'};border-radius:6px;padding:3px 10px;font-size:11px">${s.feeStatus==='paid'?'✅ Fee Paid':'⚠️ Fee Pending'}</span>
        </div>
      </div>
      <div style="text-align:center;background:rgba(255,255,255,.08);border-radius:14px;padding:16px 20px;flex-shrink:0">
        <div style="font-size:36px;font-weight:700">${pct}%</div>
        <div style="font-size:11px;opacity:.5">Attendance</div>
        <div style="margin-top:8px;height:5px;border-radius:3px;background:rgba(255,255,255,.15);overflow:hidden"><div style="width:${pct}%;height:100%;background:${pct>=75?'var(--emerald)':'var(--rose)'}"></div></div>
      </div>
    </div>
  </div>

  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">📋</div><div class="stat-info"><h3>${present}/${total}</h3><p>Days Present</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">📝</div><div class="stat-info"><h3>${myRes.length ? Math.round(myRes.reduce((a,r)=>a+r.marks,0)/myRes.length) : '—'}</h3><p>Avg Score</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">📖</div><div class="stat-info"><h3>${pendingHW}</h3><p>Pending HW</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">💰</div><div class="stat-info"><h3>₹${totalPaid.toLocaleString()}</h3><p>Fees Paid</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(6,182,212,.1)">📚</div><div class="stat-info"><h3>${myBooks.length}</h3><p>Books Issued</p></div></div>
  </div>

  <div class="two-col">
    <!-- Personal Info -->
    <div class="card">
      <div class="card-hdr"><div class="card-title">👤 Personal Information</div></div>
      <div class="ir"><span class="ir-label">Full Name</span><span class="ir-val">${s.name}</span></div>
      <div class="ir"><span class="ir-label">Student ID</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace;color:var(--blue)">${s.id}</span></div>
      <div class="ir"><span class="ir-label">Date of Birth</span><span class="ir-val">${fmtDate(s.dob)}</span></div>
      <div class="ir"><span class="ir-label">Gender</span><span class="ir-val">${s.gender || '—'}</span></div>
      <div class="ir"><span class="ir-label">Blood Group</span><span class="ir-val">${s.blood || '—'}</span></div>
      <div class="ir"><span class="ir-label">Phone</span><span class="ir-val">${s.phone || '—'}</span></div>
      <div class="ir"><span class="ir-label">Email</span><span class="ir-val">${s.email || '—'}</span></div>
      <div class="ir"><span class="ir-label">Aadhar No.</span><span class="ir-val">${s.aadhar || '—'}</span></div>
      <div class="ir"><span class="ir-label">Address</span><span class="ir-val" style="max-width:200px;text-align:right;line-height:1.4">${s.address || '—'}</span></div>
      <div class="ir"><span class="ir-label">Category</span><span class="ir-val">${s.category || '—'}</span></div>
      <div class="ir"><span class="ir-label">Religion</span><span class="ir-val">${s.religion || '—'}</span></div>
      <div class="ir"><span class="ir-label">Nationality</span><span class="ir-val">${s.nationality || 'Indian'}</span></div>
    </div>

    <!-- Academic & Family -->
    <div>
      <div class="card" style="margin-bottom:14px">
        <div class="card-hdr"><div class="card-title">👨‍👩‍👧 Family Details</div></div>
        <div class="ir"><span class="ir-label">Father</span><span class="ir-val">${s.father || '—'}</span></div>
        <div class="ir"><span class="ir-label">Mother</span><span class="ir-val">${s.mother || '—'}</span></div>
        <div class="ir"><span class="ir-label">Guardian</span><span class="ir-val">${s.guardian ? `${s.guardian} (${s.guardianRel||'—'})` : '—'}</span></div>
        <div class="ir"><span class="ir-label">Parent Phone</span><span class="ir-val">${s.parentPhone || '—'}</span></div>
        <div class="ir"><span class="ir-label">Parent Email</span><span class="ir-val">${s.parentEmail || '—'}</span></div>
      </div>
      <div class="card">
        <div class="card-hdr"><div class="card-title">🎓 Academic Details</div></div>
        <div class="ir"><span class="ir-label">Class</span><span class="ir-val">${s.class}</span></div>
        <div class="ir"><span class="ir-label">Department</span><span class="ir-val">${s.dept || '—'}</span></div>
        <div class="ir"><span class="ir-label">Roll No.</span><span class="ir-val">${s.roll || '—'}</span></div>
        <div class="ir"><span class="ir-label">Admission Date</span><span class="ir-val">${fmtDate(s.admDate || s.joined)}</span></div>
        <div class="ir"><span class="ir-label">Previous School</span><span class="ir-val">${s.prevSchool || '—'}</span></div>
        <div class="ir"><span class="ir-label">Previous Board</span><span class="ir-val">${s.prevBoard || '—'}</span></div>
        <div class="ir"><span class="ir-label">Last Class Passed</span><span class="ir-val">${s.lastClass || '—'}</span></div>
        <div class="ir"><span class="ir-label">Previous Marks</span><span class="ir-val">${s.prevMarks || '—'} ${s.division ? `(${s.division.split(' ')[0]})` : ''}</span></div>
        ${s.transport === 'yes' ? `<div class="ir"><span class="ir-label">Transport</span><span class="badge b-blue">🚌 Yes</span></div>` : ''}
        ${s.hostel === 'yes' ? `<div class="ir"><span class="ir-label">Hostel</span><span class="badge b-violet">🏠 Yes</span></div>` : ''}
      </div>
    </div>
  </div>

  <!-- Exam Results -->
  ${myRes.length ? `
  <div class="card">
    <div class="card-hdr"><div class="card-title">📊 Exam Results</div></div>
    <div class="tbl-wrap"><table>
      <thead><tr><th>Subject</th><th>Exam Type</th><th>Date</th><th>Marks</th><th>Grade</th><th>%</th></tr></thead>
      <tbody>${myRes.map(r=>{const ex=store.exams.find(e=>e.id===r.examId);const p=ex?Math.round((r.marks/ex.maxMarks)*100):0;return`<tr><td><b>${ex?.subject||r.examId}</b></td><td><span class="badge b-violet">${ex?.type||'—'}</span></td><td style="font-size:12px">${fmtDate(ex?.date)}</td><td><b>${r.marks}</b>/${ex?.maxMarks||100}</td><td><span class="badge ${gradeClass(r.grade).replace('g','b-').replace('A','green').replace('B','blue').replace('C','amber').replace('D','red').replace('F','gray')}">${r.grade}</span></td><td><b style="color:${p>=75?'var(--emerald)':'var(--rose)'}">${p}%</b></td></tr>`;}).join('')}
      </tbody>
    </table></div>
  </div>` : ''}
  <!-- Announcements widget for student -->
  <div class="card">
    <div class="card-hdr">
      <div><div class="card-title">📢 Announcements & Events</div><div class="card-sub">${activeAnns().length} active notices</div></div>
      <button class="btn btn-outline btn-sm" onclick="loadSection('announcements')">View All</button>
    </div>
    ${annWidgetHTML(3)}
  </div>`;
}

// ═══════════════════════════════════════════════
//  UPDATED TEACHER DASHBOARD — with photo + info
// ═══════════════════════════════════════════════
function renderTeacherProfileFull() {
  const t = store.teachers.find(x => x.id === currentUser.id) || currentUser;
  const myStudents = store.students.filter(s => s.class === t.class);
  const myExams = store.exams.filter(e => e.teacher === t.id);
  const myHW = store.homework.filter(h => h.assignedBy === t.id);
  const pendingReviews = Object.keys(store.hwSubmissions).filter(k => {
    const [hwId] = k.split('_');
    const hw = store.homework.find(h => h.id === hwId);
    return hw?.assignedBy === t.id && store.hwSubmissions[k].status === 'submitted';
  }).length;

  area().innerHTML = `
  <!-- Teacher Profile Hero -->
  <div style="background:linear-gradient(135deg,var(--navy),var(--navy2));border-radius:18px;padding:28px;color:white;margin-bottom:20px;position:relative;overflow:hidden">
    <div style="position:absolute;top:-30px;right:-30px;width:160px;height:160px;border-radius:50%;background:rgba(255,255,255,.03)"></div>
    <div style="display:flex;align-items:center;gap:22px;flex-wrap:wrap;position:relative">
      <div style="flex-shrink:0">
        ${t.photo ? `<img src="${t.photo}" style="width:90px;height:110px;border-radius:16px;object-fit:cover;border:3px solid rgba(255,255,255,.3)">` :
          `<div style="width:80px;height:80px;border-radius:18px;background:linear-gradient(135deg,var(--emerald),var(--blue));display:flex;align-items:center;justify-content:center;font-size:32px;font-weight:700;border:3px solid rgba(255,255,255,.2)">${t.name[0]}</div>`}
      </div>
      <div style="flex:1">
        <div style="font-family:'Playfair Display',serif;font-size:24px;margin-bottom:4px">${t.name}</div>
        <div style="color:rgba(255,255,255,.5);font-size:12.5px;margin-bottom:12px">${t.email || t.phone || 'Teacher'}</div>
        <div style="display:flex;flex-wrap:wrap;gap:6px">
          <span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px">📚 ${t.subject || 'Teacher'}</span>
          <span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px">🏫 Class ${t.class || '—'}</span>
          <span style="background:rgba(255,255,255,.1);border:1px solid rgba(255,255,255,.15);border-radius:6px;padding:3px 10px;font-size:11px;font-family:'JetBrains Mono',monospace">ID: ${t.id}</span>
        </div>
      </div>
      <div style="text-align:center;background:rgba(255,255,255,.08);border-radius:14px;padding:16px 20px;flex-shrink:0">
        <div style="font-size:36px;font-weight:700">${myStudents.length}</div>
        <div style="font-size:11px;opacity:.5">Students</div>
      </div>
    </div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">👨‍🎓</div><div class="stat-info"><h3>${myStudents.length}</h3><p>My Students</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">📝</div><div class="stat-info"><h3>${myExams.length}</h3><p>Exams</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">📖</div><div class="stat-info"><h3>${myHW.length}</h3><p>Assignments</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">📋</div><div class="stat-info"><h3>${pendingReviews}</h3><p>Pending Reviews</p></div></div>
  </div>
  <div class="two-col">
    <div class="card">
      <div class="card-hdr"><div class="card-title">👤 Personal Details</div></div>
      <div class="ir"><span class="ir-label">Teacher ID</span><span class="ir-val" style="font-family:'JetBrains Mono',monospace;color:var(--emerald)">${t.id}</span></div>
      <div class="ir"><span class="ir-label">Subject</span><span class="ir-val">${t.subject || '—'}</span></div>
      <div class="ir"><span class="ir-label">Assigned Class</span><span class="ir-val">${t.class || '—'}</span></div>
      <div class="ir"><span class="ir-label">Phone</span><span class="ir-val">${t.phone || '—'}</span></div>
      <div class="ir"><span class="ir-label">Email</span><span class="ir-val">${t.email || '—'}</span></div>
      <div class="ir"><span class="ir-label">Joined</span><span class="ir-val">${fmtDate(t.joined)}</span></div>
    </div>
    <div class="card">
      <div class="card-hdr"><div class="card-title">📋 Pending HW Reviews</div><button class="btn btn-outline btn-sm" onclick="loadSection('homework')">Manage</button></div>
      ${pendingReviews === 0 ? `<div class="empty"><div class="ei">✅</div><p>All caught up!</p></div>` : `
      ${Object.keys(store.hwSubmissions).filter(k=>{const[hwId]=k.split('_');const hw=store.homework.find(h=>h.id===hwId);return hw?.assignedBy===t.id&&store.hwSubmissions[k].status==='submitted';}).slice(0,6).map(k=>{
        const[hwId,sid]=k.split('_');const hw=store.homework.find(h=>h.id===hwId);const st=store.students.find(x=>x.id===sid);const sub=store.hwSubmissions[k];
        return`<div class="ir"><div><div style="font-weight:600;font-size:12.5px">${hw?.title||hwId}</div><div style="font-size:11px;color:var(--gray-400)">${st?.name||sid} · ${sub.fileName||'No file'}</div></div><div style="display:flex;gap:4px"><button class="btn btn-success btn-xs" onclick="quickAccept('${k}')">✅</button><button class="btn btn-danger btn-xs" onclick="quickReject('${k}')">❌</button></div></div>`;
      }).join('')}`}
    </div>
  </div>`;
}


// ═══════════════════════════════════════════════
//  ANNOUNCEMENTS HELPERS
// ═══════════════════════════════════════════════
const ANN_TYPES = [
  {id:'general',  label:'📢 General',    color:'var(--gray-500)',  bg:'rgba(100,116,139,.06)', border:'var(--gray-300)'},
  {id:'event',    label:'🎉 Event',      color:'var(--blue)',       bg:'rgba(37,99,235,.07)',   border:'var(--blue)'},
  {id:'holiday',  label:'🌿 Holiday',    color:'var(--emerald)',    bg:'rgba(16,185,129,.07)',  border:'var(--emerald)'},
  {id:'exam',     label:'📝 Exam',       color:'var(--violet)',     bg:'rgba(139,92,246,.07)',  border:'var(--violet)'},
  {id:'fee',      label:'💰 Fee',        color:'var(--amber)',      bg:'rgba(245,158,11,.08)',  border:'var(--amber)'},
  {id:'urgent',   label:'🚨 Urgent',     color:'var(--rose)',       bg:'rgba(244,63,94,.07)',   border:'var(--rose)'},
];
const ANN_AUDIENCE = ['All','Students Only','Teachers Only'];

function getAnnType(id){ return ANN_TYPES.find(t=>t.id===id)||ANN_TYPES[0]; }

function annVisible(ann){
  if(ann.audience==='Students Only' && currentUser.role==='teacher') return false;
  if(ann.audience==='Teachers Only' && currentUser.role==='student') return false;
  return true;
}

function activeAnns(){
  const now=new Date();
  return (store.announcements||[])
    .filter(a=>annVisible(a))
    .filter(a=>!a.expiresAt||new Date(a.expiresAt)>=now)
    .sort((a,b)=>{if(a.pinned&&!b.pinned)return-1;if(!a.pinned&&b.pinned)return 1;return new Date(b.createdAt)-new Date(a.createdAt);});
}

function annCardHTML(ann, adminMode=false){
  const at=getAnnType(ann.type);
  const isUpcoming = ann.eventDate && new Date(ann.eventDate)>=new Date();
  const daysLeft = ann.eventDate ? Math.ceil((new Date(ann.eventDate)-new Date())/(1000*60*60*24)) : null;
  return `
  <div class="ann-card ann-type-${ann.type}" style="background:${at.bg};border-left-color:${at.border}">
    ${ann.pinned?`<div class="ann-pin">📌</div>`:''}
    <div style="display:flex;align-items:flex-start;gap:10px;flex-wrap:wrap;margin-bottom:8px">
      <span class="ann-badge" style="background:${at.bg};color:${at.color};border:1px solid ${at.border}">${at.label}</span>
      ${ann.audience!=='All'?`<span class="ann-badge" style="background:rgba(15,23,41,.08);color:var(--navy)">👥 ${ann.audience}</span>`:''}
      ${ann.pinned?`<span class="ann-badge" style="background:rgba(245,158,11,.12);color:#b45309">📌 Pinned</span>`:''}
      ${daysLeft!==null && daysLeft>=0?`<span class="ann-badge" style="background:${daysLeft<=3?'rgba(244,63,94,.12)':'rgba(37,99,235,.1)'};color:${daysLeft<=3?'var(--rose)':'var(--blue)'}">${daysLeft===0?'📅 Today':daysLeft===1?'📅 Tomorrow':'📅 In '+daysLeft+' days'}</span>`:''}
    </div>
    <div style="font-weight:700;font-size:14px;color:var(--navy);margin-bottom:5px">${ann.title}</div>
    ${ann.eventDate?`<div style="font-size:11.5px;font-weight:600;color:${at.color};margin-bottom:6px">📅 Event Date: ${fmtDate(ann.eventDate)}${ann.eventTime?' · ⏰ '+ann.eventTime:''}</div>`:''}
    ${ann.venue?`<div style="font-size:11.5px;color:var(--gray-500);margin-bottom:6px">📍 Venue: ${ann.venue}</div>`:''}
    <div style="font-size:13px;color:var(--gray-600);line-height:1.65;white-space:pre-wrap">${ann.details}</div>
    <div style="display:flex;align-items:center;justify-content:space-between;margin-top:10px;flex-wrap:wrap;gap:6px">
      <div style="font-size:10.5px;color:var(--gray-400)">Posted by Admin · ${fmtDate(ann.createdAt)}${ann.expiresAt?' · Expires: '+fmtDate(ann.expiresAt):''}</div>
      ${adminMode?`<div style="display:flex;gap:4px">
        <button class="btn btn-warning btn-xs" onclick="editAnn('${ann.id}')">Edit</button>
        <button class="btn btn-xs" style="background:${ann.pinned?'rgba(245,158,11,.15)':'rgba(37,99,235,.1)'};color:${ann.pinned?'#b45309':'var(--blue)'}" onclick="togglePin('${ann.id}')">${ann.pinned?'Unpin':'📌 Pin'}</button>
        <button class="btn btn-danger btn-xs" onclick="deleteAnn('${ann.id}')">Del</button>
      </div>`:''}
    </div>
  </div>`;
}

// ═══════════════════════════════════════════════
//  ADMIN — ANNOUNCEMENTS MANAGEMENT
// ═══════════════════════════════════════════════
function renderAdminAnnouncements(){
  const all=(store.announcements||[]).sort((a,b)=>{if(a.pinned&&!b.pinned)return-1;if(!a.pinned&&b.pinned)return 1;return new Date(b.createdAt)-new Date(a.createdAt);});
  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📢 Announcements & Events</div><div style="font-size:12px;color:var(--gray-400)">${all.length} announcements · Send to students & teachers</div></div>
    <button class="btn btn-primary" style="padding:10px 20px" onclick="showAddAnn()">+ New Announcement</button>
  </div>

  <!-- Summary row -->
  <div class="stat-grid" style="margin-bottom:18px">
    ${ANN_TYPES.map(at=>{
      const cnt=(store.announcements||[]).filter(a=>a.type===at.id).length;
      return`<div class="stat-card" style="border-left:3px solid ${at.border}"><div class="stat-icon" style="background:${at.bg};font-size:18px">${at.label.split(' ')[0]}</div><div class="stat-info"><h3>${cnt}</h3><p>${at.label.split(' ').slice(1).join(' ')}</p></div></div>`;
    }).join('')}
  </div>

  <!-- Filter row -->
  <div class="search-row" style="margin-bottom:16px">
    <div class="si-wrap"><span>🔍</span><input class="si" placeholder="Search announcements..." id="annSearch" oninput="filterAnns()"></div>
    <select class="fc" style="width:140px" id="annTypeFilter" onchange="filterAnns()">
      <option value="">All Types</option>
      ${ANN_TYPES.map(t=>`<option value="${t.id}">${t.label}</option>`).join('')}
    </select>
    <select class="fc" style="width:150px" id="annAudFilter" onchange="filterAnns()">
      <option value="">All Audiences</option>
      ${ANN_AUDIENCE.map(a=>`<option>${a}</option>`).join('')}
    </select>
  </div>

  <div id="annList">
    ${all.length===0?`<div class="card"><div class="empty"><div class="ei">📢</div><p>No announcements yet. Click "+ New Announcement" to get started.</p></div></div>`:
      all.map(a=>annCardHTML(a,true)).join('')}
  </div>`;
}

window.filterAnns=function(){
  const q=document.getElementById('annSearch').value.toLowerCase();
  const type=document.getElementById('annTypeFilter').value;
  const aud=document.getElementById('annAudFilter').value;
  let list=(store.announcements||[]).filter(a=>{
    if(type&&a.type!==type) return false;
    if(aud&&a.audience!==aud) return false;
    if(q&&!a.title.toLowerCase().includes(q)&&!a.details.toLowerCase().includes(q)) return false;
    return true;
  }).sort((a,b)=>{if(a.pinned&&!b.pinned)return-1;if(!a.pinned&&b.pinned)return 1;return new Date(b.createdAt)-new Date(a.createdAt);});
  document.getElementById('annList').innerHTML=list.length?list.map(a=>annCardHTML(a,true)).join(''):`<div class="card"><div class="empty"><div class="ei">🔍</div><p>No matching announcements</p></div></div>`;
};

function showAddAnn(editId){
  const ex=editId?(store.announcements||[]).find(a=>a.id===editId):null;
  const isEdit=!!ex;
  openMo(isEdit?'✏️ Edit Announcement':'📢 New Announcement / Event',`
    <div class="fg"><label>Title *</label><input class="fc" id="an_title" placeholder="Announcement title or event name" value="${ex?.title||''}"></div>
    <div class="form-grid">
      <div class="fg"><label>Type / Category</label>
        <select class="fc" id="an_type">
          ${ANN_TYPES.map(t=>`<option value="${t.id}" ${ex?.type===t.id?'selected':''}>${t.label}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Audience</label>
        <select class="fc" id="an_aud">
          ${ANN_AUDIENCE.map(a=>`<option ${ex?.audience===a?'selected':''}>${a}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Event Date (if applicable)</label><input class="fc" id="an_edate" type="date" value="${ex?.eventDate||''}"></div>
      <div class="fg"><label>Event Time</label><input class="fc" id="an_etime" type="time" value="${ex?.eventTime||''}"></div>
      <div class="fg"><label>Venue / Location</label><input class="fc" id="an_venue" placeholder="Hall, Ground, Online..." value="${ex?.venue||''}"></div>
      <div class="fg"><label>Expires On (auto-hide after)</label><input class="fc" id="an_exp" type="date" value="${ex?.expiresAt||''}"></div>
    </div>
    <div class="fg"><label>Details / Description *</label><textarea class="fc" id="an_details" style="min-height:110px" placeholder="Full details of the announcement or event...">${ex?.details||''}</textarea></div>
    <div style="display:flex;align-items:center;gap:10px;margin-bottom:14px">
      <label style="display:flex;align-items:center;gap:7px;cursor:pointer;font-size:13px;color:var(--gray-700)">
        <input type="checkbox" id="an_pin" ${ex?.pinned?'checked':''} style="width:16px;height:16px;accent-color:var(--amber)">
        📌 Pin this announcement (shows at top)
      </label>
    </div>
    <div style="display:flex;gap:8px">
      <button class="btn btn-primary" onclick="${isEdit?`saveEditAnn('${editId}')`:'saveNewAnn()'}">
        ${isEdit?'✅ Update Announcement':'📢 Publish Announcement'}
      </button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}

window.editAnn=function(id){ showAddAnn(id); };

function saveNewAnn(){
  const title=document.getElementById('an_title').value.trim();
  const details=document.getElementById('an_details').value.trim();
  if(!title||!details){alert('Title and Details are required');return;}
  if(!store.announcements) store.announcements=[];
  const id='ANN'+Date.now();
  store.announcements.push({
    id, title, details,
    type:document.getElementById('an_type').value,
    audience:document.getElementById('an_aud').value,
    eventDate:document.getElementById('an_edate').value,
    eventTime:document.getElementById('an_etime').value,
    venue:document.getElementById('an_venue').value,
    expiresAt:document.getElementById('an_exp').value,
    pinned:document.getElementById('an_pin').checked,
    createdAt:new Date().toISOString().slice(0,10)
  });
  saveStore(); closeMo(); notify('Announcement published! 📢','📢'); renderAdminAnnouncements();
}

window.saveEditAnn=function(id){
  const ann=(store.announcements||[]).find(a=>a.id===id); if(!ann) return;
  ann.title=document.getElementById('an_title').value.trim();
  ann.details=document.getElementById('an_details').value.trim();
  ann.type=document.getElementById('an_type').value;
  ann.audience=document.getElementById('an_aud').value;
  ann.eventDate=document.getElementById('an_edate').value;
  ann.eventTime=document.getElementById('an_etime').value;
  ann.venue=document.getElementById('an_venue').value;
  ann.expiresAt=document.getElementById('an_exp').value;
  ann.pinned=document.getElementById('an_pin').checked;
  saveStore(); closeMo(); notify('Announcement updated!'); renderAdminAnnouncements();
};

window.deleteAnn=function(id){
  if(!confirm('Delete this announcement?')) return;
  store.announcements=(store.announcements||[]).filter(a=>a.id!==id);
  saveStore(); notify('Deleted.','🗑️'); renderAdminAnnouncements();
};

window.togglePin=function(id){
  const ann=(store.announcements||[]).find(a=>a.id===id); if(!ann) return;
  ann.pinned=!ann.pinned;
  saveStore(); notify(ann.pinned?'Pinned to top!':'Unpinned.','📌'); renderAdminAnnouncements();
};

// ═══════════════════════════════════════════════
//  TEACHER & STUDENT — VIEW ANNOUNCEMENTS
// ═══════════════════════════════════════════════
function renderViewAnnouncements(){
  const role=currentUser.role;
  const list=activeAnns();
  const upcoming=list.filter(a=>a.eventDate&&new Date(a.eventDate)>=new Date()).sort((a,b)=>new Date(a.eventDate)-new Date(b.eventDate));
  const pinned=list.filter(a=>a.pinned);
  const rest=list.filter(a=>!a.pinned);

  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📢 Announcements & Events</div><div style="font-size:12px;color:var(--gray-400)">${list.length} active announcement${list.length!==1?'s':''} for you</div></div>
  </div>

  ${upcoming.length?`
  <!-- Upcoming Events Timeline -->
  <div class="card" style="margin-bottom:16px;background:linear-gradient(135deg,var(--navy),var(--navy2));color:white;border:none">
    <div style="font-weight:700;font-size:15px;margin-bottom:14px;opacity:.9">🗓️ Upcoming Events</div>
    <div style="display:flex;gap:0;overflow-x:auto;padding-bottom:4px">
      ${upcoming.slice(0,6).map((a,i)=>{
        const at=getAnnType(a.type);
        const daysLeft=Math.ceil((new Date(a.eventDate)-new Date())/(1000*60*60*24));
        return`<div style="flex-shrink:0;min-width:160px;max-width:180px;margin-right:12px;background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.12);border-radius:12px;padding:14px;position:relative">
          <div style="font-size:22px;margin-bottom:6px">${at.label.split(' ')[0]}</div>
          <div style="font-weight:700;font-size:12.5px;margin-bottom:4px;line-height:1.3">${a.title}</div>
          ${a.venue?`<div style="font-size:10px;opacity:.5;margin-bottom:4px">📍 ${a.venue}</div>`:''}
          <div style="font-size:11px;font-weight:600;color:${daysLeft<=3?'#fca5a5':'#86efac'}">
            📅 ${fmtDate(a.eventDate)}${a.eventTime?' · '+a.eventTime:''}
          </div>
          <div style="margin-top:6px;font-size:10px;background:rgba(255,255,255,.1);border-radius:5px;padding:2px 7px;display:inline-block">
            ${daysLeft===0?'Today!':daysLeft===1?'Tomorrow':'In '+daysLeft+' days'}
          </div>
        </div>`;
      }).join('')}
    </div>
  </div>`:''}

  <!-- Filter -->
  <div class="search-row" style="margin-bottom:14px">
    <div class="si-wrap"><span>🔍</span><input class="si" placeholder="Search announcements..." id="vannSearch" oninput="filterVAnns()"></div>
    <select class="fc" style="width:140px" id="vannType" onchange="filterVAnns()">
      <option value="">All Types</option>
      ${ANN_TYPES.map(t=>`<option value="${t.id}">${t.label}</option>`).join('')}
    </select>
  </div>

  <div id="vannList">
    ${list.length===0
      ?`<div class="card"><div class="empty"><div class="ei">📢</div><p>No announcements yet. Check back later!</p></div></div>`
      :`${pinned.length?`<div style="font-size:11px;font-weight:700;color:var(--navy);text-transform:uppercase;letter-spacing:.6px;margin-bottom:8px">📌 Pinned</div>${pinned.map(a=>annCardHTML(a,false)).join('')}<div style="height:4px;border-bottom:1px dashed var(--gray-200);margin-bottom:14px"></div>`:''}
       ${rest.length?`<div style="font-size:11px;font-weight:700;color:var(--gray-400);text-transform:uppercase;letter-spacing:.6px;margin-bottom:8px">Latest</div>${rest.map(a=>annCardHTML(a,false)).join('')}`:''}
      `}
  </div>`;
}

window.filterVAnns=function(){
  const q=document.getElementById('vannSearch')?.value.toLowerCase()||'';
  const type=document.getElementById('vannType')?.value||'';
  let list=activeAnns().filter(a=>{
    if(type&&a.type!==type) return false;
    if(q&&!a.title.toLowerCase().includes(q)&&!a.details.toLowerCase().includes(q)) return false;
    return true;
  });
  const pinned=list.filter(a=>a.pinned);
  const rest=list.filter(a=>!a.pinned);
  const el=document.getElementById('vannList');
  if(!el) return;
  el.innerHTML=list.length===0
    ?`<div class="card"><div class="empty"><div class="ei">🔍</div><p>No matching announcements</p></div></div>`
    :`${pinned.length?pinned.map(a=>annCardHTML(a,false)).join(''):''}${rest.map(a=>annCardHTML(a,false)).join('')}`;
};

// ═══════════════════════════════════════════════
//  ANNOUNCEMENTS WIDGET (for dashboards)
// ═══════════════════════════════════════════════
function annWidgetHTML(limit=3){
  const list=activeAnns().slice(0,limit);
  if(!list.length) return `<div class="empty" style="padding:20px"><div class="ei" style="font-size:28px">📢</div><p>No announcements</p></div>`;
  return list.map(a=>{
    const at=getAnnType(a.type);
    const daysLeft=a.eventDate?Math.ceil((new Date(a.eventDate)-new Date())/(1000*60*60*24)):null;
    return`<div style="padding:12px;border-radius:10px;background:${at.bg};border-left:3px solid ${at.border};margin-bottom:8px">
      <div style="display:flex;align-items:flex-start;justify-content:space-between;gap:8px;margin-bottom:5px">
        <div style="font-weight:700;font-size:13px;color:var(--navy);line-height:1.3">${a.pinned?'📌 ':''}${a.title}</div>
        <span style="font-size:18px;flex-shrink:0">${at.label.split(' ')[0]}</span>
      </div>
      ${a.eventDate?`<div style="font-size:11px;font-weight:600;color:${at.color};margin-bottom:3px">📅 ${fmtDate(a.eventDate)}${a.eventTime?' · '+a.eventTime:''}${daysLeft!==null&&daysLeft>=0?' ('+( daysLeft===0?'Today':daysLeft===1?'Tomorrow':'In '+daysLeft+' days')+')':''}</div>`:''}
      ${a.venue?`<div style="font-size:11px;color:var(--gray-400);margin-bottom:3px">📍 ${a.venue}</div>`:''}
      <div style="font-size:12px;color:var(--gray-600);line-height:1.5;display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden">${a.details}</div>
    </div>`;
  }).join('');
}


// ═══════════════════════════════════════════════
//  WHATSAPP HELPER
// ═══════════════════════════════════════════════
function sendWhatsApp(phone, message) {
  // Clean phone number - remove spaces, dashes, +
  let p = (phone||'').replace(/[\s\-\(\)]/g,'');
  if (!p) { notify('No phone number available!','⚠️'); return false; }
  // Add country code if missing (default India +91)
  if (!p.startsWith('+') && !p.startsWith('91') && p.length === 10) p = '91' + p;
  p = p.replace(/^\+/,'');
  const encoded = encodeURIComponent(message);
  const url = `https://wa.me/${p}?text=${encoded}`;
  window.open(url, '_blank');
  // Log it
  store.whatsappLog.push({
    id: 'WA'+Date.now(),
    to: phone,
    message,
    sentAt: new Date().toISOString(),
    type: 'manual'
  });
  saveStore();
  return true;
}

function whatsAppFeesMsg(student) {
  const fs = store.feeStructures[student.id] || {};
  const paid = store.fees.filter(f=>f.studentId===student.id&&f.status==='paid').reduce((a,f)=>a+f.paid,0);
  const due = (fs.amount||0) - paid;
  return `🏫 *S.V. Vidhyalay*\n\nDear Parent of *${student.name}*,\n\nThis is a gentle reminder that the following fee is pending:\n\n📋 Term: ${fs.term||'Current Term'}\n💰 Total Fee: ₹${(fs.amount||0).toLocaleString()}\n✅ Paid: ₹${paid.toLocaleString()}\n⏳ *Balance Due: ₹${due.toLocaleString()}*\n${fs.dueDate?`📅 Due Date: ${fmtDate(fs.dueDate)}\n`:''}\nKindly pay at the earliest to avoid any inconvenience.\n\nThank you,\nS.V. Vidhyalay`;
}

function whatsAppAbsentMsg(student, date) {
  return `🏫 *S.V. Vidhyalay*\n\nDear Parent of *${student.name}*,\n\nWe wish to inform you that your ward was *Absent* from school on:\n📅 Date: ${fmtDate(date)||date}\n🏫 Class: ${student.class}\n\nIf this absence was unplanned, please ensure your child attends school regularly. Low attendance may affect academic progress.\n\nFor any queries, please contact the school office.\n\nThank you,\nS.V. Vidhyalay`;
}

// ═══════════════════════════════════════════════
//  ADMIN — WHATSAPP ALERTS
// ═══════════════════════════════════════════════
function renderAdminWhatsApp() {
  const today = new Date().toISOString().slice(0,10);
  let selDate = today;
  let activeTab = 'absent';

  const render = () => {
    // Absent students for selected date
    const absentStudents = store.students.filter(s => (store.attendance[s.id]||{})[selDate] === 'A');
    // Pending fees students
    const feesPending = store.students.filter(s => {
      const fs = store.feeStructures[s.id];
      if (!fs || !fs.amount) return false;
      const paid = store.fees.filter(f=>f.studentId===s.id&&f.status==='paid').reduce((a,f)=>a+f.paid,0);
      return paid < fs.amount;
    });
    const recentLog = (store.whatsappLog||[]).slice().reverse().slice(0,20);

    area().innerHTML = `
    <div style="margin-bottom:18px">
      <div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">💬 WhatsApp Alerts</div>
      <div style="font-size:12px;color:var(--gray-400)">Send automated messages to parents via WhatsApp</div>
    </div>

    <!-- Info banner -->
    <div style="background:linear-gradient(135deg,#25D366,#128C7E);border-radius:14px;padding:18px 22px;color:white;margin-bottom:20px;display:flex;align-items:center;gap:16px;flex-wrap:wrap">
      <div style="font-size:36px">💬</div>
      <div>
        <div style="font-weight:700;font-size:15px;margin-bottom:3px">WhatsApp Business Integration</div>
        <div style="opacity:.8;font-size:12px;line-height:1.5">Clicking "Send WhatsApp" will open WhatsApp Web / App with a pre-filled message for the parent's number. No API required — uses wa.me links.</div>
      </div>
      <div style="margin-left:auto;text-align:right">
        <div style="font-size:22px;font-weight:700">${(store.whatsappLog||[]).length}</div>
        <div style="opacity:.7;font-size:11px">Messages Sent</div>
      </div>
    </div>

    <!-- Tabs -->
    <div style="display:flex;gap:4px;background:var(--gray-100);border-radius:10px;padding:4px;margin-bottom:18px;max-width:560px">
      <button onclick="waTab('absent')" id="waTab_absent" style="flex:1;padding:9px 12px;border:none;border-radius:7px;font-family:'DM Sans',sans-serif;font-size:12.5px;font-weight:600;cursor:pointer;transition:all .2s;${activeTab==='absent'?'background:white;color:var(--navy);box-shadow:0 2px 8px rgba(0,0,0,.08)':'background:transparent;color:var(--gray-500)'}">📅 Absent Alerts</button>
      <button onclick="waTab('fees')" id="waTab_fees" style="flex:1;padding:9px 12px;border:none;border-radius:7px;font-family:'DM Sans',sans-serif;font-size:12.5px;font-weight:600;cursor:pointer;transition:all .2s;${activeTab==='fees'?'background:white;color:var(--navy);box-shadow:0 2px 8px rgba(0,0,0,.08)':'background:transparent;color:var(--gray-500)'}">💳 Fees Reminder</button>
      <button onclick="waTab('groups')" id="waTab_groups" style="flex:1;padding:9px 12px;border:none;border-radius:7px;font-family:'DM Sans',sans-serif;font-size:12.5px;font-weight:600;cursor:pointer;transition:all .2s;${activeTab==='groups'?'background:white;color:var(--navy);box-shadow:0 2px 8px rgba(0,0,0,.08)':'background:transparent;color:var(--gray-500)'}">👥 Group Maker</button>
      <button onclick="waTab('log')" id="waTab_log" style="flex:1;padding:9px 12px;border:none;border-radius:7px;font-family:'DM Sans',sans-serif;font-size:12.5px;font-weight:600;cursor:pointer;transition:all .2s;${activeTab==='log'?'background:white;color:var(--navy);box-shadow:0 2px 8px rgba(0,0,0,.08)':'background:transparent;color:var(--gray-500)'}">📋 Message Log</button>
    </div>

    <div id="waContent">
      ${activeTab==='absent' ? `
      <!-- ABSENT TAB -->
      <div class="card">
        <div class="card-hdr">
          <div><div class="card-title">📅 Absent Students</div><div class="card-sub">Send absence notification to parents</div></div>
          <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
            <input type="date" class="fc" style="width:155px" value="${selDate}" onchange="waDateChange(this.value)">
            ${absentStudents.length>0?`<button class="btn btn-success btn-sm" onclick="waSendAllAbsent('${selDate}')">📤 Send All (${absentStudents.length})</button>`:''}
          </div>
        </div>
        ${absentStudents.length===0?`<div class="empty"><div class="ei">✅</div><p>No absent students on ${fmtDate(selDate)||selDate}</p></div>`:`
        <div class="tbl-wrap"><table>
          <thead><tr><th>Student</th><th>Class</th><th>Parent</th><th>Phone</th><th>Preview</th><th>Action</th></tr></thead>
          <tbody>
            ${absentStudents.map(s=>`<tr>
              <td><div style="font-weight:600">${s.name}</div><div style="font-size:10px;color:var(--gray-400)">${s.id}</div></td>
              <td><span class="badge b-blue">${s.class}</span></td>
              <td style="font-size:12px">${s.parent||s.father||'—'}</td>
              <td style="font-size:12px;font-family:'JetBrains Mono',monospace">${s.parentPhone||s.phone||'—'}</td>
              <td><button class="btn btn-outline btn-xs" onclick="previewWAMsg('absent','${s.id}','${selDate}')">👁 Preview</button></td>
              <td>
                ${(s.parentPhone||s.phone)?`<button style="background:#25D366;color:white;border:none;border-radius:7px;padding:5px 12px;font-size:11.5px;font-weight:600;cursor:pointer;display:flex;align-items:center;gap:4px" onclick="waSendAbsent('${s.id}','${selDate}')">
                  <svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor"><path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413z"/></svg>
                  Send WhatsApp
                </button>`:`<span style="font-size:11px;color:var(--rose)">⚠️ No phone</span>`}
              </td>
            </tr>`).join('')}
          </tbody>
        </table></div>`}
      </div>` : activeTab==='fees' ? `
      <!-- FEES TAB -->
      <div class="card">
        <div class="card-hdr">
          <div><div class="card-title">💳 Fee Reminder</div><div class="card-sub">Send fee reminder to parents of students with pending dues</div></div>
          ${feesPending.length>0?`<button class="btn btn-success btn-sm" onclick="waSendAllFees()">📤 Send All (${feesPending.length})</button>`:''}
        </div>
        ${feesPending.length===0?`<div class="empty"><div class="ei">🎉</div><p>All fees are paid! No reminders needed.</p></div>`:`
        <div class="tbl-wrap"><table>
          <thead><tr><th>Student</th><th>Class</th><th>Due Amount</th><th>Due Date</th><th>Parent Phone</th><th>Preview</th><th>Action</th></tr></thead>
          <tbody>
            ${feesPending.map(s=>{
              const fs=store.feeStructures[s.id]||{};
              const paid=store.fees.filter(f=>f.studentId===s.id&&f.status==='paid').reduce((a,f)=>a+f.paid,0);
              const due=(fs.amount||0)-paid;
              const overdue=fs.dueDate&&new Date(fs.dueDate)<new Date();
              return`<tr>
                <td><div style="font-weight:600">${s.name}</div><div style="font-size:10px;color:var(--gray-400)">${s.id}</div></td>
                <td><span class="badge b-blue">${s.class}</span></td>
                <td><span style="font-weight:700;color:var(--rose)">₹${due.toLocaleString()}</span></td>
                <td><span style="font-size:12px;color:${overdue?'var(--rose)':'var(--gray-700)'}${overdue?';font-weight:700':''}">${fmtDate(fs.dueDate)||'—'}${overdue?' ⚠️':''}</span></td>
                <td style="font-size:12px;font-family:'JetBrains Mono',monospace">${s.parentPhone||s.phone||'—'}</td>
                <td><button class="btn btn-outline btn-xs" onclick="previewWAMsg('fees','${s.id}','')">👁 Preview</button></td>
                <td>
                  ${(s.parentPhone||s.phone)?`<button style="background:#25D366;color:white;border:none;border-radius:7px;padding:5px 12px;font-size:11.5px;font-weight:600;cursor:pointer;display:flex;align-items:center;gap:4px" onclick="waSendFees('${s.id}')">
                    <svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor"><path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347m-5.421 7.403h-.004a9.87 9.87 0 01-5.031-1.378l-.361-.214-3.741.982.998-3.648-.235-.374a9.86 9.86 0 01-1.51-5.26c.001-5.45 4.436-9.884 9.888-9.884 2.64 0 5.122 1.03 6.988 2.898a9.825 9.825 0 012.893 6.994c-.003 5.45-4.437 9.884-9.885 9.884m8.413-18.297A11.815 11.815 0 0012.05 0C5.495 0 .16 5.335.157 11.892c0 2.096.547 4.142 1.588 5.945L.057 24l6.305-1.654a11.882 11.882 0 005.683 1.448h.005c6.554 0 11.89-5.335 11.893-11.893a11.821 11.821 0 00-3.48-8.413z"/></svg>
                    Send WhatsApp
                  </button>`:`<span style="font-size:11px;color:var(--rose)">⚠️ No phone</span>`}
                </td>
              </tr>`;
            }).join('')}
          </tbody>
        </table></div>`}
      </div>` : activeTab==='groups' ? `
      <!-- GROUP MAKER TAB -->
      <div class="card" style="margin-bottom:16px">
        <div class="card-hdr">
          <div><div class="card-title">👥 WhatsApp Group Maker</div><div class="card-sub">Create groups for classes, parents, or teachers</div></div>
          <button class="btn btn-primary btn-sm" onclick="showCreateGroup()">+ Create New Group</button>
        </div>

        ${!(store.whatsappGroups||[]).length ? `
        <div class="empty"><div class="ei">👥</div><p>No groups created yet. Click <b>"+ Create New Group"</b> to start.</p></div>
        ` : `
        <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:14px">
          ${(store.whatsappGroups||[]).map(g=>{
            const memberCount = (g.members||[]).length;
            const typeColors = {students:'var(--blue)',parents:'var(--emerald)',teachers:'var(--violet)',custom:'var(--amber)'};
            const typeBadge = {students:'b-blue',parents:'b-green',teachers:'b-violet',custom:'b-amber'};
            return `<div style="border:1.5px solid var(--gray-200);border-radius:14px;padding:18px;background:white;position:relative;overflow:hidden">
              <div style="position:absolute;top:0;left:0;right:0;height:4px;background:${typeColors[g.type]||'var(--blue)'}"></div>
              <div style="display:flex;align-items:flex-start;justify-content:space-between;gap:8px;margin-top:4px">
                <div style="flex:1">
                  <div style="font-size:18px;margin-bottom:4px">👥</div>
                  <div style="font-weight:800;font-size:15px;color:var(--navy)">${g.name}</div>
                  <div style="margin:6px 0;display:flex;gap:6px;flex-wrap:wrap">
                    <span class="badge ${typeBadge[g.type]||'b-blue'}">${g.type==='students'?'🎓 Students':g.type==='parents'?'👨‍👩‍👧 Parents':g.type==='teachers'?'👨‍🏫 Teachers':'⚙️ Custom'}</span>
                    ${g.filterClass?`<span class="badge b-gray">Class ${g.filterClass}</span>`:''}
                  </div>
                  <div style="font-size:12px;color:var(--gray-500);margin-bottom:10px">${memberCount} member${memberCount!==1?'s':''} · Created ${fmtDate(g.createdAt)}</div>
                  <div style="display:flex;gap:6px;flex-wrap:wrap">
                    <button class="btn btn-success btn-xs" onclick="openGroupInstructions('${g.id}')">📲 Open Instructions</button>
                    <button class="btn btn-cyan btn-xs" onclick="copyGroupNumbers('${g.id}')">📋 Copy Numbers</button>
                    <button class="btn btn-primary btn-xs" onclick="sendGroupWelcome('${g.id}')">💬 Welcome Msg</button>
                    <button class="btn btn-danger btn-xs" onclick="deleteWAGroup('${g.id}')">🗑️</button>
                  </div>
                </div>
              </div>
            </div>`;
          }).join('')}
        </div>`}
      </div>` : `
      <!-- LOG TAB -->
      <div class="card">
        <div class="card-hdr">
          <div><div class="card-title">📋 Message Log</div><div class="card-sub">${(store.whatsappLog||[]).length} messages sent total</div></div>
          ${(store.whatsappLog||[]).length>0?`<button class="btn btn-danger btn-sm" onclick="clearWALog()">🗑️ Clear Log</button>`:''}
        </div>
        ${recentLog.length===0?`<div class="empty"><div class="ei">💬</div><p>No messages sent yet</p></div>`:`
        <div class="tbl-wrap"><table>
          <thead><tr><th>To (Phone)</th><th>Message Preview</th><th>Sent At</th></tr></thead>
          <tbody>
            ${recentLog.map(l=>`<tr>
              <td style="font-family:'JetBrains Mono',monospace;font-size:12px">${l.to||'—'}</td>
              <td style="font-size:12px;color:var(--gray-600);max-width:280px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${l.message.replace(/\*/g,'').replace(/\n/g,' ').substring(0,80)}…</td>
              <td style="font-size:11px;color:var(--gray-400)">${new Date(l.sentAt).toLocaleString('en-IN')}</td>
            </tr>`).join('')}
          </tbody>
        </table></div>`}
      </div>`}
    </div>`;
  };

  window.waTab = (tab) => { activeTab = tab; render(); };
  window.waDateChange = (d) => { selDate = d; render(); };

  // ── Group Maker functions ─────────────────────────────────────────────────
  window.showCreateGroup = () => {
    const classes = [...new Set(store.students.map(s=>s.class).concat((store.classes||[]).map(c=>c.name)))].filter(Boolean).sort();
    openMo('👥 Create WhatsApp Group', `
      <div class="form-grid">
        <div class="fg form-full">
          <label>Group Name *</label>
          <input class="fc" id="wg_name" placeholder="e.g. Class 10A Parents, Science Teachers" style="font-size:14px;font-weight:600">
        </div>
        <div class="fg">
          <label>Group Type *</label>
          <select class="fc" id="wg_type" onchange="waGroupTypeChange()">
            <option value="parents">👨‍👩‍👧 Parents</option>
            <option value="students">🎓 Students</option>
            <option value="teachers">👨‍🏫 Teachers</option>
            <option value="custom">⚙️ Custom (select manually)</option>
          </select>
        </div>
        <div class="fg" id="wg_class_wrap">
          <label>Filter by Class</label>
          <select class="fc" id="wg_class">
            <option value="">All Classes</option>
            ${classes.map(c=>`<option value="${c}">${c}</option>`).join('')}
          </select>
        </div>
        <div class="fg form-full" id="wg_custom_wrap" style="display:none">
          <label>Select Members (Custom)</label>
          <div style="max-height:200px;overflow-y:auto;border:1.5px solid var(--gray-200);border-radius:8px;padding:10px;background:var(--gray-50)">
            <div style="margin-bottom:8px;display:flex;gap:8px">
              <button type="button" class="btn btn-outline btn-xs" onclick="waSelectAll(true)">✅ Select All</button>
              <button type="button" class="btn btn-outline btn-xs" onclick="waSelectAll(false)">⬜ Deselect All</button>
            </div>
            ${store.students.map(s=>`
              <label style="display:flex;align-items:center;gap:8px;padding:4px 0;cursor:pointer;font-size:12px">
                <input type="checkbox" class="wg-custom-cb" value="${s.id}" data-phone="${s.parentPhone||s.phone||''}" data-name="${s.name}" data-role="parent">
                <span><b>${s.name}</b> <span style="color:var(--gray-400)">(${s.class}) — Parent: ${s.parentPhone||s.phone||'No phone'}</span></span>
              </label>`).join('')}
            ${store.teachers.map(t=>`
              <label style="display:flex;align-items:center;gap:8px;padding:4px 0;cursor:pointer;font-size:12px">
                <input type="checkbox" class="wg-custom-cb" value="${t.id}" data-phone="${t.phone||''}" data-name="${t.name}" data-role="teacher">
                <span><b>${t.name}</b> <span style="color:var(--gray-400)">(Teacher) — ${t.phone||'No phone'}</span></span>
              </label>`).join('')}
          </div>
        </div>
        <div class="fg form-full">
          <label>Welcome Message (sent to each member)</label>
          <textarea class="fc" id="wg_welcome" style="min-height:80px">🏫 *S.V. Vidhyalay*

Welcome to our WhatsApp group! 🎉
This group is for school updates, announcements, and important notices.

📌 Please keep messages relevant to school matters.
Thank you for being part of our school community! 🙏

— S.V. Vidhyalay</textarea>
        </div>
      </div>
      <div style="display:flex;gap:8px;margin-top:14px">
        <button class="btn btn-primary" onclick="saveWAGroup()">✅ Create Group</button>
        <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
      </div>
    `);
  };

  window.waGroupTypeChange = () => {
    const type = document.getElementById('wg_type')?.value;
    const classWrap = document.getElementById('wg_class_wrap');
    const customWrap = document.getElementById('wg_custom_wrap');
    if (!classWrap || !customWrap) return;
    customWrap.style.display = type==='custom' ? 'block' : 'none';
    classWrap.style.display = type==='custom' ? 'none' : 'block';
  };

  window.waSelectAll = (v) => {
    document.querySelectorAll('.wg-custom-cb').forEach(cb => cb.checked = v);
  };

  window.saveWAGroup = () => {
    const name = document.getElementById('wg_name')?.value.trim();
    if (!name) { alert('Group name is required!'); return; }
    const type = document.getElementById('wg_type')?.value;
    const filterClass = document.getElementById('wg_class')?.value || '';
    const welcome = document.getElementById('wg_welcome')?.value || '';

    let members = [];
    if (type === 'custom') {
      document.querySelectorAll('.wg-custom-cb:checked').forEach(cb => {
        if (cb.dataset.phone) members.push({ id: cb.value, name: cb.dataset.name, phone: cb.dataset.phone, role: cb.dataset.role });
      });
    } else if (type === 'parents') {
      store.students.filter(s => !filterClass || s.class === filterClass).forEach(s => {
        const phone = s.parentPhone || s.phone;
        if (phone) members.push({ id: s.id, name: s.parent || s.father || s.name + ' (Parent)', phone, role: 'parent', studentName: s.name });
      });
    } else if (type === 'students') {
      store.students.filter(s => !filterClass || s.class === filterClass).forEach(s => {
        if (s.phone) members.push({ id: s.id, name: s.name, phone: s.phone, role: 'student' });
      });
    } else if (type === 'teachers') {
      store.teachers.forEach(t => {
        if (t.phone) members.push({ id: t.id, name: t.name, phone: t.phone, role: 'teacher' });
      });
    }

    if (!members.length) { alert('No members with phone numbers found for this selection!'); return; }

    if (!store.whatsappGroups) store.whatsappGroups = [];
    store.whatsappGroups.push({ id: 'WG' + Date.now(), name, type, filterClass, welcome, members, createdAt: new Date().toISOString().slice(0,10) });
    saveStore();
    closeMo();
    notify(`Group "${name}" created with ${members.length} members! 🎉`, '👥');
    activeTab = 'groups';
    render();
  };

  window.copyGroupNumbers = (gid) => {
    const g = (store.whatsappGroups||[]).find(x=>x.id===gid);
    if (!g) return;
    const nums = g.members.map(m => m.phone.replace(/[\s\-\(\)]/g,'').replace(/^\+/,'')).join('\n');
    navigator.clipboard.writeText(nums).then(() => notify(`${g.members.length} numbers copied to clipboard! 📋`, '📋')).catch(() => {
      const ta = document.createElement('textarea');
      ta.value = nums; document.body.appendChild(ta); ta.select(); document.execCommand('copy'); document.body.removeChild(ta);
      notify(`${g.members.length} numbers copied! 📋`, '📋');
    });
  };

  window.sendGroupWelcome = (gid) => {
    const g = (store.whatsappGroups||[]).find(x=>x.id===gid);
    if (!g) return;
    const withPhone = g.members.filter(m=>m.phone);
    if (!withPhone.length) { notify('No members with phone numbers!','⚠️'); return; }
    openMo(`💬 Send Welcome Message — ${g.name}`, `
      <div style="background:rgba(37,99,235,.05);border:1.5px solid rgba(37,99,235,.2);border-radius:10px;padding:14px;margin-bottom:14px">
        <div style="font-weight:700;color:var(--navy);margin-bottom:6px">📱 ${g.name}</div>
        <div style="font-size:12px;color:var(--gray-600)">${withPhone.length} members will receive this message one by one (WhatsApp will open for each).</div>
      </div>
      <div style="background:#e5ddd5;border-radius:12px;padding:14px;margin-bottom:14px">
        <div style="background:white;border-radius:12px 12px 12px 3px;padding:12px 14px;max-width:88%;display:inline-block;box-shadow:0 1px 2px rgba(0,0,0,.13)">
          <pre style="font-family:'DM Sans',sans-serif;font-size:12px;line-height:1.65;white-space:pre-wrap;margin:0;color:#111">${(g.welcome||'').replace(/\*(.*?)\*/g,'<b>$1</b>')}</pre>
        </div>
      </div>
      <div style="font-size:12px;color:var(--gray-500);margin-bottom:14px">⚠️ WhatsApp will open once for each member (${withPhone.length} times). Keep clicking Send in each window.</div>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button style="background:#25D366;color:white;border:none;border-radius:8px;padding:10px 18px;font-size:13px;font-weight:600;cursor:pointer" onclick="doSendGroupWelcome('${gid}');closeMo()">💬 Send to All ${withPhone.length} Members</button>
        <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
      </div>
    `);
  };

  window.doSendGroupWelcome = (gid) => {
    const g = (store.whatsappGroups||[]).find(x=>x.id===gid);
    if (!g) return;
    const msg = g.welcome || `🏫 *S.V. Vidhyalay*\n\nWelcome to ${g.name}! 🎉`;
    let sent = 0;
    g.members.filter(m=>m.phone).forEach((m, i) => {
      setTimeout(() => sendWhatsApp(m.phone, msg), i * 900);
      sent++;
    });
    notify(`Sending welcome to ${sent} members…`, '💬');
  };

  window.openGroupInstructions = (gid) => {
    const g = (store.whatsappGroups||[]).find(x=>x.id===gid);
    if (!g) return;
    const nums = g.members.map(m=>m.phone.replace(/[\s\-\(\)]/g,'').replace(/^\+/,'')).filter(Boolean);
    const formattedNums = g.members.map((m,i) => `${i+1}. ${m.name} — ${m.phone}`).join('\n');
    openMo(`📲 Create Group: "${g.name}"`, `
      <div style="display:grid;gap:12px">
        <!-- Step 1 -->
        <div style="background:rgba(37,99,235,.06);border:1.5px solid rgba(37,99,235,.2);border-radius:12px;padding:14px">
          <div style="font-weight:700;color:var(--blue);margin-bottom:8px;font-size:13px">Step 1 — Copy all member numbers</div>
          <div style="font-size:12px;color:var(--gray-600);margin-bottom:10px">${nums.length} members ready to be added</div>
          <button class="btn btn-primary btn-sm" onclick="copyGroupNumbers('${gid}');notify('Numbers copied!','📋')">📋 Copy ${nums.length} Numbers</button>
        </div>
        <!-- Step 2 -->
        <div style="background:rgba(16,185,129,.06);border:1.5px solid rgba(16,185,129,.2);border-radius:12px;padding:14px">
          <div style="font-weight:700;color:var(--emerald);margin-bottom:8px;font-size:13px">Step 2 — Create group in WhatsApp</div>
          <ol style="font-size:12px;color:var(--gray-700);line-height:1.8;padding-left:18px">
            <li>Open <b>WhatsApp</b> on your phone</li>
            <li>Tap <b>New Chat</b> → <b>New Group</b></li>
            <li>Add members by searching the numbers</li>
            <li>Set the group name: <b style="background:var(--gray-100);padding:1px 6px;border-radius:4px">${g.name}</b></li>
            <li>Tap <b>Create</b> ✅</li>
          </ol>
        </div>
        <!-- Step 3 -->
        <div style="background:rgba(245,158,11,.06);border:1.5px solid rgba(245,158,11,.2);border-radius:12px;padding:14px">
          <div style="font-weight:700;color:#b45309;margin-bottom:8px;font-size:13px">Step 3 — Send welcome message to each member</div>
          <div style="font-size:12px;color:var(--gray-600);margin-bottom:10px">Or open WhatsApp directly to send the welcome message to each member:</div>
          <button style="background:#25D366;color:white;border:none;border-radius:8px;padding:8px 16px;font-size:12px;font-weight:600;cursor:pointer" onclick="closeMo();sendGroupWelcome('${gid}')">💬 Send Welcome Messages</button>
        </div>
        <!-- Member list -->
        <div style="background:var(--gray-50);border-radius:10px;padding:12px;max-height:180px;overflow-y:auto">
          <div style="font-size:11px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.5px;margin-bottom:8px">Member List (${g.members.length})</div>
          <pre style="font-size:11px;font-family:'JetBrains Mono',monospace;color:var(--gray-700);white-space:pre-wrap;margin:0">${formattedNums}</pre>
        </div>
      </div>
      <div style="display:flex;gap:8px;margin-top:14px">
        <button class="btn btn-outline" onclick="closeMo()">Close</button>
      </div>
    `);
  };

  window.deleteWAGroup = (gid) => {
    if (!confirm('Delete this group?')) return;
    store.whatsappGroups = (store.whatsappGroups||[]).filter(g=>g.id!==gid);
    saveStore(); notify('Group deleted.','🗑️'); render();
  };

  window.waSendAbsent = (sid, date) => {
    const s = store.students.find(x=>x.id===sid);
    if (!s) return;
    const phone = s.parentPhone || s.phone;
    if (!phone) { notify('No phone number for this student!','⚠️'); return; }
    sendWhatsApp(phone, whatsAppAbsentMsg(s, date));
    notify(`WhatsApp opened for ${s.name}!`,'💬');
  };

  window.waSendFees = (sid) => {
    const s = store.students.find(x=>x.id===sid);
    if (!s) return;
    const phone = s.parentPhone || s.phone;
    if (!phone) { notify('No phone number!','⚠️'); return; }
    sendWhatsApp(phone, whatsAppFeesMsg(s));
    notify(`Fee reminder sent for ${s.name}!`,'💬');
  };

  window.waSendAllAbsent = (date) => {
    const absList = store.students.filter(s=>(store.attendance[s.id]||{})[date]==='A');
    let sent=0;
    absList.forEach((s,i) => {
      const phone = s.parentPhone||s.phone;
      if (!phone) return;
      setTimeout(()=>sendWhatsApp(phone, whatsAppAbsentMsg(s,date)), i*800);
      sent++;
    });
    notify(`Opening WhatsApp for ${sent} parents...`,'💬');
  };

  window.waSendAllFees = () => {
    const pending = store.students.filter(s=>{
      const fs=store.feeStructures[s.id];
      if(!fs||!fs.amount) return false;
      const paid=store.fees.filter(f=>f.studentId===s.id&&f.status==='paid').reduce((a,f)=>a+f.paid,0);
      return paid<fs.amount;
    });
    let sent=0;
    pending.forEach((s,i)=>{
      const phone=s.parentPhone||s.phone;
      if(!phone) return;
      setTimeout(()=>sendWhatsApp(phone,whatsAppFeesMsg(s)),i*800);
      sent++;
    });
    notify(`Opening WhatsApp for ${sent} parents...`,'💬');
  };

  window.clearWALog = () => {
    if(!confirm('Clear all message logs?')) return;
    store.whatsappLog=[];
    saveStore();
    notify('Log cleared.','🗑️');
    render();
  };

  window.previewWAMsg = (type, sid, date) => {
    const s = store.students.find(x=>x.id===sid);
    if (!s) return;
    const msg = type==='fees' ? whatsAppFeesMsg(s) : whatsAppAbsentMsg(s, date);
    const phone = s.parentPhone||s.phone||'';
    openMo(`📱 Message Preview — ${s.name}`, `
      <div style="background:#e5ddd5;border-radius:12px;padding:16px;margin-bottom:16px;min-height:120px">
        <div style="background:white;border-radius:12px 12px 12px 3px;padding:12px 14px;max-width:88%;display:inline-block;box-shadow:0 1px 2px rgba(0,0,0,.13)">
          <pre style="font-family:'DM Sans',sans-serif;font-size:12.5px;line-height:1.65;white-space:pre-wrap;margin:0;color:#111">${msg.replace(/\*(.*?)\*/g,'<b>$1</b>').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/&lt;b&gt;/g,'<b>').replace(/&lt;\/b&gt;/g,'</b>')}</pre>
          <div style="text-align:right;font-size:10px;color:#999;margin-top:6px">${new Date().toLocaleTimeString('en-IN',{hour:'2-digit',minute:'2-digit'})}</div>
        </div>
      </div>
      <div style="font-size:12px;color:var(--gray-500);margin-bottom:14px">📱 Will be sent to: <b style="color:var(--navy)">${phone||'No phone set'}</b> (${s.parent||s.father||'Parent'})</div>
      <div style="display:flex;gap:8px">
        ${phone?`<button style="background:#25D366;color:white;border:none;border-radius:8px;padding:9px 18px;font-family:'DM Sans',sans-serif;font-size:13px;font-weight:600;cursor:pointer" onclick="sendWhatsApp('${phone}',decodeURIComponent('${encodeURIComponent(msg)}'));closeMo()">💬 Send via WhatsApp</button>`:''}
        <button class="btn btn-outline" onclick="closeMo()">Close</button>
      </div>
    `);
  };

  render();
}

// ═══════════════════════════════════════════════
//  ADMIN — TEACHER SALARY MANAGEMENT
// ═══════════════════════════════════════════════
const SALARY_MONTHS = ['January','February','March','April','May','June','July','August','September','October','November','December'];

function renderAdminSalary() {
  const totalPaid = (store.salaries||[]).filter(s=>s.status==='paid').reduce((a,s)=>a+s.netSalary,0);
  const thisMonth = new Date().toLocaleString('default',{month:'long',year:'numeric'});
  const thisMonthPaid = (store.salaries||[]).filter(s=>s.status==='paid'&&s.month===new Date().getMonth()&&s.year===new Date().getFullYear()).length;

  area().innerHTML = `
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">💼 Teacher Salary Management</div><div style="font-size:12px;color:var(--gray-400)">${store.teachers.length} teaching staff · Pay history & records</div></div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="dl-btn" onclick="exportSalaryHistory()">📥 Export History</button>
      <button class="btn btn-primary" onclick="showPaySalary()">+ Pay Salary</button>
    </div>
  </div>

  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">👨‍🏫</div><div class="stat-info"><h3>${store.teachers.length}</h3><p>Total Teachers</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">💰</div><div class="stat-info"><h3>₹${(totalPaid/1000).toFixed(0)}K</h3><p>Total Paid</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">📅</div><div class="stat-info"><h3>${thisMonthPaid}/${store.teachers.length}</h3><p>This Month Paid</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">⏳</div><div class="stat-info"><h3>${store.teachers.length - thisMonthPaid}</h3><p>Pending This Month</p></div></div>
  </div>

  <!-- Quick Pay This Month -->
  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr"><div><div class="card-title">🗓️ ${thisMonth} — Salary Status</div><div class="card-sub">Quick overview of current month salary payments</div></div></div>
    ${store.teachers.length===0?`<div class="empty"><div class="ei">👨‍🏫</div><p>No teachers added yet</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Teacher</th><th>ID</th><th>Subject</th><th>Base Salary</th><th>This Month</th><th>Status</th><th>Actions</th></tr></thead>
      <tbody>
        ${store.teachers.map(t=>{
          const curRec = (store.salaries||[]).find(s=>s.teacherId===t.id&&s.month===new Date().getMonth()&&s.year===new Date().getFullYear());
          return `<tr>
            <td><div style="font-weight:600">${t.name}</div><div style="font-size:11px;color:var(--gray-400)">${t.email||'—'}</div></td>
            <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--emerald)">${t.id}</span></td>
            <td><span class="badge b-violet">${t.subject||'—'}</span></td>
            <td style="font-weight:600">₹${(t.baseSalary||0).toLocaleString()}</td>
            <td>${curRec?`<b style="color:var(--emerald)">₹${curRec.netSalary.toLocaleString()}</b>`:`<span style="color:var(--gray-400);font-size:12px">Not paid</span>`}</td>
            <td><span class="badge ${curRec?.status==='paid'?'b-green':'b-red'}">${curRec?.status==='paid'?'✅ Paid':'⏳ Pending'}</span></td>
            <td><div style="display:flex;gap:4px">
              ${!curRec||curRec.status!=='paid'?`<button class="btn btn-success btn-xs" onclick="quickPaySalary('${t.id}')">💰 Pay</button>`:''}
              <button class="btn btn-outline btn-xs" onclick="viewTeacherSalaryHistory('${t.id}')">History</button>
              ${curRec?`<button class="btn btn-outline btn-xs" onclick="viewSalarySlip('${curRec.id}')">📄 Slip</button>`:''}
            </div></td>
          </tr>`;
        }).join('')}
      </tbody>
    </table></div>`}
  </div>

  <!-- Full History -->
  <div class="card">
    <div class="card-hdr"><div class="card-title">📋 Full Salary History</div></div>
    ${(store.salaries||[]).length===0?`<div class="empty"><div class="ei">💼</div><p>No salary records yet</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Slip No.</th><th>Teacher</th><th>Month / Year</th><th>Basic</th><th>Allowances</th><th>Deductions</th><th>Net Salary</th><th>Method</th><th>Date</th><th>Status</th><th>Action</th></tr></thead>
      <tbody>
        ${(store.salaries||[]).slice().reverse().map(s=>{
          const t=store.teachers.find(x=>x.id===s.teacherId);
          return `<tr>
            <td><span style="font-family:'JetBrains Mono',monospace;font-size:10.5px;background:rgba(15,23,41,.08);padding:2px 7px;border-radius:5px;color:var(--navy)">${s.slipNo||'—'}</span></td>
            <td><div style="font-weight:600;font-size:13px">${t?.name||s.teacherName||s.teacherId}</div></td>
            <td><span class="badge b-blue">${SALARY_MONTHS[s.month]} ${s.year}</span></td>
            <td>₹${(s.basic||0).toLocaleString()}</td>
            <td style="color:var(--emerald)">+₹${(s.allowances||0).toLocaleString()}</td>
            <td style="color:var(--rose)">-₹${(s.deductions||0).toLocaleString()}</td>
            <td><b style="font-size:14px;color:var(--navy)">₹${(s.netSalary||0).toLocaleString()}</b></td>
            <td><span class="badge b-cyan">${s.method||'—'}</span></td>
            <td style="font-size:11px">${fmtDate(s.paidDate)}</td>
            <td><span class="badge ${s.status==='paid'?'b-green':'b-amber'}">${s.status}</span></td>
            <td><button class="btn btn-outline btn-xs" onclick="viewSalarySlip('${s.id}')">📄 Slip</button></td>
          </tr>`;
        }).join('')}
      </tbody>
    </table></div>`}
  </div>`;
}

function showPaySalary(teacherId) {
  if (!store.teachers.length) { alert('No teachers added yet'); return; }
  const now = new Date();
  const t = teacherId ? store.teachers.find(x=>x.id===teacherId) : null;
  openMo('💰 Pay Teacher Salary', `
    <div class="fg"><label>Select Teacher *</label>
      <select class="fc" id="ps_teacher" onchange="autoFillSalary(this.value)">
        <option value="">Choose teacher...</option>
        ${store.teachers.map(t=>`<option value="${t.id}" ${teacherId===t.id?'selected':''}>${t.name} (${t.id})</option>`).join('')}
      </select>
    </div>
    <div class="form-grid">
      <div class="fg"><label>Month</label>
        <select class="fc" id="ps_month">
          ${SALARY_MONTHS.map((m,i)=>`<option value="${i}" ${i===now.getMonth()?'selected':''}>${m}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Year</label>
        <input class="fc" id="ps_year" type="number" value="${now.getFullYear()}" min="2020" max="2040">
      </div>
      <div class="fg"><label>Basic Salary (₹) *</label>
        <input class="fc" id="ps_basic" type="number" placeholder="e.g. 25000" value="${t?.baseSalary||''}">
      </div>
      <div class="fg"><label>HRA / Allowances (₹)</label>
        <input class="fc" id="ps_allow" type="number" value="0" placeholder="0">
      </div>
      <div class="fg"><label>PF / Deductions (₹)</label>
        <input class="fc" id="ps_deduct" type="number" value="0" placeholder="0">
      </div>
      <div class="fg"><label>Payment Method</label>
        <select class="fc" id="ps_method">
          <option>Bank Transfer</option><option>Cash</option><option>Cheque</option>
          <option>UPI</option><option>NEFT</option><option>RTGS</option>
        </select>
      </div>
      <div class="fg"><label>Payment Date</label>
        <input class="fc" id="ps_date" type="date" value="${now.toISOString().slice(0,10)}">
      </div>
      <div class="fg"><label>Days Worked</label>
        <input class="fc" id="ps_days" type="number" value="26" min="1" max="31">
      </div>
    </div>
    <div class="fg"><label>Remarks / Notes</label>
      <input class="fc" id="ps_remarks" placeholder="Optional note">
    </div>
    <!-- Net salary preview -->
    <div id="salaryPreview" style="background:rgba(37,99,235,.06);border:1px solid rgba(37,99,235,.15);border-radius:10px;padding:14px;margin-bottom:16px">
      <div style="font-size:12px;font-weight:600;color:var(--navy);margin-bottom:8px">Net Salary Preview</div>
      <div style="display:flex;gap:16px;flex-wrap:wrap;font-size:13px">
        <span>Basic: <b id="prev_basic">₹0</b></span>
        <span style="color:var(--emerald)">+ Allowances: <b id="prev_allow">₹0</b></span>
        <span style="color:var(--rose)">- Deductions: <b id="prev_deduct">₹0</b></span>
        <span style="font-size:15px;font-weight:700;color:var(--navy)">= Net: <b id="prev_net">₹0</b></span>
      </div>
    </div>
    <div style="display:flex;gap:8px">
      <button class="btn btn-success" style="padding:10px 22px" onclick="saveSalaryPayment()">✅ Confirm & Pay</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);

  // Live preview
  ['ps_basic','ps_allow','ps_deduct'].forEach(id => {
    document.getElementById(id)?.addEventListener('input', updateSalaryPreview);
  });
  if (teacherId) autoFillSalary(teacherId);
  updateSalaryPreview();
}

window.autoFillSalary = function(tid) {
  const t = store.teachers.find(x=>x.id===tid);
  if (t?.baseSalary) {
    document.getElementById('ps_basic').value = t.baseSalary;
    updateSalaryPreview();
  }
};

function updateSalaryPreview() {
  const b = parseFloat(document.getElementById('ps_basic')?.value)||0;
  const a = parseFloat(document.getElementById('ps_allow')?.value)||0;
  const d = parseFloat(document.getElementById('ps_deduct')?.value)||0;
  const net = b+a-d;
  if(document.getElementById('prev_basic')) {
    document.getElementById('prev_basic').textContent = '₹'+b.toLocaleString();
    document.getElementById('prev_allow').textContent = '₹'+a.toLocaleString();
    document.getElementById('prev_deduct').textContent = '₹'+d.toLocaleString();
    document.getElementById('prev_net').textContent = '₹'+net.toLocaleString();
  }
}

function quickPaySalary(tid) {
  const t = store.teachers.find(x=>x.id===tid);
  if (!t) return;
  showPaySalary(tid);
}

function saveSalaryPayment() {
  const tid = document.getElementById('ps_teacher').value;
  if (!tid) { alert('Please select a teacher'); return; }
  const t = store.teachers.find(x=>x.id===tid);
  const basic = parseFloat(document.getElementById('ps_basic').value)||0;
  if (!basic) { alert('Basic salary is required'); return; }
  const allowances = parseFloat(document.getElementById('ps_allow').value)||0;
  const deductions = parseFloat(document.getElementById('ps_deduct').value)||0;
  const netSalary = basic + allowances - deductions;
  const month = parseInt(document.getElementById('ps_month').value);
  const year = parseInt(document.getElementById('ps_year').value);

  // Check duplicate
  const dup = (store.salaries||[]).find(s=>s.teacherId===tid&&s.month===month&&s.year===year);
  if (dup && !confirm(`Salary for ${SALARY_MONTHS[month]} ${year} already paid (${dup.slipNo}). Pay again?`)) return;

  if (!store.salaries) store.salaries = [];
  const yr = new Date().getFullYear();
  const slipNo = `SAL-${yr}-${String((store.salaries.length+1)).padStart(4,'0')}`;
  const id = 'SAL'+Date.now();

  store.salaries.push({
    id, slipNo, teacherId: tid, teacherName: t.name,
    month, year, basic, allowances, deductions, netSalary,
    method: document.getElementById('ps_method').value,
    paidDate: document.getElementById('ps_date').value,
    days: parseInt(document.getElementById('ps_days').value)||26,
    remarks: document.getElementById('ps_remarks').value,
    status: 'paid', paidAt: new Date().toISOString()
  });

  // Update teacher base salary for future reference
  t.baseSalary = basic;
  saveStore();
  closeMo();
  notify(`Salary paid! Slip: ${slipNo}`,'💰');
  viewSalarySlip(id);
  renderAdminSalary();
}

function viewSalarySlip(sid) {
  const s = (store.salaries||[]).find(x=>x.id===sid); if (!s) return;
  const t = store.teachers.find(x=>x.id===s.teacherId)||{};
  openMo('📄 Salary Slip', `
    <div class="receipt" style="max-width:520px;margin:0 auto">
      <div class="receipt-header">
        <img src="${LOGO_URI}" alt="Logo" style="width:52px;height:52px;border-radius:10px;object-fit:cover;margin-bottom:8px">
        <div style="font-family:'Playfair Display',serif;font-size:20px;color:var(--navy)">S.V. Vidhyalay</div>
        <div style="font-size:12px;color:var(--gray-500);margin-bottom:8px">Teacher Salary Slip</div>
        <div style="display:inline-block;background:linear-gradient(135deg,var(--navy),var(--navy2));color:white;padding:4px 16px;border-radius:6px;font-family:'JetBrains Mono',monospace;font-size:12px;font-weight:700">${s.slipNo}</div>
      </div>
      <div class="receipt-body">
        <div class="rr"><span style="color:var(--gray-500)">Teacher Name</span><b>${s.teacherName||t.name||'—'}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Teacher ID</span><span style="font-family:'JetBrains Mono',monospace">${s.teacherId}</span></div>
        <div class="rr"><span style="color:var(--gray-500)">Subject</span>${t.subject||'—'}</div>
        <div class="rr"><span style="color:var(--gray-500)">Class</span>${t.class||'—'}</div>
        <div class="rr"><span style="color:var(--gray-500)">Pay Period</span><b>${SALARY_MONTHS[s.month]} ${s.year}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Days Worked</span>${s.days||26} days</div>
        <div style="border-top:1px dashed var(--gray-300);margin:10px 0"></div>
        <div class="rr"><span style="color:var(--gray-500)">Basic Salary</span><b>₹${(s.basic||0).toLocaleString()}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Allowances (HRA etc.)</span><b style="color:var(--emerald)">+₹${(s.allowances||0).toLocaleString()}</b></div>
        <div class="rr"><span style="color:var(--gray-500)">Deductions (PF etc.)</span><b style="color:var(--rose)">-₹${(s.deductions||0).toLocaleString()}</b></div>
        <div style="border-top:2px solid var(--navy);margin:10px 0"></div>
        <div class="rr" style="font-size:15px"><span style="font-weight:700;color:var(--navy)">Net Salary</span><b style="font-size:18px;color:var(--navy)">₹${(s.netSalary||0).toLocaleString()}</b></div>
        <div style="border-top:1px dashed var(--gray-300);margin:10px 0"></div>
        <div class="rr"><span style="color:var(--gray-500)">Payment Method</span><span class="badge b-cyan">${s.method||'—'}</span></div>
        <div class="rr"><span style="color:var(--gray-500)">Payment Date</span>${fmtDate(s.paidDate)}</div>
        ${s.remarks?`<div class="rr"><span style="color:var(--gray-500)">Remarks</span><span style="font-size:12px">${s.remarks}</span></div>`:''}
        <div class="rr"><span style="color:var(--gray-500)">Status</span><span class="badge b-green">✅ PAID</span></div>
      </div>
      <div class="receipt-footer">
        <div>Generated: ${new Date().toLocaleString('en-IN')}</div>
        <div style="margin-top:3px">S.V. Vidhyalay — Official Salary Slip</div>
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-top:16px">
      <button class="btn btn-primary" onclick="window.print()">🖨️ Print</button>
      <button class="btn btn-outline" onclick="closeMo()">Close</button>
    </div>
  `);
}

function viewTeacherSalaryHistory(tid) {
  const t = store.teachers.find(x=>x.id===tid); if (!t) return;
  const history = (store.salaries||[]).filter(s=>s.teacherId===tid).sort((a,b)=>{
    if(a.year!==b.year) return b.year-a.year;
    return b.month-a.month;
  });
  const totalPaid = history.reduce((a,s)=>a+s.netSalary,0);
  openMo(`📋 Salary History — ${t.name}`, `
    <div style="display:flex;align-items:center;gap:14px;margin-bottom:16px;padding:14px;background:var(--gray-50);border-radius:10px">
      <div style="width:46px;height:46px;border-radius:12px;background:linear-gradient(135deg,var(--emerald),var(--blue));display:flex;align-items:center;justify-content:center;color:white;font-size:20px;font-weight:700">${t.name[0]}</div>
      <div>
        <div style="font-weight:700;font-size:15px;color:var(--navy)">${t.name}</div>
        <div style="font-size:12px;color:var(--gray-400)">${t.subject||'—'} · Class ${t.class||'—'} · ${t.id}</div>
      </div>
      <div style="margin-left:auto;text-align:right">
        <div style="font-size:20px;font-weight:700;color:var(--emerald)">₹${totalPaid.toLocaleString()}</div>
        <div style="font-size:11px;color:var(--gray-400)">Total Paid</div>
      </div>
    </div>
    ${history.length===0?`<div class="empty"><div class="ei">💼</div><p>No salary records for this teacher</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Slip No.</th><th>Month / Year</th><th>Basic</th><th>Allowances</th><th>Deductions</th><th>Net</th><th>Method</th><th>Date</th><th>Action</th></tr></thead>
      <tbody>
        ${history.map(s=>`<tr>
          <td style="font-family:'JetBrains Mono',monospace;font-size:10.5px">${s.slipNo}</td>
          <td><b>${SALARY_MONTHS[s.month]} ${s.year}</b></td>
          <td>₹${(s.basic||0).toLocaleString()}</td>
          <td style="color:var(--emerald)">+₹${(s.allowances||0).toLocaleString()}</td>
          <td style="color:var(--rose)">-₹${(s.deductions||0).toLocaleString()}</td>
          <td><b style="color:var(--navy)">₹${(s.netSalary||0).toLocaleString()}</b></td>
          <td><span class="badge b-cyan">${s.method||'—'}</span></td>
          <td style="font-size:11px">${fmtDate(s.paidDate)}</td>
          <td><button class="btn btn-outline btn-xs" onclick="viewSalarySlip('${s.id}')">📄 Slip</button></td>
        </tr>`).join('')}
      </tbody>
    </table></div>
    <div style="margin-top:14px">
      <button class="dl-btn" onclick="exportTeacherSalaryHistory('${tid}')">📥 Export History</button>
    </div>`}
  `);
}

window.exportTeacherSalaryHistory = function(tid) {
  const t = store.teachers.find(x=>x.id===tid);
  const history = (store.salaries||[]).filter(s=>s.teacherId===tid);
  const data = history.map(s=>[s.slipNo,t?.name||s.teacherName,SALARY_MONTHS[s.month]+' '+s.year,s.basic,s.allowances||0,s.deductions||0,s.netSalary,s.method||'',fmtDate(s.paidDate),s.status]);
  exportToExcel(data,['Slip No','Teacher','Month-Year','Basic','Allowances','Deductions','Net Salary','Method','Date','Status'],'SV_Salary_'+(t?.name||tid).replace(/\s+/g,'_'));
};

window.exportSalaryHistory = function() {
  const data = (store.salaries||[]).map(s=>{
    const t=store.teachers.find(x=>x.id===s.teacherId);
    return [s.slipNo,t?.name||s.teacherName,s.teacherId,SALARY_MONTHS[s.month]+' '+s.year,s.basic,s.allowances||0,s.deductions||0,s.netSalary,s.method||'',fmtDate(s.paidDate),s.status];
  });
  exportToExcel(data,['Slip No','Teacher Name','Teacher ID','Month-Year','Basic','Allowances','Deductions','Net Salary','Method','Paid Date','Status'],'SV_SalaryHistory_'+new Date().toISOString().slice(0,10));
};



// ═══════════════════════════════════════════════
//  TEACHER — MY SALARY VIEW
// ═══════════════════════════════════════════════
function renderTeacherSalaryView() {
  const t = currentUser;
  const history = (store.salaries||[]).filter(s=>s.teacherId===t.id).sort((a,b)=>{
    if(a.year!==b.year) return b.year-a.year;
    return b.month-a.month;
  });
  const totalPaid = history.reduce((a,s)=>a+s.netSalary,0);
  const latest = history[0];

  area().innerHTML = `
  <div style="background:linear-gradient(135deg,var(--navy),var(--navy2));border-radius:16px;padding:24px;color:white;margin-bottom:20px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:16px">
    <div>
      <div style="font-family:'Playfair Display',serif;font-size:22px;margin-bottom:4px">💼 My Salary</div>
      <div style="color:rgba(255,255,255,.5);font-size:12px">${t.name} · ${t.subject||'Teacher'} · ${t.id}</div>
    </div>
    <div style="text-align:right">
      <div style="font-size:32px;font-weight:700">₹${(latest?.netSalary||0).toLocaleString()}</div>
      <div style="color:rgba(255,255,255,.5);font-size:11px">Last salary ${latest?SALARY_MONTHS[latest.month]+' '+latest.year:'—'}</div>
    </div>
  </div>
  <div class="stat-grid">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">💰</div><div class="stat-info"><h3>₹${totalPaid.toLocaleString()}</h3><p>Total Received</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">📅</div><div class="stat-info"><h3>${history.length}</h3><p>Payments</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">📊</div><div class="stat-info"><h3>₹${history.length?Math.round(totalPaid/history.length).toLocaleString():'0'}</h3><p>Avg Monthly</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">💼</div><div class="stat-info"><h3>₹${(t.baseSalary||0).toLocaleString()}</h3><p>Base Salary</p></div></div>
  </div>
  <div class="card">
    <div class="card-hdr"><div class="card-title">📋 Salary History</div><button class="dl-btn" onclick="exportTeacherSalaryHistory('${t.id}')">📥 Export</button></div>
    ${history.length===0?`<div class="empty"><div class="ei">💼</div><p>No salary records yet. Contact admin.</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Slip No.</th><th>Month / Year</th><th>Basic</th><th>+Allow.</th><th>-Deduct.</th><th>Net Salary</th><th>Method</th><th>Date</th><th>Slip</th></tr></thead>
      <tbody>
        ${history.map(s=>`<tr>
          <td><span style="font-family:'JetBrains Mono',monospace;font-size:10.5px;background:rgba(15,23,41,.08);padding:2px 7px;border-radius:5px">${s.slipNo}</span></td>
          <td><b>${SALARY_MONTHS[s.month]} ${s.year}</b></td>
          <td>₹${(s.basic||0).toLocaleString()}</td>
          <td style="color:var(--emerald)">+₹${(s.allowances||0).toLocaleString()}</td>
          <td style="color:var(--rose)">-₹${(s.deductions||0).toLocaleString()}</td>
          <td><b style="font-size:14px;color:var(--navy)">₹${(s.netSalary||0).toLocaleString()}</b></td>
          <td><span class="badge b-cyan">${s.method||'—'}</span></td>
          <td style="font-size:12px">${fmtDate(s.paidDate)}</td>
          <td><button class="btn btn-outline btn-xs" onclick="viewSalarySlip('${s.id}')">📄 View</button></td>
        </tr>`).join('')}
      </tbody>
    </table></div>`}
  </div>`;
}


// ═══════════════════════════════════════════════
//  ADMIN — DAILY INCOME & EXPENSE TRACKER
// ═══════════════════════════════════════════════
function renderAdminFinance() {
  const today = new Date().toISOString().slice(0,10);
  let viewMode = 'dashboard'; // dashboard | daily | monthly | manage
  let selDate = today;
  let selMonth = today.slice(0,7);
  let filterType = '';

  // ── helpers ─────────────────────────────────
  const txns = () => store.transactions || [];
  const totalsByType = (list, type) => list.filter(t=>t.type===type).reduce((a,t)=>a+t.amount,0);
  const fmtCur = n => '₹' + (n||0).toLocaleString('en-IN');
  const monthName = ym => { const [y,m]=ym.split('-'); return new Date(y,m-1).toLocaleString('en-IN',{month:'long',year:'numeric'}); };
  const allMonths = () => [...new Set(txns().map(t=>t.date.slice(0,7)))].sort().reverse();

  const renderMain = () => {
    // Stats
    const allT = txns();
    const todayT = allT.filter(t=>t.date===today);
    const monthT = allT.filter(t=>t.date.slice(0,7)===today.slice(0,7));
    const yearT  = allT.filter(t=>t.date.slice(0,4)===today.slice(0,4));

    const tInc=totalsByType(allT,'income'), tExp=totalsByType(allT,'expense');
    const mInc=totalsByType(monthT,'income'), mExp=totalsByType(monthT,'expense');
    const dInc=totalsByType(todayT,'income'), dExp=totalsByType(todayT,'expense');

    // Category totals for chart
    const catTotals = {};
    allT.forEach(t=>{ catTotals[t.category]=(catTotals[t.category]||0)+t.amount; });
    const topCats = Object.entries(catTotals).sort((a,b)=>b[1]-a[1]).slice(0,6);

    area().innerHTML = `
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📒 Income & Expense Tracker</div>
        <div style="font-size:12px;color:var(--gray-400)">${allT.length} transactions · ${today}</div>
      </div>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="btn btn-outline" onclick="printFinance()">🖨️ Print Report</button>
        <button class="dl-btn" onclick="exportFinance()">📥 Export Excel</button>
        <button class="btn btn-success" onclick="showAddTxn('income')">+ Income</button>
        <button class="btn btn-danger" onclick="showAddTxn('expense')">− Expense</button>
      </div>
    </div>

    <!-- View tabs -->
    <div style="display:flex;gap:4px;background:var(--gray-100);border-radius:10px;padding:4px;margin-bottom:20px;max-width:480px">
      ${[['dashboard','📊 Dashboard'],['daily','📅 Daily'],['monthly','📆 Monthly'],['manage','📋 All Txns']].map(([id,label])=>`
      <button onclick="finTab('${id}')" style="flex:1;padding:8px 6px;border:none;border-radius:7px;font-family:'DM Sans',sans-serif;font-size:11.5px;font-weight:600;cursor:pointer;transition:all .2s;white-space:nowrap;${viewMode===id?'background:white;color:var(--navy);box-shadow:0 2px 8px rgba(0,0,0,.08)':'background:transparent;color:var(--gray-500)'}">${label}</button>`).join('')}
    </div>

    <div id="finContent">
    ${viewMode==='dashboard' ? `
      <!-- SUMMARY CARDS -->
      <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(160px,1fr));gap:14px;margin-bottom:20px">
        <div style="background:linear-gradient(135deg,var(--emerald),#059669);border-radius:14px;padding:18px;color:white">
          <div style="font-size:11px;opacity:.7;font-weight:600;text-transform:uppercase;letter-spacing:.5px;margin-bottom:6px">Total Income</div>
          <div style="font-size:26px;font-weight:700">${fmtCur(tInc)}</div>
          <div style="font-size:11px;opacity:.6;margin-top:4px">All time</div>
        </div>
        <div style="background:linear-gradient(135deg,var(--rose),#e11d48);border-radius:14px;padding:18px;color:white">
          <div style="font-size:11px;opacity:.7;font-weight:600;text-transform:uppercase;letter-spacing:.5px;margin-bottom:6px">Total Expense</div>
          <div style="font-size:26px;font-weight:700">${fmtCur(tExp)}</div>
          <div style="font-size:11px;opacity:.6;margin-top:4px">All time</div>
        </div>
        <div style="background:linear-gradient(135deg,${tInc-tExp>=0?'var(--blue),#1d4ed8':'#dc2626,#991b1b'});border-radius:14px;padding:18px;color:white">
          <div style="font-size:11px;opacity:.7;font-weight:600;text-transform:uppercase;letter-spacing:.5px;margin-bottom:6px">Net Balance</div>
          <div style="font-size:26px;font-weight:700">${fmtCur(tInc-tExp)}</div>
          <div style="font-size:11px;opacity:.6;margin-top:4px">${tInc-tExp>=0?'Surplus':'Deficit'}</div>
        </div>
        <div style="background:linear-gradient(135deg,var(--violet),#7c3aed);border-radius:14px;padding:18px;color:white">
          <div style="font-size:11px;opacity:.7;font-weight:600;text-transform:uppercase;letter-spacing:.5px;margin-bottom:6px">This Month</div>
          <div style="font-size:26px;font-weight:700">${fmtCur(mInc-mExp)}</div>
          <div style="font-size:11px;opacity:.6;margin-top:4px">+${fmtCur(mInc)} / -${fmtCur(mExp)}</div>
        </div>
        <div style="background:linear-gradient(135deg,var(--cyan),#0891b2);border-radius:14px;padding:18px;color:white">
          <div style="font-size:11px;opacity:.7;font-weight:600;text-transform:uppercase;letter-spacing:.5px;margin-bottom:6px">Today</div>
          <div style="font-size:26px;font-weight:700">${fmtCur(dInc-dExp)}</div>
          <div style="font-size:11px;opacity:.6;margin-top:4px">+${fmtCur(dInc)} / -${fmtCur(dExp)}</div>
        </div>
        <div style="background:linear-gradient(135deg,var(--amber),#d97706);border-radius:14px;padding:18px;color:white">
          <div style="font-size:11px;opacity:.7;font-weight:600;text-transform:uppercase;letter-spacing:.5px;margin-bottom:6px">This Year</div>
          <div style="font-size:26px;font-weight:700">${fmtCur(totalsByType(yearT,'income')-totalsByType(yearT,'expense'))}</div>
          <div style="font-size:11px;opacity:.6;margin-top:4px">${today.slice(0,4)}</div>
        </div>
      </div>

      <div class="two-col">
        <!-- Income vs Expense bar -->
        <div class="card">
          <div class="card-hdr"><div class="card-title">📊 Income vs Expense</div></div>
          ${allMonths().slice(0,6).reverse().map(ym=>{
            const mt=allT.filter(t=>t.date.slice(0,7)===ym);
            const inc=totalsByType(mt,'income'), exp=totalsByType(mt,'expense');
            const maxVal=Math.max(inc,exp,1);
            return `<div style="margin-bottom:14px">
              <div style="display:flex;justify-content:space-between;font-size:12px;margin-bottom:5px">
                <b style="color:var(--navy)">${monthName(ym)}</b>
                <span style="color:${inc-exp>=0?'var(--emerald)':'var(--rose)'}">Net: ${fmtCur(inc-exp)}</span>
              </div>
              <div style="display:flex;gap:4px;align-items:center;margin-bottom:3px">
                <span style="font-size:10px;color:var(--emerald);width:48px">Income</span>
                <div style="flex:1;height:10px;background:var(--gray-100);border-radius:99px;overflow:hidden">
                  <div style="width:${Math.round((inc/maxVal)*100)}%;height:100%;background:linear-gradient(90deg,var(--emerald),#34d399);border-radius:99px"></div>
                </div>
                <span style="font-size:11px;font-weight:600;color:var(--emerald);min-width:70px;text-align:right">${fmtCur(inc)}</span>
              </div>
              <div style="display:flex;gap:4px;align-items:center">
                <span style="font-size:10px;color:var(--rose);width:48px">Expense</span>
                <div style="flex:1;height:10px;background:var(--gray-100);border-radius:99px;overflow:hidden">
                  <div style="width:${Math.round((exp/maxVal)*100)}%;height:100%;background:linear-gradient(90deg,var(--rose),#fb7185);border-radius:99px"></div>
                </div>
                <span style="font-size:11px;font-weight:600;color:var(--rose);min-width:70px;text-align:right">${fmtCur(exp)}</span>
              </div>
            </div>`;
          }).join('') || `<div class="empty"><div class="ei">📊</div><p>No transactions yet</p></div>`}
        </div>

        <!-- Top Categories -->
        <div class="card">
          <div class="card-hdr"><div class="card-title">🏷️ Top Categories</div></div>
          ${topCats.length===0?`<div class="empty"><div class="ei">🏷️</div><p>No categories yet</p></div>`:`
          ${topCats.map(([cat,amt])=>{
            const isInc = (store.transCategories?.income||[]).includes(cat);
            const pct = Math.round((amt/Math.max(...Object.values(catTotals)))*100);
            return `<div style="margin-bottom:12px">
              <div style="display:flex;justify-content:space-between;font-size:12.5px;margin-bottom:5px">
                <span style="font-weight:500;color:var(--navy)">${cat}</span>
                <div style="display:flex;align-items:center;gap:7px">
                  <span class="badge ${isInc?'b-green':'b-red'}">${isInc?'Income':'Expense'}</span>
                  <b>${fmtCur(amt)}</b>
                </div>
              </div>
              <div class="pb"><div class="pf" style="width:${pct}%;background:${isInc?'linear-gradient(90deg,var(--emerald),#34d399)':'linear-gradient(90deg,var(--rose),#fb7185)'}"></div></div>
            </div>`;
          }).join('')}`}
        </div>
      </div>

      <!-- Recent Transactions -->
      <div class="card">
        <div class="card-hdr"><div class="card-title">🕐 Recent Transactions</div><button class="btn btn-outline btn-sm" onclick="finTab('manage')">View All</button></div>
        ${allT.length===0?`<div class="empty"><div class="ei">📒</div><p>No transactions yet. Add income or expense to get started.</p></div>`:`
        <div class="tbl-wrap"><table>
          <thead><tr><th>Date</th><th>Type</th><th>Category</th><th>Description</th><th>Method</th><th>Amount</th><th>Actions</th></tr></thead>
          <tbody>
            ${allT.slice().sort((a,b)=>new Date(b.date)-new Date(a.date)).slice(0,10).map(t=>txnRow(t)).join('')}
          </tbody>
        </table></div>`}
      </div>

    ` : viewMode==='daily' ? `
      <!-- DAILY VIEW -->
      <div class="card">
        <div class="card-hdr">
          <div><div class="card-title">📅 Daily Transactions</div></div>
          <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
            <input type="date" class="fc" style="width:160px" value="${selDate}" onchange="finDateChange(this.value)">
            <button class="btn btn-outline btn-sm" onclick="printFinance('','${selDate}','${selDate}')">🖨️ Print Day</button>
            <button class="btn btn-success btn-sm" onclick="showAddTxn('income','${selDate}')">+ Income</button>
            <button class="btn btn-danger btn-sm" onclick="showAddTxn('expense','${selDate}')">− Expense</button>
          </div>
        </div>
        ${(()=>{
          const dt = allT.filter(t=>t.date===selDate);
          const inc=totalsByType(dt,'income'), exp=totalsByType(dt,'expense');
          if(!dt.length) return `<div class="empty"><div class="ei">📅</div><p>No transactions on ${fmtDate(selDate)}</p></div>`;
          return `
          <div style="display:flex;gap:12px;margin-bottom:16px;flex-wrap:wrap">
            <div style="flex:1;min-width:130px;background:rgba(16,185,129,.08);border:1px solid rgba(16,185,129,.2);border-radius:10px;padding:14px;text-align:center">
              <div style="font-size:20px;font-weight:700;color:var(--emerald)">${fmtCur(inc)}</div>
              <div style="font-size:11px;color:var(--gray-400)">Income</div>
            </div>
            <div style="flex:1;min-width:130px;background:rgba(244,63,94,.08);border:1px solid rgba(244,63,94,.2);border-radius:10px;padding:14px;text-align:center">
              <div style="font-size:20px;font-weight:700;color:var(--rose)">${fmtCur(exp)}</div>
              <div style="font-size:11px;color:var(--gray-400)">Expense</div>
            </div>
            <div style="flex:1;min-width:130px;background:rgba(37,99,235,.08);border:1px solid rgba(37,99,235,.2);border-radius:10px;padding:14px;text-align:center">
              <div style="font-size:20px;font-weight:700;color:${inc-exp>=0?'var(--emerald)':'var(--rose)'}">${fmtCur(inc-exp)}</div>
              <div style="font-size:11px;color:var(--gray-400)">Net</div>
            </div>
          </div>
          <div class="tbl-wrap"><table>
            <thead><tr><th>Type</th><th>Category</th><th>Description</th><th>Method</th><th>Amount</th><th>Actions</th></tr></thead>
            <tbody>${dt.map(t=>txnRow(t,false)).join('')}</tbody>
          </table></div>`;
        })()}
      </div>

    ` : viewMode==='monthly' ? `
      <!-- MONTHLY VIEW -->
      <div style="display:flex;align-items:center;gap:12px;margin-bottom:16px;flex-wrap:wrap">
        <input type="month" class="fc" style="width:170px" value="${selMonth}" onchange="finMonthChange(this.value)">
        <button class="btn btn-outline btn-sm" onclick="printFinance('','${selMonth}-01','${selMonth}-31')">🖨️ Print Month</button>
        <button class="dl-btn" onclick="exportFinanceMonth('${selMonth}')">📥 Export Month</button>
      </div>
      ${(()=>{
        const mt = allT.filter(t=>t.date.slice(0,7)===selMonth);
        const inc=totalsByType(mt,'income'), exp=totalsByType(mt,'expense');
        if(!mt.length) return `<div class="card"><div class="empty"><div class="ei">📆</div><p>No transactions for ${monthName(selMonth)}</p></div></div>`;
        // Group by day
        const byDay = {};
        mt.forEach(t=>{ (byDay[t.date]=byDay[t.date]||[]).push(t); });
        return `
        <div class="stat-grid" style="margin-bottom:16px">
          <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">💚</div><div class="stat-info"><h3>${fmtCur(inc)}</h3><p>Month Income</p></div></div>
          <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">❤️</div><div class="stat-info"><h3>${fmtCur(exp)}</h3><p>Month Expense</p></div></div>
          <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">💙</div><div class="stat-info"><h3 style="color:${inc-exp>=0?'var(--emerald)':'var(--rose)'}">${fmtCur(inc-exp)}</h3><p>Net Balance</p></div></div>
          <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1)">📋</div><div class="stat-info"><h3>${mt.length}</h3><p>Transactions</p></div></div>
        </div>
        ${Object.keys(byDay).sort().reverse().map(date=>{
          const dayT=byDay[date];
          const dInc=totalsByType(dayT,'income'), dExp=totalsByType(dayT,'expense');
          return `<div class="card" style="margin-bottom:12px">
            <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:10px;flex-wrap:wrap;gap:6px">
              <div><b style="color:var(--navy)">${new Date(date).toLocaleDateString('en-IN',{weekday:'long',day:'numeric',month:'long'})}</b></div>
              <div style="display:flex;gap:10px;font-size:12.5px">
                <span style="color:var(--emerald);font-weight:600">+${fmtCur(dInc)}</span>
                <span style="color:var(--rose);font-weight:600">-${fmtCur(dExp)}</span>
                <span style="font-weight:700;color:${dInc-dExp>=0?'var(--navy)':'var(--rose)'}">Net: ${fmtCur(dInc-dExp)}</span>
              </div>
            </div>
            ${dayT.map(t=>`
            <div style="display:flex;align-items:center;gap:10px;padding:8px 10px;border-radius:8px;background:${t.type==='income'?'rgba(16,185,129,.05)':'rgba(244,63,94,.05)'};border:1px solid ${t.type==='income'?'rgba(16,185,129,.15)':'rgba(244,63,94,.15)'};margin-bottom:6px">
              <div style="width:32px;height:32px;border-radius:8px;background:${t.type==='income'?'rgba(16,185,129,.15)':'rgba(244,63,94,.12)'};display:flex;align-items:center;justify-content:center;font-size:14px;flex-shrink:0">${t.type==='income'?'💚':'❤️'}</div>
              <div style="flex:1;min-width:0">
                <div style="font-weight:600;font-size:13px;color:var(--navy)">${t.description||t.category}</div>
                <div style="font-size:11px;color:var(--gray-400)">${t.category} · ${t.method||'—'}</div>
              </div>
              <div style="text-align:right;flex-shrink:0">
                <div style="font-weight:700;font-size:14px;color:${t.type==='income'?'var(--emerald)':'var(--rose)'}">${t.type==='income'?'+':'-'}${fmtCur(t.amount)}</div>
              </div>
              <div style="display:flex;gap:4px">
                <button class="btn btn-warning btn-xs" onclick="editTxn('${t.id}')">✏️</button>
                <button class="btn btn-danger btn-xs" onclick="deleteTxn('${t.id}')">🗑️</button>
              </div>
            </div>`).join('')}
          </div>`;
        }).join('')}`;
      })()}

    ` : `
      <!-- ALL TRANSACTIONS -->
      <div class="card">
        <div class="card-hdr">
          <div><div class="card-title">📋 All Transactions</div><div class="card-sub">${allT.length} records</div></div>
          <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
            <select class="fc" style="width:130px" onchange="finFilterType(this.value)">
              <option value="">All Types</option>
              <option value="income" ${filterType==='income'?'selected':''}>Income Only</option>
              <option value="expense" ${filterType==='expense'?'selected':''}>Expense Only</option>
            </select>
            <button class="btn btn-outline btn-sm" onclick="printFinance(filterType)">🖨️ Print</button>
            <button class="dl-btn" onclick="exportFinance()">📥 Export All</button>
          </div>
        </div>
        ${(()=>{
          const list = filterType ? allT.filter(t=>t.type===filterType) : allT;
          const sorted = list.slice().sort((a,b)=>new Date(b.date)-new Date(a.date));
          return sorted.length===0?`<div class="empty"><div class="ei">📒</div><p>No transactions found</p></div>`:`
          <div class="tbl-wrap"><table>
            <thead><tr><th>Date</th><th>Type</th><th>Category</th><th>Description</th><th>Method</th><th>Ref</th><th>Amount</th><th>Actions</th></tr></thead>
            <tbody>${sorted.map(t=>txnRow(t)).join('')}</tbody>
          </table></div>
          <div style="display:flex;gap:20px;margin-top:12px;padding:12px 14px;background:var(--gray-50);border-radius:9px;font-size:13px;flex-wrap:wrap">
            <span>Total Income: <b style="color:var(--emerald)">${fmtCur(totalsByType(list,'income'))}</b></span>
            <span>Total Expense: <b style="color:var(--rose)">${fmtCur(totalsByType(list,'expense'))}</b></span>
            <span>Net: <b style="color:var(--navy)">${fmtCur(totalsByType(list,'income')-totalsByType(list,'expense'))}</b></span>
          </div>`;
        })()}
      </div>
    `}
    </div>`;
  };

  function txnRow(t, showDate=true) {
    return `<tr>
      ${showDate?`<td style="font-size:12px;white-space:nowrap">${fmtDate(t.date)}</td>`:''}
      <td><span class="badge ${t.type==='income'?'b-green':'b-red'}">${t.type==='income'?'💚 Income':'❤️ Expense'}</span></td>
      <td style="font-size:12.5px;font-weight:500">${t.category}</td>
      <td style="font-size:12.5px;color:var(--gray-600);max-width:200px;overflow:hidden;text-overflow:ellipsis">${t.description||'—'}</td>
      <td><span class="badge b-cyan" style="font-size:10px">${t.method||'—'}</span></td>
      ${showDate?`<td style="font-size:11px;color:var(--gray-400);font-family:'JetBrains Mono',monospace">${t.ref||'—'}</td>`:''}
      <td><b style="color:${t.type==='income'?'var(--emerald)':'var(--rose)'};">${t.type==='income'?'+':'-'}${fmtCur(t.amount)}</b></td>
      <td><div style="display:flex;gap:4px">
        <button class="btn btn-warning btn-xs" onclick="editTxn('${t.id}')">✏️</button>
        <button class="btn btn-danger btn-xs" onclick="deleteTxn('${t.id}')">🗑️</button>
      </div></td>
    </tr>`;
  }

  // Tab controls
  window.finTab = (tab) => { viewMode=tab; renderMain(); };
  window.finDateChange = (d) => { selDate=d; viewMode='daily'; renderMain(); };
  window.finMonthChange = (m) => { selMonth=m; renderMain(); };
  window.finFilterType = (t) => { filterType=t; renderMain(); };

  // ── Add / Edit Transaction Modal ────────────────
  window.showAddTxn = (defaultType='income', defaultDate=today) => {
    const cats = store.transCategories || {};
    openMo('📒 Add Transaction', `
      <div class="form-grid">
        <div class="fg"><label>Type *</label>
          <select class="fc" id="tx_type" onchange="updateTxnCats()">
            <option value="income" ${defaultType==='income'?'selected':''}>💚 Income</option>
            <option value="expense" ${defaultType==='expense'?'selected':''}>❤️ Expense</option>
          </select>
        </div>
        <div class="fg"><label>Date *</label>
          <input class="fc" id="tx_date" type="date" value="${defaultDate}">
        </div>
        <div class="fg"><label>Category *</label>
          <select class="fc" id="tx_cat">
            ${(defaultType==='income'?cats.income||[]:cats.expense||[]).map(c=>`<option>${c}</option>`).join('')}
          </select>
        </div>
        <div class="fg"><label>Amount (₹) *</label>
          <input class="fc" id="tx_amount" type="number" placeholder="0.00" min="0">
        </div>
        <div class="fg"><label>Payment Method</label>
          <select class="fc" id="tx_method">
            <option>Cash</option><option>Bank Transfer</option><option>UPI</option><option>Cheque</option><option>NEFT</option><option>Card</option>
          </select>
        </div>
        <div class="fg"><label>Reference No.</label>
          <input class="fc" id="tx_ref" placeholder="Optional">
        </div>
        <div class="fg form-full"><label>Description</label>
          <input class="fc" id="tx_desc" placeholder="Brief description...">
        </div>
        <div class="fg form-full"><label>Notes / Remarks</label>
          <textarea class="fc" id="tx_notes" placeholder="Additional details..." style="min-height:60px"></textarea>
        </div>
      </div>
      <div style="display:flex;gap:8px;margin-top:14px">
        <button class="btn btn-success" onclick="saveTxn()">✅ Save Transaction</button>
        <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
      </div>
    `);
  };

  window.updateTxnCats = function() {
    const type = document.getElementById('tx_type').value;
    const cats = store.transCategories || {};
    const list = type==='income' ? cats.income||[] : cats.expense||[];
    document.getElementById('tx_cat').innerHTML = list.map(c=>`<option>${c}</option>`).join('');
  };

  window.saveTxn = function() {
    const amount = parseFloat(document.getElementById('tx_amount').value)||0;
    if(!amount) { alert('Amount is required'); return; }
    const id = 'TX'+Date.now();
    store.transactions.push({
      id,
      type: document.getElementById('tx_type').value,
      date: document.getElementById('tx_date').value,
      category: document.getElementById('tx_cat').value,
      amount,
      method: document.getElementById('tx_method').value,
      ref: document.getElementById('tx_ref').value,
      description: document.getElementById('tx_desc').value,
      notes: document.getElementById('tx_notes').value,
      createdAt: new Date().toISOString()
    });
    saveStore();
    closeMo();
    notify('Transaction saved!','💰');
    renderMain();
  };

  window.editTxn = function(id) {
    const t = (store.transactions||[]).find(x=>x.id===id); if(!t) return;
    const cats = store.transCategories||{};
    openMo('✏️ Edit Transaction', `
      <div class="form-grid">
        <div class="fg"><label>Type</label>
          <select class="fc" id="tx_type" onchange="updateTxnCats()">
            <option value="income" ${t.type==='income'?'selected':''}>💚 Income</option>
            <option value="expense" ${t.type==='expense'?'selected':''}>❤️ Expense</option>
          </select>
        </div>
        <div class="fg"><label>Date</label><input class="fc" id="tx_date" type="date" value="${t.date}"></div>
        <div class="fg"><label>Category</label>
          <select class="fc" id="tx_cat">
            ${(t.type==='income'?cats.income||[]:cats.expense||[]).map(c=>`<option ${c===t.category?'selected':''}>${c}</option>`).join('')}
          </select>
        </div>
        <div class="fg"><label>Amount (₹)</label><input class="fc" id="tx_amount" type="number" value="${t.amount}"></div>
        <div class="fg"><label>Method</label>
          <select class="fc" id="tx_method">
            ${['Cash','Bank Transfer','UPI','Cheque','NEFT','Card'].map(m=>`<option ${m===t.method?'selected':''}>${m}</option>`).join('')}
          </select>
        </div>
        <div class="fg"><label>Reference</label><input class="fc" id="tx_ref" value="${t.ref||''}"></div>
        <div class="fg form-full"><label>Description</label><input class="fc" id="tx_desc" value="${t.description||''}"></div>
        <div class="fg form-full"><label>Notes</label><textarea class="fc" id="tx_notes" style="min-height:55px">${t.notes||''}</textarea></div>
      </div>
      <div style="display:flex;gap:8px;margin-top:14px">
        <button class="btn btn-primary" onclick="updateTxn('${id}')">Update</button>
        <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
      </div>
    `);
  };

  window.updateTxn = function(id) {
    const t = (store.transactions||[]).find(x=>x.id===id); if(!t) return;
    t.type=document.getElementById('tx_type').value;
    t.date=document.getElementById('tx_date').value;
    t.category=document.getElementById('tx_cat').value;
    t.amount=parseFloat(document.getElementById('tx_amount').value)||0;
    t.method=document.getElementById('tx_method').value;
    t.ref=document.getElementById('tx_ref').value;
    t.description=document.getElementById('tx_desc').value;
    t.notes=document.getElementById('tx_notes').value;
    saveStore(); closeMo(); notify('Updated!'); renderMain();
  };

  window.deleteTxn = function(id) {
    if(!confirm('Delete this transaction?')) return;
    store.transactions=(store.transactions||[]).filter(x=>x.id!==id);
    saveStore(); notify('Deleted.','🗑️'); renderMain();
  };

  window.exportFinance = function() {
    const data=(store.transactions||[]).slice().sort((a,b)=>new Date(b.date)-new Date(a.date))
      .map(t=>[t.date,t.type,t.category,t.description||'',t.amount,t.method||'',t.ref||'',t.notes||'']);
    exportToExcel(data,['Date','Type','Category','Description','Amount','Method','Reference','Notes'],'SV_Finance_'+new Date().toISOString().slice(0,10));
  };

  window.exportFinanceMonth = function(ym) {
    const data=(store.transactions||[]).filter(t=>t.date.slice(0,7)===ym)
      .sort((a,b)=>new Date(a.date)-new Date(b.date))
      .map(t=>[t.date,t.type,t.category,t.description||'',t.amount,t.method||'',t.ref||'']);
    exportToExcel(data,['Date','Type','Category','Description','Amount','Method','Reference'],'SV_Finance_'+ym);
  };

  renderMain();
}


// ═══════════════════════════════════════════════
//  ADMIN — TEACHER ATTENDANCE (Present/Absent/Leave)
// ═══════════════════════════════════════════════
function renderAdminTeacherAtt() {
  const today = new Date().toISOString().slice(0,10);
  let selDate = today;

  const render = () => {
    const ta = store.teacherAttendance || {};
    const getStatus = (tid, date) => (ta[tid]||{})[date] || null;
    const present  = store.teachers.filter(t => getStatus(t.id, selDate)==='P').length;
    const absent   = store.teachers.filter(t => getStatus(t.id, selDate)==='A').length;
    const leave    = store.teachers.filter(t => ['L','HL'].includes(getStatus(t.id, selDate))).length;
    const unmarked = store.teachers.length - present - absent - leave;

    area().innerHTML = `
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">🗓️ Teacher Attendance</div>
        <div style="font-size:12px;color:var(--gray-400)">Track daily teacher attendance</div></div>
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <input type="date" class="fc" style="width:160px" value="${selDate}" onchange="taDate(this.value)">
        <button class="btn btn-success btn-sm" onclick="taMarkAll('P','${selDate}')">✅ All Present</button>
        <button class="btn btn-outline btn-sm" onclick="taMarkAll('A','${selDate}')">❌ All Absent</button>
        <button class="btn btn-primary btn-sm" onclick="taSave('${selDate}')">💾 Save</button>
        <button class="dl-btn" onclick="exportTeacherAtt()">📥 Export</button>
      </div>
    </div>

    <div class="stat-grid" style="margin-bottom:18px">
      <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>${present}</h3><p>Present</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">❌</div><div class="stat-info"><h3>${absent}</h3><p>Absent</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">🏖️</div><div class="stat-info"><h3>${leave}</h3><p>On Leave</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(100,116,139,.1)">⚪</div><div class="stat-info"><h3>${unmarked}</h3><p>Unmarked</p></div></div>
    </div>

    <div class="card">
      ${store.teachers.length===0 ? `<div class="empty"><div class="ei">👨‍🏫</div><p>No teachers added yet</p></div>` : `
      <div class="tbl-wrap"><table>
        <thead><tr><th>Teacher</th><th>ID</th><th>Subject</th><th>Class</th><th>Status for ${selDate}</th><th>Monthly %</th></tr></thead>
        <tbody>
          ${store.teachers.map(t => {
            const status = getStatus(t.id, selDate);
            // Monthly stats
            const month = selDate.slice(0,7);
            const monthRecs = Object.entries(ta[t.id]||{}).filter(([d]) => d.startsWith(month));
            const mP = monthRecs.filter(([,v])=>v==='P').length;
            const mTotal = monthRecs.length;
            const mPct = mTotal ? Math.round((mP/mTotal)*100) : null;
            return `<tr>
              <td><div style="font-weight:600">${t.name}</div></td>
              <td><span style="font-family:'JetBrains Mono',monospace;font-size:11px;color:var(--emerald)">${t.id}</span></td>
              <td><span class="badge b-violet">${t.subject||'—'}</span></td>
              <td><span class="badge b-blue">${t.class||'—'}</span></td>
              <td>
                <div style="display:flex;gap:5px;flex-wrap:wrap">
                  ${[['P','✅ Present','rgba(16,185,129,.15)','var(--emerald)'],
                     ['A','❌ Absent','rgba(244,63,94,.12)','var(--rose)'],
                     ['L','🏖️ Leave','rgba(245,158,11,.12)','#b45309'],
                     ['HL','½ Half Leave','rgba(139,92,246,.1)','var(--violet)']
                    ].map(([val,label,bg,col]) =>
                    `<button onclick="taSetStatus('${t.id}','${selDate}','${val}')"
                      style="padding:5px 10px;border:2px solid ${status===val?col:'transparent'};border-radius:7px;background:${status===val?bg:'var(--gray-50)'};color:${status===val?col:'var(--gray-500)'};font-size:11.5px;font-weight:600;cursor:pointer;transition:all .15s">${label}</button>`
                  ).join('')}
                </div>
              </td>
              <td>${mPct!==null ? `<div style="display:flex;align-items:center;gap:6px">
                <div class="pb" style="width:55px"><div class="pf" style="width:${mPct}%;background:${mPct>=80?'linear-gradient(90deg,var(--blue),var(--emerald))':'linear-gradient(90deg,var(--amber),var(--rose))'}"></div></div>
                <b style="font-size:11px;color:${mPct>=80?'var(--emerald)':'var(--rose)'}">${mPct}%</b>
              </div>` : '<span style="color:var(--gray-300);font-size:12px">—</span>'}</td>
            </tr>`;
          }).join('')}
        </tbody>
      </table></div>
      <div style="margin-top:14px;padding:12px 14px;background:var(--gray-50);border-radius:9px;font-size:12.5px;color:var(--gray-600)">
        💡 Click a status button to set teacher's attendance, then click <b>Save</b>
      </div>`}
    </div>

    <!-- Monthly overview -->
    <div class="card" style="margin-top:16px">
      <div class="card-hdr"><div class="card-title">📆 Monthly Attendance Overview</div>
        <select class="fc" style="width:160px" onchange="taMonthChange(this.value)">
          ${[...new Set(Object.values(store.teacherAttendance||{}).flatMap(r=>Object.keys(r).map(d=>d.slice(0,7))))].sort().reverse().concat([today.slice(0,7)]).filter((v,i,a)=>a.indexOf(v)===i).slice(0,12).map(m=>`<option value="${m}" ${m===selDate.slice(0,7)?'selected':''}>${new Date(m+'-01').toLocaleString('en-IN',{month:'long',year:'numeric'})}</option>`).join('')}
        </select>
      </div>
      ${store.teachers.length===0?'':(() => {
        const month = selDate.slice(0,7);
        const ta2 = store.teacherAttendance||{};
        return `<div class="tbl-wrap"><table>
          <thead><tr><th>Teacher</th><th>Present</th><th>Absent</th><th>Leave</th><th>Working Days</th><th>Attendance %</th></tr></thead>
          <tbody>
          ${store.teachers.map(t => {
            const recs = Object.entries(ta2[t.id]||{}).filter(([d])=>d.startsWith(month));
            const p=recs.filter(([,v])=>v==='P').length;
            const a=recs.filter(([,v])=>v==='A').length;
            const l=recs.filter(([,v])=>['L','HL'].includes(v)).length;
            const total=recs.length;
            const pct=total?Math.round((p/total)*100):0;
            return `<tr>
              <td><b>${t.name}</b></td>
              <td style="color:var(--emerald);font-weight:600">${p}</td>
              <td style="color:var(--rose);font-weight:600">${a}</td>
              <td style="color:#b45309;font-weight:600">${l}</td>
              <td>${total}</td>
              <td><div style="display:flex;align-items:center;gap:7px">
                <div class="pb" style="width:65px"><div class="pf" style="width:${pct}%;background:${pct>=80?'linear-gradient(90deg,var(--blue),var(--emerald))':'linear-gradient(90deg,var(--amber),var(--rose))'}"></div></div>
                <b style="font-size:12px;color:${pct>=80?'var(--emerald)':'var(--rose)'}">${total?pct+'%':'—'}</b>
              </div></td>
            </tr>`;
          }).join('')}
          </tbody>
        </table></div>`;
      })()}
    </div>`;
  };

  window.taDate = d => { selDate=d; render(); };
  window.taMonthChange = m => { selDate=m+'-01'; render(); };
  window.taSetStatus = (tid, date, status) => {
    if (!store.teacherAttendance) store.teacherAttendance={};
    if (!store.teacherAttendance[tid]) store.teacherAttendance[tid]={};
    store.teacherAttendance[tid][date] = status;
    render();
  };
  window.taMarkAll = (status, date) => {
    if (!store.teacherAttendance) store.teacherAttendance={};
    store.teachers.forEach(t => {
      if (!store.teacherAttendance[t.id]) store.teacherAttendance[t.id]={};
      store.teacherAttendance[t.id][date] = status;
    });
    render();
  };
  window.taSave = date => { saveStore(); notify('Teacher attendance saved for '+date+'!'); };
  window.exportTeacherAtt = function() {
    const dates=[...new Set(Object.values(store.teacherAttendance||{}).flatMap(r=>Object.keys(r)))].sort();
    const data=store.teachers.map(t=>[t.id,t.name,t.subject||'',t.class||'',...dates.map(d=>(store.teacherAttendance[t.id]||{})[d]||'—')]);
    exportToExcel(data,['ID','Name','Subject','Class',...dates],'SV_TeacherAttendance_'+new Date().toISOString().slice(0,10));
  };
  render();
}

// ═══════════════════════════════════════════════
//  ADMIN — STOCK & STATIONERY
// ═══════════════════════════════════════════════
const STOCK_CATS = ['Stationery','Books & Textbooks','Lab Equipment','Sports Equipment','Furniture','Electronics','Cleaning Supplies','Office Supplies','Printing & Paper','Other'];

function renderAdminStock() {
  const low = (store.stock||[]).filter(s=>s.quantity<=s.minStock);
  area().innerHTML = `
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📦 Stock & Stationery</div>
      <div style="font-size:12px;color:var(--gray-400)">${(store.stock||[]).length} items · ${low.length} low stock alerts</div></div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="dl-btn" onclick="exportStock()">📥 Export</button>
      <button class="btn btn-primary" onclick="showAddStock()">+ Add Item</button>
    </div>
  </div>

  ${low.length>0?`<div style="background:rgba(244,63,94,.08);border:1px solid rgba(244,63,94,.25);border-radius:12px;padding:14px 18px;margin-bottom:16px;display:flex;align-items:center;gap:12px;flex-wrap:wrap">
    <span style="font-size:20px">⚠️</span>
    <div>
      <div style="font-weight:700;font-size:13px;color:var(--rose)">Low Stock Alert — ${low.length} item${low.length>1?'s':''}</div>
      <div style="font-size:12px;color:var(--gray-600);margin-top:2px">${low.map(s=>`<b>${s.name}</b> (${s.quantity} ${s.unit||'units'} left)`).join(', ')}</div>
    </div>
  </div>`:''}

  <div class="stat-grid" style="margin-bottom:16px">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">📦</div><div class="stat-info"><h3>${(store.stock||[]).length}</h3><p>Total Items</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>${(store.stock||[]).filter(s=>s.quantity>s.minStock).length}</h3><p>In Stock</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(244,63,94,.1)">⚠️</div><div class="stat-info"><h3>${low.length}</h3><p>Low Stock</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">💰</div><div class="stat-info"><h3>₹${(store.stock||[]).reduce((a,s)=>a+(s.price||0)*s.quantity,0).toLocaleString()}</h3><p>Stock Value</p></div></div>
  </div>

  <div class="card">
    <div class="card-hdr"><div class="card-title">Inventory List</div></div>
    ${!(store.stock||[]).length?`<div class="empty"><div class="ei">📦</div><p>No items in stock. Click "Add Item" to begin.</p></div>`:`
    <div class="tbl-wrap"><table>
      <thead><tr><th>Item Name</th><th>Category</th><th>Quantity</th><th>Unit</th><th>Min Stock</th><th>Unit Price</th><th>Total Value</th><th>Last Updated</th><th>Status</th><th>Actions</th></tr></thead>
      <tbody>
        ${(store.stock||[]).map(s=>{
          const low = s.quantity<=s.minStock;
          return `<tr style="${low?'background:rgba(244,63,94,.03)':''}">
            <td><div style="font-weight:600">${s.name}</div>${s.notes?`<div style="font-size:10px;color:var(--gray-400)">${s.notes}</div>`:''}
            <td><span class="badge b-blue">${s.category||'—'}</span></td>
            <td><b style="font-size:15px;color:${low?'var(--rose)':'var(--navy)'}">${s.quantity}</b></td>
            <td style="font-size:12px">${s.unit||'pcs'}</td>
            <td style="font-size:12px;color:var(--gray-400)">${s.minStock||0}</td>
            <td>₹${(s.price||0).toLocaleString()}</td>
            <td><b>₹${((s.price||0)*s.quantity).toLocaleString()}</b></td>
            <td style="font-size:11px;color:var(--gray-400)">${fmtDate(s.updatedAt||s.addedAt)}</td>
            <td><span class="badge ${low?'b-red':'b-green'}">${low?'⚠️ Low':'✅ OK'}</span></td>
            <td><div style="display:flex;gap:4px">
              <button class="btn btn-success btn-xs" onclick="stockAddQty('${s.id}')">+Add</button>
              <button class="btn btn-warning btn-xs" onclick="editStock('${s.id}')">Edit</button>
              <button class="btn btn-danger btn-xs" onclick="deleteStock('${s.id}')">Del</button>
            </div></td>
          </tr>`;
        }).join('')}
      </tbody>
    </table></div>`}
  </div>`;
}

function showAddStock() {
  openMo('📦 Add Stock Item',`
    <div class="form-grid">
      <div class="fg"><label>Item Name *</label><input class="fc" id="sk_name" placeholder="e.g. A4 Paper Ream"></div>
      <div class="fg"><label>Category</label><select class="fc" id="sk_cat">${STOCK_CATS.map(c=>`<option>${c}</option>`).join('')}</select></div>
      <div class="fg"><label>Quantity *</label><input class="fc" id="sk_qty" type="number" placeholder="0" min="0"></div>
      <div class="fg"><label>Unit</label><input class="fc" id="sk_unit" placeholder="pcs / reams / boxes / sets"></div>
      <div class="fg"><label>Minimum Stock (alert level)</label><input class="fc" id="sk_min" type="number" value="5" min="0"></div>
      <div class="fg"><label>Unit Price (₹)</label><input class="fc" id="sk_price" type="number" placeholder="0" min="0"></div>
      <div class="fg form-full"><label>Notes</label><input class="fc" id="sk_notes" placeholder="Supplier, location, etc."></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveNewStock()">Add Item</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function saveNewStock(){
  const name=document.getElementById('sk_name').value.trim();
  const qty=parseInt(document.getElementById('sk_qty').value)||0;
  if(!name){alert('Item name required');return;}
  if(!store.stock)store.stock=[];
  store.stock.push({id:'STK'+Date.now(),name,category:document.getElementById('sk_cat').value,quantity:qty,unit:document.getElementById('sk_unit').value||'pcs',minStock:parseInt(document.getElementById('sk_min').value)||5,price:parseFloat(document.getElementById('sk_price').value)||0,notes:document.getElementById('sk_notes').value,addedAt:new Date().toISOString().slice(0,10),updatedAt:new Date().toISOString().slice(0,10)});
  saveStore();closeMo();notify('Stock item added!');renderAdminStock();
}
function editStock(id){
  const s=(store.stock||[]).find(x=>x.id===id);if(!s)return;
  openMo(`Edit — ${s.name}`,`
    <div class="form-grid">
      <div class="fg"><label>Item Name</label><input class="fc" id="sk_name" value="${s.name}"></div>
      <div class="fg"><label>Category</label><select class="fc" id="sk_cat">${STOCK_CATS.map(c=>`<option ${c===s.category?'selected':''}>${c}</option>`).join('')}</select></div>
      <div class="fg"><label>Quantity</label><input class="fc" id="sk_qty" type="number" value="${s.quantity}" min="0"></div>
      <div class="fg"><label>Unit</label><input class="fc" id="sk_unit" value="${s.unit||'pcs'}"></div>
      <div class="fg"><label>Min Stock</label><input class="fc" id="sk_min" type="number" value="${s.minStock||5}" min="0"></div>
      <div class="fg"><label>Unit Price (₹)</label><input class="fc" id="sk_price" type="number" value="${s.price||0}"></div>
      <div class="fg form-full"><label>Notes</label><input class="fc" id="sk_notes" value="${s.notes||''}"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveEditStock('${id}')">Update</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function saveEditStock(id){
  const s=(store.stock||[]).find(x=>x.id===id);if(!s)return;
  s.name=document.getElementById('sk_name').value;s.category=document.getElementById('sk_cat').value;
  s.quantity=parseInt(document.getElementById('sk_qty').value)||0;s.unit=document.getElementById('sk_unit').value;
  s.minStock=parseInt(document.getElementById('sk_min').value)||5;s.price=parseFloat(document.getElementById('sk_price').value)||0;
  s.notes=document.getElementById('sk_notes').value;s.updatedAt=new Date().toISOString().slice(0,10);
  saveStore();closeMo();notify('Updated!');renderAdminStock();
}
function stockAddQty(id){
  const s=(store.stock||[]).find(x=>x.id===id);if(!s)return;
  const n=prompt(`Add quantity to "${s.name}" (current: ${s.quantity} ${s.unit||'pcs'}):`,'10');
  if(n===null)return;
  const add=parseInt(n)||0;s.quantity+=add;s.updatedAt=new Date().toISOString().slice(0,10);
  saveStore();notify(`Added ${add} ${s.unit||'pcs'} to ${s.name}!`);renderAdminStock();
}
function deleteStock(id){if(!confirm('Delete this item?'))return;store.stock=(store.stock||[]).filter(x=>x.id!==id);saveStore();notify('Deleted.','🗑️');renderAdminStock();}
window.exportStock=function(){
  const data=(store.stock||[]).map(s=>[s.name,s.category||'',s.quantity,s.unit||'pcs',s.minStock||0,'₹'+s.price,s.quantity<=s.minStock?'Low':'OK',s.notes||'',fmtDate(s.updatedAt)]);
  exportToExcel(data,['Item','Category','Qty','Unit','Min Stock','Unit Price','Status','Notes','Updated'],'SV_Stock_'+new Date().toISOString().slice(0,10));
};

// ═══════════════════════════════════════════════
//  ADMIN — CERTIFICATES
// ═══════════════════════════════════════════════
function renderAdminCertificates() {
  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">🏆 Competition Certificates</div>
      <div style="font-size:12px;color:var(--gray-400)">${(store.certificates||[]).length} certificates issued</div></div>
    <button class="btn btn-primary" onclick="showCreateCert()">+ Create Certificate</button>
  </div>
  ${!(store.certificates||[]).length?`<div class="card"><div class="empty"><div class="ei">🏆</div><p>No certificates created yet. Click "Create Certificate" to start.</p></div></div>`:`
  <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:16px">
    ${(store.certificates||[]).slice().reverse().map(c=>`
    <div style="background:white;border:1px solid var(--gray-200);border-radius:14px;overflow:hidden;box-shadow:var(--shadow)">
      <div style="background:linear-gradient(135deg,#7c3aed,#4f46e5);padding:14px;color:white;text-align:center">
        <div style="font-size:28px;margin-bottom:4px">🏆</div>
        <div style="font-weight:700;font-size:13px">${c.event}</div>
        <div style="opacity:.7;font-size:11px;margin-top:3px">${fmtDate(c.date)}</div>
      </div>
      <div style="padding:14px">
        <div style="font-weight:700;font-size:15px;color:var(--navy);margin-bottom:4px">${c.studentName}</div>
        <div style="font-size:12px;color:var(--gray-500);margin-bottom:3px">${c.class||''} ${c.position?'· '+c.position:''}</div>
        <div style="font-size:11.5px;color:var(--gray-600);line-height:1.5;margin-bottom:10px">${c.description||''}</div>
        <div style="display:flex;gap:6px">
          <button class="btn btn-primary btn-xs" onclick="viewCertificate('${c.id}')">🏆 View</button>
          <button class="btn btn-violet btn-xs" onclick="printCertificate('${c.id}')">🖨️ Print</button>
          <button class="btn btn-danger btn-xs" onclick="deleteCert('${c.id}')">Del</button>
        </div>
      </div>
    </div>`).join('')}
  </div>`}`;
}

function showCreateCert(){
  openMo('🏆 Create Competition Certificate',`
    <div class="form-grid">
      <div class="fg"><label>Student Name *</label>
        <select class="fc" id="ct_student" onchange="fillCertStudent(this.value)">
          <option value="">Select student or type below</option>
          ${store.students.map(s=>`<option value="${s.id}">${s.name} (${s.class})</option>`).join('')}
          <option value="__custom__">— Enter manually</option>
        </select>
      </div>
      <div class="fg" id="ct_custom_wrap" style="display:none"><label>Custom Name</label><input class="fc" id="ct_custom_name" placeholder="Full name"></div>
      <div class="fg"><label>Class / Standard</label><input class="fc" id="ct_class" placeholder="e.g. 10A"></div>
      <div class="fg"><label>Event / Competition *</label><input class="fc" id="ct_event" placeholder="e.g. Science Quiz, Drawing Competition"></div>
      <div class="fg"><label>Position / Achievement</label><input class="fc" id="ct_position" placeholder="e.g. 1st Place, Best Participant"></div>
      <div class="fg"><label>Date</label><input class="fc" id="ct_date" type="date" value="${new Date().toISOString().slice(0,10)}"></div>
      <div class="fg form-full"><label>Description / Achievement Details</label><textarea class="fc" id="ct_desc" placeholder="Having demonstrated excellent skills and performance in..."></textarea></div>
      <div class="fg"><label>Issued By / Authority</label><input class="fc" id="ct_by" value="Principal, S.V. Vidhyalay"></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveCreateCert()">Create Certificate</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
  document.getElementById('ct_student').addEventListener('change',function(){
    document.getElementById('ct_custom_wrap').style.display=this.value==='__custom__'?'block':'none';
  });
}
window.fillCertStudent=function(sid){
  if(!sid||sid==='__custom__')return;
  const s=store.students.find(x=>x.id===sid);
  if(s)document.getElementById('ct_class').value=s.class||'';
};
function saveCreateCert(){
  const sel=document.getElementById('ct_student').value;
  const customName=document.getElementById('ct_custom_name')?.value.trim();
  let studentName='',sClass='';
  if(sel&&sel!=='__custom__'){const s=store.students.find(x=>x.id===sel);studentName=s?.name||'';sClass=s?.class||'';}
  else if(customName){studentName=customName;}
  else{alert('Student name required');return;}
  const event=document.getElementById('ct_event').value.trim();
  if(!event){alert('Event name required');return;}
  if(!store.certificates)store.certificates=[];
  store.certificates.push({
    id:'CERT'+Date.now(),
    studentId:sel!=='__custom__'?sel:null,
    studentName,
    class:document.getElementById('ct_class').value||sClass,
    event,position:document.getElementById('ct_position').value,
    date:document.getElementById('ct_date').value,
    description:document.getElementById('ct_desc').value,
    issuedBy:document.getElementById('ct_by').value,
    createdAt:new Date().toISOString()
  });
  saveStore();closeMo();notify('Certificate created!','🏆');renderAdminCertificates();
}
function deleteCert(id){if(!confirm('Delete certificate?'))return;store.certificates=(store.certificates||[]).filter(c=>c.id!==id);saveStore();notify('Deleted.','🗑️');renderAdminCertificates();}

function viewCertificate(id){
  const c=(store.certificates||[]).find(x=>x.id===id);if(!c)return;
  openMo('🏆 Certificate Preview',`
    <div id="certPrint" style="background:white;border-radius:4px;padding:0">
      ${certHTML(c)}
    </div>
    <div style="display:flex;gap:8px;margin-top:16px">
      <button class="btn btn-primary" onclick="printCertificate('${c.id}')">🖨️ Print</button>
      <button class="btn btn-outline" onclick="closeMo()">Close</button>
    </div>
  `);
}

function certHTML(c){
  return `<div style="border:8px double #7c3aed;border-radius:8px;padding:36px 40px;text-align:center;font-family:'Playfair Display',Georgia,serif;background:linear-gradient(135deg,#fafafa 0%,#f3f0ff 100%);position:relative;min-width:580px;max-width:700px;margin:0 auto">
    <!-- Corner decorations -->
    <div style="position:absolute;top:12px;left:12px;width:36px;height:36px;border-top:3px solid #7c3aed;border-left:3px solid #7c3aed"></div>
    <div style="position:absolute;top:12px;right:12px;width:36px;height:36px;border-top:3px solid #7c3aed;border-right:3px solid #7c3aed"></div>
    <div style="position:absolute;bottom:12px;left:12px;width:36px;height:36px;border-bottom:3px solid #7c3aed;border-left:3px solid #7c3aed"></div>
    <div style="position:absolute;bottom:12px;right:12px;width:36px;height:36px;border-bottom:3px solid #7c3aed;border-right:3px solid #7c3aed"></div>
    <!-- Logo + School -->
    <div style="display:flex;align-items:center;justify-content:center;gap:14px;margin-bottom:18px">
      <img src="${LOGO_URI}" style="width:60px;height:60px;border-radius:12px;object-fit:cover">
      <div style="text-align:left">
        <div style="font-size:22px;font-weight:700;color:#0f1729;letter-spacing:.5px">S.V. Vidhyalay</div>
        <div style="font-size:11px;color:#64748b;letter-spacing:.3px">सा विद्या या विमुक्ते</div>
      </div>
    </div>
    <div style="color:#7c3aed;font-size:13px;font-weight:700;text-transform:uppercase;letter-spacing:3px;margin-bottom:14px">Certificate of Achievement</div>
    <div style="font-size:13px;color:#475569;margin-bottom:8px">This is to certify that</div>
    <div style="font-size:30px;font-weight:700;color:#0f1729;border-bottom:2px solid #7c3aed;display:inline-block;padding-bottom:4px;margin-bottom:14px">${c.studentName}</div>
    <div style="font-size:12.5px;color:#475569;margin-bottom:6px">${c.class?`Class: <b style="color:#0f1729">${c.class}</b> &nbsp;·&nbsp;`:''} S.V. Vidhyalay</div>
    <div style="font-size:13px;color:#475569;margin-bottom:8px">has successfully participated and excelled in</div>
    <div style="font-size:20px;font-weight:700;color:#4f46e5;margin-bottom:6px">${c.event}</div>
    ${c.position?`<div style="display:inline-block;background:linear-gradient(135deg,#7c3aed,#4f46e5);color:white;padding:6px 22px;border-radius:30px;font-size:14px;font-weight:700;margin-bottom:14px">🏆 ${c.position}</div>`:''}
    ${c.description?`<div style="font-size:12px;color:#64748b;margin:10px auto 14px;max-width:420px;line-height:1.6;font-style:italic">"${c.description}"</div>`:''}
    <div style="margin-top:20px;padding-top:16px;border-top:1px dashed #c4b5fd;display:flex;justify-content:space-between;align-items:flex-end">
      <div style="text-align:center">
        <div style="font-size:12px;color:#94a3b8;margin-bottom:4px">Date</div>
        <div style="font-size:13px;font-weight:700;color:#0f1729">${fmtDate(c.date)}</div>
      </div>
      <div style="text-align:center">
        <div style="width:100px;border-top:1px solid #0f1729;padding-top:6px">
          <div style="font-size:11px;font-weight:700;color:#0f1729">${c.issuedBy||'Principal'}</div>
          <div style="font-size:10px;color:#94a3b8">Authorized Signatory</div>
        </div>
      </div>
    </div>
  </div>`;
}

window.printCertificate=function(id){
  const c=(store.certificates||[]).find(x=>x.id===id);if(!c)return;
  const html=`<!DOCTYPE html><html><head><title>Certificate - ${c.studentName}</title>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&display=swap" rel="stylesheet">
    <style>body{margin:0;padding:24px;background:#fff;display:flex;justify-content:center} @media print{body{padding:0}}</style>
    </head><body>${certHTML(c)}</body></html>`;
  const iframe=document.createElement('iframe');
  iframe.style.cssText='position:fixed;top:-9999px;left:-9999px;width:0;height:0;border:none;visibility:hidden';
  document.body.appendChild(iframe);
  iframe.contentDocument.open();
  iframe.contentDocument.write(html);
  iframe.contentDocument.close();
  setTimeout(()=>{iframe.contentWindow.focus();iframe.contentWindow.print();setTimeout(()=>document.body.removeChild(iframe),1000);},600);
};

// ═══════════════════════════════════════════════
//  ADMIN — TIMETABLE
// ═══════════════════════════════════════════════
const DAYS = ['Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'];
const PERIODS = ['Period 1','Period 2','Period 3','Period 4','Period 5','Period 6','Period 7','Period 8'];

function renderAdminTimetable() {
  const classes=[...new Set(store.students.map(s=>s.class))].concat((store.classes||[]).map(c=>c.name)).filter((v,i,a)=>a.indexOf(v)===i).filter(Boolean);
  let selClass=classes[0]||'';

  const render=()=>{
    const tt=((store.timetable||{})[selClass])||{};
    area().innerHTML=`
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📅 Timetable Manager</div>
        <div style="font-size:12px;color:var(--gray-400)">Create and manage class timetables</div></div>
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <select class="fc" style="width:140px" onchange="ttClass(this.value)">
          ${classes.length?classes.map(c=>`<option value="${c}" ${c===selClass?'selected':''}>${c}</option>`).join(''):'<option value="">No classes</option>'}
        </select>
        <button class="btn btn-success btn-sm" onclick="showAddTTPeriod('${selClass}')">+ Add Period</button>
        ${selClass?`<button class="btn btn-danger btn-sm" onclick="clearTimetable('${selClass}')">🗑️ Clear</button>`:''}
      </div>
    </div>
    ${classes.length===0?`<div class="card"><div class="empty"><div class="ei">📅</div><p>No classes found. Add students or classes first.</p></div></div>`:`
    <div class="card" style="overflow-x:auto">
      <table style="width:100%;border-collapse:collapse;min-width:700px">
        <thead><tr style="background:linear-gradient(135deg,var(--navy),var(--navy2))">
          <th style="padding:10px 14px;text-align:left;color:rgba(255,255,255,.8);font-size:11px;font-weight:600;text-transform:uppercase;letter-spacing:.5px;min-width:90px">Period</th>
          ${DAYS.map(d=>`<th style="padding:10px 14px;text-align:center;color:rgba(255,255,255,.8);font-size:11px;font-weight:600;text-transform:uppercase;letter-spacing:.5px">${d}</th>`).join('')}
        </tr></thead>
        <tbody>
          ${PERIODS.map((period,pi)=>`<tr style="${pi%2===0?'background:#fafbff':'background:white'}">
            <td style="padding:10px 14px;font-weight:600;font-size:12px;color:var(--navy);border-bottom:1px solid var(--gray-100)">${period}</td>
            ${DAYS.map(day=>{
              const cell=(tt[day]||[]).find(p=>p.period===period);
              return `<td style="padding:8px;border-bottom:1px solid var(--gray-100);text-align:center">
                ${cell?`<div style="background:linear-gradient(135deg,${subjectColor(cell.subject)});border-radius:8px;padding:7px 10px;cursor:pointer" onclick="editTTPeriod('${selClass}','${day}','${period}')">
                  <div style="font-weight:700;font-size:12px;color:white">${cell.subject}</div>
                  ${cell.teacher?`<div style="font-size:10px;color:rgba(255,255,255,.75);margin-top:1px">👤 ${cell.teacher}</div>`:''}
                  ${cell.time?`<div style="font-size:10px;color:rgba(255,255,255,.6)">${cell.time}</div>`:''}
                </div>
                <button onclick="deleteTTPeriod('${selClass}','${day}','${period}')" style="background:none;border:none;font-size:10px;color:var(--rose);cursor:pointer;margin-top:3px">✕ remove</button>`
                :`<button onclick="showAddTTPeriod('${selClass}','${day}','${period}')" style="width:100%;padding:8px;background:var(--gray-50);border:1.5px dashed var(--gray-300);border-radius:7px;color:var(--gray-400);font-size:11px;cursor:pointer;transition:all .2s" onmouseover="this.style.background='rgba(37,99,235,.06)';this.style.borderColor='var(--blue)'" onmouseout="this.style.background='var(--gray-50)';this.style.borderColor='var(--gray-300)'">+ Add</button>`}
              </td>`;
            }).join('')}
          </tr>`).join('')}
        </tbody>
      </table>
    </div>`}`;
  };

  function subjectColor(subj){
    const colors=[['#2563eb,#1d4ed8'],['#7c3aed,#6d28d9'],['#059669,#047857'],['#dc2626,#b91c1c'],['#d97706,#b45309'],['#0891b2,#0e7490'],['#db2777,#be185d'],['#65a30d,#4d7c0f']];
    let h=0;for(let i=0;i<(subj||'').length;i++)h=(h*31+subj.charCodeAt(i))&0xffff;
    return colors[h%colors.length][0];
  }

  window.ttClass=c=>{selClass=c;render();};
  window.clearTimetable=cls=>{if(!confirm('Clear entire timetable for '+cls+'?'))return;if(store.timetable)delete store.timetable[cls];saveStore();notify('Timetable cleared.');render();};

  window.showAddTTPeriod=(cls,preDay,prePeriod)=>{
    openMo('Add Timetable Period',`
      <div class="form-grid">
        <div class="fg"><label>Day</label>
          <select class="fc" id="ttp_day">${DAYS.map(d=>`<option ${d===preDay?'selected':''}>${d}</option>`).join('')}</select>
        </div>
        <div class="fg"><label>Period</label>
          <select class="fc" id="ttp_period">${PERIODS.map(p=>`<option ${p===prePeriod?'selected':''}>${p}</option>`).join('')}</select>
        </div>
        <div class="fg"><label>Subject *</label><input class="fc" id="ttp_sub" placeholder="e.g. Mathematics"></div>
        <div class="fg"><label>Teacher</label>
          <select class="fc" id="ttp_teacher">
            <option value="">Select teacher</option>
            ${store.teachers.map(t=>`<option value="${t.name}">${t.name} (${t.subject||'—'})</option>`).join('')}
          </select>
        </div>
        <div class="fg"><label>Time</label><input class="fc" id="ttp_time" placeholder="e.g. 9:00–9:45"></div>
        <div class="fg"><label>Room</label><input class="fc" id="ttp_room" placeholder="e.g. Room 201"></div>
      </div>
      <div style="display:flex;gap:8px;margin-top:14px">
        <button class="btn btn-primary" onclick="saveTTPeriod('${cls}')">Save Period</button>
        <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
      </div>
    `);
  };

  window.editTTPeriod=(cls,day,period)=>{
    const tt=(store.timetable||{})[cls]||{};
    const cell=(tt[day]||[]).find(p=>p.period===period);
    if(!cell)return;
    openMo('Edit Period',`
      <div class="form-grid">
        <div class="fg"><label>Subject</label><input class="fc" id="ttp_sub" value="${cell.subject}"></div>
        <div class="fg"><label>Teacher</label>
          <select class="fc" id="ttp_teacher">
            <option value="">None</option>
            ${store.teachers.map(t=>`<option value="${t.name}" ${t.name===cell.teacher?'selected':''}>${t.name}</option>`).join('')}
          </select>
        </div>
        <div class="fg"><label>Time</label><input class="fc" id="ttp_time" value="${cell.time||''}"></div>
        <div class="fg"><label>Room</label><input class="fc" id="ttp_room" value="${cell.room||''}"></div>
      </div>
      <div style="display:flex;gap:8px;margin-top:14px">
        <button class="btn btn-primary" onclick="updateTTPeriod('${cls}','${day}','${period}')">Update</button>
        <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
      </div>
    `);
  };

  window.updateTTPeriod=(cls,day,period)=>{
    if(!store.timetable)store.timetable={};
    if(!store.timetable[cls])store.timetable[cls]={};
    if(!store.timetable[cls][day])store.timetable[cls][day]=[];
    const idx=store.timetable[cls][day].findIndex(p=>p.period===period);
    const obj={period,subject:document.getElementById('ttp_sub').value,teacher:document.getElementById('ttp_teacher').value,time:document.getElementById('ttp_time').value,room:document.getElementById('ttp_room').value};
    if(idx>=0)store.timetable[cls][day][idx]=obj;
    saveStore();closeMo();notify('Period updated!');render();
  };

  window.saveTTPeriod=cls=>{
    if(!store.timetable)store.timetable={};
    if(!store.timetable[cls])store.timetable[cls]={};
    const day=document.getElementById('ttp_day').value;
    const period=document.getElementById('ttp_period').value;
    if(!store.timetable[cls][day])store.timetable[cls][day]=[];
    const existing=store.timetable[cls][day].findIndex(p=>p.period===period);
    const obj={period,subject:document.getElementById('ttp_sub').value,teacher:document.getElementById('ttp_teacher').value,time:document.getElementById('ttp_time').value,room:document.getElementById('ttp_room').value};
    if(existing>=0)store.timetable[cls][day][existing]=obj;
    else store.timetable[cls][day].push(obj);
    saveStore();closeMo();notify('Period saved!');render();
  };

  window.deleteTTPeriod=(cls,day,period)=>{
    if(!confirm('Remove this period?'))return;
    if(store.timetable&&store.timetable[cls]&&store.timetable[cls][day])
      store.timetable[cls][day]=store.timetable[cls][day].filter(p=>p.period!==period);
    saveStore();render();
  };

  render();
}

// ── Teacher/Student view timetable ──────────────────────────────────────────
function renderViewTimetable() {
  const role=currentUser.role;
  const cls=role==='teacher'?(currentUser.class||''):currentUser.class;
  const tt=(store.timetable||{})[cls]||{};
  const today=DAYS[Math.min(new Date().getDay()-1,5)] || 'Monday';
  const hasTT=Object.keys(tt).length>0;

  area().innerHTML=`
  <div class="sec-hero">
    <div><h2>📅 ${role==='teacher'?'My ':''}Timetable</h2><p>Class ${cls||'—'} · S.V. Vidhyalay</p></div>
    <div style="text-align:right;color:white"><div style="font-size:14px;font-weight:600">${today}</div><div style="font-size:11px;opacity:.5">${new Date().toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'})}</div></div>
  </div>
  ${!cls?`<div class="card"><div class="empty"><div class="ei">📅</div><p>No class assigned yet</p></div></div>`:`
  ${!hasTT?`<div class="card"><div class="empty"><div class="ei">📅</div><p>No timetable created yet for Class ${cls}.<br>Please contact admin.</p></div></div>`:`
  <div class="card" style="overflow-x:auto">
    <table style="width:100%;border-collapse:collapse;min-width:700px">
      <thead><tr style="background:linear-gradient(135deg,var(--navy),var(--navy2))">
        <th style="padding:10px 14px;text-align:left;color:rgba(255,255,255,.8);font-size:11px;font-weight:600;text-transform:uppercase;letter-spacing:.5px">Period</th>
        ${DAYS.map(d=>`<th style="padding:10px 14px;text-align:center;color:rgba(255,255,255,.8);font-size:11px;font-weight:600;text-transform:uppercase;letter-spacing:.5px;${d===today?'background:rgba(255,255,255,.1)':''}">${d}${d===today?'<div style="font-size:9px;color:rgba(255,255,255,.5)">TODAY</div>':''}</th>`).join('')}
      </tr></thead>
      <tbody>
        ${PERIODS.map((period,pi)=>`<tr style="${pi%2===0?'background:#fafbff':'background:white'}">
          <td style="padding:10px 14px;font-weight:600;font-size:12px;color:var(--navy);border-bottom:1px solid var(--gray-100);white-space:nowrap">${period}</td>
          ${DAYS.map(day=>{
            const cell=(tt[day]||[]).find(p=>p.period===period);
            const isToday=day===today;
            const colors=['#2563eb','#7c3aed','#059669','#dc2626','#d97706','#0891b2','#db2777','#65a30d'];
            let h=0;for(let i=0;i<(cell?.subject||'').length;i++)h=(h*31+(cell?.subject||'').charCodeAt(i))&0xffff;
            const col=colors[h%colors.length];
            return `<td style="padding:8px;border-bottom:1px solid var(--gray-100);text-align:center;${isToday?'background:rgba(37,99,235,.02)':''}">
              ${cell?`<div style="background:${col};border-radius:8px;padding:8px 10px;${isToday?'box-shadow:0 2px 8px rgba(0,0,0,.15)':''}">
                <div style="font-weight:700;font-size:12px;color:white">${cell.subject}</div>
                ${cell.teacher?`<div style="font-size:10px;color:rgba(255,255,255,.75);margin-top:1px">👤 ${cell.teacher}</div>`:''}
                ${cell.time?`<div style="font-size:10px;color:rgba(255,255,255,.6)">${cell.time}</div>`:''}
                ${cell.room?`<div style="font-size:10px;color:rgba(255,255,255,.55)">📍${cell.room}</div>`:''}
              </div>
              `:`<div style="color:var(--gray-300);font-size:18px;padding:6px">—</div>`}
            </td>`;
          }).join('')}
        </tr>`).join('')}
      </tbody>
    </table>
  </div>`}`}`;
}

// ═══════════════════════════════════════════════
//  ADMIN — EXAM SCHEDULE
// ═══════════════════════════════════════════════
function renderAdminExamSchedule() {
  area().innerHTML=`
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
    <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📝 Exam Schedule</div>
      <div style="font-size:12px;color:var(--gray-400)">${(store.examSchedule||[]).length} exam entries</div></div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="dl-btn" onclick="exportExamSchedule()">📥 Export</button>
      <button class="btn btn-primary" onclick="showAddExamSchedule()">+ Add Exam</button>
    </div>
  </div>

  ${!(store.examSchedule||[]).length?`<div class="card"><div class="empty"><div class="ei">📝</div><p>No exam schedule created yet. Click "Add Exam" to begin.</p></div></div>`:`
  <div class="card">
    <div class="tbl-wrap"><table>
      <thead><tr><th>Subject</th><th>Class</th><th>Exam Type</th><th>Date</th><th>Time</th><th>Duration</th><th>Venue</th><th>Max Marks</th><th>Status</th><th>Actions</th></tr></thead>
      <tbody>
        ${(store.examSchedule||[]).slice().sort((a,b)=>new Date(a.date)-new Date(b.date)).map(e=>{
          const isPast=new Date(e.date)<new Date();
          const isToday=e.date===new Date().toISOString().slice(0,10);
          return `<tr style="${isToday?'background:rgba(37,99,235,.04)':''}">
            <td><b style="color:var(--navy)">${e.subject}</b></td>
            <td><span class="badge b-blue">${e.class}</span></td>
            <td><span class="badge b-violet">${e.type||'—'}</span></td>
            <td><span style="font-weight:600;color:${isToday?'var(--blue)':isPast?'var(--gray-400)':'var(--navy)'}">${fmtDate(e.date)}${isToday?' 🔴':''}</span></td>
            <td style="font-size:12px">${e.time||'—'}</td>
            <td style="font-size:12px">${e.duration||'—'}</td>
            <td style="font-size:12px">${e.venue||'—'}</td>
            <td><b>${e.maxMarks||100}</b></td>
            <td><span class="badge ${isToday?'b-blue':isPast?'b-gray':'b-amber'}">${isToday?'Today':isPast?'Done':'Upcoming'}</span></td>
            <td><div style="display:flex;gap:4px">
              <button class="btn btn-warning btn-xs" onclick="editExamSchedule('${e.id}')">Edit</button>
              <button class="btn btn-danger btn-xs" onclick="deleteExamSchedule('${e.id}')">Del</button>
            </div></td>
          </tr>`;
        }).join('')}
      </tbody>
    </table></div>
  </div>`}`;
}

function showAddExamSchedule(editId){
  const ex=editId?(store.examSchedule||[]).find(x=>x.id===editId):null;
  const classes=[...new Set(store.students.map(s=>s.class))].concat((store.classes||[]).map(c=>c.name)).filter((v,i,a)=>a.indexOf(v)===i&&v);
  openMo(editId?'Edit Exam':'Add Exam to Schedule',`
    <div class="form-grid">
      <div class="fg"><label>Subject *</label><input class="fc" id="es_sub" placeholder="e.g. Mathematics" value="${ex?.subject||''}"></div>
      <div class="fg"><label>Class *</label>
        <select class="fc" id="es_class">
          <option value="">All Classes</option>
          ${classes.map(c=>`<option value="${c}" ${c===ex?.class?'selected':''}>${c}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Exam Type</label>
        <select class="fc" id="es_type">
          ${['Unit Test','Mid Term','Final Exam','Quiz','Practical','Oral Exam'].map(t=>`<option ${t===ex?.type?'selected':''}>${t}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Date *</label><input class="fc" id="es_date" type="date" value="${ex?.date||''}"></div>
      <div class="fg"><label>Start Time</label><input class="fc" id="es_time" type="time" value="${ex?.time||''}"></div>
      <div class="fg"><label>Duration</label><input class="fc" id="es_dur" placeholder="e.g. 3 hrs" value="${ex?.duration||''}"></div>
      <div class="fg"><label>Max Marks</label><input class="fc" id="es_marks" type="number" value="${ex?.maxMarks||100}"></div>
      <div class="fg"><label>Venue / Room</label><input class="fc" id="es_venue" placeholder="e.g. Exam Hall A" value="${ex?.venue||''}"></div>
      <div class="fg form-full"><label>Instructions / Notes</label><textarea class="fc" id="es_notes" style="min-height:55px" placeholder="Bring ID card, calculator allowed...">${ex?.notes||''}</textarea></div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="${editId?`saveEditExamSched('${editId}')`:'saveNewExamSched()'}">Save</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
}
function getExamSchedData(){
  return {subject:document.getElementById('es_sub').value.trim(),class:document.getElementById('es_class').value,type:document.getElementById('es_type').value,date:document.getElementById('es_date').value,time:document.getElementById('es_time').value,duration:document.getElementById('es_dur').value,maxMarks:parseInt(document.getElementById('es_marks').value)||100,venue:document.getElementById('es_venue').value,notes:document.getElementById('es_notes').value};
}
function saveNewExamSched(){
  const d=getExamSchedData();if(!d.subject||!d.date){alert('Subject and date required');return;}
  if(!store.examSchedule)store.examSchedule=[];
  store.examSchedule.push({id:'EXS'+Date.now(),...d});
  saveStore();closeMo();notify('Exam added to schedule!');renderAdminExamSchedule();
}
function editExamSchedule(id){showAddExamSchedule(id);}
function saveEditExamSched(id){
  const e=(store.examSchedule||[]).find(x=>x.id===id);if(!e)return;
  Object.assign(e,getExamSchedData());
  saveStore();closeMo();notify('Updated!');renderAdminExamSchedule();
}
function deleteExamSchedule(id){if(!confirm('Delete this exam?'))return;store.examSchedule=(store.examSchedule||[]).filter(x=>x.id!==id);saveStore();notify('Deleted.','🗑️');renderAdminExamSchedule();}
window.exportExamSchedule=function(){
  const data=(store.examSchedule||[]).sort((a,b)=>new Date(a.date)-new Date(b.date)).map(e=>[e.subject,e.class||'All',e.type||'',fmtDate(e.date),e.time||'',e.duration||'',e.maxMarks||100,e.venue||'',e.notes||'']);
  exportToExcel(data,['Subject','Class','Type','Date','Time','Duration','Max Marks','Venue','Notes'],'SV_ExamSchedule_'+new Date().toISOString().slice(0,10));
};

// ── Teacher/Student view exam schedule ──────────────────────────────────────
function renderViewExamSchedule() {
  const role=currentUser.role;
  const cls=currentUser.class||'';
  const all=(store.examSchedule||[]).filter(e=>!e.class||e.class===cls||e.class==='');
  const upcoming=all.filter(e=>new Date(e.date)>=new Date()).sort((a,b)=>new Date(a.date)-new Date(b.date));
  const past=all.filter(e=>new Date(e.date)<new Date()).sort((a,b)=>new Date(b.date)-new Date(a.date));

  area().innerHTML=`
  <div class="sec-hero">
    <div><h2>📝 Exam Schedule</h2><p>Class ${cls||'All'} · ${upcoming.length} upcoming · ${past.length} completed</p></div>
  </div>
  ${all.length===0?`<div class="card"><div class="empty"><div class="ei">📝</div><p>No exam schedule available yet. Check back later.</p></div></div>`:`
  ${upcoming.length?`
  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr"><div class="card-title">📅 Upcoming Exams</div><span class="badge b-amber">${upcoming.length} scheduled</span></div>
    <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(240px,1fr));gap:12px">
      ${upcoming.map(e=>{
        const daysLeft=Math.ceil((new Date(e.date)-new Date())/(1000*60*60*24));
        const isToday=e.date===new Date().toISOString().slice(0,10);
        return `<div style="border:1.5px solid ${isToday?'var(--blue)':'var(--gray-200)'};border-radius:12px;padding:16px;background:${isToday?'rgba(37,99,235,.04)':'white'}">
          <div style="display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:8px">
            <div><div style="font-weight:700;font-size:14px;color:var(--navy)">${e.subject}</div>
              <span class="badge b-violet" style="margin-top:4px">${e.type||'Exam'}</span></div>
            <span style="font-size:10.5px;font-weight:700;padding:4px 9px;border-radius:20px;background:${daysLeft<=3?'rgba(244,63,94,.12)':'rgba(37,99,235,.1)'};color:${daysLeft<=3?'var(--rose)':'var(--blue)'}">${isToday?'📍 Today':daysLeft===1?'Tomorrow':'In '+daysLeft+'d'}</span>
          </div>
          <div style="font-size:12px;color:var(--gray-600);display:flex;flex-direction:column;gap:4px">
            <span>📅 ${fmtDate(e.date)}${e.time?' · ⏰ '+e.time:''}</span>
            ${e.duration?`<span>⏱ Duration: ${e.duration}</span>`:''}
            ${e.venue?`<span>📍 ${e.venue}</span>`:''}
            <span>📊 Max Marks: <b>${e.maxMarks||100}</b></span>
            ${e.notes?`<div style="margin-top:6px;padding:7px 10px;background:rgba(245,158,11,.08);border-radius:6px;font-size:11px;color:var(--gray-600)">💡 ${e.notes}</div>`:''}
          </div>
        </div>`;
      }).join('')}
    </div>
  </div>`:''}
  ${past.length?`
  <div class="card">
    <div class="card-hdr"><div class="card-title">✅ Completed Exams</div></div>
    <div class="tbl-wrap"><table>
      <thead><tr><th>Subject</th><th>Type</th><th>Date</th><th>Max Marks</th><th>Venue</th></tr></thead>
      <tbody>${past.map(e=>`<tr>
        <td><b style="color:var(--gray-600)">${e.subject}</b></td>
        <td><span class="badge b-gray">${e.type||'—'}</span></td>
        <td style="font-size:12px;color:var(--gray-400)">${fmtDate(e.date)}</td>
        <td>${e.maxMarks||100}</td>
        <td style="font-size:12px">${e.venue||'—'}</td>
      </tr>`).join('')}</tbody>
    </table></div>
  </div>`:''}
  `}`;
}

// ═══════════════════════════════════════════════
//  ADMIN — EXAM TIMETABLE GENERATOR
// ═══════════════════════════════════════════════
function renderAdminExamTimetable() {
  const classes = [...new Set(store.students.map(s=>s.class))].concat((store.classes||[]).map(c=>c.name)).filter((v,i,a)=>a.indexOf(v)===i&&v).sort();
  const allExams = store.examSchedule || [];
  const examTypes = [...new Set(allExams.map(e=>e.type).filter(Boolean))];

  // State
  let filterClass = '__all__';
  let filterType  = '__all__';
  let filterFrom  = '';
  let filterTo    = '';
  let viewMode    = 'calendar'; // 'calendar' | 'table' | 'class'

  function getFiltered() {
    return allExams.filter(e=>{
      if (filterClass !== '__all__' && e.class !== filterClass) return false;
      if (filterType  !== '__all__' && e.type  !== filterType)  return false;
      if (filterFrom  && e.date < filterFrom) return false;
      if (filterTo    && e.date > filterTo)   return false;
      return true;
    }).sort((a,b)=>new Date(a.date)-new Date(b.date));
  }

  function examTypeBadgeColor(type){
    const m={'Unit Test':'b-blue','Mid Term':'b-amber','Final Exam':'b-red','Quiz':'b-cyan','Practical':'b-violet','Oral Exam':'b-green'};
    return m[type]||'b-gray';
  }

  function subjectBg(subj){
    const cols=['#2563eb','#7c3aed','#059669','#dc2626','#d97706','#0891b2','#db2777','#65a30d','#9333ea','#0f766e'];
    let h=0;for(let i=0;i<(subj||'').length;i++)h=(h*31+subj.charCodeAt(i))&0xffff;
    return cols[h%cols.length];
  }

  function fmtDay(dateStr){
    if(!dateStr)return'';
    const d=new Date(dateStr+'T00:00:00');
    return d.toLocaleDateString('en-IN',{weekday:'long',day:'numeric',month:'short',year:'numeric'});
  }
  function dayLabel(dateStr){
    if(!dateStr)return'';
    return new Date(dateStr+'T00:00:00').toLocaleDateString('en-IN',{weekday:'short'});
  }

  function renderCalendarView(exams){
    if(!exams.length) return `<div class="empty"><div class="ei">🗓️</div><p>No exams match the selected filters. Adjust filters or add exams from Exam Schedule.</p></div>`;
    // Group by date
    const byDate={};
    exams.forEach(e=>{ if(!byDate[e.date])byDate[e.date]=[]; byDate[e.date].push(e); });
    const dates=Object.keys(byDate).sort();
    const today=new Date().toISOString().slice(0,10);

    return dates.map(date=>{
      const list=byDate[date];
      const isPast=date<today, isToday=date===today;
      const borderCol=isToday?'var(--blue)':isPast?'var(--gray-200)':'var(--amber)';
      const daysLeft=Math.ceil((new Date(date)-new Date())/(1000*60*60*24));
      return `
      <div style="margin-bottom:18px">
        <div style="display:flex;align-items:center;gap:10px;margin-bottom:10px">
          <div style="background:${isToday?'var(--blue)':isPast?'var(--gray-300)':'var(--navy)'};color:white;border-radius:10px;padding:8px 14px;min-width:54px;text-align:center">
            <div style="font-size:10px;opacity:.7;font-weight:600">${dayLabel(date)}</div>
            <div style="font-size:20px;font-weight:800;line-height:1">${new Date(date+'T00:00:00').getDate()}</div>
            <div style="font-size:9px;opacity:.65">${new Date(date+'T00:00:00').toLocaleDateString('en-IN',{month:'short',year:'2-digit'})}</div>
          </div>
          <div>
            <div style="font-weight:700;font-size:14px;color:var(--navy)">${fmtDay(date)}</div>
            <div style="font-size:11px;color:${isToday?'var(--blue)':isPast?'var(--gray-400)':'#b45309'};font-weight:600">
              ${isToday?'📍 Today':isPast?'✅ Completed':'⏳ In '+Math.max(0,daysLeft)+' day'+(daysLeft===1?'':'s')}
              · ${list.length} exam${list.length>1?'s':''}
            </div>
          </div>
        </div>
        <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(230px,1fr));gap:10px">
          ${list.map(e=>`
          <div style="border:2px solid ${isToday?'var(--blue)':isPast?'var(--gray-200)':'rgba(245,158,11,.35)'};border-radius:12px;padding:14px;background:white;position:relative;overflow:hidden;opacity:${isPast?.75:1}">
            <div style="position:absolute;top:0;left:0;right:0;height:4px;background:${subjectBg(e.subject)}"></div>
            <div style="display:flex;align-items:flex-start;justify-content:space-between;gap:8px;margin-top:2px">
              <div style="font-weight:800;font-size:14px;color:var(--navy);flex:1">${e.subject}</div>
              ${e.class?`<span class="badge b-blue" style="flex-shrink:0">${e.class}</span>`:''}
            </div>
            <div style="margin-top:6px;display:flex;flex-wrap:wrap;gap:4px">
              ${e.type?`<span class="badge ${examTypeBadgeColor(e.type)}">${e.type}</span>`:''}
            </div>
            <div style="margin-top:8px;display:flex;flex-direction:column;gap:3px;font-size:12px;color:var(--gray-600)">
              ${e.time?`<span>⏰ ${e.time}${e.duration?' · ⏱ '+e.duration:''}</span>`:''}
              ${!e.time&&e.duration?`<span>⏱ ${e.duration}</span>`:''}
              ${e.venue?`<span>📍 ${e.venue}</span>`:''}
              <span>📊 Max Marks: <b style="color:var(--navy)">${e.maxMarks||100}</b></span>
            </div>
            ${e.notes?`<div style="margin-top:8px;padding:7px 9px;background:rgba(245,158,11,.08);border-radius:7px;font-size:11px;color:var(--gray-600);line-height:1.4">💡 ${e.notes}</div>`:''}
          </div>`).join('')}
        </div>
      </div>`;
    }).join('<hr style="border:none;border-top:1px solid var(--gray-100);margin:4px 0">');
  }

  function renderTableView(exams){
    if(!exams.length) return `<div class="empty"><div class="ei">🗓️</div><p>No exams match the selected filters.</p></div>`;
    const today=new Date().toISOString().slice(0,10);
    return `<div class="tbl-wrap"><table>
      <thead><tr><th>#</th><th>Date</th><th>Day</th><th>Subject</th><th>Class</th><th>Exam Type</th><th>Time</th><th>Duration</th><th>Venue</th><th>Max Marks</th><th>Status</th></tr></thead>
      <tbody>
        ${exams.map((e,i)=>{
          const isPast=e.date<today,isToday=e.date===today;
          return `<tr style="${isToday?'background:rgba(37,99,235,.04)':isPast?'opacity:.7':''}">
            <td style="color:var(--gray-400);font-size:11px">${i+1}</td>
            <td><b style="color:${isToday?'var(--blue)':isPast?'var(--gray-400)':'var(--navy)'}">${fmtDate(e.date)}</b>${isToday?` <span style="color:var(--blue);font-size:10px">●</span>`:''}</td>
            <td style="font-size:12px;color:var(--gray-500)">${dayLabel(e.date)}</td>
            <td><b style="color:var(--navy)">${e.subject}</b></td>
            <td><span class="badge b-blue">${e.class||'All'}</span></td>
            <td><span class="badge ${examTypeBadgeColor(e.type)}">${e.type||'—'}</span></td>
            <td style="font-size:12px">${e.time||'—'}</td>
            <td style="font-size:12px">${e.duration||'—'}</td>
            <td style="font-size:12px">${e.venue||'—'}</td>
            <td><b>${e.maxMarks||100}</b></td>
            <td><span class="badge ${isToday?'b-blue':isPast?'b-gray':'b-amber'}">${isToday?'Today':isPast?'Done':'Upcoming'}</span></td>
          </tr>`;
        }).join('')}
      </tbody>
    </table></div>`;
  }

  function renderClassView(exams){
    if(!exams.length) return `<div class="empty"><div class="ei">🗓️</div><p>No exams match the selected filters.</p></div>`;
    const byClass={};
    exams.forEach(e=>{const k=e.class||'All Classes';if(!byClass[k])byClass[k]=[];byClass[k].push(e);});
    const clsList=Object.keys(byClass).sort();
    const today=new Date().toISOString().slice(0,10);
    return clsList.map(cls=>`
      <div class="card" style="margin-bottom:14px">
        <div class="card-hdr">
          <div><div class="card-title">🏫 Class ${cls}</div><div class="card-sub">${byClass[cls].length} exam${byClass[cls].length>1?'s':''} scheduled</div></div>
          <span class="badge b-blue">${byClass[cls].filter(e=>e.date>=today).length} upcoming</span>
        </div>
        <div class="tbl-wrap"><table>
          <thead><tr><th>#</th><th>Subject</th><th>Type</th><th>Date</th><th>Day</th><th>Time</th><th>Duration</th><th>Venue</th><th>Marks</th></tr></thead>
          <tbody>
            ${byClass[cls].map((e,i)=>{
              const isPast=e.date<today,isToday=e.date===today;
              return `<tr style="${isToday?'background:rgba(37,99,235,.04)':isPast?'opacity:.65':''}">
                <td style="color:var(--gray-400);font-size:11px">${i+1}</td>
                <td><b style="color:var(--navy)">${e.subject}</b></td>
                <td><span class="badge ${examTypeBadgeColor(e.type)}">${e.type||'—'}</span></td>
                <td><b style="color:${isPast?'var(--gray-400)':isToday?'var(--blue)':'var(--navy)'}">${fmtDate(e.date)}${isToday?' 🔴':''}</b></td>
                <td style="font-size:12px;color:var(--gray-500)">${dayLabel(e.date)}</td>
                <td style="font-size:12px">${e.time||'—'}</td>
                <td style="font-size:12px">${e.duration||'—'}</td>
                <td style="font-size:12px">${e.venue||'—'}</td>
                <td><b>${e.maxMarks||100}</b></td>
              </tr>`;
            }).join('')}
          </tbody>
        </table></div>
      </div>`).join('');
  }

  function renderStats(exams){
    const today=new Date().toISOString().slice(0,10);
    const upcoming=exams.filter(e=>e.date>=today).length;
    const done=exams.filter(e=>e.date<today).length;
    const todayCount=exams.filter(e=>e.date===today).length;
    const subjects=[...new Set(exams.map(e=>e.subject))].length;
    return `<div class="stat-grid" style="margin-bottom:16px">
      <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1);font-size:22px">📋</div><div class="stat-info"><h3>${exams.length}</h3><p>Total Exams</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1);font-size:22px">⏳</div><div class="stat-info"><h3>${upcoming}</h3><p>Upcoming</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1);font-size:22px">✅</div><div class="stat-info"><h3>${done}</h3><p>Completed</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(239,68,68,.1);font-size:22px">📍</div><div class="stat-info"><h3>${todayCount}</h3><p>Today</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1);font-size:22px">📚</div><div class="stat-info"><h3>${subjects}</h3><p>Subjects</p></div></div>
    </div>`;
  }

  function doRender(){
    const exams=getFiltered();
    document.getElementById('ett_stats').innerHTML=renderStats(exams);
    const body=document.getElementById('ett_body');
    if(viewMode==='calendar') body.innerHTML=renderCalendarView(exams);
    else if(viewMode==='table') body.innerHTML=renderTableView(exams);
    else body.innerHTML=renderClassView(exams);
  }

  area().innerHTML=`
  <div class="sec-hero" style="margin-bottom:18px">
    <div>
      <h2>🗓️ Exam Timetable Generator</h2>
      <p>Generate, filter & print formatted exam timetables for any class or exam type</p>
    </div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="btn btn-success btn-sm" onclick="ettPrint()">🖨️ Print Timetable</button>
      <button class="btn btn-cyan btn-sm" onclick="ettExportCSV()">📥 Export CSV</button>
      <button class="btn btn-outline btn-sm" style="color:white;border-color:rgba(255,255,255,.3)" onclick="loadSection('examschedule')">+ Add Exams</button>
    </div>
  </div>

  <!-- Filters -->
  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr" style="margin-bottom:12px">
      <div class="card-title">🔍 Filters & View</div>
    </div>
    <div style="display:flex;gap:10px;flex-wrap:wrap;align-items:flex-end">
      <div class="fg" style="margin:0;min-width:130px">
        <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">Class</label>
        <select class="fc" id="ett_class" onchange="ettFilter()">
          <option value="__all__">All Classes</option>
          ${classes.map(c=>`<option value="${c}">${c}</option>`).join('')}
        </select>
      </div>
      <div class="fg" style="margin:0;min-width:130px">
        <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">Exam Type</label>
        <select class="fc" id="ett_type" onchange="ettFilter()">
          <option value="__all__">All Types</option>
          ${['Unit Test','Mid Term','Final Exam','Quiz','Practical','Oral Exam'].map(t=>`<option value="${t}">${t}</option>`).join('')}
        </select>
      </div>
      <div class="fg" style="margin:0;min-width:130px">
        <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">From Date</label>
        <input class="fc" type="date" id="ett_from" onchange="ettFilter()">
      </div>
      <div class="fg" style="margin:0;min-width:130px">
        <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">To Date</label>
        <input class="fc" type="date" id="ett_to" onchange="ettFilter()">
      </div>
      <div class="fg" style="margin:0">
        <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">View Mode</label>
        <div style="display:flex;gap:4px">
          <button id="btn_cal" class="btn btn-primary btn-sm" onclick="ettView('calendar')">📅 Calendar</button>
          <button id="btn_tbl" class="btn btn-outline btn-sm" onclick="ettView('table')">📋 Table</button>
          <button id="btn_cls" class="btn btn-outline btn-sm" onclick="ettView('class')">🏫 By Class</button>
        </div>
      </div>
      <div class="fg" style="margin:0">
        <button class="btn btn-outline btn-sm" onclick="ettReset()" style="margin-top:19px">↺ Reset</button>
      </div>
    </div>
  </div>

  <!-- Stats -->
  <div id="ett_stats"></div>

  <!-- Body -->
  <div id="ett_body" class="card"></div>

  <!-- Hidden Print Area -->
  <div id="ett_print_area" style="display:none"></div>
  `;

  // Wire controls
  window.ettFilter=()=>{
    filterClass=document.getElementById('ett_class').value;
    filterType=document.getElementById('ett_type').value;
    filterFrom=document.getElementById('ett_from').value;
    filterTo=document.getElementById('ett_to').value;
    doRender();
  };
  window.ettView=(mode)=>{
    viewMode=mode;
    document.getElementById('btn_cal').className='btn btn-sm '+(mode==='calendar'?'btn-primary':'btn-outline');
    document.getElementById('btn_tbl').className='btn btn-sm '+(mode==='table'?'btn-primary':'btn-outline');
    document.getElementById('btn_cls').className='btn btn-sm '+(mode==='class'?'btn-primary':'btn-outline');
    doRender();
  };
  window.ettReset=()=>{
    filterClass='__all__'; filterType='__all__'; filterFrom=''; filterTo='';
    document.getElementById('ett_class').value='__all__';
    document.getElementById('ett_type').value='__all__';
    document.getElementById('ett_from').value='';
    document.getElementById('ett_to').value='';
    doRender();
  };
  window.ettExportCSV=()=>{
    const exams=getFiltered();
    if(!exams.length){notify('No exams to export.','⚠️');return;}
    const data=exams.map((e,i)=>[i+1,e.subject,e.class||'All',e.type||'',fmtDate(e.date),dayLabel(e.date),e.time||'',e.duration||'',e.venue||'',e.maxMarks||100,e.notes||'']);
    exportToExcel(data,['#','Subject','Class','Type','Date','Day','Time','Duration','Venue','Max Marks','Notes'],'SV_ExamTimetable_'+(filterClass!=='__all__'?filterClass+'_':'')+(filterType!=='__all__'?filterType+'_':'')+new Date().toISOString().slice(0,10));
  };
  window.ettPrint=()=>{
    const exams=getFiltered();
    if(!exams.length){notify('No exams match current filters.','⚠️');return;}
    const today=new Date().toISOString().slice(0,10);
    const byDate={};
    exams.forEach(e=>{ if(!byDate[e.date])byDate[e.date]=[]; byDate[e.date].push(e); });
    const dates=Object.keys(byDate).sort();
    const classLabel=filterClass!=='__all__'?`Class: ${filterClass}`:'All Classes';
    const typeLabel=filterType!=='__all__'?`Type: ${filterType}`:'All Types';
    const html=`<!DOCTYPE html><html><head><meta charset="UTF-8"><title>Exam Timetable - S.V. Vidhyalay</title>
    <style>
      *{box-sizing:border-box;margin:0;padding:0}
      body{font-family:Arial,sans-serif;color:#111;padding:20px;background:white}
      .header{text-align:center;border-bottom:3px double #1a2540;padding-bottom:14px;margin-bottom:20px}
      .header h1{font-size:22px;color:#1a2540;letter-spacing:.5px}
      .header p{font-size:12px;color:#555;margin-top:4px}
      .meta{display:flex;justify-content:space-between;font-size:11px;color:#555;margin-bottom:18px;padding:8px 12px;background:#f8fafc;border-radius:6px;border:1px solid #e2e8f0}
      .day-block{margin-bottom:20px;break-inside:avoid}
      .day-header{background:#1a2540;color:white;padding:8px 14px;border-radius:6px 6px 0 0;display:flex;justify-content:space-between;align-items:center}
      .day-header .date{font-size:14px;font-weight:700}
      .day-header .badge{font-size:10px;background:rgba(255,255,255,.15);padding:2px 8px;border-radius:10px}
      table{width:100%;border-collapse:collapse}
      th{background:#243152;color:white;padding:7px 10px;text-align:left;font-size:10px;text-transform:uppercase;letter-spacing:.4px}
      td{padding:7px 10px;border-bottom:1px solid #e2e8f0;font-size:12px}
      tr:nth-child(even) td{background:#f8fafc}
      .subject{font-weight:700;color:#1a2540}
      .badge-type{padding:2px 7px;border-radius:10px;font-size:10px;font-weight:600}
      .upcoming{background:#fef3c7;color:#92400e}
      .done{background:#f1f5f9;color:#64748b}
      .today-row td{background:#eff6ff!important}
      .footer{margin-top:24px;text-align:center;font-size:10px;color:#aaa;border-top:1px solid #e2e8f0;padding-top:10px}
      @media print{body{padding:10px}@page{margin:1cm}}
    </style></head><body>
    <div class="header">
      <div style="display:flex;align-items:center;justify-content:center;gap:14px;margin-bottom:8px">
        <img src="${LOGO_URI}" style="width:56px;height:56px;border-radius:12px;object-fit:cover;box-shadow:0 2px 8px rgba(0,0,0,.12)">
        <div style="text-align:left">
          <h1 style="margin:0">S.V. Vidhyalay — Exam Timetable</h1>
          <p style="margin:2px 0 0;font-size:11px;color:#64748b">सा विद्या या विमुक्ते</p>
        </div>
      </div>
      <p>${classLabel} &nbsp;·&nbsp; ${typeLabel} &nbsp;·&nbsp; ${exams.length} Exam${exams.length>1?'s':''}</p>
    </div>
    <div class="meta">
      <span>📅 Generated: ${new Date().toLocaleDateString('en-IN',{weekday:'long',day:'numeric',month:'long',year:'numeric'})}</span>
      <span>${filterFrom||filterTo?'Period: '+(filterFrom?fmtDate(filterFrom):'Start')+' → '+(filterTo?fmtDate(filterTo):'End'):'Full Schedule'}</span>
    </div>
    ${dates.map(date=>{
      const list=byDate[date];
      const isPast=date<today, isToday=date===today;
      const daysLeft=Math.ceil((new Date(date)-new Date())/(1000*60*60*24));
      const statusText=isToday?'📍 TODAY':isPast?'✓ Completed':'In '+Math.max(0,daysLeft)+' day'+(daysLeft===1?'':'s');
      return `<div class="day-block">
        <div class="day-header"><span class="date">${fmtDay(date)}</span><span class="badge">${statusText}</span></div>
        <table>
          <thead><tr><th>#</th><th>Subject</th><th>Class</th><th>Type</th><th>Time</th><th>Duration</th><th>Venue</th><th>Max Marks</th></tr></thead>
          <tbody>
            ${list.map((e,i)=>`<tr class="${isToday?'today-row':''}">
              <td>${i+1}</td>
              <td class="subject">${e.subject}</td>
              <td>${e.class||'All'}</td>
              <td><span class="badge-type ${isPast?'done':'upcoming'}">${e.type||'—'}</span></td>
              <td>${e.time||'—'}</td>
              <td>${e.duration||'—'}</td>
              <td>${e.venue||'—'}</td>
              <td><b>${e.maxMarks||100}</b></td>
            </tr>`).join('')}
          </tbody>
        </table>
      </div>`;
    }).join('')}
    <div class="footer">S.V. Vidhyalay Student Management System · Exam Timetable · Printed ${new Date().toLocaleString('en-IN')}</div>
    </body></html>`;
    const iframe=document.createElement('iframe');
    iframe.style.cssText='position:fixed;top:-9999px;left:-9999px;width:0;height:0;border:none;visibility:hidden';
    document.body.appendChild(iframe);
    iframe.contentDocument.open();
    iframe.contentDocument.write(html);
    iframe.contentDocument.close();
    setTimeout(()=>{
      iframe.contentWindow.focus();
      iframe.contentWindow.print();
      setTimeout(()=>document.body.removeChild(iframe),1000);
    },500);
    notify('Opening print dialog…','🖨️');
  };

  doRender();
}

// ── Teacher/Student view exam timetable ──────────────────────────────────────
function renderViewExamTimetable() {
  const role=currentUser.role;
  const cls=currentUser.class||'';
  const all=(store.examSchedule||[]).filter(e=>!e.class||e.class===cls||e.class==='');
  const today=new Date().toISOString().slice(0,10);
  const upcoming=all.filter(e=>e.date>=today).sort((a,b)=>new Date(a.date)-new Date(b.date));
  const past=all.filter(e=>e.date<today).sort((a,b)=>new Date(b.date)-new Date(a.date));

  function examTypeBadgeColor(type){
    const m={'Unit Test':'b-blue','Mid Term':'b-amber','Final Exam':'b-red','Quiz':'b-cyan','Practical':'b-violet','Oral Exam':'b-green'};
    return m[type]||'b-gray';
  }
  function subjectBg(subj){
    const cols=['#2563eb','#7c3aed','#059669','#dc2626','#d97706','#0891b2','#db2777','#65a30d'];
    let h=0;for(let i=0;i<(subj||'').length;i++)h=(h*31+subj.charCodeAt(i))&0xffff;
    return cols[h%cols.length];
  }
  function fmtDay(dateStr){if(!dateStr)return'';return new Date(dateStr+'T00:00:00').toLocaleDateString('en-IN',{weekday:'long',day:'numeric',month:'short',year:'numeric'});}
  function dayLabel(dateStr){if(!dateStr)return'';return new Date(dateStr+'T00:00:00').toLocaleDateString('en-IN',{weekday:'short'});}

  if(!all.length){
    area().innerHTML=`<div class="sec-hero"><div><h2>🗓️ Exam Timetable</h2><p>Class ${cls||'All'}</p></div></div><div class="card"><div class="empty"><div class="ei">🗓️</div><p>No exam timetable available yet. Check back later.</p></div></div>`;
    return;
  }

  const byDate={};
  upcoming.forEach(e=>{ if(!byDate[e.date])byDate[e.date]=[]; byDate[e.date].push(e); });
  const dates=Object.keys(byDate).sort();

  area().innerHTML=`
  <div class="sec-hero" style="margin-bottom:18px">
    <div>
      <h2>🗓️ Exam Timetable</h2>
      <p>Class ${cls||'All'} · ${upcoming.length} upcoming · ${past.length} completed</p>
    </div>
    <button class="btn btn-success btn-sm" onclick="ettStudentPrint()">🖨️ Print</button>
  </div>

  <!-- Stats row -->
  <div class="stat-grid" style="margin-bottom:16px">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1);font-size:22px">📋</div><div class="stat-info"><h3>${all.length}</h3><p>Total Exams</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1);font-size:22px">⏳</div><div class="stat-info"><h3>${upcoming.length}</h3><p>Upcoming</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1);font-size:22px">✅</div><div class="stat-info"><h3>${past.length}</h3><p>Completed</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(239,68,68,.1);font-size:22px">📍</div><div class="stat-info"><h3>${all.filter(e=>e.date===today).length}</h3><p>Today</p></div></div>
  </div>

  ${upcoming.length?`
  <div class="card" style="margin-bottom:16px">
    <div class="card-hdr"><div class="card-title">📅 Upcoming Exam Timetable</div><span class="badge b-amber">${upcoming.length} exams</span></div>
    ${dates.map(date=>{
      const list=byDate[date];
      const isToday=date===today;
      const daysLeft=Math.ceil((new Date(date)-new Date())/(1000*60*60*24));
      return `<div style="margin-bottom:16px">
        <div style="display:flex;align-items:center;gap:10px;margin-bottom:8px">
          <div style="background:${isToday?'var(--blue)':'var(--navy)'};color:white;border-radius:10px;padding:7px 12px;min-width:50px;text-align:center">
            <div style="font-size:9px;opacity:.7;font-weight:600">${dayLabel(date)}</div>
            <div style="font-size:18px;font-weight:800;line-height:1">${new Date(date+'T00:00:00').getDate()}</div>
            <div style="font-size:9px;opacity:.6">${new Date(date+'T00:00:00').toLocaleDateString('en-IN',{month:'short'})}</div>
          </div>
          <div>
            <div style="font-weight:700;font-size:13px;color:var(--navy)">${fmtDay(date)}</div>
            <div style="font-size:11px;color:${isToday?'var(--blue)':'#b45309'};font-weight:600">${isToday?'📍 Today':'⏳ In '+daysLeft+' day'+(daysLeft===1?'':'s')} · ${list.length} exam${list.length>1?'s':''}</div>
          </div>
        </div>
        <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(210px,1fr));gap:8px;margin-left:60px">
          ${list.map(e=>`<div style="border:2px solid ${isToday?'var(--blue)':'rgba(245,158,11,.3)'};border-radius:10px;padding:12px;background:white;position:relative;overflow:hidden">
            <div style="position:absolute;top:0;left:0;right:0;height:3px;background:${subjectBg(e.subject)}"></div>
            <div style="font-weight:800;font-size:13px;color:var(--navy);margin-top:2px">${e.subject}</div>
            <div style="margin:5px 0;display:flex;gap:4px;flex-wrap:wrap">
              ${e.type?`<span class="badge ${examTypeBadgeColor(e.type)}">${e.type}</span>`:''}
            </div>
            <div style="font-size:11px;color:var(--gray-600);display:flex;flex-direction:column;gap:2px">
              ${e.time?`<span>⏰ ${e.time}${e.duration?' · '+e.duration:''}</span>`:''}
              ${e.venue?`<span>📍 ${e.venue}</span>`:''}
              <span>📊 <b>${e.maxMarks||100}</b> marks</span>
            </div>
          </div>`).join('')}
        </div>
      </div>`;
    }).join('<hr style="border:none;border-top:1px solid var(--gray-100);margin:10px 0">')}
  </div>
  `:''}

  ${past.length?`
  <div class="card">
    <div class="card-hdr"><div class="card-title">✅ Completed Exams</div><span class="badge b-gray">${past.length}</span></div>
    <div class="tbl-wrap"><table>
      <thead><tr><th>Subject</th><th>Type</th><th>Date</th><th>Day</th><th>Time</th><th>Venue</th><th>Marks</th></tr></thead>
      <tbody>${past.map(e=>`<tr style="opacity:.7">
        <td><b style="color:var(--gray-600)">${e.subject}</b></td>
        <td><span class="badge b-gray">${e.type||'—'}</span></td>
        <td style="font-size:12px;color:var(--gray-400)">${fmtDate(e.date)}</td>
        <td style="font-size:11px;color:var(--gray-400)">${dayLabel(e.date)}</td>
        <td style="font-size:12px">${e.time||'—'}</td>
        <td style="font-size:12px">${e.venue||'—'}</td>
        <td>${e.maxMarks||100}</td>
      </tr>`).join('')}</tbody>
    </table></div>
  </div>`:''}
  `;

  window.ettStudentPrint=()=>{
    const html=`<!DOCTYPE html><html><head><meta charset="UTF-8"><title>Exam Timetable</title>
    <style>*{box-sizing:border-box;margin:0;padding:0}body{font-family:Arial,sans-serif;color:#111;padding:20px}
    .header{text-align:center;border-bottom:3px double #1a2540;padding-bottom:12px;margin-bottom:18px}
    .school-row{display:flex;align-items:center;justify-content:center;gap:12px;margin-bottom:6px}
    .school-row img{width:50px;height:50px;border-radius:10px;object-fit:cover}
    .header h1{font-size:20px;color:#1a2540}.header p{font-size:12px;color:#555;margin-top:4px}
    table{width:100%;border-collapse:collapse;margin-top:14px}
    th{background:#1a2540;color:white;padding:7px 10px;text-align:left;font-size:10px;text-transform:uppercase;letter-spacing:.4px}
    td{padding:7px 10px;border-bottom:1px solid #e2e8f0;font-size:12px}
    tr:nth-child(even) td{background:#f8fafc}
    .subject{font-weight:700;color:#1a2540}
    .footer{margin-top:18px;text-align:center;font-size:10px;color:#aaa;border-top:1px solid #e2e8f0;padding-top:8px}
    @media print{@page{margin:1cm}}</style></head><body>
    <div class="header">
      <div class="school-row"><img src="${LOGO_URI}" onerror="this.style.display='none'"><div><h1>S.V. Vidhyalay — Exam Timetable</h1><p style="font-size:11px;color:#64748b">सा विद्या या विमुक्ते</p></div></div>
      <p>Class ${cls||'All'} · Generated ${new Date().toLocaleDateString('en-IN')}</p>
    </div>
    <table><thead><tr><th>#</th><th>Subject</th><th>Type</th><th>Date</th><th>Day</th><th>Time</th><th>Duration</th><th>Venue</th><th>Max Marks</th></tr></thead>
    <tbody>${all.sort((a,b)=>new Date(a.date)-new Date(b.date)).map((e,i)=>`<tr>
      <td>${i+1}</td><td class="subject">${e.subject}</td><td>${e.type||'—'}</td><td><b>${fmtDate(e.date)}</b></td>
      <td>${dayLabel(e.date)}</td><td>${e.time||'—'}</td><td>${e.duration||'—'}</td><td>${e.venue||'—'}</td><td><b>${e.maxMarks||100}</b></td>
    </tr>`).join('')}</tbody></table>
    <div class="footer">S.V. Vidhyalay SMS · Printed ${new Date().toLocaleString('en-IN')}</div></body></html>`;
    const iframe=document.createElement('iframe');
    iframe.style.cssText='position:fixed;top:-9999px;left:-9999px;width:0;height:0;border:none;visibility:hidden';
    document.body.appendChild(iframe);
    iframe.contentDocument.open();
    iframe.contentDocument.write(html);
    iframe.contentDocument.close();
    setTimeout(()=>{
      iframe.contentWindow.focus();
      iframe.contentWindow.print();
      setTimeout(()=>document.body.removeChild(iframe),1000);
    },500);
    notify('Opening print dialog…','🖨️');
  };
}

// ═══════════════════════════════════════════════
//  ADMIN — SCHOOL LEAVING CERTIFICATE (TC)
// ═══════════════════════════════════════════════
function renderAdminSLC() {
  const issued = store.slcIssued || [];
  area().innerHTML = `
  <div class="sec-hero" style="margin-bottom:18px">
    <div>
      <h2>📜 School Leaving Certificate</h2>
      <p>${issued.length} certificate${issued.length!==1?'s':''} issued · Transfer / Character / TC</p>
    </div>
    <button class="btn btn-primary" onclick="showSLCForm()">+ Issue New Certificate</button>
  </div>

  <div class="stat-grid" style="margin-bottom:18px">
    <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1);font-size:22px">📜</div><div class="stat-info"><h3>${issued.length}</h3><p>Total Issued</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1);font-size:22px">📅</div><div class="stat-info"><h3>${issued.filter(c=>c.issueDate&&c.issueDate.slice(0,7)===new Date().toISOString().slice(0,7)).length}</h3><p>This Month</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1);font-size:22px">🎓</div><div class="stat-info"><h3>${[...new Set(issued.map(c=>c.lastClass))].length}</h3><p>Classes</p></div></div>
    <div class="stat-card"><div class="stat-icon" style="background:rgba(139,92,246,.1);font-size:22px">🔢</div><div class="stat-info"><h3>${(store.slcCounter||1)-1}</h3><p>Last Serial No.</p></div></div>
  </div>

  ${!issued.length ? `<div class="card"><div class="empty"><div class="ei">📜</div><p>No leaving certificates issued yet. Click <b>"+ Issue New Certificate"</b> to begin.</p></div></div>` : `
  <div class="card">
    <div class="card-hdr">
      <div><div class="card-title">📋 Issued Certificates</div><div class="card-sub">All school leaving / transfer certificates</div></div>
      <button class="dl-btn" onclick="exportSLCList()">📥 Export List</button>
    </div>
    <div class="tbl-wrap"><table>
      <thead><tr><th>Sr.No</th><th>Certificate No.</th><th>Student Name</th><th>Father's Name</th><th>Class</th><th>Leaving Date</th><th>Issue Date</th><th>Reason</th><th>Conduct</th><th>Actions</th></tr></thead>
      <tbody>
        ${issued.slice().reverse().map(c=>`<tr>
          <td style="color:var(--gray-400);font-size:11px">${c.serialNo||'—'}</td>
          <td><b style="font-family:'JetBrains Mono',monospace;color:var(--navy)">${c.certNo||'—'}</b></td>
          <td><b>${c.studentName}</b></td>
          <td style="font-size:12px">${c.fatherName||'—'}</td>
          <td><span class="badge b-blue">${c.lastClass||'—'}</span></td>
          <td style="font-size:12px">${fmtDate(c.leavingDate)}</td>
          <td style="font-size:12px;color:var(--gray-400)">${fmtDate(c.issueDate)}</td>
          <td style="font-size:12px">${c.reason||'—'}</td>
          <td><span class="badge ${c.conduct==='Excellent'?'b-green':c.conduct==='Good'?'b-blue':c.conduct==='Satisfactory'?'b-amber':'b-gray'}">${c.conduct||'—'}</span></td>
          <td><div style="display:flex;gap:4px">
            <button class="btn btn-violet btn-xs" onclick="previewSLC('${c.id}')">👁 View</button>
            <button class="btn btn-primary btn-xs" onclick="printSLC('${c.id}')">🖨️ Print</button>
            <button class="btn btn-danger btn-xs" onclick="deleteSLC('${c.id}')">Del</button>
          </div></td>
        </tr>`).join('')}
      </tbody>
    </table></div>
  </div>`}
  `;
}

function showSLCForm(editId) {
  const ex = editId ? (store.slcIssued||[]).find(x=>x.id===editId) : null;
  const nextSerial = store.slcCounter || 1;
  const nextCertNo = `SLC-${new Date().getFullYear()}-${String(nextSerial).padStart(4,'0')}`;
  const students = store.students || [];

  openMo(editId ? '✏️ Edit Leaving Certificate' : '📜 Issue School Leaving Certificate', `
    <div style="max-height:70vh;overflow-y:auto;padding-right:4px">
    <!-- Auto-fill from student -->
    ${!editId ? `<div style="background:rgba(37,99,235,.06);border:1.5px solid rgba(37,99,235,.15);border-radius:10px;padding:12px 14px;margin-bottom:16px">
      <label style="font-size:10px;font-weight:700;color:var(--blue);text-transform:uppercase;letter-spacing:.4px;display:block;margin-bottom:6px">Quick Fill from Student Record</label>
      <select class="fc" id="slc_student_pick" onchange="slcAutoFill(this.value)" style="margin:0">
        <option value="">— Select student to auto-fill —</option>
        ${students.map(s=>`<option value="${s.id}">${s.name} (${s.class||'—'}) · ${s.id}</option>`).join('')}
      </select>
    </div>` : ''}

    <div style="display:grid;grid-template-columns:1fr 1fr;gap:14px">
      <!-- Certificate Info -->
      <div class="fg"><label>Certificate No. *</label><input class="fc" id="slc_certNo" value="${ex?.certNo||nextCertNo}" placeholder="SLC-2025-0001"></div>
      <div class="fg"><label>Issue Date *</label><input class="fc" id="slc_issueDate" type="date" value="${ex?.issueDate||new Date().toISOString().slice(0,10)}"></div>

      <!-- Student Details -->
      <div class="fg"><label>Student Full Name *</label><input class="fc" id="slc_name" placeholder="As per school records" value="${ex?.studentName||''}"></div>
      <div class="fg"><label>Father's Name *</label><input class="fc" id="slc_father" placeholder="Father's full name" value="${ex?.fatherName||''}"></div>
      <div class="fg"><label>Mother's Name</label><input class="fc" id="slc_mother" placeholder="Mother's full name" value="${ex?.motherName||''}"></div>
      <div class="fg"><label>Date of Birth *</label><input class="fc" id="slc_dob" type="date" value="${ex?.dob||''}"></div>
      <div class="fg"><label>Place of Birth</label><input class="fc" id="slc_pob" placeholder="City / Village" value="${ex?.pob||''}"></div>
      <div class="fg"><label>Nationality</label><input class="fc" id="slc_nationality" value="${ex?.nationality||'Indian'}"></div>
      <div class="fg"><label>Caste / Category</label><input class="fc" id="slc_caste" placeholder="e.g. General, OBC, SC, ST" value="${ex?.caste||''}"></div>
      <div class="fg"><label>Religion</label><input class="fc" id="slc_religion" placeholder="e.g. Hindu, Muslim, Christian" value="${ex?.religion||''}"></div>

      <!-- Admission & Class -->
      <div class="fg"><label>Admission No.</label><input class="fc" id="slc_admNo" placeholder="e.g. ADM-2020-001" value="${ex?.admNo||''}"></div>
      <div class="fg"><label>Date of Admission</label><input class="fc" id="slc_admDate" type="date" value="${ex?.admDate||''}"></div>
      <div class="fg"><label>Last Class Studied *</label>
        <select class="fc" id="slc_class">
          <option value="">Select class</option>
          ${[...new Set(students.map(s=>s.class).concat((store.classes||[]).map(c=>c.name)))].filter(Boolean).sort().map(c=>`<option value="${c}" ${c===ex?.lastClass?'selected':''}>${c}</option>`).join('')}
          <option value="__other__">Other (type below)</option>
        </select>
      </div>
      <div class="fg" id="slc_class_other_wrap" style="display:none"><label>Custom Class</label><input class="fc" id="slc_class_other" placeholder="e.g. 12 Science" value=""></div>
      <div class="fg"><label>Academic Year</label><input class="fc" id="slc_year" placeholder="e.g. 2024-25" value="${ex?.academicYear||new Date().getFullYear()+'-'+(new Date().getFullYear()+1).toString().slice(2)}"></div>
      <div class="fg"><label>Date of Leaving *</label><input class="fc" id="slc_leaving" type="date" value="${ex?.leavingDate||''}"></div>

      <!-- Attendance & Progress -->
      <div class="fg"><label>Total Working Days</label><input class="fc" id="slc_workDays" type="number" placeholder="e.g. 240" value="${ex?.workDays||''}"></div>
      <div class="fg"><label>Days Present</label><input class="fc" id="slc_presentDays" type="number" placeholder="e.g. 220" value="${ex?.presentDays||''}"></div>

      <!-- Character & Conduct -->
      <div class="fg"><label>Conduct / Character</label>
        <select class="fc" id="slc_conduct">
          ${['Excellent','Good','Satisfactory','Average'].map(o=>`<option ${o===(ex?.conduct||'Good')?'selected':''}>${o}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Progress / Result</label>
        <select class="fc" id="slc_progress">
          ${['Promoted','Passed','Failed','Appeared','Not Appeared','Detained'].map(o=>`<option ${o===(ex?.progress||'Promoted')?'selected':''}>${o}</option>`).join('')}
        </select>
      </div>

      <!-- Fees & Other -->
      <div class="fg"><label>Fees Paid Up To</label><input class="fc" id="slc_feesPaid" placeholder="e.g. March 2025" value="${ex?.feesPaid||''}"></div>
      <div class="fg"><label>Dues Cleared</label>
        <select class="fc" id="slc_dues">
          ${['Yes','No','Partially'].map(o=>`<option ${o===(ex?.duesCleared||'Yes')?'selected':''}>${o}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Reason for Leaving</label>
        <select class="fc" id="slc_reason">
          ${['Transfer of Parents','Admission to Other School','Completed Education','Migration','Personal Reasons','Other'].map(o=>`<option ${o===(ex?.reason||'Transfer of Parents')?'selected':''}>${o}</option>`).join('')}
        </select>
      </div>
      <div class="fg"><label>Leaving to (School/City)</label><input class="fc" id="slc_leavingTo" placeholder="New school or city" value="${ex?.leavingTo||''}"></div>

      <!-- Signatures -->
      <div class="fg"><label>Class Teacher</label><input class="fc" id="slc_classTeacher" placeholder="Class teacher name" value="${ex?.classTeacher||''}"></div>
      <div class="fg"><label>Issued By (Authority)</label><input class="fc" id="slc_issuedBy" value="${ex?.issuedBy||'Principal, S.V. Vidhyalay'}"></div>

      <div class="fg" style="grid-column:1/-1"><label>Remarks / Extra Notes</label>
        <textarea class="fc" id="slc_remarks" style="min-height:50px" placeholder="Any additional remarks...">${ex?.remarks||''}</textarea>
      </div>
    </div>
    </div>

    <div style="display:flex;gap:8px;margin-top:16px;flex-wrap:wrap">
      <button class="btn btn-primary" onclick="${editId?`saveSLCEdit('${editId}')`:'saveSLCNew()'}">💾 ${editId?'Update':'Issue Certificate'}</button>
      <button class="btn btn-violet btn-sm" onclick="previewSLCDraft()" style="font-size:12px">👁 Preview</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);

  // Wire class select
  setTimeout(()=>{
    const sel = document.getElementById('slc_class');
    if (sel) sel.addEventListener('change', function(){
      document.getElementById('slc_class_other_wrap').style.display = this.value==='__other__'?'block':'none';
    });
  }, 50);
}

window.slcAutoFill = function(sid) {
  if (!sid) return;
  const s = store.students.find(x=>x.id===sid);
  if (!s) return;
  const set = (id, val) => { const el=document.getElementById(id); if(el&&val) el.value=val; };
  set('slc_name', s.name);
  set('slc_father', s.fatherName||s.father||'');
  set('slc_mother', s.motherName||s.mother||'');
  set('slc_dob', s.dob||s.birthDate||'');
  set('slc_admNo', s.admNo||s.id||'');
  set('slc_admDate', s.admissionDate||s.joinDate||'');
  set('slc_caste', s.caste||'');
  set('slc_religion', s.religion||'');
  set('slc_nationality', s.nationality||'Indian');
  // Set class select
  const cls = s.class||'';
  const sel = document.getElementById('slc_class');
  if (sel && cls) {
    let found = false;
    for (let o of sel.options) { if (o.value===cls) { sel.value=cls; found=true; break; } }
    if (!found) { sel.value='__other__'; document.getElementById('slc_class_other_wrap').style.display='block'; document.getElementById('slc_class_other').value=cls; }
  }
  // Attendance from store
  const att = store.attendance[sid]||{};
  const total = Object.keys(att).length, present = Object.values(att).filter(v=>v==='P').length;
  if (total) { set('slc_workDays', total); set('slc_presentDays', present); }
  // Fees
  const fees = (store.fees||[]).filter(f=>f.studentId===sid);
  if (fees.length) {
    const lastPaid = fees.filter(f=>f.status==='paid').sort((a,b)=>new Date(b.date)-new Date(a.date))[0];
    if (lastPaid) set('slc_feesPaid', lastPaid.term||fmtDate(lastPaid.date));
  }
  notify('Student details auto-filled!','✅');
};

function getSLCData() {
  const classEl = document.getElementById('slc_class');
  const cls = classEl?.value==='__other__' ? (document.getElementById('slc_class_other')?.value||'') : (classEl?.value||'');
  return {
    certNo:       document.getElementById('slc_certNo')?.value.trim(),
    issueDate:    document.getElementById('slc_issueDate')?.value,
    studentName:  document.getElementById('slc_name')?.value.trim(),
    fatherName:   document.getElementById('slc_father')?.value.trim(),
    motherName:   document.getElementById('slc_mother')?.value.trim(),
    dob:          document.getElementById('slc_dob')?.value,
    pob:          document.getElementById('slc_pob')?.value.trim(),
    nationality:  document.getElementById('slc_nationality')?.value.trim(),
    caste:        document.getElementById('slc_caste')?.value.trim(),
    religion:     document.getElementById('slc_religion')?.value.trim(),
    admNo:        document.getElementById('slc_admNo')?.value.trim(),
    admDate:      document.getElementById('slc_admDate')?.value,
    lastClass:    cls,
    academicYear: document.getElementById('slc_year')?.value.trim(),
    leavingDate:  document.getElementById('slc_leaving')?.value,
    workDays:     document.getElementById('slc_workDays')?.value,
    presentDays:  document.getElementById('slc_presentDays')?.value,
    conduct:      document.getElementById('slc_conduct')?.value,
    progress:     document.getElementById('slc_progress')?.value,
    feesPaid:     document.getElementById('slc_feesPaid')?.value.trim(),
    duesCleared:  document.getElementById('slc_dues')?.value,
    reason:       document.getElementById('slc_reason')?.value,
    leavingTo:    document.getElementById('slc_leavingTo')?.value.trim(),
    classTeacher: document.getElementById('slc_classTeacher')?.value.trim(),
    issuedBy:     document.getElementById('slc_issuedBy')?.value.trim(),
    remarks:      document.getElementById('slc_remarks')?.value.trim(),
  };
}

function saveSLCNew() {
  const d = getSLCData();
  if (!d.studentName || !d.leavingDate || !d.certNo) { alert('Student name, Certificate No. and Leaving Date are required.'); return; }
  if (!store.slcIssued) store.slcIssued = [];
  d.id = 'SLC'+Date.now();
  d.serialNo = store.slcCounter || 1;
  store.slcCounter = (store.slcCounter||1) + 1;
  d.createdAt = new Date().toISOString();
  store.slcIssued.push(d);
  saveStore(); closeMo(); notify('📜 School Leaving Certificate issued!','📜');
  renderAdminSLC();
}

function saveSLCEdit(id) {
  const rec = (store.slcIssued||[]).find(x=>x.id===id);
  if (!rec) return;
  const d = getSLCData();
  if (!d.studentName || !d.leavingDate || !d.certNo) { alert('Required fields missing.'); return; }
  Object.assign(rec, d);
  saveStore(); closeMo(); notify('Certificate updated!','✅');
  renderAdminSLC();
}

window.deleteSLC = function(id) {
  if (!confirm('Delete this leaving certificate?')) return;
  store.slcIssued = (store.slcIssued||[]).filter(x=>x.id!==id);
  saveStore(); notify('Deleted.','🗑️'); renderAdminSLC();
};

window.previewSLCDraft = function() {
  const d = getSLCData();
  if (!d.studentName) { alert('Please enter at least the student name.'); return; }
  d.id='_preview_'; d.serialNo=store.slcCounter||1;
  const win = window.open('','_blank','width=860,height:720');
  win.document.write(slcFullHTML(d, true));
  win.document.close();
};

window.previewSLC = function(id) {
  const c = (store.slcIssued||[]).find(x=>x.id===id);
  if (!c) return;
  const win = window.open('','_blank','width=860,height:720');
  win.document.write(slcFullHTML(c, false));
  win.document.close();
};

window.printSLC = function(id) {
  const c = (store.slcIssued||[]).find(x=>x.id===id);
  if (!c) return;
  const html = slcFullHTML(c, false);
  const iframe = document.createElement('iframe');
  iframe.style.cssText = 'position:fixed;top:-9999px;left:-9999px;width:1px;height:1px;border:none;visibility:hidden';
  document.body.appendChild(iframe);
  iframe.contentDocument.open();
  iframe.contentDocument.write(html);
  iframe.contentDocument.close();
  setTimeout(()=>{ iframe.contentWindow.focus(); iframe.contentWindow.print(); setTimeout(()=>document.body.removeChild(iframe),1500); }, 600);
  notify('Opening print dialog…','🖨️');
};

window.exportSLCList = function() {
  const data = (store.slcIssued||[]).map(c=>[c.serialNo||'',c.certNo||'',c.studentName,c.fatherName||'',c.motherName||'',c.dob?fmtDate(c.dob):'',c.lastClass||'',fmtDate(c.leavingDate),fmtDate(c.issueDate),c.reason||'',c.conduct||'',c.duesCleared||'',c.issuedBy||'']);
  exportToExcel(data,['Sr.No','Cert No','Student','Father','Mother','DOB','Class','Leaving Date','Issue Date','Reason','Conduct','Dues Cleared','Issued By'],'SV_LeavingCertificates_'+new Date().toISOString().slice(0,10));
};

function dobInWords(dobStr) {
  if (!dobStr) return '—';
  const d = new Date(dobStr+'T00:00:00');
  const months = ['January','February','March','April','May','June','July','August','September','October','November','December'];
  const day = d.getDate();
  const month = months[d.getMonth()];
  const year = d.getFullYear();
  // Number to words helper
  const ones = ['','One','Two','Three','Four','Five','Six','Seven','Eight','Nine','Ten','Eleven','Twelve','Thirteen','Fourteen','Fifteen','Sixteen','Seventeen','Eighteen','Nineteen'];
  const tens = ['','','Twenty','Thirty','Forty','Fifty','Sixty','Seventy','Eighty','Ninety'];
  function numToWords(n) {
    if (n === 0) return 'Zero';
    if (n < 20) return ones[n];
    if (n < 100) return tens[Math.floor(n/10)] + (n%10 ? ' '+ones[n%10] : '');
    if (n < 1000) return ones[Math.floor(n/100)] + ' Hundred' + (n%100 ? ' '+numToWords(n%100) : '');
    if (n < 10000) return ones[Math.floor(n/1000)] + ' Thousand' + (n%1000 ? ' '+numToWords(n%1000) : '');
    return n.toString();
  }
  return `${numToWords(day)} ${month}, ${numToWords(year)} (${String(day).padStart(2,'0')}/${String(d.getMonth()+1).padStart(2,'0')}/${year})`;
}

function slcFullHTML(c, isDraft) {
  const attendance = c.workDays && c.presentDays ? `${c.presentDays} / ${c.workDays}` : (c.workDays||'—');
  const attendancePct = c.workDays && c.presentDays ? ` (${Math.round((c.presentDays/c.workDays)*100)}%)` : '';
  return `<!DOCTYPE html>
<html lang="en"><head>
<meta charset="UTF-8">
<title>School Leaving Certificate - ${c.studentName}</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700;800&family=DM+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'DM Sans',Arial,sans-serif;background:#f0f0f0;display:flex;justify-content:center;padding:24px;min-height:100vh}
  .page{background:white;width:210mm;min-height:297mm;padding:14mm 16mm;position:relative;box-shadow:0 4px 32px rgba(0,0,0,.15)}
  /* Outer border */
  .outer-border{position:absolute;inset:8mm;border:2.5px solid #1a2540;pointer-events:none}
  .inner-border{position:absolute;inset:10mm;border:1px solid #9ca3af;pointer-events:none}
  /* Header */
  .cert-header{text-align:center;padding-bottom:10px;border-bottom:2px solid #1a2540;margin-bottom:14px}
  .school-row{display:flex;align-items:center;justify-content:center;gap:16px;margin-bottom:6px}
  .school-logo{width:64px;height:64px;border-radius:12px;object-fit:cover}
  .school-info{}
  .school-name{font-family:'Playfair Display',serif;font-size:22px;font-weight:800;color:#0f1729;letter-spacing:.5px}
  .school-tagline{font-size:11px;color:#64748b;letter-spacing:.4px}
  .school-address{font-size:10.5px;color:#64748b;margin-top:2px}
  .cert-title-wrap{margin-top:8px}
  .cert-title{font-family:'Playfair Display',serif;font-size:17px;font-weight:700;color:#1a2540;text-transform:uppercase;letter-spacing:2.5px;display:inline-block;border-bottom:2px solid #c9a84c;padding-bottom:3px}
  .cert-subtitle{font-size:10px;color:#64748b;margin-top:3px;letter-spacing:1px;text-transform:uppercase}
  /* Serial & Date bar */
  .meta-bar{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px;padding:6px 10px;background:#f8fafc;border:1px solid #e2e8f0;border-radius:6px;font-size:11.5px}
  .meta-bar span b{color:#1a2540}
  /* Fields table */
  .fields{width:100%;border-collapse:collapse;font-size:12px;margin-bottom:10px}
  .fields tr{border-bottom:1px dashed #e2e8f0}
  .fields tr:last-child{border-bottom:none}
  .fields td{padding:6px 4px;vertical-align:top}
  .fields td.lbl{color:#475569;font-weight:600;width:46%;white-space:nowrap}
  .fields td.colon{width:14px;color:#94a3b8;text-align:center}
  .fields td.val{color:#0f1729;font-weight:500}
  /* Certification text */
  .cert-body{font-size:12px;line-height:1.7;color:#334155;margin:10px 0}
  .cert-body .highlight{font-weight:700;color:#0f1729;text-decoration:underline;text-decoration-color:#c9a84c}
  /* Section headings */
  .section-head{background:#1a2540;color:white;font-size:10px;font-weight:700;text-transform:uppercase;letter-spacing:1px;padding:4px 10px;border-radius:4px;margin:12px 0 6px}
  /* Signature row */
  .sig-row{display:flex;justify-content:space-between;margin-top:24px;padding-top:14px;border-top:1px solid #e2e8f0}
  .sig-col{text-align:center;min-width:120px}
  .sig-line{border-top:1.5px solid #1a2540;width:130px;margin:0 auto 4px}
  .sig-name{font-weight:700;font-size:11.5px;color:#1a2540}
  .sig-title{font-size:10px;color:#64748b}
  /* Logo background watermark */
  .logo-watermark{
    position:absolute;
    top:50%;left:50%;
    transform:translate(-50%,-50%);
    width:300px;height:300px;
    background-image:url('${LOGO_URI}');
    background-size:contain;
    background-repeat:no-repeat;
    background-position:center;
    opacity:.1;
    pointer-events:none;
    z-index:0;
    -webkit-print-color-adjust:exact;
    print-color-adjust:exact;
  }
  /* Corner stamp — bottom right */
  .logo-stamp{
    position:absolute;
    bottom:18mm;right:14mm;
    width:85px;height:85px;
    background-image:url('${LOGO_URI}');
    background-size:contain;
    background-repeat:no-repeat;
    background-position:center;
    opacity:.14;
    pointer-events:none;
    z-index:0;
    -webkit-print-color-adjust:exact;
    print-color-adjust:exact;
  }
  /* Draft watermark */
  .watermark{position:fixed;top:50%;left:50%;transform:translate(-50%,-50%) rotate(-35deg);font-size:90px;color:rgba(244,63,94,.08);font-family:'Playfair Display',serif;font-weight:800;pointer-events:none;white-space:nowrap;z-index:100}
  /* All content above watermark */
  .cert-header,.meta-bar,.cert-body,.section-head,.fields,.sig-row,.noprint-btn{position:relative;z-index:2}
  /* Gold corners */
  .corner{position:absolute;width:24px;height:24px}
  .corner.tl{top:10mm;left:10mm;border-top:3px solid #c9a84c;border-left:3px solid #c9a84c}
  .corner.tr{top:10mm;right:10mm;border-top:3px solid #c9a84c;border-right:3px solid #c9a84c}
  .corner.bl{bottom:10mm;left:10mm;border-bottom:3px solid #c9a84c;border-left:3px solid #c9a84c}
  .corner.br{bottom:10mm;right:10mm;border-bottom:3px solid #c9a84c;border-right:3px solid #c9a84c}
  @media print{
    body{background:white;padding:0}
    .page{box-shadow:none;width:100%;padding:10mm 12mm;min-height:unset}
    @page{size:A4;margin:0}
  }
  /* No-print button */
  .noprint-btn{display:block;text-align:center;margin-bottom:14px}
  @media print{.noprint-btn{display:none}}
</style>
</head><body>
${isDraft?'<div class="watermark">DRAFT</div>':''}
<div class="noprint-btn"><button onclick="window.print()" style="padding:10px 28px;background:#1a2540;color:white;border:none;border-radius:8px;font-size:13px;font-weight:600;cursor:pointer;margin-right:8px">🖨️ Print</button><button onclick="window.close()" style="padding:10px 18px;background:#e2e8f0;color:#334155;border:none;border-radius:8px;font-size:13px;cursor:pointer">✕ Close</button></div>
<div class="page">
  <div class="outer-border"></div>
  <div class="inner-border"></div>
  <div class="corner tl"></div><div class="corner tr"></div>
  <div class="corner bl"></div><div class="corner br"></div>
  <!-- Logo background watermark -->
  <div class="logo-watermark"></div>
  <div class="logo-stamp"></div>

  <!-- Header -->
  <div class="cert-header">
    <div class="school-row">
      <img class="school-logo" src="${LOGO_URI}" onerror="this.style.display='none'">
      <div class="school-info">
        <div class="school-name">S.V. Vidhyalay</div>
        <div class="school-tagline">सा विद्या या विमुक्ते &nbsp;·&nbsp; Knowledge that Liberates</div>
        <div class="school-address">Recognised by Gujarat Government &nbsp;·&nbsp; Affiliated School</div>
      </div>
    </div>
    <div class="cert-title-wrap">
      <div class="cert-title">School Leaving Certificate</div>
      <div class="cert-subtitle">(Transfer Certificate)</div>
    </div>
  </div>

  <!-- Meta bar -->
  <div class="meta-bar">
    <span>Certificate No.: <b>${c.certNo||'—'}</b></span>
    <span>Serial No.: <b>${c.serialNo||'—'}</b></span>
    <span>Date of Issue: <b>${c.issueDate?new Date(c.issueDate+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'}):'—'}</b></span>
  </div>

  <!-- Certification paragraph -->
  <div class="cert-body">
    This is to certify that <span class="highlight">${c.studentName||'______________'}</span>,
    ${c.fatherName?`son/daughter/ward of <span class="highlight">${c.fatherName}</span>,`:''}
    was a bonafide student of this school.
    ${c.admDate?`He/She was admitted on <span class="highlight">${new Date(c.admDate+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'})}</span> and`:''}
    He/She studied up to <span class="highlight">${c.lastClass||'__________'}</span>
    ${c.academicYear?`(Academic Year: ${c.academicYear})`:''}
    and left the school on <span class="highlight">${c.leavingDate?new Date(c.leavingDate+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'}):'______________'}</span>.
  </div>

  <!-- Student Details -->
  <div class="section-head">Student Particulars</div>
  <table class="fields">
    <tr><td class="lbl">1. Full Name of Student</td><td class="colon">:</td><td class="val"><b>${c.studentName||'—'}</b></td></tr>
    <tr><td class="lbl">2. Father's Name</td><td class="colon">:</td><td class="val">${c.fatherName||'—'}</td></tr>
    <tr><td class="lbl">3. Mother's Name</td><td class="colon">:</td><td class="val">${c.motherName||'—'}</td></tr>
    <tr><td class="lbl">4. Date of Birth (in words &amp; figures)</td><td class="colon">:</td><td class="val">${dobInWords(c.dob)}</td></tr>
    <tr><td class="lbl">5. Place of Birth</td><td class="colon">:</td><td class="val">${c.pob||'—'}</td></tr>
    <tr><td class="lbl">6. Nationality</td><td class="colon">:</td><td class="val">${c.nationality||'Indian'}</td></tr>
    <tr><td class="lbl">7. Religion</td><td class="colon">:</td><td class="val">${c.religion||'—'}</td></tr>
    <tr><td class="lbl">8. Caste / Category</td><td class="colon">:</td><td class="val">${c.caste||'—'}</td></tr>
  </table>

  <!-- Academic Details -->
  <div class="section-head">Academic & Admission Details</div>
  <table class="fields">
    <tr><td class="lbl">9. Admission Number</td><td class="colon">:</td><td class="val">${c.admNo||'—'}</td></tr>
    <tr><td class="lbl">10. Date of Admission</td><td class="colon">:</td><td class="val">${c.admDate?new Date(c.admDate+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'}):'—'}</td></tr>
    <tr><td class="lbl">11. Class Last Studied</td><td class="colon">:</td><td class="val"><b>${c.lastClass||'—'}</b> &nbsp;(Academic Year: ${c.academicYear||'—'})</td></tr>
    <tr><td class="lbl">12. Whether Passed / Promoted</td><td class="colon">:</td><td class="val">${c.progress||'—'}</td></tr>
    <tr><td class="lbl">13. Total Working Days</td><td class="colon">:</td><td class="val">${c.workDays||'—'}</td></tr>
    <tr><td class="lbl">14. Days Present</td><td class="colon">:</td><td class="val">${c.presentDays||'—'}${attendancePct}</td></tr>
    <tr><td class="lbl">15. Conduct &amp; Character</td><td class="colon">:</td><td class="val"><b>${c.conduct||'Good'}</b></td></tr>
    <tr><td class="lbl">16. Fees Paid Up To</td><td class="colon">:</td><td class="val">${c.feesPaid||'—'}</td></tr>
    <tr><td class="lbl">17. School Dues Cleared</td><td class="colon">:</td><td class="val">${c.duesCleared||'Yes'}</td></tr>
    <tr><td class="lbl">18. Reason for Leaving</td><td class="colon">:</td><td class="val">${c.reason||'—'}</td></tr>
    <tr><td class="lbl">19. Leaving to (School / Place)</td><td class="colon">:</td><td class="val">${c.leavingTo||'—'}</td></tr>
    ${c.remarks?`<tr><td class="lbl">20. Remarks</td><td class="colon">:</td><td class="val">${c.remarks}</td></tr>`:''}
  </table>

  <!-- Signatures -->
  <div class="sig-row">
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">${c.classTeacher||'Class Teacher'}</div>
      <div class="sig-title">Class Teacher</div>
    </div>
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">Parent / Guardian</div>
      <div class="sig-title">Signature</div>
    </div>
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">${c.issuedBy||'Principal'}</div>
      <div class="sig-title">Signature &amp; School Stamp</div>
    </div>
  </div>
</div>
</body></html>`;
}

// ═══════════════════════════════════════════════
//  ADMIN — CLOUD STORAGE PAGE
// ═══════════════════════════════════════════════
function renderAdminCloudSync() {
  const cfg = loadCloudCfg();
  const connected = _cloudStatus==='connected'||_cloudStatus==='syncing';
  const lastSaved = store._lastSaved ? new Date(store._lastSaved).toLocaleString('en-IN') : 'Never';
  const dbSize = (JSON.stringify(store).length/1024).toFixed(1);

  area().innerHTML=`
  <div class="sec-hero">
    <div><h2>☁️ Cloud Storage</h2><p>Firebase Realtime Database sync · Free · Real-time across all devices</p></div>
    <div style="display:flex;gap:8px;flex-wrap:wrap">
      ${connected?`
        <button class="btn btn-success btn-sm" onclick="cloudPush();notify('Pushed to cloud!','☁️')">⬆️ Push Now</button>
        <button class="btn btn-cyan btn-sm" onclick="cloudPull()">⬇️ Pull Now</button>
        <button class="btn btn-danger btn-sm" onclick="if(confirm('Disconnect cloud sync?'))disconnectCloud()">🔌 Disconnect</button>
      `:`<button class="btn btn-primary btn-sm" onclick="openCloudPanel()">☁️ Connect Firebase</button>`}
    </div>
  </div>

  <!-- Status -->
  <div class="stat-grid" style="margin-bottom:18px">
    <div class="stat-card">
      <div class="stat-icon" style="background:${connected?'rgba(16,185,129,.1)':'rgba(100,116,139,.1)'};font-size:22px">${connected?'☁️':'💾'}</div>
      <div class="stat-info"><h3 style="font-size:14px;font-weight:700;color:${connected?'var(--emerald)':'var(--gray-400)'}">${connected?'Connected':'Local Only'}</h3><p>Sync Status</p></div>
    </div>
    <div class="stat-card">
      <div class="stat-icon" style="background:rgba(37,99,235,.1);font-size:22px">🗃️</div>
      <div class="stat-info"><h3>${dbSize} KB</h3><p>Database Size</p></div>
    </div>
    <div class="stat-card">
      <div class="stat-icon" style="background:rgba(245,158,11,.1);font-size:22px">🕐</div>
      <div class="stat-info"><h3 style="font-size:11px;font-weight:600">${lastSaved}</h3><p>Last Saved</p></div>
    </div>
    <div class="stat-card">
      <div class="stat-icon" style="background:rgba(139,92,246,.1);font-size:22px">📡</div>
      <div class="stat-info"><h3>${connected?cfg?.projectId||'—':'—'}</h3><p>Firebase Project</p></div>
    </div>
  </div>

  ${!connected?`
  <!-- Setup guide -->
  <div class="card" style="margin-bottom:18px">
    <div class="card-hdr"><div class="card-title">🚀 Setup Firebase Cloud Sync (Free)</div></div>
    <div class="cloud-setup-card" style="margin-bottom:0">
      <div class="cloud-step"><div class="cloud-step-num">1</div><div class="cloud-step-text">Visit <a href="https://console.firebase.google.com" target="_blank" style="color:var(--blue);font-weight:700">console.firebase.google.com</a> and sign in with your Google account. Click <b>Add project</b> and give it a name like <code>sv-vidhyalay-sms</code>.</div></div>
      <div class="cloud-step"><div class="cloud-step-num">2</div><div class="cloud-step-text">In your project, click <b>Realtime Database</b> in the left menu → <b>Create database</b> → choose a region → select <b>Start in test mode</b> → click Enable.</div></div>
      <div class="cloud-step"><div class="cloud-step-num">3</div><div class="cloud-step-text">Go to <b>Project Settings</b> (⚙️ gear icon) → <b>Your apps</b> → click the <b>&lt;/&gt; Web</b> icon → register the app → copy the <code>firebaseConfig</code> object shown.</div></div>
      <div class="cloud-step"><div class="cloud-step-num">4</div><div class="cloud-step-text">Click <b>Connect Firebase</b> below, paste each value from your config, and click <b>Connect & Sync</b>. Your data syncs instantly across all devices!</div></div>
    </div>
    <div style="margin-top:16px">
      <button class="btn btn-primary" onclick="openCloudPanel()">☁️ Connect Firebase Now</button>
    </div>
  </div>`:
  `
  <!-- Connected info -->
  <div class="card" style="margin-bottom:18px">
    <div class="card-hdr"><div class="card-title">🔗 Connection Details</div></div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
      ${[['Project ID',cfg?.projectId],['Database URL',cfg?.databaseURL],['Auth Domain',cfg?.authDomain],['Storage Bucket',cfg?.storageBucket]].map(([k,v])=>`
      <div style="background:var(--gray-50);border-radius:8px;padding:12px 14px">
        <div style="font-size:10px;font-weight:700;color:var(--gray-400);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">${k}</div>
        <div style="font-size:12px;color:var(--navy);font-family:'JetBrains Mono',monospace;word-break:break-all">${v||'—'}</div>
      </div>`).join('')}
    </div>
    <div style="margin-top:14px;display:flex;gap:8px">
      <button class="btn btn-outline btn-sm" onclick="openCloudPanel()">✏️ Edit Config</button>
    </div>
  </div>
  `}

  <!-- Data actions -->
  <div class="card">
    <div class="card-hdr"><div class="card-title">🛠️ Data Management</div></div>
    <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:12px">
      <div style="border:1.5px solid var(--gray-200);border-radius:10px;padding:16px">
        <div style="font-size:20px;margin-bottom:6px">⬆️</div>
        <div style="font-weight:700;font-size:13px;color:var(--navy);margin-bottom:4px">Push to Cloud</div>
        <div style="font-size:11px;color:var(--gray-500);margin-bottom:10px">Upload current local data to Firebase. Overwrites cloud data.</div>
        <button class="btn btn-primary btn-sm" onclick="${connected?`cloudPush();notify('Data pushed to cloud!','☁️')`:`openCloudPanel()`}" ${!connected?'':''}>${connected?'Push Now':'Connect First'}</button>
      </div>
      <div style="border:1.5px solid var(--gray-200);border-radius:10px;padding:16px">
        <div style="font-size:20px;margin-bottom:6px">⬇️</div>
        <div style="font-weight:700;font-size:13px;color:var(--navy);margin-bottom:4px">Pull from Cloud</div>
        <div style="font-size:11px;color:var(--gray-500);margin-bottom:10px">Download latest data from Firebase. Overwrites local data.</div>
        <button class="btn btn-cyan btn-sm" onclick="${connected?`cloudPull()`:`openCloudPanel()`}">${connected?'Pull Now':'Connect First'}</button>
      </div>
      <div style="border:1.5px solid var(--gray-200);border-radius:10px;padding:16px">
        <div style="font-size:20px;margin-bottom:6px">📥</div>
        <div style="font-weight:700;font-size:13px;color:var(--navy);margin-bottom:4px">Export Backup</div>
        <div style="font-size:11px;color:var(--gray-500);margin-bottom:10px">Download entire database as a JSON file for offline backup.</div>
        <button class="btn btn-success btn-sm" onclick="exportBackupJSON()">Download JSON</button>
      </div>
      <div style="border:1.5px solid var(--gray-200);border-radius:10px;padding:16px">
        <div style="font-size:20px;margin-bottom:6px">📤</div>
        <div style="font-weight:700;font-size:13px;color:var(--navy);margin-bottom:4px">Restore Backup</div>
        <div style="font-size:11px;color:var(--gray-500);margin-bottom:10px">Upload a JSON backup file to restore your database.</div>
        <label class="btn btn-warning btn-sm" style="cursor:pointer">Upload JSON <input type="file" accept=".json" style="display:none" onchange="importBackupJSON(this)"></label>
      </div>
    </div>
  </div>
  `;
}

window.exportBackupJSON = function(){
  const blob = new Blob([JSON.stringify(store, null, 2)], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href=url; a.download='SV_Vidhyalay_Backup_'+new Date().toISOString().slice(0,10)+'.json';
  a.click(); URL.revokeObjectURL(url);
  notify('Backup downloaded!','📥');
};

window.importBackupJSON = function(input){
  const file = input.files[0]; if(!file) return;
  const reader = new FileReader();
  reader.onload = async e => {
    try {
      if(!confirm('This will overwrite ALL current data with the backup. Are you sure?')) return;
      const data = JSON.parse(e.target.result);
      store = data;
      saveStore();
      if (_fbDb) await cloudPush();
      notify('Backup restored successfully!','✅');
      renderAdminCloudSync();
    } catch(err) { alert('Invalid JSON file. Please use a valid SV Vidhyalay backup.'); }
  };
  reader.readAsText(file);
};

// ═══════════════════════════════════════════════
//  ADMISSION FORM PRINT
// ═══════════════════════════════════════════════
window.printAdmissionForm = function(id) {
  const s = store.students.find(x => x.id === id); if (!s) return;

  function row(label, val) {
    return `<tr><td class="lbl">${label}</td><td class="colon">:</td><td class="val">${val||'—'}</td></tr>`;
  }
  function secHead(title) {
    return `<tr><td colspan="3" class="sec-head">${title}</td></tr>`;
  }

  const admDate = s.admDate || s.joined || '';
  const dob = s.dob || '';

  const html = `<!DOCTYPE html>
<html lang="en"><head>
<meta charset="UTF-8">
<title>Admission Form — ${s.name}</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700;800&family=DM+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'DM Sans',Arial,sans-serif;background:#e8eaf0;display:flex;justify-content:center;padding:20px;min-height:100vh}
  .page{background:white;width:210mm;min-height:297mm;padding:12mm 14mm;position:relative;box-shadow:0 4px 32px rgba(0,0,0,.15)}
  /* Borders */
  .outer-border{position:absolute;inset:6mm;border:2.5px solid #1a2540;pointer-events:none;z-index:0}
  .inner-border{position:absolute;inset:8mm;border:1px solid #c9a84c;pointer-events:none;z-index:0}
  /* Gold corners */
  .corner{position:absolute;width:20px;height:20px;z-index:2}
  .corner.tl{top:8mm;left:8mm;border-top:3px solid #c9a84c;border-left:3px solid #c9a84c}
  .corner.tr{top:8mm;right:8mm;border-top:3px solid #c9a84c;border-right:3px solid #c9a84c}
  .corner.bl{bottom:8mm;left:8mm;border-bottom:3px solid #c9a84c;border-left:3px solid #c9a84c}
  .corner.br{bottom:8mm;right:8mm;border-bottom:3px solid #c9a84c;border-right:3px solid #c9a84c}
  /* Logo watermark — multiple tiled for full-page coverage */
  .logo-watermark{
    position:absolute;top:0;left:0;right:0;bottom:0;
    background-image:url('${LOGO_URI}');
    background-size:240px 240px;
    background-repeat:no-repeat;
    background-position:center center;
    opacity:.08;
    pointer-events:none;
    z-index:1
  }
  /* Also add a smaller stamp at bottom-right */
  .logo-stamp{
    position:absolute;bottom:20mm;right:16mm;
    width:90px;height:90px;
    background-image:url('${LOGO_URI}');
    background-size:contain;
    background-repeat:no-repeat;
    background-position:center;
    opacity:.12;
    pointer-events:none;
    z-index:1
  }
  /* Header */
  .header{position:relative;z-index:2;text-align:center;border-bottom:2.5px solid #1a2540;padding-bottom:10px;margin-bottom:12px}
  .school-row{display:flex;align-items:center;justify-content:center;gap:14px;margin-bottom:6px}
  .school-logo{width:62px;height:62px;border-radius:12px;object-fit:cover;border:2px solid #e2e8f0}
  .school-name{font-family:'Playfair Display',serif;font-size:22px;font-weight:800;color:#0f1729;letter-spacing:.5px}
  .school-sub{font-size:10.5px;color:#64748b;margin-top:2px;letter-spacing:.3px}
  .form-title{font-family:'Playfair Display',serif;font-size:15px;font-weight:700;color:#1a2540;text-transform:uppercase;letter-spacing:2.5px;display:inline-block;border-bottom:2px solid #c9a84c;padding-bottom:3px;margin-top:7px}
  /* Photo box */
  .photo-box{position:absolute;top:12mm;right:14mm;z-index:3;width:75px;height:90px;border:2px solid #1a2540;background:#f8fafc;display:flex;align-items:center;justify-content:center;overflow:hidden;border-radius:4px}
  .photo-box img{width:100%;height:100%;object-fit:cover}
  .photo-box .photo-placeholder{font-size:9px;color:#94a3b8;text-align:center;padding:6px}
  /* Admission ID strip */
  .id-strip{display:flex;justify-content:space-between;align-items:center;background:#0f1729;color:white;padding:5px 12px;border-radius:5px;margin-bottom:12px;font-size:11px;position:relative;z-index:2}
  .id-strip b{font-family:monospace;font-size:13px;letter-spacing:.5px}
  /* Fields table */
  .fields{width:100%;border-collapse:collapse;font-size:11.5px;position:relative;z-index:2}
  .fields .sec-head{background:#1a2540;color:white;font-size:9.5px;font-weight:700;text-transform:uppercase;letter-spacing:1px;padding:4px 8px}
  .fields tr{border-bottom:1px solid #f1f5f9}
  .fields tr:last-child{border-bottom:none}
  .fields td{padding:4.5px 6px;vertical-align:top}
  .fields td.lbl{color:#475569;font-weight:600;width:42%;font-size:11px}
  .fields td.colon{width:12px;color:#94a3b8;text-align:center}
  .fields td.val{color:#0f1729;font-weight:500}
  /* Two-column layout */
  .two-col{display:grid;grid-template-columns:1fr 1fr;gap:0;position:relative;z-index:2}
  .two-col table{width:100%}
  /* Signature row */
  .sig-row{display:flex;justify-content:space-between;margin-top:16px;padding-top:12px;border-top:1px solid #e2e8f0;position:relative;z-index:2}
  .sig-col{text-align:center}
  .sig-line{border-top:1.5px solid #1a2540;width:110px;margin:0 auto 3px}
  .sig-name{font-weight:700;font-size:10px;color:#1a2540}
  .sig-title{font-size:9px;color:#64748b;margin-top:1px}
  /* Declaration */
  .declaration{font-size:9.5px;color:#475569;line-height:1.6;border:1px solid #e2e8f0;border-radius:5px;padding:8px 10px;margin:10px 0;background:#fafafa;position:relative;z-index:2}
  /* Footer */
  .footer{text-align:center;font-size:9px;color:#94a3b8;margin-top:10px;position:relative;z-index:2;border-top:1px dashed #e2e8f0;padding-top:7px}
  /* No-print button */
  .noprint-btn{display:block;text-align:center;margin-bottom:16px}
  @media print{
    body{background:white;padding:0}
    .page{box-shadow:none;width:100%;padding:10mm 12mm;min-height:unset}
    .noprint-btn{display:none}
    @page{size:A4;margin:0}
  }
</style>
</head><body>
<div class="noprint-btn">
  <button onclick="window.print()" style="padding:10px 28px;background:#1a2540;color:white;border:none;border-radius:8px;font-size:13px;font-weight:600;cursor:pointer;margin-right:8px">🖨️ Print Form</button>
  <button onclick="window.close()" style="padding:10px 18px;background:#e2e8f0;color:#334155;border:none;border-radius:8px;font-size:13px;cursor:pointer">✕ Close</button>
</div>
<div class="page">
  <div class="outer-border"></div>
  <div class="inner-border"></div>
  <div class="corner tl"></div><div class="corner tr"></div>
  <div class="corner bl"></div><div class="corner br"></div>
  <div class="logo-watermark"></div>
  <div class="logo-stamp"></div>

  <!-- Student photo top-right -->
  <div class="photo-box">
    ${s.photo ? `<img src="${s.photo}" alt="Student Photo">` : `<div class="photo-placeholder">📷<br>Student<br>Photo</div>`}
  </div>

  <!-- Header -->
  <div class="header">
    <div class="school-row">
      <img class="school-logo" src="${LOGO_URI}" onerror="this.style.display='none'">
      <div>
        <div class="school-name">S.V. Vidhyalay</div>
        <div class="school-sub">सा विद्या या विमुक्ते &nbsp;·&nbsp; Knowledge that Liberates</div>
        <div class="school-sub">Recognised by Gujarat Government &nbsp;·&nbsp; Affiliated School</div>
      </div>
    </div>
    <div class="form-title">Student Admission Form</div>
  </div>

  <!-- ID strip -->
  <div class="id-strip">
    <span>Student ID: <b>${s.id}</b></span>
    <span>GR No: <b>${s.grNo||'—'}</b></span>
    <span>Admission Date: <b>${admDate ? new Date(admDate+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'}) : '—'}</b></span>
    <span>Class: <b>${s.class||'—'}</b></span>
    <span>Roll No: <b>${s.roll||'—'}</b></span>
  </div>

  <!-- Fields -->
  <div class="two-col">
    <!-- Left column -->
    <table class="fields" style="border-right:1px solid #f1f5f9">
      ${secHead('👤 Personal Details')}
      ${row('Full Name', `<b>${s.name}</b>`)}
      ${row('Date of Birth', dob ? new Date(dob+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'}) : '—')}
      ${row('Gender', s.gender)}
      ${row('Blood Group', s.blood)}
      ${row('Nationality', s.nationality)}
      ${row('Religion', s.religion)}
      ${row('Category / Caste', s.category)}
      ${row('Aadhar Number', s.aadhar)}
      ${row('Mobile Number', s.phone)}
      ${row('Email Address', s.email)}
      ${secHead('🏠 Address')}
      ${row('Permanent Address', s.address)}
      ${row('Current Address', s.currAddress||s.address)}
    </table>
    <!-- Right column -->
    <table class="fields">
      ${secHead('👨‍👩‍👧 Parent / Guardian')}
      ${row("Father's Name", s.father)}
      ${row("Father's Occupation", s.fatherOcc)}
      ${row("Mother's Name", s.mother)}
      ${row("Mother's Occupation", s.motherOcc)}
      ${row('Guardian Name', s.guardian)}
      ${row('Relationship', s.guardianRel)}
      ${row('Parent / Guardian Phone', s.parentPhone)}
      ${row('Parent Email', s.parentEmail)}
      ${row('Annual Family Income', s.income)}
      ${secHead('📚 Admission Details')}
      ${row('Class / Standard', s.class)}
      ${row('Department / Stream', s.dept)}
      ${row('Roll Number', s.roll)}
      ${row('Admission Date', fmtDate(s.admDate||s.joined))}
      ${row('Transport Required', s.transport==='yes'?'Yes':'No')}
      ${row('Hostel Required', s.hostel==='yes'?'Yes':'No')}
    </table>
  </div>

  <!-- Academic History -->
  <table class="fields" style="margin-top:0">
    ${secHead('🎓 Previous Academic Record')}
    <tr>
      <td class="lbl">Previous School</td><td class="colon">:</td><td class="val">${s.prevSchool||'—'}</td>
      <td style="width:14px"></td>
      <td class="lbl">Board</td><td class="colon">:</td><td class="val">${s.prevBoard||'—'}</td>
    </tr>
    <tr>
      <td class="lbl">Last Class Passed</td><td class="colon">:</td><td class="val">${s.lastClass||'—'}</td>
      <td></td>
      <td class="lbl">Year of Passing</td><td class="colon">:</td><td class="val">${s.passYear||'—'}</td>
    </tr>
    <tr>
      <td class="lbl">Marks / Percentage</td><td class="colon">:</td><td class="val">${s.prevMarks||'—'}</td>
      <td></td>
      <td class="lbl">Division / Grade</td><td class="colon">:</td><td class="val">${s.division||'—'}</td>
    </tr>
    <tr>
      <td class="lbl">TC Number</td><td class="colon">:</td><td class="val">${s.tc||'—'}</td>
      <td></td>
      <td class="lbl">TC Date</td><td class="colon">:</td><td class="val">${fmtDate(s.tcDate)}</td>
    </tr>
    ${s.achievements?`<tr><td class="lbl">Achievements</td><td class="colon">:</td><td class="val" colspan="4">${s.achievements}</td></tr>`:''}
    ${s.notes?`<tr><td class="lbl">Special Notes</td><td class="colon">:</td><td class="val" colspan="4">${s.notes}</td></tr>`:''}
  </table>

  <!-- Declaration -->
  <div class="declaration">
    <b>Declaration:</b> I hereby declare that all the information provided above is true and correct to the best of my knowledge. I agree to abide by the rules and regulations of S.V. Vidhyalay and confirm that my ward will follow the school's code of conduct.
  </div>

  <!-- Signatures -->
  <div class="sig-row">
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">Parent / Guardian</div>
      <div class="sig-title">Signature &amp; Date</div>
    </div>
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">Student</div>
      <div class="sig-title">Signature</div>
    </div>
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">Class Teacher</div>
      <div class="sig-title">Signature</div>
    </div>
    <div class="sig-col">
      <div class="sig-line"></div>
      <div class="sig-name">Principal</div>
      <div class="sig-title">Signature &amp; School Stamp</div>
    </div>
  </div>

  <!-- Footer -->
  <div class="footer">
    S.V. Vidhyalay &nbsp;·&nbsp; Official Admission Form &nbsp;·&nbsp; Printed: ${new Date().toLocaleString('en-IN')} &nbsp;·&nbsp; Form No: ADM-${s.id}
  </div>
</div>
</body></html>`;

  const iframe = document.createElement('iframe');
  iframe.style.cssText = 'position:fixed;top:-9999px;left:-9999px;width:1px;height:1px;border:none;visibility:hidden';
  document.body.appendChild(iframe);
  iframe.contentDocument.open();
  iframe.contentDocument.write(html);
  iframe.contentDocument.close();
  setTimeout(() => {
    iframe.contentWindow.focus();
    iframe.contentWindow.print();
    setTimeout(() => document.body.removeChild(iframe), 1500);
  }, 600);
  notify('Opening print dialog…', '🖨️');
};

// ═══════════════════════════════════════════════
//  ADMIN — STUDENT ID CARD GENERATOR
// ═══════════════════════════════════════════════
function renderAdminIDCard() {
  const classes = [...new Set(store.students.map(s=>s.class).concat((store.classes||[]).map(c=>c.name)))].filter(Boolean).sort();
  let filterClass = '__all__';
  let selectedIds = new Set();
  let cardStyle = 'classic'; // classic | modern | minimal

  const styleColors = {
    classic: { bg:'linear-gradient(135deg,#0f1729 0%,#1a2540 100%)', accent:'#c9a84c', text:'white', subtext:'rgba(255,255,255,.7)' },
    modern:  { bg:'linear-gradient(135deg,#2563eb 0%,#7c3aed 100%)', accent:'#fbbf24', text:'white', subtext:'rgba(255,255,255,.75)' },
    minimal: { bg:'linear-gradient(135deg,#f8fafc 0%,#e2e8f0 100%)', accent:'#2563eb', text:'#0f1729', subtext:'#64748b' },
  };

  function getFiltered() {
    return store.students.filter(s => filterClass==='__all__' || s.class===filterClass);
  }

  function idCardHTML(s, style) {
    const c = styleColors[style] || styleColors.classic;
    const initials = (s.name||'?').split(' ').map(w=>w[0]).join('').substring(0,2).toUpperCase();
    const accentBorder = style==='minimal' ? `border:2.5px solid ${c.accent}` : '';
    return `
    <div class="id-card" style="
      width:85.6mm;height:53.98mm;
      background:${c.bg};
      border-radius:8px;
      position:relative;overflow:hidden;
      display:flex;flex-direction:column;
      box-shadow:0 4px 16px rgba(0,0,0,.22);
      page-break-inside:avoid;
      ${accentBorder}
    ">
      <!-- Watermark logo -->
      <div style="position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);
        width:90px;height:90px;
        background-image:url('${LOGO_URI}');background-size:contain;background-repeat:no-repeat;background-position:center;
        opacity:${style==='minimal'?'.06':'.09'};pointer-events:none;z-index:0"></div>

      <!-- Top bar with logo + school name -->
      <div style="display:flex;align-items:center;gap:7px;padding:7px 8px 5px;border-bottom:1.5px solid ${c.accent};position:relative;z-index:1;background:${style==='minimal'?'rgba(37,99,235,.06)':'rgba(0,0,0,.25)'}">
        <img src="${LOGO_URI}" style="width:26px;height:26px;border-radius:5px;object-fit:cover;flex-shrink:0;border:1px solid ${c.accent}" onerror="this.style.display='none'">
        <div>
          <div style="font-family:'Playfair Display',Georgia,serif;font-size:9.5px;font-weight:700;color:${c.text};letter-spacing:.3px;line-height:1.1">S.V. Vidhyalay</div>
          <div style="font-size:6.5px;color:${c.subtext};letter-spacing:.2px">Student Identity Card</div>
        </div>
        <div style="margin-left:auto;font-size:6px;color:${c.subtext};text-align:right;line-height:1.4">
          <div>${new Date().getFullYear()}–${(new Date().getFullYear()+1).toString().slice(2)}</div>
          <div style="color:${c.accent};font-weight:700">STUDENT</div>
        </div>
      </div>

      <!-- Body -->
      <div style="display:flex;flex:1;padding:6px 8px;gap:7px;position:relative;z-index:1">
        <!-- Photo -->
        <div style="flex-shrink:0">
          ${s.photo
            ? `<img src="${s.photo}" style="width:36px;height:44px;border-radius:5px;object-fit:cover;border:2px solid ${c.accent}">`
            : `<div style="width:36px;height:44px;border-radius:5px;background:${style==='minimal'?'rgba(37,99,235,.1)':'rgba(255,255,255,.12)'};border:2px solid ${c.accent};display:flex;align-items:center;justify-content:center;font-family:'Playfair Display',serif;font-size:14px;font-weight:700;color:${c.accent}">${initials}</div>`}
        </div>

        <!-- Info -->
        <div style="flex:1;display:flex;flex-direction:column;justify-content:center;gap:2px">
          <div style="font-family:'Playfair Display',Georgia,serif;font-size:10px;font-weight:700;color:${c.text};line-height:1.2;margin-bottom:2px">${s.name||'—'}</div>
          <div style="display:grid;grid-template-columns:auto 1fr;gap:1px 5px;font-size:6.8px;line-height:1.55">
            <span style="color:${c.subtext}">ID</span><span style="color:${c.accent};font-weight:700;font-family:monospace">${s.id||'—'}</span>
            <span style="color:${c.subtext}">Class</span><span style="color:${c.text};font-weight:600">${s.class||'—'}</span>
            ${s.roll?`<span style="color:${c.subtext}">Roll</span><span style="color:${c.text}">${s.roll}</span>`:''}
            <span style="color:${c.subtext}">DOB</span><span style="color:${c.text}">${s.dob?new Date(s.dob+'T00:00:00').toLocaleDateString('en-IN',{day:'numeric',month:'short',year:'numeric'}):'—'}</span>
            ${s.blood?`<span style="color:${c.subtext}">Blood</span><span style="color:${c.accent};font-weight:700">${s.blood}</span>`:''}
            <span style="color:${c.subtext}">Phone</span><span style="color:${c.text}">${s.phone||s.parentPhone||'—'}</span>
          </div>
        </div>
      </div>

      <!-- Bottom bar -->
      <div style="padding:4px 8px;background:${style==='minimal'?'rgba(37,99,235,.08)':'rgba(0,0,0,.3)'};border-top:1px solid ${c.accent};display:flex;justify-content:space-between;align-items:center;position:relative;z-index:1">
        <div style="font-size:6px;color:${c.subtext}">📍 ${s.address?(s.address.substring(0,28)+(s.address.length>28?'…':'')):''}&nbsp;</div>
        <div style="font-size:5.5px;color:${c.subtext};text-align:right">If found, please return to school</div>
      </div>
    </div>`;
  }

  function doRender() {
    const students = getFiltered();
    const sel = students.filter(s => selectedIds.has(s.id));
    area().innerHTML = `
    <div class="sec-hero" style="margin-bottom:18px">
      <div><h2>🪪 Student ID Card Generator</h2><p>Design & print professional ID cards for students</p></div>
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="btn btn-primary btn-sm" onclick="idSelectAll()">✅ Select All</button>
        <button class="btn btn-outline btn-sm" style="color:white;border-color:rgba(255,255,255,.3)" onclick="idSelectNone()">⬜ Deselect All</button>
        <button class="btn btn-success btn-sm" onclick="idPrintSelected()" ${sel.length===0?'disabled style="opacity:.5;cursor:not-allowed"':''}>🖨️ Print ${sel.length>0?sel.length+' Card'+(sel.length>1?'s':''):'Selected'}</button>
      </div>
    </div>

    <!-- Controls -->
    <div class="card" style="margin-bottom:16px">
      <div style="display:flex;gap:12px;flex-wrap:wrap;align-items:flex-end">
        <div class="fg" style="margin:0;min-width:150px">
          <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:5px">Filter by Class</label>
          <select class="fc" id="idc_class" onchange="idFilterClass(this.value)">
            <option value="__all__">All Classes</option>
            ${classes.map(c=>`<option value="${c}">${c}</option>`).join('')}
          </select>
        </div>
        <div class="fg" style="margin:0">
          <label style="display:block;font-size:10px;font-weight:700;color:var(--gray-500);text-transform:uppercase;letter-spacing:.4px;margin-bottom:5px">Card Style</label>
          <div style="display:flex;gap:6px">
            <button id="idc_classic" class="btn btn-sm btn-primary" onclick="idSetStyle('classic')" style="font-size:11px">🎓 Classic</button>
            <button id="idc_modern" class="btn btn-sm btn-outline" onclick="idSetStyle('modern')" style="font-size:11px">✨ Modern</button>
            <button id="idc_minimal" class="btn btn-sm btn-outline" onclick="idSetStyle('minimal')" style="font-size:11px">🤍 Minimal</button>
          </div>
        </div>
        <div style="font-size:12px;color:var(--gray-500);margin-bottom:4px">${students.length} students found · <b style="color:var(--blue)">${sel.length} selected</b></div>
      </div>
    </div>

    <!-- Card grid preview -->
    <div class="card">
      <div class="card-hdr">
        <div class="card-title">🪪 Preview & Select Cards</div>
        <div class="card-sub">Click a card to select / deselect it for printing</div>
      </div>
      ${students.length===0
        ? `<div class="empty"><div class="ei">🪪</div><p>No students found. Add students first.</p></div>`
        : `<div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:16px">
          ${students.map(s=>{
            const isSel = selectedIds.has(s.id);
            return `<div onclick="idToggle('${s.id}')" style="cursor:pointer;border-radius:12px;padding:3px;background:${isSel?'linear-gradient(135deg,var(--blue),var(--violet))':'var(--gray-100)'};transition:all .2s;transform:${isSel?'scale(1.02)':'scale(1)'};box-shadow:${isSel?'0 8px 24px rgba(37,99,235,.3)':'none'}">
              <div style="pointer-events:none">
                ${idCardHTML(s, cardStyle)}
              </div>
              <div style="text-align:center;padding:5px 0;font-size:11px;font-weight:600;color:${isSel?'var(--blue)':'var(--gray-400)'}">
                ${isSel?'✅ Selected':'Click to select'}
              </div>
            </div>`;
          }).join('')}
        </div>`}
    </div>`;
  }

  // Wire controls
  window.idFilterClass = (cls) => { filterClass=cls; selectedIds.clear(); doRender(); };
  window.idSetStyle = (s) => {
    cardStyle=s;
    ['classic','modern','minimal'].forEach(x=>{
      const btn=document.getElementById('idc_'+x);
      if(btn) btn.className='btn btn-sm '+(x===s?'btn-primary':'btn-outline');
    });
    doRender();
  };
  window.idToggle = (id) => { if(selectedIds.has(id)) selectedIds.delete(id); else selectedIds.add(id); doRender(); };
  window.idSelectAll = () => { getFiltered().forEach(s=>selectedIds.add(s.id)); doRender(); };
  window.idSelectNone = () => { selectedIds.clear(); doRender(); };

  window.idPrintSelected = () => {
    const sel = store.students.filter(s=>selectedIds.has(s.id));
    if (!sel.length) { notify('Select at least one student!','⚠️'); return; }
    const c = styleColors[cardStyle] || styleColors.classic;
    const cardsHTML = sel.map(s=>idCardHTML(s,cardStyle)).join('');

    const html = `<!DOCTYPE html><html lang="en"><head>
<meta charset="UTF-8"><title>Student ID Cards — S.V. Vidhyalay</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:Arial,sans-serif;background:#e8eaf0;padding:20px;display:flex;flex-direction:column;align-items:center}
  .noprint{text-align:center;margin-bottom:16px}
  .grid{display:flex;flex-wrap:wrap;gap:12px;justify-content:center;max-width:900px}
  .id-card{flex-shrink:0}
  @media print{
    body{background:white;padding:8mm}
    .noprint{display:none}
    .grid{gap:6mm;justify-content:flex-start}
    @page{size:A4;margin:8mm}
  }
</style>
</head><body>
<div class="noprint">
  <button onclick="window.print()" style="padding:10px 28px;background:#1a2540;color:white;border:none;border-radius:8px;font-size:13px;font-weight:600;cursor:pointer;margin-right:8px">🖨️ Print All ${sel.length} ID Cards</button>
  <button onclick="window.close()" style="padding:10px 18px;background:#e2e8f0;color:#334155;border:none;border-radius:8px;font-size:13px;cursor:pointer">✕ Close</button>
  <p style="margin-top:10px;font-size:12px;color:#64748b">${sel.length} cards · Style: ${cardStyle.charAt(0).toUpperCase()+cardStyle.slice(1)} · S.V. Vidhyalay</p>
</div>
<div class="grid">${cardsHTML}</div>
</body></html>`;

    const iframe = document.createElement('iframe');
    iframe.style.cssText = 'position:fixed;top:-9999px;left:-9999px;width:1px;height:1px;border:none;visibility:hidden';
    document.body.appendChild(iframe);
    iframe.contentDocument.open();
    iframe.contentDocument.write(html);
    iframe.contentDocument.close();
    setTimeout(()=>{
      iframe.contentWindow.focus();
      iframe.contentWindow.print();
      setTimeout(()=>document.body.removeChild(iframe),1500);
    }, 700);
    notify(`Printing ${sel.length} ID card${sel.length>1?'s':''}…`,'🖨️');
  };

  doRender();
}

// ═══════════════════════════════════════════════
//  FEES INSTALLMENT SYSTEM
// ═══════════════════════════════════════════════
window.setupInstallments = function(sid) {
  const s = store.students.find(x => x.id === sid); if (!s) return;
  const fs = store.feeStructures[sid] || {};
  const totalAmount = fs.amount || 0;

  openMo(`📅 Setup Installments — ${s.name}`, `
    <div style="background:rgba(37,99,235,.06);border:1.5px solid rgba(37,99,235,.2);border-radius:10px;padding:14px;margin-bottom:16px">
      <div style="display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:8px">
        <div>
          <div style="font-weight:700;color:var(--navy)">${s.name}</div>
          <div style="font-size:12px;color:var(--gray-500)">GR: ${s.grNo||'—'} &nbsp;·&nbsp; Class: ${s.class||'—'} &nbsp;·&nbsp; Term: ${fs.term||'—'}</div>
        </div>
        <div style="text-align:right">
          <div style="font-size:20px;font-weight:800;color:var(--navy)">₹${totalAmount.toLocaleString()}</div>
          <div style="font-size:11px;color:var(--gray-400)">Total Fee</div>
        </div>
      </div>
    </div>

    <div class="form-grid" style="margin-bottom:14px">
      <div class="fg">
        <label>Number of Installments</label>
        <select class="fc" id="inst_count" onchange="previewInstallments('${sid}')">
          <option value="2">2 Installments</option>
          <option value="3" selected>3 Installments</option>
          <option value="4">4 Installments</option>
          <option value="6">6 Installments</option>
          <option value="12">12 Monthly</option>
        </select>
      </div>
      <div class="fg">
        <label>First Due Date</label>
        <input class="fc" id="inst_start" type="date" value="${new Date().toISOString().slice(0,10)}" onchange="previewInstallments('${sid}')">
      </div>
      <div class="fg">
        <label>Interval</label>
        <select class="fc" id="inst_interval" onchange="previewInstallments('${sid}')">
          <option value="30">Monthly (30 days)</option>
          <option value="60">Bi-Monthly (60 days)</option>
          <option value="90">Quarterly (90 days)</option>
          <option value="7">Weekly (7 days)</option>
        </select>
      </div>
    </div>

    <div id="inst_preview" style="margin-bottom:14px"></div>

    <div style="display:flex;gap:8px">
      <button class="btn btn-primary" onclick="saveInstallments('${sid}')">✅ Create Installment Plan</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);

  // Trigger preview immediately
  setTimeout(() => previewInstallments(sid), 100);
};

window.previewInstallments = function(sid) {
  const fs = store.feeStructures[sid] || {};
  const total = fs.amount || 0;
  const count = parseInt(document.getElementById('inst_count')?.value || 3);
  const startDate = document.getElementById('inst_start')?.value || new Date().toISOString().slice(0,10);
  const interval = parseInt(document.getElementById('inst_interval')?.value || 30);
  const preview = document.getElementById('inst_preview');
  if (!preview) return;

  const baseAmount = Math.floor(total / count);
  const remainder = total - (baseAmount * count);
  const installments = [];
  let d = new Date(startDate + 'T00:00:00');
  for (let i = 0; i < count; i++) {
    const amt = i === 0 ? baseAmount + remainder : baseAmount;
    installments.push({ no: i+1, label: `Installment ${i+1}`, amount: amt, dueDate: d.toISOString().slice(0,10) });
    d = new Date(d.getTime() + interval * 86400000);
  }

  preview.innerHTML = `
    <div style="font-size:12px;font-weight:700;color:var(--gray-600);margin-bottom:8px;text-transform:uppercase;letter-spacing:.4px">Preview (${count} installments)</div>
    <div style="border:1.5px solid var(--gray-200);border-radius:10px;overflow:hidden">
      <table style="width:100%;border-collapse:collapse;font-size:12.5px">
        <thead><tr style="background:var(--gray-50)">
          <th style="padding:8px 12px;text-align:left;color:var(--gray-500);font-weight:600">#</th>
          <th style="padding:8px 12px;text-align:left;color:var(--gray-500);font-weight:600">Label</th>
          <th style="padding:8px 12px;text-align:right;color:var(--gray-500);font-weight:600">Amount</th>
          <th style="padding:8px 12px;text-align:left;color:var(--gray-500);font-weight:600">Due Date</th>
        </tr></thead>
        <tbody>
          ${installments.map((inst,i) => `<tr style="border-top:1px solid var(--gray-100)">
            <td style="padding:8px 12px;color:var(--gray-400)">${inst.no}</td>
            <td style="padding:8px 12px;font-weight:600;color:var(--navy)">${inst.label}</td>
            <td style="padding:8px 12px;text-align:right;font-weight:700;color:var(--blue)">₹${inst.amount.toLocaleString()}</td>
            <td style="padding:8px 12px;color:var(--gray-600)">${fmtDate(inst.dueDate)}</td>
          </tr>`).join('')}
          <tr style="border-top:2px solid var(--gray-200);background:var(--gray-50)">
            <td colspan="2" style="padding:8px 12px;font-weight:700;color:var(--navy)">Total</td>
            <td style="padding:8px 12px;text-align:right;font-weight:800;color:var(--navy)">₹${total.toLocaleString()}</td>
            <td style="padding:8px 12px"></td>
          </tr>
        </tbody>
      </table>
    </div>`;
};

window.saveInstallments = function(sid) {
  const fs = store.feeStructures[sid] || {};
  const total = fs.amount || 0;
  const count = parseInt(document.getElementById('inst_count')?.value || 3);
  const startDate = document.getElementById('inst_start')?.value || new Date().toISOString().slice(0,10);
  const interval = parseInt(document.getElementById('inst_interval')?.value || 30);

  const baseAmount = Math.floor(total / count);
  const remainder = total - (baseAmount * count);
  const installments = [];
  let d = new Date(startDate + 'T00:00:00');
  for (let i = 0; i < count; i++) {
    const amt = i === 0 ? baseAmount + remainder : baseAmount;
    installments.push({ no: i+1, label: `Installment ${i+1}`, amount: amt, dueDate: d.toISOString().slice(0,10), status: 'pending', paidDate: null, receiptNo: null });
    d = new Date(d.getTime() + interval * 86400000);
  }

  if (!store.feeInstallments) store.feeInstallments = {};
  store.feeInstallments[sid] = installments;
  saveStore();
  closeMo();
  notify(`${count} installments created for ${store.students.find(x=>x.id===sid)?.name||sid}!`, '📅');
  renderAdminFees();
};

window.viewInstallments = function(sid) {
  const s = store.students.find(x => x.id === sid); if (!s) return;
  const fs = store.feeStructures[sid] || {};
  const installments = store.feeInstallments[sid] || [];
  const paid = installments.filter(i => i.status === 'paid').reduce((a,i) => a+i.amount, 0);
  const total = fs.amount || 0;
  const pending = total - paid;

  openMo(`📅 Installment Plan — ${s.name}`, `
    <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:10px;margin-bottom:16px">
      <div style="background:rgba(37,99,235,.06);border:1.5px solid rgba(37,99,235,.15);border-radius:10px;padding:12px;text-align:center">
        <div style="font-size:18px;font-weight:800;color:var(--navy)">₹${total.toLocaleString()}</div>
        <div style="font-size:11px;color:var(--gray-400);margin-top:2px">Total Fee</div>
      </div>
      <div style="background:rgba(16,185,129,.06);border:1.5px solid rgba(16,185,129,.2);border-radius:10px;padding:12px;text-align:center">
        <div style="font-size:18px;font-weight:800;color:var(--emerald)">₹${paid.toLocaleString()}</div>
        <div style="font-size:11px;color:var(--gray-400);margin-top:2px">Paid</div>
      </div>
      <div style="background:rgba(244,63,94,.06);border:1.5px solid rgba(244,63,94,.2);border-radius:10px;padding:12px;text-align:center">
        <div style="font-size:18px;font-weight:800;color:var(--rose)">₹${pending.toLocaleString()}</div>
        <div style="font-size:11px;color:var(--gray-400);margin-top:2px">Pending</div>
      </div>
    </div>

    <div style="font-size:11px;color:var(--gray-500);margin-bottom:8px">GR: <b>${s.grNo||'—'}</b> &nbsp;·&nbsp; Class: <b>${s.class||'—'}</b> &nbsp;·&nbsp; Term: <b>${fs.term||'—'}</b></div>

    <div style="border:1.5px solid var(--gray-200);border-radius:10px;overflow:hidden;margin-bottom:14px">
      <table style="width:100%;border-collapse:collapse;font-size:12.5px">
        <thead><tr style="background:var(--gray-50)">
          <th style="padding:9px 12px;text-align:left;color:var(--gray-500);font-weight:600">#</th>
          <th style="padding:9px 12px;text-align:left;color:var(--gray-500);font-weight:600">Label</th>
          <th style="padding:9px 12px;text-align:right;color:var(--gray-500);font-weight:600">Amount</th>
          <th style="padding:9px 12px;text-align:left;color:var(--gray-500);font-weight:600">Due Date</th>
          <th style="padding:9px 12px;text-align:left;color:var(--gray-500);font-weight:600">Status</th>
          <th style="padding:9px 12px;text-align:left;color:var(--gray-500);font-weight:600">Paid On</th>
          <th style="padding:9px 12px;text-align:center;color:var(--gray-500);font-weight:600">Action</th>
        </tr></thead>
        <tbody>
          ${installments.map((inst, idx) => {
            const isOverdue = inst.status !== 'paid' && inst.dueDate && new Date(inst.dueDate+'T00:00:00') < new Date();
            return `<tr style="border-top:1px solid var(--gray-100);${isOverdue?'background:rgba(244,63,94,.03)':''}">
              <td style="padding:9px 12px;color:var(--gray-400);font-weight:600">${inst.no}</td>
              <td style="padding:9px 12px;font-weight:600;color:var(--navy)">${inst.label}</td>
              <td style="padding:9px 12px;text-align:right;font-weight:700;color:var(--blue)">₹${inst.amount.toLocaleString()}</td>
              <td style="padding:9px 12px;color:${isOverdue?'var(--rose)':'var(--gray-600)'};font-weight:${isOverdue?700:400}">${fmtDate(inst.dueDate)}${isOverdue?' ⚠️':''}</td>
              <td style="padding:9px 12px"><span class="badge ${inst.status==='paid'?'b-green':isOverdue?'b-red':'b-blue'}">${inst.status==='paid'?'✅ Paid':isOverdue?'Overdue':'Pending'}</span></td>
              <td style="padding:9px 12px;font-size:11px;color:var(--gray-500)">${inst.paidDate?fmtDate(inst.paidDate):'—'}</td>
              <td style="padding:9px 12px;text-align:center">
                ${inst.status!=='paid'
                  ? `<button class="btn btn-success btn-xs" onclick="payInstallment('${sid}',${idx})">💳 Pay</button>`
                  : `<span style="font-size:10px;color:var(--emerald)">${inst.receiptNo||'Paid'}</span>`}
              </td>
            </tr>`;
          }).join('')}
        </tbody>
      </table>
    </div>

    <div style="display:flex;gap:8px;flex-wrap:wrap">
      <button class="btn btn-outline btn-sm" onclick="printInstallmentCard('${sid}')">🖨️ Print Schedule</button>
      <button class="btn btn-danger btn-sm" onclick="deleteInstallments('${sid}')">🗑️ Clear Plan</button>
      <button class="btn btn-outline" onclick="closeMo()">Close</button>
    </div>
  `);
};

window.payInstallment = function(sid, idx) {
  const s = store.students.find(x => x.id === sid); if (!s) return;
  const inst = (store.feeInstallments[sid] || [])[idx]; if (!inst) return;

  openMo(`💳 Pay Installment ${inst.no} — ${s.name}`, `
    <div style="background:rgba(37,99,235,.06);border-radius:10px;padding:14px;margin-bottom:14px">
      <div style="font-weight:700;color:var(--navy);margin-bottom:6px">${inst.label}</div>
      <div style="font-size:22px;font-weight:800;color:var(--blue)">₹${inst.amount.toLocaleString()}</div>
      <div style="font-size:12px;color:var(--gray-500);margin-top:3px">Due: ${fmtDate(inst.dueDate)} &nbsp;·&nbsp; GR: ${s.grNo||'—'}</div>
    </div>
    <div class="form-grid">
      <div class="fg">
        <label>Payment Method</label>
        <select class="fc" id="ip_method">${(FEE_METHODS||['Cash','Cheque','Online','UPI','Bank Transfer']).map(m=>`<option>${m}</option>`).join('')}</select>
      </div>
      <div class="fg">
        <label>Payment Date</label>
        <input class="fc" id="ip_date" type="date" value="${new Date().toISOString().slice(0,10)}">
      </div>
      <div class="fg">
        <label>Reference No. (optional)</label>
        <input class="fc" id="ip_ref" placeholder="Cheque / UTR / UPI ref">
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-success" onclick="confirmPayInstallment('${sid}',${idx})">✅ Confirm Payment</button>
      <button class="btn btn-outline" onclick="closeMo();viewInstallments('${sid}')">Back</button>
    </div>
  `);
};

window.confirmPayInstallment = function(sid, idx) {
  const s = store.students.find(x => x.id === sid); if (!s) return;
  if (!store.feeInstallments[sid]) return;
  const inst = store.feeInstallments[sid][idx]; if (!inst) return;

  const method = document.getElementById('ip_method').value;
  const date = document.getElementById('ip_date').value;
  const ref = document.getElementById('ip_ref').value;
  const receiptNo = genReceipt();

  // Mark installment paid
  inst.status = 'paid';
  inst.paidDate = date;
  inst.method = method;
  inst.ref = ref;
  inst.receiptNo = receiptNo;

  // Also create a fee record for tracking
  const feeId = genId('fee');
  store.fees.push({
    id: feeId,
    studentId: sid,
    studentName: s.name,
    term: `${store.feeStructures[sid]?.term||''} — ${inst.label}`,
    amount: inst.amount,
    paid: inst.amount,
    method, date, ref, receiptNo,
    status: 'paid',
    notes: `Installment ${inst.no} of ${store.feeInstallments[sid].length}`
  });

  // Check if all installments paid — update student fee status
  const allPaid = store.feeInstallments[sid].every(i => i.status === 'paid');
  if (allPaid) { s.feeStatus = 'paid'; notify('All installments paid! 🎉', '✅'); }
  else notify(`Installment ${inst.no} paid! Receipt: ${receiptNo}`, '🧾');

  saveStore();
  closeMo();
  viewReceipt(feeId);
  renderAdminFees();
};

window.deleteInstallments = function(sid) {
  if (!confirm('Delete the installment plan for this student? Payment records will be kept.')) return;
  delete store.feeInstallments[sid];
  saveStore();
  notify('Installment plan deleted.', '🗑️');
  closeMo();
  renderAdminFees();
};

window.printInstallmentCard = function(sid) {
  const s = store.students.find(x => x.id === sid); if (!s) return;
  const fs = store.feeStructures[sid] || {};
  const installments = store.feeInstallments[sid] || [];
  const paid = installments.filter(i => i.status === 'paid').reduce((a,i) => a+i.amount, 0);

  const rows = installments.map(inst => {
    const isOverdue = inst.status !== 'paid' && inst.dueDate && new Date(inst.dueDate+'T00:00:00') < new Date();
    return `<tr style="border-bottom:1px solid #e2e8f0">
      <td style="padding:8px 10px;font-weight:600">${inst.no}</td>
      <td style="padding:8px 10px">${inst.label}</td>
      <td style="padding:8px 10px;font-weight:700;color:#1a2540">₹${inst.amount.toLocaleString()}</td>
      <td style="padding:8px 10px;color:${isOverdue?'#f43f5e':'#334155'}">${fmtDate(inst.dueDate)}</td>
      <td style="padding:8px 10px">
        <span style="display:inline-block;padding:2px 10px;border-radius:20px;font-size:11px;font-weight:700;background:${inst.status==='paid'?'#d1fae5':'#fee2e2'};color:${inst.status==='paid'?'#065f46':'#991b1b'}">${inst.status==='paid'?'PAID':'PENDING'}</span>
      </td>
      <td style="padding:8px 10px;font-size:11px;color:#64748b">${inst.paidDate?fmtDate(inst.paidDate):'—'}</td>
      <td style="padding:8px 10px;font-size:11px;font-family:monospace;color:#64748b">${inst.receiptNo||'—'}</td>
    </tr>`;
  }).join('');

  const html = `<!DOCTYPE html><html><head>
  <meta charset="UTF-8"><title>Fee Installment Schedule — ${s.name}</title>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=DM+Sans:wght@400;600&display=swap" rel="stylesheet">
  <style>
    *{box-sizing:border-box;margin:0;padding:0}
    body{font-family:'DM Sans',Arial,sans-serif;background:#e8eaf0;padding:20px;display:flex;justify-content:center}
    .page{background:white;width:210mm;padding:12mm;border-radius:0;box-shadow:0 4px 20px rgba(0,0,0,.15)}
    .hdr{text-align:center;border-bottom:2.5px solid #1a2540;padding-bottom:10px;margin-bottom:16px}
    .school{font-family:'Playfair Display',serif;font-size:20px;color:#1a2540;font-weight:700}
    .title{font-size:13px;font-weight:700;text-transform:uppercase;letter-spacing:2px;color:#64748b;margin-top:4px}
    .info{display:flex;justify-content:space-between;background:#f8fafc;border-radius:8px;padding:12px 16px;margin-bottom:16px;font-size:12.5px}
    .info div{display:flex;flex-direction:column;gap:3px}
    .info label{font-size:10px;color:#94a3b8;text-transform:uppercase;letter-spacing:.5px}
    .info b{font-size:14px;color:#0f1729}
    table{width:100%;border-collapse:collapse;font-size:12.5px}
    thead{background:#1a2540;color:white}
    thead th{padding:9px 10px;text-align:left;font-size:11px;font-weight:600;letter-spacing:.3px}
    .totrow{background:#f8fafc;font-weight:700}
    .noprint{text-align:center;margin-bottom:14px}
    .summary{display:grid;grid-template-columns:1fr 1fr 1fr;gap:10px;margin-bottom:16px}
    .scard{border-radius:8px;padding:10px 14px;text-align:center}
    @media print{body{background:white;padding:4mm}.page{box-shadow:none;width:100%;padding:8mm}.noprint{display:none}@page{size:A4;margin:0}}
  </style>
  </head><body>
  <div class="noprint">
    <button onclick="window.print()" style="padding:10px 24px;background:#1a2540;color:white;border:none;border-radius:8px;font-size:13px;font-weight:600;cursor:pointer;margin-right:8px">🖨️ Print</button>
    <button onclick="window.close()" style="padding:10px 16px;background:#e2e8f0;color:#334155;border:none;border-radius:8px;font-size:13px;cursor:pointer">✕ Close</button>
  </div>
  <div class="page">
    <div class="hdr">
      <img src="${LOGO_URI}" style="width:48px;height:48px;border-radius:10px;object-fit:cover;margin-bottom:6px">
      <div class="school">S.V. Vidhyalay</div>
      <div class="title">Fee Installment Schedule</div>
    </div>
    <div class="info">
      <div><label>Student Name</label><b>${s.name}</b></div>
      <div><label>GR Number</label><b style="color:#8b5cf6">${s.grNo||'—'}</b></div>
      <div><label>Student ID</label><b>${s.id}</b></div>
      <div><label>Class</label><b>${s.class||'—'}</b></div>
      <div><label>Term</label><b>${fs.term||'—'}</b></div>
    </div>
    <div class="summary">
      <div class="scard" style="background:#eff6ff;border:1.5px solid #bfdbfe">
        <div style="font-size:18px;font-weight:800;color:#1a2540">₹${(fs.amount||0).toLocaleString()}</div>
        <div style="font-size:10px;color:#64748b;margin-top:2px">Total Fee</div>
      </div>
      <div class="scard" style="background:#f0fdf4;border:1.5px solid #bbf7d0">
        <div style="font-size:18px;font-weight:800;color:#065f46">₹${paid.toLocaleString()}</div>
        <div style="font-size:10px;color:#64748b;margin-top:2px">Paid</div>
      </div>
      <div class="scard" style="background:#fff1f2;border:1.5px solid #fecdd3">
        <div style="font-size:18px;font-weight:800;color:#991b1b">₹${((fs.amount||0)-paid).toLocaleString()}</div>
        <div style="font-size:10px;color:#64748b;margin-top:2px">Pending</div>
      </div>
    </div>
    <table>
      <thead><tr>
        <th>#</th><th>Installment</th><th>Amount</th><th>Due Date</th><th>Status</th><th>Paid On</th><th>Receipt</th>
      </tr></thead>
      <tbody>${rows}</tbody>
      <tr class="totrow"><td colspan="2" style="padding:9px 10px">Total</td><td style="padding:9px 10px">₹${(fs.amount||0).toLocaleString()}</td><td colspan="4" style="padding:9px 10px;font-size:11px;color:#64748b">Printed: ${new Date().toLocaleString('en-IN')}</td></tr>
    </table>
    <div style="margin-top:20px;padding-top:12px;border-top:1px dashed #e2e8f0;display:flex;justify-content:space-between;font-size:10px;color:#94a3b8">
      <span>S.V. Vidhyalay — Official Installment Schedule</span>
      <span>Generated: ${new Date().toLocaleString('en-IN')}</span>
    </div>
  </div>
  </body></html>`;

  const iframe = document.createElement('iframe');
  iframe.style.cssText = 'position:fixed;top:-9999px;left:-9999px;width:1px;height:1px;border:none;visibility:hidden';
  document.body.appendChild(iframe);
  iframe.contentDocument.open();
  iframe.contentDocument.write(html);
  iframe.contentDocument.close();
  setTimeout(() => {
    iframe.contentWindow.focus();
    iframe.contentWindow.print();
    setTimeout(() => document.body.removeChild(iframe), 1500);
  }, 700);
  notify('Opening print dialog…', '🖨️');
};

// ═══════════════════════════════════════════════
//  FINANCE PRINT
// ═══════════════════════════════════════════════
window.printFinance = function(typeFilter='', dateFrom='', dateTo='') {
  const allT = store.transactions || [];
  let list = [...allT];

  // Apply filters
  if (typeFilter) list = list.filter(t => t.type === typeFilter);
  if (dateFrom) list = list.filter(t => t.date >= dateFrom);
  if (dateTo)   list = list.filter(t => t.date <= dateTo);
  list.sort((a,b) => new Date(a.date)-new Date(b.date));

  const totalInc  = list.filter(t=>t.type==='income').reduce((s,t)=>s+t.amount,0);
  const totalExp  = list.filter(t=>t.type==='expense').reduce((s,t)=>s+t.amount,0);
  const net = totalInc - totalExp;
  const fmtC = n => '₹' + (n||0).toLocaleString('en-IN');

  // Determine report title
  let reportTitle = 'Income & Expense Report';
  if (dateFrom && dateFrom===dateTo) reportTitle = `Daily Report — ${new Date(dateFrom+'T00:00:00').toLocaleDateString('en-IN',{weekday:'long',day:'numeric',month:'long',year:'numeric'})}`;
  else if (dateFrom && dateFrom.length===10 && dateTo && dateTo.slice(0,7)===dateFrom.slice(0,7)) reportTitle = `Monthly Report — ${new Date(dateFrom+'T00:00:00').toLocaleString('en-IN',{month:'long',year:'numeric'})}`;
  else if (typeFilter) reportTitle = `${typeFilter==='income'?'Income':'Expense'} Report`;
  const dateRange = (dateFrom||dateTo) ? `${dateFrom?new Date(dateFrom+'T00:00:00').toLocaleDateString('en-IN'):''}${dateFrom&&dateTo?' – ':''}${dateTo&&dateTo!==dateFrom?new Date(dateTo+'T00:00:00').toLocaleDateString('en-IN'):''}` : 'All Dates';

  // Group by date for ledger view
  const byDate = {};
  list.forEach(t => { (byDate[t.date]=byDate[t.date]||[]).push(t); });

  const tableRows = list.map((t,i) => `
    <tr style="background:${i%2===0?'#fafafa':'white'}">
      <td style="padding:7px 10px;font-size:11px;color:#64748b;white-space:nowrap">${new Date(t.date+'T00:00:00').toLocaleDateString('en-IN',{day:'2-digit',month:'short',year:'numeric'})}</td>
      <td style="padding:7px 10px">
        <span style="display:inline-block;padding:2px 10px;border-radius:20px;font-size:10px;font-weight:700;background:${t.type==='income'?'#dcfce7':'#fee2e2'};color:${t.type==='income'?'#166534':'#991b1b'}">${t.type==='income'?'INCOME':'EXPENSE'}</span>
      </td>
      <td style="padding:7px 10px;font-size:12px;font-weight:500;color:#334155">${t.category}</td>
      <td style="padding:7px 10px;font-size:12px;color:#64748b;max-width:200px">${t.description||'—'}</td>
      <td style="padding:7px 10px;font-size:11px;color:#94a3b8">${t.method||'—'}</td>
      <td style="padding:7px 10px;font-size:11px;font-family:monospace;color:#94a3b8">${t.ref||'—'}</td>
      <td style="padding:7px 10px;text-align:right;font-weight:700;font-size:13px;color:${t.type==='income'?'#16a34a':'#dc2626'}">${t.type==='income'?'+':'-'}${fmtC(t.amount)}</td>
    </tr>`).join('');

  const html = `<!DOCTYPE html><html lang="en"><head>
<meta charset="UTF-8"><title>${reportTitle} — S.V. Vidhyalay</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700;800&family=DM+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{font-family:'DM Sans',Arial,sans-serif;background:#e8eaf0;padding:20px;display:flex;flex-direction:column;align-items:center}
  .page{background:white;width:210mm;padding:12mm 14mm;box-shadow:0 4px 24px rgba(0,0,0,.15)}
  /* Borders */
  .outer-border{position:fixed;inset:6mm;border:2px solid #1a2540;pointer-events:none;z-index:0}
  /* Header */
  .hdr{display:flex;align-items:center;gap:14px;border-bottom:2.5px solid #1a2540;padding-bottom:12px;margin-bottom:16px}
  .school-logo{width:54px;height:54px;border-radius:10px;object-fit:cover}
  .school-name{font-family:'Playfair Display',serif;font-size:20px;color:#0f1729;font-weight:800}
  .report-title{font-size:13px;font-weight:700;text-transform:uppercase;letter-spacing:2px;color:#64748b;margin-top:2px}
  /* Summary cards */
  .summary{display:grid;grid-template-columns:repeat(4,1fr);gap:10px;margin-bottom:16px}
  .scard{border-radius:10px;padding:12px 14px}
  .scard .val{font-size:20px;font-weight:800;margin-bottom:2px}
  .scard .lbl{font-size:10px;font-weight:600;text-transform:uppercase;letter-spacing:.5px;opacity:.7}
  /* Table */
  table{width:100%;border-collapse:collapse;font-size:12px}
  thead tr{background:#1a2540;color:white}
  thead th{padding:9px 10px;text-align:left;font-size:11px;font-weight:600;letter-spacing:.3px}
  thead th:last-child{text-align:right}
  tbody tr:hover{background:#f8fafc}
  .footer{margin-top:14px;padding-top:10px;border-top:1px dashed #e2e8f0;display:flex;justify-content:space-between;font-size:10px;color:#94a3b8}
  /* Totals row */
  .totals{background:#f1f5f9;border-top:2px solid #1a2540}
  .totals td{padding:9px 10px;font-weight:700;font-size:12.5px}
  /* No-print */
  .noprint{text-align:center;margin-bottom:14px}
  @media print{
    body{background:white;padding:0}
    .page{box-shadow:none;padding:8mm 10mm;width:100%}
    .noprint{display:none}
    @page{size:A4;margin:0}
  }
</style>
</head><body>
<div class="noprint">
  <button onclick="window.print()" style="padding:10px 28px;background:#1a2540;color:white;border:none;border-radius:8px;font-size:13px;font-weight:600;cursor:pointer;margin-right:8px">🖨️ Print / Save PDF</button>
  <button onclick="window.close()" style="padding:10px 18px;background:#e2e8f0;color:#334155;border:none;border-radius:8px;font-size:13px;cursor:pointer">✕ Close</button>
</div>
<div class="page">
  <!-- Header -->
  <div class="hdr">
    <img class="school-logo" src="${LOGO_URI}" onerror="this.style.display='none'">
    <div style="flex:1">
      <div class="school-name">S.V. Vidhyalay</div>
      <div class="report-title">${reportTitle}</div>
      <div style="font-size:11px;color:#94a3b8;margin-top:3px">Period: ${dateRange} &nbsp;·&nbsp; ${list.length} transactions &nbsp;·&nbsp; Printed: ${new Date().toLocaleDateString('en-IN')}</div>
    </div>
    <div style="text-align:right">
      <div style="font-size:26px;font-weight:800;color:${net>=0?'#16a34a':'#dc2626'}">${fmtC(net)}</div>
      <div style="font-size:11px;color:#94a3b8">${net>=0?'Net Surplus':'Net Deficit'}</div>
    </div>
  </div>

  <!-- Summary -->
  <div class="summary">
    <div class="scard" style="background:#f0fdf4;border:1.5px solid #bbf7d0">
      <div class="val" style="color:#166534">${fmtC(totalInc)}</div>
      <div class="lbl" style="color:#166534">Total Income</div>
    </div>
    <div class="scard" style="background:#fff1f2;border:1.5px solid #fecdd3">
      <div class="val" style="color:#991b1b">${fmtC(totalExp)}</div>
      <div class="lbl" style="color:#991b1b">Total Expense</div>
    </div>
    <div class="scard" style="background:${net>=0?'#eff6ff':'#fff1f2'};border:1.5px solid ${net>=0?'#bfdbfe':'#fecdd3'}">
      <div class="val" style="color:${net>=0?'#1e40af':'#991b1b'}">${fmtC(net)}</div>
      <div class="lbl" style="color:${net>=0?'#1e40af':'#991b1b'}">${net>=0?'Surplus':'Deficit'}</div>
    </div>
    <div class="scard" style="background:#f5f3ff;border:1.5px solid #ddd6fe">
      <div class="val" style="color:#5b21b6">${list.length}</div>
      <div class="lbl" style="color:#5b21b6">Transactions</div>
    </div>
  </div>

  <!-- Transactions table -->
  ${list.length === 0 ? `<div style="text-align:center;padding:40px;color:#94a3b8;font-size:14px">No transactions found for the selected filter.</div>` : `
  <table>
    <thead><tr>
      <th>Date</th><th>Type</th><th>Category</th><th>Description</th><th>Method</th><th>Ref No.</th><th style="text-align:right">Amount</th>
    </tr></thead>
    <tbody>
      ${tableRows}
      <tr class="totals">
        <td colspan="5" style="color:#1a2540">Grand Total (${list.length} transactions)</td>
        <td style="color:#16a34a">Income: ${fmtC(totalInc)}</td>
        <td style="text-align:right;color:${net>=0?'#16a34a':'#dc2626'}">Net: ${fmtC(net)}</td>
      </tr>
    </tbody>
  </table>`}

  <!-- Footer -->
  <div class="footer">
    <span>S.V. Vidhyalay &nbsp;·&nbsp; Income & Expense Ledger &nbsp;·&nbsp; Confidential</span>
    <span>Generated: ${new Date().toLocaleString('en-IN')} &nbsp;·&nbsp; Page 1</span>
  </div>
</div>
</body></html>`;

  const iframe = document.createElement('iframe');
  iframe.style.cssText = 'position:fixed;top:-9999px;left:-9999px;width:1px;height:1px;border:none;visibility:hidden';
  document.body.appendChild(iframe);
  iframe.contentDocument.open();
  iframe.contentDocument.write(html);
  iframe.contentDocument.close();
  setTimeout(() => {
    iframe.contentWindow.focus();
    iframe.contentWindow.print();
    setTimeout(() => document.body.removeChild(iframe), 1500);
  }, 700);
  notify('Opening print dialog…', '🖨️');
};

// ═══════════════════════════════════════════════
//  COMPLAINT SYSTEM
// ═══════════════════════════════════════════════
const COMPLAINT_CATS = ['Academic','Facilities','Teacher Conduct','Bullying / Ragging','Fee / Financial','Administration','Transport','Hostel','Other'];
const COMPLAINT_STATUS_COLORS = { pending:'b-amber', 'in-review':'b-blue', resolved:'b-green', rejected:'b-red' };
const COMPLAINT_PRIORITY_COLORS = { low:'b-gray', medium:'b-blue', high:'b-amber', urgent:'b-red' };

// ── ADMIN VIEW ───────────────────────────────────────────────────────────────
function renderAdminComplaints() {
  const complaints = store.complaints || [];
  let filterStatus = 'all', filterRole = 'all';

  const render = () => {
    let list = [...complaints].sort((a,b) => new Date(b.createdAt)-new Date(a.createdAt));
    if (filterStatus !== 'all') list = list.filter(c => c.status === filterStatus);
    if (filterRole !== 'all') list = list.filter(c => c.fromRole === filterRole);

    const counts = { all: complaints.length, pending: complaints.filter(c=>c.status==='pending').length, 'in-review': complaints.filter(c=>c.status==='in-review').length, resolved: complaints.filter(c=>c.status==='resolved').length, rejected: complaints.filter(c=>c.status==='rejected').length };

    area().innerHTML = `
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:18px;flex-wrap:wrap;gap:10px">
      <div><div style="font-family:'Playfair Display',serif;font-size:22px;color:var(--navy)">📣 Complaints Management</div><div style="font-size:12px;color:var(--gray-400)">Review and respond to complaints from students & teachers</div></div>
      <button class="dl-btn" onclick="exportComplaints()">📥 Export</button>
    </div>

    <div class="stat-grid" style="margin-bottom:18px">
      <div class="stat-card"><div class="stat-icon" style="background:rgba(245,158,11,.1)">⏳</div><div class="stat-info"><h3>${counts.pending}</h3><p>Pending</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(37,99,235,.1)">🔍</div><div class="stat-info"><h3>${counts['in-review']}</h3><p>In Review</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(16,185,129,.1)">✅</div><div class="stat-info"><h3>${counts.resolved}</h3><p>Resolved</p></div></div>
      <div class="stat-card"><div class="stat-icon" style="background:rgba(100,116,139,.1)">📊</div><div class="stat-info"><h3>${complaints.length}</h3><p>Total</p></div></div>
    </div>

    <!-- Filters -->
    <div class="card" style="margin-bottom:16px">
      <div style="display:flex;gap:12px;flex-wrap:wrap;align-items:center">
        <div style="display:flex;gap:4px;background:var(--gray-100);border-radius:10px;padding:4px">
          ${['all','pending','in-review','resolved','rejected'].map(s=>`
            <button onclick="cmpFilterStatus('${s}')" style="padding:7px 12px;border:none;border-radius:7px;font-size:12px;font-weight:600;cursor:pointer;transition:all .2s;${filterStatus===s?'background:white;color:var(--navy);box-shadow:0 2px 8px rgba(0,0,0,.08)':'background:transparent;color:var(--gray-500)'}">
              ${s==='all'?`All (${counts.all})`:s==='in-review'?`In Review (${counts['in-review']})`:s.charAt(0).toUpperCase()+s.slice(1)+` (${counts[s]||0})`}
            </button>`).join('')}
        </div>
        <select class="fc" style="width:160px;margin:0" onchange="cmpFilterRole(this.value)">
          <option value="all">All Roles</option>
          <option value="student">Students Only</option>
          <option value="teacher">Teachers Only</option>
        </select>
      </div>
    </div>

    <!-- Complaints list -->
    <div class="card">
      ${list.length === 0 ? `<div class="empty"><div class="ei">📣</div><p>No complaints found.</p></div>` : `
      <div style="display:grid;gap:12px">
        ${list.map(c => {
          const isUrgent = c.priority === 'urgent';
          return `<div style="border:1.5px solid ${isUrgent?'var(--rose)':c.status==='resolved'?'rgba(16,185,129,.25)':'var(--gray-200)'};border-radius:12px;padding:16px;background:${isUrgent?'rgba(244,63,94,.03)':c.status==='resolved'?'rgba(16,185,129,.03)':'white'};position:relative;overflow:hidden">
            ${isUrgent?`<div style="position:absolute;top:0;left:0;right:0;height:3px;background:var(--rose)"></div>`:''}
            <div style="display:flex;justify-content:space-between;align-items:flex-start;gap:12px;flex-wrap:wrap">
              <div style="flex:1;min-width:200px">
                <div style="display:flex;align-items:center;gap:8px;margin-bottom:6px;flex-wrap:wrap">
                  <span class="badge ${COMPLAINT_STATUS_COLORS[c.status]||'b-gray'}">${c.status==='in-review'?'🔍 In Review':c.status==='resolved'?'✅ Resolved':c.status==='rejected'?'❌ Rejected':'⏳ Pending'}</span>
                  <span class="badge ${COMPLAINT_PRIORITY_COLORS[c.priority]||'b-gray'}">${c.priority||'medium'}</span>
                  <span class="badge b-gray">${c.category||'Other'}</span>
                  <span class="badge ${c.fromRole==='teacher'?'b-violet':'b-blue'}">${c.fromRole==='teacher'?'👨‍🏫 Teacher':'👨‍🎓 Student'}</span>
                </div>
                <div style="font-weight:700;font-size:14px;color:var(--navy);margin-bottom:4px">${c.subject}</div>
                <div style="font-size:12px;color:var(--gray-500);margin-bottom:8px">From: <b>${c.fromName}</b> (${c.from}) &nbsp;·&nbsp; ${new Date(c.createdAt).toLocaleDateString('en-IN',{day:'numeric',month:'short',year:'numeric',hour:'2-digit',minute:'2-digit'})}</div>
                <div style="font-size:13px;color:var(--gray-700);line-height:1.6;background:var(--gray-50);border-radius:8px;padding:10px 12px;margin-bottom:${c.adminNote?'8px':'0'}">${c.description}</div>
                ${c.adminNote?`<div style="font-size:12px;color:var(--emerald);background:rgba(16,185,129,.06);border:1px solid rgba(16,185,129,.2);border-radius:8px;padding:8px 12px;margin-top:6px"><b>Admin Reply:</b> ${c.adminNote}</div>`:''}
              </div>
              <div style="display:flex;flex-direction:column;gap:6px;flex-shrink:0">
                <button class="btn btn-primary btn-sm" onclick="adminReviewComplaint('${c.id}')">📝 Review & Action</button>
                ${c.status!=='resolved'?`<button class="btn btn-success btn-xs" onclick="adminQuickResolve('${c.id}')">✅ Resolve</button>`:''}
              </div>
            </div>
          </div>`;
        }).join('')}
      </div>`}
    </div>`;
  };

  window.cmpFilterStatus = (s) => { filterStatus = s; render(); };
  window.cmpFilterRole = (r) => { filterRole = r; render(); };
  render();
}

window.adminReviewComplaint = function(cid) {
  const c = (store.complaints||[]).find(x=>x.id===cid); if(!c) return;
  openMo('📝 Review Complaint', `
    <div style="background:var(--gray-50);border-radius:10px;padding:14px;margin-bottom:14px">
      <div style="display:flex;gap:8px;flex-wrap:wrap;margin-bottom:8px">
        <span class="badge ${COMPLAINT_STATUS_COLORS[c.status]||'b-gray'}">${c.status}</span>
        <span class="badge ${COMPLAINT_PRIORITY_COLORS[c.priority]||'b-gray'}">${c.priority||'medium'}</span>
        <span class="badge b-gray">${c.category||'Other'}</span>
      </div>
      <div style="font-weight:700;font-size:15px;color:var(--navy);margin-bottom:4px">${c.subject}</div>
      <div style="font-size:12px;color:var(--gray-500);margin-bottom:8px">From: <b>${c.fromName}</b> · ${c.fromRole} · ${new Date(c.createdAt).toLocaleString('en-IN')}</div>
      <div style="font-size:13px;color:var(--gray-700);line-height:1.6">${c.description}</div>
    </div>
    <div class="form-grid">
      <div class="fg">
        <label>Update Status</label>
        <select class="fc" id="cr_status">
          <option value="pending" ${c.status==='pending'?'selected':''}>⏳ Pending</option>
          <option value="in-review" ${c.status==='in-review'?'selected':''}>🔍 In Review</option>
          <option value="resolved" ${c.status==='resolved'?'selected':''}>✅ Resolved</option>
          <option value="rejected" ${c.status==='rejected'?'selected':''}>❌ Rejected</option>
        </select>
      </div>
      <div class="fg">
        <label>Priority</label>
        <select class="fc" id="cr_priority">
          <option value="low" ${c.priority==='low'?'selected':''}>Low</option>
          <option value="medium" ${c.priority==='medium'||!c.priority?'selected':''}>Medium</option>
          <option value="high" ${c.priority==='high'?'selected':''}>High</option>
          <option value="urgent" ${c.priority==='urgent'?'selected':''}>🚨 Urgent</option>
        </select>
      </div>
      <div class="fg form-full">
        <label>Admin Reply / Action Note</label>
        <textarea class="fc" id="cr_note" style="min-height:80px" placeholder="Write your response or action taken...">${c.adminNote||''}</textarea>
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="saveComplaintReview('${cid}')">💾 Save Response</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
};

window.saveComplaintReview = function(cid) {
  const c = (store.complaints||[]).find(x=>x.id===cid); if(!c) return;
  c.status = document.getElementById('cr_status').value;
  c.priority = document.getElementById('cr_priority').value;
  c.adminNote = document.getElementById('cr_note').value.trim();
  if (c.status === 'resolved' && !c.resolvedAt) c.resolvedAt = new Date().toISOString();
  saveStore();
  closeMo();
  notify(`Complaint updated — ${c.status}`, c.status==='resolved'?'✅':'📝');
  renderAdminComplaints();
};

window.adminQuickResolve = function(cid) {
  const c = (store.complaints||[]).find(x=>x.id===cid); if(!c) return;
  c.status = 'resolved';
  c.resolvedAt = new Date().toISOString();
  if (!c.adminNote) c.adminNote = 'Complaint has been reviewed and resolved by admin.';
  saveStore();
  notify('Complaint resolved!', '✅');
  renderAdminComplaints();
};

window.exportComplaints = function() {
  const data = (store.complaints||[]).map(c=>[c.id,c.fromName,c.from,c.fromRole,c.category||'',c.subject,c.description,c.priority||'medium',c.status,c.adminNote||'',new Date(c.createdAt).toLocaleDateString('en-IN'),c.resolvedAt?new Date(c.resolvedAt).toLocaleDateString('en-IN'):'']);
  exportToExcel(data,['ID','Name','User ID','Role','Category','Subject','Description','Priority','Status','Admin Note','Submitted On','Resolved On'],'SV_Complaints_'+new Date().toISOString().slice(0,10));
};

// ── STUDENT / TEACHER VIEW ────────────────────────────────────────────────────
function renderMyComplaints() {
  const myId = currentUser.id;
  const myRole = currentUser.role;
  const myComplaints = (store.complaints||[]).filter(c=>c.from===myId).sort((a,b)=>new Date(b.createdAt)-new Date(a.createdAt));

  area().innerHTML = `
  <div class="sec-hero" style="margin-bottom:18px">
    <div><h2>📣 My Complaints</h2><p>Submit complaints or grievances to the school administration</p></div>
    <button class="btn btn-primary btn-sm" onclick="showSubmitComplaint()">+ Submit Complaint</button>
  </div>

  ${myComplaints.length === 0 ? `
  <div class="card"><div class="empty"><div class="ei">📣</div><p>You haven't submitted any complaints yet.<br><button class="btn btn-primary" style="margin-top:12px" onclick="showSubmitComplaint()">Submit Your First Complaint</button></p></div></div>
  ` : `
  <div style="display:grid;gap:12px">
    ${myComplaints.map(c => `
    <div style="border:1.5px solid ${c.status==='resolved'?'rgba(16,185,129,.3)':c.status==='rejected'?'var(--gray-200)':'rgba(37,99,235,.2)'};border-radius:14px;padding:18px;background:white;overflow:hidden;position:relative">
      <div style="position:absolute;top:0;left:0;width:4px;height:100%;background:${c.status==='resolved'?'var(--emerald)':c.status==='rejected'?'var(--gray-300)':c.status==='in-review'?'var(--blue)':'var(--amber)'}"></div>
      <div style="padding-left:8px">
        <div style="display:flex;justify-content:space-between;align-items:flex-start;gap:10px;flex-wrap:wrap;margin-bottom:8px">
          <div>
            <div style="display:flex;gap:6px;flex-wrap:wrap;margin-bottom:6px">
              <span class="badge ${COMPLAINT_STATUS_COLORS[c.status]||'b-gray'}">${c.status==='in-review'?'🔍 In Review':c.status==='resolved'?'✅ Resolved':c.status==='rejected'?'❌ Rejected':'⏳ Pending'}</span>
              <span class="badge b-gray">${c.category||'Other'}</span>
              <span class="badge ${COMPLAINT_PRIORITY_COLORS[c.priority]||'b-gray'}">${c.priority||'medium'}</span>
            </div>
            <div style="font-weight:700;font-size:15px;color:var(--navy)">${c.subject}</div>
            <div style="font-size:11px;color:var(--gray-400);margin-top:3px">Submitted: ${new Date(c.createdAt).toLocaleDateString('en-IN',{day:'numeric',month:'long',year:'numeric'})}</div>
          </div>
          ${c.status==='pending'?`<button class="btn btn-danger btn-xs" onclick="withdrawComplaint('${c.id}')">Withdraw</button>`:''}
        </div>
        <div style="font-size:13px;color:var(--gray-700);line-height:1.6;background:var(--gray-50);border-radius:8px;padding:10px 12px;margin-bottom:${c.adminNote?'8px':'0'}">${c.description}</div>
        ${c.adminNote?`
        <div style="background:rgba(16,185,129,.06);border:1.5px solid rgba(16,185,129,.2);border-radius:8px;padding:12px;margin-top:8px">
          <div style="font-size:11px;font-weight:700;color:var(--emerald);text-transform:uppercase;letter-spacing:.4px;margin-bottom:4px">🏫 Admin Response</div>
          <div style="font-size:13px;color:var(--gray-700);line-height:1.6">${c.adminNote}</div>
          ${c.resolvedAt?`<div style="font-size:11px;color:var(--gray-400);margin-top:4px">Resolved on: ${new Date(c.resolvedAt).toLocaleDateString('en-IN')}</div>`:''}
        </div>`:''}
      </div>
    </div>`).join('')}
  </div>`}`;
}

window.showSubmitComplaint = function() {
  openMo('📣 Submit a Complaint', `
    <div style="background:rgba(37,99,235,.05);border:1.5px solid rgba(37,99,235,.15);border-radius:10px;padding:12px 14px;margin-bottom:16px;font-size:12.5px;color:var(--gray-600)">
      ℹ️ Your complaint will be sent to the school administration. You can track its status here. All submissions are confidential.
    </div>
    <div class="form-grid">
      <div class="fg">
        <label>Category *</label>
        <select class="fc" id="nc_cat">
          ${COMPLAINT_CATS.map(c=>`<option>${c}</option>`).join('')}
        </select>
      </div>
      <div class="fg">
        <label>Priority</label>
        <select class="fc" id="nc_priority">
          <option value="low">Low — Minor issue</option>
          <option value="medium" selected>Medium — Needs attention</option>
          <option value="high">High — Urgent matter</option>
          <option value="urgent">🚨 Urgent — Immediate action needed</option>
        </select>
      </div>
      <div class="fg form-full">
        <label>Subject *</label>
        <input class="fc" id="nc_subject" placeholder="Brief title of your complaint">
      </div>
      <div class="fg form-full">
        <label>Description *</label>
        <textarea class="fc" id="nc_desc" style="min-height:110px" placeholder="Describe your complaint in detail. Include dates, names, and any relevant information..."></textarea>
      </div>
    </div>
    <div style="display:flex;gap:8px;margin-top:14px">
      <button class="btn btn-primary" onclick="submitComplaint()">📤 Submit Complaint</button>
      <button class="btn btn-outline" onclick="closeMo()">Cancel</button>
    </div>
  `);
};

window.submitComplaint = function() {
  const subject = document.getElementById('nc_subject')?.value.trim();
  const desc = document.getElementById('nc_desc')?.value.trim();
  const cat = document.getElementById('nc_cat')?.value;
  const priority = document.getElementById('nc_priority')?.value;

  if (!subject) { alert('Please enter a subject for your complaint.'); return; }
  if (!desc || desc.length < 10) { alert('Please write a detailed description (at least 10 characters).'); return; }

  if (!store.complaints) store.complaints = [];
  const cid = 'CMP-' + Date.now();
  store.complaints.push({
    id: cid,
    from: currentUser.id,
    fromName: currentUser.name || currentUser.id,
    fromRole: currentUser.role,
    category: cat,
    priority,
    subject,
    description: desc,
    status: 'pending',
    adminNote: '',
    createdAt: new Date().toISOString(),
    resolvedAt: null
  });
  saveStore();
  closeMo();
  notify('Complaint submitted successfully! Admin will review it soon.', '📣');
  renderMyComplaints();
};

window.withdrawComplaint = function(cid) {
  if (!confirm('Withdraw this complaint? It will be permanently deleted.')) return;
  store.complaints = (store.complaints||[]).filter(c=>c.id!==cid);
  saveStore();
  notify('Complaint withdrawn.', '🗑️');
  renderMyComplaints();
};

// ═══════════════════════════════════════════════
//  BOOT
// ═══════════════════════════════════════════════
document.getElementById('topbarDate').textContent = new Date().toLocaleDateString('en-IN',{weekday:'short',day:'numeric',month:'short',year:'numeric'});
</script>
</body>
</html>
