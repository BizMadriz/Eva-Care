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
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; position: relative; cursor: pointer; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 6px; }
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[1000] flex flex-col p-8 hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold tracking-tighter italic">EvaCare</h2>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold border-none outline-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas iniciales</h2>
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
                <span class="text-2xl font-extrabold tracking-tighter italic">EvaCare</span>
                <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm border border-slate-100"><i class="fa-solid fa-bars-staggered"></i></button>
            </div>
            <div class="bg-blue-600 p-6 rounded-[2.5rem] text-white shadow-xl">
                <div class="flex justify-between items-end">
                    <div>
                        <h2 id="view-nombre" class="text-2xl font-extrabold leading-none">---</h2>
                        <p id="view-edad" class="text-[10px] font-black text-blue-200 mt-2 uppercase tracking-widest"></p>
                    </div>
                    <div class="text-right text-[10px] font-bold">
                        <p id="view-medidas-act"></p>
                        <p id="view-nacimiento" class="opacity-60 text-[8px]"></p>
                    </div>
                </div>
            </div>
        </header>

        <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
            <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4">Próximos 15 días</h3>
            <div id="timeline-eventos" class="space-y-3"></div>
        </div>

        <div id="lista-compra-container" class="hidden bg-amber-50 border border-amber-100 p-5 rounded-[2rem]">
            <h4 class="text-[10px] font-black text-amber-600 uppercase mb-3 tracking-widest">Pendiente de comprar</h4>
            <div id="lista-compra-items" class="flex flex-wrap gap-2"></div>
        </div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] uppercase shadow-sm border">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] uppercase shadow-sm border">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] uppercase shadow-sm border">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-slate-900 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border"><i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i><span class="text-[10px] font-black uppercase text-slate-400">Pecho</span></button>
            <button onclick="openModal('modal-bibe')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border"><i class="fa-solid fa-bottle-water text-blue-500 text-xl"></i><span class="text-[10px] font-black uppercase text-slate-400">Biberón</span></button>
        </div>

        <div id="diario-hoy" class="space-y-2 pt-2"></div>
    </main>

    <main id="tab-nutricion" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic p-2 mt-4">Alimentación</h2>
        <div class="card space-y-4">
            <h3 class="text-xs font-black text-orange-600 uppercase tracking-widest">Nuevo Alimento</h3>
            <input id="food-n" type="text" placeholder="Ej: Plátano, Huevo..." class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="setFoodReac('😋 Le gusta')" class="bg-slate-50 p-4 rounded-xl text-lg">😋</button>
                <button onclick="setFoodReac('🤢 No le gusta')" class="bg-slate-50 p-4 rounded-xl text-lg">🤢</button>
            </div>
            <div class="flex items-center gap-3 bg-red-50 p-3 rounded-xl">
                <input type="checkbox" id="food-alert" class="w-4 h-4">
                <label for="food-alert" class="text-[10px] font-bold text-red-600 uppercase">¿Hubo reacción alérgica?</label>
            </div>
            <button onclick="addFood()" class="w-full bg-orange-500 text-white p-4 rounded-2xl font-black uppercase text-xs">Registrar Prueba</button>
        </div>
        <div id="lista-alimentos" class="space-y-2 pt-4"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="grid grid-cols-1 gap-3">
            <button onclick="openModal('modal-add-med')" class="w-full bg-indigo-600 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg">
                <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Tratamientos</p><p class="text-lg">Añadir Medicación</p></div>
                <i class="fa-solid fa-pills text-2xl opacity-50"></i>
            </button>
            <button onclick="openModal('modal-add-crecimiento')" class="w-full bg-emerald-600 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg">
                <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Evolución</p><p class="text-lg">Peso y Talla</p></div>
                <i class="fa-solid fa-scale-balanced text-2xl opacity-50"></i>
            </button>
        </div>
        <div id="lista-salud" class="space-y-3 pt-4"></div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-6">
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <button onclick="changeMonth(-1)" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400"><i class="fa-solid fa-chevron-left"></i></button>
                <h3 id="cal-mes-titulo" class="font-black text-slate-900 uppercase tracking-widest text-sm text-center">---</h3>
                <button onclick="changeMonth(1)" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400"><i class="fa-solid fa-chevron-right"></i></button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>
        <div id="agenda-completa" class="space-y-2"></div>
    </main>

    <nav class="fixed bottom-6 left-6 right-6 h-16 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="text-blue-600"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="showTab('nutricion')" id="nav-nutricion" class="text-slate-300"><i class="fa-solid fa-carrot text-lg"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="text-slate-300"><i class="fa-solid fa-suitcase-medical text-lg"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="text-slate-300"><i class="fa-solid fa-calendar-day text-lg"></i></button>
    </nav>

    <div id="modal-add-med" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 class="text-xs font-black text-indigo-600 uppercase text-center tracking-widest">Nueva Medicación</h3>
            <input id="m-n" type="text" placeholder="Nombre (Ej: Apiretal)" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none">
            <div class="grid grid-cols-2 gap-2">
                <input id="m-q" type="text" placeholder="Dosis (ml)" class="p-4 bg-slate-50 rounded-2xl font-bold">
                <input id="m-h" type="number" placeholder="Cada h" class="p-4 bg-slate-50 rounded-2xl font-bold">
            </div>
            <div>
                <p class="text-[8px] font-black text-slate-400 uppercase mb-1 ml-2">Duración (días)</p>
                <input id="m-d" type="number" placeholder="Ej: 7" class="w-full p-4 bg-slate-50 rounded-2xl font-bold">
            </div>
            <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button>
            <button onclick="closeModal('modal-add-med')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cancelar</button>
        </div>
    </div>

    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <input id="bibe-ml" type="number" placeholder="ml" class="w-full p-6 bg-slate-100 rounded-3xl text-center text-3xl font-black outline-none">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-6 rounded-3xl font-black">REGISTRAR</button>
            <button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-end">
        <div class="bg-white w-full p-10 rounded-t-[3rem] grid grid-cols-3 gap-4">
            <button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs text-center">IZQ</button>
            <button onclick="addLog('🤱 Ambos'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-700 text-xs text-center">AMBOS</button>
            <button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs text-center">DER</button>
        </div>
    </div>

    <div id="modal-cal" class="hidden fixed inset-0 bg-black/80 z-[1000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4 shadow-2xl">
            <h3 id="cal-modal-fecha" class="font-black text-center text-blue-600 uppercase text-xs tracking-widest"></h3>
            <input id="cal-ev-txt" type="text" placeholder="¿Qué evento?" class="w-full p-4 bg-slate-100 rounded-2xl font-bold outline-none">
            <input id="cal-ev-hora" type="time" class="w-full p-4 bg-slate-100 rounded-2xl font-bold text-center">
            <button onclick="saveCita()" class="w-full bg-slate-900 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button>
            <button onclick="closeModal('modal-cal')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[1000] flex justify-end">
        <div class="bg-white w-4/5 h-full p-8 flex flex-col">
            <h2 class="text-3xl font-extrabold italic mb-8">Ajustes</h2>
            <div class="flex-1 space-y-4">
                <button onclick="reiniciarApp()" class="w-full p-4 bg-red-50 text-red-600 rounded-2xl font-bold text-[10px] uppercase">Borrar Datos</button>
            </div>
            <button onclick="closeModal('modal-menu')" class="text-slate-300 font-bold uppercase text-[10px] py-4">Cerrar</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V15')) || {
            idx: 0, bebes: [], lastBibe: 0, plazo: 3
        };
        let currentDate = new Date();
        let selectedDate = null;
        let foodReac = "";
        const b = () => DATA.bebes[DATA.idx];

        function save() { localStorage.setItem('EVA_CARE_V15', JSON.stringify(DATA)); render(); }

        // --- CORE & SETUP ---
        function validaPaso(actual, sig) {
            const id = actual === 1 ? 'n' : (actual === 2 ? 'f' : 'p');
            if(!document.getElementById(`setup-${id}`).value) return;
            document.getElementById(`step-${actual}`).classList.add('hidden');
            document.getElementById(`step-${sig}`).classList.remove('hidden');
        }

        function finishSetup() {
            const n = document.getElementById('setup-n').value, f = document.getElementById('setup-f').value,
                  p = document.getElementById('setup-p').value, a = document.getElementById('setup-a').value;
            DATA.bebes.push({ n, f, p, a, curP: p, curA: a, logs: [], meds: [], compra: [], citas: {}, foods: [] });
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
            document.getElementById('view-medidas-act').innerText = `${be.curP || be.p}kg · ${be.curA || be.a}cm`;
            document.getElementById('view-nacimiento').innerText = `Nacimiento: ${be.p}kg · ${be.a}cm`;

            renderTimeline();
            renderLogs();
            renderSalud();
            renderCompra();
            renderFoods();
            renderCal();
        }

        function renderLogs() {
            const be = b();
            const hoyStr = new Date().toLocaleDateString();
            document.getElementById('diario-hoy').innerHTML = be.logs.filter(l => l.fecha === hoyStr).map(l => `
                <div class="bg-white p-4 rounded-2xl flex justify-between items-center shadow-sm border border-slate-50 mb-2">
                    <span class="text-xs font-bold text-slate-700">${l.txt}</span>
                    <span class="text-[9px] font-bold text-slate-300 bg-slate-50 px-2 py-1 rounded-lg">${l.hora}</span>
                </div>
            `).join('');
        }

        // --- ALIMENTACIÓN ---
        function setFoodReac(r) { foodReac = r; }
        function addFood() {
            const n = document.getElementById('food-n').value;
            const al = document.getElementById('food-alert').checked;
            if(!n || !foodReac) return;
            if(!b().foods) b().foods = [];
            b().foods.unshift({ n, r: foodReac, al, f: new Date().toLocaleDateString() });
            addLog(`🍴 Probó: ${n} (${foodReac})`);
            document.getElementById('food-n').value = "";
            document.getElementById('food-alert').checked = false;
            save();
        }
        function renderFoods() {
            const be = b();
            document.getElementById('lista-alimentos').innerHTML = (be.foods || []).map(f => `
                <div class="card flex justify-between items-center mb-2">
                    <div>
                        <b class="text-orange-600 text-xs uppercase">${f.n}</b>
                        <p class="text-[8px] font-bold text-slate-400 mt-1 uppercase">${f.f} - ${f.r}</p>
                    </div>
                    ${f.al ? '<span class="bg-red-500 text-white text-[8px] px-2 py-1 rounded-full font-black">ALERTA</span>' : ''}
                </div>
            `).join('');
        }

        // --- SALUD ---
        function addMed() {
            const n = document.getElementById('m-n').value, h = document.getElementById('m-h').value, 
                  q = document.getElementById('m-q').value, d = document.getElementById('m-d').value;
            if(!n || !h || !q) return;
            b().meds.push({n, h: parseInt(h), q, d: parseInt(d) || 0, last: 0, start: Date.now()}); 
            closeModal('modal-add-med');
            save();
        }
        function renderSalud() {
            document.getElementById('lista-salud').innerHTML = b().meds.map((m, i) => {
                const diasPasados = Math.floor((Date.now() - m.start) / 86400000);
                const finalizado = m.d > 0 && diasPasados >= m.d;
                return `
                <div class="card flex justify-between items-center border-l-4 ${finalizado ? 'border-slate-300' : 'border-indigo-500'} mb-2 relative">
                    <button onclick="deleteMed(${i})" class="absolute top-2 right-4 text-slate-300 text-[8px] font-black uppercase">Borrar</button>
                    <div>
                        <b class="${finalizado ? 'text-slate-400' : 'text-indigo-600'} text-xs uppercase">${m.n}</b><br>
                        <span class="text-[9px] text-slate-400 font-bold uppercase">${m.q} cada ${m.h}h</span>
                        ${m.d > 0 ? `<p class="text-[8px] font-black mt-1 ${finalizado ? 'text-red-400' : 'text-indigo-400'}">DÍA ${diasPasados + 1} DE ${m.d}</p>` : ''}
                    </div>
                    ${!finalizado ? `<button onclick="darMed(${i})" class="bg-indigo-600 text-white px-5 py-2 rounded-xl font-black text-[10px]">TOMAR</button>` : '<span class="text-[8px] font-black text-slate-300">FIN</span>'}
                </div>`;
            }).join('');
        }
        function darMed(i) {
            const m = b().meds[i];
            m.last = Date.now();
            addLog(`💊 ${m.n} (${m.q})`);
        }
        function deleteMed(i) { if(confirm("¿Eliminar este tratamiento?")) { b().meds.splice(i, 1); save(); } }

        // --- CALENDARIO & PANEL EVENTOS ---
        function renderTimeline() {
            const be = b();
            const container = document.getElementById('timeline-eventos');
            let html = ""; const ahora = new Date();
            
            // Buscar eventos próximos 15 días
            const limite = new Date(); limite.setDate(limite.getDate() + 15);
            const proximasCitas = [];
            
            Object.keys(be.citas).forEach(fecha => {
                const fechaCita = new Date(fecha);
                if(fechaCita >= ahora && fechaCita <= limite) {
                    be.citas[fecha].forEach(c => proximasCitas.push({...c, fecha}));
                }
            });

            proximasCitas.sort((a,b) => new Date(a.fecha) - new Date(b.fecha));

            if(proximasCitas.length > 0) {
                html = proximasCitas.map(c => `
                    <div class="flex justify-between items-center bg-white/5 p-3 rounded-2xl">
                        <div class="flex items-center gap-3">
                            <div class="w-8 h-8 rounded-full bg-blue-500 flex items-center justify-center text-white text-[10px] font-black">
                                ${new Date(c.fecha).getDate()}
                            </div>
                            <p class="text-[10px] font-bold text-white">${c.txt}</p>
                        </div>
                        <span class="text-[10px] text-slate-500 font-black">${c.hora}</span>
                    </div>
                `).join('');
            } else {
                html = "<p class='text-center text-[9px] text-slate-600 uppercase py-2'>No hay citas próximas</p>";
            }
            container.innerHTML = html;
        }

        // --- COMPRA ---
        function renderCompra() {
            const container = document.getElementById('lista-compra-container');
            const items = document.getElementById('lista-compra-items');
            if(b().compra.length > 0) {
                container.classList.remove('hidden');
                items.innerHTML = b().compra.map((it, i) => `
                    <button onclick="checkCompra(${i})" class="bg-white px-3 py-1 rounded-full text-[10px] font-bold text-amber-700 shadow-sm border border-amber-200 uppercase">
                        ✓ ${it}
                    </button>
                `).join('');
            } else {
                container.classList.add('hidden');
            }
        }
        function addCompraUrgent() { const it = prompt("¿Qué comprar?"); if(it) { b().compra.push(it); save(); } }
        function checkCompra(i) { b().compra.splice(i, 1); save(); }

        // --- UTILIDADES ---
        function addLog(txt) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            b().logs.unshift({txt, hora: h, fecha: new Date().toLocaleDateString(), ts: Date.now()});
            save();
        }
        function addBibe(ml) { if(!ml) return; DATA.lastBibe = ml; addLog(`🍼 Bibe ${ml}ml`); closeModal('modal-bibe'); }
        function changeMonth(delta) { currentDate.setMonth(currentDate.getMonth() + delta); renderCal(); }
        function renderCal() {
            const be = b();
            const grid = document.getElementById('cal-grid');
            const titulo = document.getElementById('cal-mes-titulo');
            const y = currentDate.getFullYear(), m = currentDate.getMonth();
            titulo.innerText = new Intl.DateTimeFormat('es', { month: 'long', year: 'numeric' }).format(currentDate);
            grid.innerHTML = "";
            const start = new Date(y, m, 1).getDay();
            const adj = start === 0 ? 6 : start - 1;
            const days = new Date(y, m + 1, 0).getDate();
            for (let i = 0; i < adj; i++) grid.innerHTML += `<div></div>`;
            for (let i = 1; i <= days; i++) {
                const dStr = `${y}-${String(m + 1).padStart(2, '0')}-${String(i).padStart(2, '0')}`;
                const has = be.citas[dStr] && be.citas[dStr].length > 0;
                grid.innerHTML += `<div onclick="openDay('${dStr}')" class="cal-day bg-white text-slate-500">${i}${has ? '<div class="event-dot"></div>' : ''}</div>`;
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
        function showTab(id) {
            ['inicio', 'nutricion', 'salud', 'cal'].forEach(t => {
                document.getElementById('tab-' + t).classList.toggle('hidden', t !== id);
                document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id);
                document.getElementById('nav-' + t).classList.toggle('text-slate-300', t !== id);
            });
            window.scrollTo(0,0);
        }
        function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }
        function calcularEdad(f) { const d = Math.floor((new Date() - new Date(f)) / 86400000); return d < 31 ? `${d} DÍAS` : `${Math.floor(d/30.4)} MESES`; }
        function reiniciarApp() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        render();
    </script>
</body>
</html>
