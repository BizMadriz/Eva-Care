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
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 2rem; padding: 1.5rem; shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; transition: all 0.2s; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 6px; }
        
        /* Animación suave para el timeline */
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <header class="bg-white px-6 pt-8 pb-4 sticky top-0 z-50">
        <div class="flex justify-between items-center mb-6">
            <div class="flex items-center gap-1">
                <span class="text-2xl font-extrabold tracking-tighter text-slate-900">EvaCare</span>
                <span class="w-2 h-2 rounded-full bg-blue-600 self-end mb-1.5"></span>
            </div>
            <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-slate-50 rounded-full flex items-center justify-center text-slate-400">
                <i class="fa-solid fa-ellipsis-vertical"></i>
            </button>
        </div>

        <div onclick="openModal('modal-menu')" class="bg-blue-50/50 p-4 rounded-[1.5rem] border border-blue-100/50">
            <div class="flex justify-between items-end">
                <div>
                    <h2 id="view-nombre" class="text-2xl font-extrabold text-blue-900 leading-none">---</h2>
                    <p id="view-edad" class="text-[10px] font-bold text-blue-600/60 mt-1 uppercase tracking-widest"></p>
                </div>
                <div class="text-right">
                    <p id="view-nacimiento" class="text-[9px] font-medium text-slate-400"></p>
                    <p id="view-actual" class="text-[10px] font-extrabold text-blue-500"></p>
                </div>
            </div>
        </div>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4">
        
        <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl shadow-slate-200">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-[10px] font-bold uppercase text-slate-400 tracking-[0.2em]">Próximos Eventos</h3>
                <i class="fa-solid fa-clock-rotate-left text-slate-600 text-xs"></i>
            </div>
            <div id="timeline-eventos" class="space-y-4">
                </div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-400 hidden bg-orange-50/30">
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white border border-slate-100 h-24 rounded-[2rem] flex flex-col items-center justify-center gap-2 shadow-sm active:scale-95 transition-all">
                <div class="w-10 h-10 bg-pink-50 rounded-full flex items-center justify-center text-pink-500">
                    <i class="fa-solid fa-person-breastfeeding"></i>
                </div>
                <span class="text-[10px] font-black uppercase tracking-widest text-slate-400">Pecho</span>
            </button>
            <button onclick="openModal('modal-bibe')" class="bg-white border border-slate-100 h-24 rounded-[2rem] flex flex-col items-center justify-center gap-2 shadow-sm active:scale-95 transition-all">
                <div class="w-10 h-10 bg-blue-50 rounded-full flex items-center justify-center text-blue-500">
                    <i class="fa-solid fa-bottle-water"></i>
                </div>
                <span class="text-[10px] font-black uppercase tracking-widest text-slate-400">Biberón</span>
            </button>
        </div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white p-4 rounded-2xl font-extrabold text-cyan-600 text-[10px] uppercase shadow-sm border border-slate-50">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white p-4 rounded-2xl font-extrabold text-orange-800 text-[10px] uppercase shadow-sm border border-slate-50">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white p-4 rounded-2xl font-extrabold text-purple-600 text-[10px] uppercase shadow-sm border border-slate-50">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-slate-900 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus text-xs"></i></button>
        </div>

        <div class="space-y-3 pt-2">
            <div class="flex justify-between items-center px-2">
                <h3 class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Actividad de hoy</h3>
                <span class="w-1 h-1 rounded-full bg-slate-200"></span>
            </div>
            <div id="diario-hoy" class="space-y-2"></div>
        </div>
    </main>

    <nav class="fixed bottom-6 left-6 right-6 h-16 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="w-12 h-12 rounded-full text-blue-600 flex items-center justify-center transition-all"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center transition-all"><i class="fa-solid fa-calendar-day text-lg"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center transition-all"><i class="fa-solid fa-suitcase-medical text-lg"></i></button>
    </nav>

    <script>
        let DATA = JSON.parse(localStorage.getItem('EVA_CARE_V5')) || {
            idx: 0, bebes: [], lastBibe: 0, plazo: 3
        };
        let selectedDate = null;

        const b = () => DATA.bebes[DATA.idx];

        function save() {
            localStorage.setItem('EVA_CARE_V5', JSON.stringify(DATA));
            render();
        }

        function render() {
            if (!b()) return;
            const be = b();

            // Header Update
            document.getElementById('view-nombre').innerText = be.n;
            document.getElementById('view-edad').innerText = calcularEdad(be.f);
            document.getElementById('view-nacimiento').innerText = `${be.p}kg · ${be.a}cm`;
            document.getElementById('view-actual').innerText = `Hoy: ${be.pAct || be.p}kg`;

            renderTimeline();
            
            // Diario con estilo de burbuja
            const hoyStr = new Date().toLocaleDateString();
            document.getElementById('diario-hoy').innerHTML = be.logs.filter(l => l.fecha === hoyStr).map(l => `
                <div class="bg-white p-4 rounded-2xl flex justify-between items-center shadow-sm border border-slate-50 timeline-item">
                    <span class="text-xs font-bold text-slate-700">${l.txt}</span>
                    <span class="text-[9px] font-bold text-slate-300 bg-slate-50 px-2 py-1 rounded-lg">${l.hora}</span>
                </div>
            `).join('') || "<p class='text-center text-[10px] text-slate-300 py-8 italic'>No hay registros hoy</p>";

            // Lógica de Compra
            const cBox = document.getElementById('card-compra');
            cBox.classList.toggle('hidden', be.compra.length === 0);
            document.getElementById('lista-compra').innerHTML = be.compra.map((it, i) => `
                <div class="flex justify-between items-center p-1">
                    <span class="text-[11px] font-bold text-orange-900">${it}</span>
                    <button onclick="checkCompra(${i})" class="text-orange-400 px-2"><i class="fa-solid fa-check"></i></button>
                </div>
            `).join('');

            renderCal();
        }

        // Timeline optimizado
        function renderTimeline() {
            const be = b();
            const container = document.getElementById('timeline-eventos');
            let html = "";

            // Tomas
            const ultToma = be.logs.find(l => l.txt.includes('Bibe') || l.txt.includes('Pecho'));
            if(ultToma) {
                const rest = (DATA.plazo * 3600000) - (Date.now() - ultToma.ts);
                html += `
                <div class="flex justify-between items-center timeline-item">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-full bg-blue-500/20 flex items-center justify-center text-blue-400 text-xs">
                            <i class="fa-solid fa-baby"></i>
                        </div>
                        <div>
                            <p class="text-[10px] font-black uppercase tracking-wider text-slate-400">Próxima Toma</p>
                            <p class="text-[9px] font-bold text-slate-500 italic">Última: ${ultToma.hora}</p>
                        </div>
                    </div>
                    <span class="text-xl font-black ${rest < 0 ? 'text-red-500 animate-pulse' : 'text-white'}">${formatTime(rest)}</span>
                </div>`;
            }

            // Medicinas activas
            be.meds.forEach((m, i) => {
                if(m.last > 0) {
                    const restMed = (m.last + m.h*3600000) - Date.now();
                    html += `
                    <div class="flex justify-between items-center pt-2 border-t border-white/5 timeline-item">
                        <div class="flex items-center gap-3">
                            <div class="w-8 h-8 rounded-full bg-indigo-500/20 flex items-center justify-center text-indigo-400 text-xs">
                                <i class="fa-solid fa-pills"></i>
                            </div>
                            <p class="text-[10px] font-black uppercase tracking-wider text-slate-300">${m.n}</p>
                        </div>
                        <span class="text-sm font-black ${restMed < 0 ? 'text-red-400 animate-pulse' : 'text-slate-400'}">${formatTime(restMed)}</span>
                    </div>`;
                }
            });

            container.innerHTML = html || "<div class='text-center py-4'><p class='text-[10px] font-bold text-slate-600 uppercase tracking-[0.3em]'>Todo bajo control</p></div>";
        }

        // [Funciones auxiliares iguales a V4: formatTime, calcularEdad, etc.]
        function formatTime(ms) {
            const s = Math.abs(ms);
            const h = Math.floor(s/3600000);
            const m = Math.floor((s%3600000)/60000);
            return (ms < 0 ? '+' : '') + `${h}h ${m}m`;
        }

        function calcularEdad(f) {
            const d = Math.floor((new Date() - new Date(f)) / 86400000);
            return d < 31 ? `${d} DÍAS` : `${Math.floor(d/30.4)} MESES`;
        }

        // Lógica de pestañas
        function showTab(id) {
            ['inicio', 'cal', 'salud'].forEach(t => {
                const el = document.getElementById('tab-' + t);
                if(el) el.classList.toggle('hidden', t !== id);
                document.getElementById('nav-' + t).classList.toggle('text-blue-600', t === id);
                document.getElementById('nav-' + t).classList.toggle('text-slate-300', t !== id);
            });
        }

        // Inicialización
        if(DATA.bebes.length === 0) {
            // Mostrar wizard si no hay datos
        } else {
            render();
        }
        setInterval(renderTimeline, 30000);
    </script>
</body>
</html>
