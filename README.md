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
        body { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; margin:0; padding:0; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 2rem; padding: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); }
        /* Quitamos el sticky del header para que todo deslice */
        header { position: relative; }
        .timeline-item { animation: fadeIn 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-32">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[1000] flex flex-col p-8 hidden">
        <div id="step-1" class="flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold tracking-tighter italic">EvaCare</h2>
            <p class="text-slate-400 font-bold uppercase text-[10px] tracking-widest">Nombre del bebé</p>
            <input id="setup-n" type="text" placeholder="Ej. Eva" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold outline-none border-none">
            <button onclick="validaPaso(1, 2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black shadow-xl">CONTINUAR</button>
        </div>
        <div id="step-2" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Fecha de nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold outline-none border-none">
            <button onclick="validaPaso(2, 3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black shadow-xl text-xs">SIGUIENTE</button>
        </div>
        <div id="step-3" class="hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas iniciales</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Peso (kg)" class="p-5 bg-slate-100 rounded-3xl font-bold border-none">
                <input id="setup-a" type="number" placeholder="Talla (cm)" class="p-5 bg-slate-100 rounded-3xl font-bold border-none">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black shadow-xl uppercase">Finalizar</button>
        </div>
    </div>

    <main id="tab-inicio" class="p-4 space-y-6">
        <header class="pt-4 px-2">
            <div class="flex justify-between items-center mb-6">
                <div class="flex items-center gap-1">
                    <span class="text-2xl font-extrabold tracking-tighter text-slate-900 italic">EvaCare</span>
                    <span class="w-2 h-2 rounded-full bg-blue-600 mb-1"></span>
                </div>
                <button onclick="openModal('modal-menu')" class="w-10 h-10 bg-white rounded-full flex items-center justify-center text-slate-400 shadow-sm border border-slate-100">
                    <i class="fa-solid fa-bars-staggered"></i>
                </button>
            </div>
            <div class="bg-white rounded-[2rem] p-6 shadow-sm border border-slate-100">
                <div class="flex justify-between items-end">
                    <div>
                        <h2 id="view-nombre" class="text-2xl font-extrabold text-slate-900">---</h2>
                        <p id="view-edad" class="text-[10px] font-black text-blue-600 uppercase tracking-widest mt-1"></p>
                    </div>
                    <div class="text-right">
                        <p id="view-nacimiento" class="text-[9px] font-medium text-slate-400"></p>
                        <p id="view-actual" class="text-[11px] font-black text-slate-900"></p>
                    </div>
                </div>
            </div>
        </header>

        <div id="panel-agenda" class="bg-slate-900 rounded-[2.5rem] p-6 text-white shadow-xl">
            <h3 class="text-[10px] font-bold uppercase text-slate-500 tracking-[0.2em] mb-4">Próximos Avisos</h3>
            <div id="timeline-eventos" class="space-y-4"></div>
        </div>

        <div id="card-compra" class="card border-l-4 border-orange-500 hidden bg-orange-50/30">
            <h3 class="text-[10px] font-black text-orange-600 uppercase mb-3 tracking-widest">Pendiente de Comprar</h3>
            <div id="lista-compra" class="space-y-2"></div>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-24 rounded-[2rem] flex flex-col items-center justify-center gap-1 shadow-sm border border-slate-100">
                <i class="fa-solid fa-person-breastfeeding text-pink-500 text-xl"></i>
                <span class="text-[10px] font-
