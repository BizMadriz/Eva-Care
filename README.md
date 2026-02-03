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
        body { font-family: 'Plus Jakarta Sans', sans-serif; margin: 0; padding: 0; overflow-x: hidden; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 2rem; padding: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 1rem; font-size: 0.85rem; font-weight: 700; border: 1px solid #f1f5f9; position: relative; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 4px; }
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[999] flex flex-col p-8 overflow-y-auto hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold text-slate-900 tracking-tighter">Hola, <br><span class="text-blue-600">¿Nombre del bebé?</span></h2>
            <input id="setup-n" type="text" placeholder="Ej. Eva" class="w-full p-4 bg-slate-100 rounded-2xl text-2xl font-bold border-none outline-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-5 rounded-3xl font-black shadow-xl">CONTINUAR</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold text-slate-900">¿Cuándo nació?</h2>
            <input id="setup-f" type="date" class="w-full p-4 bg-slate-100 rounded-2xl text-xl font-bold border-none outline-none">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-5 rounded-3xl font-black shadow-xl">CONTINUAR</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold text-slate-900">Medidas al nacer</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Peso (kg)" class="p-4 bg-slate-100 rounded-2xl font-bold border-none outline-none">
                <input id="setup-a" type="number" placeholder="Talla (cm)" class="p-4 bg-slate-100 rounded-2xl font-bold border-none outline-none">
            </div>
            <button onclick="finishSetup()" class="bg-blue-600 text-white p-5 rounded-3xl font-black shadow-xl">EMPEZAR</button>
        </div>
    </div>

    <header class="bg-white px-6 pt-6 pb-2 sticky top-0 z-50">
        <div class="flex justify-between items-center mb-4">
            <div class="flex items-center gap-1">
                <span class="text-2xl font-extrabold tracking-tighter text-slate-900 italic">EvaCare</span>
                <span class="w-2 h-2 rounded-full bg-blue-600 mb-1"></span>
            </div>
            <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-slate-50 rounded-full flex items-center justify-center text-slate-400">
                <i class="fa-solid fa-bars-staggered"></i>
            </button>
        </div>

        <div onclick="openModal('modal-menu')" class="bg-slate-900 rounded-[1.8rem] p-4 text-white">
            <div class="flex justify-between items-end">
                <div>
                    <h2 id="view-nombre" class="text-xl font-extrabold leading-none">---</h2>
                    <p id="view-edad" class="text-[9px] font-bold text-blue-400 mt-1 uppercase tracking-widest"></p>
                </div>
                <div class="text-right">
                    <p id="view-nacimiento" class="text-[9px] font-medium text-slate-400"></p>
                    <p id="view-actual" class="text-[10px] font-black text-white"></p>
                </div>
            </div>
        </div>
    </header>

    <main id="tab-inicio" class="p-4 space-y-4">
        <div class="space-y-2">
            <h3 class="text-[10px] font-bold text-slate-400 uppercase tracking-widest ml-4">Agenda de hoy</h3>
            <div id="timeline-eventos" class="space-y-3">
                </div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-400 hidden bg-orange-50/50">
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-3">
            <button onclick="openModal('modal-pecho')" class="bg-white border border-slate-100 h-24 rounded-[2rem] flex flex-col items-center justify-center shadow-sm">
                <i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl mb-1"></i>
                <span class="text-[10px] font-black uppercase tracking-widest text-slate-400">Pecho</span>
            </button>
            <button onclick="openModal('modal-bibe')" class="bg-white border border-slate-100 h-24 rounded-[2rem] flex flex-col items-center justify-center shadow-sm">
                <i class="fa-solid fa-bottle-water text-blue-500 text-xl mb-1"></i>
                <span class="text-[10px] font-black uppercase tracking-widest text-slate-400">Biberón</span>
            </button>
        </div>

        <div class="flex gap-2">
            <button onclick="addLog('💦 Pis')" class="flex-1 bg-
