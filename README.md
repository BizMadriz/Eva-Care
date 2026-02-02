<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro 2026</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        .bg-baby { background-color: #f8fafc; }
        @keyframes pulse-red { 0%, 100% { color: #dc2626; } 50% { color: #f87171; } }
        .animate-alert { animation: pulse-red 1s infinite; }
    </style>
</head>
<body class="bg-baby pb-24 font-sans">

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-6 overflow-y-auto">
        <div class="max-w-sm mx-auto">
            <h2 class="text-3xl font-bold text-blue-600 mb-2 text-center">Configuración</h2>
            <p class="text-gray-500 text-center mb-8 text-sm">Personalicemos la app para tu bebé</p>
            
            <div id="step-1">
                <label class="block mb-2 font-bold text-gray-700 text-sm">¿Cuántos bebés vas a registrar?</label>
                <select id="num-bebes" class="w-full p-4 border rounded-2xl mb-6 bg-blue-50 outline-none">
                    <option value="1">1 Bebé</option>
                    <option value="2">2 Bebés</option>
                </select>
                <button onclick="siguientePaso(2)" class="w-full bg-blue-600 text-white p-4 rounded-2xl font-bold shadow-lg">Siguiente</button>
            </div>

            <div id="step-2" class="hidden">
                <div id="bebes-inputs"></div>
                <button onclick="guardarTodoElSetup()" class="w-full bg-green-600 text-white p-4 rounded-2xl font-bold mt-4 shadow-md">Finalizar y Empezar</button>
            </div>
        </div>
    </div>

    <header class="bg-white shadow-sm p-4 sticky top-0 z-10">
        <div class="flex justify-between items-center">
            <select id="select-bebe-actual" onchange="cambiarBebeActual()" class="font-bold text-slate-700 text-xl border-none bg-transparent focus:ring-0 outline-none"></select>
            <button onclick="openSetup()" class="text-gray-300 p-2"><i class="fa-solid fa-gear text-lg"></i></button>
        </div>
        <div id="display-age" class="text-xs text-blue-500 font-medium ml-1"></div>
    </header>

    <main class="p-4 max-w-md mx-auto space-y-6">
        
        <section id="agenda-semanal" class="space-y-3">
            <h3 class="text-xs font-bold text-gray-400 uppercase tracking-wider ml-1">Plan Semanal</h3>
            <div id="semana-grid" class="flex justify-between gap-1 mb-2">
                </div>
            <div id="proximas-citas" class="space-y-2">
                </div>
        </section>

        <nav class="flex justify-around bg-white rounded-2xl shadow-sm border mb-4 text-gray-400 overflow-hidden">
            <button onclick="showTab('diario')" id="tab-diario" class="p-4 active-tab flex-1"><i class="fa-solid fa-baby"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-4 flex-1"><i class="fa-solid fa-stethoscope"></i></button>
            <button onclick="showTab('solidos')" id="tab-solidos" class="p-4 flex-1"><i class="fa-solid fa-apple-whole"></i></button>
            <button onclick="showTab('timer')" id="tab-timer" class="p-4 flex-1"><i class="fa-solid fa-clock"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button id="btn-pecho-izq" onclick="registrarToma('🤱 Pecho Izq')" class="bg-pink-50 border border-pink-100 p-4 rounded-2xl text-pink-700 font-bold">Pecho IZQ</button>
                <button id="btn-pecho-der" onclick="registrarToma('🤱 Pecho Der')" class="bg-pink-50 border border-pink-100 p-4 rounded-2xl text-pink-700 font-bold">Pecho DER</button>
                <button id="btn-biberon" onclick="promptBiberon()" class="col-span-2 bg-blue-50 border border-blue-100 p-4 rounded-2xl text-blue-700 font-bold italic">🍼 Biberón</button>
                <button onclick="registrar('💦 Pañal Pis')" class="bg-yellow-50 p-4 rounded-2xl text-yellow-700 font-bold text-sm">💦 Pis</button>
                <button onclick="registrar('💩 Pañal Caca')" class="bg-orange-50 p-4 rounded-2xl text-orange-900 font-bold text-sm">💩 Caca</button>
            </div>
            <div id="log-actividad" class="space-y-2 pt-2"></div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-blue-100">
                <h3 class="font-bold text-blue-700 mb-3 text-sm">Agendar Cita Médica</h3>
                <input id="cita-motivo" type="text" placeholder="¿Qué médico o motivo?" class="w-full p-3 border rounded-xl mb-2 text-sm outline-none">
                <div class="flex gap-2">
                    <input id="cita-fecha" type="date" class="flex-1 p-3 border rounded-xl text-sm outline-none">
                    <input id="cita-hora" type="time" class="w-24 p-3 border rounded-xl text-sm outline-none">
                </div>
                <button onclick="guardarCita()" class="w-full bg-blue-600 text-white p-3 rounded-xl font-bold mt-3 text-sm shadow-md">Añadir Cita</button>
            </div>

            <div class="bg-white p-4 rounded-2xl shadow-sm border">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-bold text-gray-700 text-sm">Vacunas</h3>
                    <button onclick="nuevaVacunaExtra()" class="text-xs bg-gray-100 px-2 py-1 rounded-lg text-gray-500">+ Añadir Extra</button>
                </div>
                <div id="lista-vacunas" class="space-y-3"></div>
            </div>
        </section>

        <section id="sec-solidos" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border">
                <input id="food-input" type="text" placeholder="Ej: Introducción Plátano" class="w-full p-3 border rounded-xl mb-3">
                <div class="flex gap-2">
                    <button onclick="registrarSolido('✅')" class="flex-1 bg-green-500 text-white p-3 rounded-xl font-bold text-sm">GUSTA</button>
                    <button onclick="registrarSolido('🚨')" class="flex-1 bg-red-600 text-white p-3 rounded-xl font-bold text-sm">ALERGIA</button>
                </div>
            </div>
            <div id="log-solidos" class="space-y-2"></div>
        </section>

        <section id="sec-timer" class="hidden space-y-8 text-center pt-6">
            <div class="w-48 h-48 mx-auto flex items-center justify-center border-[10px] border-blue-50 rounded-full bg-white shadow-inner relative">
                <span id="timer-val" class="text-3xl font-black text-slate-700">00:00:00</span>
            </div>
            <div class="bg-white p-4 rounded-2xl border inline-block">
                <label class="text-xs font-bold text-gray-400 block mb-2 uppercase">Avisar cada</label>
                <select id="intervalo-tomas" onchange="guardarIntervalo()" class="font-bold text-blue-600 outline-none">
                    <option value="2">2h</option><option value="2.5">2.5h</option><option value="3" selected>3h</option><option value="3.5">3.5h</option><option value="4">4h</option>
                </select>
            </div>
        </section>
    </main>

    <script>
        let bebes = []; let indexActual = 0;

        function checkSetup() {
            bebes = JSON.parse(localStorage.getItem('bebes_config')) || [];
            if (bebes.length === 0) document.getElementById('setup-screen').classList.remove('hidden');
            else {
                document.getElementById('setup-screen').classList.add('hidden');
                poblarSelectBebes();
                actualizarInterfazBebé();
            }
        }

        function siguientePaso(paso) {
            if (paso === 2) {
                const num = document.getElementById('num-bebes').value;
                let html = '';
                for (let i = 1; i <= num; i++) {
                    html += `
                        <div class="mb-6 p-4 border-2 border-blue-50 rounded-2xl bg-white">
                            <h4 class="font-bold text-blue-500 mb-3 underline">Bebé ${i}</h4>
                            <div class="mb-3">
                                <label class="text-[10px] font-bold text-gray-400 uppercase ml-1">Nombre</label>
                                <input id="n-${i}" type="text" placeholder="Ej: Mateo" class="w-full p-2 border-b outline-none">
                            </div>
                            <div class="mb-4">
                                <label class="text-[10px] font-bold text-gray-400 uppercase ml-1">Fecha de Nacimiento</label>
                                <input id="f-${i}" type="date" class="w-full p-2 border-b outline-none text-sm">
                            </div>
                            <select id="l-${i}" class="w-full p-3 border rounded-xl bg-gray-50 text-sm font-medium">
                                <option value="materna">🍼 Lactancia Materna</option>
                                <option value="formula">🍼 Solo Fórmula</option>
                                <option value="mixta">🍼 Mixta</option>
                            </select>
                        </div>`;
                }
                document.getElementById('bebes-inputs').innerHTML = html;
                document.getElementById('step-1').classList.add('hidden');
                document.getElementById('step-2').classList.remove('hidden');
            }
        }

        function guardarTodoElSetup() {
            const num = document.getElementById('num-bebes').value;
            bebes = [];
            for (let i = 1; i <= num; i++) {
                const n = document.getElementById(`n-${i}`).value;
                const f = document.getElementById(`f-${i}`).value;
                if(n && f) bebes.push({ nombre: n, fecha: f, lactancia: document.getElementById(`l-${i}`).value });
            }
            if(bebes.length > 0) {
                localStorage.setItem('bebes_config', JSON.stringify(bebes));
                indexActual = 0; checkSetup();
            }
        }

        function poblarSelectBebes() {
            const select = document.getElementById('select-bebe-actual');
            select.innerHTML = bebes.map((b, i) => `<option value="${i}">${b.nombre}</option>`).join('');
        }

        function cambiarBebeActual() { indexActual = document.getElementById('select-bebe-actual').value; actualizarInterfazBebé(); }

        function actualizarInterfazBebé() {
            const b = bebes[indexActual];
            document.getElementById('display-age').innerText = `${calcularEdad(b.fecha)} de vida`;
            document.getElementById('btn-pecho-izq').classList.toggle('hidden', b.lactancia === 'formula');
            document.getElementById('btn-pecho-der').classList.toggle('hidden', b.lactancia === 'formula');
            document.getElementById('btn-biberon').classList.toggle('hidden', b.lactancia === 'materna');
            renderAgenda(); render(); renderVacunas(calcularEdadNum(b.fecha));
        }

        function calcularEdad(f) {
            const meses = calcularEdadNum(f);
            if(meses < 1) return "Recién nacido";
            if(meses >= 12) return `${Math.floor(meses/12)} años y ${meses%12} meses`;
            return `${meses} meses`;
        }

        function calcularEdadNum(f) {
            const hoy = new Date(); const nac = new Date(f);
            let m = (hoy.getFullYear() - nac.getFullYear()) * 12 + (hoy.getMonth() - nac.getMonth());
            if (hoy.getDate() < nac.getDate()) m--;
            return m;
        }

        // --- CALENDARIO Y CITAS ---
        function guardarCita() {
            const mot = document.getElementById('cita-motivo').value;
            const fec = document.getElementById('cita-fecha').value;
            const hor = document.getElementById('cita-hora').value;
            if(!mot || !fec) return alert("Completa fecha y motivo");
            
            let citas = JSON.parse(localStorage.getItem(`citas_${indexActual}`)) || [];
            citas.push({ id: Date.now(), mot, fec, hor });
            citas.sort((a,b) => new Date(a.fec + ' ' + a.hor) - new Date(b.fec + ' ' + b.hor));
            localStorage.setItem(`citas_${indexActual}`, JSON.stringify(citas));
            
            document.getElementById('cita-motivo').value = ""; renderAgenda();
            alert("Cita agendada");
        }

        function renderAgenda() {
            const hoy = new Date(); hoy.setHours(0,0,0,0);
            const citas = (JSON.parse(localStorage.getItem(`citas_${indexActual}`)) || []).filter(c => new Date(c.fec) >= hoy);
            
            // Grid Semanal
            let gridHtml = '';
            for(let i=0; i<7; i++) {
                const d = new Date(hoy); d.setDate(hoy.getDate() + i);
                const tiene = citas.some(c => c.fec === d.toISOString().split('T')[0]);
                gridHtml += `
                    <div class="flex-1 flex flex-col items-center p-2 rounded-xl ${i===0?'bg-blue-600 text-white shadow-md':'bg-white text-gray-400 border'}">
                        <span class="text-[8px] uppercase font-bold">${d.toLocaleDateString('es',{weekday:'short'})}</span>
                        <span class="text-xs font-black">${d.getDate()}</span>
                        ${tiene?'<div class="w-1 h-1 bg-red-400 rounded-full mt-1"></div>':''}
                    </div>`;
            }
            document.getElementById('semana-grid').innerHTML = gridHtml;

            // Listado próximas
            document.getElementById('proximas-citas').innerHTML = citas.slice(0,2).map(c => `
                <div class="bg-white p-3 rounded-xl border-l-4 border-blue-500 shadow-sm flex justify-between items-center">
                    <div>
                        <p class="text-xs font-bold text-gray-700">${c.mot}</p>
                        <p class="text-[10px] text-gray-400">${new Date(c.fec).toLocaleDateString('es',{day:'numeric', month:'short'})} - ${c.hor}</p>
                    </div>
                    <i class="fa-solid fa-chevron-right text-gray-200 text-xs"></i>
                </div>
            `).join('') || '<p class="text-[10px] text-gray-300 italic text-center">No hay citas próximas</p>';
        }

        // --- VACUNAS ---
        function renderVacunas(meses) {
            const oficiales = [
                { m: 2, n: "Hexavalente (1ª), Neumococo (1ª), Meningo B (1ª)" },
                { m: 4, n: "Hexavalente (2ª), Neumococo (2ª), Meningo B (2ª), Meningo C (1ª)" },
                { m: 11, n: "Hexavalente (3ª), Neumococo (3ª), Meningo ACWY" }
            ];
            let extras = JSON.parse(localStorage.getItem(`vac_ext_${indexActual}`)) || [];
            
            document.getElementById('lista-vacunas').innerHTML = oficiales.map(v => `
                <div class="flex items-start gap-3 p-2 border-b border-gray-50 ${meses >= v.m ? 'opacity-40' : ''}">
                    <input type="checkbox" class="mt-1">
                    <div>
                        <p class="text-xs font-bold text-gray-600">${v.n}</p>
                        <p class="text-[9px] text-blue-400 uppercase font-bold">A los ${v.m} meses</p>
                    </div>
                </div>
            `).join('') + extras.map((v, i) => `
                <div class="flex items-start gap-3 p-2 border-b border-gray-50">
                    <input type="checkbox" checked>
                    <div>
                        <p class="text-xs font-bold text-gray-600">${v}</p>
                        <p class="text-[9px] text-green-500 uppercase font-bold text-xs">Extra / Personalizada</p>
                    </div>
                </div>
            `).join('');
        }

        function nuevaVacunaExtra() {
            const v = prompt("Nombre de la vacuna:");
            if(v) {
                let e = JSON.parse(localStorage.getItem(`vac_ext_${indexActual}`)) || [];
                e.push(v); localStorage.setItem(`vac_ext_${indexActual}`, JSON.stringify(e));
                actualizarInterfazBebé();
            }
        }

        // --- FUNCIONES COMUNES ---
        function showTab(t) {
            ['diario','salud','solidos','timer'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
            document.getElementById('agenda-semanal').classList.toggle('hidden', t!=='diario');
        }

        function registrar(texto) {
            const item = { id: Date.now(), texto, hora: new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}) };
            let db = JSON.parse(localStorage.getItem(`db_${indexActual}`)) || [];
            db.unshift(item); localStorage.setItem(`db_${indexActual}`, JSON.stringify(db));
            render();
        }

        function registrarToma(tipo) { registrar(tipo); reiniciarAlertaToma(); }
        function promptBiberon() { const ml = prompt("¿Cuántos ml?"); if(ml) { registrar(`🍼 Biberón: ${ml}ml`); reiniciarAlertaToma(); } }
        function guardarIntervalo() { localStorage.setItem('int', document.getElementById('intervalo-tomas').value); reiniciarAlertaToma(); }
        function reiniciarAlertaToma() { localStorage.setItem(`target_${indexActual}`, Date.now() + (parseFloat(localStorage.getItem('int')||3)*3600000)); }
        
        function updateTimer() {
            const t = localStorage.getItem(`target_${indexActual}`);
            const disp = document.getElementById('timer-val');
            if(!t) return;
            const d = t - Date.now();
            if(d > 0) {
                const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000);
                disp.innerText = `${h}:${m}:${s}`.replace(/\b\d\b/g, '0$&');
                disp.classList.remove('animate-alert');
            } else { disp.innerText = "¡TOMA!"; disp.classList.add('animate-alert'); }
        }

        function registrarSolido(r) {
            const f = document.getElementById('food-input').value; if(!f) return;
            let db = JSON.parse(localStorage.getItem(`sol_${indexActual}`)) || [];
            db.unshift({ texto: f + " " + r, fecha: new Date().toLocaleDateString() });
            localStorage.setItem(`sol_${indexActual}`, JSON.stringify(db));
            document.getElementById('food-input').value = ""; renderSolidos();
        }

        function renderSolidos() {
            const db = JSON.parse(localStorage.getItem(`sol_${indexActual}`)) || [];
            document.getElementById('log-solidos').innerHTML = db.map(i => `<div class="bg-white p-3 rounded-xl mb-2 text-xs shadow-sm">${i.texto} <span class="text-gray-300 ml-2">${i.fecha}</span></div>`).join('');
        }

        function render() {
            const db = JSON.parse(localStorage.getItem(`db_${indexActual}`)) || [];
            document.getElementById('log-actividad').innerHTML = db.slice(0,8).map(i => `<div class="bg-white p-3 rounded-xl shadow-sm flex justify-between mb-2 text-sm"><span>${i.texto}</span><span class="text-[10px] text-gray-300">${i.hora}</span></div>`).join('');
            renderSolidos();
        }

        function openSetup() { document.getElementById('setup-screen').classList.remove('hidden'); }
        setInterval(updateTimer, 1000);
        checkSetup();
    </script>
</body>
</html>
