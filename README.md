<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>EvaCare</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 2rem; padding: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; position: relative; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 6px; }
        /* Animación suave */
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[1000] flex flex-col p-8 hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold tracking-tighter text-slate-900 italic">EvaCare</h2>
            <p class="text-[10px] font-black uppercase text-blue-600 tracking-widest">Nombre del bebé</p>
            <input id="setup-n" type="text" placeholder="Ej. Eva" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold border-none outline-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold border-none outline-none">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Peso (kg)" class="p-5 bg-slate-100 rounded-3xl font-bold">
                <input id="setup-a" type="number" placeholder="Talla (cm)" class="p-5 bg-slate-100 rounded-3xl font-bold">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black uppercase">Finalizar</button>
        </div>
    </div>

    <main id="tab-inicio" class="p-4 space-y-4">
        <header class="pt-4 pb-2">
            <div class="flex justify-between items-center mb-6">
                <div class="flex items-center gap-1">
                    <span class="text-2xl font-extrabold tracking-tighter text-slate-900 italic">EvaCare</span>
                    <span class="w-2 h-2 rounded-full bg-blue-600 mb-1.5"></span>
                </div>
                <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm">
                    <i class="fa-solid fa-bars-staggered"></i>
                </button>
            </div>
            <div class="bg-blue-600 p-6 rounded-[2.5rem] text-white shadow-xl shadow-blue-100">
                <div class="flex justify-between items-end">
                    <div>
                        <h2 id="view-nombre" class="text-2xl font-extrabold leading-none">---</h2>
                        <p id="view-edad" class="text-[10px] font-black text-blue-200 mt-2 uppercase tracking-widest"></p>
                    </div>
                    <div class="text-right">
                        <p id="view-nacimiento" class="text-[9px] font-medium opacity-60"></p>
                        <p id="view-actual" class="text-[10px] font-black"></p>
                    </div>
                </div>
            </div>
        </header>

        <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
            <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4">Próximos 14 días</h3>
            <div id="timeline-eventos" class="space-y-4"></div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-400 hidden bg-orange-50/30">
            <h3 class="text-[10px] font-black text-orange-600 uppercase mb-2">Pendiente de Comprar:</h3>
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i>
                <span class="text-[10px] font-black uppercase text-slate-400">Pecho</span>
            </button>
            <button onclick="openModal('modal-bibe')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-bottle-water text-blue-500 text-xl"></i>
                <span class="text-[10px] font-black uppercase text-slate-400">Biberón</span>
            </button>
        </div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] uppercase shadow-sm border border-slate-50">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] uppercase shadow-sm border border-slate-50">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] uppercase shadow-sm border border-slate-50">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-slate-900 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="space-y-3 pt-2">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest px-2">Actividad de hoy</h3>
            <div id="diario-hoy" class="space-y-2"></div>
        </div>
    </main>

    <main id="tab-cal" class="hidden p-4">
        <div class="card">
            <h3 id="cal-mes" class="font-black text-slate-900 uppercase text-center mb-6 tracking-widest"></h3>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="card">
            <h3 class="text-xs font-black text-blue-600 uppercase mb-4 tracking-tighter">Nuevo Tratamiento</h3>
            <div class="space-y-3">
                <input id="m-n" type="text" placeholder="Medicamento" class="w-full p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none">
                <div class="grid grid-cols-2 gap-2">
                    <input id="m-q" type="text" placeholder="Dosis (Ej: 2ml)" class="p-4 bg-slate-50 rounded-2xl font-bold">
                    <input id="m-h" type="number" placeholder="Cada horas" class="p-4 bg-slate-50 rounded-2xl font-bold">
                </div>
                <input id="m-d" type="number" placeholder="Días de duración" class="w-full p-4 bg-slate-50 rounded-2xl font-bold">
                <button onclick="addMed()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar Medicina</button>
            </div>
        </div>
        <div id="lista-salud" class="space-y-3"></div>
    </main>

    <nav class="fixed bottom-6 left-6 right-6 h-16 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="w-12 h-12 rounded-full text-blue-600 flex items-center justify-center"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center"><i class="fa-solid fa-calendar-day text-lg"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center"><i class="fa-solid fa-suitcase-medical text-lg"></i></button>
    </nav>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[1000] flex justify-end">
        <div class="bg-white w-4/5 h-full p-8 space-y-8 flex flex-col">
            <h2 class="text-3xl font-extrabold italic">Ajustes</h2>
            <div class="flex-1 space-y-4">
                <div class="bg-slate-50 p-6 rounded-3xl">
                    <p class="text-[10px] font-black text-blue-600 uppercase mb-2">Aviso de tomas (h)</p>
                    <input id="set-plazo" type="number" class="w-full p-4 rounded-xl font-bold bg-white">
                </div>
                <button onclick="saveSettings(); closeModal('modal-menu')" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black">GUARDAR</button>
                <button onclick="reiniciarApp()" class="w-full p-4 bg-red-50 text-red-600 rounded-2xl font-bold text-xs">BORRAR DATOS</button>
            </div>
            <button onclick="closeModal('modal-menu')" class="text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-cal" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4 shadow-2xl">
            <h3 id="cal-modal-fecha" class="font-black text-center text-blue-600 uppercase text-xs"></h3>
            <input id="cal-ev-txt" type="text" placeholder="¿Qué evento?" class="w-full p-4 bg-slate-100 rounded-2xl font-bold outline-none border-none">
            <input id="cal-ev-hora" type="time" class="w-full p-4 bg-slate-100 rounded-2xl font-bold text-center border-none">
            <button onclick="saveCita()" class="w-full bg-slate-900 text-white p-5 rounded-2xl font-black">GUARDAR EVENTO</button>
            <button onclick="closeModal('modal-cal')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <button id="btn-rep-bibe" onclick="addBibe(DATA.lastBibe)" class="w-full bg-blue-50 text-blue-600 p-6 rounded-3xl font-black uppercase text-xs">---</button>
            <input id="bibe-ml" type="number" placeholder="ml" class="w-full p-6 bg-slate-100 rounded-3xl text-center text-3xl font-black outline-none">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-6 rounded-3xl font-black">REGISTRAR</button>
            <button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-end">
        <div class="bg-white w-full p-10 rounded-t-[3rem] grid grid-cols-3 gap-4">
            <button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs">IZQ</button>
            <button onclick="addLog('🤱 Ambos'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-700 text-[10px]">AMBOS</button>
            <button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs">DER</button>
            <button onclick="closeModal('modal-pecho')" class="col-span-3 text-slate-300 font-black p-4 text-center uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V10')) || {
            idx: 0, bebes: [], lastBibe: 0, plazo: 3
        };
        let selectedDate = null;
        const b = () => DATA.bebes[DATA.idx];

        function save() { localStorage.setItem('EVA_CARE_V10', JSON.stringify(DATA)); render(); }

        function validaPaso(actual, sig) {
            const val = document.getElementById(`setup-${actual === 1 ? 'n' : (actual === 2 ? 'f' : 'p')}`).value;
            if(!val) return;
            document.getElementById(`step-${actual}`).classList.add('hidden');
            document.getElementById(`step-${sig}`).classList.remove('hidden');
        }

        function finishSetup() {
            const n = document.getElementById('setup-n').value, f = document.getElementById('setup-f').value,
                  p = document.getElementById('setup-p').value, a = document.getElementById('setup-a').value;
            DATA.bebes.push({ n, f, p, a, logs: [], meds: [], compra: [], citas: {} });
            DATA.idx = DATA.bebes.length - 1;
            document.getElementById('setup-wizard').classList.add('hidden');
            save();
        }

        function render() {
            if (DATA.bebes.length === 0) {
                document.getElementById('setup-wizard').classList.remove('hidden');
                return;
            }
            const be = b();

            document.getElementById('view-nombre').innerText = be.n;
            document.getElementById('view-edad').innerText = calcularEdad(be.f);
            document.getElementById('view-nacimiento').innerText = `Nace: ${be.p}kg · ${be.a}cm`;
            document.getElementById('view-actual').innerText = `Toma: ${DATA.plazo}h`;
            document.getElementById('set-plazo').value = DATA.plazo;
            
            // Texto del botón repetir bibe
            const btnBibe = document.getElementById('btn-rep-bibe');
            if(DATA.lastBibe > 0) btnBibe.innerText = `REPETIR ÚLTIMO (${DATA.lastBibe}ml)`;
            else btnBibe.innerText = "NO HAY REGISTRO PREVIO";

            renderTimeline();
            
            const hoyStr = new Date().toLocaleDateString();
            document.getElementById('diario-hoy').innerHTML = be.logs.filter(l => l.fecha === hoyStr).map(l => `
                <div class="bg-white p-4 rounded-2xl flex justify-between items-center shadow-sm border border-slate-50">
                    <span class="text-xs font-bold text-slate-700">${l.txt}</span>
                    <span class="text-[9px] font-bold text-slate-300 bg-slate-50 px-2 py-1 rounded-lg">${l.hora}</span>
                </div>
            `).join('') || "<p class='text-center py-6 text-slate-300 text-[10px] italic uppercase'>Esperando registros</p>";

            const cBox = document.getElementById('card-compra');
            cBox.classList.toggle('hidden', be.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = be.compra.map((it, i) => `
                <div class="flex justify-between items-center p-1"><span class="text-[11px] font-bold text-orange-900">${it}</span><button onclick="checkCompra(${i})" class="text-orange-400 px-2">✓</button></div>
            `).join('');

            document.getElementById('lista-salud').innerHTML = be.meds.map((m, i) => `
                <div class="card flex justify-between items-center border-l-4 border-indigo-500 mb-2 relative">
                    <button onclick="deleteMed(${i})" class="absolute top-2 right-4 text-slate-200 text-[9px] font-black">BORRAR</button>
                    <div>
                        <b class="text-indigo-600 text-xs uppercase">${m.n}</b><br>
                        <span class="text-[9px] text-slate-400 font-bold uppercase">Dosis: ${m.q} | Cada ${m.h}h</span>
                    </div>
                    <button onclick="darMed(${i})" class="bg-indigo-600 text-white px-5 py-2 rounded-xl font-black text-[10px]">DAR</button>
                </div>
            `).join('');

            renderCal();
        }

        function renderTimeline() {
            const be = b();
            const container = document.getElementById('timeline-eventos');
            let html = "";
            const ahora = Date.now();

            // TOMAS
            const ultToma = be.logs.find(l => l.txt.includes('Bibe') || l.txt.includes('Pecho'));
            if(ultToma) {
                const rest = (DATA.plazo * 3600000) - (ahora - ultToma.ts);
                html += `
                <div class="flex justify-between items-center timeline-item bg-white/5 p-3 rounded-2xl">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-full bg-blue-500/20 flex items-center justify-center text-blue-400 text-xs"><i class="fa-solid fa-baby"></i></div>
                        <div><p class="text-[9px] font-black uppercase text-slate-400">Siguiente Toma</p></div>
                    </div>
                    <span class="text-lg font-black ${rest < 0 ? 'text-red-500 animate-pulse' : 'text-white'}">${formatTime(rest)}</span>
                </div>`;
            }

            // MEDICINAS
            be.meds.forEach(m => {
                if(m.last > 0) {
                    const restMed = (m.last + m.h*3600000) - ahora;
                    html += `
                    <div class="flex justify-between items-center timeline-item bg-white/5 p-3 rounded-2xl">
                        <div class="flex items-center gap-3">
                            <div class="w-8 h-8 rounded-full bg-indigo-500/20 flex items-center justify-center text-indigo-400 text-xs"><i class="fa-solid fa-pills"></i></div>
                            <p class="text-[9px] font-black uppercase text-slate-300">${m.n} (${m.q})</p>
                        </div>
                        <span class="text-sm font-black ${restMed < 0 ? 'text-red-400 animate-pulse' : 'text-slate-400'}">${formatTime(restMed)}</span>
                    </div>`;
                }
            });

            // AGENDA PRÓXIMOS 14 DÍAS (Semanas actual y siguiente)
            const proximasCitas = [];
            for (let i = 0; i < 14; i++) {
                const d = new Date(); d.setDate(d.getDate() + i);
                const clave = d.toISOString().split('T')[0];
                if (be.citas[clave]) {
                    be.citas[clave].forEach(c => proximasCitas.push({ ...c, fecha: clave, fullDate: d }));
                }
            }

            proximasCitas.forEach(cita => {
                const fechaLegible = new Date(cita.fecha).toLocaleDateString('es', { day: 'numeric', month: 'short' });
                html += `
                <div class="flex justify-between items-center timeline-item border-t border-white/5 pt-3">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-full bg-orange-500/20 flex items-center justify-center text-orange-400 text-xs"><i class="fa-solid fa-calendar"></i></div>
                        <div>
                            <p class="text-[10px] font-black uppercase text-orange-400">${cita.txt}</p>
                            <p class="text-[8px] font-bold text-slate-500 uppercase">${fechaLegible} a las ${cita.hora}</p>
                        </div>
                    </div>
                </div>`;
            });

            container.innerHTML = html || "<p class='text-center text-[9px] text-slate-600 uppercase py-4'>No hay tareas próximas</p>";
        }

        function addLog(txt) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            b().logs.unshift({txt, hora: h, fecha: new Date().toLocaleDateString(), ts: Date.now()});
            save();
        }
        function addBibe(ml) { if(!ml) return; DATA.lastBibe = ml; addLog(`🍼 Bibe ${ml}ml`); closeModal('modal-bibe'); }
        function addMed() {
            const n = document.getElementById('m-n').value, h = document.getElementById('m-h').value, 
                  q = document.getElementById('m-q').value, d = document.getElementById('m-d').value;
            if(!n || !h || !q) return alert("Rellena Medicina, Dosis y Horas");
            b().meds.push({n, h: parseInt(h), q, d, last: 0}); save();
        }
        function darMed(i) {
            const m = b().meds[i];
            if(m.last > 0 && (Date.now() - m.last) < (m.h * 3600000)) {
                if(!confirm("⚠️ Aún no toca. ¿Dar dosis igualmente?")) return;
            }
            m.last = Date.now(); addLog(`💊 ${m.n} (${m.q})`);
        }
        function deleteMed(i) { if(confirm("¿Borrar medicina?")) { b().meds.splice(i, 1); save(); } }

        function renderCal() {
            const grid = document.getElementById('cal-grid');
            const hoy = new Date();
            document.getElementById('cal-mes').innerText = hoy.toLocaleDateString('es', {month: 'long', year: 'numeric'});
            grid.innerHTML = "";
            const total = new Date(hoy.getFullYear(), hoy.getMonth()+1, 0).getDate();
            for(let i=1; i<=total; i++) {
                const dStr = `${hoy.getFullYear()}-${String(hoy.getMonth()+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
                const tiene = b().citas[dStr] && b().citas[dStr].length > 0;
                grid.innerHTML += `<div onclick="openDay('${dStr}')" class="cal-day ${i==hoy.getDate()?'bg-blue-600 text-white shadow-lg':''}">${i}${tiene?'<div class="event-dot"></div>':''}</div>`;
            }
        }
        function openDay(f) { selectedDate = f; document.getElementById('cal-modal-fecha').innerText = f; openModal('modal-cal'); }
        function saveCita() {
            const t = document.getElementById('cal-ev-txt').value, h = document.getElementById('cal-ev-hora').value;
            if(!t || !h) return;
            if(!b().citas[selectedDate]) b().citas[selectedDate] = [];
            b().citas[selectedDate].push({txt: t, hora: h});
            save(); closeModal('modal-cal');
        }

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
        function showTab(id) {
            ['inicio', 'cal', 'salud'].forEach(t => {
                document.getElementById('tab-' + t).classList.toggle('hidden', t !== id);
                document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id);
                document.getElementById('nav-' + t).classList.toggle('text-slate-300', t !== id);
            });
            window.scrollTo(0,0);
        }
        function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }
        function addCompraUrgent() { const it = prompt("¿Qué falta?"); if(it) { b().compra.push(it); save(); } }
        function checkCompra(i) { b().compra.splice(i, 1); save(); }
        function reiniciarApp() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        render();
        setInterval(renderTimeline, 30000);
    </script>
</body>
</html>
