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
        .card { background: white; border-radius: 2rem; padding: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; position: relative; cursor: pointer; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 6px; }
        .nav-active { color: #2563eb !important; }
    </style>
</head>
<body class="text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[2000] flex flex-col p-8 hidden">
        <div class="flex items-center gap-2 mb-12">
            <span class="text-2xl font-black tracking-tighter italic text-slate-800">EvaCare</span>
            <span class="w-2 h-2 bg-blue-600 rounded-full"></span>
        </div>
        
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold text-slate-900">Bienvenida. <br><span class="text-blue-600">¿Cómo se llama?</span></h2>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold border-none outline-none focus:ring-2 ring-blue-500">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase shadow-lg shadow-blue-100">Siguiente</button>
        </div>

        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold text-slate-900">¿Cuándo nació?</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold border-none outline-none">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase shadow-lg shadow-blue-100">Siguiente</button>
        </div>

        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold text-slate-900">Medidas al nacer</h2>
            <div class="grid grid-cols-2 gap-4">
                <div class="space-y-2">
                    <p class="text-[10px] font-black text-slate-400 uppercase ml-2">Peso (kg)</p>
                    <input id="setup-p" type="number" step="0.1" placeholder="3.5" class="w-full p-5 bg-slate-100 rounded-3xl font-bold border-none outline-none">
                </div>
                <div class="space-y-2">
                    <p class="text-[10px] font-black text-slate-400 uppercase ml-2">Talla (cm)</p>
                    <input id="setup-a" type="number" placeholder="50" class="w-full p-5 bg-slate-100 rounded-3xl font-bold border-none outline-none">
                </div>
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black uppercase shadow-lg shadow-slate-200">Empezar</button>
        </div>
    </div>

    <header id="main-header" class="p-6 pb-2 hidden">
        <div class="flex justify-between items-center mb-6">
            <div class="flex items-center gap-1">
                <span class="text-2xl font-extrabold tracking-tighter text-slate-900 italic">EvaCare</span>
                <span class="w-2 h-2 rounded-full bg-blue-600 mb-1"></span>
            </div>
            <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm border border-slate-100">
                <i class="fa-solid fa-bars-staggered"></i>
            </button>
        </div>

        <div class="bg-white p-6 rounded-[2.5rem] shadow-sm border border-slate-50">
            <div class="flex justify-between items-start">
                <div>
                    <h2 id="view-nombre" class="text-4xl font-black text-blue-600 italic leading-none mb-2">---</h2>
                    <p id="view-edad" class="text-[10px] font-black text-slate-400 uppercase tracking-widest"></p>
                </div>
            </div>
            <div class="mt-6 flex justify-between items-center border-t border-slate-50 pt-4">
                <div>
                    <p class="text-[8px] font-black text-slate-300 uppercase">Nacimiento</p>
                    <p id="view-nacimiento" class="text-[11px] font-bold text-slate-500"></p>
                </div>
                <div class="text-right">
                    <p class="text-[8px] font-black text-blue-300 uppercase">Actual (Hoy)</p>
                    <p id="view-medidas-act" class="text-[11px] font-bold text-blue-600"></p>
                </div>
            </div>
        </div>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4 hidden">
        <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
            <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4">Próximos Eventos</h3>
            <div id="timeline-eventos" class="space-y-4"></div>
        </div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] uppercase shadow-sm border border-slate-50">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] uppercase shadow-sm border border-slate-50">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] uppercase shadow-sm border border-slate-50">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-slate-900 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i>
                <span class="text-[10px] font-black uppercase text-slate-400">Pecho</span>
            </button>
            <button onclick="openModal('modal-bibe')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-bottle-water text-blue-500 text-xl"></i>
                <span class="text-[10px] font-black uppercase text-slate-400">Biberón</span>
            </button>
        </div>

        <div id="diario-hoy" class="space-y-2"></div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-6">
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <button onclick="changeMonth(-1)" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400"><i class="fa-solid fa-chevron-left"></i></button>
                <h3 id="cal-mes-titulo" class="font-black text-slate-900 uppercase tracking-widest text-sm">---</h3>
                <button onclick="changeMonth(1)" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400"><i class="fa-solid fa-chevron-right"></i></button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>
        <div id="agenda-completa" class="space-y-2"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <div class="grid grid-cols-1 gap-3">
            <button onclick="openModal('modal-add-med')" class="w-full bg-indigo-600 text-white p-6 rounded-[2.5rem] font-black flex items-center justify-between shadow-lg">
                <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Tratamiento</p><p class="text-lg">Nueva Medicina</p></div>
                <i class="fa-solid fa-pills text-2xl opacity-40"></i>
            </button>
            <button onclick="openModal('modal-add-crecimiento')" class="w-full bg-emerald-600 text-white p-6 rounded-[2.5rem] font-black flex items-center justify-between shadow-lg">
                <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Crecimiento</p><p class="text-lg">Peso y Talla</p></div>
                <i class="fa-solid fa-scale-balanced text-2xl opacity-40"></i>
            </button>
        </div>
        <div id="lista-salud" class="space-y-3 pt-4"></div>
    </main>

    <nav id="main-nav" class="fixed bottom-6 left-6 right-6 h-16 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-4 z-[1000] hidden">
        <button onclick="showTab('inicio')" id="nav-inicio" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center"><i class="fa-solid fa-calendar-day text-lg"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="w-12 h-12 rounded-full text-slate-300 flex items-center justify-center"><i class="fa-solid fa-suitcase-medical text-lg"></i></button>
    </nav>

    <div id="modal-add-med" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 class="text-xs font-black text-indigo-600 uppercase text-center tracking-widest">Añadir Medicina</h3>
            <input id="m-n" type="text" placeholder="Medicamento" class="w-full p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none">
            <div class="grid grid-cols-2 gap-2">
                <input id="m-q" type="text" placeholder="Dosis" class="p-4 bg-slate-50 rounded-2xl font-bold">
                <input id="m-h" type="number" placeholder="Cada (h)" class="p-4 bg-slate-50 rounded-2xl font-bold">
            </div>
            <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black uppercase">Guardar</button>
            <button onclick="closeModal('modal-add-med')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-add-crecimiento" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 class="text-xs font-black text-emerald-600 uppercase text-center tracking-widest">Actualizar Peso/Talla</h3>
            <div class="grid grid-cols-2 gap-2">
                <input id="up-p" type="number" step="0.1" placeholder="Peso kg" class="p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none">
                <input id="up-a" type="number" placeholder="Talla cm" class="p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none">
            </div>
            <button onclick="updateCrecimiento()" class="w-full bg-emerald-600 text-white p-5 rounded-2xl font-black uppercase">Actualizar</button>
            <button onclick="closeModal('modal-add-crecimiento')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <button id="btn-rep-bibe" onclick="addBibe(DATA.lastBibe)" class="w-full bg-blue-50 text-blue-600 p-6 rounded-3xl font-black uppercase text-xs">Sin registros</button>
            <input id="bibe-ml" type="number" placeholder="ml" class="w-full p-6 bg-slate-100 rounded-3xl text-center text-3xl font-black outline-none">
            <button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Registrar</button>
            <button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-end">
        <div class="bg-white w-full p-10 rounded-t-[3rem] grid grid-cols-3 gap-4">
            <button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs">IZQ</button>
            <button onclick="addLog('🤱 Ambos'); closeModal('modal-pecho')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-700 text-[10px]">AMBOS</button>
            <button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-50 p-8 rounded-3xl font-black text-pink-600 text-xs">DER</button>
            <button onclick="closeModal('modal-pecho')" class="col-span-3 text-slate-300 font-black p-4 text-center uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-cal" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 id="cal-modal-fecha" class="font-black text-center text-blue-600 uppercase text-[10px] tracking-widest"></h3>
            <input id="cal-ev-txt" type="text" placeholder="¿Qué evento?" class="w-full p-4 bg-slate-100 rounded-2xl font-bold border-none outline-none">
            <input id="cal-ev-hora" type="time" class="w-full p-4 bg-slate-100 rounded-2xl font-bold text-center border-none">
            <button onclick="saveCita()" class="w-full bg-slate-900 text-white p-5 rounded-2xl font-black uppercase">Guardar</button>
            <button onclick="closeModal('modal-cal')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[3000] flex justify-end">
        <div class="bg-white w-4/5 h-full p-8 flex flex-col">
            <h2 class="text-3xl font-extrabold italic mb-10">Ajustes</h2>
            <div class="flex-1 space-y-4">
                <div class="bg-slate-50 p-6 rounded-3xl">
                    <p class="text-[10px] font-black text-blue-600 uppercase mb-2">Recordatorio tomas (h)</p>
                    <input id="set-plazo" type="number" class="w-full p-4 rounded-xl font-bold">
                </div>
                <button onclick="
