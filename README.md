<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro v2.2 - Lanzamiento</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        @keyframes pulse-orange { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.02); opacity: 0.8; } }
        .btn-compra-urgente { animation: pulse-orange 2s infinite; }
    </style>
</head>
<body class="bg-slate-50 pb-24 font-sans">

    <header class="bg-white p-5 shadow-md sticky top-0 z-40">
        <div class="flex justify-between items-center mb-4">
            <div>
                <h1 id="top-nombre" class="text-2xl font-black text-slate-800 leading-none">Cargando...</h1>
                <p id="top-edad" class="text-blue-500 font-bold text-[10px] mt-1 uppercase"></p>
            </div>
            <button onclick="toggleMenu()" class="bg-slate-100 p-3 rounded-2xl text-slate-600"><i class="fa-solid fa-sliders"></i></button>
        </div>
        
        <div class="flex gap-2">
            <button onclick="addCompraPrompt()" class="flex-1 bg-orange-600 text-white py-3 rounded-xl font-black text-[10px] flex items-center justify-center gap-2 shadow-lg active:scale-95">
                <i class="fa-solid fa-plus-circle"></i> AÑADIR A LA COMPRA
            </button>
            <button id="compra-status" onclick="showTab('salud')" class="hidden flex-1 bg-white border-2 border-orange-600 text-orange-600 py-3 rounded-xl font-black text-[10px] flex items-center justify-center gap-2 btn-compra-urgente">
                <i class="fa-solid fa-cart-shopping"></i> VER LISTA (<span id="compra-count">0</span>)
            </button>
        </div>
    </header>

    <div id="side-menu" class="hidden fixed inset-0 bg-black/60 z-50 flex justify-end">
        <div class="bg-white w-80 h-full p-6 shadow-2xl space-y-8">
            <div class="flex justify-between items-center">
                <h3 class="font-black text-xl">Configuración</h3>
                <button onclick="toggleMenu()" class="text-3xl">&times;</button>
            </div>
            <div class="space-y-6">
                <div class="p-5 bg-blue-50 rounded-3xl border border-blue-100">
                    <label class="text-[10px] font-black text-blue-400 uppercase tracking-widest">Ritmo de Alimentación</label>
                    <p class="text-xs text-slate-500 mb-4">Horas recomendadas entre tomas (Pecho/Bibi):</p>
                    <div class="flex items-center gap-3">
                        <input id="cfg-toma-h" type="number" step="0.5" class="w-20 p-3 rounded-2xl font-black text-center text-blue-600 border-2 border-white shadow-inner outline-none">
                        <button onclick="saveInterval()" class="flex-1 bg-blue-600 text-white py-3 rounded-2xl font-bold text-xs shadow-md">Actualizar</button>
                    </div>
                </div>
                <button onclick="alert('Funcionalidad de compartir disponible en la próxima actualización de red.')" class="w-full text-left p-4 bg-green-50 text-green-700 rounded-2xl font-bold flex items-center gap-3">
                    <i class="fa-solid fa-user-group"></i> Añadir Cuidador / Pareja
                </button>
                <hr>
                <button onclick="borrarTodo()" class="w-full text-left p-4 text-red-400 font-bold flex items-center gap-2 text-xs">
                    <i class="fa-solid fa-trash-alt"></i> ELIMINAR TODOS LOS DATOS
                </button>
            </div>
        </div>
    </div>

    <main class="p-4 max-w-md mx-auto space-y-6">
        
        <section class="space-y-3">
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest ml-1">Eventos y Vacunas (14 días)</h3>
            <div id="upcoming-events-panel" class="space-y-2">
                </div>
        </section>

        <section class="space-y-3">
            <div id="timers-container" class="space-y-2"></div>
        </section>

        <nav class="flex justify-around bg-white rounded-3xl shadow-xl border border-slate-100 overflow-hidden sticky top-[185px] z-30">
            <button onclick="showTab('diario')" id="tab-diario" class="p-5 active-tab flex-1"><i class="fa-solid fa-baby"></i></button>
            <button onclick="showTab('calendario')" id="tab-calendario" class="p-5 flex-1"><i class="fa-solid fa-calendar-check"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-5 flex-1"><i class="fa-solid fa-kit-medical"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="openPechoModal()" class="col-span-2 bg-pink-500 text-white p-6 rounded-[32px] font-black shadow-lg flex items-center justify-center gap-3">
                    <i class="fa-solid fa-breast-pumping text-2xl"></i> REGISTRAR PECHO
                </button>
                <button id="btn-last-bib" onclick="registrarLastBiberon()" class="hidden col-span-2 bg-blue-600 text-white p-4 rounded-2xl font-bold shadow-md"></button>
                <button onclick="promptBiberon()" class="col-span-2 bg-blue-100 text-blue-700 p-4 rounded-2xl font-bold border border-blue-200">🍼 Nuevo Biberón...</button>
                <button onclick="registrar('💦 Pis')" class="bg-yellow-100 text-yellow-700 p-4 rounded-2xl font-bold">💦 Pis</button>
                <button onclick="registrar('💩 Caca')" class="bg-orange-100 text-orange-900 p-4 rounded-2xl font-bold">💩 Caca</button>
            </div>
            <div id="log-recent" class="space-y-2 opacity-60"></div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-white p-6 rounded-[32px] shadow-sm border border-slate-100">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-black text-slate-800 text-sm">Tratamientos Médicos</h3>
                    <button onclick="openMedModal()" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-[10px] font-black">+ NUEVO</button>
                </div>
                <div id="lista-medicinas" class="space-y-3"></div>
            </div>

            <div class="bg-orange-50 p-6 rounded-[32px] border border-orange-100">
                <h3 class="font-black text-orange-800 text-sm mb-4">Lista de la Compra</h3>
                <div id="lista-compra" class="space-y-2"></div>
            </div>
        </section>

        <section id="sec-calendario" class="hidden">
            <div class="bg-white p-5 rounded-3xl shadow-sm mb-4">
                <div id="month-name" class="text-center font-black uppercase text-xs mb-4 text-blue-600"></div>
                <div id="days-container" class="grid grid-cols-7 gap-1"></div>
            </div>
            <div id="day-detail" class="hidden p-4 bg-blue-50 rounded-2xl">
                <div id="detail-date" class="font-black text-xs text-blue-800 mb-3"></div>
                <div id="detail-events" class="space-y-2"></div>
                <button onclick="openEventModal()" class="w-full mt-3 bg-white text-blue-600 p-3 rounded-xl font-bold text-[10px] border border-blue-200">Añadir Cita/Evento</button>
            </div>
        </section>
    </main>

    <div id="med-modal" class="hidden fixed inset-0 bg-black/70 z-50 flex items-center justify-center p-6 backdrop-blur-sm">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <h3 class="font-black text-xl">Iniciar Tratamiento</h3>
            <input id="m-nombre" type="text" placeholder="Medicamento (ej: Apiretal)" class="w-full p-4 bg-slate-100 rounded-2xl outline-none font-bold">
            <div class="flex gap-2">
                <input id="m-dosis" type="text" placeholder="Dosis (ej: 0.8ml)" class="w-1/2 p-4 bg-slate-100 rounded-2xl outline-none font-bold">
                <input id="m-cada" type="number" placeholder="Cada (horas)" class="w-1/2 p-4 bg-slate-100 rounded-2xl outline-none font-bold">
            </div>
            <input id="m-dias" type="number" placeholder="Duración (días totales)" class="w-full p-4 bg-slate-100 rounded-2xl outline-none font-bold">
            <button onclick="saveMedicina()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black shadow-lg">GUARDAR</button>
            <button onclick="closeMedModal()" class="w-full text-slate-400 font-bold text-xs uppercase">Cerrar</button>
        </div>
    </div>

    <div id="pecho-modal" class="hidden fixed inset-0 bg-black/70 z-50 flex items-end">
        <div class="bg-white w-full p-10 rounded-t-[50px] space-y-6">
            <h3 class="font-black text-center text-xl">¿Qué lado toca?</h3>
            <div class="grid grid-cols-3 gap-3">
                <button onclick="registrarPecho('Izquierdo')" class="bg-pink-50 text-pink-600 p-6 rounded-3xl font-black border-2 border-pink-100">IZQ</button>
                <button onclick="registrarPecho('Ambos')" class="bg-purple-50 text-purple-600 p-6 rounded-3xl font-black border-2 border-purple-100">AMBOS</button>
                <button onclick="registrarPecho('Derecho')" class="bg-pink-50 text-pink-600 p-6 rounded-3xl font-black border-2 border-pink-100">DER</button>
            </div>
            <button onclick="closePechoModal()" class="w-full text-slate-300 font-bold uppercase text-xs">Cancelar</button>
        </div>
    </div>

    <script>
        let bData = { nombre: "Bebé", fecha: "2025-01-01", logs: {}, medicinas: [], eventos: {}, compra: [], intervaloToma: 3 };

        function init() {
            const saved = localStorage.getItem('baby_pro_v2_2');
            if(saved) bData = JSON.parse(saved);
            
            // Limpieza automática de medicinas caducadas
            const ahora = Date.now();
            bData.medicinas = bData.medicinas.filter(m => !m.finTratamiento || m.finTratamiento > ahora);
            save();

            renderAll();
            setInterval(updateTimers, 1000);
        }

        function save() { localStorage.setItem('baby_pro_v2_2', JSON.stringify(bData)); }

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

        function renderUpcomingEvents() {
            const container = document.getElementById('upcoming-events-panel');
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
            container.innerHTML = html || '<p class="text-[10px] text-center text-slate-300 py-4 italic">No hay citas en los próximos 14 días</p>';
        }

        function saveMedicina() {
            const n = document.getElementById('m-nombre').value;
            const ds = document.getElementById('m-dosis').value;
            const c = parseFloat(document.getElementById('m-cada').value);
            const d = parseInt(document.getElementById('m-dias').value);
            if(n && c && d) {
                const fin = Date.now() + (d * 24 * 60 * 60 * 1000);
                bData.medicinas.push({ nombre: n, dosis: ds, cada: c, finTratamiento: fin, ultimaToma: null });
                save(); renderMedicina(); closeMedModal();
            }
        }

        function renderMedicina() {
            document.getElementById('lista-medicinas').innerHTML = bData.medicinas.map((m, i) => `
                <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center border border-slate-100">
                    <div>
                        <p class="font-black text-slate-800 text-sm">${m.nombre} <span class="text-blue-500">${m.dosis}</span></p>
                        <p class="text-[9px] text-slate-400 font-bold uppercase">Caduca: ${new Date(m.finTratamiento).toLocaleDateString()}</p>
                    </div>
                    <button onclick="tomarMedicina(${i})" class="bg-blue-600 text-white w-10 h-10 rounded-xl shadow-lg active:scale-90 transition-transform">
                        <i class="fa-solid fa-check"></i>
                    </button>
                </div>
            `).join('') || '<p class="text-center text-xs text-slate-300 italic">Sin tratamientos activos</p>';
        }

        function tomarMedicina(i) {
            bData.medicinas[i].ultimaToma = Date.now();
            registrar(`💊 ${bData.medicinas[i].nombre} (${bData.medicinas[i].dosis})`);
            save(); renderMedicina();
        }

        function addCompraPrompt() {
            const it = prompt("¿Qué falta comprar?");
            if(it) { bData.compra.push(it); save(); renderCompra(); }
        }

        function renderCompra() {
            const count = bData.compra.length;
            document.getElementById('compra-status').classList.toggle('hidden', count === 0);
            document.getElementById('compra-count').innerText = count;
            document.getElementById('lista-compra').innerHTML = bData.compra.map((it, i) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center text-xs shadow-sm">
                    <span class="font-bold text-slate-700">${it}</span>
                    <button onclick="borrarCompra(${i})" class="text-green-500 font-black"><i class="fa-solid fa-check-double"></i></button>
                </div>
            `).join('');
        }

        function updateTimers() {
            const container = document.getElementById('timers-container');
            let html = ""; const ahora = Date.now();
            const lt = localStorage.getItem('last_toma_time');
            if(lt) {
                const diff = (parseInt(lt) + (bData.intervaloToma * 3600000)) - ahora;
                html += renderTimerElement("🍼 Próxima Alimentación", diff, "blue");
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
            if(s <= 0) return `<div class="bg-red-500 text-white p-3 rounded-xl flex justify-between font-black text-[10px] animate-pulse"><span>${lbl}</span><span>TOCA YA</span></div>`;
            const h = Math.floor(s/3600); const m = Math.floor((s%3600)/60); const sc = s%60;
            return `<div class="bg-white p-3 rounded-xl border flex justify-between text-[10px] font-bold text-slate-500 shadow-sm">
                <span>${lbl}</span><span class="text-${col}-600 font-mono">${h}:${String(m).padStart(2,'0')}:${String(sc).padStart(2,'0')}</span>
            </div>`;
        }

        function saveInterval() {
            bData.intervaloToma = parseFloat(document.getElementById('cfg-toma-h').value);
            save(); alert("Intervalo actualizado"); toggleMenu();
        }

        // Resto de funciones básicas
        function calcularEdad(f) { const d = Math.floor((new Date() - new Date(f))/86400000); return d < 30 ? d+" DÍAS" : Math.floor(d/30.44)+" MESES"; }
        function registrar(t) { const iso = new Date().toISOString().split('T')[0]; const h = new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}); if(!bData.logs[iso]) bData.logs[iso]=[]; bData.logs[iso].unshift({txt:t, h}); if(t.includes('Biberón') || t.includes('Pecho')) localStorage.setItem('last_toma_time', Date.now()); save(); renderLog(); }
        function renderLog() { const hoy = new Date().toISOString().split('T')[0]; const logs = bData.logs[hoy] || []; document.getElementById('log-recent').innerHTML = logs.slice(0,3).map(l => `<div class="bg-white p-2 rounded-lg flex justify-between text-[9px] border-l-2 border-blue-200"><span>${l.txt}</span><span class="text-slate-300">${l.h}</span></div>`).join(''); }
        function showTab(t) { ['diario','calendario','salud'].forEach(s => { document.getElementById('sec-'+s).classList.toggle('hidden', s!==t); document.getElementById('tab-'+s).classList.toggle('active-tab', s===t); }); }
        function toggleMenu() { document.getElementById('side-menu').classList.toggle('hidden'); }
        function openMedModal() { document.getElementById('med-modal').classList.remove('hidden'); }
        function closeMedModal() { document.getElementById('med-modal').classList.add('hidden'); }
        function openPechoModal() { document.getElementById('pecho-modal').classList.remove('hidden'); }
        function closePechoModal() { document.getElementById('pecho-modal').classList.add('hidden'); }
        function registrarPecho(l) { registrar("🤱 Pecho: "+l); closePechoModal(); }
        function promptBiberon() { const ml = prompt("ml?"); if(ml) { bData.lastBib = ml; registrar(`🍼 Biberón ${ml}ml`); renderAll(); } }
        function registrarLastBiberon() { if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`); }
        function borrarCompra(i) { bData.compra.splice(i,1); save(); renderCompra(); }
        function borrarTodo() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        init();
    </script>
</body>
</html>
