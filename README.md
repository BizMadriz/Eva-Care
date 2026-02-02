# Eva-Care
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro + Calendario</title>
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

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-6 flex flex-col items-center justify-center">
        <div class="bg-blue-50 p-8 rounded-3xl shadow-xl w-full max-w-sm border border-blue-100">
            <h2 class="text-2xl font-bold text-blue-600 mb-2">¡Bienvenido! 👶</h2>
            <input id="baby-name" type="text" placeholder="Nombre del bebé" class="w-full p-3 border rounded-xl mb-4 outline-none">
            <label class="block text-left text-xs text-gray-400 ml-1 mb-1">Fecha de nacimiento</label>
            <input id="baby-dob" type="date" class="w-full p-3 border rounded-xl mb-6 outline-none">
            <button onclick="saveSetup()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-bold shadow-lg">Empezar</button>
        </div>
    </div>

    <header class="bg-white shadow-sm p-4 sticky top-0 z-10 flex justify-between items-center">
        <div class="flex flex-col">
            <span id="display-name" class="font-bold text-slate-700 text-lg leading-none">Cargando...</span>
            <span id="display-age" class="text-xs text-blue-500 font-semibold mt-1 italic">Calculando edad...</span>
        </div>
        <button onclick="openSetup()" class="text-gray-300 px-2"><i class="fa-solid fa-gear"></i></button>
    </header>

    <nav class="flex justify-around bg-white border-b sticky top-[68px] z-10 text-gray-400">
        <button onclick="showTab('diario')" id="tab-diario" class="p-3 active-tab flex-1"><i class="fa-solid fa-home"></i></button>
        <button onclick="showTab('solidos')" id="tab-solidos" class="p-3 flex-1"><i class="fa-solid fa-apple-whole"></i></button>
        <button onclick="showTab('salud')" id="tab-salud" class="p-3 flex-1"><i class="fa-solid fa-stethoscope"></i></button>
        <button onclick="showTab('timer')" id="tab-timer" class="p-3 flex-1"><i class="fa-solid fa-clock"></i></button>
    </nav>

    <main class="p-4 max-w-md mx-auto">
        
        <section id="resumen-citas" class="mb-6 space-y-2">
            </section>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="registrarToma('🤱 Pecho Izq')" class="bg-pink-50 border p-4 rounded-2xl text-pink-700 font-bold shadow-sm">Pecho IZQ</button>
                <button onclick="registrarToma('🤱 Pecho Der')" class="bg-pink-50 border p-4 rounded-2xl text-pink-700 font-bold shadow-sm">Pecho DER</button>
                <button onclick="promptBiberon()" class="col-span-2 bg-blue-50 border p-4 rounded-2xl text-blue-700 font-bold shadow-sm">Biberón</button>
                <button onclick="registrar('💦 Pañal Pis')" class="bg-yellow-50 p-3 rounded-2xl text-yellow-700 font-bold text-sm">💦 Pis</button>
                <button onclick="registrar('💩 Pañal Caca')" class="bg-amber-100 p-3 rounded-2xl text-amber-900 font-bold text-sm">💩 Caca</button>
            </div>
            <div id="log-actividad" class="space-y-2 pt-4 border-t"></div>
        </section>

        <section id="sec-solidos" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-2xl shadow-sm border">
                <input id="food-input" type="text" placeholder="¿Qué alimento probó?" class="w-full p-3 border rounded-xl mb-3 outline-none">
                <div class="flex gap-2">
                    <button onclick="registrarSolido('✅')" class="flex-1 bg-green-500 text-white p-2 rounded-lg text-xs font-bold font-bold">GUSTA</button>
                    <button onclick="registrarSolido('🚨')" class="flex-1 bg-red-600 text-white p-2 rounded-lg text-xs font-bold">ALERGIA</button>
                </div>
            </div>
            <div id="log-solidos" class="space-y-2"></div>
        </section>

        <section id="sec-salud" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-2xl shadow-sm border border-blue-100">
                <h3 class="font-bold text-blue-700 mb-3 text-sm"><i class="fa-solid fa-calendar-plus mr-2"></i>Nueva Cita Médica</h3>
                <input id="cita-motivo" type="text" placeholder="Ej: Pediatra (Revisión)" class="w-full p-2 border rounded-lg mb-2 text-sm outline-none">
                <div class="flex gap-2 mb-3">
                    <input id="cita-fecha" type="date" class="flex-1 p-2 border rounded-lg text-sm">
                    <input id="cita-hora" type="time" class="p-2 border rounded-lg text-sm">
                </div>
                <button onclick="guardarCita()" class="w-full bg-blue-600 text-white p-2 rounded-lg font-bold text-sm">Añadir al Calendario</button>
            </div>
            
            <div id="lista-vacunas" class="space-y-3">
                <h3 class="font-bold text-gray-700 text-sm italic">Calendario Vacunación 2026</h3>
            </div>
        </section>

        <section id="sec-timer" class="hidden space-y-8 text-center pt-10">
            <div class="relative w-56 h-56 mx-auto">
                <div class="absolute inset-0 border-[12px] border-blue-100 rounded-full"></div>
                <div id="timer-display" class="absolute inset-0 flex flex-col items-center justify-center">
                    <span class="text-4xl font-black text-slate-700">00:00:00</span>
                </div>
            </div>
            <select id="intervalo-tomas" onchange="guardarIntervalo()" class="bg-blue-50 p-2 rounded text-blue-600 font-bold">
                <option value="3">Cada 3h</option>
                <option value="3.5">Cada 3.5h</option>
                <option value="4">Cada 4h</option>
            </select>
        </section>

    </main>

    <script>
        // --- GESTIÓN DE CITAS ---
        function guardarCita() {
            const motivo = document.getElementById('cita-motivo').value;
            const fecha = document.getElementById('cita-fecha').value;
            const hora = document.getElementById('cita-hora').value;

            if(!motivo || !fecha) return alert("Pon el motivo y la fecha");

            const cita = { id: Date.now(), motivo, fecha, hora };
            let citas = JSON.parse(localStorage.getItem('baby_citas')) || [];
            citas.push(cita);
            // Ordenar por fecha
            citas.sort((a,b) => new Date(a.fecha + ' ' + a.hora) - new Date(b.fecha + ' ' + b.hora));
            localStorage.setItem('baby_citas', JSON.stringify(citas));
            
            document.getElementById('cita-motivo').value = "";
            renderResumenCitas();
            alert("Cita guardada");
        }

        function renderResumenCitas() {
            const citas = JSON.parse(localStorage.getItem('baby_citas')) || [];
            const hoy = new Date();
            hoy.setHours(0,0,0,0);
            const finSemana = new Date(hoy);
            finSemana.setDate(hoy.getDate() + 7);

            // Filtrar solo las futuras
            const futuras = citas.filter(c => new Date(c.fecha) >= hoy);
            const estaSemana = futuras.filter(c => new Date(c.fecha) <= finSemana);

            let html = "";
            
            if(futuras.length > 0) {
                const proxima = futuras[0];
                html += `
                    <div class="bg-blue-600 text-white p-4 rounded-2xl shadow-lg mb-4">
                        <p class="text-[10px] uppercase font-bold opacity-80">Siguiente Cita</p>
                        <p class="font-bold text-lg">${proxima.motivo}</p>
                        <p class="text-xs italic">${formatearFecha(proxima.fecha)} - ${proxima.hora || '--:--'}</p>
                    </div>
                `;
            }

            if(estaSemana.length > 0) {
                html += `<p class="text-[10px] font-bold text-gray-400 uppercase ml-2 mb-1">Esta Semana</p>`;
                estaSemana.forEach(c => {
                    html += `
                        <div class="bg-white p-3 rounded-xl shadow-sm border-l-4 border-green-500 flex justify-between items-center mb-2">
                            <span class="text-sm font-bold text-gray-700">${c.motivo}</span>
                            <span class="text-[10px] bg-green-50 text-green-600 px-2 py-1 rounded-full">${formatearFechaShort(c.fecha)}</span>
                        </div>
                    `;
                });
            }

            document.getElementById('resumen-citas').innerHTML = html;
        }

        function formatearFecha(f) {
            return new Date(f).toLocaleDateString('es-ES', { weekday: 'long', day: 'numeric', month: 'long' });
        }
        function formatearFechaShort(f) {
            return new Date(f).toLocaleDateString('es-ES', { day: 'numeric', month: 'short' });
        }

        // --- FUNCIONES CORE ---
        function checkSetup() {
            const data = JSON.parse(localStorage.getItem('baby_profile'));
            if (!data) document.getElementById('setup-screen').classList.remove('hidden');
            else {
                document.getElementById('setup-screen').classList.add('hidden');
                document.getElementById('display-name').innerText = data.nombre;
                generarVacunas(calcularEdad(data.fecha));
                renderResumenCitas();
            }
        }

        function saveSetup() {
            const nombre = document.getElementById('baby-name').value;
            const fecha = document.getElementById('baby-dob').value;
            if (nombre && fecha) {
                localStorage.setItem('baby_profile', JSON.stringify({ nombre, fecha }));
                checkSetup();
            }
        }

        function openSetup() { document.getElementById('setup-screen').classList.remove('hidden'); }

        function calcularEdad(fecha) {
            const hoy = new Date();
            const nac = new Date(fecha);
            let meses = (hoy.getFullYear() - nac.getFullYear()) * 12 + (hoy.getMonth() - nac.getMonth());
            if (hoy.getDate() < nac.getDate()) meses--;
            document.getElementById('display-age').innerText = meses < 1 ? "Recién nacido" : `${meses} meses`;
            return meses;
        }

        function showTab(tab) {
            ['diario', 'salud', 'solidos', 'timer'].forEach(t => {
                document.getElementById('sec-' + t).classList.add('hidden');
                document.getElementById('tab-' + t).classList.remove('active-tab');
            });
            document.getElementById('sec-' + tab).classList.remove('hidden');
            document.getElementById('tab-' + tab).classList.add('active-tab');
            document.getElementById('resumen-citas').style.display = (tab === 'diario') ? 'block' : 'none';
        }

        function registrar(texto) {
            const item = { id: Date.now(), texto, hora: new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) };
            let db = JSON.parse(localStorage.getItem('baby_db')) || [];
            db.unshift(item);
            localStorage.setItem('baby_db', JSON.stringify(db));
            render();
        }

        function registrarToma(tipo) { registrar(tipo); reiniciarAlertaToma(); }
        function promptBiberon() {
            const ml = prompt("¿Cuántos ml?");
            if(ml) { registrar(`🍼 Biberón: ${ml}ml`); reiniciarAlertaToma(); }
        }

        function guardarIntervalo() {
            localStorage.setItem('intervalo', document.getElementById('intervalo-tomas').value);
            reiniciarAlertaToma();
        }

        function reiniciarAlertaToma() {
            const h = parseFloat(localStorage.getItem('intervalo')) || 3;
            localStorage.setItem('target_toma', Date.now() + (h * 60 * 60 * 1000));
        }

        function updateTimer() {
            const target = localStorage.getItem('target_toma');
            const display = document.querySelector('#sec-timer span');
            if(!target) return;
            const dif = target - Date.now();
            if(dif > 0) {
                const h = Math.floor(dif/3600000), m = Math.floor((dif%3600000)/60000), s = Math.floor((dif%60000)/1000);
                display.innerText = `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
                display.classList.remove('animate-alert');
            } else {
                display.innerText = "¡TOMA!";
                display.classList.add('animate-alert');
            }
        }

        function render() {
            const db = JSON.parse(localStorage.getItem('baby_db')) || [];
            document.getElementById('log-actividad').innerHTML = db.slice(0,10).map(i => `
                <div class="bg-white p-3 rounded-xl shadow-sm flex justify-between mb-2">
                    <span class="text-sm font-medium">${i.texto}</span>
                    <span class="text-[10px] text-gray-400">${i.hora}</span>
                </div>
            `).join('');
        }

        function generarVacunas(mesesActuales) {
            const esquema = [
                { m: 2, v: "Hexavalente, Neumococo, Meningo B" },
                { m: 4, v: "Hexavalente, Neumococo, Meningo B/C" },
                { m: 11, v: "Hexavalente, Neumococo, Meningo ACWY" }
            ];
            document.getElementById('lista-vacunas').innerHTML += esquema.map(ev => `
                <div class="p-3 rounded-xl ${mesesActuales >= ev.m ? 'bg-gray-100 opacity-50' : 'bg-white shadow-sm border-l-4 border-red-400'}">
                    <p class="text-[10px] font-bold text-red-500 uppercase">A los ${ev.m} meses</p>
                    <p class="text-xs text-gray-700">${ev.v}</p>
                </div>
            `).join('');
        }

        function limpiarTodo() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        setInterval(updateTimer, 1000);
        checkSetup();
        render();
    </script>
</body>
</html>
