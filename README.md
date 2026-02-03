<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro V4</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .hidden { display: none !important; }
        .card { background: white; border-radius: 1.5rem; padding: 1.25rem; box-shadow: 0 4px 12px rgba(0,0,0,0.03); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 0.75rem; font-size: 0.85rem; font-weight: 800; border: 1px solid #f1f5f9; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 4px; }
        input[type="time"] { background: transparent; border: none; font-weight: bold; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 font-sans pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[200] flex flex-col p-8 overflow-y-auto">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-black text-slate-800 tracking-tighter text-blue-600">Nombre</h2>
            <input id="setup-n" type="text" placeholder="Ej. Eva" class="w-full p-4 bg-slate-100 rounded-2xl text-2xl font-bold">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-xl">SIGUIENTE</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-black text-slate-800 text-blue-600">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-4 bg-slate-100 rounded-2xl text-xl font-bold">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-xl">SIGUIENTE</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-slate-800 text-blue-600">Medidas nacer</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Peso kg" class="p-4 bg-slate-100 rounded-2xl text-xl font-bold">
                <input id="setup-a" type="number" placeholder="Talla cm" class="p-4 bg-slate-100 rounded-2xl text-xl font-bold">
            </div>
            <button onclick="finishSetup()" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-xl">EMPEZAR</button>
        </div>
    </div>

    <header class="bg-white/80 backdrop-blur-md p-4 sticky top-0 z-50 border-b flex justify-between items-center">
        <div onclick="openModal('modal-menu')" class="flex-1">
            <h1 id="view-nombre" class="text-xl font-black text-blue-600 italic leading-none">---</h1>
            <p id="view-edad" class="text-[10px] font-black text-slate-500 mt-1 uppercase"></p>
            <div class="flex gap-2 text-[9px] font-bold text-slate-400 mt-0.5">
                <span id="view-nacimiento"></span>
                <span id="view-actual" class="text-blue-500"></span>
            </div>
        </div>
        <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-slate-100 rounded-xl text-slate-600 flex items-center justify-center">
            <i class="fa-solid fa-bars-staggered"></i>
        </button>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4">
        
        <div class="card bg-slate-900 text-white min-h-[100px]">
            <h3 class="text-[10px] font-black uppercase text-blue-400 tracking-widest mb-3">Agenda y Avisos</h3>
            <div id="timeline-eventos" class="space-y-4">
                </div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-500 hidden">
            <h3 class="text-[10px] font-black text-orange-500 uppercase mb-2">Lista de compra</h3>
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-3">
            <button onclick="openModal('modal-pecho')" class="bg-pink-500 text-white h-20 rounded-3xl font-black shadow-lg uppercase">Pecho</button>
            <button onclick="openModal('modal-bibe')" class="bg-blue-600 text-white h-20 rounded-3xl font-black shadow-lg uppercase">Bibe</button>
        </div>

        <div class="flex gap-2">
            <button onclick="addLog('💦 Pis')" class="flex-1 bg-white border border-slate-200 py-4 rounded-2xl font-black text-cyan-600 text-[11px] uppercase">Pis</button>
            <button onclick="addLog('💩 Caca')" class="flex-1 bg-white border border-slate-200 py-4 rounded-2xl font-black text-orange-800 text-[11px] uppercase">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="flex-1 bg-white border border-slate-200 py-4 rounded-2xl font-black text-purple-600 text-[11px] uppercase">Aseo</button>
            <button onclick="addCompraUrgent()" class="w-14 bg-orange-600 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="space-y-2">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-2">Historial de hoy</h3>
            <div id="diario-hoy" class="bg-white rounded-[2rem] p-5 text-[11px] font-bold text-slate-600 space-y-3 shadow-sm border"></div>
        </div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-4">
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <h3 id="cal-mes" class="font-black text-blue-600 uppercase"></h3>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>
        <div id="cal-detalle" class="space-y-2"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="card">
            <h3 class="text-xs font-black text-indigo-600 uppercase mb-4">Nuevo Tratamiento</h3>
            <div class="space-y-3">
                <input id="m-n" type="text" placeholder="Medicina" class="w-full p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none">
                <div class="grid grid-cols-3 gap-2">
                    <input id="m-ds" type="text" placeholder="Dosis" class="p-4 bg-slate-50 rounded-2xl font-bold border-none text-center">
                    <input id="m-h" type="number" placeholder="Horas" class="p-4 bg-slate-50 rounded-2xl font-bold border-none text-center">
                    <input id="m-d" type="number" placeholder="Días" class="p-4 bg-slate-50 rounded-2xl font-bold border-none text-center">
                </div>
                <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-black shadow-lg">EMPEZAR</button>
            </div>
        </div>
        <div id="lista-salud" class="space-y-2"></div>
    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-md border-t flex justify-around p-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="text-blue-600"><i class="fa-solid fa-house-user text-xl"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="text-slate-300"><i class="fa-solid fa-calendar-check text-xl"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="text-slate-300"><i class="fa-solid fa-suitcase-medical text-xl"></i></button>
    </nav>

    <div id="modal-cal" class="hidden fixed inset-0 bg-black/80 z-[300] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 id="cal-modal-fecha" class="font-black text-center text-blue-600 uppercase"></h3>
            <input id="cal-ev-txt" type="text" placeholder="¿Qué evento es?" class="w-full p-4 bg-slate-100 rounded-2xl font-bold outline-none">
            <input id="cal-ev-hora" type="time" class="w-full p-4 bg-slate-100 rounded-2xl font-bold text-center">
            <button onclick="saveCita()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">GUARDAR CITA</button>
            <button onclick="closeModal('modal-cal')" class="w-full text-slate-300 font-bold uppercase text-xs">Cerrar</button>
        </div>
    </div>

    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[300] flex items-end">
        <div class="bg-white w-full p-10 rounded-t-[3rem] grid grid-cols-3 gap-4">
            <button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-600">IZQ</button>
            <button onclick="addLog('🤱 Ambos'); closeModal('modal-pecho')" class="bg-pink-200 p-8 rounded-3xl font-black text-pink-700 text-[10px]">AMBOS</button>
            <button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-600">DER</button>
            <button onclick="closeModal('modal-pecho')" class="col-span-3 text-slate-300 font-black p-4 text-center">CERRAR</button>
        </div>
    </div>

    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[300] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[3rem] p-8 space-y-4">
            <button id="btn-rep-bibe" onclick="addBibe(DATA.lastBibe)" class="hidden w-full bg-blue-100 text-blue-600 p-6 rounded-[2rem] font-black uppercase"></button>
            <input id="bibe-ml" type="number" placeholder="Cantidad ml" class="w-full p-5 bg-slate-100 rounded-2xl text-center text-2xl font-black outline-none border-none">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-5 rounded-[2rem] font-black">REGISTRAR</button>
            <button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 font-bold uppercase text-xs">Cerrar</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[300] flex justify-end">
        <div class="bg-white w-5/6 h-full p-8 space-y-8 overflow-y-auto">
            <h2 class="text-3xl font-black text-slate-800 italic">Menu</h2>
            <div class="space-y-4">
                <div class="bg-slate-50 p-6 rounded-3xl">
                    <p class="text-[10px] font-black text-blue-600 uppercase mb-2">Plazo tomas (horas)</p>
                    <input id="set-plazo" type="number" class="w-full p-4 rounded-xl font-bold">
                </div>
                <button onclick="reiniciarApp()" class="w-full p-5 bg-red-50 text-red-600 rounded-2xl font-black">BORRAR TODO</button>
            </div>
            <button onclick="saveSettings(); closeModal('modal-menu')" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black">GUARDAR</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V4')) || {
            idx: 0, bebes: [], lastBibe: 0, plazo: 3
        };
        let selectedDate = null;

        const b = () => DATA.bebes[DATA.idx];

        function save() {
            localStorage.setItem('EVA_CARE_V4', JSON.stringify(DATA));
            render();
        }

        // WIZARD
        function validaPaso(actual, sig) {
            const val = document.getElementById(`setup-${actual === 1 ? 'n' : 'f'}`).value;
            if(!val) return alert("Completa este campo.");
            document.getElementById(`step-${actual}`).classList.add('hidden');
            document.getElementById(`step-${sig}`).classList.remove('hidden');
        }

        function finishSetup() {
            const n = document.getElementById('setup-n').value, f = document.getElementById('setup-f').value,
                  p = document.getElementById('setup-p').value, a = document.getElementById('setup-a').value;
            if(!n || !f || !p || !a) return alert("Rellena todo.");
            DATA.bebes.push({ n, f, p, a, pAct: p, aAct: a, logs: [], meds: [], compra: [], citas: {} });
            DATA.idx = DATA.bebes.length - 1;
            document.getElementById('setup-wizard').classList.add('hidden');
            save();
        }

        // RENDER CENTRAL
        function render() {
            if (DATA.bebes.length === 0) return;
            const be = b();

            // Header
            document.getElementById('view-nombre').innerText = be.n;
            document.getElementById('view-edad').innerText = calcularEdad(be.f);
            document.getElementById('view-nacimiento').innerText = `Nace: ${be.p}kg · ${be.a}cm`;
            document.getElementById('view-actual').innerText = `| Hoy: ${be.pAct}kg`;
            document.getElementById('set-plazo').value = DATA.plazo;

            // Timeline inteligente
            renderTimeline();

            // Compra
            const cBox = document.getElementById('card-compra');
            cBox.classList.toggle('hidden', be.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = be.compra.map((it, i) => `
                <div class="flex justify-between items-center bg-orange-50/50 p-2 rounded-xl">
                    <span class="text-xs font-bold text-orange-900 italic">● ${it}</span>
                    <button onclick="checkCompra(${i})" class="w-8 h-8 bg-white rounded-full text-green-600 shadow-sm">✓</button>
                </div>
            `).join('');

            // Diario
            const hoyStr = new Date().toLocaleDateString();
            document.getElementById('diario-hoy').innerHTML = be.logs.filter(l => l.fecha === hoyStr).map(l => `
                <div class="flex justify-between border-b border-slate-50 pb-2">
                    <span class="text-slate-700">${l.txt}</span>
                    <span class="opacity-40 italic">${l.hora}</span>
                </div>
            `).join('') || "<p class='text-center opacity-30'>Sin actividad hoy</p>";

            // Salud
            document.getElementById('lista-salud').innerHTML = be.meds.map((m, i) => `
                <div class="card flex justify-between items-center border-l-4 border-indigo-500">
                    <div>
                        <b class="text-indigo-600 text-xs uppercase tracking-tight">${m.n}</b><br>
                        <span class="text-[9px] text-slate-400 font-black">${m.ds}ml | cada ${m.h}h</span>
                    </div>
                    <button onclick="darMed(${i})" class="bg-indigo-600 text-white px-5 py-2 rounded-xl font-black text-[10px]">DAR</button>
                </div>
            `).join('');

            // Bibe Repeat
            const bBibe = document.getElementById('btn-rep-bibe');
            if(DATA.lastBibe > 0) {
                bBibe.innerText = `REPETIR ${DATA.lastBibe}ml`;
                bBibe.classList.remove('hidden');
            } else { bBibe.classList.add('hidden'); }

            renderCal();
        }

        function renderTimeline() {
            const be = b();
            const container = document.getElementById('timeline-eventos');
            container.innerHTML = "";
            let html = "";

            // 1. Próxima Alimentación
            const ultToma = be.logs.find(l => l.txt.includes('Bibe') || l.txt.includes('Pecho'));
            if(ultToma) {
                const rest = (DATA.plazo * 3600000) - (Date.now() - ultToma.ts);
                html += `<div class="flex justify-between items-center">
                    <div class="flex items-center gap-3"><i class="fa-solid fa-baby-carriage text-blue-400"></i>
                    <div><p class="text-[11px] font-bold">ALIMENTACIÓN</p><p class="text-[9px] opacity-50 uppercase">Última: ${ultToma.hora}</p></div></div>
                    <span class="text-lg font-black ${rest < 0 ? 'text-red-500 animate-pulse' : ''}">${formatTime(rest)}</span>
                </div>`;
            }

            // 2. Próxima Medicación
            const medActivas = be.meds.filter(m => m.last > 0);
            if(medActivas.length > 0) {
                const prox = medActivas.sort((x,y) => (x.last + x.h*3600000) - (y.last + y.h*3600000))[0];
                const restMed = (prox.last + prox.h*3600000) - Date.now();
                html += `<div class="flex justify-between items-center">
                    <div class="flex items-center gap-3"><i class="fa-solid fa-capsules text-indigo-400"></i>
                    <div><p class="text-[11px] font-bold">${prox.n.toUpperCase()}</p><p class="text-[9px] opacity-50 uppercase">Tratamiento</p></div></div>
                    <span class="text-lg font-black ${restMed < 0 ? 'text-red-500 animate-pulse' : ''}">${formatTime(restMed)}</span>
                </div>`;
            }

            // 3. Citas del calendario (Solo hoy)
            const hoyClave = new Date().toISOString().split('T')[0];
            const citasHoy = be.citas[hoyClave] || [];
            citasHoy.forEach(c => {
                html += `<div class="flex justify-between items-center border-t border-white/10 pt-3">
                    <div class="flex items-center gap-3"><i class="fa-solid fa-calendar-day text-orange-400"></i>
                    <div><p class="text-[11px] font-black">${c.txt.toUpperCase()}</p><p class="text-[9px] opacity-50 uppercase">Evento hoy</p></div></div>
                    <span class="text-xs font-black opacity-80">${c.hora}</span>
                </div>`;
            });

            container.innerHTML = html || "<p class='text-center opacity-30 text-[10px] py-4'>Sin eventos próximos</p>";
        }

        // ACCIONES
        function addLog(txt) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            b().logs.unshift({txt, hora: h, fecha: new Date().toLocaleDateString(), ts: Date.now()});
            save();
        }

        function addBibe(ml) {
            if(!ml) return;
            DATA.lastBibe = ml;
            addLog(`🍼 Bibe ${ml}ml`);
            closeModal('modal-bibe');
            document.getElementById('bibe-ml').value = "";
        }

        function darMed(i) {
            const m = b().meds[i];
            if(Date.now() < m.last + (m.h * 3600000) && !confirm("Aviso: Falta tiempo. ¿Dar?")) return;
            m.last = Date.now();
            addLog(`💊 Med: ${m.n}`);
        }

        function addMed() {
            const n = document.getElementById('m-n').value, ds = document.getElementById('m-ds').value, 
                  h = document.getElementById('m-h').value, d = document.getElementById('m-d').value;
            if(!n || !h) return alert("Datos incompletos.");
            b().meds.push({n, ds, h, d, last: 0});
            save();
        }

        // CALENDARIO FUNCIONAL
        function renderCal() {
            const grid = document.getElementById('cal-grid');
            const hoy = new Date();
            document.getElementById('cal-mes').innerText = hoy.toLocaleDateString('es', {month: 'long', year: 'numeric'});
            grid.innerHTML = "";
            const total = new Date(hoy.getFullYear(), hoy.getMonth()+1, 0).getDate();
            for(let i=1; i<=total; i++) {
                const diaStr = `${hoy.getFullYear()}-${String(hoy.getMonth()+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
                const tieneCita = b().citas[diaStr] && b().citas[diaStr].length > 0;
                grid.innerHTML += `<div onclick="openDay('${diaStr}')" class="cal-day relative ${i==hoy.getDate()?'bg-blue-600 text-white shadow-lg':'bg-white text-slate-700'}">
                    ${i} ${tieneCita ? '<div class="event-dot"></div>' : ''}
                </div>`;
            }
        }

        function openDay(fecha) {
            selectedDate = fecha;
            document.getElementById('cal-modal-fecha').innerText = fecha;
            openModal('modal-cal');
        }

        function saveCita() {
            const txt = document.getElementById('cal-ev-txt').value;
            const hora = document.getElementById('cal-ev-hora').value;
            if(!txt || !hora) return alert("Escribe qué y a qué hora.");
            if(!b().citas[selectedDate]) b().citas[selectedDate] = [];
            b().citas[selectedDate].push({txt, hora});
            save();
            closeModal('modal-cal');
            document.getElementById('cal-ev-txt').value = "";
        }

        // HELPERS
        function formatTime(ms) {
            const s = Math.abs(ms);
            const h = Math.floor(s/3600000);
            const m = Math.floor((s%3600000)/60000);
            return (ms < 0 ? '+' : '') + `${h}h ${m}m`;
        }
        function calcularEdad(f) {
            const d = Math.floor((new Date() - new Date(f)) / 86400000);
            return d < 31 ? `${d} DÍAS` : `${Math.floor(d/30.4)} MESES`;
        }
        function saveSettings() { DATA.plazo = document.getElementById('set-plazo').value; save(); }
        function addCompraUrgent() { const it = prompt("¿Qué falta?"); if(it) { b().compra.push(it); save(); } }
        function checkCompra(i) { b().compra.splice(i, 1); save(); }
        function showTab(id) {
            ['inicio', 'cal', 'salud'].forEach(t => {
                document.getElementById('tab-' + t).classList.toggle('hidden', t !== id);
                document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id);
                document.getElementById('nav-' + t).classList.toggle('text-slate-300', t !== id);
            });
        }
        function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }
        function reiniciarApp() { if(confirm("¿Seguro?")) { localStorage.clear(); location.reload(); } }

        if(DATA.bebes.length === 0) {
            document.getElementById('setup-wizard').classList.remove('hidden');
        } else {
            document.getElementById('setup-wizard').classList.add('hidden');
            render();
        }
        setInterval(renderTimeline, 60000);
    </script>
</body>
</html>
