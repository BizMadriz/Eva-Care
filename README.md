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
            <p class="text-[10px] text-slate-400 mt-4 italic">Si tienes más de un bebé, podrás añadirlos luego desde el menú de ajustes.</p>
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
            <button onclick="toggleMenu()" class="text-slate-400 text-xl p-2"><i class="fa-solid fa-gear"></i></button>
        </div>
        <div class="grid grid-cols-2 gap-2 mt-4 text-center text-[10px]">
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="font-bold text-slate-400 uppercase text-[8px]">Nacimiento</p>
                <p class="font-black text-slate-700"><span id="top-peso-nac">--</span> | <span id="top-talla-nac">--</span></p>
            </div>
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="font-bold text-slate-400 uppercase text-[8px]">Último Control (<span id="top-fecha-act">--</span>)</p>
                <p class="font-black text-slate-700"><span id="top-peso-act">--</span> | <span id="top-talla-act">--</span></p>
            </div>
        </div>
    </header>

    <div id="side-menu" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex justify-end">
        <div class="bg-white w-72 h-full p-6 shadow-xl space-y-6">
            <div class="flex justify-between items-center">
                <h3 class="font-black text-slate-800">Ajustes</h3>
                <button onclick="toggleMenu()"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="space-y-3 pt-4">
                <button onclick="alert('Funcionalidad multi-bebé en desarrollo')" class="w-full flex items-center gap-3 p-4 bg-blue-50 rounded-xl font-bold text-blue-700">
                    <i class="fa-solid fa-baby"></i> Añadir otro bebé
                </button>
                <button onclick="alert('Próximamente')" class="w-full flex items-center gap-3 p-4 bg-slate-50 rounded-xl font-bold text-slate-600">
                    <i class="fa-solid fa-share-nodes text-green-500"></i> Compartir datos
                </button>
                <hr>
                <button onclick="borrarTodo()" class="w-full flex items-center gap-3 p-4 bg-red-50 rounded-xl font-bold text-red-600">
                    <i class="fa-solid fa-trash"></i> Borrar todos los datos
                </button>
            </div>
        </div>
    </div>

    <main class="p-4 max-w-md mx-auto space-y-6">
        <section id="home-timers" class="space-y-3">
            <h3 class="text-xs font-bold text-slate-400 uppercase ml-1">Próximos eventos y tomas</h3>
            <div id="timers-container" class="space-y-2"></div>
        </section>

        <nav class="flex justify-around bg-white rounded-2xl shadow-sm border text-slate-400 overflow-hidden">
            <button onclick="showTab('diario')" id="tab-diario" class="p-4 active-tab flex-1"><i class="fa-solid fa-baby"></i></button>
            <button onclick="showTab('calendario')" id="tab-calendario" class="p-4 flex-1"><i class="fa-solid fa-calendar-days"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-4 flex-1"><i class="fa-solid fa-stethoscope"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="openPechoModal()" class="col-span-2 bg-pink-50 p-6 rounded-2xl text-pink-700 font-bold shadow-sm flex items-center justify-center gap-3">
                    <i class="fa-solid fa-breast-pumping"></i> Registrar Pecho
                </button>
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
                <div id="days-grid-header" class="grid grid-cols-7 gap-1 text-center text-[10px] font-bold text-slate-300 mb-1">
                    <div>L</div><div>M</div><div>X</div><div>J</div><div>V</div><div>S</div><div>D</div>
                </div>
                <div id="days-container" class="grid grid-cols-7 gap-1"></div>
            </div>
            <div id="day-detail" class="bg-blue-50 p-4 rounded-3xl border border-blue-100 hidden">
                <div class="flex justify-between items-center mb-3">
                    <h5 id="detail-date" class="font-bold text-blue-800 text-sm"></h5>
                    <button onclick="openEventModal()" class="bg-blue-600 text-white text-[10px] px-3 py-1 rounded-full">+ Nuevo Evento</button>
                </div>
                <div id="detail-events" class="space-y-2"></div>
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
                <h3 class="font-bold text-slate-800 mb-3 text-sm">Nuevo Peso / Talla</h3>
                <div class="grid grid-cols-2 gap-2">
                    <input id="input-peso" type="number" step="0.01" placeholder="Kg" class="p-3 bg-slate-50 rounded-xl">
                    <input id="input-talla" type="number" placeholder="cm" class="p-3 bg-slate-50 rounded-xl">
                    <button onclick="updateGrowth()" class="col-span-2 bg-blue-600 text-white p-3 rounded-xl font-bold">Guardar</button>
                </div>
            </div>
        </section>
    </main>

    <div id="pecho-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-end">
        <div class="bg-white w-full p-6 rounded-t-3xl space-y-4">
            <h3 class="font-black text-center text-slate-800">¿Qué pecho?</h3>
            <div class="grid grid-cols-3 gap-3">
                <button onclick="registrarPecho('P. Izquierdo')" class="bg-blue-50 p-4 rounded-xl font-bold text-blue-700">Izquierdo</button>
                <button onclick="registrarPecho('Ambos Pechos')" class="bg-purple-50 p-4 rounded-xl font-bold text-purple-700">Ambos</button>
                <button onclick="registrarPecho('P. Derecho')" class="bg-blue-50 p-4 rounded-xl font-bold text-blue-700">Derecho</button>
            </div>
            <button onclick="closePechoModal()" class="w-full p-3 text-slate-400">Cancelar</button>
        </div>
    </div>

    <div id="event-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-3xl p-6 space-y-4 shadow-2xl">
            <h3 class="font-black">Nuevo Evento</h3>
            <input id="ev-titulo" type="text" placeholder="Título (Cita médica, vacunas...)" class="w-full p-4 bg-slate-50 rounded-xl">
            <input id="ev-hora" type="time" class="w-full p-4 bg-slate-50 rounded-xl">
            <button onclick="saveCustomEvent()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold">Guardar Evento</button>
            <button onclick="closeEventModal()" class="w-full text-slate-400">Cancelar</button>
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
                renderHeader();
                renderLog();
                renderMedicina();
                setInterval(updateAllTimers, 1000);
            }
        }

        // --- EDAD Y CABECERA ---
        function getEdadTexto(f) {
            const dias = Math.floor((new Date() - new Date(f)) / 86400000);
            return dias < 30 ? `${dias} días` : `${Math.floor(dias/30.44)} meses`;
        }

        function renderHeader() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = getEdadTexto(bData.fecha);
            const nac = bData.crecimiento[0];
            const act = bData.crecimiento[bData.crecimiento.length - 1];
            document.getElementById('top-peso-nac').innerText = nac.p + 'kg';
            document.getElementById('top-talla-nac').innerText = nac.m + 'cm';
            document.getElementById('top-peso-act').innerText = act.p + 'kg';
            document.getElementById('top-talla-act').innerText = act.m + 'cm';
            document.getElementById('top-fecha-act').innerText = act.fecha.split('-').reverse().slice(0,2).join('/');
            
            if(bData.lastBib) {
                document.getElementById('btn-last-bib').innerText = `Repetir Biberón (${bData.lastBib}ml)`;
                document.getElementById('btn-last-bib').classList.remove('hidden');
            }
        }

        // --- TIMERS Y ALERTAS ---
        function updateAllTimers() {
            const container = document.getElementById('timers-container');
            let html = "";
            const ahora = Date.now();

            // 1. Toma (Bibe/Pecho)
            const lt = localStorage.getItem('last_toma_time');
            if(lt) html += renderTimerRow("🍼 Próxima Toma", parseInt(lt) + 10800000 - ahora);

            // 2. Medicinas
            bData.medicinas.forEach(m => {
                if(m.ultimaToma) {
                    const prox = m.ultimaToma + (m.cada * 3600000);
                    html += renderTimerRow(`💊 ${m.nombre}`, prox - ahora);
                    if(ahora >= prox && !notifiedMeds.has(`${m.nombre}-${prox}`)) {
                        document.getElementById('alert-sound').play();
                        if(Notification.permission==="granted") new Notification("Toca Medicina", {body: m.nombre});
                        notifiedMeds.add(`${m.nombre}-${prox}`);
                    }
                }
            });

            // 3. Eventos del día
            const hoyIso = new Date().toISOString().split('T')[0];
            if(bData.eventos[hoyIso]) {
                bData.eventos[hoyIso].forEach(e => {
                    const [h, m] = e.hora.split(':');
                    const eMs = new Date().setHours(h, m, 0, 0);
                    if(eMs > ahora) html += renderTimerRow(`📅 ${e.titulo}`, eMs - ahora);
                });
            }

            container.innerHTML = html || '<p class="text-[10px] text-center opacity-30 italic">Sin plazos activos</p>';
        }

        function renderTimerRow(label, diff) {
            const s = Math.floor(diff/1000);
            if(s <= 0) return `<div class="bg-red-500 text-white p-3 rounded-xl flex justify-between font-black animate-pulse text-xs"><span>${label}</span><span>¡YA!</span></div>`;
            const time = `${Math.floor(s/3600)}:${String(Math.floor((s%3600)/60)).padStart(2,'0')}:${String(s%60).padStart(2,'0')}`;
            return `<div class="bg-white p-3 rounded-xl border flex justify-between text-[11px] font-bold text-slate-600"><span>${label}</span><span class="text-blue-600 font-mono">${time}</span></div>`;
        }

        // --- CALENDARIO ---
        function renderFullCalendar() {
            const grid = document.getElementById('days-container');
            grid.innerHTML = '';
            const y = viewDate.getFullYear(), m = viewDate.getMonth();
            document.getElementById('month-name').innerText = viewDate.toLocaleDateString('es', { month: 'long', year: 'numeric' });
            const first = new Date(y, m, 1).getDay(), days = new Date(y, m + 1, 0).getDate();
            const offset = first === 0 ? 6 : first - 1;
            for(let i=0; i<offset; i++) grid.innerHTML += `<div></div>`;
            for(let d=1; d<=days; d++) {
                const ds = `${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
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
            document.getElementById('detail-date').innerText = iso.split('-').reverse().join('/');
            const evs = bData.eventos[iso] || [];
            document.getElementById('detail-events').innerHTML = evs.map((e, i) => `
                <div class="flex justify-between items-center bg-white p-3 rounded-xl shadow-sm text-xs">
                    <span><b>${e.hora}</b> - ${e.titulo}</span>
                    <button onclick="borrarEv('${iso}',${i})" class="text-red-300 p-1"><i class="fa-solid fa-trash"></i></button>
                </div>`).join('') || '<p class="opacity-40 italic text-xs text-center">Sin eventos para este día</p>';
        }

        function saveCustomEvent() {
            const t = document.getElementById('ev-titulo').value, h = document.getElementById('ev-hora').value;
            if(!t || !h) return;
            if(!bData.eventos[selectedDayStr]) bData.eventos[selectedDayStr] = [];
            bData.eventos[selectedDayStr].push({ titulo: t, hora: h });
            bData.eventos[selectedDayStr].sort((a,b) => a.hora.localeCompare(b.hora));
            save(); closeEventModal(); selectCalendarDay(selectedDayStr);
        }

        // --- OTROS ---
        function registrar(txt) {
            const iso = new Date().toISOString().split('T')[0], h = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            if(!bData.logs[iso]) bData.logs[iso] = [];
            bData.logs[iso].unshift({txt, h});
            if(txt.includes('Biberón') || txt.includes('🤱')) localStorage.setItem('last_toma_time', Date.now());
            save(); renderLog();
        }

        function updateGrowth() {
            const p = document.getElementById('input-peso').value, t = document.getElementById('input-talla').value;
            if(p && t) { 
                bData.crecimiento.push({fecha: new Date().toISOString().split('T')[0], p, m: t}); 
                save(); renderHeader(); alert("Guardado");
            }
        }

        function tomarMedicina(idx) {
            const m = bData.medicinas[idx], ahora = Date.now();
            if(m.ultimaToma && (ahora - m.ultimaToma) < m.cada * 3600000) {
                if(!confirm("Aún no toca. ¿Registrar de todos modos?")) return;
            }
            bData.medicinas[idx].ultimaToma = ahora;
            registrar(`💊 ${m.nombre}`);
            save(); renderMedicina();
        }

        function showTab(t) {
            ['diario','calendario','salud'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
            if(t==='calendario') renderFullCalendar();
        }

        function openEventModal() { document.getElementById('event-modal').classList.remove('hidden'); }
        function closeEventModal() { document.getElementById('event-modal').classList.add('hidden'); }
        function openPechoModal() { document.getElementById('pecho-modal').classList.remove('hidden'); }
        function closePechoModal() { document.getElementById('pecho-modal').classList.add('hidden'); }
        function registrarPecho(tipo) { registrar(`🤱 ${tipo}`); closePechoModal(); }
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('hidden'); }
        function borrarTodo() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }
        function save() { localStorage.setItem('baby_pro_data', JSON.stringify(bData)); }
        function nextStep(s) { document.querySelectorAll('.setup-step').forEach(e => e.classList.add('hidden')); document.getElementById('step-'+s).classList.remove('hidden'); }
        function finalizarSetup() {
            const p = document.getElementById('setup-peso').value, m = document.getElementById('setup-medida').value;
            bData = { nombre: document.getElementById('setup-nombre').value, fecha: document.getElementById('setup-fecha').value, logs: {}, crecimiento: [{fecha: new Date().toISOString().split('T')[0], p, m}], medicinas: [], eventos: {} };
            save(); location.reload();
        }
        function renderLog() {
            const logs = bData.logs[new Date().toISOString().split('T')[0]] || [];
            document.getElementById('log-recent').innerHTML = logs.slice(0,5).map(l => `<div class="bg-white p-3 rounded-xl flex justify-between text-xs shadow-sm border-l-4 border-blue-400"><span>${l.txt}</span><span class="text-slate-300">${l.h}</span></div>`).join('');
        }
        function nuevoMedicamento() { const n = prompt("Nombre:"); const c = prompt("Horas:", "8"); if(n&&c) { bData.medicinas.push({nombre:n, cada:parseInt(c), ultimaToma:null}); save(); renderMedicina(); } }
        function renderMedicina() { document.getElementById('lista-medicinas').innerHTML = bData.medicinas.map((m, idx) => `<div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center"><div class="font-bold text-slate-800">${m.nombre}</div><button onclick="tomarMedicina(${idx})" class="bg-blue-600 text-white p-3 rounded-xl"><i class="fa-solid fa-check"></i></button></div>`).join('') || '<p class="text-center opacity-30 text-xs">Sin medicinas</p>'; }
        function promptBiberon() { const ml = prompt("ml?"); if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderHeader(); } }
        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }
        function borrarEv(iso, i) { bData.eventos[iso].splice(i,1); save(); selectCalendarDay(iso); }
        function changeMonth(d) { viewDate.setMonth(viewDate.getMonth()+d); renderFullCalendar(); }

        init();
    </script>
</body>
</html>
