<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .tab-active { color: #2563eb; border-bottom: 3px solid #2563eb; font-weight: 900; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 1.2rem; padding: 1rem; box-shadow: 0 2px 8px rgba(0,0,0,0.05); }
        button:active { transform: scale(0.96); opacity: 0.9; }
    </style>
</head>
<body class="bg-slate-50 pb-24 font-sans text-slate-900">

    <header class="bg-white p-4 shadow-sm sticky top-0 z-50 flex justify-between items-center">
        <div onclick="openModal('modal-bebes')" class="cursor-pointer">
            <h1 class="text-xl font-black italic text-blue-600">Eva Care Pro <i class="fa-solid fa-chevron-down text-[10px]"></i></h1>
            <div id="header-info" class="mt-1">
                <p id="h-nombre" class="text-sm font-bold text-slate-800 leading-none">Cargando...</p>
                <p id="h-stats" class="text-[10px] text-blue-500 font-bold uppercase mt-1 tracking-tight"></p>
            </div>
        </div>
        <button onclick="openModal('modal-config')" class="p-3 bg-slate-100 rounded-2xl text-slate-500">
            <i class="fa-solid fa-gear"></i>
        </button>
    </header>

    <main class="p-4 space-y-4">
        <section class="grid grid-cols-2 gap-3">
            <div class="card border-l-4 border-blue-500">
                <h4 class="text-[9px] font-black text-blue-500 uppercase mb-2">Próximas Citas</h4>
                <div id="home-agenda" class="text-[10px] space-y-1 font-bold h-16 overflow-y-auto"></div>
            </div>
            <div class="card border-l-4 border-orange-500">
                <h4 class="text-[9px] font-black text-orange-500 uppercase mb-2">Lista Compra</h4>
                <div id="home-compra" class="text-[10px] space-y-1 h-16 overflow-y-auto"></div>
            </div>
        </section>

        <nav class="flex bg-white rounded-2xl shadow-sm border text-[10px] font-black overflow-hidden">
            <button id="btn-diario" onclick="showTab('diario')" class="flex-1 p-4 tab-active">DIARIO</button>
            <button id="btn-cal" onclick="showTab('cal')" class="flex-1 p-4 text-slate-400">CALENDARIO</button>
            <button id="btn-salud" onclick="showTab('salud')" class="flex-1 p-4 text-slate-400">SALUD</button>
        </nav>

        <div id="sec-diario" class="space-y-4">
            <button onclick="openModal('modal-pecho')" class="w-full bg-pink-500 text-white py-8 rounded-[30px] font-black text-xl shadow-lg">TOMA DE PECHO</button>
            
            <div class="grid grid-cols-4 gap-2">
                <button onclick="addBiberon()" class="col-span-3 bg-blue-600 text-white p-5 rounded-2xl font-bold">🍼 BIBERÓN</button>
                <button onclick="repeatBibi()" class="bg-blue-100 text-blue-600 rounded-2xl text-[10px] font-black">REP.</button>
                <button onclick="addSimpleLog('💦 Pis')" class="bg-white border p-5 rounded-2xl font-bold text-cyan-600">PIS</button>
                <button onclick="addSimpleLog('💩 Caca')" class="bg-white border p-5 rounded-2xl font-bold text-orange-800">CACA</button>
                <button onclick="addCompraItem()" class="bg-orange-600 text-white rounded-2xl flex items-center justify-center"><i class="fa-solid fa-cart-plus"></i></button>
                <button onclick="showTab('salud')" class="bg-indigo-600 text-white rounded-2xl flex items-center justify-center"><i class="fa-solid fa-pills"></i></button>
            </div>
            
            <div id="log-list" class="bg-white p-4 rounded-2xl text-[10px] text-slate-400 font-bold uppercase space-y-2 border border-dashed"></div>
        </div>

        <div id="sec-cal" class="hidden space-y-4">
            <div class="card">
                <div id="cal-mes" class="text-center font-black text-blue-600 uppercase text-xs mb-4"></div>
                <div id="cal-grid" class="grid grid-cols-7 gap-2 text-center text-[11px] font-bold"></div>
            </div>
            <div id="cal-eventos-lista" class="space-y-2"></div>
        </div>

        <div id="sec-salud" class="hidden space-y-4">
            <button onclick="openModal('modal-add-med')" class="w-full bg-indigo-600 text-white p-4 rounded-2xl font-bold">+ AÑADIR MEDICINA</button>
            <div id="salud-list" class="space-y-2"></div>
        </div>
    </main>

    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/70 z-[100] flex items-end">
        <div class="bg-white w-full p-8 rounded-t-[40px] grid grid-cols-3 gap-4">
            <button onclick="addSimpleLog('🤱 Pecho IZQ'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-2xl font-black text-pink-600">IZQ</button>
            <button onclick="addSimpleLog('🤱 Pecho AMBOS'); closeModal('modal-pecho')" class="bg-pink-200 p-8 rounded-2xl font-black text-pink-700 text-xs">AMBOS</button>
            <button onclick="addSimpleLog('🤱 Pecho DER'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-2xl font-black text-pink-600">DER</button>
            <button onclick="closeModal('modal-pecho')" class="col-span-3 text-slate-300 font-bold p-2">CERRAR</button>
        </div>
    </div>

    <div id="modal-config" class="hidden fixed inset-0 bg-black/70 z-[100] flex justify-end">
        <div class="bg-white w-4/5 h-full p-6 overflow-y-auto">
            <h2 class="text-xl font-black mb-6 text-blue-600">Ficha del Bebé</h2>
            <div class="space-y-4">
                <input id="in-n" type="text" placeholder="Nombre" class="w-full p-3 border rounded-xl font-bold">
                <input id="in-f" type="date" class="w-full p-3 border rounded-xl">
                <div class="flex gap-2">
                    <input id="in-p" type="text" placeholder="Peso (kg)" class="w-1/2 p-3 border rounded-xl">
                    <input id="in-a" type="text" placeholder="Altura (cm)" class="w-1/2 p-3 border rounded-xl">
                </div>
                <button onclick="saveCurrentBebe()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold">GUARDAR DATOS</button>
                <hr class="my-6">
                <button onclick="borrarTodo()" class="w-full text-red-500 text-[10px] font-bold uppercase mt-20">✕ REINICIAR TODA LA APP</button>
            </div>
            <button onclick="closeModal('modal-config')" class="mt-10 w-full text-slate-300 font-bold uppercase text-xs">Cerrar</button>
        </div>
    </div>

    <div id="modal-bebes" class="hidden fixed inset-0 bg-black/70 z-[110] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-3xl p-6 space-y-4">
            <h3 class="font-black text-center text-blue-600 uppercase text-sm">Mis Bebés</h3>
            <div id="bebes-lista" class="space-y-2"></div>
            <button onclick="addNewBebe()" class="w-full bg-blue-50 text-blue-600 p-4 rounded-xl font-bold">+ AÑADIR OTRO BEBÉ</button>
            <button onclick="closeModal('modal-bebes')" class="w-full text-slate-300 font-bold uppercase text-xs">Cancelar</button>
        </div>
    </div>

    <div id="modal-add-med" class="hidden fixed inset-0 bg-black/70 z-[100] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-3xl p-6 space-y-4">
            <h3 class="font-black text-center text-indigo-600">Nueva Medicina</h3>
            <input id="m-nombre" type="text" placeholder="Ej: Apiretal" class="w-full p-3 border rounded-xl font-bold">
            <input id="m-dosis" type="text" placeholder="Ej: 0.5ml" class="w-full p-3 border rounded-xl">
            <button onclick="saveNewMed()" class="w-full bg-indigo-600 text-white p-4 rounded-xl font-bold">AÑADIR</button>
            <button onclick="closeModal('modal-add-med')" class="w-full text-slate-300 font-bold uppercase text-xs">Cancelar</button>
        </div>
    </div>

    <script>
        // --- BASE DE DATOS LOCAL ---
        let DB = JSON.parse(localStorage.getItem('EVA_CARE_VFINAL')) || {
            idx: 0,
            bebes: [{
                n: 'Mi Bebé', 
                f: '', 
                p: '', 
                a: '', 
                logs: [], 
                meds: [], 
                compra: [], 
                eventos: {}
            }],
            lastBibi: ''
        };

        const curr = () => DB.bebes[DB.idx];

        function save() {
            localStorage.setItem('EVA_CARE_VFINAL', JSON.stringify(DB));
            render();
        }

        function render() {
            const b = curr();
            
            // Header
            document.getElementById('h-nombre').innerText = b.n;
            if(b.f) {
                const nac = new Date(b.f);
                const dias = Math.floor((new Date() - nac) / 86400000);
                document.getElementById('h-stats').innerText = `${dias} DÍAS • ${b.p || '--'}KG • ${b.a || '--'}CM`;
            } else {
                document.getElementById('h-stats').innerText = "Pulsa el engranaje para configurar";
            }

            // Home - Agenda
            const hAg = document.getElementById('home-agenda');
            hAg.innerHTML = "";
            const hoyIso = new Date().toISOString().split('T')[0];
            const evHoy = b.eventos[hoyIso] || [];
            evHoy.forEach(e => hAg.innerHTML += `<div class="text-blue-600 truncate">● ${e}</div>`);
            if(hAg.innerHTML === "") hAg.innerHTML = "<span class='text-slate-300'>Sin citas hoy</span>";

            // Home - Compra
            const hComp = document.getElementById('home-compra');
            hComp.innerHTML = b.compra.map((it, i) => `
                <div class="flex justify-between border-b pb-0.5">
                    <span class="truncate">${it}</span>
                    <button onclick="deleteCompra(${i})" class="text-green-500 font-bold">✓</button>
                </div>
            `).join('') || "<span class='text-slate-300'>Vacía</span>";

            // Diario - Logs
            document.getElementById('log-list').innerHTML = b.logs.slice(0,6).map(l => `
                <div class="flex justify-between">
                    <span>${l.t}</span>
                    <span class="text-slate-300">${l.h}</span>
                </div>
            `).join('') || "No hay registros hoy";

            // Salud
            document.getElementById('salud-list').innerHTML = b.meds.map((m, i) => `
                <div class="card flex justify-between items-center text-xs">
                    <div><b class="text-slate-800">${m.n}</b><br><span class="text-slate-400">${m.d}</span></div>
                    <div class="flex gap-2">
                        <button onclick="addSimpleLog('💊 Tomó ${m.n}')" class="bg-indigo-600 text-white px-3 py-2 rounded-lg font-bold">DAR</button>
                        <button onclick="deleteMed(${i})" class="bg-slate-100 text-red-400 px-3 py-2 rounded-lg"><i class="fa-solid fa-trash"></i></button>
                    </div>
                </div>
            `).join('');

            // Bebés Lista (Modal)
            document.getElementById('bebes-lista').innerHTML = DB.bebes.map((bebe, i) => `
                <button onclick="switchBebe(${i})" class="w-full p-4 rounded-xl font-bold ${i === DB.idx ? 'bg-blue-600 text-white shadow-md' : 'bg-slate-100 text-slate-600'}">
                    ${bebe.n}
                </button>
            `).join('');

            // Sincronizar inputs config
            document.getElementById('in-n').value = b.n;
            document.getElementById('in-f').value = b.f;
            document.getElementById('in-p').value = b.p;
            document.getElementById('in-a').value = b.a;

            renderCalendar();
        }

        function renderCalendar() {
            const b = curr();
            const grid = document.getElementById('cal-grid');
            grid.innerHTML = "";
            const hoy = new Date();
            document.getElementById('cal-mes').innerText = hoy.toLocaleDateString('es', {month: 'long', year: 'numeric'});
            
            const ultimoDiaMes = new Date(hoy.getFullYear(), hoy.getMonth() + 1, 0).getDate();
            
            for(let i = 1; i <= ultimoDiaMes; i++) {
                const fStr = `${hoy.getFullYear()}-${String(hoy.getMonth()+1).padStart(2,'0')}-${String(i).padStart(2,'0')}`;
                const tieneEv = b.eventos[fStr] && b.eventos[fStr].length > 0;
                const esHoy = i === hoy.getDate();
                
                grid.innerHTML += `
                    <div onclick="clickDia('${fStr}')" class="p-2 rounded-lg ${esHoy ? 'bg-blue-600 text-white' : 'bg-white border'} ${tieneEv ? 'border-b-4 border-orange-400' : ''}">
                        ${i}
                    </div>`;
            }
        }

        // --- FUNCIONES DE ACCIÓN ---
        function addSimpleLog(t) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            curr().logs.unshift({t, h});
            save();
        }

        function addBiberon() {
            const ml = prompt("¿Cuántos ml ha tomado?");
            if(ml) {
                DB.lastBibi = ml;
                addSimpleLog(`🍼 Biberón ${ml}ml`);
            }
        }

        function repeatBibi() {
            if(DB.lastBibi) addSimpleLog(`🍼 Biberón ${DB.lastBibi}ml`);
            else alert("No hay biberón previo registrado");
        }

        function addCompraItem() {
            const it = prompt("¿Qué necesitas comprar?");
            if(it) { curr().compra.push(it); save(); }
        }

        function deleteCompra(i) {
            curr().compra.splice(i, 1);
            save();
        }

        function saveCurrentBebe() {
            const b = curr();
            b.n = document.getElementById('in-n').value;
            b.f = document.getElementById('in-f').value;
            b.p = document.getElementById('in-p').value;
            b.a = document.getElementById('in-a').value;
            save();
            closeModal('modal-config');
        }

        function addNewBebe() {
            DB.bebes.push({n: 'Nuevo Bebé', f: '', p: '', a: '', logs: [], meds: [], compra: [], eventos: {}});
            DB.idx = DB.bebes.length - 1;
            save();
            closeModal('modal-bebes');
            openModal('modal-config');
        }

        function switchBebe(i) {
            DB.idx = i;
            save();
            closeModal('modal-bebes');
        }

        function clickDia(f) {
            const ev = prompt("Añadir cita/evento para el " + f);
            if(ev) {
                if(!curr().eventos[f]) curr().eventos[f] = [];
                curr().eventos[f].push(ev);
                addSimpleLog("📅 Cita: " + ev);
                save();
            }
        }

        function saveNewMed() {
            const n = document.getElementById('m-nombre').value;
            const d = document.getElementById('m-dosis').value;
            if(n) {
                curr().meds.push({n, d});
                save();
                closeModal('modal-add-med');
                document.getElementById('m-nombre').value = "";
                document.getElementById('m-dosis').value = "";
            }
        }

        function deleteMed(i) {
            curr().meds.splice(i, 1);
            save();
        }

        function borrarTodo() {
            if(confirm("¿Seguro? Se borrarán TODOS los bebés y registros.")) {
                localStorage.clear();
                location.reload();
            }
        }

        // --- GUI ---
        function showTab(id) {
            ['diario', 'cal', 'salud'].forEach(t => {
                document.getElementById('sec-' + t).classList.toggle('hidden', t !== id);
                document.getElementById('btn-' + t).classList.toggle('tab-active', t === id);
                document.getElementById('btn-' + t).classList.toggle('text-slate-400', t !== id);
            });
        }

        function openModal(id) { document.getElementById(id).classList.remove('hidden'); }
        function closeModal(id) { document.getElementById(id).classList.add('hidden'); }

        // Inicio
        render();
    </script>
</body>
</html>
