<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro v2026</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        .bg-baby { background-color: #f8fafc; }
        @keyframes pulse-red { 0%, 100% { background-color: #fee2e2; } 50% { background-color: #fecaca; } }
    </style>
</head>
<body class="bg-baby pb-24 font-sans overflow-x-hidden">

    <audio id="alert-sound" src="https://assets.mixkit.co/active_storage/sfx/2869/2869-preview.mp3" preload="auto"></audio>

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-8 flex flex-col justify-center text-center">
        <div id="step-1" class="setup-step space-y-4">
            <h2 class="text-3xl font-black text-slate-800">¿Nombre del bebé?</h2>
            <input id="setup-nombre" type="text" class="w-full p-4 bg-blue-50 rounded-2xl outline-none text-xl font-bold text-blue-600 text-center">
            <button onclick="nextStep(2)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
            <p class="text-[10px] text-slate-400 mt-4">Podrás añadir más bebés desde Ajustes.</p>
        </div>
        <div id="step-2" class="setup-step hidden space-y-4">
            <h2 class="text-3xl font-black text-slate-800">¿Nacimiento?</h2>
            <input id="setup-fecha" type="date" class="w-full p-4 bg-blue-50 rounded-2xl outline-none text-xl font-bold text-blue-600 text-center">
            <button onclick="nextStep(3)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
        </div>
        <div id="step-3" class="setup-step hidden space-y-4">
            <h2 class="text-3xl font-black text-slate-800">Peso y Talla al nacer</h2>
            <div class="flex gap-2">
                <input id="setup-peso" type="number" step="0.01" placeholder="Kg" class="w-1/2 p-4 bg-blue-50 rounded-2xl font-bold text-center">
                <input id="setup-medida" type="number" placeholder="cm" class="w-1/2 p-4 bg-blue-50 rounded-2xl font-bold text-center">
            </div>
            <button onclick="finalizarSetup()" class="w-full bg-green-600 text-white p-5 rounded-2xl font-bold shadow-xl">¡Empezar!</button>
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
        <div class="grid grid-cols-2 gap-2 mt-4 text-center">
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="text-[8px] font-bold text-slate-400 uppercase">Nacimiento</p>
                <p class="text-[11px] font-black text-slate-700"><span id="top-peso-nac">--</span> | <span id="top-talla-nac">--</span></p>
            </div>
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="text-[8px] font-bold text-slate-400 uppercase">Último Control (<span id="top-fecha-act">--</span>)</p>
                <p class="text-[11px] font-black text-slate-700"><span id="top-peso-act">--</span> | <span id="top-talla-act">--</span></p>
            </div>
        </div>
    </header>

    <div id="side-menu" class="hidden fixed inset-0 bg-black/50 z-50 flex justify-end">
        <div class="bg-white w-72 h-full p-6 shadow-xl space-y-6 overflow-y-auto">
            <div class="flex justify-between items-center">
                <h3 class="font-black text-slate-800">Configuración</h3>
                <button onclick="toggleMenu()"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <div class="space-y-4">
                <div class="p-4 bg-blue-50 rounded-2xl">
                    <p class="text-[10px] font-bold text-blue-700 uppercase mb-2">Intervalo entre tomas</p>
                    <div class="flex items-center gap-2">
                        <input id="cfg-toma-h" type="number" step="0.5" class="w-full p-2 rounded-lg font-bold text-blue-800 text-center" value="3">
                        <span class="text-xs font-bold text-blue-700">Horas</span>
                    </div>
                    <button onclick="saveInterval()" class="w-full mt-2 bg-blue-600 text-white text-[10px] py-2 rounded-lg font-bold">Actualizar</button>
                </div>
                <button onclick="addCompraPrompt()" class="w-full flex items-center gap-3 p-4 bg-orange-50 rounded-xl font-bold text-orange-700">
                    <i class="fa-solid fa-cart-plus"></i> Añadir a la Compra
                </button>
                <button onclick="alert('Próximamente: Soporte Multi-bebé')" class="w-full flex items-center gap-3 p-4 bg-slate-50 rounded-xl font-bold text-slate-500">
                    <i class="fa-solid fa-baby"></i> Añadir otro bebé
                </button>
                <hr>
                <button onclick="borrarTodo()" class="w-full flex items-center gap-3 p-4 bg-red-50 rounded-xl font-bold text-red-600 text-xs">
                    <i class="fa-solid fa-trash"></i> Eliminar todos los datos
                </button>
            </div>
        </div>
    </div>

    <main class="p-4 max-w-md mx-auto space-y-6">
        <div id="compra-alert" class="hidden bg-orange-500 text-white p-4 rounded-2xl flex justify-between items-center shadow-lg animate-pulse" onclick="showTab('salud')">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-cart-shopping"></i>
                <span class="text-xs font-bold uppercase tracking-wide">Faltan productos en casa</span>
            </div>
            <i class="fa-solid fa-chevron-right text-xs"></i>
        </div>

        <section id="home-timers" class="space-y-3">
            <h3 class="text-[10px] font-bold text-slate-400 uppercase tracking-widest ml-1">Próximos plazos</h3>
            <div id="timers-container" class="space-y-2"></div>
        </section>

        <nav class="flex justify-around bg-white rounded-2xl shadow-sm border text-slate-400 overflow-hidden">
            <button onclick="showTab('diario')" id="tab-diario" class="p-4 active-tab flex-1"><i class="fa-solid fa-baby"></i></button>
            <button onclick="showTab('calendario')" id="tab-calendario" class="p-4 flex-1"><i class="fa-solid fa-calendar-days"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-4 flex-1"><i class="fa-solid fa-stethoscope"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="openPechoModal()" class="col-span-2 bg-pink-50 p-6 rounded-2xl text-pink-700 font-black shadow-sm flex items-center justify-center gap-3 active:scale-95 transition-transform">
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
                <div id="days-container" class="grid grid-cols-7 gap-1"></div>
            </div>

            <div class="bg-indigo-50 p-4 rounded-3xl border border-indigo-100">
                <h5 class="text-xs font-black text-indigo-800 uppercase mb-2">Asistente de Vacunación 2026</h5>
                <p class="text-[9px] text-indigo-400 mb-3 italic">Basado en el calendario común de España. Consulta siempre a tu pediatra.</p>
                <div id="vaccine-suggestions" class="flex flex-wrap gap-2"></div>
            </div>

            <div id="day-detail" class="bg-blue-50 p-4 rounded-3xl border border-blue-100 hidden">
                <div id="detail-date" class="font-bold text-blue-800 text-sm mb-3"></div>
                <div id="detail-events" class="space-y-2"></div>
                <button onclick="openEventModal()" class="w-full mt-3 bg-blue-600 text-white p-3 rounded-xl font-bold text-xs">+ Añadir Evento Manual</button>
            </div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-orange-50 p-5 rounded-3xl border border-orange-100">
                <h3 class="font-bold text-orange-800 text-sm mb-4">Lista de la Compra</h3>
                <div id="lista-compra" class="space-y-2"></div>
                <button onclick="addCompraPrompt()" class="w-full mt-3 bg-orange-600 text-white p-3 rounded-xl font-bold text-xs">+ Añadir Producto</button>
            </div>

            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-bold text-slate-800 text-sm">Medicación</h3>
                    <button onclick="nuevoMedicamento()" class="text-blue-600 text-xs font-bold">+ Añadir</button>
                </div>
                <div id="lista-medicinas" class="space-y-3"></div>
            </div>

            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <h3 class="font-bold text-slate-800 mb-3 text-sm">Registro de Crecimiento</h3>
                <div class="grid grid-cols-2 gap-2">
                    <input id="input-peso" type="number" step="0.01" placeholder="Kg" class="p-3 bg-slate-50 rounded-xl text-center">
                    <input id="input-talla" type="number" placeholder="cm" class="p-3 bg-slate-50 rounded-xl text-center">
                    <button onclick="updateGrowth()" class="col-span-2 bg-blue-600 text-white p-3 rounded-xl font-bold">Guardar</button>
                </div>
            </div>
        </section>
    </main>

    <div id="pecho-modal" class="hidden fixed inset-0 bg-black/50 z-50 flex items-end">
        <div class="bg-white w-full p-6 rounded-t-3xl space-y-4">
            <h3 class="font-black text-center text-slate-800">¿Qué lado toca?</h3>
            <div class="grid grid-cols-3 gap-2">
                <button onclick="registrarPecho('Izquierdo')" class="bg-pink-50 p-4 rounded-xl font-bold text-pink-700 border border-pink-100">Izquierdo</button>
                <button onclick="registrarPecho('Ambos')" class="bg-purple-50 p-4 rounded-xl font-bold text-purple-700 border border-purple-100">Ambos</button>
                <button onclick="registrarPecho('Derecho')" class="bg-pink-50 p-4 rounded-xl font-bold text-pink-700 border border-pink-100">Derecho</button>
            </div>
            <button onclick="closePechoModal()" class="w-full p-3 text-slate-400 text-sm font-bold">Cerrar</button>
        </div>
    </div>

    <div id="event-modal" class="hidden fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-3xl p-6 space-y-4">
            <h3 class="font-black">Nuevo Evento</h3>
            <input id="ev-titulo" type="text" placeholder="Ej: Pediatra, Vacunas..." class="w-full p-4 bg-slate-50 rounded-xl border-none outline-none">
            <input id="ev-hora" type="time" class="w-full p-4 bg-slate-50 rounded-xl border-none outline-none">
            <button onclick="saveCustomEvent()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold">Guardar</button>
            <button onclick="closeEventModal()" class="w-full text-slate-400" onclick="closeEventModal()">Cancelar</button>
        </div>
    </div>

    <script>
        let bData = {}; 
        let viewDate = new Date();
        let selectedDayStr = "";
        let notifiedMeds = new Set();

        const VACUNAS_2026 = [
            { m: 0, n: "Hepatitis B (Nacimiento)" },
            { m: 2, n: "Hexavalente (1ª), Neumococo (1ª), MenB (1ª), Rotavirus (1ª)" },
            { m: 4, n: "Hexavalente (2ª), Neumococo (2ª), MenB (2ª), MenC/ACWY, Rotavirus (2ª)" },
            { m: 6, n: "Hexavalente (3ª), Rotavirus (3ª), Gripe (estacional)" },
            { m: 11, n: "Hexavalente (Refuerzo), Neumococo (Refuerzo)" },
            { m: 12, n: "Triple Vírica (1ª), MenB (Refuerzo), MenACWY" },
            { m: 15, n: "Varicela (1ª), Hepatitis A (1ª)" }
        ];

        function init() {
            bData = JSON.parse(localStorage.getItem('baby_pro_data_v2'));
            if(!bData) {
                document.getElementById('setup-screen').classList.remove('hidden');
            } else {
                if(!bData.eventos) bData.eventos = {};
                if(!bData.compra) bData.compra = [];
                if(!bData.intervaloToma) bData.intervaloToma = 3;
                document.getElementById('cfg-toma-h').value = bData.intervaloToma;
                renderHeader();
                renderLog();
                renderMedicina();
                renderCompra();
                renderVaccines();
                setInterval(updateAllTimers, 1000);
            }
        }

        // --- VACUNAS ---
        function renderVaccines() {
            const nac = new Date(bData.fecha);
            const hoy = new Date();
            const mesesVida = (hoy.getFullYear() - nac.getFullYear()) * 12 + (hoy.getMonth() - nac.getMonth());
            
            const container = document.getElementById('vaccine-suggestions');
            container.innerHTML = VACUNAS_2026.filter(v => v.m >= mesesVida - 1 && v.m <= mesesVida + 2).map(v => `
                <button onclick="addVaccineToCalendar('${v.n}', ${v.m})" class="bg-white text-indigo-600 text-[9px] px-3 py-2 rounded-full font-black border border-indigo-200 shadow-sm">
                    En mes ${v.m}: ${v.n.split(',')[0]}...
                </button>
            `).join('') || '<p class="text-[9px] text-indigo-400">No hay vacunas sugeridas próximamente.</p>';
        }

        function addVaccineToCalendar(nombre, mes) {
            const nac = new Date(bData.fecha);
            const fechaPrevista = new Date(nac.setMonth(nac.getMonth() + mes));
            const iso = fechaPrevista.toISOString().split('T')[0];
            if(!bData.eventos[iso]) bData.eventos[iso] = [];
            bData.eventos[iso].push({ titulo: "💉 Vacuna: " + nombre, hora: "10:00" });
            save(); 
            alert("Sugerencia añadida al " + iso.split('-').reverse().join('/'));
            showTab('calendario');
            selectCalendarDay(iso);
        }

        // --- CORE FUNCTIONS ---
        function getEdadTexto(f) {
            const dias = Math.floor((new Date() - new Date(f)) / 86400000);
            return dias < 30 ? `${dias} días` : `${Math.floor(dias/30.44)} meses`;
        }

        function renderHeader() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = getEdadTexto(bData.fecha);
            const nac = bData.crecimiento[0], act = bData.crecimiento[bData.crecimiento.length - 1];
            document.getElementById('top-peso-nac').innerText = nac.p + 'kg';
            document.getElementById('top-talla-nac').innerText = nac.m + 'cm';
            document.getElementById('top-peso-act').innerText = act.p + 'kg';
            document.getElementById('top-talla-act').innerText = act.m + 'cm';
            document.getElementById('top-fecha-act').innerText = act.fecha.split('-').reverse().slice(0,2).join('/');
            if(bData.lastBib) {
                document.getElementById('btn-last-bib').innerText = `🍼 Repetir ${bData.lastBib}ml`;
                document.getElementById('btn-last-bib').classList.remove('hidden');
            }
        }

        function updateAllTimers() {
            const container = document.getElementById('timers-container');
            let html = "";
            const ahora = Date.now();
            
            const lt = localStorage.getItem('last_toma_time');
            if(lt) html += renderTimerRow("🍼 Próxima Toma", parseInt(lt) + (bData.intervaloToma * 3600000) - ahora);
            
            bData.medicinas.forEach(m => {
                if(m.ultimaToma) {
                    const px = m.ultimaToma + (m.cada * 3600000);
                    html += renderTimerRow(`💊 ${m.nombre}`, px - ahora);
                    if(ahora >= px && !notifiedMeds.has(`${m.nombre}-${px}`)) {
                        document.getElementById('alert-sound').play();
                        notifiedMeds.add(`${m.nombre}-${px}`);
                    }
                }
            });

            const hoyIso = new Date().toISOString().split('T')[0];
            if(bData.eventos[hoyIso]) {
                bData.eventos[hoyIso].forEach(e => {
                    const [h, m] = e.hora.split(':');
                    const eMs = new Date().setHours(h, m, 0, 0);
                    if(eMs > ahora) html += renderTimerRow(`📅 ${e.titulo}`, eMs - ahora);
                });
            }
            container.innerHTML = html || '<p class="text-[10px] text-center opacity-30 italic">Todo al día</p>';
        }

        function renderTimerRow(label, diff) {
            const s = Math.floor(diff/1000);
            if(s <= 0) return `<div class="bg-red-500 text-white p-3 rounded-xl flex justify-between font-black animate-pulse text-[10px]"><span>${label}</span><span>¡TOCA YA!</span></div>`;
            const time = `${Math.floor(s/3600)}:${String(Math.floor((s%3600)/60)).padStart(2,'0')}:${String(s%60).padStart(2,'0')}`;
            return `<div class="bg-white p-3 rounded-xl border border-slate-100 flex justify-between text-[10px] font-bold text-slate-500 shadow-sm"><span>${label}</span><span class="text-blue-600 font-mono">${time}</span></div>`;
        }

        // --- OTROS ---
        function registrar(txt) {
            const iso = new Date().toISOString().split('T')[0], h = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            if(!bData.logs[iso]) bData.logs[iso] = [];
            bData.logs[iso].unshift({txt, h});
            if(txt.includes('Biberón') || txt.includes('Pecho') || txt.includes('🤱')) localStorage.setItem('last_toma_time', Date.now());
            save(); renderLog();
        }

        function showTab(t) {
            ['diario','calendario','salud'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
            if(t==='calendario') renderFullCalendar();
        }

        function renderFullCalendar() {
            const grid = document.getElementById('days-container'); grid.innerHTML = '';
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
                    ${hasEv ? '<div class="w-1.5 h-1.5 bg-orange-400 rounded-full absolute bottom-1"></div>' : ''}
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
                    <span class="font-bold text-slate-700">${e.hora} - ${e.titulo}</span>
                    <button onclick="borrarEv('${iso}',${i})" class="text-red-300"><i class="fa-solid fa-trash"></i></button>
                </div>`).join('') || '<p class="opacity-40 italic text-xs text-center">Día libre</p>';
        }

        function saveCustomEvent() {
            const t = document.getElementById('ev-titulo').value, h = document.getElementById('ev-hora').value;
            if(!t || !h) return;
            if(!bData.eventos[selectedDayStr]) bData.eventos[selectedDayStr] = [];
            bData.eventos[selectedDayStr].push({ titulo: t, hora: h });
            save(); closeEventModal(); selectCalendarDay(selectedDayStr);
        }

        function addCompraPrompt() { const it = prompt("¿Qué falta?"); if(it) { bData.compra.push(it); save(); renderCompra(); } }
        function renderCompra() {
            document.getElementById('compra-alert').classList.toggle('hidden', bData.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = bData.compra.map((it, idx) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center text-xs shadow-sm border border-orange-100">
                    <span class="font-bold text-slate-700">${it}</span>
                    <button onclick="borrarCompra(${idx})" class="text-green-500 font-bold"><i class="fa-solid fa-check"></i></button>
                </div>`).join('') || '<p class="text-[10px] text-center opacity-30 italic">Lista vacía</p>';
        }
        function borrarCompra(i) { bData.compra.splice(i,1); save(); renderCompra(); }

        function saveInterval() { bData.intervaloToma = parseFloat(document.getElementById('cfg-toma-h').value); save(); alert("Guardado"); toggleMenu(); }
        function updateGrowth() {
            const p = document.getElementById('input-peso').value, t = document.getElementById('input-talla').value;
            if(p && t) { bData.crecimiento.push({fecha: new Date().toISOString().split('T')[0], p, m: t}); save(); renderHeader(); alert("Control registrado"); }
        }
        function registrarPecho(lado) { registrar(`🤱 Pecho: ${lado}`); closePechoModal(); }
        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }
        function promptBiberon() { const ml = prompt("¿Cuántos ml?"); if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderHeader(); } }
        function tomarMedicina(idx) {
            const m = bData.medicinas[idx], ahora = Date.now();
            if(m.ultimaToma && (ahora - m.ultimaToma) < m.cada * 3600000) { if(!confirm("Aún no toca. ¿Registrar de todos modos?")) return; }
            bData.medicinas[idx].ultimaToma = ahora; registrar(`💊 ${m.nombre}`); save(); renderMedicina();
        }
        function nuevoMedicamento() { const n = prompt("Nombre:"); const c = prompt("Cada cuantas horas:", "8"); if(n&&c) { bData.medicinas.push({nombre:n, cada:parseInt(c), ultimaToma:null}); save(); renderMedicina(); } }
        function renderMedicina() { document.getElementById('lista-medicinas').innerHTML = bData.medicinas.map((m, idx) => `<div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center"><div class="font-bold text-slate-800">${m.nombre} <span class="text-[10px] text-blue-500 ml-2">${m.cada}h</span></div><button onclick="tomarMedicina(${idx})" class="bg-blue-600 text-white p-3 rounded-xl"><i class="fa-solid fa-check"></i></button></div>`).join('') || '<p class="text-center opacity-30 text-xs">Sin medicinas</p>'; }
        function renderLog() {
            const logs = bData.logs[new Date().toISOString().split('T')[0]] || [];
            document.getElementById('log-recent').innerHTML = logs.slice(0,5).map(l => `<div class="bg-white p-3 rounded-xl flex justify-between text-xs shadow-sm border-l-4 border-blue-400"><span>${l.txt}</span><span class="text-slate-300">${l.h}</span></div>`).join('');
        }
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('hidden'); }
        function openEventModal() { document.getElementById('event-modal').classList.remove('hidden'); }
        function closeEventModal() { document.getElementById('event-modal').classList.add('hidden'); }
        function openPechoModal() { document.getElementById('pecho-modal').classList.remove('hidden'); }
        function closePechoModal() { document.getElementById('pecho-modal').classList.add('hidden'); }
        function changeMonth(d) { viewDate.setMonth(viewDate.getMonth()+d); renderFullCalendar(); }
        function borrarEv(iso, i) { bData.eventos[iso].splice(i,1); save(); selectCalendarDay(iso); }
        function save() { localStorage.setItem('baby_pro_data_v2', JSON.stringify(bData)); }
        function nextStep(s) { document.querySelectorAll('.setup-step').forEach(e => e.classList.add('hidden')); document.getElementById('step-'+s).classList.remove('hidden'); }
        function finalizarSetup() {
            const p = document.getElementById('setup-peso').value, m = document.getElementById('setup-medida').value;
            bData = { nombre: document.getElementById('setup-nombre').value, fecha: document.getElementById('setup-fecha').value, logs: {}, crecimiento: [{fecha: new Date().toISOString().split('T')[0], p, m}], medicinas: [], eventos: {}, compra: [], intervaloToma: 3 };
            save(); location.reload();
        }
        function borrarTodo() { if(confirm("¿SEGURO? Se borrará todo.")) { localStorage.clear(); location.reload(); } }

        init();
    </script>
</body>
</html>
