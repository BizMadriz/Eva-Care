<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro - 2026</title>
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
<body class="bg-baby pb-24 font-sans overflow-x-hidden">

    <div id="setup-screen" class="hidden fixed inset-0 bg-white z-50 p-8 flex flex-col justify-center">
        <div class="max-w-sm mx-auto w-full space-y-8">
            <div id="step-1" class="setup-step space-y-4">
                <h2 class="text-3xl font-black text-slate-800">¿Cómo se llama tu bebé?</h2>
                <input id="setup-nombre" type="text" placeholder="Nombre del bebé" class="w-full p-4 bg-blue-50 rounded-2xl border-none outline-none text-xl font-bold text-blue-600">
                <button onclick="multiBebeInfo()" class="text-blue-400 text-sm font-bold"><i class="fa-solid fa-circle-plus mr-2"></i>Tengo más de un bebé</button>
                <button onclick="nextStep(2)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
            </div>

            <div id="step-2" class="setup-step hidden space-y-4">
                <h2 class="text-3xl font-black text-slate-800">¿Cuándo nació?</h2>
                <input id="setup-fecha" type="date" class="w-full p-4 bg-blue-50 rounded-2xl border-none outline-none text-xl font-bold text-blue-600">
                <button onclick="nextStep(3)" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-bold shadow-xl">Continuar</button>
            </div>

            <div id="step-3" class="setup-step hidden space-y-4">
                <h2 class="text-3xl font-black text-slate-800">¿Cuánto pesó y midió al nacer?</h2>
                <div class="flex gap-2">
                    <input id="setup-peso" type="number" step="0.01" placeholder="Peso (kg)" class="w-1/2 p-4 bg-blue-50 rounded-2xl border-none outline-none font-bold">
                    <input id="setup-medida" type="number" placeholder="Talla (cm)" class="w-1/2 p-4 bg-blue-50 rounded-2xl border-none outline-none font-bold">
                </div>
                <button onclick="finalizarSetup()" class="w-full bg-green-600 text-white p-5 rounded-2xl font-bold shadow-xl">Empezar ahora</button>
            </div>
        </div>
    </div>

    <header class="bg-white p-5 shadow-sm sticky top-0 z-40">
        <div class="flex justify-between items-start">
            <div>
                <h1 id="top-nombre" class="text-2xl font-black text-slate-800 leading-none">Cargando...</h1>
                <p id="top-edad" class="text-blue-500 font-bold text-sm mt-1"></p>
            </div>
            <button onclick="openSettings()" class="text-slate-300"><i class="fa-solid fa-gear"></i></button>
        </div>
        <div class="grid grid-cols-2 gap-2 mt-4">
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="text-[9px] uppercase font-bold text-slate-400">Peso Actual</p>
                <p id="top-peso" class="text-sm font-black text-slate-700">-- kg</p>
            </div>
            <div class="bg-slate-50 p-2 rounded-xl border border-slate-100">
                <p class="text-[9px] uppercase font-bold text-slate-400">Talla Actual</p>
                <p id="top-medida" class="text-sm font-black text-slate-700">-- cm</p>
            </div>
        </div>
    </header>

    <main class="p-4 max-w-md mx-auto space-y-6">
        
        <section class="space-y-3">
            <h3 class="text-xs font-bold text-slate-400 uppercase ml-1">Historial Diario</h3>
            <div id="calendar-row" class="flex justify-between gap-1"></div>
            <div id="day-info" class="bg-white p-4 rounded-2xl shadow-sm border border-blue-50 hidden animate-fade-in">
                <div class="flex justify-between items-center mb-3">
                    <h4 id="selected-date-label" class="font-bold text-blue-600 text-sm">Hoy</h4>
                    <button onclick="closeDayInfo()" class="text-slate-300"><i class="fa-solid fa-xmark"></i></button>
                </div>
                <div id="day-content" class="space-y-2 text-xs text-slate-600"></div>
            </div>
        </section>

        <nav class="flex justify-around bg-white rounded-2xl shadow-sm border text-slate-400 overflow-hidden">
            <button onclick="showTab('diario')" id="tab-diario" class="p-4 active-tab flex-1"><i class="fa-solid fa-baby"></i></button>
            <button onclick="showTab('salud')" id="tab-salud" class="p-4 flex-1"><i class="fa-solid fa-weight-scale"></i></button>
            <button onclick="showTab('timer')" id="tab-timer" class="p-4 flex-1"><i class="fa-solid fa-clock"></i></button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-2 gap-3">
                <button onclick="registrar('🤱 Pecho Izq')" class="bg-pink-50 p-4 rounded-2xl text-pink-700 font-bold border border-pink-100">Pecho IZQ</button>
                <button onclick="registrar('🤱 Pecho Der')" class="bg-pink-50 p-4 rounded-2xl text-pink-700 font-bold border border-pink-100">Pecho DER</button>
                <div class="col-span-2 space-y-2">
                    <button id="btn-last-bib" onclick="registrarLastBiberon()" class="hidden w-full bg-blue-600 text-white p-4 rounded-2xl font-bold shadow-lg"></button>
                    <button onclick="promptBiberon()" class="w-full bg-blue-50 p-4 rounded-2xl text-blue-700 font-bold border border-blue-100 italic">🍼 Nuevo Biberón...</button>
                </div>
                <button onclick="registrar('💦 Pis')" class="bg-yellow-50 p-4 rounded-2xl text-yellow-700 font-bold">💦 Pis</button>
                <button onclick="registrar('💩 Caca')" class="bg-orange-50 p-4 rounded-2xl text-orange-900 font-bold">💩 Caca</button>
            </div>
            <div id="log-recent" class="space-y-2 pt-2"></div>
        </section>

        <section id="sec-salud" class="hidden space-y-4">
            <div class="bg-white p-5 rounded-3xl shadow-sm border border-slate-100">
                <h3 class="font-bold text-slate-800 mb-4">Actualizar Crecimiento</h3>
                <div class="space-y-3">
                    <input id="input-peso" type="number" step="0.01" placeholder="Peso (kg)" class="w-full p-4 bg-slate-50 rounded-2xl border-none outline-none font-bold">
                    <input id="input-talla" type="number" placeholder="Talla (cm)" class="w-full p-4 bg-slate-50 rounded-2xl border-none outline-none font-bold">
                    <button onclick="updateGrowth()" class="w-full bg-blue-600 text-white p-4 rounded-2xl font-bold">Guardar Cambios</button>
                </div>
            </div>
        </section>

        <section id="sec-timer" class="hidden space-y-6 text-center pt-6">
            <div class="w-48 h-48 mx-auto flex items-center justify-center border-[12px] border-blue-100 rounded-full bg-white">
                <span id="timer-val" class="text-3xl font-black text-slate-700">00:00:00</span>
            </div>
            <p class="text-xs text-slate-400 font-bold uppercase tracking-widest">Próxima toma estimada</p>
        </section>

    </main>

    <script>
        let bData = {}; 
        let currentDay = new Date().toISOString().split('T')[0];

        function init() {
            bData = JSON.parse(localStorage.getItem('baby_pro_data'));
            if(!bData) {
                document.getElementById('setup-screen').classList.remove('hidden');
            } else {
                renderHeader();
                renderCalendar();
                renderLog();
                updateTimer();
                setInterval(updateTimer, 1000);
            }
        }

        // --- SETUP ---
        function nextStep(s) {
            document.querySelectorAll('.setup-step').forEach(el => el.classList.add('hidden'));
            document.getElementById('step-'+s).classList.remove('hidden');
        }

        function finalizarSetup() {
            const data = {
                nombre: document.getElementById('setup-nombre').value,
                fecha: document.getElementById('setup-fecha').value,
                peso: document.getElementById('setup-peso').value,
                medida: document.getElementById('setup-medida').value,
                logs: {}, // Estructura: {'2026-02-02': [logs]}
                crecimiento: [{fecha: new Date().toISOString().split('T')[0], p: document.getElementById('setup-peso').value, m: document.getElementById('setup-medida').value}],
                lastBib: null
            };
            localStorage.setItem('baby_pro_data', JSON.stringify(data));
            location.reload();
        }

        function multiBebeInfo() { alert("Para gestionar más de un bebé, crea una nueva pestaña en GitHub con un nombre distinto. ¡Próximamente soporte multi-perfil!"); }

        // --- RENDER HEADER ---
        function renderHeader() {
            document.getElementById('top-nombre').innerText = bData.nombre;
            document.getElementById('top-edad').innerText = calcularEdad(bData.fecha);
            
            const lastG = bData.crecimiento[bData.crecimiento.length - 1];
            document.getElementById('top-peso').innerText = lastG.p + ' kg';
            document.getElementById('top-medida').innerText = lastG.m + ' cm';

            if(bData.lastBib) {
                const btn = document.getElementById('btn-last-bib');
                btn.innerText = `Repetir Biberón (${bData.lastBib}ml)`;
                btn.classList.remove('hidden');
            }
        }

        function calcularEdad(f) {
            const hoy = new Date(); const nac = new Date(f);
            const dif = hoy - nac;
            const dias = Math.floor(dif / (1000 * 60 * 60 * 24));
            
            if(dias < 31) return `${dias} días`;
            
            let meses = (hoy.getFullYear() - nac.getFullYear()) * 12 + (hoy.getMonth() - nac.getMonth());
            if (hoy.getDate() < nac.getDate()) meses--;
            
            if(meses < 24) return `${meses} meses`;
            return `${Math.floor(meses/12)} años y ${meses%12} meses`;
        }

        // --- CALENDARIO ---
        function renderCalendar() {
            const row = document.getElementById('calendar-row');
            row.innerHTML = '';
            const hoy = new Date();
            for(let i=6; i>=0; i--) {
                const d = new Date(); d.setDate(hoy.getDate() - i);
                const iso = d.toISOString().split('T')[0];
                const act = iso === currentDay;
                row.innerHTML += `
                    <div onclick="selectDay('${iso}')" class="flex-1 flex flex-col items-center p-3 rounded-2xl cursor-pointer ${act?'bg-blue-600 text-white shadow-lg':'bg-white text-slate-400 border border-slate-100'}">
                        <span class="text-[8px] font-bold uppercase">${d.toLocaleDateString('es',{weekday:'short'})}</span>
                        <span class="text-sm font-black">${d.getDate()}</span>
                    </div>`;
            }
        }

        function selectDay(iso) {
            currentDay = iso;
            renderCalendar();
            const infoBox = document.getElementById('day-info');
            const content = document.getElementById('day-content');
            const logs = bData.logs[iso] || [];
            
            // Buscar peso/medida ese día
            const g = bData.crecimiento.find(c => c.fecha === iso);
            
            infoBox.classList.remove('hidden');
            document.getElementById('selected-date-label').innerText = iso === new Date().toISOString().split('T')[0] ? 'Resumen de Hoy' : iso;
            
            let html = logs.length ? logs.map(l => `<div class="flex justify-between border-b border-slate-50 pb-1"><span>${l.txt}</span><span class="opacity-50">${l.h}</span></div>`).join('') : '<p class="italic opacity-50">Sin registros este día</p>';
            if(g) html = `<div class="bg-blue-50 p-2 rounded-lg mb-3 text-blue-700 font-bold text-center">Peso: ${g.p}kg | Talla: ${g.m}cm</div>` + html;
            
            content.innerHTML = html;
        }

        function closeDayInfo() { document.getElementById('day-info').classList.add('hidden'); }

        // --- LOGS ---
        function registrar(txt) {
            const hoyIso = new Date().toISOString().split('T')[0];
            const hora = new Date().toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
            
            if(!bData.logs[hoyIso]) bData.logs[hoyIso] = [];
            bData.logs[hoyIso].unshift({txt, h: hora, ts: Date.now()});
            
            localStorage.setItem('baby_pro_data', JSON.stringify(bData));
            renderLog();
            reiniciarTimer();
        }

        function promptBiberon() {
            const ml = prompt("¿Cuántos ml?");
            if(ml) {
                bData.lastBib = ml;
                registrar(`🍼 Biberón ${ml}ml`);
                renderHeader();
            }
        }

        function registrarLastBiberon() {
            if(bData.lastBib) registrar(`🍼 Biberón ${bData.lastBib}ml`);
        }

        function updateGrowth() {
            const p = document.getElementById('input-peso').value;
            const m = document.getElementById('input-talla').value;
            if(!p || !m) return;
            const hoy = new Date().toISOString().split('T')[0];
            bData.crecimiento.push({fecha: hoy, p, m});
            localStorage.setItem('baby_pro_data', JSON.stringify(bData));
            renderHeader();
            alert("¡Crecimiento actualizado!");
            showTab('diario');
        }

        function renderLog() {
            const hoyIso = new Date().toISOString().split('T')[0];
            const logs = bData.logs[hoyIso] || [];
            document.getElementById('log-recent').innerHTML = logs.slice(0,5).map(l => `
                <div class="bg-white p-3 rounded-xl flex justify-between text-sm shadow-sm border-l-4 border-blue-400 animate-fade-in">
                    <span class="font-medium">${l.txt}</span>
                    <span class="text-[10px] text-slate-300">${l.h}</span>
                </div>
            `).join('');
        }

        // --- TIMER ---
        function reiniciarTimer() {
            const target = Date.now() + (3 * 60 * 60 * 1000); // 3h por defecto
            localStorage.setItem('next_toma', target);
        }

        function updateTimer() {
            const target = localStorage.getItem('next_toma');
            const disp = document.getElementById('timer-val');
            if(!target) return;
            const d = target - Date.now();
            if(d > 0) {
                const h = Math.floor(d/3600000), m = Math.floor((d%3600000)/60000), s = Math.floor((d%60000)/1000);
                disp.innerText = `${h}:${m}:${s}`.replace(/\b\d\b/g, '0$&');
            } else {
                disp.innerText = "TOCA TOMA";
                disp.classList.add('animate-alert');
            }
        }

        function showTab(t) {
            ['diario','salud','timer'].forEach(s => {
                document.getElementById('sec-'+s).classList.toggle('hidden', s!==t);
                document.getElementById('tab-'+s).classList.toggle('active-tab', s===t);
            });
        }

        function openSettings() { if(confirm("¿Quieres borrar todo para empezar de nuevo?")) { localStorage.clear(); location.reload(); } }

        init();
    </script>
</body>
</html>
