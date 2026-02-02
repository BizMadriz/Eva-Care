<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro v2.1</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        @keyframes pulse-orange { 0%, 100% { opacity: 1; } 50% { opacity: 0.6; } }
        .animate-urgent { animation: pulse-orange 1.5s infinite; }
    </style>
</head>
<body class="bg-slate-50 pb-24 font-sans">

    <header class="bg-white p-5 shadow-sm sticky top-0 z-40">
        <div class="flex justify-between items-start mb-4">
            <div>
                <h1 id="top-nombre" class="text-2xl font-black text-slate-800 leading-none">Cargando...</h1>
                <p id="top-edad" class="text-blue-500 font-bold text-xs mt-1 uppercase tracking-wider"></p>
            </div>
            <button onclick="toggleMenu()" class="bg-slate-100 p-3 rounded-2xl text-slate-600"><i class="fa-solid fa-bars-staggered"></i></button>
        </div>
        
        <div class="flex gap-2">
            <button onclick="addCompraPrompt()" class="flex-1 bg-orange-500 text-white py-3 rounded-xl font-bold text-[10px] flex items-center justify-center gap-2 shadow-md">
                <i class="fa-solid fa-cart-plus"></i> AÑADIR COMPRA
            </button>
            <div id="compra-status" class="hidden flex-1 bg-orange-100 text-orange-700 py-3 rounded-xl font-black text-[10px] flex items-center justify-center gap-2 border border-orange-200 animate-urgent" onclick="showTab('salud')">
                <i class="fa-solid fa-shopping-basket"></i> HAY PENDIENTES
            </div>
        </div>
    </header>

    <div id="side-menu" class="hidden fixed inset-0 bg-black/60 z-50 flex justify-end">
        <div class="bg-white w-80 h-full p-6 shadow-2xl space-y-8">
            <div class="flex justify-between items-center">
                <h3 class="font-black text-xl">Ajustes</h3>
                <button onclick="toggleMenu()" class="text-2xl">&times;</button>
            </div>
            <div class="space-y-6">
                <section>
                    <label class="text-[10px] font-black text-slate-400 uppercase">Configuración de Tomas</label>
                    <div class="mt-2 p-4 bg-slate-50 rounded-2xl border border-slate-100">
                        <p class="text-xs mb-2 text-slate-600">Frecuencia recomendada (horas):</p>
                        <div class="flex items-center gap-4">
                            <input id="cfg-toma-h" type="number" step="0.5" class="w-20 p-2 rounded-xl font-bold text-center border-2 border-blue-100 focus:border-blue-400 outline-none">
                            <button onclick="saveInterval()" class="flex-1 bg-blue-600 text-white py-2 rounded-xl font-bold text-xs">Guardar</button>
                        </div>
                    </div>
                </section>
                <button onclick="alert('Próximamente: Sincronización en la nube')" class="w-full text-left p-4 bg-green-50 text-green-700 rounded-2xl font-bold flex items-center gap-3">
                    <i class="fa-solid fa-user-plus"></i> Añadir Cuidador
                </button>
                <hr>
                <button onclick="borrarTodo()" class="w-full text-left p-4 text-red-500 font-bold flex items-center gap-3">
                    <i class="fa-solid fa-trash-can"></i> Borrar todos los datos
                </button>
            </div>
        </div>
    </div>

    <main class="p-4 max-w-md mx-auto space-y-6">
        
        <section class="space-y-3">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">Agenda Próximos 14 días</h3>
            <div id="upcoming-events-panel" class="space-y-2">
                </div>
        </section>

        <section class="space-y-3">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">Contadores en tiempo real</h3>
            <div id="timers-container" class="space-y-2"></div>
        </section>

        <nav class="flex justify-around bg-white rounded-2xl shadow-lg border border-slate-100 overflow-hidden sticky top-[180px] z-30">
            <button onclick="showTab('diario')" id="tab-diario" class="p-4 active-tab flex-1"><i class="fa-solid fa-baby-carriage"></i></button>
            <button onclick="showTab('calendario')" id="tab-calendario" class="p-4 flex-1"><i class="fa-solid fa-calendar"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-4 flex-1"><i class="fa-solid fa-hand-holding-medical"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="openPechoModal()" class="col-span-2 bg-pink-500 text-white p-6 rounded-3xl font-black shadow-lg flex items-center justify-center gap-3 active:scale-95 transition-all">
                    <i class="fa-solid fa-person-breastfeeding text-2xl"></i> REGISTRAR PECHO
                </button>
                <div class="col-span-2 space-y-2">
                    <button id="btn-last-bib" onclick="registrarLastBiberon()" class="hidden w-full bg-blue-600 text-white p-4 rounded-2xl font-bold shadow-md"></button>
                    <button onclick="promptBiberon()" class="w-full bg-blue-100 text-blue-700 p-4 rounded-2xl font-bold border border-blue-200">🍼 Nuevo Biberón...</button>
                </div>
            </div>
            <div id="log-recent" class="space-y-2 opacity-80"></div>
        </section>

        <section id="sec-calendario" class="hidden space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-sm border border-slate-200">
                <div id="month-name" class="text-center font-black uppercase text-sm mb-4"></div>
                <div id="days-container" class="grid grid-cols-7 gap-1 text-center text-xs"></div>
            </div>
            <div id="day-detail" class="bg-white p-5 rounded-3xl border-2 border-blue-50 hidden">
                <div id="detail-date" class="font-black text-blue-600 mb-3"></div>
                <div id="detail-events" class="space-y-2"></div>
                <button onclick="openEventModal()" class="w-full mt-4 bg-slate-800 text-white p-3 rounded-xl font-bold text-xs">+ Añadir Evento Manual</button>
            </div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-orange-50 p-5 rounded-3xl border border-orange-100">
                <h3 class="font-black text-orange-800 text-sm mb-4 flex items-center gap-2">
                    <i class="fa-solid fa-list-check"></i> Lista de la Compra
                </h3>
                <div id="lista-compra" class="space-y-2"></div>
            </div>

            <div class="bg-white p-5 rounded-3xl shadow-sm border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-black text-slate-800 text-sm">Tratamientos Activos</h3>
                    <button onclick="openMedModal()" class="bg-blue-600 text-white px-3 py-1 rounded-lg text-[10px] font-bold">+ NUEVO</button>
                </div>
                <div id="lista-medicinas" class="space-y-3"></div>
            </div>
        </section>
    </main>

    <div id="med-modal" class="hidden fixed inset-0 bg-black/70 z-50 flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-3xl p-6 space-y-4">
            <h3 class="font-black text-xl">Nuevo Tratamiento</h3>
            <input id="m-nombre" type="text" placeholder="Nombre (ej: Apiretal)" class="w-full p-4 bg-slate-100 rounded-2xl outline-none">
            <div class="flex gap-2">
                <input id="m-dosis" type="text" placeholder="Dosis (ej: 2ml)" class="w-1/2 p-4 bg-slate-100 rounded-2xl outline-none">
                <input id="m-cada" type="number" placeholder="Cada (h)" class="w-1/2 p-4 bg-slate-100 rounded-2xl outline-none">
            </div>
            <input id="m-dias" type="number" placeholder="Duración (días)" class="w-full p-4 bg-slate-100 rounded-2xl outline-none">
            <button onclick="saveMedicina()" class="w-full bg-blue-600 text-white p-4 rounded-2xl font-black shadow-lg">Comenzar Tratamiento</button>
            <button onclick="closeMedModal()" class="w-full text-slate-400 font-bold text-xs">CANCELAR</button>
        </div>
    </div>

    <div id="pecho-modal" class="hidden fixed inset-0 bg-black/70 z-50 flex items-end">
        <div class="bg-white w-full p-8 rounded-t-[40px] space-y-6">
            <h3 class="font-black text-center text-xl text-slate-800">¿Qué lado ha tomado?</h3>
            <div class="grid grid-cols-3 gap-3">
                <button onclick="registrarPecho('Izquierdo')" class="bg-pink-50 p-6 rounded-3xl font-black text-pink-600 border-2 border-pink-100">Izquierdo</button>
                <button onclick="registrarPecho('Ambos')" class="bg-purple-50 p-6 rounded-3xl font-black text-purple-600 border-2 border-purple-100">Ambos</button>
                <button onclick="registrarPecho('Derecho')" class="bg-pink-50 p-6 rounded-3xl font-black text-pink-600 border-2 border-pink-100">Derecho</button>
            </div>
            <button onclick="closePechoModal()" class="w-full p-3 text-slate-300 font-bold">CERRAR</button>
        </div>
    </div>

    <script>
        let bData = {
            nombre: "", fecha: "", logs: {}, crecimiento: [], 
            medicinas: [], eventos: {}, compra: [], intervaloToma: 3
        };
        let viewDate = new Date();
        let selectedDayStr = "";

        // INICIALIZACIÓN
        function init() {
            const saved = localStorage.getItem('baby_pro_v2_1');
            if(!saved) {
                // Si no hay datos, podrías redirigir a un setup, aquí simplifico:
                bData.nombre = prompt("Nombre del bebé:") || "Mi Bebé";
                bData.fecha = prompt("Fecha nacimiento (YYYY-MM-DD):") || "2025-01-01";
                bData.crecimiento = [{fecha: new Date().toISOString().split('T')[0], p: 3.5, m: 50}];
                save();
            } else {
                bData = JSON.parse(saved);
            }
            
            // Limpiar tratamientos caducados
            const ahora = Date.now();
            bData.medicinas = bData.medicinas.filter(m => !m.finTratamiento || m.finTratamiento > ahora);
            save();

            renderAll();
            setInterval(updateTimers, 1000);
        }

        function save() { localStorage.setItem('baby_pro_v2_1', JSON.stringify(bData)); }

        // RENDERIZADO GENERAL
        function renderAll() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = calcularEdad(bData.fecha);
            document.getElementById('cfg-toma-h').value = bData.intervaloToma;
            
            renderUpcomingEvents();
            renderLog();
            renderMedicina();
            renderCompra();
            if(bData.lastBib) {
                document.getElementById('btn-last-bib').innerText = `🍼 Repetir Biberón ${bData.lastBib}ml`;
                document.getElementById('btn-last-bib').classList.remove('hidden');
            }
        }

        // PANEL EVENTOS PRÓXIMOS (14 DÍAS)
        function renderUpcomingEvents() {
            const container = document.getElementById('upcoming-events-panel');
            let html = "";
            const hoy = new Date();
            
            for(let i=0; i < 14; i++) {
                const tempDate = new Date();
                tempDate.setDate(hoy.getDate() + i);
                const iso = tempDate.toISOString().split('T')[0];
                
                if(bData.eventos[iso]) {
                    bData.eventos[iso].forEach(ev => {
                        const esHoy = i === 0;
                        html += `
                        <div class="flex items-center gap-3 bg-white p-3 rounded-2xl border-l-4 ${esHoy?'border-orange-500':'border-blue-400'} shadow-sm">
                            <div class="text-center min-w-[40px]">
                                <p class="text-[8px] font-black text-slate-400 uppercase">${tempDate.toLocaleDateString('es', {weekday:'short'})}</p>
                                <p class="text-sm font-black text-slate-700">${tempDate.getDate()}</p>
                            </div>
                            <div class="flex-1">
                                <p class="text-xs font-black text-slate-800">${ev.titulo}</p>
                                <p class="text-[9px] text-slate-400">${ev.hora}</p>
                            </div>
                            ${esHoy ? '<span class="text-[8px] bg-orange-100 text-orange-600 px-2 py-1 rounded-full font-bold">HOY</span>' : ''}
                        </div>`;
                    });
                }
            }
            container.innerHTML = html || '<p class="text-[10px] text-center text-slate-300 italic py-2">No hay eventos en las próximas 2 semanas</p>';
        }

        // GESTIÓN MEDICAMENTOS
        function saveMedicina() {
            const nombre = document.getElementById('m-nombre').value;
            const dosis = document.getElementById('m-dosis').value;
            const cada = parseFloat(document.getElementById('m-cada').value);
            const dias = parseInt(document.getElementById('m-dias').value);

            if(nombre && cada && dias) {
                const fin = Date.now() + (dias * 24 * 60 * 60 * 1000);
                bData.medicinas.push({
                    nombre, dosis, cada, dias, 
                    inicio: Date.now(), 
                    finTratamiento: fin,
                    ultimaToma: null
                });
                save();
                renderMedicina();
                closeMedModal();
            }
        }

        function renderMedicina() {
            const container = document.getElementById('lista-medicinas');
            container.innerHTML = bData.medicinas.map((m, idx) => {
                const fin = new Date(m.finTratamiento).toLocaleDateString();
                return `
                <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center border border-slate-100">
                    <div>
                        <p class="font-black text-slate-800 text-sm">${m.nombre} <span class="text-blue-500 text-xs">${m.dosis}</span></p>
                        <p class="text-[9px] text-slate-400 font-bold uppercase">Finaliza: ${fin} (Cada ${m.cada}h)</p>
                    </div>
                    <button onclick="tomarMedicina(${idx})" class="bg-blue-600 text-white w-10 h-10 rounded-xl flex items-center justify-center shadow-lg active:scale-90">
                        <i class="fa-solid fa-check"></i>
                    </button>
                </div>`;
            }).join('') || '<p class="text-center text-xs text-slate-300">Sin tratamientos activos</p>';
        }

        function tomarMedicina(idx) {
            bData.medicinas[idx].ultimaToma = Date.now();
            registrar(`💊 Dosis ${bData.medicinas[idx].nombre} (${bData.medicinas[idx].dosis})`);
            save();
            renderMedicina();
        }

        // COMPRA
        function addCompraPrompt() {
            const item = prompt("¿Qué necesitas comprar?");
            if(item) { bData.compra.push(item); save(); renderCompra(); }
        }

        function renderCompra() {
            const status = document.getElementById('compra-status');
            status.classList.toggle('hidden', bData.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = bData.compra.map((it, i) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center text-xs shadow-sm">
                    <span class="font-bold">${it}</span>
                    <button onclick="borrarCompra(${i})" class="text-green-500 font-black px-2">✓</button>
                </div>
            `).join('') || '<p class="text-center text-[10px] text-orange-300">Todo al día</p>';
        }

        function borrarCompra(i) { bData.compra.splice(i,1); save(); renderCompra(); }

        // TIMERS TIEMPO REAL
        function updateTimers() {
            const container = document.getElementById('timers-container');
            let html = "";
            const ahora = Date.now();

            // Timer Toma
            const lt = localStorage.getItem('last_toma_time');
            if(lt) {
                const diff = (parseInt(lt) + (bData.intervaloToma * 3600000)) - ahora;
                html += renderTimerElement("🍼 Próxima Toma", diff, "blue");
            }

            // Timers Medicinas
            bData.medicinas.forEach(m => {
                if(m.ultimaToma) {
                    const diff = (m.ultimaToma + (m.cada * 3600000)) - ahora;
                    html += renderTimerElement(`💊 ${m.nombre}`, diff, "indigo");
                }
            });

            container.innerHTML = html;
        }

        function renderTimerElement(label, diff, color) {
            const s = Math.floor(diff/1000);
            if(s <= 0) return `<div class="bg-red-500 text-white p-3 rounded-xl flex justify-between font-black text-[10px] animate-pulse"><span>${label}</span><span>¡YA!</span></div>`;
            const h = Math.floor(s/3600);
            const m = Math.floor((s%3600)/60);
            const sec = s%60;
            return `<div class="bg-white p-3 rounded-xl border flex justify-between text-[10px] font-bold text-slate-500 shadow-sm">
                <span>${label}</span>
                <span class="text-${color}-600 font-mono">${h}:${String(m).padStart(2,'0')}:${String(sec).padStart(2,'0')}</span>
            </div>`;
        }

        // AUXILIARES
        function calcularEdad(f) {
            const dias = Math.floor((new Date() - new Date(f)) / 86400000);
            if(dias < 30) return dias + " DÍAS";
            return Math.floor(dias/30.44) + " MESES";
        }

        function registrar(txt) {
            const iso = new Date().toISOString().split('T')[0];
            const hora = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            if(!bData.logs[iso]) bData.logs[iso] = [];
            bData.logs[iso].unshift({txt, h: hora});
            if(txt.includes('Biberón') || txt.includes('Pecho')) localStorage.setItem('last_toma_time', Date.now());
            save(); renderLog();
        }

        function renderLog() {
            const hoy = new Date().toISOString().split('T')[0];
            const logs = bData.logs[hoy] || [];
            document.getElementById('log-recent').innerHTML = logs.slice(0,3).map(l => `
                <div class="bg-white p-2 rounded-lg flex justify-between text-[10px] border-l-2 border-blue-200">
                    <span>${l.txt}</span><span class="text-slate-300">${l.h}</span>
                </div>
            `).join('');
        }

        // NAVEGACIÓN Y MODALES
        function showTab(t) {
            ['diario','calendario','salud'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
            if(t==='calendario') renderFullCalendar();
        }

        function toggleMenu() { document.getElementById('side-menu').classList.toggle('hidden'); }
        function openMedModal() { document.getElementById('med-modal').classList.remove('hidden'); }
        function closeMedModal() { document.getElementById('med-modal').classList.add('hidden'); }
        function openPechoModal() { document.getElementById('pecho-modal').classList.remove('hidden'); }
        function closePechoModal() { document.getElementById('pecho-modal').classList.add('hidden'); }
        function saveInterval() { bData.intervaloToma = parseFloat(document.getElementById('cfg-toma-h').value); save(); toggleMenu(); }
        
        function registrarPecho(l) { registrar(`🤱 Pecho: ${l}`); closePechoModal(); }
        function promptBiberon() { const ml = prompt("¿Cuántos ml?"); if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderAll(); } }
        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }
        function borrarTodo() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        // Ejecutar al cargar
        init();
    </script>
</body>
</html>
