<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>EvaCare V12.2</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; -webkit-tap-highlight-color: transparent; }
        .hidden { display: none !important; }
        .card { background: white; border-radius: 1.5rem; padding: 1.2rem; box-shadow: 0 4px 15px rgba(0,0,0,0.03); }
        .cal-day { aspect-ratio: 1/1; display: flex; align-items: center; justify-content: center; border-radius: 0.8rem; font-size: 0.8rem; font-weight: 700; position: relative; cursor: pointer; }
        .hoy { background-color: #2563eb !important; color: white !important; }
        .seleccionado { border: 2px solid #2563eb !important; }
        .event-dot { width: 4px; height: 4px; background: #3b82f6; border-radius: 50%; position: absolute; bottom: 4px; }
    </style>
</head>
<body class="bg-slate-50 text-slate-900 pb-28">

    <div id="setup-wizard" class="fixed inset-0 bg-white z-[1000] flex flex-col p-8 hidden">
        <div id="step-1" class="step-div flex flex-col h-full justify-center space-y-6">
            <h2 class="text-4xl font-extrabold italic text-blue-600">EvaCare</h2>
            <input id="setup-n" type="text" placeholder="Nombre del bebé" class="w-full p-5 bg-slate-100 rounded-3xl text-2xl font-bold outline-none">
            <button onclick="irAStep(2)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-2" class="step-div hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Nacimiento</h2>
            <input id="setup-f" type="date" class="w-full p-5 bg-slate-100 rounded-3xl text-xl font-bold">
            <button onclick="irAStep(3)" class="bg-blue-600 text-white p-6 rounded-3xl font-black uppercase">Siguiente</button>
        </div>
        <div id="step-3" class="step-div hidden flex flex-col h-full justify-center space-y-6">
            <h2 class="text-3xl font-extrabold">Medidas</h2>
            <div class="grid grid-cols-2 gap-4">
                <input id="setup-p" type="number" step="0.1" placeholder="Kg" class="p-5 bg-slate-100 rounded-3xl font-bold">
                <input id="setup-a" type="number" placeholder="cm" class="p-5 bg-slate-100 rounded-3xl font-bold">
            </div>
            <button onclick="finishSetup()" class="bg-slate-900 text-white p-6 rounded-3xl font-black uppercase">Finalizar</button>
        </div>
    </div>

    <main id="tab-inicio" class="p-4 space-y-4">
        <header class="pt-4 pb-2 flex justify-between items-center">
            <span class="text-2xl font-extrabold text-slate-900 italic">EvaCare</span>
            <button onclick="openModal('modal-menu')" class="text-slate-400"><i class="fa-solid fa-bars-staggered"></i></button>
        </header>

        <div class="bg-blue-600 p-6 rounded-[2rem] text-white shadow-lg">
            <h2 id="view-nombre" class="text-2xl font-black italic">---</h2>
            <p id="view-edad" class="text-[10px] font-bold opacity-80 uppercase tracking-tighter"></p>
        </div>

        <div class="bg-slate-900 rounded-[2rem] p-5 text-white shadow-xl">
            <h3 class="text-[9px] font-black uppercase text-slate-500 mb-3 tracking-widest">Avisos</h3>
            <div id="timeline-eventos" class="space-y-3"></div>
        </div>

        <div id="lista-compra" class="hidden space-y-2"></div>

        <div class="grid grid-cols-4 gap-2">
            <button onclick="addLog('💦 Pis')" class="bg-white py-4 rounded-2xl font-bold text-cyan-600 text-[10px] border">Pis</button>
            <button onclick="addLog('💩 Caca')" class="bg-white py-4 rounded-2xl font-bold text-orange-800 text-[10px] border">Caca</button>
            <button onclick="addLog('✨ Aseo')" class="bg-white py-4 rounded-2xl font-bold text-purple-600 text-[10px] border">Aseo</button>
            <button onclick="addCompraUrgent()" class="bg-yellow-400 text-slate-900 rounded-2xl flex items-center justify-center shadow-lg"><i class="fa-solid fa-cart-plus"></i></button>
        </div>

        <div class="grid grid-cols-2 gap-4">
            <button onclick="openModal('modal-pecho')" class="bg-white h-20 rounded-3xl flex flex-col items-center justify-center border"><i class="fa-solid fa-person-breastfeeding text-pink-500"></i><span class="text-[9px] font-black uppercase mt-1">Pecho</span></button>
            <button onclick="openModal('modal-bibe')" class="bg-white h-20 rounded-3xl flex flex-col items-center justify-center border"><i class="fa-solid fa-bottle-water text-blue-500"></i><span class="text-[9px] font-black uppercase mt-1">Biberón</span></button>
        </div>

        <div id="diario-hoy" class="space-y-2"></div>
    </main>

    <main id="tab-nutricion" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic mt-4 px-2">Alimentación</h2>
        <div class="card space-y-3">
            <input id="food-n" type="text" placeholder="Nuevo alimento..." class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none border-none">
            <div class="grid grid-cols-2 gap-4">
                <button onclick="setReac('😋', this)" class="btn-reac bg-slate-50 p-4 rounded-2xl text-2xl">😋</button>
                <button onclick="setReac('🤢', this)" class="btn-reac bg-slate-50 p-4 rounded-2xl text-2xl">🤢</button>
            </div>
            <button onclick="saveFood()" class="w-full bg-orange-500 text-white p-4 rounded-2xl font-black uppercase text-[10px]">Registrar Prueba</button>
        </div>
        <div class="mt-4">
            <input id="food-search" type="text" onkeyup="filterFoods()" placeholder="🔍 Buscar alimento registrado..." class="w-full p-4 bg-white rounded-2xl text-xs font-bold shadow-sm outline-none border">
        </div>
        <div id="lista-alimentos" class="space-y-2"></div>
    </main>

    <main id="tab-salud" class="hidden p-4 space-y-4">
        <h2 class="text-2xl font-black italic mt-4 px-2">Salud</h2>
        <button onclick="openModal('modal-add-med')" class="w-full bg-indigo-600 text-white p-5 rounded-3xl font-black flex items-center justify-between shadow-lg"><span>Añadir Tratamiento</span><i class="fa-solid fa-pills"></i></button>
        <div id="lista-salud" class="space-y-3"></div>
    </main>

    <main id="tab-cal" class="hidden p-4 space-y-6">
        <h2 class="text-2xl font-black italic mt-4 px-2">Agenda</h2>
        <div class="card">
            <div class="flex justify-between items-center mb-4 px-2">
                <button onclick="changeMonth(-1)" class="p-2"><i class="fa-solid fa-chevron-left text-slate-300"></i></button>
                <h3 id="cal-mes-titulo" class="font-black uppercase text-[10px] tracking-widest text-blue-600">---</h3>
                <button onclick="changeMonth(1)" class="p-2"><i class="fa-solid fa-chevron-right text-slate-300"></i></button>
            </div>
            <div id="cal-grid" class="grid grid-cols-7 gap-1"></div>
        </div>

        <div class="space-y-4">
            <h4 class="text-[10px] font-black uppercase text-slate-400 px-2">Próximos Eventos Guardados</h4>
            <div id="agenda-total-lista" class="space-y-2 pb-10"></div>
        </div>
    </main>

    <nav class="fixed bottom-6 left-6 right-6 h-16 bg-white/90 backdrop-blur-xl rounded-full border border-slate-100 shadow-2xl flex justify-around items-center z-50">
        <button onclick="showTab('inicio')" id="nav-inicio" class="text-blue-600"><i class="fa-solid fa-house-chimney"></i></button>
        <button onclick="showTab('nutricion')" id="nav-nutricion" class="text-slate-300"><i class="fa-solid fa-carrot"></i></button>
        <button onclick="showTab('salud')" id="nav-salud" class="text-slate-300"><i class="fa-solid fa-suitcase-medical"></i></button>
        <button onclick="showTab('cal')" id="nav-cal" class="text-slate-300"><i class="fa-solid fa-calendar-day"></i></button>
    </nav>

    <div id="modal-cal" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[2rem] p-8 space-y-4">
            <h3 id="cal-modal-fecha" class="text-center font-black text-blue-600 uppercase text-[10px]"></h3>
            <input id="cal-ev-txt" type="text" placeholder="¿Qué evento es?" class="w-full p-4 bg-slate-50 rounded-2xl font-bold outline-none border-none">
            <input id="cal-ev-hora" type="time" class="w-full p-4 bg-slate-50 rounded-2xl font-bold">
            <button onclick="saveCita()" class="w-full bg-slate-900 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar Evento</button>
            <button onclick="closeModal('modal-cal')" class="w-full text-slate-300 font-bold uppercase text-[10px] text-center">Cancelar</button>
        </div>
    </div>

    <div id="modal-menu" class="hidden fixed inset-0 bg-black/80 z-[3000] flex justify-end"><div class="bg-white w-4/5 h-full p-8 flex flex-col"><h2 class="text-3xl font-black italic mb-10">Ajustes</h2><div class="space-y-6"><div class="bg-slate-50 p-6 rounded-3xl"><p class="text-[10px] font-black text-blue-600 uppercase mb-2">Intervalo tomas (h)</p><input id="set-plazo" type="number" class="w-full p-4 rounded-xl font-bold bg-white outline-none border-none"></div><button onclick="saveSettings(); closeModal('modal-menu')" class="w-full bg-blue-600 text-white p-5 rounded-2xl font-black uppercase text-xs">Guardar</button><button onclick="reiniciarApp()" class="w-full p-4 text-red-500 font-bold text-[10px] uppercase">Borrar todo</button></div><button onclick="closeModal('modal-menu')" class="mt-auto text-center p-4 text-slate-300 font-bold text-[10px]">CERRAR</button></div></div>
    <div id="modal-bibe" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6"><div class="bg-white w-full rounded-[2rem] p-8 space-y-4"><input id="bibe-ml" type="number" placeholder="ml" class="w-full p-6 bg-slate-100 rounded-3xl text-center text-3xl font-black outline-none border-none"><button onclick="addBibe(document.getElementById('bibe-ml').value)" class="w-full bg-blue-600 text-white p-6 rounded-3xl font-black">REGISTRAR</button><button onclick="closeModal('modal-bibe')" class="w-full text-slate-300 text-center font-bold text-[10px]">CANCELAR</button></div></div>
    <div id="modal-pecho" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-end"><div class="bg-white w-full p-10 rounded-t-[3rem] grid grid-cols-3 gap-4"><button onclick="addLog('🤱 Pecho Izq'); closeModal('modal-pecho')" class="bg-pink-50 p-6 rounded-2xl font-black text-pink-600 text-xs text-center">IZQ</button><button onclick="addLog('🤱 Ambos'); closeModal('modal-pecho')" class="bg-pink-100 p-6 rounded-2xl font-black text-pink-700 text-xs text-center">AMBOS</button><button onclick="addLog('🤱 Pecho Der'); closeModal('modal-pecho')" class="bg-pink-50 p-6 rounded-2xl font-black text-pink-600 text-xs text-center">DER</button></div></div>
    <div id="modal-add-med" class="hidden fixed inset-0 bg-black/80 z-[3000] flex items-center justify-center p-6"><div class="bg-white w-full rounded-[2rem] p-8 space-y-4"><h3 class="text-center font-black text-indigo-600 uppercase text-[10px]">Medicación</h3><input id="m-n" type="text" placeholder="Nombre" class="w-full p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none"><div class="grid grid-cols-2 gap-2"><input id="m-q" type="text" placeholder="Dosis" class="p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none"><input id="m-h" type="number" placeholder="Horas" class="p-4 bg-slate-50 rounded-2xl font-bold border-none outline-none"></div><input id="m-d"
