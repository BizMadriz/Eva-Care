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
        .dia-hoy { background: #dbeafe; border: 2px solid #2563eb; }
        .tiene-evento { position: relative; }
        .tiene-evento::after { content: ''; position: absolute; bottom: 4px; left: 50%; transform: translateX(-50%); width: 4px; height: 4px; background: #ef4444; border-radius: 50%; }
    </style>
</head>
<body class="bg-slate-50 pb-28 font-sans text-slate-900">

    <header class="bg-white p-5 shadow-sm sticky top-0 z-50">
        <div class="flex justify-between items-center mb-4">
            <div>
                <h1 class="text-2xl font-black tracking-tighter text-blue-600">Eva Care <span class="text-slate-800">Pro</span></h1>
                <p id="nombre-bebe-txt" class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Cargando...</p>
            </div>
            <button onclick="toggleMod('modal-ajustes')" class="bg-slate-100 p-3 rounded-2xl text-slate-500"><i class="fa-solid fa-gear"></i></button>
        </div>

        <button onclick="addCompra()" class="w-full bg-orange-600 text-white py-4 rounded-2xl font-black text-xs shadow-lg flex items-center justify-center gap-2 active:scale-95 transition-all">
            <i class="fa-solid fa-cart-plus text-lg"></i> ¿QUÉ FALTA COMPRAR?
        </button>
    </header>

    <main class="p-4 space-y-6">
        
        <nav class="flex bg-white rounded-3xl shadow-md border overflow-hidden sticky top-40 z-40">
            <button id="t-diario" onclick="verTab('diario')" class="flex-1 p-4 tab-activa text-[10px]">DIARIO</button>
            <button id="t-cal" onclick="verTab('cal')" class="flex-1 p-4 text-slate-400 text-[10px]">CALENDARIO</button>
            <button id="t-salud" onclick="verTab('salud')" class="flex-1 p-4 text-slate-400 text-[10px]">SALUD</button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <button onclick="registrar('🤱 Toma de pecho')" class="w-full bg-pink-500 text-white py-8 rounded-[40px] font-black text-xl shadow-xl">
                TOMA DE PECHO
            </button>
            
            <div class="grid grid-cols-1 gap-3">
                <button onclick="addBibi()" class="bg-blue-600 text-white p-5 rounded-3xl font-black flex items-center justify-center gap-3">
                    <i class="fa-solid fa-bottle-water"></i> BIBERÓN
                </button>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <button onclick="registrar('💦 Pis')" class="bg-cyan-50 text-cyan-700 p-5 rounded-3xl font-black border border-cyan-100">💦 PIS</button>
                <button onclick="registrar('💩 Caca')" class="bg-orange-50 text-orange-800 p-5 rounded-3xl font-black border border-orange-100">💩 CACA</button>
            </div>

            <div id="logs" class="space-y-2 opacity-60"></div>
        </section>

        <section id="sec-cal" class="oculto space-y-4">
            <div class="bg-white p-5 rounded-[35px] shadow-sm">
                <div id="mes-nombre" class="text-center font-black text-blue-600 mb-4 uppercase text-sm"></div>
                <div class="grid grid-cols-7 gap-1 text-[10px] text-center font-bold text-slate-300 mb-2">
                    <div>LU</div><div>MA</div><div>MI</div><div>JU</div><div>VI</div><div>SA</div><div>DO</div>
                </div>
                <div id="calendario-grid" class="grid grid-cols-7 gap-2"></div>
            </div>
            
            <div class="bg-blue-600 text-white p-5 rounded-3xl shadow-lg">
                <h4 class="font-black mb-3">Agenda 14 días</h4>
                <div id="agenda-lista" class="space-y-2 text-xs"></div>
            </div>
        </section>

        <section id="sec-salud" class="oculto space-y-6">
            <div class="bg-white p-6 rounded-[35px] shadow-sm border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-black">Medicinas</h3>
                    <button onclick="toggleMod('modal-med')" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-xs font-black">+ AÑADIR</button>
                </div>
                <div id="med-lista" class="space-y-3"></div>
            </div>

            <div class="bg-orange-50 p-6 rounded-[35px] border border-orange-100">
                <h3 class="font-black text-orange-800 mb-4 text-center">Lista de la Compra</h3>
                <div id="compra-lista" class="space-y-2"></div>
            </div>
        </section>
    </main>

    <div id="modal-ajustes" class="oculto fixed inset-0 bg-black/80 z-[100] flex justify-end">
        <div class="bg-white w-80 h-full p-8 shadow-2xl overflow-y-auto">
            <h3 class="text-2xl font-black mb-8">Eva Care Pro</h3>
            
            <div class="space-y-6">
                <div class="p-4 bg-slate-50 rounded-2xl">
                    <label class="text-[10px] font-black text-slate-400 uppercase">Nombre del Bebé</label>
                    <input id="cfg-nombre" type="text" class="w-full bg-transparent border-b-2 border-blue-600 py-2 font-bold outline-none">
                </div>

                <div class="p-4 bg-slate-50 rounded-2xl">
                    <label class="text-[10px] font-black text-slate-400 uppercase block mb-2">Intervalo de Tomas (h)</label>
                    <input id="cfg-h" type="number" step="0.5" class="w-full bg-white p-2 rounded-xl font-bold text-center">
                </div>

                <button onclick="compartirDatos()" class="w-full bg-blue-100 text-blue-700 p-4 rounded-2xl font-bold text-sm flex items-center justify-center gap-2">
                    <i class="fa-solid fa-share-nodes"></i> Vincular a otra persona
                </button>

                <button onclick="saveCfg()" class="w-full bg-blue-600 text-white p-4 rounded-2xl font-black">GUARDAR CAMBIOS</button>
            </div>
            
            <button onclick="toggleMod('modal-ajustes')" class="mt-8 text-slate-300 font-bold w-full text-center">CERRAR</button>
        </div>
    </div>

    <div id="modal-med" class="oculto fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <h3 class="font-black text-xl text-center">Nuevo Tratamiento</h3>
            <input id="m-n" type="text" placeholder="Medicamento" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <input id="m-d" type="number" placeholder="Duración (Días)" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <button onclick="saveMed()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black">EMPEZAR</button>
            <button onclick="toggleMod('modal-med')" class="w-full text-slate-400 font-bold uppercase text-xs">Cerrar</button>
        </div>
    </div>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_PRO_DATA')) || {
            bebe: "Mi Bebé", h: 3, logs: [], medicinas: [], compra: [], eventos: {}
        };

        function save() { 
            localStorage.setItem('EVA_CARE_PRO_DATA', JSON.stringify(DATA)); 
            render(); 
        }

        function render() {
            document.getElementById('nombre-bebe-txt').innerText = DATA.bebe;
            document.getElementById('cfg-nombre').value = DATA.bebe;
            document.getElementById('cfg-h').value = DATA.h;

            // Render Logs
            document.getElementById('logs').innerHTML = DATA.logs.slice(0,5).map(l => `
                <div class="flex justify-between text-[10px] font-bold py-1 border-b uppercase">
                    <span>${l.t}</span><span>${l.h}</span>
                </div>
            `).join('');

            // Render Compra
            document.getElementById('compra-lista').innerHTML = DATA.compra.map((it, i) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center shadow-sm">
                    <span class="font-bold text-sm">${it}</span>
                    <button onclick="DATA.compra.splice(${i},1); save();" class="text-green-500 text-xl">✓</button>
                </div>
            `).join('');

            // Render Medicinas
            const ahora = Date.now();
            DATA.medicinas = DATA.medicinas.filter(m => m.fin > ahora);
            document.getElementById('med-lista').innerHTML = DATA.medicinas.map(m => `
                <div class="bg-slate-50 p-4 rounded-2xl border flex justify-between items-center">
                    <div><p class="font-black text-sm">${m.n}</p><p class="text-[9px] text-slate-400 uppercase">Fin: ${new Date(m.fin).toLocaleDateString()}</p></div>
                </div>
            `).join('') || "<p class='text-center text-xs text-slate-300'>Sin medicación activa</p>";

            renderCalendario();
            renderAgenda14();
        }

        function renderCalendario() {
            const grid = document.getElementById('calendario-grid');
            grid.innerHTML = "";
            const hoy = new Date();
            document.getElementById('mes-nombre').innerText = hoy.toLocaleDateString('es',{month:'long', year:'numeric'});
            
            for(let i=1; i<=31; i++) {
                const fechaStr = `2026-02-${String(i).padStart(2,'0')}`; // Simplificado para febrero
                const esHoy = i === hoy.getDate();
                const tieneEv = DATA.eventos[fechaStr];
                
                grid.innerHTML += `
                    <div onclick="addCita('${fechaStr}')" class="p-3 text-sm font-bold rounded-xl text-center ${esHoy?'dia-hoy':'bg-white'} ${tieneEv?'tiene-evento':''}">
                        ${i}
                    </div>`;
            }
        }

        function renderAgenda14() {
            const list = document.getElementById('agenda-lista');
            let html = "";
            const hoy = new Date();
            for(let i=0; i<14; i++) {
                let d = new Date(); d.setDate(hoy.getDate() + i);
                let iso = d.toISOString().split('T')[0];
                if(DATA.eventos[iso]) {
                    DATA.eventos[iso].forEach(ev => {
                        html += `<div class="bg-white/20 p-2 rounded-lg"><b>${d.getDate()}/${d.getMonth()+1}:</b> ${ev}</div>`;
                    });
                }
            }
            list.innerHTML = html || "No hay eventos en las próximas 2 semanas.";
        }

        function registrar(t) {
            const h = new Date().toLocaleTimeString([],{hour:'2-digit', minute:'2-digit'});
            DATA.logs.unshift({t, h});
            save();
        }

        function addBibi() {
            const ml = prompt("¿Cuántos ml?");
            if(ml) registrar(`🍼 Biberón ${ml}ml`);
        }

        function addCompra() {
            const it = prompt("¿Qué falta comprar?");
            if(it) { DATA.compra.push(it); save(); }
        }

        function addCita(fecha) {
            const ev = prompt("Añadir Cita o Vacuna para el día " + fecha);
            if(ev) {
                if(!DATA.eventos[fecha]) DATA.eventos[fecha] = [];
                DATA.eventos[fecha].push(ev);
                save();
            }
        }

        function saveMed() {
            const n = document.getElementById('m-n').value;
            const d = document.getElementById('m-d').value;
            if(n && d) {
                DATA.medicinas.push({ n, fin: Date.now() + (d * 86400000) });
                save(); toggleMod('modal-med');
            }
        }

        function saveCfg() {
            DATA.bebe = document.getElementById('cfg-nombre').value;
            DATA.h = document.getElementById('cfg-h').value;
            save(); toggleMod('modal-ajustes');
        }

        function compartirDatos() {
            const blob = btoa(JSON.stringify(DATA));
            prompt("Copia este código y envíalo a la otra persona para que lo cargue en su app:", blob);
        }

        function verTab(id) {
            ['diario','cal','salud'].forEach(t => {
                document.getElementById('sec-'+t).classList.toggle('oculto', t!==id);
                document.getElementById('t-'+t).classList.toggle('tab-activa', t===id);
                document.getElementById('t-'+t).classList.toggle('text-slate-400', t!==id);
            });
        }

        function toggleMod(id) { document.getElementById(id).classList.toggle('oculto'); }

        render();
    </script>
</body>
</html>
