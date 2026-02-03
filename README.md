<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro V2</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .hidden { display: none !important; }
        .tab-active { color: #2563eb; border-bottom: 3px solid #2563eb; font-weight: 900; }
        .card { background: white; border-radius: 1.5rem; padding: 1.25rem; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
        .cal-day { min-height: 40px; display: flex; align-items: center; justify-content: center; border-radius: 0.75rem; font-weight: bold; font-size: 0.75rem; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 font-sans pb-24">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[200] flex flex-col p-8 transition-all">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-blue-600">Nombre del bebé</h2>
            <input id="setup-n" type="text" class="w-full p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">CONTINUAR</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-blue-600">Fecha de nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">CONTINUAR</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-blue-600 text-center">Peso y Talla al nacer</h2>
            <div class="flex gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Kg" class="w-1/2 p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
                <input id="setup-a" type="number" placeholder="cm" class="w-1/2 p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
            </div>
            <button onclick="finishSetup()" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">FINALIZAR</button>
        </div>
    </div>

    <header class="bg-white p-4 shadow-sm sticky top-0 z-50 border-b">
        <div class="flex justify-between items-start">
            <div onclick="openModal('modal-menu')" class="cursor-pointer">
                <h1 id="view-nombre" class="text-xl font-black text-blue-600 italic leading-tight">Cargando...</h1>
                <div class="mt-1">
                    <p id="view-edad" class="text-[11px] font-black text-slate-500 uppercase"></p>
                    <div class="flex gap-3 text-[10px] text-slate-400 mt-0.5">
                        <p id="view-nacimiento"></p>
                        <p id="view-actual" class="font-bold text-blue-500"></p>
                    </div>
                </div>
            </div>
            <button onclick="openModal('modal-menu')" class="p-3 bg-slate-100 rounded-2xl text-slate-600"><i class="fa-solid fa-bars"></i></button>
        </div>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4">
        <div class="card bg-slate-900 text-white">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-[10px] font-black uppercase opacity-60 tracking-widest">Próxima toma estimada</h3>
                <span id="countdown-toma" class="text-xl font-black text-blue-400">--:--</span>
            </div>
            <div id="lista-eventos-hoy" class="space-y-2 text-[11px] border-t border-white/10 pt-3">
                </div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-500 hidden">
            <h3 class="text-[10px] font-black text-orange-500 uppercase mb-2">Compra pendiente</h3>
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-3">
            <button onclick="openModal('modal-pecho')" class="bg-pink-500 text-white py-6 rounded-3xl font-black shadow-lg">PECHO</button>
            <button onclick="openModal('modal-bibe')" class="bg-blue-600 text-white py-6 rounded-3xl font-black shadow-lg">BIBERÓN</button>
        </div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white border p-4 rounded-2xl font-black text-cyan-600 text-[10px]">PIS</button>
            <button onclick="addLog('💩 Caca')" class="bg-white border p-4 rounded-2xl font-black text-orange-800 text-[10px]">CACA</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white border p-4 rounded-2xl font-black text-purple-600 text-[10px]">ASEO</button>
            <button onclick="addCompraUrgent()" class="bg-orange-600 text-white p-4 rounded-2xl"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="space-y-2">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest">Registro de hoy</h3>
            <div id="diario-hoy" class="bg-white rounded-3xl p-4 text-[11px] font-bold text-slate-500 space-y-3 border"></div>
        </div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-4">
        <div class="card">
            <div class="flex justify-between items-center mb-4">
                <h3 id="cal-mes" class="font-black text-blue-600 uppercase text-sm"></h3>
                <button onclick="addCita()" class="bg-blue-600 text-white px-3 py-1 rounded-lg text-[10px] font-black">+ AÑADIR CITA</button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1">
                <div class="text-center text-[9px] font-bold text-slate-300">L</div>
                <div class="text-center text-[9px] font-bold text-slate-300">M</div>
                <div class="text-center text-[9px] font-bold text-slate-300">X</div>
                <div class="text-center text-[9px] font-bold text-slate-300">J</div>
                <div class="text-center text-[9px] font-bold text-slate-300">V</div>
                <div class="text-center text-[9px] font-bold text-slate-300">S</div>
                <div class="text-center text-[9px] font-bold text-slate-300">D</div>
            </div>
        </div>
        <div id="cal-eventos" class="space-y-2"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="card">
            <h3 class="text-sm font-black text-indigo-600 uppercase mb-3">Nuevo Tratamiento</h3>
            <div class="space-y-3">
                <input id="m-n" type="text" placeholder="Medicina (ej. Apiretal)" class="w-full p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                <div class="flex gap-2">
                    <input id="m-ds" type="text" placeholder="Dosis (ml)" class="w-1/3 p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                    <input id="m-h" type="number" placeholder="Horas" class="w-1/3 p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                    <input id="m-d" type="number" placeholder="Días" class="w-1/3 p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                </div>
                <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-3 rounded-xl font-black text-xs">GUARDAR TRATAMIENTO</button>
            </div>
        </div>
        <div id="lista-salud" class="space-y-3 pb-8"></div>
    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white border-t flex justify-around p-3 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="flex flex-col items-center text-blue-600"><i class="fa-solid fa-house"></i><span class="text-[9px] font-black uppercase">Inicio</span></button>
        <button onclick="showTab('cal')" id="nav-cal" class="flex flex-col items-center text-slate-400"><i class="fa-solid fa-calendar"></i><span class="text-[9px] font-black uppercase">Citas</span></button>
        <button onclick="showTab('salud')" id="nav-salud" class="flex flex-col items-center text-slate-400"><i class="fa-solid fa-stethoscope"></i><span class="text-[9px] font-black uppercase">Salud</span></button>
    </nav>

    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[300] flex items-end">
        <div class="bg-white w-full p-8 rounded-t-[40px] grid grid-cols-3 gap-4">
            <button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-2xl font-black text-pink-600">IZQ</button>
            <button onclick="addLog('🤱 Ambos Pechos'); closeModal('modal-pecho')" class="bg-pink-200 p-8 rounded-2xl font-black text-pink-700 text-xs text-center">AMBOS</button>
            <button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-2xl font-black text-pink-600">DER</button>
            <button onclick="closeModal('modal-pecho')" class="col-span-3 text-slate-400 font-bold p-4 text-center">CANCELAR</button>
        </div>
    </div>

    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[300] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <div id="rep-bibe-box" class="hidden">
                <button id="btn-rep-bibe" onclick="addBibe(DATA.lastBibe)" class="w-full bg-blue-100 text-blue-600 p-6 rounded-3xl font-black mb-2 uppercase"></button>
            </div>
            <input id="bibe-ml" type="number" placeholder="Nuevo ml" class="w-full p-4 bg-slate-100 rounded-2xl text-center text-xl font-black">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">REGISTRAR</button>
            <button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 font-bold uppercase">Cancelar</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[300] flex justify-end">
        <div class="bg-white w-4/5 h-full p-6 space-y-6">
            <h2 class="text-2xl font-black text-blue-600 italic">Eva Care Menu</h2>
            <div class="bg-slate-50 p-4 rounded-2xl space-y-3">
                <p class="text-[10px] font-black text-slate-400 uppercase">Ajustes de Toma</p>
                <div class="flex justify-between items-center">
                    <span class="text-xs font-bold">Plazo entre tomas (h):</span>
                    <input id="set-tomas" type="number" class="w-12 p-1 border rounded text-center font-bold">
                </div>
                <button onclick="saveSettings()" class="w-full bg-blue-600 text-white py-2 rounded-xl text-xs font-bold">GUARDAR AJUSTES</button>
            </div>
            <div class="space-y-2">
                <button onclick="openEditBebe()" class="w-full text-left p-4 bg-slate-100 rounded-xl font-bold text-sm">Editar datos del bebé</button>
                <button onclick="addNewBebe()" class="w-full text-left p-4 bg-slate-100 rounded-xl font-bold text-sm">Añadir otro bebé</button>
                <button onclick="reiniciarApp()" class="w-full text-left p-4 bg-red-50 text-red-600 rounded-xl font-bold text-sm mt-10">Borrar y reiniciar</button>
            </div>
            <button onclick="closeModal('modal-menu')" class="w-full py-4 text-slate-300 font-black">CERRAR</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_VFINAL')) || {
            idx: 0,
            bebes: [],
            lastBibe: 0,
            plazo: 3
        };

        const b = () => DATA.bebes[DATA.idx];

        function save() {
            localStorage.setItem('EVA_CARE_VFINAL', JSON.stringify(DATA));
            render();
        }

        // VALIDACIONES WIZARD
        function validaPaso(actual, sig) {
            const input = document.getElementById(`setup-${actual === 1 ? 'n' : 'f'}`).value;
            if(!input) { alert("Este campo es obligatorio"); return; }
            document.getElementById(`step-${actual}`).classList.add('hidden');
            document.getElementById(`step-${sig}`).classList.remove('hidden');
        }

        function finishSetup() {
            const n = document.getElementById('setup-n').value;
            const f = document.getElementById('setup-f').value;
            const p = document.getElementById('setup-p').value;
            const a = document.getElementById('setup-a').value;
            if(!n || !f || !p || !a) { alert("Por favor, rellena todos los campos"); return; }
            
            const nuevo = {
                n, f, p, a, pAct: p, aAct: a,
                logs: [], meds: [], compra: [], eventos: []
            };
            DATA.bebes.push(nuevo);
            DATA.idx = DATA.bebes.length - 1;
            document.getElementById('setup-wizard').classList.add('hidden');
            save();
        }

        // RENDER
        function render() {
            if (DATA.bebes.length === 0) return;
            const be = b();

            // Cabecera
            document.getElementById('view-nombre').innerText = be.n;
            document.getElementById('view-edad').innerText = calcularEdad(be.f);
            document.getElementById('view-nacimiento').innerText = `NACIMIENTO: ${be.p}kg | ${be.a}cm`;
            document.getElementById('view-actual').innerText = `ÚLTIMO: ${be.pAct}kg | ${be.aAct}cm`;
            document.getElementById('set-tomas').value = DATA.plazo;

            // Compra
            const cBox = document.getElementById('card-compra');
            cBox.classList.toggle('hidden', be.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = be.compra.map((it, i) => `
                <div class="flex justify-between items-center bg-orange-50 p-3 rounded-xl border border-orange-100">
                    <span class="text-xs font-bold text-orange-800">${it}</span>
                    <button onclick="checkCompra(${i})" class="w-6 h-6 bg-white border-2 border-orange-400 rounded-full text-orange-600">✓</button>
                </div>
            `).join('');

            // Diario
            const hoy = new Date().toLocaleDateString();
            const logsHoy = be.logs.filter(l => l.fecha === hoy);
            document.getElementById('diario-hoy').innerHTML = logsHoy.map(l => `
                <div class="flex justify-between items-center border-b border-slate-50 pb-1">
                    <span>${l.txt}</span>
                    <span class="text-[9px] text-slate-300 italic">${l.hora}</span>
                </div>
            `).join('') || "<p class='text-center opacity-30 italic py-2'>Sin actividad</p>";

            // Salud
            document.getElementById('lista-salud').innerHTML = be.meds.map((m, i) => `
                <div class="card flex justify-between items-center border-l-4 border-indigo-500">
                    <div>
                        <b class="text-indigo-600 text-xs">${m.n}</b><br>
                        <span class="text-[10px] text-slate-400">${m.ds}ml | cada ${m.h}h | resto ${m.d} días</span>
                    </div>
                    <button onclick="darMed(${i})" class="bg-indigo-100 text-indigo-600 px-4 py-2 rounded-xl font-black text-[10px]">TOMAR</button>
                </div>
            `).join('');

            // Bibe Repeat
            if(DATA.lastBibe > 0) {
                document.getElementById('rep-bibe-box').classList.remove('hidden');
                document.getElementById('btn-rep-bibe').innerText = `REPETIR ÚLTIMA: ${DATA.lastBibe}ml`;
            }

            renderCal();
            updateRelojes();
        }

        // LOGS
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
        }

        // SALUD
        function addMed() {
            const n = document.getElementById('m-n').value, ds = document.getElementById('m-ds').value, h = document.getElementById('m-h').value, d = document.getElementById('m-d').value;
            if(!n || !h || !d) return alert("Completa los datos de medicación");
            b().meds.push({n, ds, h, d, last: 0});
            save();
        }

        function darMed(i) {
            const m = b().meds[i];
            const ahora = Date.now();
            if(ahora < m.last + (m.h * 3600000)) {
                if(!confirm(`⚠️ Aún no han pasado las ${m.h}h de rigor. ¿Deseas darla de todos modos?`)) return;
            }
            m.last = ahora;
            addLog(`💊 Toma: ${m.n}`);
        }

        // CALENDARIO (CORREGIDO)
        function renderCal() {
            const grid = document.getElementById('cal-grid');
            const tit = document.getElementById('cal-mes');
            const hoy = new Date();
            tit.innerText = hoy.toLocaleDateString('es', {month: 'long', year: 'numeric'});
            
            // Limpiar días antiguos (mantener cabecera L-D)
            const days = grid.querySelectorAll('.cal-day');
            days.forEach(d => d.remove());

            const total = new Date(hoy.getFullYear(), hoy.getMonth()+1, 0).getDate();
            for(let i=1; i<=total; i++) {
                const esHoy = i === hoy.getDate();
                const div = document.createElement('div');
                div.className = `cal-day ${esHoy ? 'bg-blue-600 text-white shadow-lg' : 'bg-white border'}`;
                div.innerText = i;
                div.onclick = () => alert("Citas pronto...");
                grid.appendChild(div);
            }
        }

        // HELPERS
        function updateRelojes() {
            const ultToma = b().logs.find(l => l.txt.includes('Bibe') || l.txt.includes('Pecho'));
            if(ultToma) {
                const rest = (DATA.plazo * 3600000) - (Date.now() - ultToma.ts);
                const color = rest < 0 ? 'text-red-500 animate-pulse' : 'text-blue-400';
                const s = Math.abs(rest);
                const h = Math.floor(s/3600000);
                const m = Math.floor((s%3600000)/60000);
                document.getElementById('countdown-toma').innerText = (rest < 0 ? '+' : '') + `${h}h ${m}m`;
                document.getElementById('countdown-toma').className = `text-xl font-black ${color}`;
            }
        }

        function calcularEdad(f) {
            const d = Math.floor((new Date() - new Date(f)) / 86400000);
            return d < 31 ? `${d} DÍAS` : `${Math.floor(d/30.4)} MESES`;
        }

        function saveSettings() {
            DATA.plazo = document.getElementById('set-tomas').value;
            save();
            closeModal('modal-menu');
        }

        function addCompraUrgent() {
            const it = prompt("¿Qué falta?");
            if(it) { b().compra.push(it); save(); }
        }

        function checkCompra(i) { b().compra.splice(i, 1); save(); }

        function showTab(id) {
            ['inicio', 'cal', 'salud'].forEach(t => {
                document.getElementById('tab-' + t).classList.toggle('hidden', t !== id);
                document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id);
                document.getElementById('nav-' + t).classList.toggle('text-slate-400', t !== id);
            });
        }

        function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }
        function reiniciarApp() { if(confirm("Borrar todo?")) { localStorage.clear(); location.reload(); } }

        // Start
        if(DATA.bebes.length === 0) {
            document.getElementById('setup-wizard').classList.remove('hidden');
        } else {
            document.getElementById('setup-wizard').classList.add('hidden');
            render();
        }
        setInterval(updateRelojes, 60000);
    </script>
</body>
</html>
