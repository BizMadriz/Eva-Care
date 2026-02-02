<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BabyCare FIX</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
</head>
<body class="bg-slate-900 text-white pb-20">

    <div class="bg-slate-800 p-6 border-b border-slate-700 sticky top-0 z-50">
        <h1 id="nombre" class="text-2xl font-black mb-4">BabyCare v5.0</h1>
        
        <div class="grid grid-cols-2 gap-2">
            <button onclick="addCompra()" class="bg-orange-500 hover:bg-orange-600 p-4 rounded-2xl font-black text-xs shadow-lg flex items-center justify-center gap-2">
                <i class="fa-solid fa-cart-plus"></i> ¿FALTA ALGO?
            </button>
            <div id="status-compra" class="hidden bg-white text-orange-600 p-4 rounded-2xl font-black text-xs flex items-center justify-center">
                🛒 LISTA LLENA
            </div>
        </div>
    </div>

    <main class="p-4 space-y-6">
        
        <section class="bg-slate-800 p-4 rounded-3xl border border-slate-700">
            <h3 class="text-[10px] font-black text-slate-400 uppercase mb-3">Agenda Próximos 14 días</h3>
            <div id="agenda" class="space-y-2 text-xs">
                </div>
        </section>

        <section class="grid grid-cols-1 gap-3">
            <button onclick="reg('🤱 Pecho')" class="bg-pink-600 p-6 rounded-[30px] font-black text-xl shadow-lg">REGISTRAR PECHO</button>
            <button onclick="reg('🍼 Biberón')" class="bg-blue-600 p-4 rounded-2xl font-bold">🍼 NUEVO BIBERÓN</button>
        </section>

        <section class="bg-slate-800 p-5 rounded-3xl border border-slate-700">
            <div class="flex justify-between mb-4">
                <h3 class="font-black">Medicinas</h3>
                <button onclick="openMed()" class="bg-blue-500 text-[10px] px-3 py-1 rounded-lg font-bold">+ AÑADIR</button>
            </div>
            <div id="lista-med" class="space-y-2"></div>
        </section>

        <section class="bg-slate-800 p-5 rounded-3xl border border-slate-700">
            <h3 class="font-black text-xs text-slate-400 uppercase mb-3">Configuración de tomas</h3>
            <div class="flex gap-2">
                <input id="int-val" type="number" step="0.5" value="3" class="w-20 bg-slate-900 border border-slate-600 rounded-xl p-2 text-center font-bold">
                <button onclick="saveInt()" class="flex-1 bg-emerald-600 rounded-xl font-bold text-xs">GUARDAR INTERVALO</button>
            </div>
        </section>

    </main>

    <div id="m-med" class="hidden fixed inset-0 bg-black/90 z-[100] flex items-center justify-center p-6">
        <div class="bg-slate-800 w-full rounded-3xl p-8 border border-slate-700 space-y-4">
            <h2 class="text-xl font-black">Nuevo Tratamiento</h2>
            <input id="m-n" type="text" placeholder="Nombre" class="w-full p-4 bg-slate-900 rounded-xl">
            <input id="m-d" type="number" placeholder="Duración (Días)" class="w-full p-4 bg-slate-900 rounded-xl">
            <button onclick="saveMed()" class="w-full bg-blue-600 p-4 rounded-xl font-black">EMPEZAR</button>
            <button onclick="openMed()" class="w-full text-slate-500 font-bold text-xs">CERRAR</button>
        </div>
    </div>

    <script>
        let db = JSON.parse(localStorage.getItem('baby_v5')) || {
            bebe: "Bebé",
            nacimiento: "2025-01-01",
            intervalo: 3,
            compras: [],
            medicinas: [],
            eventos: {"2026-02-02": ["Vacuna 12 meses"], "2026-02-10": ["Revisión Pediatra"]}
        };

        function save() {
            localStorage.setItem('baby_v5', JSON.stringify(db));
            render();
        }

        function render() {
            // Agenda 14 días
            const agenda = document.getElementById('agenda');
            agenda.innerHTML = "";
            const hoy = new Date();
            for(let i=0; i<14; i++) {
                let d = new Date(); d.setDate(hoy.getDate() + i);
                let iso = d.toISOString().split('T')[0];
                if(db.eventos[iso]) {
                    db.eventos[iso].forEach(e => {
                        agenda.innerHTML += `<div class="bg-slate-700 p-3 rounded-xl border-l-4 border-orange-500">
                            <b>${d.getDate()}/${d.getMonth()+1}:</b> ${e}
                        </div>`;
                    });
                }
            }
            if(!agenda.innerHTML) agenda.innerHTML = "No hay eventos en 14 días.";

            // Compras
            document.getElementById('status-compra').classList.toggle('hidden', db.compras.length === 0);
            document.getElementById('status-compra').innerText = `🛒 ${db.compras.length} PENDIENTES`;

            // Medicinas (Filtro por caducidad)
            const ahora = Date.now();
            db.medicinas = db.medicinas.filter(m => m.fin > ahora);
            document.getElementById('lista-med').innerHTML = db.medicinas.map(m => `
                <div class="bg-slate-900 p-3 rounded-xl flex justify-between items-center text-xs">
                    <span>${m.nombre} (Fin: ${new Date(m.fin).toLocaleDateString()})</span>
                    <i class="fa-solid fa-check text-emerald-500"></i>
                </div>
            `).join('') || "No hay tratamientos.";
        }

        function addCompra() {
            let i = prompt("¿Qué falta?");
            if(i) { db.compras.push(i); save(); }
        }

        function saveInt() {
            db.intervalo = document.getElementById('int-val').value;
            save();
            alert("Intervalo guardado: " + db.intervalo + "h");
        }

        function openMed() { document.getElementById('m-med').classList.toggle('hidden'); }

        function saveMed() {
            let n = document.getElementById('m-n').value;
            let d = document.getElementById('m-d').value;
            if(n && d) {
                db.medicinas.push({
                    nombre: n,
                    fin: Date.now() + (d * 86400000)
                });
                save();
                openMed();
            }
        }

        function reg(t) { alert("Registrado: " + t); }

        render();
    </script>
</body>
</html>
