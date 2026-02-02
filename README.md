<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BabyCare V9</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
</head>
<body class="bg-gray-100">

    <div class="bg-white p-6 shadow-md text-center">
        <h1 class="text-xl font-bold mb-4">ACTUALIZACIÓN V9-OK</h1>
        <button onclick="let p=prompt('¿Qué falta?'); if(p) alert('Añadido: '+p)" class="w-full bg-orange-600 text-white py-4 rounded-2xl font-black shadow-lg">
            + AÑADIR A LA COMPRA
        </button>
    </div>

    <div class="p-4">
        <div class="bg-white p-4 rounded-3xl shadow-sm border border-gray-200">
            <h3 class="text-[10px] font-black text-gray-400 uppercase mb-3">Agenda 14 días</h3>
            <div id="ag" class="space-y-2 text-sm font-bold">
                <div class="p-3 bg-blue-50 border-l-4 border-blue-500 rounded-r-xl">Ejemplo: Vacuna Hoy</div>
            </div>
        </div>
    </div>

    <div class="p-4">
        <div class="bg-slate-800 text-white p-5 rounded-3xl">
            <p class="text-xs mb-2">Horas entre tomas:</p>
            <div class="flex gap-2">
                <input id="h" type="number" value="3" class="w-16 p-2 rounded-lg text-black font-bold">
                <button onclick="alert('Intervalo guardado')" class="bg-blue-500 flex-1 rounded-lg font-bold">GUARDAR</button>
            </div>
        </div>
    </div>

</body>
</html>
