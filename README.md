<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro V3</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .hidden { display: none !important; }
        .tab-active { color: #2563eb; border-bottom: 3px solid #2563eb; font-weight: 900; }
        .card { background: white; border-radius: 1.5rem; padding: 1.25rem; box-shadow: 0 4px 10px rgba(0,0,0,0.03); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 0.75rem; font-size: 0.8rem; font-weight: 800; }
        input[type="number"], input[type="text"], input[type="date"] { -webkit-appearance: none; border-radius: 0.75rem; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 font-sans pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[200] flex flex-col p-8 overflow-y-auto">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <p class="text-blue-600 font-black uppercase text-xs tracking-widest">Paso 1 de 3</p>
            <h2 class="text-4xl font-black text-slate-800">Nombre de tu bebé</h2>
            <input id="setup-n" type="text" placeholder="Ej. Eva" class="w-full p-4 bg-slate-100 border-none text-2xl font-bold focus:ring-2 ring-blue-500">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-xl">SIGUIENTE</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <p class="text-blue-600 font-black uppercase text-xs tracking-widest">Paso 2 de 3</p>
            <h2 class="text-4xl font-black text-slate-800">Fecha de nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-4 bg-slate-100 border-none text-xl font-bold">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-xl">SIGUIENTE</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <p class="text-blue-600 font-black uppercase text-xs tracking-widest">Paso 3 de 3</p>
            <h2 class="text-3xl font-black text-slate-800">Medidas al nacer</h2>
            <div class="grid grid-cols-2 gap-4">
                <div>
                    <label class="text-[10px] font-bold text-slate-400 uppercase ml-2">Peso (kg)</label>
                    <input id="setup-p" type="number" step="0.1" placeholder="3.5" class="w-full p-4 bg-slate-100 border-none text-xl font-bold">
                </div>
                <div>
                    <label class="text-[10px] font-bold text-slate-400 uppercase ml-2">Talla (cm)</label>
                    <input id="setup-a" type="number" placeholder="50" class="w-full p-4 bg-slate-100 border-none text-xl font-bold">
                </div>
            </div>
            <button onclick="finishSetup()" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-xl">EMPEZAR</button>
        </div>
    </div>

    <header class="bg-white/80 backdrop-blur-md p-4 sticky top-0 z-50 border-b flex justify-between items-center">
        <div onclick="openModal('modal-menu')" class="flex-1">
            <h1 id="view-nombre" class="text-xl font-black text-blue-600 italic leading-none">---</h1>
            <p id="view-edad" class="text-[10px] font-bold text-slate-500 mt-1 uppercase tracking-tighter"></p>
            <div class="flex gap-2 text-[9px] font-bold text-slate-400 mt-0.5">
                <span id="view-nacimiento"></span>
                <span class="text-blue-300">|</span>
                <span id="view-actual" class="text-blue-500 italic"></span>
            </div>
        </div>
        <button onclick="openModal('modal-menu')" class="ml-4 w-12 h-12 bg-slate-100 rounded-2xl text-slate-600 flex items-center justify-center">
            <i class="fa-solid fa-bars-staggered"></i>
        </button>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4">
        <div class="bg-slate-900 rounded-[2rem] p-6 text-white shadow-2xl">
            <div class="space-y-4">
                <div class="flex justify-between items-center border-b border-white/10 pb-3">
                    <div>
                        <p class="text-[9px] font-black text-blue-400 uppercase tracking-widest">Alimentación</p>
                        <p id="label-ult-toma" class="text-[10px] font-medium opacity-60">Esperando registro...</p>
                    </div>
                    <span id="reloj-toma" class="text-2xl font-black text-white tracking-tighter">--:--</span>
                </div>
                <div class="flex justify-between items-center">
                    <div>
                        <p class="text-[9px] font-black text-indigo-400 uppercase tracking-widest">Medicación</p>
                        <p id="label-ult-med" class="text-[10px] font-medium opacity-60">Sin tratamientos</p>
                    </div>
                    <span id="reloj-med" class="text-2xl font-black text-white tracking-tighter">--:--</span>
                </div>
            </div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-500 hidden">
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-3">
            <button onclick="openModal('modal-pecho')" class="bg-pink-500 text-white h-24 rounded-[2rem] font-black text-lg shadow-lg active:scale-95 transition-transform uppercase">Pecho</button>
            <button onclick="openModal('modal-bibe')" class="bg-blue-600 text-white h-24 rounded-[2rem] font-black text-lg shadow-lg active:scale-95 transition-transform uppercase">Bibe</button>
        </div>

        <div class="flex gap-2">
            <button onclick="addLog('💦 Pis')" class="flex-1 bg-white border border-slate-200 py-4 rounded-2xl font-black text-cyan-600 text-xs shadow-sm uppercase">Pis</button>
            <button onclick="addLog('💩 Caca')" class="flex-1 bg-white border border-slate-200 py-4 rounded-2xl font-black text-orange-800 text-xs shadow-sm uppercase">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="flex-1 bg-white border border-slate-200 py-4 rounded-2xl font-black text-purple-600 text-xs shadow-sm uppercase">Aseo</button>
            <button onclick="addCompraUrgent()" class="w-14 bg-orange-600 text-white rounded-2xl shadow-lg flex items-center justify-center"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="space-y-2">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-2">Historial de hoy</h3>
            <div id="diario-hoy" class="bg-white rounded-[2rem] p-5 text-[11px] font-bold text-slate-600 space-y-3 shadow-sm"></div>
        </div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-4">
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <h3 id="cal-mes" class="font-black text-blue-600 uppercase"></h3>
                <button onclick="addCita()" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-[10px] font-black shadow-md">AÑADIR CITA</button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-2">
                </div>
        </div>
        <div id="cal-eventos" class="space-y-2"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="card">
            <h3 class="text-sm font-black text-indigo-600 uppercase mb-4 tracking-tighter">Nuevo Tratamiento</h3>
            <div class="space-y-3">
                <input id="m-n" type="text" placeholder="Medicina" class="w-full p-4 bg-slate-50 rounded-2xl text-xs font-bold border-none">
                <div class="grid grid-cols-3 gap-2">
                    <input id="m-ds" type="text" placeholder="Dosis" class="p-4 bg-slate-50 rounded-2xl text-xs font-bold border-none">
                    <input id="m-h" type="number" placeholder="Horas" class="p-4 bg-slate-50 rounded-2xl text-xs font-bold border-none">
                    <input id="m-d" type="number" placeholder="Días" class="p-4 bg-slate-50 rounded-2xl text-xs font-bold border-none">
                </div>
                <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-black text-xs shadow-lg">GUARDAR TRATAMIENTO</button>
            </div>
        </div>
        <div id="lista-salud" class="space-y-3 pb-10"></div>
    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white/90 backdrop-blur-lg border-t flex justify-around p-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="flex flex-col items-center text-blue-600"><i class="fa-solid fa-house-chimney text-xl"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="flex flex-col items-center text-slate-300"><i class="fa-solid fa-calendar-day text-xl"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="flex flex-col items-center text-slate-300"><i class="fa-solid fa-heart-pulse text-xl"></i></button>
    </nav>

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
            <div id="rep-bibe-box" class="hidden">
                <button id="btn-rep-bibe" onclick="addBibe(DATA.lastBibe)" class="w-full bg-blue-100 text-blue-600 p-6 rounded-[2rem] font-black uppercase shadow-sm"></button>
                <p class="text-center text-[9px] font-bold text-slate-300 mt-2">O INTRODUCE OTRA CANTIDAD:</p>
            </div>
            <input id="bibe-ml" type="number" placeholder="Cantidad ml" class="w-full p-5 bg-slate-100 rounded-2xl text-center text-2xl font-black border-none">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-5 rounded-[2rem] font-black shadow-xl">REGISTRAR</button>
            <button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 font-bold uppercase">Volver</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[300] flex justify-end">
        <div class="bg-white w-5/6 h-full p-8 space-y-8 overflow-y-auto">
            <h2 class="text-3xl font-black text-slate-800 italic">Ajustes</h2>
            <div class="space-y-6">
                <div class="bg-slate-50 p-6 rounded-3xl space-y-4">
                    <p class="text-[10px] font-black text-blue-600 uppercase tracking-widest">Plazo entre tomas</p>
                    <div class="flex items-center gap-4">
                        <input id="set-plazo" type="number" class="w-20 p-3 bg-white border rounded-xl font-bold text-center">
                        <span class="text-sm font-bold text-slate-500">Horas</span>
                    </div>
                </div>
                <div class="space-y-3">
                    <button onclick="alert('Función próximamente: Edición directa')" class="w-full text-left p-5 bg-slate-100 rounded-2xl font-black text-sm text-slate-700">Editar Datos Bebé</button>
                    <button onclick="addNewBebe()" class="w-full text-left p-5 bg-slate-100 rounded-2xl font-black text-sm text-slate-700">Añadir otro Bebé</button>
                    <button onclick="reiniciarApp()" class="w-full text-left p-5 bg-red-50 text-red-600 rounded-2xl font-black text-sm mt-20">Borrar todo</button>
                </div>
            </div>
            <button onclick="saveSettings(); closeModal('modal-menu')" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black">GUARDAR Y SALIR</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_VFINAL_3')) || {
            idx: 0,
            bebes: [],
            lastBibe: 0,
            plazo: 3
        };

        const b = () => DATA.bebes[DATA.idx];

        function save() {
            localStorage.setItem('EVA_CARE_VFINAL_3', JSON.stringify(DATA));
            render();
        }

        // VALIDACIONES
        function validaPaso(actual, sig) {
            const val = document.getElementById(`setup-${actual === 1 ? 'n' : 'f'}`).value;
            if(!val) { alert("¡Hey! No puedes dejar este campo vacío."); return; }
            document.getElementById(`step-${actual}`).classList.add('hidden');
            document.getElementById(`step-${sig}`).classList.remove('hidden');
        }

        function finishSetup() {
            const n = document.getElementById('setup-n').value, f = document.getElementById('setup-f').value,
                  p = document.getElementById('setup-p').value, a = document.getElementById('setup-a').value;
            if(!n || !f || !p || !a) { alert("Faltan medidas por rellenar."); return; }
            DATA.bebes.push({ n, f, p, a, pAct: p, aAct: a, logs: [], meds: [], compra: [], citas: [] });
            DATA.idx = DATA.bebes.length - 1;
            document.getElementById('setup-wizard').classList.add('hidden');
            save();
        }

        // RENDER CENTRAL
        function render() {
            if (DATA.bebes.length === 0) return;
            const be = b();

            // Header Info
            document.getElementById('view-nombre').innerText = be.n;
            document.getElementById('view-edad').innerText = calcularEdad(be.f);
            document.getElementById('view-nacimiento').innerText = `Nace: ${be.p}kg · ${be.a}cm`;
            document.getElementById('view-actual').innerText = `Hoy: ${be.pAct}kg · ${be.aAct}cm`;
            document.getElementById('set-plazo').value = DATA.plazo;

            // Compra
            const cBox = document.getElementById('card-compra');
            cBox.classList.toggle('hidden', be.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = be.compra.map((it, i) => `
                <div class="flex justify-between items-center bg-orange-50/50 p-3 rounded-2xl">
                    <span class="text-xs font-bold text-orange-900">● ${it}</span>
                    <button onclick="checkCompra(${i})" class="w-8 h-8 bg-white rounded-full shadow-sm text-green-600">✓</button>
                </div>
            `).join('');

            // Historial
            const hoy = new Date().toLocaleDateString();
            const logsHoy = be.logs.filter(l => l.fecha === hoy);
            document.getElementById('diario-hoy').innerHTML = logsHoy.map(l => `
                <div class="flex justify-between border-b border-slate-50 pb-2">
                    <span class="text-slate-700">${l.txt}</span>
                    <span class="opacity-40 italic">${l.hora}</span>
                </div>
            `).join('') || "<p class='text-center opacity-30'>Sin actividad hoy</p>";

            // Salud
            document.getElementById('lista-salud').innerHTML = be.meds.map((m, i) => `
                <div class="card flex justify-between items-center border-l-4 border-indigo-500">
                    <div>
                        <b class="text-indigo-600 text-xs">${m.n}</b><br>
                        <span class="text-[9px] text-slate-400 font-bold">${m.ds} | cada ${m.h}h | resto ${m.d}d</span>
                    </div>
                    <button onclick="darMed(${i})" class="bg-indigo-600 text-white px-4 py-2 rounded-xl font-black text-[10px] shadow-md">DAR</button>
                </div>
            `).join('');

            // Bibe Repeat
            if(DATA.lastBibe > 0) {
                document.getElementById('rep-bibe-box').classList.remove('hidden');
                document.getElementById('btn-rep-bibe').innerText = `REPETIR ÚLTIMA: ${DATA.lastBibe}ml`;
            } else {
                document.getElementById('rep-bibe-box').classList.add('hidden');
            }

            renderCal();
            updateRelojes();
        }

        function addLog(txt) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            const d = new Date().toLocaleDateString();
            b().logs.unshift({txt, hora: h, fecha: d, ts: Date.now()});
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
            const ahora = Date.now();
            if(ahora < m.last + (m.h * 3600000)) {
                if(!confirm(`⚠️ Aviso: No han pasado las ${m.h}h previstas. ¿Dar dosis igualmente?`)) return;
            }
            m.last = ahora;
            addLog(`💊 Med: ${m.n}`);
        }

        function addMed() {
            const n = document.getElementById('m-n').value, ds = document.getElementById('m-ds').value, 
                  h = document.getElementById('m-h').value, d = document.getElementById('m-d').value;
            if(!n || !h || !d) return alert("Rellena el tratamiento completo");
            b().meds.push({n, ds, h, d, last: 0});
            save();
        }

        function renderCal() {
            const grid = document.getElementById('cal-grid');
            const hoy = new Date();
            document.getElementById('cal-mes').innerText = hoy.toLocaleDateString('es', {month: 'long', year: 'numeric'});
            grid.innerHTML = "";
            const total = new Date(hoy.getFullYear(), hoy.getMonth()+1, 0).getDate();
            for(let i=1; i<=total; i++) {
                const esHoy = i === hoy.getDate();
                grid.innerHTML += `<div class="cal-day ${esHoy?'bg-blue-600 text-white shadow-xl':'bg-slate-50 text-slate-400'}">${i}</div>`;
            }
        }

        function updateRelojes() {
            const ultToma = b().logs.find(l => l.txt.includes('Bibe') || l.txt.includes('Pecho'));
            if(ultToma) {
                const rest = (DATA.plazo * 3600000) - (Date.now() - ultToma.ts);
                document.getElementById('label-ult-toma').innerText = `Última: ${ultToma.txt} (${ultToma.hora})`;
                document.getElementById('reloj-toma').innerText = formatTime(rest);
                document.getElementById('reloj-toma').className = rest < 0 ? 'text-2xl font-black text-red-500 animate-pulse' : 'text-2xl font-black text-white';
            }

            const medsActivas = b().meds.filter(m => m.last > 0);
            if(medsActivas.length > 0) {
                const proxima = medsActivas.sort((x,y) => (x.last + x.h*3600000) - (y.last + y.h*3600000))[0];
                const restMed = (proxima.last + proxima.h*3600000) - Date.now();
                document.getElementById('label-ult-med').innerText = `Próxima: ${proxima.n}`;
                document.getElementById('reloj-med').innerText = formatTime(restMed);
            }
        }

        function formatTime(ms) {
            const s = Math.abs(ms);
            const h = Math.floor(s/3600000);
            const m = Math.floor((s%3600000)/60000);
            return (ms < 0 ? '+' : '') + `${h}h ${m}m`;
        }

        function calcularEdad(f) {
            const d = Math.floor((new Date() - new Date(f)) / 86400000);
            if(d < 31) return `${d} DÍAS DE VIDA`;
            return `${Math.floor(d/30.4)} MESES DE VIDA`;
        }

        function saveSettings() {
            DATA.plazo = document.getElementById('set-plazo').value;
            save();
        }

        function addCompraUrgent() {
            const it = prompt("¿Qué falta comprar?");
            if(it) { b().compra.push(it); save(); }
        }

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
        function reiniciarApp() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        if(DATA.bebes.length === 0) {
            document.getElementById('setup-wizard').classList.remove('hidden');
        } else {
            document.getElementById('setup-wizard').classList.add('hidden');
            render();
        }
        setInterval(updateRelojes, 30000);
    </script>
</body>
</html>
