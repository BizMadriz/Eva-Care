<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>EvaCare</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; background-color: #f8fafc; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 1.5rem; padding: 1.25rem; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05); }
        .nav-btn.active { color: #2563eb; }
        .nav-btn.active i { transform: translateY(-4px); transition: all 0.3s; }
    </style>
</head>
<body class="pb-32">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[9999] flex flex-col p-8 hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-black italic text-slate-800">EvaCare</h2>
            <p class="text-slate-400 font-bold text-xs uppercase tracking-widest">Bienvenida, introduce los datos</p>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-5 bg-slate-100 rounded-2xl text-xl font-bold outline-none border-2 border-transparent focus:border-blue-500">
            <button onclick="nextStep(1, 2)" class="bg-blue-600 text-white p-5 rounded-2xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-bold">¿Cuándo nació?</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-2xl text-xl font-bold outline-none">
            <button onclick="nextStep(2, 3)" class="bg-blue-600 text-white p-5 rounded-2xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-bold">Peso y Talla</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Peso (kg)" class="p-5 bg-slate-100 rounded-2xl font-bold outline-none">
                <input id="setup-a" type="number" placeholder="Talla (cm)" class="p-5 bg-slate-100 rounded-2xl font-bold outline-none">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-5 rounded-2xl font-black uppercase text-center">Empezar</button>
        </div>
    </div>

    <div id="app-content" class="hidden">
        
        <main id="tab-inicio" class="p-5 space-y-4">
            <div class="flex justify-between items-center py-4">
                <h1 class="text-2xl font-black italic">EvaCare.</h1>
                <button onclick="reiniciarTodo()" class="text-[10px] font-bold bg-red-50 text-red-500 px-3 py-1 rounded-lg">REINICIAR</button>
            </div>

            <div id="timer-toma" class="bg-slate-900 rounded-[2rem] p-6 text-white shadow-xl flex justify-between items-center">
                <div>
                    <p class="text-[10px] font-bold text-slate-500 uppercase tracking-tighter">Próxima toma</p>
                    <p id="timer-val" class="text-2xl font-black mt-1">--:--</p>
                </div>
                <i class="fa-solid fa-clock-rotate-left text-slate-700 text-2xl"></i>
            </div>

            <div class="grid grid-cols-4 gap-2">
                <button onclick="logEvent('💦 Pis')" class="bg-white py-4 rounded-xl font-bold text-cyan-600 text-[10px]">PIS</button>
                <button onclick="logEvent('💩 Caca')" class="bg-white py-4 rounded-xl font-bold text-orange-700 text-[10px]">CACA</button>
                <button onclick="logEvent('✨ Aseo')" class="bg-white py-4 rounded-xl font-bold text-purple-600 text-[10px]">ASEO</button>
                <button onclick="promptCompra()" class="bg-slate-800 text-white rounded-xl"><i class="fa-solid fa-cart-shopping text-xs"></i></button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="logEvent('🤱 Pecho')" class="card flex flex-col items-center gap-2"><i class="fa-solid fa-person-breastfeeding text-pink-500"></i><span class="text-[10px] font-bold">PECHO</span></button>
                <button onclick="logEvent('🍼 Biberón')" class="card flex flex-col items-center gap-2"><i class="fa-solid fa-bottle-water text-blue-500"></i><span class="text-[10px] font-bold">BIBERÓN</span></button>
            </div>

            <div id="logs-hoy" class="space-y-2"></div>
        </main>

        <main id="tab-comida" class="hidden p-5 space-y-4">
            <h2 class="text-2xl font-black mt-4">Alimentos</h2>
            <button onclick="registrarAlimento()" class="w-full bg-orange-500 text-white p-5 rounded-2xl font-black flex justify-between items-center">
                <span>Nuevo Alimento</span><i class="fa-solid fa-apple-whole"></i>
            </button>
            <div id="lista-comidas" class="space-y-2"></div>
        </main>

        <main id="tab-salud" class="hidden p-5 space-y-4">
            <h2 class="text-2xl font-black mt-4">Salud</h2>
            <div class="card border-l-4 border-indigo-500 flex justify-between items-center">
                <div><p id="info-bebe-n" class="font-black text-indigo-600 uppercase"></p><p id="info-bebe-m" class="text-xs text-slate-400"></p></div>
                <i class="fa-solid fa-baby text-slate-200 text-2xl"></i>
            </div>
            <div id="lista-meds" class="space-y-2"></div>
        </main>

        <main id="tab-agenda" class="hidden p-5 space-y-4">
            <h2 class="text-2xl font-black mt-4">Agenda</h2>
            <div id="cal-render" class="card grid grid-cols-7 gap-1 text-center text-xs font-bold"></div>
        </main>

        <nav class="fixed bottom-0 left-0 right-0 bg-white border-t flex justify-around p-4 z-[1000]">
            <button onclick="showTab('inicio')" id="btn-inicio" class="nav-btn active flex flex-col items-center"><i class="fa-solid fa-house"></i><span class="text-[8px] font-bold mt-1 uppercase">Inicio</span></button>
            <button onclick="showTab('comida')" id="btn-comida" class="nav-btn text-slate-300 flex flex-col items-center"><i class="fa-solid fa-carrot"></i><span class="text-[8px] font-bold mt-1 uppercase">Nutrición</span></button>
            <button onclick="showTab('salud')" id="btn-salud" class="nav-btn text-slate-300 flex flex-col items-center"><i class="fa-solid fa-heart-pulse"></i><span class="text-[8px] font-bold mt-1 uppercase">Salud</span></button>
            <button onclick="showTab('agenda')" id="btn-agenda" class="nav-btn text-slate-300 flex flex-col items-center"><i class="fa-solid fa-calendar"></i><span class="text-[8px] font-bold mt-1 uppercase">Agenda</span></button>
        </nav>
    </div>

    <script>
        let DB = JSON.parse(localStorage.getItem('EVACARE_V30')) || { bebe: null, logs: [], alimentos: [] };

        function nextStep(act, sig) {
            const inp = document.getElementById(`setup-${act === 1 ? 'n' : (act === 2 ? 'f' : 'p')}`);
            if(!inp.value) return;
            document.getElementById(`step-${act}`).classList.add('hidden');
            document.getElementById(`step-${sig}`).classList.remove('hidden');
        }

        function finishSetup() {
            DB.bebe = {
                n: document.getElementById('setup-n').value,
                f: document.getElementById('setup-f').value,
                p: document.getElementById('setup-p').value,
                a: document.getElementById('setup-a').value
            };
            localStorage.setItem('EVACARE_V30', JSON.stringify(DB));
            location.reload();
        }

        function render() {
            if(!DB.bebe) {
                document.getElementById('setup-wizard').classList.remove('hidden');
                return;
            }
            document.getElementById('app-content').classList.remove('hidden');
            
            // Render logs de hoy
            const hoy = new Date().toLocaleDateString();
            document.getElementById('logs-hoy').innerHTML = DB.logs
                .filter(l => l.fecha === hoy)
                .map(l => `<div class="bg-white p-3 rounded-xl shadow-sm text-xs flex justify-between"><b>${l.txt}</b><span class="text-slate-300">${l.hora}</span></div>`)
                .join('');

            // Info Salud
            document.getElementById('info-bebe-n').innerText = DB.bebe.n;
            document.getElementById('info-bebe-m').innerText = `${DB.bebe.p}kg · ${DB.bebe.a}cm`;
        }

        function logEvent(txt) {
            const h = new Date().toLocaleTimeString([], {hour: '2-digit', minute: '2-digit'});
            DB.logs.unshift({txt, hora: h, fecha: new Date().toLocaleDateString(), ts: Date.now()});
            localStorage.setItem('EVACARE_V30', JSON.stringify(DB));
            render();
        }

        function registrarAlimento() {
            const al = prompt("¿Qué alimento nuevo probó?");
            if(al) {
                DB.alimentos.unshift({nombre: al, fecha: new Date().toLocaleDateString()});
                logEvent(`🍴 Probó: ${al}`);
                renderAlimentos();
            }
        }

        function renderAlimentos() {
            document.getElementById('lista-comidas').innerHTML = DB.alimentos.map(a => `
                <div class="card flex justify-between items-center"><b class="text-orange-600 text-xs uppercase">${a.nombre}</b><span class="text-[9px] text-slate-300">${a.fecha}</span></div>
            `).join('');
        }

        function showTab(id) {
            ['inicio', 'comida', 'salud', 'agenda'].forEach(t => {
                document.getElementById('tab-'+t).classList.toggle('hidden', t !== id);
                document.getElementById('btn-'+t).classList.toggle('active', t === id);
                document.getElementById('btn-'+t).classList.toggle('text-slate-300', t !== id);
            });
            if(id === 'comida') renderAlimentos();
        }

        function reiniciarTodo() { if(confirm("¿Borrar todo?")) { localStorage.clear(); location.reload(); } }

        render();
    </script>
</body>
</html>
