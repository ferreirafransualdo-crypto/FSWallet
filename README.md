<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>AgroFleet - Localizador de Frotas</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { sans: ['Inter', 'sans-serif'] },
                    colors: {
                        agro: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#22c55e',
                            600: '#16a34a',
                            700: '#15803d',
                            800: '#166534',
                            900: '#14532d',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { background-color: #0f172a; color: #f1f5f9; font-family: 'Inter', sans-serif; }
        .card-agro { background-color: #1e293b; border: 1px solid #334155; border-radius: 1rem; padding: 1.25rem; box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.3); }
        .input-agro { background-color: #0f172a; border: 1px solid #334155; color: white; border-radius: 0.75rem; padding: 0.75rem 0.75rem 0.75rem 2.5rem; width: 100%; outline: none; }
        .input-agro:focus { border-color: #22c55e; }
        .btn-agro { background-color: #16a34a; color: white; font-weight: bold; border-radius: 0.75rem; padding: 1rem; width: 100%; text-align: center; display: flex; align-items: center; justify-content: center; gap: 0.5rem; cursor: pointer; border: none; }
        .btn-agro:hover { background-color: #22c55e; }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 font-sans min-h-screen flex flex-col justify-between pb-8">

    <!-- Top Header -->
    <header class="bg-slate-800 border-b border-slate-700 px-4 py-3 sticky top-0 z-50">
        <div class="max-w-3xl mx-auto flex items-center justify-between">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-agro-600 flex items-center justify-center text-white font-bold text-xl shadow-lg">
                    <i class="fa-solid fa-satellite-dish"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight text-white">AgroFleet Locator</h1>
                    <p class="text-xs text-slate-400">Localização de Maquinário & Frotas</p>
                </div>
            </div>
            <div class="flex items-center gap-2 px-3 py-1 rounded-full bg-agro-900/80 border border-agro-500/50 text-agro-300 text-xs font-semibold">
                <span class="w-2 h-2 rounded-full bg-agro-400 animate-pulse"></span>
                <span>Sistema Online</span>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-3xl mx-auto w-full px-4 pt-4 flex-1 space-y-4">

        <!-- Banner de Alerta -->
        <div id="alertBox" class="hidden p-4 rounded-xl border flex items-start gap-3 transition-all">
            <i id="alertIcon" class="fa-solid fa-circle-info mt-0.5 text-lg"></i>
            <div class="flex-1 text-sm" id="alertMessage"></div>
            <button type="button" onclick="hideAlert()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>

        <!-- PAINEL DE BUSCA DE FROTA -->
        <section class="card-agro space-y-4">
            <div class="border-b border-slate-700 pb-3">
                <h2 class="text-lg font-bold text-slate-200 flex items-center gap-2">
                    <i class="fa-solid fa-magnifying-glass-location text-agro-500"></i> Localizar Equipamento no Campo
                </h2>
                <p class="text-xs text-slate-400 mt-0.5">Digite o número da frota para traçar a rota até o maquinário.</p>
            </div>

            <form id="searchFleetForm" class="space-y-4" onsubmit="event.preventDefault(); localizarFrota();">
                <div>
                    <label class="block text-xs font-semibold uppercase tracking-wider text-slate-400 mb-1">Número da Frota / Equipamento *</label>
                    <div class="relative">
                        <i class="fa-solid fa-tractor absolute left-3 top-3.5 text-slate-500"></i>
                        <input type="text" id="inputSearchFleet" required placeholder="Ex: 336862 ou Trator 04" class="input-agro placeholder-slate-500 font-bold uppercase">
                    </div>
                </div>

                <button type="submit" class="btn-agro shadow-lg text-base">
                    <i class="fa-solid fa-location-arrow"></i> BUSCAR LOCALIZAÇÃO & ROTA
                </button>
            </form>
        </section>

        <!-- RESULTADO DA LOCALIZAÇÃO (OCULTO ATÉ BUSCAR) -->
        <section id="resultSection" class="hidden card-agro border-agro-500/40 space-y-4 bg-gradient-to-b from-slate-900 to-slate-800">
            <div class="flex justify-between items-start border-b border-slate-700 pb-3">
                <div>
                    <span class="text-[10px] font-extrabold uppercase text-agro-400 tracking-wider">Equipamento Encontrado</span>
                    <h3 id="resFleetName" class="text-2xl font-black text-white mt-0.5">---</h3>
                </div>
                <span id="resStatus" class="px-2.5 py-1 bg-agro-900/80 border border-agro-500 text-agro-300 text-xs font-bold rounded-full">
                    <i class="fa-solid fa-circle-check mr-1"></i> Operando
                </span>
            </div>

            <div class="grid grid-cols-2 gap-3 text-center">
                <div class="bg-slate-800/80 p-3 rounded-xl border border-slate-700">
                    <span class="text-[10px] font-bold text-slate-400 uppercase">Distância Estimada</span>
                    <div id="resDistance" class="text-2xl font-black text-agro-400 mt-1">-- km</div>
                </div>
                <div class="bg-slate-800/80 p-3 rounded-xl border border-slate-700">
                    <span class="text-[10px] font-bold text-slate-400 uppercase">Última Atualização</span>
                    <div id="resTime" class="text-sm font-bold text-slate-200 mt-2">Agora mesmo</div>
                </div>
            </div>

            <div class="bg-slate-900/90 p-3 rounded-xl border border-slate-800 text-xs space-y-1.5 text-slate-300">
                <div><strong>Local / Setor:</strong> <span id="resLocation" class="text-white">Talhão 04 - Fazenda Santa Maria</span></div>
                <div><strong>Coordenadas GPS:</strong> <span id="resCoords" class="font-mono text-agro-400">-20.12345, -47.98765</span></div>
            </div>

            <button type="button" onclick="abrirNavegacaoGPS()" class="w-full py-4 bg-blue-600 hover:bg-blue-500 text-white font-extrabold rounded-xl text-base shadow-xl transition flex items-center justify-center gap-2 cursor-pointer">
                <i class="fa-solid fa-map-location-dot text-xl"></i> ABRIR ROTA NO GOOGLE MAPS 🗺️
            </button>
        </section>

        <!-- LISTA DE FROTAS RECENTES NA ÁREA -->
        <section class="card-agro space-y-3">
            <h3 class="text-sm font-bold text-slate-300 flex items-center gap-2">
                <i class="fa-solid fa-list-check text-blue-400"></i> Frotas Monitoradas Recentemente
            </h3>
            <div class="space-y-2">
                <div onclick="selecionarFrotaRapida('336862')" class="bg-slate-900 border border-slate-800 p-3 rounded-xl flex justify-between items-center cursor-pointer hover:border-agro-500 transition">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-lg bg-slate-800 flex items-center justify-center text-agro-400 font-bold">
                            <i class="fa-solid fa-truck"></i>
                        </div>
                        <div>
                            <div class="font-bold text-white text-xs">Frota 336862</div>
                            <div class="text-[10px] text-slate-400">Caminhão de Apoio</div>
                        </div>
                    </div>
                    <span class="text-xs font-bold text-agro-400">Ver Rota <i class="fa-solid fa-chevron-right ml-1"></i></span>
                </div>

                <div onclick="selecionarFrotaRapida('TRATOR-04')" class="bg-slate-900 border border-slate-800 p-3 rounded-xl flex justify-between items-center cursor-pointer hover:border-agro-500 transition">
                    <div class="flex items-center gap-3">
                        <div class="w-8 h-8 rounded-lg bg-slate-800 flex items-center justify-center text-agro-400 font-bold">
                            <i class="fa-solid fa-tractor"></i>
                        </div>
                        <div>
                            <div class="font-bold text-white text-xs">Trator 04</div>
                            <div class="text-[10px] text-slate-400">Frente de Plantio</div>
                        </div>
                    </div>
                    <span class="text-xs font-bold text-agro-400">Ver Rota <i class="fa-solid fa-chevron-right ml-1"></i></span>
                </div>
            </div>
        </section>

    </main>

    <footer class="max-w-3xl mx-auto w-full px-4 text-center text-xs text-slate-500 mt-6">
        <p>AgroFleet Locator v1.0 - Base de Frotas</p>
    </footer>

    <!-- SCRIPT DO APLICATIVO -->
    <script>
        let coordenadaAtualDestino = { lat: -20.12345, lng: -47.98765 };

        function selecionarFrotaRapida(frota) {
            document.getElementById('inputSearchFleet').value = frota;
            localizarFrota();
        }

        function localizarFrota() {
            const frota = document.getElementById('inputSearchFleet').value.trim().toUpperCase();
            if (!frota) {
                showAlert('Digite o número da frota para buscar.', 'error');
                return;
            }

            // Simulando a busca dos dados do maquinário (no futuro integrado com nuvem/banco)
            document.getElementById('resFleetName').innerText = `Frota ${frota}`;
            document.getElementById('resDistance').innerText = (Math.random() * 3 + 0.5).toFixed(1) + " km";
            document.getElementById('resLocation').innerText = `Talhão 0${Math.floor(Math.random() * 5 + 1)} - Fazenda Boa Vista`;
            
            // Gerando coordenadas simuladas próximas
            const lat = -20.1000 + (Math.random() * 0.05);
            const lng = -47.9000 + (Math.random() * 0.05);
            coordenadaAtualDestino = { lat, lng };
            document.getElementById('resCoords').innerText = `${lat.toFixed(5)}, ${lng.toFixed(5)}`;

            document.getElementById('resultSection').classList.remove('hidden');
            showAlert(`Maquinário ${frota} localizado com sucesso!`, 'success');
        }

        function abrirNavegacaoGPS() {
            // Abre diretamente o Google Maps no celular traçando rota para as coordenadas do maquinário
            const url = `https://www.google.com/maps/dir/?api=1&destination=${coordenadaAtualDestino.lat},${coordenadaAtualDestino.lng}&travelmode=driving`;
            window.open(url, '_blank');
        }

        function showAlert(msg, type) {
            const box = document.getElementById('alertBox');
            const msgEl = document.getElementById('alertMessage');
            if (!box || !msgEl) return;
            msgEl.innerText = msg;
            box.className = `p-4 rounded-xl border flex items-start gap-3 transition-all ${type === 'error' ? 'bg-red-950 border-red-700 text-red-200' : 'bg-agro-950 border-agro-700 text-agro-200'}`;
            box.classList.remove('hidden');
            setTimeout(() => box.classList.add('hidden'), 4000);
        }

        function hideAlert() {
            const box = document.getElementById('alertBox');
            if (box) box.classList.add('hidden');
        }
    </script>
</body>
</html>
