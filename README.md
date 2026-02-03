<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .tab-activa { color: #2563eb; border-bottom: 4px solid #2563eb; font-weight: 900; }
        .oculto { display: none; }
        .bg-urgente { background: linear-gradient(135deg, #f97316 0%, #ea580c 100%); }
    </style>
</head>
<body class="bg-slate-50 pb-28 font-sans text-slate-900">

    <header class="bg-white p-5 shadow-sm sticky top-0 z-50">
        <div class="flex justify-between items-center mb-4">
            <div>
                <h1 class="text-2xl font-black tracking-tighter text-blue-600 italic">Eva Care <span class="text-slate-800">Pro</span></h1>
                <p id="subtitulo-bebe" class="text-[10px] font-bold text-slate-400 uppercase tracking-widest"></p>
            </div>
            <div class="flex gap-2">
                <button onclick="toggleMod('modal-bebe')" class="bg-blue-100 p-3 rounded-2xl text-blue-600"><i class="fa-solid fa-baby"></i></button>
                <button onclick="toggleMod('modal-ajustes')" class="bg-slate-100 p-3 rounded-2xl text-slate-500"><i class="fa-solid fa-gear"></i></button>
            </div>
        </div>

        <button onclick="addCompra()" class="w-full bg-urgente text-white py-4 rounded-2xl font-black text-xs shadow-lg flex items-center justify-center gap-2 mb-2">
            <i class="fa-solid fa-cart-plus"></i> ¿QUÉ FALTA COMPRAR?
        </button>
    </header>

    <main class="p-4 space-y-6">

        <section id="bloque-principal" class="space-y-4">
            <div class="bg-blue-600 text-white p-5 rounded-[30px] shadow-lg">
                <h4 class="text-[10px] font-black uppercase mb-3 opacity-80 tracking-widest">Agenda Próximos 14 días</h4>
                <div id="agenda-home" class="space-y-2 text-xs"></div>
            </div>

            <div id="compra-home-cont" class="bg-orange-50 p-5 rounded-[30px] border border-orange-100">
                <h4 class="text-[10px] font-black text-orange-800 uppercase mb-3 tracking-widest text-center">Lista de la Compra</h4>
                <div id="compra-lista" class="space-y-2"></div>
            </div>
        </section>

        <nav class="flex bg-white rounded-3xl shadow-md border overflow-hidden">
            <button id="t-diario" onclick="verTab('diario')" class="flex-1 p-4 tab-activa text-[10px]">REGISTROS</button>
            <button id="t-cal" onclick="verTab('cal')" class="flex-1 p-4 text-slate-400 text-[10px]">CALENDARIO</button>
            <button id="t-salud" onclick="verTab('salud')" class="flex-1 p-4 text-slate-400 text-[10px]">MEDICINAS</button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <button onclick="registrar('🤱 Toma de pecho')" class="w-full bg-pink-500 text-white py-8 rounded-[40px] font-black text-xl shadow-xl">
                TOMA DE PECHO
            </button>
            <div class="grid grid-cols-1 gap-2">
                <button onclick="addBibi()" class="bg-blue-600 text-white p-5 rounded-3xl font-black flex items-center justify-center gap-3">🍼 BIBERÓN</button>
            </div>
            <div class="grid grid-cols-2 gap-3">
                <button onclick="registrar('💦 Pis')" class="bg-white p-5 rounded-3xl font-black border-2 border-cyan-100 text-cyan-600 text-sm">💦 PIS</button>
                <button onclick="registrar('💩 Caca')" class="bg-white p-5 rounded-3xl font-black border-2 border-orange-100 text-orange-800 text-sm">💩 CACA</button>
            </div>
            <div id="logs" class="text-[10px] font-bold text-slate-300 uppercase px-2"></div>
        </section>

        <section id="sec-cal" class="oculto">
            <div class="bg-white p-5 rounded-[35px] shadow-sm border">
                <div id="mes-nombre" class="text-center font-black text-blue-600 mb-4 uppercase text-sm"></div>
                <div class="grid grid-cols-7 gap-1 text-[9px] text-center font-bold text-slate-300 mb-2">
                    <span>LU</span><span>MA</span><span>MI</span><span>JU</span><span>VI</span><span>SA</span><span>DO</span>
                </div>
                <div id="calendario-grid" class="grid grid-cols-7 gap-1 text-center font-bold"></div>
            </div>
        </section>

        <section id="sec-salud" class="oculto space-y-4">
            <div class="bg-white p-6 rounded-[35px] border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-black">Medicinas Activas</h3>
                    <button onclick="toggleMod('modal-med')" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-xs font-black">+ AÑADIR</button>
                </div>
                <div id="med-lista" class="space-y-2"></div>
            </div>
        </section>
    </main>

    <div id="modal-bebe" class="oculto fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-6 backdrop-blur-sm">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <h3 class="font-black text-xl text-center">Ficha del Bebé</h3>
            <input id="form-n" type="text" placeholder="Nombre" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <input id="form-f" type="date" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <div class="flex gap-2">
                <input id="form-p" type="text" placeholder="Peso (kg)" class="w-1/2 p-4 bg-slate-100 rounded-2xl font-bold">
                <input id="form-a" type="text" placeholder="Altura (cm)" class="w-1/2 p-4 bg-slate-100 rounded-2xl font-bold">
            </div>
            <button onclick="guardarBebe()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">GUARDAR FICHA</button>
        </div>
    </div>

    <div id="modal-med" class="oculto fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <h3 class="font-black text-xl text-center">Nuevo Tratamiento</h3>
            <input id="m-n" type="text" placeholder="Nombre (ej: Apiretal)" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <div class="flex gap-2">
                <input id="m-dosis" type="text" placeholder="Dosis (ej: 2ml)" class="w-1/2 p-4 bg-slate-100 rounded-2xl font-bold">
                <input id="m-dias" type="number" placeholder="Días" class="w-1/2 p-4 bg-slate-100 rounded-2xl font-bold">
            </div>
            <button onclick="saveMed()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black">EMPEZAR</button>
            <button onclick="toggleMod('modal-med')" class="w-full text-slate-400 font-bold uppercase text-xs">Cerrar</button>
        </div>
    </div>

    <div id="modal-ajustes" class="oculto fixed inset-0 bg-black/80 z-[100] flex justify-end">
        <div class="bg-white w-80 h-full p-8 shadow-2xl">
            <h3 class="text-2xl font-black mb-10">Ajustes</h3>
            <button onclick="localStorage.clear(); location.reload();" class="w-full bg-red-50 text-red-500 p-4 rounded-2xl font-bold text-xs">✕ BORRAR TODOS LOS DATOS</button>
            <button onclick="toggleMod('modal-ajustes')" class="mt-10 font-black text-slate-300 w-full">CERRAR</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V11')) || {
            bebe: {}, logs: [], meds: [], compra: [], eventos: {}
        };

        function save() { 
            localStorage.setItem('EVA_CARE_V11', JSON.stringify(DATA)); 
            render(); 
        }

        function render() {
            // Nombre en cabecera
            document.getElementById('subtitulo-bebe').innerText = DATA.bebe.n ? DATA.bebe.n : "Alta necesaria";
            if(!DATA.bebe.n) document.getElementById('modal-bebe').classList.remove('oculto');

            // Agenda en Home (14 días)
            const agH = document.getElementById('agenda-home');
            let htmlAg = "";
            const hoy = new Date();
            for(let i=0; i<14; i++) {
                let d = new Date(); d.setDate(hoy.getDate() + i);
                let iso = d.toISOString().split('T')[0];
                if(DATA.eventos[iso]) {
                    DATA.eventos[iso].forEach(ev => {
                        htmlAg += `<div class="bg-white/20 p-2 rounded-xl"><b>${d.getDate()}/${d.getMonth()+1}:</b> ${ev}</div>`;
                    });
                }
            }
            agH.innerHTML = htmlAg || "No hay citas próximas.";

            // Lista Compra en Home
            document.getElementById('compra-lista').innerHTML = DATA.compra.map((it, i) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center shadow-sm text-sm">
                    <span class="font-bold text-slate-700">${it}</span>
                    <button onclick="DATA.compra.splice(${i},1); save();" class="text-green-500 font-black px-2">✓</button>
                </div>
            `).join('') || "<p class='text-center text-orange-300 text-[10px] italic'>Lista vacía</p>";

            // Medicinas
            const ahora = Date.now();
            DATA.meds = DATA.meds.filter(m => m.fin > ahora);
            document.getElementById('med-lista').innerHTML = DATA.meds.map(m => `
                <div class="bg-slate-50 p-4 rounded-2xl border flex justify-between items-center text-xs">
                    <div><b>${m.n}</b> (${m.dosis})</div>
                    <div class="text-slate-400 font-bold uppercase">Fin: ${new Date(m.fin).toLocaleDateString()}</div>
                </div>
            `).join('') || "<p class='text-center text-slate-300 py-4 text-xs'>Sin medicación</p>";

            // Logs (Historial rápido)
            document.getElementById('logs').innerHTML = DATA.logs.slice(0,3).map(l => `<div>${l.h} - ${l.t}</div>`).join('');

            renderCalendario();
        }

        function renderCalendario() {
            const grid = document.getElementById('calendario-grid');
            grid.innerHTML = "";
            const hoy = new Date();
            document.getElementById('mes-nombre').innerText = hoy.toLocaleDateString('es',{month:'long', year:'numeric'});
            
            // Render días (ejemplo para mes actual)
            for(let i=1; i<=31; i++) {
                const f = `2026-02-${String(i).padStart(2,'0')}`;
                const tiene = DATA.eventos[f];
                grid.innerHTML += `<div onclick="addCita('${f}')" class="p-3 text-xs rounded-lg ${i==hoy.getDate()?'bg-blue-600 text-white':'bg-slate-50 text-slate-800'} ${tiene?'border-b-4 border-orange-500':''}">
                    ${i}
                </div>`;
            }
        }

        function guardarBebe() {
            DATA.bebe = {
                n: document.getElementById('form-n').value,
                f: document.getElementById('form-f').value,
                p: document.getElementById('form-p').value,
                a: document.getElementById('form-a').value
            };
            save(); toggleMod('modal-bebe');
        }

        function saveMed() {
            const n = document.getElementById('m-n').value;
            const ds = document.getElementById('m-dosis').value;
            const d = document.getElementById('m-dias').value;
            if(n && d) {
                DATA.meds.push({ n, dosis: ds, fin: Date.now() + (d * 86400000) });
                save(); toggleMod('modal-med');
            }
        }

        function addCompra() {
            const it = prompt("¿Qué falta comprar?");
            if(it) { DATA.compra.push(it); save(); }
        }

        function addCita(f) {
            const ev = prompt("Cita/Vacuna para el día " + f);
            if(ev) {
                if(!DATA.eventos[f]) DATA.eventos[f] = [];
                DATA.eventos[f].push(ev);
                save();
            }
        }

        function registrar(t) {
            DATA.logs.unshift({ t, h: new Date().toLocaleTimeString([],{hour:'2-digit', minute:'2-digit'}) });
            save();
        }

        function addBibi() {
            const ml = prompt("¿Cuántos ml?");
            if(ml) registrar(`🍼 Biberón ${ml}ml`);
        }

        function toggleMod(id) { document.getElementById(id).classList.toggle('oculto'); }
        function verTab(id) {
            ['diario','cal','salud'].forEach(t => {
                document.getElementById('sec-'+t).classList.toggle('oculto', t!==id);
                document.getElementById('t-'+t).classList.toggle('tab-activa', t===id);
                document.getElementById('t-'+t).classList.toggle('text-slate-400', t!==id);
            });
        }

        render();
    </script>
</body>
</html>
