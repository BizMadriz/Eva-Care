<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>BabyCare Pro v10</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .tab-activa { color: #3b82f6; border-bottom: 4px solid #3b82f6; }
        .oculto { display: none; }
        .btn-urgente { background: linear-gradient(135deg, #f97316 0%, #ea580c 100%); animation: pulse 2s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(0.98); opacity: 0.9; } 100% { transform: scale(1); } }
    </style>
</head>
<body class="bg-slate-50 pb-24 font-sans text-slate-900">

    <header class="bg-white p-5 shadow-lg sticky top-0 z-50">
        <div class="flex justify-between items-center mb-4">
            <div>
                <h1 class="text-2xl font-black tracking-tighter text-slate-800">BabyCare <span class="text-blue-600">Pro</span></h1>
                <p id="info-bebe" class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Panel de Control</p>
            </div>
            <button onclick="toggleAjustes()" class="bg-slate-100 p-3 rounded-2xl text-slate-500"><i class="fa-solid fa-sliders"></i></button>
        </div>

        <div class="flex gap-2">
            <button onclick="addCompra()" class="flex-1 btn-urgente text-white py-4 rounded-2xl font-black text-xs shadow-lg flex items-center justify-center gap-2">
                <i class="fa-solid fa-cart-plus text-lg"></i> ¿QUÉ FALTA COMPRAR?
            </button>
            <button id="ver-compra" onclick="irASalud()" class="oculto bg-white border-2 border-orange-500 text-orange-600 px-4 rounded-2xl font-black text-xs">
                <span id="cont-compra">0</span>
            </button>
        </div>
    </header>

    <main class="p-4 space-y-6">
        
        <section>
            <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest mb-3 ml-1">Agenda Próximos 14 días</h3>
            <div id="panel-agenda" class="space-y-2">
                </div>
        </section>

        <nav class="flex bg-white rounded-3xl shadow-sm border border-slate-200 overflow-hidden sticky top-44 z-40">
            <button id="btn-diario" onclick="verTab('diario')" class="flex-1 p-4 tab-activa font-black text-[11px]">DIARIO</button>
            <button id="btn-salud" onclick="verTab('salud')" class="flex-1 p-4 text-slate-400 font-black text-[11px]">SALUD & COMPRA</button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <button onclick="registrarAccion('🤱 Pecho')" class="w-full bg-pink-500 text-white py-8 rounded-[40px] font-black text-xl shadow-xl active:scale-95 transition-all">
                REGISTRAR PECHO
            </button>
            <div id="relojes" class="space-y-2"></div>
            <div id="log-reciente" class="bg-white/50 p-4 rounded-2xl space-y-2 text-[10px] font-bold text-slate-400"></div>
        </section>

        <section id="sec-salud" class="oculto space-y-6">
            <div class="bg-white p-6 rounded-[35px] shadow-sm border border-slate-100">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="font-black text-slate-800">Tratamientos</h3>
                    <button onclick="abrirMedicina()" class="bg-blue-600 text-white px-4 py-2 rounded-xl text-[10px] font-black">+ NUEVO</button>
                </div>
                <div id="lista-med" class="space-y-3"></div>
            </div>

            <div class="bg-orange-50 p-6 rounded-[35px] border border-orange-100">
                <h3 class="font-black text-orange-800 mb-4">Lista de la Compra</h3>
                <div id="lista-compra" class="space-y-2"></div>
            </div>
        </section>
    </main>

    <div id="modal-med" class="oculto fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-6 backdrop-blur-sm">
        <div class="bg-white w-full rounded-[40px] p-8 space-y-4">
            <h3 class="font-black text-xl text-center">Nuevo Tratamiento</h3>
            <input id="m-nombre" type="text" placeholder="Nombre (ej: Apiretal)" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <input id="m-dias" type="number" placeholder="¿Cuántos días de tratamiento?" class="w-full p-4 bg-slate-100 rounded-2xl font-bold">
            <button onclick="guardarMedicina()" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black">INICIAR</button>
            <button onclick="abrirMedicina()" class="w-full text-slate-400 font-bold text-xs uppercase">Cancelar</button>
        </div>
    </div>

    <div id="modal-ajustes" class="oculto fixed inset-0 bg-black/80 z-[100] flex justify-end">
        <div class="bg-white w-80 h-full p-8 shadow-2xl">
            <h3 class="text-2xl font-black mb-8">Ajustes</h3>
            <div class="bg-blue-50 p-5 rounded-3xl mb-6">
                <label class="text-[10px] font-black text-blue-500 block mb-2 uppercase">Horas entre tomas</label>
                <div class="flex gap-2">
                    <input id="in-h" type="number" step="0.5" class="w-20 p-3 rounded-xl font-black text-center border-none shadow-inner">
                    <button onclick="guardarIntervalo()" class="flex-1 bg-blue-600 text-white rounded-xl font-bold">OK</button>
                </div>
            </div>
            <button onclick="localStorage.clear(); location.reload();" class="text-red-400 font-bold text-xs">✕ ELIMINAR TODOS LOS DATOS</button>
            <button onclick="toggleAjustes()" class="block mt-10 font-black text-slate-300">CERRAR</button>
        </div>
    </div>

    <script>
        let APP = JSON.parse(localStorage.getItem('BABY_PRO_FINAL')) || {
            intervalo: 3,
            compra: [],
            medicinas: [],
            logs: [],
            // He cargado estos eventos para que pruebes la agenda de 14 días
            eventos: {
                "2026-02-03": ["Vacuna 12 meses (HOY)"],
                "2026-02-04": ["Cita Pediatra 10:00"],
                "2026-02-12": ["Revisión Oído"]
            }
        };

        function save() { 
            localStorage.setItem('BABY_PRO_FINAL', JSON.stringify(APP)); 
            render(); 
        }

        function render() {
            // Render Agenda
            const panelAg = document.getElementById('panel-agenda');
            let htmlAg = "";
            const hoy = new Date();
            for(let i=0; i<14; i++) {
                let d = new Date(); d.setDate(hoy.getDate() + i);
                let iso = d.toISOString().split('T')[0];
                if(APP.eventos[iso]) {
                    APP.eventos[iso].forEach(ev => {
                        htmlAg += `
                        <div class="bg-white p-4 rounded-2xl border-l-4 ${i==0?'border-orange-500':'border-blue-400'} shadow-sm">
                            <p class="text-[9px] font-black text-slate-300 uppercase">${d.toLocaleDateString('es',{weekday:'short', day:'numeric', month:'short'})}</p>
                            <p class="font-bold text-sm text-slate-800">${ev}</p>
                        </div>`;
                    });
                }
            }
            panelAg.innerHTML = htmlAg || "<p class='text-center text-slate-300 py-4 italic text-xs'>No hay eventos próximos.</p>";

            // Render Medicinas (Filtro automático de caducidad)
            const ahora = Date.now();
            APP.medicinas = APP.medicinas.filter(m => m.fin > ahora);
            document.getElementById('lista-med').innerHTML = APP.medicinas.map((m, i) => `
                <div class="bg-slate-50 p-4 rounded-2xl flex justify-between items-center border">
                    <div>
                        <p class="font-black text-sm">${m.nombre}</p>
                        <p class="text-[9px] font-bold text-slate-400 uppercase tracking-tighter">Termina: ${new Date(m.fin).toLocaleDateString()}</p>
                    </div>
                    <button onclick="APP.medicinas.splice(${i},1); save();" class="text-slate-300"><i class="fa-solid fa-trash"></i></button>
                </div>
            `).join('') || "<p class='text-center text-slate-300 py-2 text-xs'>Sin tratamientos.</p>";

            // Render Compra
            document.getElementById('ver-compra').classList.toggle('oculto', APP.compra.length === 0);
            document.getElementById('cont-compra').innerText = APP.compra.length;
            document.getElementById('lista-compra').innerHTML = APP.compra.map((it, i) => `
                <div class="bg-white p-3 rounded-xl flex justify-between items-center shadow-sm text-sm">
                    <span class="font-bold text-slate-700">${it}</span>
                    <button onclick="APP.compra.splice(${i},1); save();" class="text-green-500"><i class="fa-solid fa-check-circle text-lg"></i></button>
                </div>
            `).join('');

            // Logs
            document.getElementById('log-reciente').innerHTML = APP.logs.slice(0,3).map(l => `
                <div class="flex justify-between border-b border-slate-100 py-1">
                    <span>${l.txt}</span><span>${l.h}</span>
                </div>
            `).join('');

            document.getElementById('in-h').value = APP.intervalo;
        }

        // ACCIONES
        function addCompra() {
            const prod = prompt("¿Qué falta comprar?");
            if(prod) { APP.compra.push(prod); save(); }
        }

        function abrirMedicina() { document.getElementById('modal-med').classList.toggle('oculto'); }
        
        function guardarMedicina() {
            const n = document.getElementById('m-nombre').value;
            const d = parseInt(document.getElementById('m-dias').value);
            if(n && d) {
                APP.medicinas.push({ nombre: n, fin: Date.now() + (d * 86400000) });
                save(); abrirMedicina();
            }
        }

        function registrarAccion(txt) {
            const h = new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'});
            APP.logs.unshift({txt, h});
            save();
        }

        function guardarIntervalo() {
            APP.intervalo = document.getElementById('in-h').value;
            save(); toggleAjustes(); alert("Ritmo actualizado");
        }

        function toggleAjustes() { document.getElementById('modal-ajustes').classList.toggle('oculto'); }
        function irASalud() { verTab('salud'); }
        function verTab(id) {
            document.getElementById('sec-diario').classList.toggle('oculto', id !== 'diario');
            document.getElementById('sec-salud').classList.toggle('oculto', id !== 'salud');
            document.getElementById('btn-diario').classList.toggle('tab-activa', id === 'diario');
            document.getElementById('btn-salud').classList.toggle('tab-activa', id === 'salud');
        }

        render();
    </script>
</body>
</html>
