<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro - Calendario Local</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        .bg-baby { background-color: #f8fafc; }
        @keyframes pulse-red { 0%, 100% { color: #dc2626; } 50% { color: #f87171; } }
        .animate-alert { animation: pulse-red 1s infinite; }
    </style>
</head>
<body class="bg-baby pb-24 font-sans overflow-x-hidden">

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-8 flex flex-col justify-center text-center">
        <div id="step-1" class="setup-step space-y-4">
            <h2 class="text-3xl font-black text-slate-800">¿Cómo se llama tu bebé?</h2>
            <input id="setup-nombre" type="text" placeholder="Nombre" class="w-full p-4 bg-blue-50 rounded-2xl outline-none text-xl font-bold text-blue-600 text-center">
            <button onclick="nextStep(2)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
        </div>
        <div id="step-2" class="setup-step hidden space-y-4">
            <h2 class="text-3xl font-black text-slate-800">¿Cuándo nació?</h2>
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
        <div class="grid grid-cols-2 gap-2 mt-4 text-center">
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="text-[9px] uppercase font-bold text-slate-400">Último Peso</p>
                <p id="top-peso" class="text-sm font-black text-slate-700">--</p>
            </div>
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="text-[9px] uppercase font-bold text-slate-400">Última Talla</p>
                <p id="top-medida" class="text-sm font-black text-slate-700">--</p>
            </div>
        </div>
    </header>

    <main class="p-4 max-w-md mx-auto space-y-6">
        
        <section id="agenda-home" class="space-y-3">
            <h3 class="text-xs font-bold text-slate-400 uppercase ml-1">Eventos Próximos (14 días)</h3>
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
                <button onclick="registrar('🤱 P. Izquierdo')" class="bg-pink-50 p-4 rounded-2xl text-pink-700 font-bold border border-pink-100">P. IZQ</button>
                <button onclick="registrar('🤱 P. Derecho')" class="bg-pink-50 p-4 rounded-2xl text-pink-700 font-bold border border-pink-100">P. DER</button>
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
                <div id="calendar-header" class="flex justify-between items-center mb-4 px-2">
                    <h4 id="month-name" class="font-black text-slate-800">Febrero 2026</h4>
                    <div class="flex gap-4">
                        <button onclick="changeMonth(-1)"><i class="fa-solid fa-chevron-left text-blue-600"></i></button>
                        <button onclick="changeMonth(1)"><i class="fa-solid fa-chevron-right text-blue-600"></i></button>
                    </div>
                </div>
                <div id="full-calendar-grid" class="grid grid-cols-7 gap-1 text-center text-[10px] font-bold text-slate-400">
                    <div>L</div><div>M</div><div>X</div><div>J</div><div>V</div><div>S</div><div>D</div>
                </div>
                <div id="days-grid" class="grid grid-cols-7 gap-1 mt-2"></div>
            </div>
            <div id="day-detail" class="bg-blue-50 p-4 rounded-3xl border border-blue-100 hidden">
                <div class="flex justify-between items-center mb-2">
                    <h5 id="detail-date" class="font-bold text-blue-800 text-sm"></h5>
                    <button onclick="addCustomEvent()" class="bg-blue-600 text-white text-[10px] px-3 py-1 rounded-full">+ Añadir Evento</button>
                </div>
                <div id="detail-events" class="space-y-2 text-xs"></div>
            </div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <h3 class="font-bold text-slate-800 mb-3 text-sm">Nuevo Peso/Talla</h3>
                <div class="flex gap-2">
                    <input id="input-peso" type="number" step="0.01" placeholder="Kg" class="flex-1 p-3 bg-slate-50 rounded-xl outline-none font-bold">
                    <input id="input-talla" type="number" placeholder="cm" class="flex-1 p-3 bg-slate-50 rounded-xl outline-none font-bold">
                    <button onclick="updateGrowth()" class="bg-blue-600 text-white px-4 rounded-xl"><i class="fa-solid fa-check"></i></button>
                </div>
            </div>
            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <h3 class="font-bold text-slate-800 mb-3 text-sm">Vacunación</h3>
                <div id="lista-vacunas" class="space-y-3"></div>
            </div>
        </section>

        <section id="sec-timer" class="hidden space-y-6 text-center pt-6">
            <div class="w-48 h-48 mx-auto flex items-center justify-center border-[12px] border-blue-100 rounded-full bg-white shadow-xl">
                <span id="timer-val" class="text-3xl font-black text-slate-700">00:00:00</span>
            </div>
            <p class="text-xs text-slate-400 font-bold uppercase tracking-widest">Siguiente toma sugerida</p>
        </section>
    </main>

    <script>
        let bData = {}; 
        let viewDate = new Date();
        let selectedDayStr = "";

        function init() {
            bData = JSON.parse(localStorage.getItem('baby_pro_data'));
            if(!bData) {
                document.getElementById('setup-screen').classList.remove('hidden');
            } else {
                if(!bData.eventos) bData.eventos = {};
                renderHeader();
                renderHomeEvents();
                renderLog();
                renderSalud();
                updateTimer();
                setInterval(updateTimer, 1000);
            }
        }

        // --- CALENDARIO LOCAL ---
        function changeMonth(dir) {
            viewDate.setMonth(viewDate.getMonth() + dir);
            renderFullCalendar();
        }

        function renderFullCalendar() {
            const grid = document.getElementById('days-grid');
            const monthName = document.getElementById('month-name');
            grid.innerHTML = '';
            
            const year = viewDate.getFullYear();
            const month = viewDate.getMonth();
            monthName.innerText = viewDate.toLocaleDateString('es', { month: 'long', year: 'numeric' });

            const firstDay = new Date(year, month, 1).getDay();
            const daysInMonth = new Date(year, month + 1, 0).getDate();
            const offset = firstDay === 0 ? 6 : firstDay - 1;

            for(let i=0; i<offset; i++) grid.innerHTML += `<div></div>`;

            for(let d=1; d<=daysInMonth; d++) {
                const dateStr = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
                const tieneEv = bData.eventos[dateStr] || bData.crecimiento.some(c=>c.fecha===dateStr);
                const hoy = new Date().toISOString().split('T')[0] === dateStr;
                
                grid.innerHTML += `
                    <div onclick="selectCalendarDay('${dateStr}')" class="aspect-square flex flex-col items-center justify-center rounded-xl cursor-pointer relative ${hoy?'bg-blue-600 text-white':'text-slate-700'} ${selectedDayStr===dateStr?'border-2 border-blue-400':''}">
                        <span class="text-xs font-bold">${d}</span>
                        ${tieneEv ? '<div class="w-1 h-1 bg-orange-400 rounded-full absolute bottom-1"></div>' : ''}
                    </div>`;
            }
        }

        function selectCalendarDay(iso) {
            selectedDayStr = iso;
            renderFullCalendar();
            const detail = document.getElementById('day-detail');
            const list = document.getElementById('detail-events');
            detail.classList.remove('hidden');
            document.getElementById('detail-date').innerText = iso;

            let html = "";
            // Logs del día
            const logs = bData.logs[iso] || [];
            if(logs.length) html += `<div class="font-bold text-[10px] text-blue-400 uppercase mt-2">Actividad</div>` + logs.map(l=>`<div>• ${l.txt} (${l.h})</div>`).join('');
            
            // Eventos personalizados
            const evs = bData.eventos[iso] || [];
            if(evs.length) html += `<div class="font-bold text-[10px] text-orange-400 uppercase mt-2">Eventos</div>` + evs.map((e,idx)=>`<div class="flex justify-between"><span>• ${e}</span><button onclick="deleteEvent('${iso}',${idx})" class="text-red-400">x</button></div>`).join('');

            list.innerHTML = html || '<p class="opacity-50 italic">Sin actividad este día</p>';
        }

        function addCustomEvent() {
            const txt = prompt("Nombre del evento:");
            if(txt) {
                if(!bData.eventos[selectedDayStr]) bData.eventos[selectedDayStr] = [];
                bData.eventos[selectedDayStr].push(txt);
                localStorage.setItem('baby_pro_data', JSON.stringify(bData));
                selectCalendarDay(selectedDayStr);
                renderHomeEvents();
            }
        }

        function deleteEvent(iso, idx) {
            bData.eventos[iso].splice(idx, 1);
            localStorage.setItem('baby_pro_data', JSON.stringify(bData));
            selectCalendarDay(iso);
            renderHomeEvents();
        }

        // --- HOME LOGIC (14 DIAS) ---
        function renderHomeEvents() {
            const container = document.getElementById('eventos-proximos');
            const hoy = new Date();
            const limite = new Date(); limite.setDate(hoy.getDate() + 14);
            
            let eventos = [];
            for(let d = new Date(hoy); d <= limite; d.setDate(d.getDate()+1)) {
                const iso = d.toISOString().split('T')[0];
                if(bData.eventos[iso]) {
                    bData.eventos[iso].forEach(e => eventos.push({ date: iso, txt: e, tipo: 'custom' }));
                }
                // También incluir citas médicas en el home
                const citas = bData.citas.filter(c => c.fec === iso);
                citas.forEach(c => eventos.push({ date: iso, txt: `🩺 ${c.mot} (${c.hor})`, tipo: 'cita' }));
            }

            container.innerHTML = eventos.map(e => `
                <div class="bg-white p-3 rounded-2xl flex justify-between items-center shadow-sm border-l-4 ${e.tipo==='cita'?'border-red-400':'border-orange-400'}">
                    <span class="text-xs font-bold text-slate-700">${e.txt}</span>
                    <span class="text-[9px] font-black text-slate-300 uppercase">${e.date === hoy.toISOString().split('T')[0] ? 'Hoy' : e.date.split('-')[2] + '/' + e.date.split('-')[1]}</span>
                </div>
            `).join('') || '<p class="text-center text-[10px] text-slate-300 italic">No hay eventos las próximas 2 semanas</p>';
        }

        // --- SISTEMA ---
        function nextStep(s) {
            document.querySelectorAll('.setup-step').forEach(el => el.classList.add('hidden'));
            document.getElementById('step-'+s).classList.remove('hidden');
        }

        function finalizarSetup() {
            const data = {
                nombre: document.getElementById('setup-nombre').value,
                fecha: document.getElementById('setup-fecha').value,
                logs: {},
                crecimiento: [{fecha: new Date().toISOString().split('T')[0], p: document.getElementById('setup-peso').value, m: document.getElementById('setup-medida').value}],
                citas: [],
                vacunas: {},
                eventos: {},
                lastBib: null
            };
            localStorage.setItem('baby_pro_data', JSON.stringify(data));
            location.reload();
        }

        function renderHeader() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = calcularEdad(bData.fecha);
            const lastG = bData.crecimiento[bData.crecimiento.length - 1];
            document.getElementById('top-peso').innerText = lastG.p + ' kg';
            document.getElementById('top-medida').innerText = lastG.m + ' cm';
            if(bData.lastBib) {
                const btn = document.getElementById('btn-last-bib');
                btn.innerText = `Repetir Biberón (${bData.lastBib}ml)`;
                btn.classList.remove('hidden');
            }
        }

        function calcularEdad(f) {
            const hoy = new Date(); const nac = new Date(f);
            const dias = Math.floor((hoy - nac) / (1000 * 60 * 60 * 24));
            if(dias < 31) return `${dias} días`;
            let meses = (hoy.getFullYear() - nac.getFullYear()) * 12 + (hoy.getMonth() - nac.getMonth());
            if (hoy.getDate() < nac.getDate()) meses--;
            if(meses < 24) return `${meses} meses`;
            return `${Math.floor(meses/12)} años y ${meses%12} meses`;
        }

        function showTab(t) {
            ['diario','calendario','salud','timer'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
            if(t === 'calendario') renderFullCalendar();
            if(t === 'diario') renderHomeEvents();
        }

        function registrar(txt) {
            const iso = new Date().toISOString().split('T')[0];
            const hora = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            if(!bData.logs[iso]) bData.logs[iso] = [];
            bData.logs[iso].unshift({txt, h: hora});
            localStorage.setItem('baby_pro_data', JSON.stringify(bData));
            renderLog();
            localStorage.setItem('next_toma', Date.now() + (3*60*60*1000));
        }

        function promptBiberon() {
            const ml = prompt("¿Cuántos ml?");
            if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderHeader(); }
        }

        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }

        function updateGrowth() {
            const p = document.getElementById('input-peso').value;
            const m = document.getElementById('input-talla').value;
            if(!p || !m) return;
            bData.crecimiento.push({fecha: new Date().toISOString().split('T')[0], p, m});
            localStorage.setItem('baby_pro_data', JSON.stringify(bData));
            renderHeader(); alert("Guardado");
        }

        function renderLog() {
            const logs = bData.logs[new Date().toISOString().split('T')[0]] || [];
            document.getElementById('log-recent').innerHTML = logs.slice(0,5).map(l => `
                <div class="bg-white p-3 rounded-xl flex justify-between text-sm shadow-sm border-l-4 border-blue-400">
                    <span class="font-medium">${l.txt}</span><span class="text-[10px] text-slate-300">${l.h}</span>
                </div>`).join('');
        }

        function updateTimer() {
            const t = localStorage.getItem('next_toma');
            const disp = document.getElementById('timer-val');
            if(!t) return;
            const d = t - Date.now();
            if(d > 0) {
                const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000);
                disp.innerText = `${h}:${m}:${s}`.replace(/\b\d\b/g, '0$&');
            } else { disp.innerText = "¡TOMA!"; disp.classList.add('animate-alert'); }
        }

        function renderSalud() {
            const oficiales = [
                { id: 'v1', m: 2, n: "Hexavalente, Neumococo, Meningo B" },
                { id: 'v2', m: 4, n: "Hexavalente, Neumococo, Meningo B/C" },
                { id: 'v3', m: 11, n: "Hexavalente, Neumococo, Meningo ACWY" }
            ];
            document.getElementById('lista-vacunas').innerHTML = oficiales.map(v => `
                <div class="flex items-center gap-3 p-2 border-b">
                    <input type="checkbox" id="${v.id}" ${bData.vacunas[v.id]?'checked':''} onchange="toggleVacuna('${v.id}','${v.n}')">
                    <label for="${v.id}" class="text-xs"><b>${v.m}m:</b> ${v.n}</label>
                </div>`).join('');
        }

        function toggleVacuna(id, n) { 
            bData.vacunas[id] = document.getElementById(id).checked; 
            if(bData.vacunas[id]) registrar(`💉 Vacuna: ${n}`);
            localStorage.setItem('baby_pro_data', JSON.stringify(bData)); 
        }

        function openSettings() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        init();
    </script>
</body>
</html>
