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
        .nav-active { color: #2563eb !important; }
        .food-badge { padding: 4px 12px; border-radius: 99px; font-size: 10px; font-weight: 800; text-transform: uppercase; }
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-32">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[5000] flex flex-col p-8 hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold tracking-tighter italic">EvaCare</h2>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold outline-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold border-none outline-none">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas iniciales</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Peso (kg)" class="p-5 bg-slate-100 rounded-3xl font-bold border-none outline-none">
                <input id="setup-a" type="number" placeholder="Talla (cm)" class="p-5 bg-slate-100 rounded-3xl font-bold border-none outline-none">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black uppercase">Finalizar</button>
        </div>
    </div>

    <div id="app-shell" class="hidden">
        
        <main id="tab-inicio" class="p-4 space-y-4">
            <header class="pt-4 pb-2">
                <div class="flex justify-between items-center mb-6">
                    <span class="text-2xl font-extrabold tracking-tighter italic">EvaCare</span>
                    <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm border border-slate-100"><i class="fa-solid fa-bars-staggered"></i></button>
                </div>
                <div class="bg-blue-600 p-6 rounded-[2.5rem] text-white shadow-xl">
                    <div class="flex justify-between items-end">
                        <div>
                            <h2 id="view-nombre" class="text-3xl font-extrabold leading-none italic">---</h2>
                            <p id="view-edad" class="text-[10px] font-black text-blue-200 mt-2 uppercase tracking-widest"></p>
                        </div>
                        <div class="text-right text-[10px] font-bold">
                            <p id="view-medidas-act"></p>
                            <p id="view-nacimiento" class="opacity-60 text-[8px] mt-1"></p>
                        </div>
                    </div>
                </div>
            </header>

            <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
                <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4">Próximos Eventos</h3>
                <div id="timeline-eventos" class="space-y-4"></div>
            </div>

            <div class="grid grid-cols-4 gap-2">
                <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] uppercase shadow-sm border">Pis</button>
                <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] uppercase shadow-sm border">Caca</button>
                <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] uppercase shadow-sm border">Aseo</button>
                <button onclick="addCompraUrgent()" class="bg-slate-900 text-white rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border"><i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i><span class="text-[10px] font-black uppercase text-slate-400">Pecho</span></button>
                <button onclick="openModal('modal-bibe')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border"><i class="fa-solid fa-bottle-water text-blue-500 text-xl"></i><span class="text-[10px] font-black uppercase text-slate-400">Biberón</span></button>
            </div>
            <div id="diario-hoy" class="space-y-2 pt-2"></div>
        </main>

        <main id="tab-nutricion" class="hidden p-4 space-y-4">
            <h2 class="text-2xl font-black italic p-2 mt-4">Alimentación</h2>
            
            <div class="grid grid-cols-1 gap-3">
                <button onclick="openModal('modal-nuevo-alimento')" class="w-full bg-orange-500 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg">
                    <div class="text-left">
                        <p class="text-[10px] opacity-70 uppercase">Nuevo Alimento</p>
                        <p class="text-lg">Registro de Prueba</p>
                    </div>
                    <i class="fa-solid fa-apple-whole text-2xl opacity-50"></i>
                </button>
            </div>

            <div class="grid grid-cols-3 gap-2">
                <button onclick="addLog('🥦 Verdura')" class="bg-emerald-50 py-4 rounded-2xl font-bold text-emerald-600 text-[10px] uppercase border border-emerald-100">Verdura</button>
                <button onclick="addLog('🍎 Fruta')" class="bg-red-50 py-4 rounded-2xl font-bold text-red-600 text-[10px] uppercase border border-red-100">Fruta</button>
                <button onclick="addLog('🌾 Cereales')" class="bg-amber-50 py-4 rounded-2xl font-bold text-amber-600 text-[10px] uppercase border border-amber-100">Cereales</button>
            </div>

            <div class="pt-4">
                <h3 class="text-[10px] font-black text-slate-400 uppercase tracking-widest px-2 mb-3">Historial de Alimentos</h3>
                <div id="lista-alimentos" class="space-y-3"></div>
            </div>
        </main>

        <main id="tab-salud" class="hidden p-4 space-y-4">
            <h2 class="text-2xl font-black italic p-2 mt-4">Salud</h2>
            <div class="grid grid-cols-1 gap-3">
                <button onclick="openModal('modal-add-med')" class="bg-indigo-600 text-white p-6 rounded-[2rem] font-black flex items-center justify-between shadow-lg">
                    <div class="text-left"><p class="text-[10px] opacity-70 uppercase">Medicación</p><p class="text-lg">Nueva Medicina</p></div><i class="fa-solid fa-pills text-2xl opacity-50"></i>
                </button>
            </div>
            <div id="lista-salud" class="space-y-3 mt-4"></div>
        </main>

        <main id="tab-cal" class="hidden p-4 space-y-4">
            <h2 class="text-2xl font-black italic p-2 mt-4">Agenda</h2>
            <div id="cal-grid" class="grid grid-cols-7 gap-1 card mt-2"></div>
            <div id="agenda-completa" class="space-y-2"></div>
        </main>

        <nav class="fixed bottom-6 left-6 right-6 h-20 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center px-6 z-50">
            <button onclick="showTab('inicio')" id="nav-inicio" class="flex flex-col items-center gap-1 text-blue-600"><i class="fa-solid fa-house-chimney text-lg"></i><span class="text-[8px] font-black uppercase">Inicio</span></button>
            <button onclick="showTab('nutricion')" id="nav-nutricion" class="flex flex-col items-center gap-1 text-slate-300"><i class="fa-solid fa-carrot text-lg"></i><span class="text-[8px] font-black uppercase">Nutrición</span></button>
            <button onclick="showTab('salud')" id="nav-salud" class="flex flex-col items-center gap-1 text-slate-300"><i class="fa-solid fa-suitcase-medical text-lg"></i><span class="text-[8px] font-black uppercase">Salud</span></button>
            <button onclick="showTab('cal')" id="nav-cal" class="flex flex-col items-center gap-1 text-slate-300"><i class="fa-solid fa-calendar-day text-lg"></i><span class="text-[8px] font-black uppercase">Agenda</span></button>
        </nav>
    </div>

    <div id="modal-nuevo-alimento" class="hidden fixed inset-0 bg-black/80 z-[6000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2.5rem] p-8 space-y-5">
            <h3 class="text-xs font-black text-orange-600 uppercase text-center tracking-widest">Introducir Alimento</h3>
            <input id="food-name" type="text" placeholder="Ej: Plátano, Huevo..." class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none">
            
            <div class="space-y-2">
                <p class="text-[10px] font-black text-slate-400 uppercase">¿Le ha gustado?</p>
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="setFoodReac('😋 Le gusta')" class="bg-slate-50 p-4 rounded-xl text-xl">😋</button>
                    <button onclick="setFoodReac('🤢 No le gusta')" class="bg-slate-50 p-4 rounded-xl text-xl">🤢</button>
                </div>
            </div>

            <div class="flex items-center gap-3 bg-red-50 p-4 rounded-2xl">
                <input type="checkbox" id="food-alert" class="w-5 h-5 accent-red-600">
                <label for="food-alert" class="text-xs font-bold text-red-600">¿Ha tenido reacción?</label>
            </div>

            <button onclick="saveFood()" class="w-full bg-orange-500 text-white p-5 rounded-2xl font-black uppercase">Guardar Alimento</button>
            <button onclick="closeModal('modal-nuevo-alimento')" class="w-
