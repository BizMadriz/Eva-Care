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
        body { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 2rem; padding: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; position: relative; cursor: pointer; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 6px; }
        .btn-reac.active { background-color: #fef3c7; border: 2px solid #f59e0b; transform: scale(1.1); }
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[2000] flex flex-col p-8 hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold tracking-tighter italic">EvaCare</h2>
            <input id="setup-n" type="text" placeholder="Nombre" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold border-none outline-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Kg" class="p-5 bg-slate-100 rounded-3xl font-bold"><input id="setup-a" type="number" placeholder="cm" class="p-5 bg-slate-100 rounded-3xl font-bold">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black uppercase">Finalizar</button>
        </div>
    </div>

    <main id="tab-inicio" class="p-4 space-y-4">
        <header class="pt-4 pb-2">
            <div class="flex justify-between items-center mb-6">
                <span class="text-2xl font-extrabold tracking-tighter italic text-slate-900">EvaCare</span>
                <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm border border-slate-100"><i class="fa-solid fa-bars-staggered"></i></button>
            </div>
            <div class="bg-blue-600 p-6 rounded-[2.5rem] text-white shadow-xl">
                <div class="flex justify-between items-end">
                    <div>
                        <h2 id="view-nombre" class="text-2xl font-extrabold leading-none italic">---</h2>
                        <p id="view-edad" class="text-[10px] font-black text-blue-200 mt-2 uppercase tracking-widest"></p>
                    </div>
                    <div class="text-right text-[10px] font-bold">
                        <p id="view-medidas-act"></p>
                        <p id="view-nacimiento" class="opacity-60 text-[8px]"></p>
                    </div>
                </div>
            </div>
        </header>

        <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
            <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4 italic">Cronograma y Eventos</h3>
            <div id="timeline-eventos" class="space-y-4"></div>
        </div>

        <div id="container-compra" class="hidden bg-amber-50 p-4 rounded-3xl border border-amber-100">
            <p class="text-[9px] font-black text-amber-600 uppercase mb-2 tracking-widest italic">Lista de la Compra</p>
            <div id="items-compra" class="flex flex-wrap gap-2"></div>
        </div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] uppercase shadow-sm border">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] uppercase shadow-sm border">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] uppercase shadow-sm border">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-slate-900 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus text-xs"></i></button>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i><span class="text-[10px] font-black uppercase text-slate-400">Pecho</span>
            </button>
            <button onclick="openModal('modal-bibe')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-bottle-water text-blue-500 text-xl"></i><span class="text-[10px] font-black uppercase text-slate-400">Biberón</span>
            </button>
        </div>
        <div id="diario-hoy" class="space-y-2 pt-2"></div>
    </main>

    <main id="tab-nutricion" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic mt-4 px-2">Alimentación</h2>
        <div class="card space-y-4">
            <input id="food-n" type="text" placeholder="¿Qué alimento nuevo?" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none border-2 border-transparent focus:border-orange-200">
            <div class="grid grid-cols-2 gap-4">
                <button onclick="setFoodReac('😋 Le gusta', this)" class="btn-reac bg-slate-50 p-4 rounded-2xl text-2xl transition-all">😋</button>
                <button onclick="setFoodReac('🤢 No le gusta', this)" class="btn-reac bg-slate-50 p-4 rounded-2xl text-2xl transition-all">🤢</button>
            </div>
            <div class="flex items-center gap-3 bg-red-50 p-3 rounded-2xl">
                <input type="checkbox" id="food-alert" class="w-5 h-5 accent-red-500">
                <label for="food-alert" class="text-[10px] font-black text-red-600 uppercase">¿Ha tenido reacción?</label>
            </div>
            <button onclick="addFood()" class="w-full bg-orange-500 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar Alimento</button>
        </div>

        <div class="relative mt-6">
            <i class="fa-solid fa-magnifying-glass absolute left-4 top-4 text-slate-300"></i>
            <input type="text" id="food-search" onkeyup="filterFoods()" placeholder="Buscar alimento..." class="w-full p-4 pl-12 bg-white rounded-2xl text-xs font-bold shadow-sm border-none outline-none">
        </div>
        <div id="lista-alimentos" class="space-y-3"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic mt-4 px-2">Salud</h2>
        <div class="grid grid-cols-1 gap-3">
            <button onclick="openModal('modal-add-med')" class="w-full bg-indigo-600 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg">
                <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Tratamientos</p><p class="text-lg">Añadir Medicación</p></div><i class="fa-solid fa-pills text-2xl opacity-50"></i>
            </button>
            <button onclick="openModal('modal-add-crecimiento')" class="w-full bg-emerald-600 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg">
                <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Evolución</p><p class="text-lg">Peso y Talla</p></div><i class="fa-solid fa-scale-balanced text-2xl opacity-50"></i>
            </button>
        </div>
        <div id="lista-salud" class="space-y-3 mt-4"></div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-6">
        <h2 class="text-2xl font-black italic mt-4 px-2">Agenda</h2>
        <div class="card">
            <div class="flex justify-between items-center mb-6">
                <button onclick="changeMonth(-1)" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400"><i class="fa-solid fa-chevron-left"></i></button>
                <h3 id="cal-mes-titulo" class="font-black text-slate-900 uppercase tracking-widest text-xs text-center">---</h3>
                <button onclick="changeMonth(1)" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400"><i class="fa-solid fa-chevron-right"></i></button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>
        <div id="agenda-completa" class="space-y-2"></div>
    </main>

    <nav class="fixed bottom-6 left-6 right-6 h-18 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-4 z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="w-12 h-12 flex items-center justify-center text-blue-600"><i class="fa-solid fa-house-chimney text-lg"></i></button>
        <button onclick="showTab('nutricion')" id="nav-nutricion" class="w-12 h-12 flex items-center justify-center text-slate-300"><i class="fa-solid fa-carrot text-lg"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="w-12 h-12 flex items-center justify-center text-slate-300"><i class="fa-solid fa-suitcase-medical text-lg"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="w-12 h-12 flex items-center justify-center text-slate-300"><i class="fa-solid fa-calendar-day text-lg"></i></button>
    </nav>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[3000] flex justify-end">
        <div class="bg-white w-4/5 h-full p-8 flex flex-col">
            <h2 class="text-3xl font-extrabold italic mb-8">Ajustes</h2>
            <div class="flex-1 space-y-6">
                <div class="bg-slate-50 p-6 rounded-3xl">
                    <p class="text-[10px] font-black text-blue-600 uppercase mb-2 tracking-widest">Aviso de tomas (horas)</p>
                    <input id="set-plazo" type="number" class="w-full p-4 rounded-xl font-bold bg-white outline-none border-2 border-blue-100">
                </div>
                <button onclick="saveSettings(); closeModal('modal-menu')" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black uppercase text-xs shadow-lg">Guardar Configuración</button>
                <button onclick="reiniciarApp()" class="w-full p-4 bg-red-50 text-red-600 rounded-2xl font-bold text-[10px] uppercase">Borrar Datos</button>
            </div>
            <button onclick="closeModal('modal-menu')" class="text-slate-300 font-bold uppercase text-[10px] py-4">Cerrar</button>
        </div>
    </div>

    <div id="modal-add-med" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-4">
            <h3 class="text-xs font-black text-indigo-600 uppercase text-center tracking-widest">Nueva Medicación</h3>
            <input id="m-n" type="text" placeholder="Nombre" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none">
            <div class="grid grid-cols-2 gap-2">
                <input id="m-q" type="text" placeholder="Dosis" class="p-4 bg-slate-50 rounded-2xl font-bold">
                <input id="m-h" type="number" placeholder="Horas" class="p-4 bg-slate-50 rounded-2xl font-bold">
            </div>
            <input id="m-d" type="number" placeholder="Duración (días)" class="w-full p-4 bg-slate-50 rounded-2xl font-bold">
            <button onclick="addMed()" class="w-full bg-indigo-600 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button>
            <button onclick="closeModal('modal-add-med')" class="w-full text-slate-300 font-bold uppercase text-[10px]">Cerrar
