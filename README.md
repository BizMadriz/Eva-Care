<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Eva Care Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        .tab-activa { color: #2563eb; border-bottom: 4px solid #2563eb; font-weight: 900; }
        .oculto { display: none; }
        .card-home { background: white; border-radius: 20px; padding: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .bg-pecho { background: #fdf2f8; border: 1px solid #fbcfe8; }
    </style>
</head>
<body class="bg-slate-50 pb-28 font-sans text-slate-900">

    <header class="bg-white p-4 shadow-sm sticky top-0 z-50">
        <div class="flex justify-between items-start">
            <div>
                <h1 class="text-xl font-black italic text-blue-600 leading-none">Eva Care <span class="text-slate-800">Pro</span></h1>
                <div id="info-bebe-banner" class="mt-2 space-y-0.5">
                    <p id="txt-nombre" class="text-sm font-black text-slate-700 leading-none"></p>
                    <p id="txt-edad" class="text-[10px] font-bold text-blue-500 uppercase tracking-tighter"></p>
                    <p id="txt-stats" class="text-[10px] font-medium text-slate-400 italic"></p>
                </div>
            </div>
            <button onclick="toggleMod('modal-ajustes')" class="bg-slate-100 p-3 rounded-2xl text-slate-500 shadow-sm active:bg-slate-200">
                <i class="fa-solid fa-bars"></i>
            </button>
        </div>
    </header>

    <main class="p-4 space-y-4">

        <section class="grid grid-cols-2 gap-3">
            <div class="card-home border-l-4 border-blue-500">
                <h4 class="text-[9px] font-black uppercase text-blue-500 mb-2">Alertas y Citas</h4>
                <div id="home-agenda" class="space-y-1 text-[10px] font-bold max-h-24 overflow-y-auto"></div>
            </div>
            <div class="card-home border-l-4 border-orange-500">
                <h4 class="text-[9px] font-black uppercase text-orange-500 mb-2">Lista Compra</h4>
                <div id="home-compra" class="space-y-1 text-[10px] max-h-24 overflow-y-auto"></div>
            </div>
        </section>

        <nav class="flex bg-white rounded-2xl shadow-sm border overflow-hidden text-[10px] font-bold">
            <button id="t-diario" onclick="verTab('diario')" class="flex-1 p-3 tab-activa">REGISTROS</button>
            <button id="t-cal" onclick="verTab('cal')" class="flex-1 p-3 text-slate-400">CALENDARIO</button>
            <button id="t-salud" onclick="verTab('salud')" class="flex-1 p-3 text-slate-400">MEDICINAS</button>
        </nav>

        <section id="sec-diario" class="space-y-4">
            <div class="grid grid-cols-3 gap-2">
                <button onclick="openPecho()" class="col-span-3 bg-pink-500 text-white py-6 rounded-3xl font-black text-lg shadow-lg active:scale-95 transition-transform">TOMA DE PECHO</button>
                <button onclick="addBibi()" class="col-span-2 bg-blue-600 text-white p-4 rounded-2xl font-bold text-sm shadow-md">🍼 BIBERÓN</button>
                <button onclick="repBibi()" class="bg-blue-100 text-blue-600 p-4 rounded-2xl font-bold text-[10px] shadow-sm">REPETIR</button>
                <button onclick="registrar('💦 Pis')" class="bg-white border-2 border-cyan-100 p-4 rounded-2xl font-bold text-cyan-600 shadow-sm">💦 PIS</button>
                <button onclick="registrar('💩 Caca')" class="bg-white border-2 border-orange-100 p-4 rounded-2xl font-bold text-orange-800 shadow-sm">💩 CACA</button>
                <button onclick="addCompra()" class="bg-orange-600 text-white p-4 rounded-2xl font-bold text-[10px] flex items-center justify-center shadow-md"><i class="fa-solid fa-cart-plus"></i></button>
            </div>
            <div id="logs" class="bg-white/50 p-3 rounded-2xl text-[9px] font-bold text-slate-300 uppercase tracking-wider"></div>
        </section>

        <section id="sec-cal" class="oculto space-y-4">
            <div class="bg-white p-4 rounded-3xl shadow-sm border">
                <div id="mes-nombre" class="text-center font-black text-blue-600 mb-2 uppercase text-xs"></div>
                <div id="cal-grid" class="grid grid-cols-7 gap-1 text-center text-[10px] font-bold"></div>
            </div>
        </section>

        <section id="sec-salud" class="oculto space-y-4">
            <button onclick="toggleMod('modal-med')" class="w-full bg-blue-600 text-white p-4 rounded-2xl font-black shadow-lg">+ AÑADIR MEDICACIÓN</button>
            <div id="med-lista" class="space-y-2"></div>
        </section>
    </main>

    <div id="modal-pecho" class="oculto fixed inset-0 bg-black/80 z-[100] flex items-end">
        <div class="bg-white w-full p-6 rounded-t-[40px] grid grid-cols-3 gap-3 animate-bounce-in">
            <button onclick="registrar('🤱 Pecho IZQ')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-600 shadow-inner">IZQ</button>
            <button onclick="registrar('🤱 Pecho AMBOS')" class="bg-pink-200 p-8 rounded-3xl font-black text-pink-700 text-xs">AMBOS</button>
            <button onclick="registrar('🤱 Pecho DER')" class="bg-pink-100 p-8 rounded-3xl font-black text-pink-600 shadow-inner">DER</button>
            <button onclick="openPecho()" class="col-span-3 p-4 text-slate-400 font-bold uppercase text-xs tracking-widest">Cerrar</button>
        </div>
    </div>

    <div id="modal-med" class="oculto fixed inset-0 bg-black/80 z-[100] flex items-center justify-center p-6">
        <div class="bg-white w-full rounded-[30px] p-6 space-y-3">
            <h3 class="font-black text-center text-slate-800">Nueva Medicina</h3>
            <input id="m-n" type="text" placeholder="Nombre (ej: Apiretal)" class="w-full p-3 bg-slate-100 rounded-xl font-bold outline-none border-2 border-transparent focus:border-blue-500">
            <div class="flex gap-2">
                <input id="m-ds" type="text" placeholder="Dosis" class="w-1/2 p-3 bg-slate-100 rounded-xl font-bold">
                <input id="m-h" type="number" placeholder="Cada (h)" class="w-1/2 p-3 bg-slate-100 rounded-xl font-bold">
            </div>
            <input id="m-d" type="number" placeholder="Días totales" class="w-full p-3 bg-slate-100 rounded-xl font-bold">
            <button onclick="saveMed()" class="w-full bg-blue-600 text-white p-4 rounded-xl font-black shadow-lg">GUARDAR TRATAMIENTO</button>
        </div>
    </div>

    <div id="modal-ajustes" class="oculto fixed inset-0 bg-black/80 z-[100] flex justify-end">
        <div class="bg-white w-80 h-full p-6 shadow-2xl overflow-y-auto">
            <h3 class="text-xl font-black mb-6 text-blue-600">Ajustes Eva Care</h3>
            <div class="space-y-4">
                <div class="p-4 bg-slate-50 rounded-2xl border border-slate-100">
                    <label class="text-[9px] font-black text-slate-400 block mb-2 tracking-widest uppercase">Ficha del Bebé</label>
                    <input id="cfg-n" type="text" placeholder="Nombre" class="w-full mb-2 p-3 rounded-xl border-none shadow-sm font-bold">
                    <input id="cfg-f" type="date"
