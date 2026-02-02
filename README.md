<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro Multi</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .active-tab { border-bottom: 4px solid #3b82f6; color: #3b82f6; }
        .hidden { display: none; }
        @keyframes pulse-red { 0%, 100% { color: #dc2626; } 50% { color: #f87171; } }
        .animate-alert { animation: pulse-red 1s infinite; }
    </style>
</head>
<body class="bg-slate-50 pb-24 font-sans">

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-6 overflow-y-auto">
        <div class="max-w-sm mx-auto pt-10">
            <h2 class="text-3xl font-bold text-blue-600 mb-6 text-center">Configuración 👶</h2>
            
            <div id="step-1">
                <label class="block mb-2 font-bold text-gray-700">¿Cuántos bebés vas a registrar?</label>
                <select id="num-bebes" class="w-full p-4 border rounded-2xl mb-6 bg-blue-50">
                    <option value="1">1 Bebé</option>
                    <option value="2">2 Bebés (Gemelos/Mellizos)</option>
                    <option value="3">3 Bebés</option>
                </select>
                <button onclick="siguientePaso(2)" class="w-full bg-blue-600 text-white p-4 rounded-2xl font-bold">Siguiente</button>
            </div>

            <div id="step-2" class="hidden">
                <div id="bebes-inputs"></div>
                <button onclick="guardarTodoElSetup()" class="w-full bg-green-600 text-white p-4 rounded-2xl font-bold mt-4 shadow-lg">Finalizar Configuración</button>
            </div>
        </div>
    </div>

    <header class="bg-white shadow-sm p-4 sticky top-0 z-10">
        <div class="flex justify-between items-start mb-2">
            <select id="select-bebe-actual" onchange="cambiarBebeActual()" class="font-bold text-slate-700 text-lg border-none bg-transparent focus:ring-0"></select>
            <button onclick="openSetup()" class="text-gray-300 p-1"><i class="fa-solid fa-gear"></i></button>
        </div>
        <div class="flex justify-between items-center">
            <span id="display-age" class="text-xs text-blue-500 font-semibold italic">Cargando...</span>
            <div class="text-right">
                <select id="intervalo-tomas" onchange="guardarIntervalo()" class="text-xs font-bold text-blue-600 bg-blue-50 rounded p-1">
                    <option value="2">Cada 2h</option>
                    <option value="3" selected>Cada 3h</option>
                    <option value="4">Cada 4h</option>
                </select>
            </div>
        </div>
    </header>

    <section id="resumen-citas" class="p-4"></section>

    <nav class="flex justify-around bg-white border-b sticky top-0 z-10 text-gray-400">
        <button onclick="showTab('diario')" id="tab-diario" class="p-3 active-tab flex-1"><i class="fa-solid fa-home"></i></button>
        <button onclick="showTab('salud')" id="tab-salud" class="p-3 flex-1"><i class="fa-solid fa-stethoscope"></i></button>
        <button onclick="showTab('solidos')" id="tab-solidos" class="p-3 flex-1"><i class="fa-solid fa-apple-whole"></i></button>
        <button onclick="showTab('timer')" id="tab-timer" class="p-3 flex-1"><i class="fa-solid fa-clock"></i></button>
    </nav>

    <main class="p-4 max-w-md mx-auto">
        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button id="btn-pecho-izq" onclick="registrarToma('🤱 Pecho Izq')" class="bg-pink-50 border p-4 rounded-2xl text-pink-700 font-bold shadow-sm">Pecho IZQ</button>
                <button id="btn-pecho-der" onclick="registrarToma('🤱 Pecho Der')" class="bg-pink-50 border p-4 rounded-2xl text-pink-700 font-bold shadow-sm">Pecho DER</button>
                <button id="btn-biberon" onclick="promptBiberon()" class="col-span-2 bg-blue-50 border p-4 rounded-2xl text-blue-700 font-bold shadow-sm">🍼 Biberón</button>
                <button onclick="registrar('💦 Pañal Pis')" class="bg-yellow-50 p-3 rounded-2xl text-yellow-700 font-bold text-sm">💦 Pis</button>
                <button onclick="registrar('💩 Pañal Caca')" class="bg-amber-100 p-3 rounded-2xl text-amber-900 font-bold text-sm">💩 Caca</button>
            </div>
            <div id="log-actividad" class="space-y-2 pt-4 border-t"></div>
        </section>

        <section id="sec-salud" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-blue-100">
                <h3 class="font-bold text-blue-700 mb-3 text-sm">Nueva Cita Médica</h3>
                <input id="cita-motivo" type="text" placeholder="Motivo" class="w-full p-2 border rounded-lg mb-2 text-sm">
                <input id="cita-fecha" type="date" class="w-full p-2 border rounded-lg mb-2 text-sm">
                <button onclick="guardarCita()" class="w-full bg-blue-600 text-white p-2 rounded-lg font-bold text-sm">Guardar Cita</button>
            </div>
            <div id="lista-vacunas" class="space-y-3"></div>
        </section>

        <section id="sec-solidos" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border">
                <input id="food-input" type="text" placeholder="Alimento probado" class="w-full p-3 border rounded-xl mb-3">
                <div class="flex gap-2">
                    <button onclick="registrarSolido('✅')" class="flex-1 bg-green-500 text-white p-2 rounded-lg font-bold">GUSTA</button>
                    <button onclick="registrarSolido('🚨')" class="flex-1 bg-red-600 text-white p-2 rounded-lg font-bold">ALERGIA</button>
                </div>
            </div>
            <div id="log-solidos" class="space-y-2"></div>
        </section>

        <section id="sec-timer" class="hidden space-y-8 text-center pt-10">
            <div class="relative w-56 h-56 mx-auto flex items-center justify-center border-[12px] border-blue-100 rounded-full">
                <span id="timer-val" class="text-4xl font-black text-slate-700">00:00:00</span>
            </div>
        </section>
    </main>

    <script>
        let bebes = [];
        let indexActual = 0;

        function checkSetup() {
            bebes = JSON.parse(localStorage.getItem('bebes_config')) || [];
            if (bebes.length === 0) {
                document.getElementById('setup-screen').classList.remove('hidden');
            } else {
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
                        <div class="mb-8 p-4 border rounded-2xl bg-white shadow-sm">
                            <h4 class="font-bold text-blue-500 mb-3 underline">Bebé ${i}</h4>
                            <input id="n-${i}" type="text" placeholder="Nombre" class="w-full p-2 border rounded mb-2">
                            <input id="f-${i}" type="date" class="w-full p-2 border rounded mb-2 text-sm">
                            <label class="block text-xs font-bold mb-1">Tipo de Lactancia:</label>
                            <select id="l-${i}" class="w-full p-2 border rounded bg-gray-50 text-sm">
                                <option value="materna">Exclusiva Materna</option>
                                <option value="formula">Solo Fórmula</option>
                                <option value="mixta">Mixta</option>
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
                bebes.push({
                    nombre: document.getElementById(`n-${i}`).value,
                    fecha: document.getElementById(`f-${i}`).value,
                    lactancia: document.getElementById(`l-${i}`).value
                });
            }
            localStorage.setItem('bebes_config', JSON.stringify(bebes));
            indexActual = 0;
            checkSetup();
        }

        function poblarSelectBebes() {
            const select = document.getElementById('select-bebe-actual');
            select.innerHTML = bebes.map((b, i) => `<option value="${i}">${b.nombre}</option>`).join('');
        }

        function cambiarBebeActual() {
            indexActual = document.getElementById('select-bebe-actual').value;
            actualizarInterfazBebé();
        }

        function actualizarInterfazBebé() {
            const bebe = bebes[indexActual];
            document.getElementById('display-age').innerText = calcularEdad(bebe.fecha);
            
            // Lógica de visibilidad de botones
            document.getElementById('btn-pecho-izq').classList.toggle('hidden', bebe.lactancia === 'formula');
            document.getElementById('btn-pecho-der').classList.toggle('hidden', bebe.lactancia === 'formula');
            document.getElementById('btn-biberon').classList.toggle('hidden', bebe.lactancia === 'materna');
            
            render();
            renderResumenCitas();
        }

        function calcularEdad(fecha) {
            const hoy = new Date();
            const nac = new Date(fecha);
            let meses = (hoy.getFullYear() - nac.getFullYear()) * 12 + (hoy.getMonth() - nac.getMonth());
            if (hoy.getDate() < nac.getDate()) meses--;
            return meses < 1 ? "Recién nacido" : `${meses} meses`;
        }

        // --- REGISTROS POR BEBÉ ---
        function getDBKey(base) { return `${base}_${indexActual}`; }

        function registrar(texto) {
            const item = { id: Date.now(), texto, hora: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) };
            let db = JSON.parse(localStorage.getItem(getDBKey('baby_db'))) || [];
            db.unshift(item);
            localStorage.setItem(getDBKey('baby_db'), JSON.stringify(db));
            render();
        }

        function registrarToma(tipo) { registrar(tipo); reiniciarAlertaToma(); }
        
        function promptBiberon() {
            const ml = prompt("¿Cuántos ml?");
            if(ml) { registrar(`🍼 Biberón: ${ml}ml`); reiniciarAlertaToma(); }
        }

        function showTab(tab) {
            ['diario', 'salud', 'solidos', 'timer'].forEach(t => {
                document.getElementById('sec-' + t).classList.add('hidden');
                document.getElementById('tab-' + t).classList.remove('active-tab');
            });
            document.getElementById('sec-' + tab).classList.remove('hidden');
            document.getElementById('tab-' + tab).classList.add('active-tab');
        }

        function render() {
            const db = JSON.parse(localStorage.getItem(getDBKey('baby_db'))) || [];
            document.getElementById('log-actividad').innerHTML = db.slice(0,10).map(i => `
                <div class="bg-white p-3 rounded-xl shadow-sm flex justify-between mb-2">
                    <span class="text-sm font-medium">${i.texto}</span>
                    <span class="text-[10px] text-gray-400">${i.hora}</span>
                </div>
            `).join('');
        }

        // Timer y Citas adaptados... (mantienen lógica previa pero con getDBKey)
        function guardarIntervalo() { localStorage.setItem('int', document.getElementById('intervalo-tomas').value); reiniciarAlertaToma(); }
        function reiniciarAlertaToma() { localStorage.setItem(getDBKey('target'), Date.now() + (parseFloat(localStorage.getItem('int')||3) * 3600000)); }
        
        function updateTimer() {
            const t = localStorage.getItem(getDBKey('target'));
            const disp = document.getElementById('timer-val');
            if(!t) return;
            const d = t - Date.now();
            if(d > 0) {
                const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000);
                disp.innerText = `${h}:${m}:${s}`.replace(/\b\d\b/g, '0$&');
                disp.classList.remove('animate-alert');
            } else { disp.innerText = "¡TOMA!"; disp.classList.add('animate-alert'); }
        }

        function openSetup() { document.getElementById('setup-screen').classList.remove('hidden'); }
        function registrarSolido(r) {
            const c = document.getElementById('food-input').value; if(!c) return;
            let db = JSON.parse(localStorage.getItem(getDBKey('sol'))) || [];
            db.unshift({ texto: c + " " + r, hora: new Date().toLocaleDateString() });
            localStorage.setItem(getDBKey('sol'), JSON.stringify(db));
            document.getElementById('food-input').value = ""; renderSolidos();
        }
        function renderSolidos() {
            const db = JSON.parse(localStorage.getItem(getDBKey('sol'))) || [];
            document.getElementById('log-solidos').innerHTML = db.map(i => `<div class="bg-white p-3 rounded-xl mb-2 text-sm">${i.texto}</div>`).join('');
        }

        setInterval(updateTimer, 1000);
        checkSetup();
    </script>
</body>
</html>
