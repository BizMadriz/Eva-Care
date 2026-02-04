<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>EvaCare V12.1</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 2rem; padding: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; position: relative; cursor: pointer; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 6px; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[1000] flex flex-col p-8 hidden">
        <div id="step-1" class="step-div flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold italic text-blue-600">EvaCare</h2>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold outline-none">
            <button onclick="irAStep(2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="step-div hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold">
            <button onclick="irAStep(3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="step-div hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Kg" class="p-5 bg-slate-100 rounded-3xl font-bold">
                <input id="setup-a" type="number" placeholder="cm" class="p-5 bg-slate-100 rounded-3xl font-bold">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black uppercase">Finalizar</button>
        </div>
    </div>

    <main id="tab-inicio" class="p-4 space-y-4">
        <header class="pt-4 pb-2 flex justify-between items-center">
            <span class="text-2xl font-extrabold text-slate-900 italic">EvaCare</span>
            <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm border border-slate-100"><i class="fa-solid fa-bars-staggered"></i></button>
        </header>

        <div class="bg-blue-600 p-6 rounded-[2.5rem] text-white shadow-xl">
            <div class="flex justify-between items-end">
                <div>
                    <h2 id="view-nombre" class="text-2xl font-extrabold italic leading-none">---</h2>
                    <p id="view-edad" class="text-[10px] font-black text-blue-200 mt-2 uppercase tracking-widest"></p>
                </div>
                <div class="text-right text-[10px] font-bold">
                    <p id="view-medidas-act"></p>
                    <p id="view-nacimiento" class="opacity-60 text-[8px] mt-1"></p>
                </div>
            </div>
        </div>

        <div class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
            <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4">Avisos y Próximos eventos</h3>
            <div id="timeline-eventos" class="space-y-4"></div>
        </div>

        <div id="lista-compra" class="hidden space-y-2"></div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] shadow-sm border">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] shadow-sm border">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] shadow-sm border">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-yellow-400 text-slate-900 rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border"><i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i><span class="text-[10px] font-black text-slate-400 uppercase">Pecho</span></button>
            <button onclick="openModal('modal-bibe')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border"><i class="fa-solid fa-bottle-water text-blue-500 text-xl"></i><span class="text-[10px] font-black text-slate-400 uppercase">Biberón</span></button>
        </div>

        <div id="diario-hoy" class="space-y-2 pt-2"></div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-6">
        <h2 class="text-2xl font-black italic mt-4 px-2">Agenda</h2>
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <button onclick="changeMonth(-1)" class="text-slate-400 p-2"><i class="fa-solid fa-chevron-left"></i></button>
                <h3 id="cal-mes-titulo" class="font-black uppercase text-xs tracking-widest">---</h3>
                <button onclick="changeMonth(1)" class="text-slate-400 p-2"><i class="fa-solid fa-chevron-right"></i></button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>
        <div class="space-y-4">
            <h4 class="text-[10px] font-black uppercase text-slate-400 px-2 tracking-widest">Eventos del día</h4>
            <div id="agenda-dia-lista" class="space-y-2"></div>
        </div>
    </main>

    <main id="tab-nutricion" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic mt-4 px-2">Alimentación</h2>
        <div class="card space-y-4">
            <input id="food-n" type="text" placeholder="Nuevo alimento..." class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none">
            <div class="grid grid-cols-2 gap-4">
                <button onclick="setReac('😋', this)" class="btn-reac bg-slate-50 p-4 rounded-2xl text-2xl">😋</button>
                <button onclick="setReac('🤢', this)" class="btn-reac bg-slate-50 p-4 rounded-2xl text-2xl">🤢</button>
            </div>
            <button onclick="saveFood()" class="w-full bg-orange-500 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button>
        </div>
        <div id="lista-alimentos" class="space-y-2 mt-4"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic mt-4 px-2">Salud</h2>
        <button onclick="openModal('modal-add-med')" class="w-full bg-indigo-600 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg"><p>Añadir Tratamiento</p><i class="fa-solid fa-pills text-xl"></i></button>
        <div id="lista-salud" class="space-y-3"></div>
    </main>

    <nav class="fixed bottom-6 left-6 right-6 h-16 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="text-blue-600"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="showTab('nutricion')" id="nav-nutricion" class="text-slate-300"><i class="fa-solid fa-carrot text-lg"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="text-slate-300"><i class="fa-solid fa-suitcase-medical text-lg"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="text-slate-300"><i class="fa-solid fa-calendar-day text-lg"></i></button>
    </nav>

    <div id="modal-cal" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 id="cal-modal-fecha" class="text-center font-black text-blue-600 uppercase text-xs"></h3>
            <input id="cal-ev-txt" type="text" placeholder="¿Qué ocurre?" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none">
            <input id="cal-ev-hora" type="time" class="w-full p-4 bg-slate-50 rounded-2xl font-bold">
            <button onclick="saveCita()" class="w-full bg-slate-900 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button>
            <button onclick="closeModal('modal-cal')" class="w-full text-slate-300 font-bold uppercase text-xs text-center">Cerrar</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[3000] flex justify-end"><div class="bg-white w-4/5 h-full p-8 flex flex-col"><h2 class="text-3xl font-extrabold italic mb-10">Ajustes</h2><div class="space-y-6"><div class="bg-slate-50 p-6 rounded-3xl"><p class="text-[10px] font-black text-blue-600 uppercase mb-2">Aviso tomas (h)</p><input id="set-plazo" type="number" class="w-full p-4 rounded-xl font-bold bg-white outline-none"></div><button onclick="saveSettings(); closeModal('modal-menu')" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button><button onclick="reiniciarApp()" class="w-full p-4 bg-red-50 text-red-600 rounded-2xl font-bold text-[10px] uppercase">Reiniciar App</button></div><button onclick="closeModal('modal-menu')" class="mt-auto text-slate-300 font-bold uppercase text-[10px] text-center p-4">Cerrar</button></div></div>
    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6"><div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4"><button id="btn-rep-bibe" onclick="addBibe(DATA.lastBibe)" class="w-full bg-blue-50 text-blue-600 p-6 rounded-3xl font-black uppercase text-xs">Repetir</button><input id="bibe-ml" type="number" placeholder="ml" class="w-full p-6 bg-slate-100 rounded-3xl text-center text-3xl font-black outline-none"><button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-6 rounded-3xl font-black">REGISTRAR</button><button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 text-center font-bold uppercase text-xs p-2">Cancelar</button></div></div>
    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-end"><div class="bg-white w-full p-10 rounded-t-[3rem] grid grid-cols-3 gap-4"><button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs text-center">IZQ</button><button onclick="addLog('🤱 Ambos'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-700 text-xs text-center">AMBOS</button><button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs text-center">DER</button></div></div>
    <div id="modal-add-med" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6"><div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4"><h3 class="text-xs font-black text-indigo-600 uppercase text-center">Nuevo Tratamiento</h3><input id="m-n" type="text" placeholder="Medicamento" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none"><div class="grid grid-cols-2 gap-2"><input id="m-q" type="text" placeholder="Dosis" class="p-4 bg-slate-50 rounded-2xl font-bold outline-none"><input id="m-h" type="number" placeholder="Cada h" class="p-4 bg-slate-50 rounded-2xl font-bold outline-none"></div><input id="m-d" type="number" placeholder="Días de duración" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none"><button onclick="addMed()" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button><button onclick="closeModal('modal-add-med')" class="w-full text-slate-300 font-bold uppercase text-xs text-center">Cerrar</button></div></div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V12')) || { idx: 0, bebes: [], lastBibe: 0, plazo: 3 };
        let currentDate = new Date();
        let selectedDate = new Date().toISOString().split('T')[0];
        const b = () => DATA.bebes[DATA.idx];

        function save() { localStorage.setItem('EVA_CARE_V12', JSON.stringify(DATA)); render(); }

        function render() {
            if (!DATA.bebes || DATA.bebes.length === 0) { document.getElementById('setup-wizard').classList.remove('hidden'); return; }
            const be = b();
            document.getElementById('view-nombre').innerText = be.n;
            document.getElementById('view-edad').innerText = calcularEdad(be.f);
            document.getElementById('view-medidas-act').innerText = `${be.curP || be.p}kg · ${be.curA || be.a}cm`;
            document.getElementById('set-plazo').value = DATA.plazo;
            
            renderTimeline();
            renderLogs();
            renderSalud();
            renderFoods();
            renderCal();
            renderCompra();
            renderAgendaDia();
        }

        // TOMAS (PECHO + BIBE REINICIAN)
        function renderTimeline() {
            const be = b();
            const container = document.getElementById('timeline-eventos');
            let html = ""; const ahora = Date.now();
            
            const ultToma = be.logs.find(l => l.txt.includes('Bibe') || l.txt.includes('Pecho'));
            if(ultToma) {
                const rest = (DATA.plazo * 3600000) - (ahora - ultToma.ts);
                html += `<div class="flex justify-between items-center bg-white/5 p-4 rounded-2xl"><div class="flex items-center gap-3 text-blue-400"><i class="fa-solid fa-baby"></i><p class="text-[10px] font-black uppercase">Próxima Toma</p></div><span class="text-xl font-black ${rest < 0 ? 'text-red-500 animate-pulse' : 'text-white'}">${formatTime(rest)}</span></div>`;
            }

            (be.meds || []).forEach(m => {
                if(m.last > 0) {
                    const restMed = (m.last + m.h*3600000) - ahora;
                    html += `<div class="flex justify-between items-center bg-white/5 p-4 rounded-2xl mt-2"><div class="flex items-center gap-3 text-indigo-400"><i class="fa-solid fa-pills"></i><p class="text-[10px] font-black uppercase">${m.n}</p></div><span class="text-sm font-black ${restMed < 0 ? 'text-red-400 animate-pulse' : 'text-slate-400'}">${formatTime(restMed)}</span></div>`;
                }
            });

            container.innerHTML = html || "<p class='text-center text-[9px] text-slate-600 uppercase py-4'>Todo al día</p>";
        }

        // COMPRA (REPARADO)
        function addCompraUrgent() {
            const item = prompt("¿Qué necesitas comprar?", "Pañales");
            if(!item) return;
            if(!b().compra) b().compra = [];
            b().compra.push(item);
            save();
        }
        function renderCompra() {
            const list = document.getElementById('lista-compra');
            const be = b();
            if(!be.compra || be.compra.length === 0) { list.classList.add('hidden'); return; }
            list.classList.remove('hidden');
            list.innerHTML = be.compra.map((item, i) => `
                <div class="bg-yellow-100 p-3 rounded-xl flex justify-between items-center border border-yellow-200">
                    <span class="text-[10px] font-black text-yellow-800 uppercase">🛒 ${item}</span>
                    <button onclick="deleteCompra(${i})" class="text-yellow-600"><i class="fa-solid fa-check-circle"></i></button>
                </div>`).join('');
        }
        function deleteCompra(i) { b().compra.splice(i,1); save(); }

        // AGENDA (REPARADO)
        function renderAgendaDia() {
            const list = document.getElementById('agenda-dia-lista');
            const eventos = b().citas[selectedDate] || [];
            list.innerHTML = eventos.length ? eventos.map((c, i) => `
                <div class="card flex justify-between items-center">
                    <div><b class="text-xs uppercase">${c.txt}</b><p class="text-[10px] text-blue-500 font-bold">${c.hora}</p></div>
                    <button onclick="deleteCita(${i})" class="text-slate-200"><i class="fa-solid fa-trash"></i></button>
                </div>`).join('') : "<p class='text-center text-[9px] text-slate-300 uppercase py-4'>No hay eventos este día</p>";
        }
        function openDay(f) { selectedDate = f; renderCal(); renderAgendaDia(); openModal('modal-cal'); }
        function saveCita() {
            const t = document.getElementById('cal-ev-txt').value, h = document.getElementById('cal-ev-hora').value;
            if(!t || !h) return; if(!b().citas[selectedDate]) b().citas[selectedDate] = [];
            b().citas[selectedDate].push({txt: t, hora: h}); 
            document.getElementById('cal-ev-txt').value = "";
            save(); closeModal('modal-cal');
        }
        function deleteCita(i) { b().citas[selectedDate].splice(i,1); save(); }

        // FUNCIONES GENERALES
        function addLog(txt) {
            b().logs.unshift({txt, hora: new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}), fecha: new Date().toLocaleDateString(), ts: Date.now()});
            save();
        }
        function renderLogs() {
            const hoy = new Date().toLocaleDateString();
            document.getElementById('diario-hoy').innerHTML = b().logs.filter(l => l.fecha === hoy).map(l => `
                <div class="bg-white p-4 rounded-2xl flex justify-between items-center shadow-sm border border-slate-50 mb-2">
                    <span class="text-xs font-bold text-slate-700">${l.txt}</span>
                    <span class="text-[9px] font-bold text-slate-300">${l.hora}</span>
                </div>`).join('');
        }
        function renderCal() {
            const grid = document.getElementById('cal-grid'), tit = document.getElementById('cal-mes-titulo');
            const y = currentDate.getFullYear(), m = currentDate.getMonth();
            tit.innerText = new Intl.DateTimeFormat('es', { month: 'long', year: 'numeric' }).format(currentDate);
            grid.innerHTML = "";
            const start = (new Date(y, m, 1).getDay() + 6) % 7;
            const days = new Date(y, m + 1, 0).getDate();
            for (let i = 0; i < start; i++) grid.innerHTML += `<div></div>`;
            for (let i = 1; i <= days; i++) {
                const dStr = `${y}-${String(m + 1).padStart(2, '0')}-${String(i).padStart(2, '0')}`;
                const has = b().citas[dStr] && b().citas[dStr].length > 0;
                grid.innerHTML += `<div onclick="openDay('${dStr}')" class="cal-day ${selectedDate === dStr ? 'bg-blue-600 text-white shadow-lg' : 'bg-white text-slate-400'}">${i}${has ? '<div class="event-dot"></div>' : ''}</div>`;
            }
        }
        function showTab(id) { ['inicio', 'nutricion', 'salud', 'cal'].forEach(t => { document.getElementById('tab-' + t).classList.toggle('hidden', t !== id); document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id); document.getElementById('nav-' + t).classList.toggle('text-slate-300', t !== id); }); }
        function openModal(id) { document.getElementById(id).classList.remove('hidden'); if(id==='modal-cal') document.getElementById('cal-modal-fecha').innerText = selectedDate; }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }
        function irAStep(n) { document.querySelectorAll('.step-div').forEach(d => d.classList.add('hidden')); document.getElementById('step-' + n).classList.remove('hidden'); }
        function finishSetup() { const n=document.getElementById('setup-n').value, f=document.getElementById('setup-f').value, p=document.getElementById('setup-p').value, a=document.getElementById('setup-a').value; if(!n||!f) return; DATA.bebes.push({n,f,p,a,logs:[],meds:[],compra:[],citas:{},foods:[]}); DATA.idx=DATA.bebes.length-1; save(); document.getElementById('setup-wizard').classList.add('hidden'); }
        function formatTime(ms) { const s=Math.abs(ms); return (ms<0?'+':'')+`${Math.floor(s/3600000)}h ${Math.floor((s%3600000)/60000)}m`; }
        function calcularEdad(f) { const d=Math.floor((new Date()-new Date(f))/86400000); return d<31?`${d} DÍAS`:`${Math.floor(d/30.4)} MESES`; }
        function addBibe(ml) { if(!ml) return; DATA.lastBibe=ml; addLog(`🍼 Bibe ${ml}ml`); closeModal('modal-bibe'); }
        function addMed() { const n=document.getElementById('m-n').value, h=document.getElementById('m-h').value, q=document.getElementById('m-q').value, d=document.getElementById('m-d').value; if(!n||!h) return; b().meds.push({n,h:parseInt(h),q,d,last:0}); save(); closeModal('modal-add-med'); }
        function changeMonth(d) { currentDate.setMonth(currentDate.getMonth()+d); renderCal(); }
        function saveSettings() { DATA.plazo=document.getElementById('set-plazo').value; save(); }
        function reiniciarApp() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }
        // Nutrición/Salud simplificados para asegurar que el código entre entero
        function setReac(r,btn) { foodReacSelected=r; document.querySelectorAll('.btn-reac').forEach(b=>b.classList.remove('bg-yellow-100')); btn.classList.add('bg-yellow-100'); }
        function saveFood() { const n=document.getElementById('food-n').value; if(!n||!foodReacSelected) return; b().foods.unshift({n,r:foodReacSelected,f:new Date().toLocaleDateString()}); addLog(`🍴 Probó: ${n}`); save(); }
        function renderFoods() { document.getElementById('lista-alimentos').innerHTML = (b().foods||[]).map(f=>`<div class="card flex justify-between mb-2"><b class="text-xs uppercase">${f.n}</b><span>${f.r}</span></div>`).join(''); }
        function renderSalud() { document.getElementById('lista-salud').innerHTML = (b().meds||[]).map((m,i)=>`<div class="card flex justify-between mb-2"><div><b class="text-xs">${m.n}</b><p class="text-[9px] uppercase">${m.q} cada ${m.h}h</p></div><button onclick="darMed(${i})" class="bg-indigo-600 text-white px-4 py-2 rounded-xl text-[10px]">TOMAR</button></div>`).join(''); }
        function darMed(i) { const m=b().meds[i]; const ahora=Date.now(); if(m.last>0 && (ahora-m.last)<(m.h*3600000)) { if(!confirm("Aviso: Falta tiempo. ¿Dar dosis?")) return; } m.last=ahora; addLog(`💊 ${m.n}`); }

        render();
        setInterval(renderTimeline, 30000);
    </script>
</body>
</html>
