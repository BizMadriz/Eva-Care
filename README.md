<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        .bg-baby { background-color: #f8fafc; }
        @keyframes pulse-red { 0%, 100% { background-color: #fee2e2; } 50% { background-color: #fecaca; } }
        .animate-med-alert { animation: pulse-red 2s infinite; }
    </style>
</head>
<body class="bg-baby pb-24 font-sans overflow-x-hidden">

    <audio id="alert-sound" src="https://assets.mixkit.co/active_storage/sfx/2869/2869-preview.mp3" preload="auto"></audio>

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-8 flex flex-col justify-center text-center">
        <div id="step-1" class="setup-step space-y-4">
            <h2 class="text-3xl font-black text-slate-800">¿Nombre del bebé?</h2>
            <input id="setup-nombre" type="text" class="w-full p-4 bg-blue-50 rounded-2xl outline-none text-xl font-bold text-blue-600 text-center">
            <button onclick="nextStep(2)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
        </div>
        <div id="step-2" class="setup-step hidden space-y-4">
            <h2 class="text-3xl font-black text-slate-800">¿Nacimiento?</h2>
            <input id="setup-fecha" type="date" class="w-full p-4 bg-blue-50 rounded-2xl outline-none text-xl font-bold text-blue-600">
            <button onclick="nextStep(3)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
        </div>
        <div id="step-3" class="setup-step hidden space-y-4">
            <h2 class="text-3xl font-black text-slate-800">Peso y Talla al nacer</h2>
            <div class="flex gap-2">
                <input id="setup-peso" type="number" step="0.01" placeholder="Kg" class="w-1/2 p-4 bg-blue-50 rounded-2xl font-bold">
                <input id="setup-medida" type="number" placeholder="cm" class="w-1/2 p-4 bg-blue-50 rounded-2xl font-bold">
            </div>
            <button onclick="finalizarSetup()" class="w-full bg-green-600 text-white p-5 rounded-2xl font-bold shadow-xl">Empezar</button>
        </div>
    </div>

    <header class="bg-white p-5 shadow-sm sticky top-0 z-40">
        <div class="flex justify-between items-start">
            <div>
                <h1 id="top-nombre" class="text-2xl font-black text-slate-800 leading-none">--</h1>
                <p id="top-edad" class="text-blue-500 font-bold text-sm mt-1">--</p>
            </div>
            <button onclick="openSettings()" class="text-slate-300"><i class="fa-solid fa-gear"></i></button>
        </div>
        <div class="grid grid-cols-2 gap-2 mt-4 text-center text-xs">
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="font-bold text-slate-400 uppercase text-[8px]">Último Peso</p>
                <p id="top-peso" class="font-black text-slate-700">--</p>
            </div>
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="font-bold text-slate-400 uppercase text-[8px]">Última Talla</p>
                <p id="top-medida" class="font-black text-slate-700">--</p>
            </div>
        </div>
    </header>

    <main class="p-4 max-w-md mx-auto space-y-6">
        <div id="med-alerts" class="space-y-2"></div>

        <section id="agenda-home" class="space-y-3">
            <h3 class="text-xs font-bold text-slate-400 uppercase ml-1">Eventos Próximos</h3>
            <div id="eventos-proximos" class="space-y-2"></div>
        </section>

        <nav class="flex justify-around bg-white rounded-2xl shadow-sm border text-slate-400 overflow-hidden">
            <button onclick="showTab('diario')" id="tab-diario" class="p-4 active-tab flex-1"><i class="fa-solid fa-baby"></i></button>
            <button onclick="showTab('calendario')" id="tab-calendario" class="p-4 flex-1"><i class="fa-solid fa-calendar-days"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-4 flex-1"><i class="fa-solid fa-stethoscope"></i></button>
            <button onclick="showTab('timer')" id="tab-timer" class="p-4 flex-1"><i class="fa-solid fa-clock"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="registrar('🤱 P. Izquierdo')" class="bg-pink-50 p-4 rounded-2xl text-pink-700 font-bold">P. IZQ</button>
                <button onclick="registrar('🤱 P. Derecho')" class="bg-pink-50 p-4 rounded-2xl text-pink-700 font-bold">P. DER</button>
                <div class="col-span-2 space-y-2">
                    <button id="btn-last-bib" onclick="registrarLastBiberon()" class="hidden w-full bg-blue-600 text-white p-4 rounded-2xl font-bold shadow-lg"></button>
                    <button onclick="promptBiberon()" class="w-full bg-blue-50 p-4 rounded-2xl text-blue-700 font-bold border border-blue-100">🍼 Nuevo Biberón...</button>
                </div>
                <button onclick="registrar('💦 Pis')" class="bg-yellow-50 p-4 rounded-2xl text-yellow-700 font-bold">💦 Pis</button>
                <button onclick="registrar('💩 Caca')" class="bg-orange-50 p-4 rounded-2xl text-orange-900 font-bold">💩 Caca</button>
            </div>
            <div id="log-recent" class="space-y-2"></div>
        </section>

        <section id="sec-calendario" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border">
                <div class="flex justify-between items-center mb-4 px-2">
                    <h4 id="month-name" class="font-black text-slate-800 capitalize"></h4>
                    <div class="flex gap-4 text-blue-600"><button onclick="changeMonth(-1)"><i class="fa-solid fa-chevron-left"></i></button><button onclick="changeMonth(1)"><i class="fa-solid fa-chevron-right"></i></button></div>
                </div>
                <div id="days-grid" class="grid grid-cols-7 gap-1 mt-2 text-center text-[10px] font-bold text-slate-300">
                    <div>L</div><div>M</div><div>X</div><div>J</div><div>V</div><div>S</div><div>D</div>
                </div>
                <div id="days-container" class="grid grid-cols-7 gap-1 mt-1"></div>
            </div>
            <div id="day-detail" class="bg-blue-50 p-4 rounded-3xl border border-blue-100 hidden">
                <div class="flex justify-between items-center mb-3">
                    <h5 id="detail-date" class="font-bold text-blue-800 text-sm"></h5>
                    <button onclick="openEventModal()" class="bg-blue-600 text-white text-[10px] px-3 py-1 rounded-full">+ Nuevo Evento</button>
                </div>
                <div id="detail-events" class="space-y-2 text-xs text-slate-600"></div>
            </div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-bold text-slate-800 text-sm">Medicación</h3>
                    <button onclick="nuevoMedicamento()" class="text-blue-600 text-xs font-bold">+ Añadir</button>
                </div>
                <div id="lista-medicinas" class="space-y-3"></div>
            </div>
            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <h3 class="font-bold text-slate-800 mb-3 text-sm">Nuevo Peso/Talla</h3>
                <div class="flex gap-2">
                    <input id="input-peso" type="number" step="0.01" placeholder="Kg" class="flex-1 p-3 bg-slate-50 rounded-xl outline-none">
                    <input id="input-talla" type="number" placeholder="cm" class="flex-1 p-3 bg-slate-50 rounded-xl outline-none">
                    <button onclick="updateGrowth()" class="bg-blue-600 text-white px-4 rounded-xl"><i class="fa-solid fa-check"></i></button>
                </div>
            </div>
        </section>

        <section id="sec-timer" class="hidden space-y-6 text-center pt-6">
            <div class="w-48 h-48 mx-auto flex items-center justify-center border-[12px] border-blue-100 rounded-full bg-white shadow-xl">
                <span id="timer-val" class="text-3xl font-black text-slate-700">00:00:00</span>
            </div>
        </section>
    </main>

    <div id="event-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-3xl p-6 space-y-4">
            <h3 class="font-black text-slate-800">Nuevo Evento</h3>
            <input id="ev-titulo" type="text" placeholder="¿Qué evento es?" class="w-full p-3 bg-slate-50 rounded-xl outline-none">
            <div class="flex gap-2">
                <input id="ev-fecha" type="date" class="w-1/2 p-3 bg-slate-50 rounded-xl text-xs">
                <input id="ev-hora" type="time" class="w-1/2 p-3 bg-slate-50 rounded-xl text-xs">
            </div>
            <div class="flex gap-2 pt-2">
                <button onclick="closeEventModal()" class="flex-1 p-3 text-slate-400 font-bold">Cancelar</button>
                <button onclick="saveCustomEvent()" class="flex-1 p-3 bg-blue-600 text-white rounded-xl font-bold">Guardar</button>
            </div>
        </div>
    </div>

    <script>
        let bData = {}; 
        let viewDate = new Date();
        let selectedDayStr = "";
        let notifiedMeds = new Set();

        function init() {
            if ("Notification" in window) Notification.requestPermission();
            bData = JSON.parse(localStorage.getItem('baby_pro_data'));
            if(!bData) {
                document.getElementById('setup-screen').classList.remove('hidden');
            } else {
                if(!bData.eventos) bData.eventos = {};
                if(!bData.medicinas) bData.medicinas = [];
                renderHeader();
                renderHome();
                renderLog();
                renderMedicina();
                setInterval(checkMedAlerts, 1000);
            }
        }

        function playAlert() { document.getElementById('alert-sound').play().catch(() => {}); }

        function sendPush(t, m) {
            if (Notification.permission === "granted") new Notification(t, { body: m });
        }

        function checkMedAlerts() {
            const alertsCont = document.getElementById('med-alerts');
            let html = "";
            const ahora = Date.now();
            bData.medicinas.forEach(m => {
                if(m.ultimaToma) {
                    const proxima = m.ultimaToma + (m.cada * 3600000);
                    if(ahora >= proxima) {
                        html += `<div onclick="showTab('salud')" class="animate-med-alert p-4 rounded-2xl border-2 border-red-200 flex items-center gap-3">
                            <div class="bg-red-500 text-white p-2 rounded-lg"><i class="fa-solid fa-bell"></i></div>
                            <div><p class="text-xs font-black text-red-700 uppercase">TOCA ${m.nombre}</p></div>
                        </div>`;
                        const alertId = `${m.nombre}-${proxima}`;
                        if(!notifiedMeds.has(alertId)) { playAlert(); sendPush("Medicina", `Toca ${m.nombre}`); notifiedMeds.add(alertId); }
                    }
                }
            });
            alertsCont.innerHTML = html;
            updateTimer(); 
        }

        function nuevoMedicamento() {
            const n = prompt("Nombre:"); const c = prompt("Horas:", "8");
            if(n && c) { bData.medicinas.push({ nombre: n, cada: parseInt(c), ultimaToma: null }); save(); renderMedicina(); }
        }

        function tomarMedicina(idx) { bData.medicinas[idx].ultimaToma = Date.now(); registrar(`💊 Toma: ${bData.medicinas[idx].nombre}`); save(); renderMedicina(); renderHome(); }

        function renderMedicina() {
            document.getElementById('lista-medicinas').innerHTML = bData.medicinas.map((m, idx) => `
                <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center">
                    <div><p class="font-bold text-slate-800">${m.nombre}</p></div>
                    <button onclick="tomarMedicina(${idx})" class="bg-blue-600 text-white p-3 rounded-xl"><i class="fa-solid fa-check"></i></button>
                </div>`).join('') || '<p class="text-xs opacity-40 text-center">Sin medicinas</p>';
        }

        function openEventModal() {
            document.getElementById('event-modal').classList.remove('hidden');
            document.getElementById('ev-fecha').value = selectedDayStr;
        }

        function closeEventModal() { document.getElementById('event-modal').classList.add('hidden'); }

        function saveCustomEvent() {
            const t = document.getElementById('ev-titulo').value, f = document.getElementById('ev-fecha').value, h = document.getElementById('ev-hora').value;
            if(t && f) {
                if(!bData.eventos[f]) bData.eventos[f] = [];
                bData.eventos[f].push({ titulo: t, hora: h });
                save(); closeEventModal(); selectCalendarDay(f); renderHome();
            }
        }

        function renderFullCalendar() {
            const grid = document.getElementById('days-container');
            grid.innerHTML = '';
            const year = viewDate.getFullYear(), month = viewDate.getMonth();
            document.getElementById('month-name').innerText = viewDate.toLocaleDateString('es', { month: 'long', year: 'numeric' });
            const first = new Date(year, month, 1).getDay(), days = new Date(year, month + 1, 0).getDate();
            const offset = first === 0 ? 6 : first - 1;
            for(let i=0; i<offset; i++) grid.innerHTML += `<div></div>`;
            for(let d=1; d<=days; d++) {
                const ds = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
                const hasEv = bData.eventos[ds] && bData.eventos[ds].length > 0;
                const isHoy = new Date().toISOString().split('T')[0] === ds;
                grid.innerHTML += `<div onclick="selectCalendarDay('${ds}')" class="aspect-square flex flex-col items-center justify-center rounded-xl relative ${isHoy?'bg-blue-600 text-white':'text-slate-700'} ${selectedDayStr===ds?'ring-2 ring-blue-300':''}">
                    <span class="text-xs font-bold">${d}</span>
                    ${hasEv ? '<div class="w-1 h-1 bg-orange-400 rounded-full absolute bottom-1"></div>' : ''}
                </div>`;
            }
        }

        function selectCalendarDay(iso) {
            selectedDayStr = iso; renderFullCalendar();
            document.getElementById('day-detail').classList.remove('hidden');
            document.getElementById('detail-date').innerText = iso;
            const evs = bData.eventos[iso] || [];
            document.getElementById('detail-events').innerHTML = evs.map((e, i) => `
                <div class="flex justify-between items-center bg-white p-2 rounded-lg shadow-sm">
                    <span>${e.hora} - ${e.titulo}</span>
                    <button onclick="borrarEv('${iso}',${i})" class="text-red-300">×</button>
                </div>`).join('') || '<p class="opacity-40 italic">Sin eventos</p>';
        }

        function borrarEv(iso, i) { bData.eventos[iso].splice(i,1); save(); selectCalendarDay(iso); renderHome(); }
        function changeMonth(d) { viewDate.setMonth(viewDate.getMonth()+d); renderFullCalendar(); }

        function renderHome() {
            const container = document.getElementById('eventos-proximos');
            const hoy = new Date();
            const limite = new Date(); limite.setDate(hoy.getDate() + 14);
            let evs = [];
            for(let d = new Date(hoy); d <= limite; d.setDate(d.getDate()+1)) {
                const iso = d.toISOString().split('T')[0];
                if(bData.eventos[iso]) bData.eventos[iso].forEach(e => evs.push({f:iso, h:e.hora, t:e.titulo}));
            }
            container.innerHTML = evs.map(e => `<div class="bg-white p-3 rounded-2xl flex justify-between shadow-sm border-l-4 border-orange-400"><span class="text-xs font-bold">${e.t}</span><span class="text-[9px] font-black text-slate-300">${e.h} | ${e.f.split('-')[2]}/${e.f.split('-')[1]}</span></div>`).join('') || '<p class="text-center text-xs text-slate-300">Nada próximo</p>';
        }

        function save() { localStorage.setItem('baby_pro_data', JSON.stringify(bData)); }
        
        function registrar(txt) {
            const iso = new Date().toISOString().split('T')[0], h = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            if(!bData.logs[iso]) bData.logs[iso] = [];
            bData.logs[iso].unshift({txt, h});
            save(); renderLog();
            localStorage.setItem('next_toma', Date.now() + (3*60*60*1000));
        }

        function updateTimer() {
            const t = localStorage.getItem('next_toma'), disp = document.getElementById('timer-val');
            if(!t) return;
            const d = t - Date.now();
            if(d > 0) {
                const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000);
                disp.innerText = `${h}:${m}:${s}`.replace(/\b\d\b/g, '0$&');
            } else { disp.innerText = "TOMA!"; }
        }

        function showTab(t) {
            ['diario','calendario','salud','timer'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
            if(t === 'calendario') renderFullCalendar();
        }

        function nextStep(s) { document.querySelectorAll('.setup-step').forEach(e => e.classList.add('hidden')); document.getElementById('step-'+s).classList.remove('hidden'); }
        function finalizarSetup() {
            bData = { nombre: document.getElementById('setup-nombre').value, fecha: document.getElementById('setup-fecha').value, logs: {}, crecimiento: [{fecha: new Date().toISOString().split('T')[0], p: document.getElementById('setup-peso').value, m: document.getElementById('setup-medida').value}], medicinas: [], eventos: {} };
            save(); location.reload();
        }
        function renderHeader() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = (Math.floor((new Date() - new Date(bData.fecha)) / (1000*60*60*24*30))) + " meses";
            const lastG = bData.crecimiento[bData.crecimiento.length - 1];
            document.getElementById('top-peso').innerText = lastG.p + ' kg';
            document.getElementById('top-medida').innerText = lastG.m + ' cm';
            if(bData.lastBib) { document.getElementById('btn-last-bib').innerText = `Repetir Biberón (${bData.lastBib}ml)`; document.getElementById('btn-last-bib').classList.remove('hidden'); }
        }
        function renderLog() {
            const logs = bData.logs[new Date().toISOString().split('T')[0]] || [];
            document.getElementById('log-recent').innerHTML = logs.slice(0,5).map(l => `<div class="bg-white p-3 rounded-xl flex justify-between text-xs shadow-sm border-l-4 border-blue-400"><span>${l.txt}</span><span class="text-slate-300">${l.h}</span></div>`).join('');
        }
        function promptBiberon() { const ml = prompt("ml?"); if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderHeader(); } }
        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }
        function updateGrowth() {
            const p = document.getElementById('input-peso').value, m = document.getElementById('input-talla').value;
            if(p && m) { bData.crecimiento.push({fecha: new Date().toISOString().split('T')[0], p, m}); save(); renderHeader(); alert("¡Ok!"); }
        }
        function openSettings() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        init();
    </script>
</body>
</html>
