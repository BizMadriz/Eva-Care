<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro v3.0</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        @keyframes pulse-red { 0%, 100% { opacity: 1; } 50% { opacity: 0.7; } }
        .urgente { animation: pulse-red 1.5s infinite; border: 2px solid #ea580c; }
    </style>
</head>
<body class="bg-slate-100 pb-28 font-sans">

    <header class="bg-white p-5 shadow-lg sticky top-0 z-50">
        <div class="flex justify-between items-center mb-4">
            <div>
                <h1 id="top-nombre" class="text-2xl font-black text-slate-800 leading-none">Cargando...</h1>
                <p id="top-edad" class="text-blue-500 font-bold text-xs mt-1 uppercase tracking-tighter"></p>
            </div>
            <button onclick="toggleMenu()" class="bg-slate-100 p-3 rounded-2xl text-slate-600 shadow-sm"><i class="fa-solid fa-gear"></i></button>
        </div>
        
        <div class="flex gap-2">
            <button onclick="addCompraPrompt()" class="flex-1 bg-orange-600 text-white py-3 rounded-xl font-black text-[11px] flex items-center justify-center gap-2 shadow-md active:scale-95 transition-all">
                <i class="fa-solid fa-cart-plus text-sm"></i> ¿QUÉ FALTA?
            </button>
            <button id="compra-status" onclick="showTab('salud')" class="hidden flex-1 bg-white text-orange-600 py-3 rounded-xl font-black text-[11px] flex items-center justify-center gap-2 urgente">
                <i class="fa-solid fa-shopping-cart"></i> VER LISTA (<span id="compra-count">0</span>)
            </button>
        </div>
    </header>

    <div id="side-menu" class="hidden fixed inset-0 bg-black/60 z-[60] flex justify-end">
        <div class="bg-white w-80 h-full p-6 shadow-2xl flex flex-col">
            <div class="flex justify-between items-center mb-8">
                <h3 class="font-black text-xl">Ajustes App</h3>
                <button onclick="toggleMenu()" class="text-3xl">&times;</button>
            </div>
            
            <div class="flex-1 space-y-6">
                <div class="p-5 bg-blue-50 rounded-3xl border border-blue-100">
                    <label class="text-[10px] font-black text-blue-400 uppercase tracking-widest block mb-2">Intervalo de Tomas</label>
                    <div class="flex items-center gap-3">
                        <input id="cfg-toma-h" type="number" step="0.5" class="w-20 p-3 rounded-xl font-bold text-center border-none shadow-inner">
                        <button onclick="saveInterval()" class="flex-1 bg-blue-600 text-white py-3 rounded-xl font-bold text-xs">Guardar</button>
                    </div>
                    <p class="text-[9px] text-slate-400 mt-2 italic">Define cada cuántas horas debe comer el bebé.</p>
                </div>

                <button onclick="alert('Funcionalidad para añadir pareja disponible en la versión Cloud.')" class="w-full text-left p-4 bg-slate-50 rounded-2xl font-bold flex items-center gap-3 text-slate-600">
                    <i class="fa-solid fa-user-plus"></i> Añadir otra persona
                </button>
            </div>

            <button onclick="borrarTodo()" class="w-full p-4 text-red-400 font-bold text-xs flex items-center justify-center gap-2">
                <i class="fa-solid fa-trash"></i> BORRAR TODO Y REINICIAR
            </button>
        </div>
    </div>

    <main class="p-4 max-w-md mx-auto space-y-6">
        
        <section class="space-y-3">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">Agenda Próximos 14 días</h3>
            <div id="upcoming-events-panel" class="space-y-2">
                </div>
        </section>

        <section id="timers-container" class="space-y-2"></section>

        <nav class="flex justify-around bg-white rounded-3xl shadow-xl border border-slate-100 overflow-hidden sticky top-44 z-40">
            <button onclick="showTab('diario')" id="tab-diario" class="p-5 active-tab flex-1"><i class="fa-solid fa-baby text-xl"></i></button>
            <button onclick="showTab('calendario')" id="tab-calendario" class="p-5 flex-1 text-slate-300"><i class="fa-solid fa-calendar-alt text-xl"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-5 flex-1 text-slate-300"><i class="fa-solid fa-notes-medical text-xl"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <button onclick="openPechoModal()" class="w-full bg-pink-500 text-white p-7 rounded-[35px] font-black shadow-lg flex items-center justify-center gap-4 active:scale-95 transition-transform">
                <i class="fa-solid fa-breast-pumping text-3xl"></i> REGISTRAR PECHO
            </button>
            
            <div id="biberon-fast" class="space-y-2">
                <button id="btn-last-bib" onclick="registrarLastBiberon()" class="hidden w-full bg-blue-600 text-white p-4 rounded-2xl font-bold shadow-md"></button>
                <button onclick="promptBiberon()" class="w-full bg-blue-50 text-blue-600 p-4 rounded-2xl font-bold border-2 border-dashed border-blue-200">🍼 Nuevo Biberón...</button>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <button onclick="registrar('💦 Pis')" class="bg-yellow-50 text-yellow-700 p-5 rounded-3xl font-black border border-yellow-100">💦 PIS</button>
                <button onclick="registrar('💩 Caca')" class="bg-orange-50 text-orange-900 p-5 rounded-3xl font-black border border-orange-100">💩 CACA</button>
            </div>
            
            <div id="log-recent" class="bg-white/50 p-4 rounded-3xl space-y-2"></div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-white p-6 rounded-[35px] shadow-sm border border-slate-200">
                <div class="flex justify-between items-center mb-5">
                    <h3 class="font-black text-slate-800">Tratamientos</h3>
                    <button onclick="openMedModal()" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-[10px] font-black">+ AÑADIR</button>
                </div>
                <div id="lista-medicinas" class="space-y-3"></div>
            </div>

            <div class="bg-orange-50 p-6 rounded-[35px] border border-orange-200">
                <h3 class="font-black text-orange-800 mb-4">Lista de la Compra</h3>
                <div id="lista-compra" class="space-y-2"></div>
            </div>
        </section>

        <section id="sec-calendario" class="hidden">
            <div id="calendar-content" class="bg-white p-5 rounded-3xl shadow-sm">
                <div id="month-name" class="text-center font-black text-blue-600 mb-4 uppercase"></div>
                <div id="days-container" class="grid grid-cols-7 gap-1"></div>
            </div>
            <div id="day-detail" class="mt-4 p-5 bg-blue-50 rounded-3xl hidden">
                <h4 id="detail-date" class="font-black text-blue-800 mb-3"></h4>
                <div id="detail-events" class="space-y-2"></div>
                <button onclick="openEventModal()" class="w-full mt-4 bg-blue-600 text-white p-3 rounded-xl font-bold text-xs">Añadir Cita Manual</button>
            </div>
        </section>
    </main>

    <div id="med-modal" class="hidden fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-6 backdrop-blur-sm">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <h3 class="font-black text-xl">Nuevo Tratamiento</h3>
            <input id="m-nombre" type="text" placeholder="Medicamento" class="w-full p-4 bg-slate-100 rounded-2xl outline-none font-bold">
            <div class="flex gap-2">
                <input id="m-dosis" type="text" placeholder="Dosis (ej: 2ml)" class="w-1/2 p-4 bg-slate-100 rounded-2xl outline-none font-bold">
                <input id="m-cada" type="number" placeholder="Cada (h)" class="w-1/2 p-4 bg-slate-100 rounded-2xl outline-none font-bold">
            </div>
            <input id="m-dias" type="number" placeholder="¿Cuántos días de tratamiento?" class="w-full p-4 bg-slate-100 rounded-2xl outline-none font-bold">
            <button onclick="saveMedicina()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">EMPEZAR</button>
            <button onclick="closeMedModal()" class="w-full text-slate-400 font-bold text-xs uppercase">Cerrar</button>
        </div>
    </div>

    <div id="pecho-modal" class="hidden fixed inset-0 bg-black/80 z-[100] flex items-end">
        <div class="bg-white w-full p-10 rounded-t-[50px] space-y-6">
            <h3 class="font-black text-center text-xl">¿Qué lado ha tomado?</h3>
            <div class="grid grid-cols-3 gap-3">
                <button onclick="registrarPecho('Izquierdo')" class="bg-pink-50 text-pink-600 p-6 rounded-3xl font-black border-2 border-pink-100">IZQ</button>
                <button onclick="registrarPecho('Ambos')" class="bg-purple-50 text-purple-600 p-6 rounded-3xl font-black border-2 border-purple-100">AMBOS</button>
                <button onclick="registrarPecho('Derecho')" class="bg-pink-50 text-pink-600 p-6 rounded-3xl font-black border-2 border-pink-100">DER</button>
            </div>
            <button onclick="closePechoModal()" class="w-full text-slate-300 font-bold uppercase text-xs">Cerrar</button>
        </div>
    </div>

    <script>
        // IDENTIFICADOR ÚNICO PARA FORZAR ACTUALIZACIÓN
        const DB_NAME = 'baby_pro_v3_final';
        let bData = { 
            nombre: "Mi Bebé", fecha: "2025-01-01", logs: {}, 
            medicinas: [], eventos: {}, compra: [], intervaloToma: 3 
        };

        function init() {
            const saved = localStorage.getItem(DB_NAME);
            if(saved) bData = JSON.parse(saved);
            
            // Limpiar medicinas automáticamente
            const ahora = Date.now();
            bData.medicinas = bData.medicinas.filter(m => !m.finTratamiento || m.finTratamiento > ahora);
            save();

            renderAll();
            setInterval(updateTimers, 1000);
        }

        function save() { localStorage.setItem(DB_NAME, JSON.stringify(bData)); }

        function renderAll() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = calcularEdad(bData.fecha);
            document.getElementById('cfg-toma-h').value = bData.intervaloToma;
            
            renderUpcomingEvents();
            renderMedicina();
            renderCompra();
            renderLog();

            if(bData.lastBib) {
                const btn = document.getElementById('btn-last-bib');
                btn.innerText = `🍼 REPETIR BIBERÓN ${bData.lastBib}ml`;
                btn.classList.remove('hidden');
            }
        }

        // AGENDA 14 DÍAS
        function renderUpcomingEvents() {
            const panel = document.getElementById('upcoming-events-panel');
            let html = "";
            const hoy = new Date();
            
            for(let i=0; i < 14; i++) {
                let d = new Date(); d.setDate(hoy.getDate() + i);
                let iso = d.toISOString().split('T')[0];
                if(bData.eventos[iso]) {
                    bData.eventos[iso].forEach(ev => {
                        html += `
                        <div class="flex items-center gap-4 bg-white p-4 rounded-2xl shadow-sm border-l-4 ${i===0?'border-orange-500':'border-blue-400'}">
                            <div class="text-center min-w-[35px]">
                                <p class="text-[8px] font-black text-slate-300 uppercase">${d.toLocaleDateString('es',{weekday:'short'})}</p>
                                <p class="text-sm font-black text-slate-700">${d.getDate()}</p>
                            </div>
                            <div class="flex-1">
                                <p class="text-xs font-black text-slate-800">${ev.titulo}</p>
                                <p class="text-[9px] text-slate-400">${ev.hora} ${i===0?'(Hoy)':''}</p>
                            </div>
                        </div>`;
                    });
                }
            }
            panel.innerHTML = html || '<p class="text-[10px] text-center text-slate-300 py-4 italic">No hay citas en las próximas 2 semanas</p>';
        }

        // MEDICACIÓN CON CADUCIDAD
        function saveMedicina() {
            const n = document.getElementById('m-nombre').value;
            const ds = document.getElementById('m-dosis').value;
            const c = parseFloat(document.getElementById('m-cada').value);
            const d = parseInt(document.getElementById('m-dias').value);
            
            if(n && c && d) {
                const fin = Date.now() + (d * 24 * 60 * 60 * 1000);
                bData.medicinas.push({ 
                    nombre: n, dosis: ds, cada: c, 
                    finTratamiento: fin, ultimaToma: null 
                });
                save(); renderMedicina(); closeMedModal();
            }
        }

        function renderMedicina() {
            document.getElementById('lista-medicinas').innerHTML = bData.medicinas.map((m, i) => `
                <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center border border-slate-100">
                    <div>
                        <p class="font-black text-slate-800 text-sm">${m.nombre} <span class="text-blue-500">${m.dosis}</span></p>
                        <p class="text-[9px] text-slate-400 font-bold uppercase tracking-tighter">Fin: ${new Date(m.finTratamiento).toLocaleDateString()}</p>
                    </div>
                    <button onclick="tomarMedicina(${i})" class="bg-blue-600 text-white w-10 h-10 rounded-xl shadow-lg flex items-center justify-center">
                        <i class="fa-solid fa-check"></i>
                    </button>
                </div>
            `).join('') || '<p class="text-center text-xs text-slate-300">Sin tratamientos activos</p>';
        }

        function tomarMedicina(i) {
            bData.medicinas[i].ultimaToma = Date.now();
            registrar(`💊 ${bData.medicinas[i].nombre} (${bData.medicinas[i].dosis})`);
            save(); renderMedicina();
        }

        // COMPRA
        function addCompraPrompt() {
            const it = prompt("¿Qué falta comprar?");
            if(it) { bData.compra.push(it); save(); renderCompra(); }
        }

        function renderCompra() {
            const count = bData.compra.length;
            document.getElementById('compra-status').classList.toggle('hidden', count === 0);
            document.getElementById('compra-count').innerText = count;
            document.getElementById('lista-compra').innerHTML = bData.compra.map((it, i) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center text-xs shadow-sm mb-2">
                    <span class="font-bold text-slate-700">${it}</span>
                    <button onclick="borrarCompra(${i})" class="text-green-500 font-black"><i class="fa-solid fa-check"></i></button>
                </div>
            `).join('');
        }

        function borrarCompra(i) { bData.compra.splice(i,1); save(); renderCompra(); }

        // CONFIGURACIÓN TOMAS
        function saveInterval() {
            bData.intervaloToma = parseFloat(document.getElementById('cfg-toma-h').value);
            save(); alert("Intervalo actualizado correctamente."); toggleMenu();
        }

        // OTROS
        function updateTimers() {
            const container = document.getElementById('timers-container');
            let html = ""; const ahora = Date.now();
            const lt = localStorage.getItem('last_toma_time_v3');
            if(lt) {
                const diff = (parseInt(lt) + (bData.intervaloToma * 3600000)) - ahora;
                html += renderTimerElement("🍼 PRÓXIMA TOMA", diff, "blue");
            }
            bData.medicinas.forEach(m => {
                if(m.ultimaToma) {
                    const diff = (m.ultimaToma + (m.cada * 3600000)) - ahora;
                    html += renderTimerElement(`💊 ${m.nombre}`, diff, "indigo");
                }
            });
            container.innerHTML = html;
        }

        function renderTimerElement(lbl, diff, col) {
            const s = Math.floor(diff/1000);
            if(s <= 0) return `<div class="bg-red-500 text-white p-3 rounded-xl flex justify-between font-black text-[10px] animate-pulse"><span>${lbl}</span><span>¡YA TOCA!</span></div>`;
            const h = Math.floor(s/3600); const m = Math.floor((s%3600)/60); const sc = s%60;
            return `<div class="bg-white p-3 rounded-xl border flex justify-between text-[10px] font-bold text-slate-500 shadow-sm">
                <span>${lbl}</span><span class="text-${col}-600 font-mono">${h}:${String(m).padStart(2,'0')}:${String(sc).padStart(2,'0')}</span>
            </div>`;
        }

        function calcularEdad(f) { const d = Math.floor((new Date() - new Date(f))/86400000); return d < 30 ? d+" DÍAS" : Math.floor(d/30.44)+" MESES"; }
        function registrar(t) { 
            const iso = new Date().toISOString().split('T')[0]; 
            const h = new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}); 
            if(!bData.logs[iso]) bData.logs[iso]=[]; 
            bData.logs[iso].unshift({txt:t, h}); 
            if(t.includes('Biberón') || t.includes('Pecho')) localStorage.setItem('last_toma_time_v3', Date.now()); 
            save(); renderLog(); 
        }
        function renderLog() { 
            const hoy = new Date().toISOString().split('T')[0]; 
            const logs = bData.logs[hoy] || []; 
            document.getElementById('log-recent').innerHTML = logs.slice(0,3).map(l => `<div class="bg-white p-2 rounded-lg flex justify-between text-[9px] text-slate-400"><span>${l.txt}</span><span>${l.h}</span></div>`).join(''); 
        }
        function showTab(t) { 
            ['diario','calendario','salud'].forEach(s => { 
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t); 
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t); 
                document.getElementById('tab-'+s).classList.toggle('text-slate-300', s!==t);
            }); 
            if(t==='calendario') renderFullCalendar();
        }
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('hidden'); }
        function openMedModal() { document.getElementById('med-modal').classList.remove('hidden'); }
        function closeMedModal() { document.getElementById('med-modal').classList.add('hidden'); }
        function openPechoModal() { document.getElementById('pecho-modal').classList.remove('hidden'); }
        function closePechoModal() { document.getElementById('pecho-modal').classList.add('hidden'); }
        function registrarPecho(l) { registrar("🤱 Pecho: "+l); closePechoModal(); }
        function promptBiberon() { const ml = prompt("¿Cuántos ml?"); if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderAll(); } }
        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }
        function borrarTodo() { if(confirm("¿Seguro? Se borrará todo.")) { localStorage.clear(); location.reload(); } }

        // CALENDARIO BÁSICO
        function renderFullCalendar() {
            const grid = document.getElementById('days-container'); grid.innerHTML = '';
            const d = new Date(); const m = d.getMonth(); const y = d.getFullYear();
            document.getElementById('month-name').innerText = d.toLocaleDateString('es',{month:'long',year:'numeric'});
            for(let i=1; i<=31; i++) {
                grid.innerHTML += `<div class="p-2 border rounded-lg text-center font-bold text-slate-700">${i}</div>`;
            }
        }

        init();
    </script>
</body>
</html>
