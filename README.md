<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .hidden { display: none !important; }
        .tab-active { color: #2563eb; border-bottom: 3px solid #2563eb; font-weight: 900; }
        .card { background: white; border-radius: 1.5rem; padding: 1.25rem; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); }
        .btn-action { transition: all 0.2s; }
        .btn-action:active { transform: scale(0.95); }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 font-sans selection:bg-blue-100">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[200] flex flex-col p-8 transition-all duration-300">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-blue-600 italic">¿Cómo se llama tu bebé?</h2>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
            <button onclick="nextStep(2)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">CONTINUAR</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-blue-600 italic">¿Cuándo nació?</h2>
            <input id="setup-f" type="date" class="w-full p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
            <button onclick="nextStep(3)" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">CONTINUAR</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-black text-blue-600 italic">Peso y Talla al nacer</h2>
            <div class="flex gap-4">
                <input id="setup-p" type="number" step="0.01" placeholder="Kg" class="w-1/2 p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
                <input id="setup-a" type="number" placeholder="cm" class="w-1/2 p-4 border-b-4 border-blue-500 text-2xl font-bold focus:outline-none">
            </div>
            <button onclick="finishSetup()" class="bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">FINALIZAR</button>
        </div>
    </div>

    <header class="bg-white p-4 shadow-sm sticky top-0 z-50 flex justify-between items-start">
        <div onclick="openModal('modal-menu')" class="cursor-pointer">
            <h1 id="view-nombre" class="text-xl font-black text-blue-600 italic leading-none">Cargando...</h1>
            <div class="mt-1 space-y-0.5">
                <p id="view-edad" class="text-[10px] font-bold text-slate-500 uppercase tracking-tight"></p>
                <p id="view-nacimiento" class="text-[9px] text-slate-400"></p>
                <p id="view-actual" class="text-[9px] font-black text-blue-400 uppercase italic"></p>
            </div>
        </div>
        <button onclick="openModal('modal-menu')" class="p-3 bg-slate-100 rounded-2xl text-slate-600"><i class="fa-solid fa-bars-staggered"></i></button>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4">
        <div class="card bg-gradient-to-br from-blue-600 to-blue-700 text-white">
            <h3 class="text-[10px] font-black uppercase opacity-60 mb-3 tracking-widest">Próximos Eventos</h3>
            <div class="space-y-3">
                <div class="flex items-center justify-between">
                    <span class="text-xs font-bold">Próxima toma:</span>
                    <span id="countdown-toma" class="text-xl font-black">--:--</span>
                </div>
                <div id="lista-eventos-hoy" class="text-[11px] space-y-1 font-medium border-t border-white/20 pt-2">
                    </div>
            </div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-500 hidden">
            <h3 class="text-[10px] font-black text-orange-500 uppercase mb-2">Lista de Compra</h3>
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="space-y-3 pt-2">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="openModal('modal-pecho')" class="bg-pink-500 text-white py-6 rounded-3xl font-black btn-action shadow-md uppercase">Pecho</button>
                <button onclick="openModal('modal-bibe')" class="bg-blue-600 text-white py-6 rounded-3xl font-black btn-action shadow-md uppercase">Bibe</button>
            </div>
            <div class="grid grid-cols-4 gap-2">
                <button onclick="addLog('💦 Pis')" class="bg-white border-2 border-cyan-100 p-4 rounded-2xl font-black text-cyan-600 text-xs btn-action">PIS</button>
                <button onclick="addLog('💩 Caca')" class="bg-white border-2 border-orange-100 p-4 rounded-2xl font-black text-orange-800 text-xs btn-action">CACA</button>
                <button onclick="addLog('✨ Aseo')" class="bg-white border-2 border-purple-100 p-4 rounded-2xl font-black text-purple-600 text-xs btn-action">BAÑO</button>
                <button onclick="addCompraUrgent()" class="bg-orange-600 text-white p-4 rounded-2xl flex items-center justify-center btn-action"><i class="fa-solid fa-cart-plus"></i></button>
            </div>
        </div>

        <div class="space-y-2">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">Registro del día</h3>
            <div id="diario-hoy" class="bg-white rounded-3xl p-4 text-[11px] font-bold text-slate-500 space-y-2 border"></div>
        </div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-4">
        <div class="card">
            <div id="cal-header" class="flex justify-between items-center mb-4">
                <h3 id="cal-mes" class="font-black text-blue-600 uppercase"></h3>
                <button onclick="addCita()" class="bg-blue-100 text-blue-600 px-3 py-1 rounded-lg text-[10px] font-black">+ CITA</button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1 text-center font-bold text-xs"></div>
        </div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="card">
            <h3 class="text-sm font-black text-indigo-600 uppercase mb-3">Añadir Tratamiento</h3>
            <div class="space-y-3">
                <input id="m-n" type="text" placeholder="Nombre Medicina" class="w-full p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                <div class="flex gap-2">
                    <input id="m-ds" type="text" placeholder="Dosis" class="w-1/2 p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                    <input id="m-h" type="number" placeholder="Horas" class="w-1/2 p-3 bg-slate-50 rounded-xl text-xs font-bold border">
                </div>
                <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-3 rounded-xl font-black text-xs">EMPEZAR TRATAMIENTO</button>
            </div>
        </div>
        <div id="lista-salud" class="space-y-3"></div>
    </main>

    <nav class="fixed bottom-0 left-0 right-0 bg-white border-t flex justify-around p-2 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="flex flex-col items-center p-2 text-blue-600"><i class="fa-solid fa-house text-lg"></i><span class="text-[9px] font-black uppercase mt-1">Inicio</span></button>
        <button onclick="showTab('cal')" id="nav-cal" class="flex flex-col items-center p-2 text-slate-400"><i class="fa-solid fa-calendar text-lg"></i><span class="text-[9px] font-black uppercase mt-1">Citas</span></button>
        <button onclick="showTab('salud')" id="nav-salud" class="flex flex-col items-center p-2 text-slate-400"><i class="fa-solid fa-stethoscope text-lg"></i><span class="text-[9px] font-black uppercase mt-1">Salud</span></button>
    </nav>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[300] flex justify-end">
        <div class="bg-white w-4/5 h-full p-6 space-y-8 animate-slide-in">
            <h2 class="text-2xl font-black text-blue-600 italic">Eva Care Menu</h2>
            <div class="space-y-4">
                <button onclick="alert('Editar datos (próximamente)')" class="w-full text-left p-4 bg-slate-50 rounded-2xl font-bold flex items-center justify-between">Editar Bebé <i class="fa-solid fa-chevron-right text-xs"></i></button>
                <button onclick="addNewBebeWizard()" class="w-full text-left p-4 bg-slate-50 rounded-2xl font-bold flex items-center justify-between">Añadir otro Bebé <i class="fa-solid fa-baby text-xs"></i></button>
                <button onclick="reiniciarApp()" class="w-full text-left p-4 bg-red-50 text-red-600 rounded-2xl font-bold italic">Borrar todo y reiniciar</button>
            </div>
            <button onclick="closeModal('modal-menu')" class="w-full py-4 text-slate-400 font-black uppercase">Cerrar</button>
        </div>
    </div>

    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[300] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4 text-center">
            <h3 class="text-xl font-black">Registro de Biberón</h3>
            <div id="rep-bibe-container" class="hidden">
                <button onclick="addBibe(DATA.lastBibe)" id="btn-rep-bibe" class="w-full bg-blue-100 text-blue-600 p-6 rounded-3xl font-black mb-2 uppercase">Repetir última (${DATA.lastBibe}ml)</button>
                <p class="text-[10px] font-bold text-slate-300 mb-4">O INTRODUCE UNA NUEVA</p>
            </div>
            <input id="bibe-ml" type="number" placeholder="Cantidad ml" class="w-full p-4 bg-slate-100 rounded-2xl text-center text-xl font-black mb-4">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">REGISTRAR</button>
            <button onclick="closeModal('modal-bibe')" class="text-slate-300 font-bold uppercase mt-4">Cancelar</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V5')) || {
            activeIdx: 0,
            bebes: [],
            lastBibe: 0,
            intervaloTomas: 3
        };

        const b = () => DATA.bebes[DATA.activeIdx];

        function save() {
            localStorage.setItem('EVA_CARE_V5', JSON.stringify(DATA));
            render();
        }

        // --- WIZARD FLOW ---
        function nextStep(s) {
            document.getElementById(`step-${s-1}`).classList.add('hidden');
            document.getElementById(`step-${s}`).classList.remove('hidden');
        }

        function finishSetup() {
            const nuevo = {
                n: document.getElementById('setup-n').value || 'Bebé',
                f: document.getElementById('setup-f').value,
                p: document.getElementById('setup-p').value,
                a: document.getElementById('setup-a').value,
                pAct: document.getElementById('setup-p').value,
                aAct: document.getElementById('setup-a').value,
                logs: [],
                meds: [],
                compra: [],
                eventos: []
            };
            DATA.bebes.push(nuevo);
            DATA.activeIdx = DATA.bebes.length - 1;
            document.getElementById('setup-wizard').classList.add('hidden');
            save();
        }

        // --- RENDER ENGINE ---
        function render() {
            if (DATA.bebes.length === 0) return;
            const be = b();

            // Cabecera
            document.getElementById('view-nombre').innerText = be.n;
            const edadStr = calcularEdad(be.f);
            document.getElementById('view-edad').innerText = edadStr;
            document.getElementById('view-nacimiento').innerText = `Nacimiento: ${be.p}kg | ${be.a}cm`;
            document.getElementById('view-actual').innerText = `Último: ${be.pAct}kg | ${be.aAct}cm`;

            // Compra
            const cardC = document.getElementById('card-compra');
            const listC = document.getElementById('lista-compra');
            if(be.compra.length > 0) {
                cardC.classList.remove('hidden');
                listC.innerHTML = be.compra.map((it, i) => `
                    <div class="flex justify-between items-center bg-orange-50 p-3 rounded-xl border border-orange-100">
                        <span class="text-xs font-bold text-orange-800">${it}</span>
                        <button onclick="checkCompra(${i})" class="w-6 h-6 bg-white border-2 border-orange-400 rounded-full flex items-center justify-center text-orange-600">✓</button>
                    </div>
                `).join('');
            } else { cardC.classList.add('hidden'); }

            // Diario Hoy
            const dH = document.getElementById('diario-hoy');
            const hoy = new Date().toLocaleDateString();
            const logsHoy = be.logs.filter(l => l.d === hoy);
            dH.innerHTML = logsHoy.map(l => `
                <div class="flex justify-between border-b border-slate-50 pb-1">
                    <span>${l.t}</span>
                    <span class="text-[9px] text-slate-300 font-black italic">${l.h}</span>
                </div>
            `).join('') || "<p class='italic opacity-40 text-center py-4'>Sin actividad registrada hoy</p>";

            // Modal Bibe repeat
            if(DATA.lastBibe > 0) {
                document.getElementById('rep-bibe-container').classList.remove('hidden');
                document.getElementById('btn-rep-bibe').innerText = `REPETIR ÚLTIMA (${DATA.lastBibe}ml)`;
            }

            // Lista Salud
            document.getElementById('lista-salud').innerHTML = be.meds.map((m, i) => `
                <div class="card flex justify-between items-center">
                    <div><b class="text-indigo-600">${m.n}</b><br><span class="text-[10px] text-slate-400">${m.ds} cada ${m.h}h</span></div>
                    <button onclick="addLog('💊 Toma: ${m.n}')" class="bg-indigo-100 text-indigo-600 px-4 py-2 rounded-xl font-black text-[10px]">TOMAR</button>
                </div>
            `).join('');

            renderCalendar();
            updateCountdown();
        }

        function calcularEdad(fecha) {
            const nace = new Date(fecha);
            const hoy = new Date();
            const diffTime = Math.abs(hoy - nace);
            const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
            
            if (diffDays < 31) return `${diffDays} DÍAS`;
            const meses = Math.floor(diffDays / 30.41);
            return `${meses} MESES`;
        }

        // --- ACCIONES ---
        function addLog(t) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            const d = new Date().toLocaleDateString();
            b().logs.unshift({t, h, d, ts: Date.now()});
            save();
        }

        function addBibe(ml) {
            if(!ml) return;
            DATA.lastBibe = ml;
            addLog(`🍼 Biberón ${ml}ml`);
            closeModal('modal-bibe');
        }

        function addCompraUrgent() {
            const it = prompt("¿Qué falta?");
            if(it) { b().compra.push(it); save(); }
        }

        function checkCompra(i) {
            b().compra.splice(i, 1);
            save();
        }

        function addMed() {
            const n = document.getElementById('m-n').value;
            const ds = document.getElementById('m-ds').value;
            const h = document.getElementById('m-h').value;
            if(n && h) {
                b().meds.push({n, ds, h});
                save();
            }
        }

        // --- NAVEGACIÓN ---
        function showTab(id) {
            ['inicio', 'cal', 'salud'].forEach(t => {
                document.getElementById('tab-' + t).classList.toggle('hidden', t !== id);
                document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id);
                document.getElementById('nav-' + t).classList.toggle('text-slate-400', t !== id);
            });
        }

        function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }

        function reiniciarApp() {
            if(confirm("¿Estás seguro de borrar todo? No hay vuelta atrás.")) {
                localStorage.clear();
                location.reload();
            }
        }

        function renderCalendar() {
            const grid = document.getElementById('cal-grid');
            const mesHeader = document.getElementById('cal-mes');
            const hoy = new Date();
            mesHeader.innerText = hoy.toLocaleDateString('es', {month: 'long', year: 'numeric'});
            grid.innerHTML = "";
            const total = new Date(hoy.getFullYear(), hoy.getMonth()+1, 0).getDate();
            for(let i=1; i<=total; i++) {
                grid.innerHTML += `<div class="p-3 rounded-xl ${i==hoy.getDate()?'bg-blue-600 text-white shadow-lg':'bg-white border'}">${i}</div>`;
            }
        }

        function updateCountdown() {
            const ultToma = b().logs.find(l => l.t.includes('🍼') || l.t.includes('🤱'));
            if(ultToma) {
                const diff = Date.now() - ultToma.ts;
                const total = DATA.intervaloTomas * 3600000;
                const rest = total - diff;
                if(rest > 0) {
                    const min = Math.floor(rest / 60000);
                    document.getElementById('countdown-toma').innerText = `${Math.floor(min/60)}h ${min%60}m`;
                } else {
                    document.getElementById('countdown-toma').innerText = "¡TOCA YA!";
                }
            }
        }

        // --- INICIO ---
        if(DATA.bebes.length === 0) {
            document.getElementById('setup-wizard').classList.remove('hidden');
        } else {
            document.getElementById('setup-wizard').classList.add('hidden');
            render();
        }

        setInterval(updateCountdown, 60000);
    </script>
</body>
</html>
