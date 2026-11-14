```html
<!DOCTYPE html>
<html lang="es" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CodeNavidad & Copicoins | Comunidad de Scripts</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Fuentes: Inter y Fira Code -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;500;600&family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Highlight.js para resaltado de sintaxis -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/atom-one-dark.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>

    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['Fira Code', 'monospace'],
                    },
                    colors: {
                        night: {
                            950: '#060913',
                            900: '#0b1021',
                            800: '#131b31',
                            700: '#1e2946'
                        },
                        festive: {
                            red: '#e11d48',
                            redDark: '#9f1239',
                            green: '#10b981',
                            greenDark: '#047857',
                            gold: '#f59e0b',
                            goldLight: '#fde68a'
                        }
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background: radial-gradient(circle at top center, #131b31 0%, #060913 70%);
            color: #f8fafc;
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* Glassmorphism */
        .glass-card {
            background: rgba(19, 27, 49, 0.72);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        /* Partículas de nieve en CSS puro (0 bloqueos) */
        .snow-container {
            position: fixed;
            inset: 0;
            pointer-events: none;
            z-index: 1;
            overflow: hidden;
        }
        .snowflake {
            position: absolute;
            top: -10px;
            color: rgba(255, 255, 255, 0.35);
            user-select: none;
            animation: fall linear infinite;
        }
        @keyframes fall {
            0% { transform: translateY(-10vh) translateX(0) rotate(0deg); }
            50% { transform: translateY(50vh) translateX(15px) rotate(180deg); }
            100% { transform: translateY(110vh) translateX(-10px) rotate(360deg); }
        }

        /* Animación de monedas ganadas */
        @keyframes coinPop {
            0% { transform: scale(0.8); }
            50% { transform: scale(1.25); filter: drop-shadow(0 0 12px rgba(245, 158, 11, 0.9)); }
            100% { transform: scale(1); }
        }
        .coin-animate {
            animation: coinPop 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        /* Estilizado de bloques de código */
        pre code.hljs {
            background: #070b14 !important;
            padding: 1rem !important;
            border-radius: 0.75rem;
            font-size: 0.82rem;
            line-height: 1.6;
        }

        ::-webkit-scrollbar { width: 7px; height: 7px; }
        ::-webkit-scrollbar-track { background: #060913; }
        ::-webkit-scrollbar-thumb { background: #1e2946; border-radius: 9999px; }
        ::-webkit-scrollbar-thumb:hover { background: #334155; }
    </style>
</head>
<body class="relative selection:bg-festive-red selection:text-white">

    <!-- Contenedor de nieve CSS -->
    <div id="snow-layer" class="snow-container" aria-hidden="true"></div>

    <!-- Notificaciones Toast (Sin alert) -->
    <div id="toast-box" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2.5 max-w-sm w-full px-4 sm:px-0 pointer-events-none"></div>

    <!-- Modal de Registro Rápido / Edición de Perfil -->
    <div id="welcome-modal" class="fixed inset-0 z-40 bg-black/80 backdrop-blur-md hidden items-center justify-center p-4">
        <div class="glass-card rounded-3xl max-w-md w-full p-6 sm:p-8 shadow-2xl relative overflow-hidden border border-white/15">
            <div class="absolute top-0 inset-x-0 h-1.5 bg-gradient-to-r from-festive-red via-festive-gold to-festive-green"></div>
            
            <div class="text-center mb-6">
                <div class="inline-flex items-center justify-center w-16 h-16 rounded-2xl bg-gradient-to-br from-festive-red/20 to-festive-gold/20 border border-festive-gold/30 text-3xl mb-3 shadow-lg">
                    🎄
                </div>
                <h2 class="text-2xl font-extrabold text-white tracking-tight">CodeNavidad & Copicoins</h2>
                <p class="text-sm text-slate-300 mt-1.5">
                    Publica tus scripts, recibe el veredicto de <span class="text-festive-gold font-semibold">SantaDev IA</span> y escala en el ranking de la comunidad.
                </p>
            </div>

            <form id="profile-form" class="space-y-5">
                <div>
                    <label class="block text-xs font-bold uppercase tracking-wider text-slate-400 mb-2">Tu Alias Navideño</label>
                    <input type="text" id="input-username" required maxlength="22" placeholder="Ej. ElfMaster, GrinchCoder, NoelJS..."
                        class="w-full bg-night-950/90 border border-slate-700 rounded-xl px-4 py-3 text-white placeholder-slate-500 focus:outline-none focus:border-festive-gold focus:ring-1 focus:ring-festive-gold transition">
                </div>

                <div>
                    <label class="block text-xs font-bold uppercase tracking-wider text-slate-400 mb-2">Elige tu Avatar</label>
                    <div class="grid grid-cols-6 gap-2" id="avatar-grid">
                        <button type="button" data-avatar="🎅" class="avatar-choice text-2xl p-2 rounded-xl bg-night-950 border-2 border-festive-gold transition hover:scale-105">🎅</button>
                        <button type="button" data-avatar="🤶" class="avatar-choice text-2xl p-2 rounded-xl bg-night-950 border border-slate-700 transition hover:scale-105">🤶</button>
                        <button type="button" data-avatar="🧝" class="avatar-choice text-2xl p-2 rounded-xl bg-night-950 border border-slate-700 transition hover:scale-105">🧝</button>
                        <button type="button" data-avatar="⛄" class="avatar-choice text-2xl p-2 rounded-xl bg-night-950 border border-slate-700 transition hover:scale-105">⛄</button>
                        <button type="button" data-avatar="🦌" class="avatar-choice text-2xl p-2 rounded-xl bg-night-950 border border-slate-700 transition hover:scale-105">🦌</button>
                        <button type="button" data-avatar="🐧" class="avatar-choice text-2xl p-2 rounded-xl bg-night-950 border border-slate-700 transition hover:scale-105">🐧</button>
                    </div>
                </div>

                <div class="bg-festive-green/10 border border-festive-green/30 rounded-xl p-3 flex items-center gap-3">
                    <span class="text-xl">🎁</span>
                    <p class="text-xs text-emerald-200">
                        <strong class="font-bold">Bono de Bienvenida:</strong> Recibes <span class="text-festive-gold font-bold">+10 Copicoins</span> al registrarte en la comunidad.
                    </p>
                </div>

                <button type="submit" class="w-full py-3.5 px-5 rounded-xl font-bold text-white bg-gradient-to-r from-festive-red to-rose-700 hover:from-rose-500 hover:to-festive-red shadow-lg shadow-rose-950/50 transition transform active:scale-[0.99]">
                    Comenzar a Programar 🚀
                </button>
            </form>
        </div>
    </div>

    <!-- Header / Barra Superior -->
    <header class="sticky top-0 z-30 bg-night-950/85 backdrop-blur-lg border-b border-white/10">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between gap-3">
            <!-- Marca -->
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-festive-red to-rose-800 flex items-center justify-center text-xl shadow-lg shadow-rose-950/40 border border-rose-400/30">
                    🎄
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <span class="font-extrabold text-lg sm:text-xl tracking-tight text-white">Code<span class="text-festive-red">Navidad</span></span>
                        <span class="hidden md:inline-flex items-center px-2 py-0.5 rounded-full text-[10px] font-bold uppercase tracking-wider bg-festive-green/15 text-festive-green border border-festive-green/30">
                            Comunidad Activa
                        </span>
                    </div>
                </div>
            </div>

            <!-- Controles de Usuario y Billetera -->
            <div class="flex items-center gap-2.5 sm:gap-4">
                <!-- Contador de Copicoins -->
                <div id="wallet-badge" class="flex items-center gap-2 bg-night-800/90 border border-festive-gold/40 px-3.5 py-1.5 rounded-full shadow-inner">
                    <span class="text-lg leading-none">🪙</span>
                    <div class="flex items-baseline gap-1.5">
                        <span id="user-coins-display" class="font-extrabold text-festive-gold text-sm sm:text-base">0</span>
                        <span class="text-[11px] font-semibold text-amber-200/80 hidden sm:inline">Copicoins</span>
                    </div>
                </div>

                <!-- Botón Perfil -->
                <button id="open-profile-btn" type="button" class="flex items-center gap-2 bg-night-800/90 hover:bg-night-700 border border-white/10 px-3 py-1.5 rounded-full transition" title="Cambiar alias o avatar">
                    <span id="user-avatar-display" class="text-lg leading-none">🎅</span>
                    <span id="user-name-display" class="text-xs sm:text-sm font-semibold text-slate-200 max-w-[95px] sm:max-w-[140px] truncate">Invitado</span>
                    <svg class="w-3.5 h-3.5 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.232 5.232l3.536 3.536m-2.036-5.036a2.5 2.5 0 113.536 3.536L6.5 21.036H3v-3.572L16.732 3.732z"></path></svg>
                </button>
            </div>
        </div>
    </header>

    <!-- Layout Principal -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 sm:py-8 relative z-10">
        
        <!-- Selector de Vista en Móvil -->
        <div class="grid grid-cols-2 gap-2 p-1 rounded-2xl glass-card mb-6 lg:hidden">
            <button id="mobile-tab-wall" type="button" class="py-2.5 rounded-xl text-xs font-bold bg-festive-red text-white shadow transition">
                💻 Publicar y Muro
            </button>
            <button id="mobile-tab-rank" type="button" class="py-2.5 rounded-xl text-xs font-bold text-slate-400 hover:text-white transition">
                🏆 Top Copicoins
            </button>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
            
            <!-- Columna Principal: Editor + Muro de Scripts (8 cols) -->
            <div id="wall-column" class="lg:col-span-8 space-y-8">
                
                <!-- Caja de Publicación con SantaDev IA -->
                <section class="glass-card rounded-3xl p-5 sm:p-7 shadow-2xl relative overflow-hidden">
                    <div class="absolute top-0 left-0 w-1.5 h-full bg-gradient-to-b from-festive-green via-festive-gold to-festive-red"></div>
                    
                    <div class="flex flex-wrap items-center justify-between gap-2 mb-5">
                        <div class="flex items-center gap-3">
                            <div class="w-11 h-11 rounded-2xl bg-festive-green/15 border border-festive-green/30 flex items-center justify-center text-2xl">
                                🤖
                            </div>
                            <div>
                                <h1 class="text-lg sm:text-xl font-extrabold text-white">Publicar Script para Evaluación IA</h1>
                                <p class="text-xs text-slate-400">SantaDev IA analizará tu código y te premiará con <span class="text-festive-gold font-semibold">15 a 100 Copicoins</span></p>
                            </div>
                        </div>
                        <span class="text-[11px] font-mono bg-night-950/80 border border-white/10 text-emerald-400 px-3 py-1 rounded-full">
                            ● Motor SantaDev IA Listo
                        </span>
                    </div>

                    <form id="publish-form" class="space-y-4">
                        <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                            <div class="sm:col-span-2">
                                <label class="block text-xs font-semibold text-slate-300 mb-1.5">Título del Script</label>
                                <input type="text" id="script-title" required maxlength="65" placeholder="Ej. Generador de Nieve en Pantalla o Auto-Farm Regalos"
                                    class="w-full bg-night-950/90 border border-slate-700/80 rounded-xl px-4 py-2.5 text-sm text-white placeholder-slate-500 focus:outline-none focus:border-festive-green transition">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-slate-300 mb-1.5">Lenguaje (Auto-detectable)</label>
                                <select id="script-lang" class="w-full bg-night-950/90 border border-slate-700/80 rounded-xl px-3.5 py-2.5 text-sm text-white focus:outline-none focus:border-festive-green transition">
                                    <option value="Auto">✨ Detectar con IA</option>
                                    <option value="Lua">Lua</option>
                                    <option value="Python">Python</option>
                                    <option value="JavaScript">JavaScript</option>
                                    <option value="HTML/CSS">HTML / CSS</option>
                                    <option value="C#">C# / C++</option>
                                </select>
                            </div>
                        </div>

                        <div>
                            <div class="flex items-center justify-between mb-1.5">
                                <label class="block text-xs font-semibold text-slate-300">Pega tu código aquí</label>
                                <span id="code-stats" class="text-[11px] font-mono text-slate-400">0 líneas • 0 caracteres</span>
                            </div>
                            <textarea id="script-code" required rows="7" maxlength="5000" spellcheck="false"
                                placeholder="-- Escribe o pega tu script navideño aquí...&#10;local function entregarRegalos(listaNinos)&#10;    for _, nino in ipairs(listaNinos) do&#10;        print('🎁 Regalo entregado a: ' .. nino)&#10;    end&#10;end"
                                class="w-full bg-[#070b14] border border-slate-700/80 rounded-2xl p-4 font-mono text-xs sm:text-sm text-emerald-300 placeholder-slate-600 focus:outline-none focus:border-festive-green transition leading-relaxed"></textarea>
                        </div>

                        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 pt-1">
                            <span class="text-xs text-slate-400">
                                ⚠️ El spam, texto sin sentido o códigos vacíos reciben <strong class="text-rose-400">0 Copicoins</strong>.
                            </span>
                            <button type="submit" id="btn-evaluate" class="py-3 px-6 rounded-xl font-bold text-sm text-white bg-gradient-to-r from-festive-green to-emerald-700 hover:from-emerald-400 hover:to-festive-green shadow-lg shadow-emerald-950/50 transition flex items-center justify-center gap-2 shrink-0">
                                <span>✨ Evaluar y Publicar Script</span>
                            </button>
                        </div>
                    </form>

                    <!-- Banner animado de análisis IA -->
                    <div id="ai-analyzing-box" class="hidden mt-4 p-4 rounded-2xl bg-night-950/90 border border-festive-gold/40 flex items-center gap-3.5">
                        <div class="w-6 h-6 border-2 border-festive-gold border-t-transparent rounded-full animate-spin shrink-0"></div>
                        <div>
                            <p class="text-sm font-bold text-festive-gold">SantaDev IA está auditando tu código...</p>
                            <p class="text-xs text-slate-400">Analizando estructuras, sintaxis, comentarios y creatividad navideña.</p>
                        </div>
                    </div>
                </section>

                <!-- Barra de Filtros del Muro -->
                <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                    <div class="flex items-center gap-2.5">
                        <h2 class="text-xl font-extrabold text-white">📜 Muro de la Comunidad</h2>
                        <span id="feed-counter" class="px-2.5 py-0.5 rounded-full text-xs font-bold bg-white/10 text-slate-200">0</span>
                    </div>

                    <div class="flex flex-wrap items-center gap-1.5" id="lang-filters">
                        <button type="button" data-filter="ALL" class="filter-btn px-3 py-1.5 rounded-xl text-xs font-bold bg-festive-red text-white transition">Todos</button>
                        <button type="button" data-filter="Lua" class="filter-btn px-3 py-1.5 rounded-xl text-xs font-semibold glass-card text-slate-300 hover:text-white transition">Lua</button>
                        <button type="button" data-filter="Python" class="filter-btn px-3 py-1.5 rounded-xl text-xs font-semibold glass-card text-slate-300 hover:text-white transition">Python</button>
                        <button type="button" data-filter="JavaScript" class="filter-btn px-3 py-1.5 rounded-xl text-xs font-semibold glass-card text-slate-300 hover:text-white transition">JavaScript</button>
                    </div>
                </div>

                <!-- Lista de Scripts Publicados -->
                <div id="scripts-list" class="space-y-6"></div>
            </div>

            <!-- Columna Lateral: Tabla de Posiciones / Ranking en Vivo (4 cols) -->
            <aside id="rank-column" class="hidden lg:block lg:col-span-4 space-y-6 sticky top-24">
                <div class="glass-card rounded-3xl p-6 shadow-2xl relative overflow-hidden">
                    <div class="absolute top-0 inset-x-0 h-1 bg-gradient-to-r from-festive-gold via-amber-200 to-festive-gold"></div>
                    
                    <div class="flex items-center justify-between mb-5">
                        <div class="flex items-center gap-2.5">
                            <span class="text-2xl">🏆</span>
                            <div>
                                <h2 class="text-lg font-extrabold text-white">Top Copicoins</h2>
                                <p class="text-xs text-slate-400">Clasificación de la comunidad</p>
                            </div>
                        </div>
                        <span class="text-[11px] font-semibold text-emerald-400 bg-emerald-500/10 border border-emerald-500/20 px-2.5 py-1 rounded-full">
                            En vivo
                        </span>
                    </div>

                    <div id="leaderboard-container" class="space-y-2.5 max-h-[480px] overflow-y-auto pr-1"></div>

                    <div class="mt-5 pt-4 border-t border-white/10 flex items-center justify-between text-xs text-slate-400">
                        <span>Miembros activos: <strong id="members-count" class="text-white">0</strong> / 100</span>
                        <button type="button" id="reset-demo-btn" class="text-slate-500 hover:text-rose-400 underline transition" title="Restaurar datos de ejemplo">
                            Reiniciar demo
                        </button>
                    </div>
                </div>

                <!-- Guía de Puntuación -->
                <div class="glass-card rounded-3xl p-5 border border-festive-gold/20 bg-gradient-to-br from-amber-500/5 to-transparent">
                    <h3 class="text-sm font-bold text-festive-gold mb-2.5 flex items-center gap-2">
                        <span>💡</span> Criterios de SantaDev IA
                    </h3>
                    <ul class="text-xs text-slate-300 space-y-2 leading-relaxed">
                        <li class="flex items-start gap-2">
                            <span class="text-festive-green font-bold">✓</span>
                            <span><strong>15 - 35 🪙:</strong> Scripts cortos o funcionales básicos.</span>
                        </li>
                        <li class="flex items-start gap-2">
                            <span class="text-festive-green font-bold">✓</span>
                            <span><strong>36 - 70 🪙:</strong> Uso de funciones, bucles, condicionales y buena estructura.</span>
                        </li>
                        <li class="flex items-start gap-2">
                            <span class="text-festive-green font-bold">✓</span>
                            <span><strong>71 - 100 🪙:</strong> Código completo con comentarios claros y temática navideña.</span>
                        </li>
                    </ul>
                </div>
            </aside>
        </div>
    </main>

    <!-- Script Principal 100% Autocontenido -->
    <script>
        (function() {
            // ==========================================
            // 1. CLAVES DE ALMACENAMIENTO Y DATOS INICIALES
            // ==========================================
            const STORAGE_KEYS = {
                USER: 'codenavidad_current_user_v1',
                USERS: 'codenavidad_all_users_v1',
                SCRIPTS: 'codenavidad_scripts_v1'
            };

            const INITIAL_USERS = [
                { id: 'u_noel', username: 'SantaDev_Oficial', avatar: '🎅', copicoins: 245 },
                { id: 'u_elfo', username: 'ElfoScripter', avatar: '🧝', copicoins: 190 },
                { id: 'u_reno', username: 'RudolphLua', avatar: '🦌', copicoins: 155 },
                { id: 'u_nieve', username: 'FrostyPy', avatar: '⛄', copicoins: 120 },
                { id: 'u_mamanoel', username: 'ClausJS', avatar: '🤶', copicoins: 95 }
            ];

            const INITIAL_SCRIPTS = [
                {
                    id: 'sc_1',
                    title: 'Sistema de Entrega de Regalos en Trineo',
                    language: 'Lua',
                    authorId: 'u_noel',
                    authorName: 'SantaDev_Oficial',
                    authorAvatar: '🎅',
                    copicoins: 88,
                    summary: 'Itera sobre una tabla de jugadores conectados en el servidor y les asigna un regalo aleatorio con efecto de partículas.',
                    judgeComment: '¡Excelente estructura con tablas y bucles en Lua! Los renos aprueban este código limpio.',
                    likes: ['u_elfo', 'u_reno', 'u_nieve'],
                    createdAt: Date.now() - 1000 * 60 * 42,
                    code: `-- Script navideño para repartir regalos en el servidor
local regalos = {"Espada de Caramelo", "Trineo Turbo", "Gorro de Santa", "500 Copicoins"}

function repartirRegalosNavidad(jugadores)
    for _, jugador in ipairs(jugadores) do
        local premio = regalos[math.random(1, #regalos)]
        print("🎅 ¡Feliz Navidad " .. jugador .. "! Recibiste: " .. premio)
    end
end

repartirRegalosNavidad({"Alex", "Sofía", "Mateo", "Valeria"})`
                },
                {
                    id: 'sc_2',
                    title: 'Árbol de Navidad ASCII Generativo con Estrellas',
                    language: 'Python',
                    authorId: 'u_nieve',
                    authorName: 'FrostyPy',
                    authorAvatar: '⛄',
                    copicoins: 76,
                    summary: 'Genera en consola un pino navideño de altura configurable decorado con luces y una estrella en la cima.',
                    judgeComment: 'Muy creativo y festivo. El uso de comprensiones y formato de cadenas en Python está impecable.',
                    likes: ['u_noel', 'u_mamanoel'],
                    createdAt: Date.now() - 1000 * 60 * 115,
                    code: `import random

def dibujar_arbol_navidad(altura=8):
    print(" " * altura + "🌟")
    adornos = ["*", "o", "+", "@"]
    for i in range(1, altura + 1):
        fila = "".join(random.choice(adornos) for _ in range(2 * i - 1))
        print(" " * (altura - i) + fila)
    print(" " * (altura - 1) + "|||")

if __name__ == "__main__":
    dibujar_arbol_navidad(7)`
                },
                {
                    id: 'sc_3',
                    title: 'Cuenta Regresiva en Tiempo Real para Nochebuena',
                    language: 'JavaScript',
                    authorId: 'u_mamanoel',
                    authorName: 'ClausJS',
                    authorAvatar: '🤶',
                    copicoins: 82,
                    summary: 'Calcula exactamente los días, horas, minutos y segundos restantes hasta el 25 de diciembre.',
                    judgeComment: 'Código súper útil para cualquier web navideña. Manejo limpio del objeto Date.',
                    likes: ['u_noel', 'u_elfo', 'u_reno', 'u_nieve'],
                    createdAt: Date.now() - 1000 * 60 * 210,
                    code: `function cuentaRegresivaNavidad() {
  const ahora = new Date();
  const navidad = new Date(ahora.getFullYear(), 11, 25);
  if (ahora > navidad) navidad.setFullYear(navidad.getFullYear() + 1);

  const diff = navidad - ahora;
  const dias = Math.floor(diff / (1000 * 60 * 60 * 24));
  const horas = Math.floor((diff / (1000 * 60 * 60)) % 24);
  
  return \`🎄 Faltan \${dias} días y \${horas} horas para Navidad!\`;
}

console.log(cuentaRegresivaNavidad());`
                },
                {
                    id: 'sc_4',
                    title: 'Detector de Espíritu Navideño en Chat',
                    language: 'Lua',
                    authorId: 'u_reno',
                    authorName: 'RudolphLua',
                    authorAvatar: '🦌',
                    copicoins: 64,
                    summary: 'Analiza mensajes del chat buscando palabras festivas y otorga puntos de karma positivo al usuario.',
                    judgeComment: 'Buen uso de coincidencia de patrones string.match. ¡A los elfos les encantó!',
                    likes: ['u_elfo'],
                    createdAt: Date.now() - 1000 * 60 * 340,
                    code: `local palabrasClave = {"navidad", "felices fiestas", "jojojo", "regalo", "nieve"}

function evaluarMensaje(usuario, mensaje)
    local texto = string.lower(mensaje)
    for _, palabra in ipairs(palabrasClave) do
        if string.find(texto, palabra) then
            return usuario .. " ganó +5 puntos de Espíritu Navideño 🎄"
        end
    end
    return "Mensaje normal"
end`
                },
                {
                    id: 'sc_5',
                    title: 'Mezclador Secreto de Amigo Invisible (Sin Auto-Asignación)',
                    language: 'Python',
                    authorId: 'u_elfo',
                    authorName: 'ElfoScripter',
                    authorAvatar: '🧝',
                    copicoins: 94,
                    summary: 'Implementa un algoritmo de permutación circular para garantizar que nadie se toque a sí mismo en el intercambio.',
                    judgeComment: '¡Algoritmo brillante y libre de errores! Merece casi la puntuación máxima por su utilidad para los 100 miembros.',
                    likes: ['u_noel', 'u_mamanoel', 'u_reno'],
                    createdAt: Date.now() - 1000 * 60 * 500,
                    code: `import random

def sortear_amigo_secreto(participantes):
    if len(participantes) < 2:
        return {}
    mezcla = participantes[:]
    random.shuffle(mezcla)
    # Asignación circular: cada quien regala al siguiente
    return {
        mezcla[i]: mezcla[(i + 1) % len(mezcla)]
        for i in range(len(mezcla))
    }

print(sortear_amigo_secreto(["Ana", "Carlos", "Elena", "Luis"]))`
                }
            ];

            // Estado de la aplicación
            let currentUser = null;
            let allUsers = [];
            let allScripts = [];
            let activeFilter = 'ALL';
            let selectedAvatar = '🎅';

            // ==========================================
            // 2. INICIALIZACIÓN INSTANTÁNEA (SIN BLOQUEOS)
            // ==========================================
            function init() {
                createSnowflakes();
                loadStateFromStorage();
                bindEvents();
                renderAll();

                // Si es la primera visita, mostrar modal de bienvenida inmediatamente
                if (!currentUser) {
                    openWelcomeModal(true);
                }
            }

            function loadStateFromStorage() {
                try {
                    const savedUsers = localStorage.getItem(STORAGE_KEYS.USERS);
                    const savedScripts = localStorage.getItem(STORAGE_KEYS.SCRIPTS);
                    const savedCurrentUser = localStorage.getItem(STORAGE_KEYS.USER);

                    allUsers = savedUsers ? JSON.parse(savedUsers) : [...INITIAL_USERS];
                    allScripts = savedScripts ? JSON.parse(savedScripts) : [...INITIAL_SCRIPTS];
                    currentUser = savedCurrentUser ? JSON.parse(savedCurrentUser) : null;

                    if (!savedUsers) localStorage.setItem(STORAGE_KEYS.USERS, JSON.stringify(allUsers));
                    if (!savedScripts) localStorage.setItem(STORAGE_KEYS.SCRIPTS, JSON.stringify(allScripts));
                } catch (e) {
                    allUsers = [...INITIAL_USERS];
                    allScripts = [...INITIAL_SCRIPTS];
                }
            }

            function saveStateToStorage() {
                try {
                    localStorage.setItem(STORAGE_KEYS.USERS, JSON.stringify(allUsers));
                    localStorage.setItem(STORAGE_KEYS.SCRIPTS, JSON.stringify(allScripts));
                    if (currentUser) {
                        localStorage.setItem(STORAGE_KEYS.USER, JSON.stringify(currentUser));
                    }
                } catch (e) {
                    console.warn('No se pudo guardar en localStorage', e);
                }
            }

            // ==========================================
            // 3. MOTOR DE EVALUACIÓN "SANTADEV IA"
            // ==========================================
            async function evaluateScriptWithSantaAI(title, chosenLang, code) {
                // Primero: Validación instantánea anti-spam y detección heurística local
                const cleanCode = code.trim();
                const lines = cleanCode.split('\n').filter(l => l.trim().length > 0);
                
                // Detectar texto basura, repeticiones o texto sin estructura de código
                const uniqueChars = new Set(cleanCode.replace(/\s/g, '')).size;
                const hasCodeTokens = /[{}();=<>[\]:]|function|local|def|const|let|var|if|for|while|print|console|return|import|#|--|\/\//i.test(cleanCode);

                if (cleanCode.length < 18 || uniqueChars < 6 || !hasCodeTokens) {
                    const spamReplies = [
                        "¡Jo, jo, no! Eso parece una carta en blanco o teclas aplastadas por un reno. Pega código real para ganar Copicoins.",
                        "Mis elfos revisaron esto con lupa y no encontraron ni una variable. ¡Intenta escribir un script de verdad!",
                        "¡Carbón digital! Este envío es demasiado corto o no parece código de programación."
                    ];
                    return {
                        isValid: false,
                        language: chosenLang === 'Auto' ? 'Texto' : chosenLang,
                        copicoins: 0,
                        summary: "Envío inválido detectado por el filtro anti-spam.",
                        judgeComment: spamReplies[Math.floor(Math.random() * spamReplies.length)]
                    };
                }

                // Detectar lenguaje automáticamente si está en "Auto"
                const detectedLang = chosenLang === 'Auto' ? detectProgrammingLanguage(cleanCode) : chosenLang;

                // Intentar conectar con Gemini API si hay entorno disponible; si falla o no hay red, usar motor inteligente local al instante
                try {
                    const apiKey = "";
                    if (typeof fetch !== 'undefined' && window.location.protocol !== 'file:') {
                        const controller = new AbortController();
                        const timeoutId = setTimeout(() => controller.abort(), 4500); // Máximo 4.5s para nunca congelar la UI

                        const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
                        const prompt = `Evalúa este script para una comunidad navideña. Responde SOLO JSON con: isValid (boolean), language (string), copicoins (int 15-100, o 0 si es spam), summary (resumen en español max 140 chars), judgeComment (comentario navideño corto de SantaDev IA).
Título: ${title}
Lenguaje: ${detectedLang}
Código:\n${cleanCode.slice(0, 2500)}`;

                        const res = await fetch(apiUrl, {
                            method: 'POST',
                            headers: { 'Content-Type': 'application/json' },
                            signal: controller.signal,
                            body: JSON.stringify({
                                contents: [{ parts: [{ text: prompt }] }],
                                generationConfig: {
                                    responseMimeType: "application/json",
                                    responseSchema: {
                                        type: "OBJECT",
                                        properties: {
                                            isValid: { type: "BOOLEAN" },
                                            language: { type: "STRING" },
                                            copicoins: { type: "INTEGER" },
                                            summary: { type: "STRING" },
                                            judgeComment: { type: "STRING" }
                                        },
                                        required: ["isValid", "language", "copicoins", "summary", "judgeComment"]
                                    }
                                }
                            })
                        });
                        clearTimeout(timeoutId);

                        if (res.ok) {
                            const data = await res.json();
                            const rawText = data.candidates?.[0]?.content?.parts?.[0]?.text;
                            if (rawText) {
                                const parsed = JSON.parse(rawText);
                                return {
                                    isValid: Boolean(parsed.isValid),
                                    language: parsed.language || detectedLang,
                                    copicoins: parsed.isValid ? Math.max(15, Math.min(100, Number(parsed.copicoins) || 35)) : 0,
                                    summary: parsed.summary || generateLocalSummary(cleanCode, detectedLang, title),
                                    judgeComment: parsed.judgeComment || "¡Gran aporte para el taller de Santa!"
                                };
                            }
                        }
                    }
                } catch (_) {
                    // Fallback silencioso al motor local determinístico
                }

                // Motor Local Inteligente SantaDev IA (100% offline/instantáneo)
                return runLocalSantaEvaluator(title, detectedLang, cleanCode, lines);
            }

            function detectProgrammingLanguage(code) {
                if (/\blocal\b|\bthen\b|\bend\b|\bipairs\b|\bpairs\b|--\[\[/.test(code)) return 'Lua';
                if (/\bdef\b|\bimport\b|\belif\b|print\(|if __name__/.test(code)) return 'Python';
                if (/\bconst\b|\blet\b|=>|console\.log|document\.|function\s*\(/.test(code)) return 'JavaScript';
                if (/<html|<div|<style|<script|<body/i.test(code)) return 'HTML/CSS';
                if (/\busing System|\bpublic class|\bvoid Main|\b#include/.test(code)) return 'C#';
                return 'JavaScript';
            }

            function runLocalSantaEvaluator(title, lang, code, lines) {
                let score = 18; // Base por código válido

                // Puntos por longitud y líneas
                score += Math.min(25, lines.length * 2);

                // Puntos por estructuras de control y funciones
                const hasFunctions = /(function|def|=>|void|class)\b/.test(code);
                const hasLoops = /(for|while|forEach|map|ipairs|pairs)\b/.test(code);
                const hasConditionals = /(if|else|elif|switch|case)\b/.test(code);
                const hasComments = /(\/\/|--|#)/.test(code);
                const hasChristmasTheme = /(navidad|santa|noel|regalo|nieve|elfo|reno|trineo|xmas|christmas|copicoin)/i.test(code + ' ' + title);

                if (hasFunctions) score += 14;
                if (hasLoops) score += 12;
                if (hasConditionals) score += 8;
                if (hasComments) score += 10;
                if (hasChristmasTheme) score += 13;

                const finalCoins = Math.max(15, Math.min(100, score));

                // Construir comentario personalizado según nota
                let comment = "";
                if (finalCoins >= 75) {
                    comment = "¡Obra maestra navideña! Estructura sólida, excelente lógica y espíritu festivo de sobra. ¡Los elfos están impresionados!";
                } else if (finalCoins >= 45) {
                    comment = "¡Buen trabajo! El código está bien organizado y cumple su propósito. Un par de comentarios extra y sería digno del trineo dorado.";
                } else {
                    comment = "¡Script aprobado! Es un buen comienzo funcional. Prueba añadir funciones o bucles en tu próximo aporte para multiplicar tus Copicoins.";
                }

                return {
                    isValid: true,
                    language: lang,
                    copicoins: finalCoins,
                    summary: generateLocalSummary(code, lang, title),
                    judgeComment: comment
                };
            }

            function generateLocalSummary(code, lang, title) {
                const funcMatch = code.match(/(?:function|def)\s+([a-zA-Z0-9_]+)/);
                const linesCount = code.split('\n').filter(l => l.trim()).length;
                if (funcMatch && funcMatch[1]) {
                    return `Script en ${lang} (${linesCount} líneas) que define la función '${funcMatch[1]}' para implementar ${title.toLowerCase()}.`;
                }
                return `Implementación en ${lang} de ${linesCount} líneas diseñada para ejecutar "${title}" de forma automatizada.`;
            }

            // ==========================================
            // 4. EVENTOS DE INTERFAZ Y FORMULARIOS
            // ==========================================
            function bindEvents() {
                // Selección de avatar
                document.querySelectorAll('.avatar-choice').forEach(btn => {
                    btn.addEventListener('click', () => {
                        selectedAvatar = btn.dataset.avatar;
                        document.querySelectorAll('.avatar-choice').forEach(b => {
                            b.classList.remove('border-2', 'border-festive-gold');
                            b.classList.add('border', 'border-slate-700');
                        });
                        btn.classList.remove('border', 'border-slate-700');
                        btn.classList.add('border-2', 'border-festive-gold');
                    });
                });

                // Abrir modal perfil
                document.getElementById('open-profile-btn').addEventListener('click', () => openWelcomeModal(false));

                // Guardar perfil
                document.getElementById('profile-form').addEventListener('submit', (e) => {
                    e.preventDefault();
                    const nameInput = document.getElementById('input-username').value.trim();
                    if (!nameInput) return;

                    const isNewUser = !currentUser;
                    if (isNewUser) {
                        currentUser = {
                            id: 'u_' + Date.now(),
                            username: nameInput,
                            avatar: selectedAvatar,
                            copicoins: 10 // Bono de bienvenida
                        };
                        allUsers.push(currentUser);
                        showToast(`¡Bienvenido ${nameInput}! Ganaste +10 Copicoins de regalo 🎁`, 'success');
                    } else {
                        currentUser.username = nameInput;
                        currentUser.avatar = selectedAvatar;
                        const idx = allUsers.findIndex(u => u.id === currentUser.id);
                        if (idx !== -1) allUsers[idx] = { ...currentUser };
                        showToast('Perfil navideño actualizado ✨', 'success');
                    }

                    saveStateToStorage();
                    closeWelcomeModal();
                    renderAll();
                });

                // Contador en vivo del editor
                const codeArea = document.getElementById('script-code');
                const statsLabel = document.getElementById('code-stats');
                codeArea.addEventListener('input', () => {
                    const val = codeArea.value;
                    const lines = val ? val.split('\n').length : 0;
                    statsLabel.textContent = `${lines} líneas • ${val.length} caracteres`;
                });

                // Publicar y evaluar script
                document.getElementById('publish-form').addEventListener('submit', async (e) => {
                    e.preventDefault();
                    if (!currentUser) {
                        openWelcomeModal(true);
                        return;
                    }

                    const title = document.getElementById('script-title').value.trim();
                    const chosenLang = document.getElementById('script-lang').value;
                    const code = codeArea.value;

                    const btn = document.getElementById('btn-evaluate');
                    const analyzingBox = document.getElementById('ai-analyzing-box');

                    btn.disabled = true;
                    analyzingBox.classList.remove('hidden');

                    // Breve pausa visual de 550ms para sensación de inspección IA
                    await new Promise(r => setTimeout(r, 550));
                    const result = await evaluateScriptWithSantaAI(title, chosenLang, code);

                    btn.disabled = false;
                    analyzingBox.classList.add('hidden');

                    if (!result.isValid || result.copicoins <= 0) {
                        showToast(`0 Copicoins: ${result.judgeComment}`, 'error');
                        return;
                    }

                    // Crear publicación
                    const newScript = {
                        id: 'sc_' + Date.now(),
                        title,
                        language: result.language,
                        authorId: currentUser.id,
                        authorName: currentUser.username,
                        authorAvatar: currentUser.avatar,
                        copicoins: result.copicoins,
                        summary: result.summary,
                        judgeComment: result.judgeComment,
                        likes: [],
                        createdAt: Date.now(),
                        code: code.trim()
                    };

                    allScripts.unshift(newScript);

                    // Sumar Copicoins al usuario
                    currentUser.copicoins += result.copicoins;
                    const userIndex = allUsers.findIndex(u => u.id === currentUser.id);
                    if (userIndex !== -1) {
                        allUsers[userIndex].copicoins = currentUser.copicoins;
                    }

                    saveStateToStorage();
                    document.getElementById('publish-form').reset();
                    statsLabel.textContent = '0 líneas • 0 caracteres';

                    // Animar billetera
                    const walletBadge = document.getElementById('wallet-badge');
                    walletBadge.classList.remove('coin-animate');
                    void walletBadge.offsetWidth;
                    walletBadge.classList.add('coin-animate');

                    showToast(`¡Script aprobado! +${result.copicoins} Copicoins añadidos 🪙`, 'success');
                    renderAll();
                });

                // Filtros por lenguaje
                document.querySelectorAll('.filter-btn').forEach(btn => {
                    btn.addEventListener('click', () => {
                        activeFilter = btn.dataset.filter;
                        document.querySelectorAll('.filter-btn').forEach(b => {
                            b.classList.remove('bg-festive-red', 'text-white');
                            b.classList.add('glass-card', 'text-slate-300');
                        });
                        btn.classList.remove('glass-card', 'text-slate-300');
                        btn.classList.add('bg-festive-red', 'text-white');
                        renderScripts();
                    });
                });

                // Tabs móviles
                const tabWall = document.getElementById('mobile-tab-wall');
                const tabRank = document.getElementById('mobile-tab-rank');
                const wallCol = document.getElementById('wall-column');
                const rankCol = document.getElementById('rank-column');

                tabWall.addEventListener('click', () => {
                    tabWall.className = 'py-2.5 rounded-xl text-xs font-bold bg-festive-red text-white shadow transition';
                    tabRank.className = 'py-2.5 rounded-xl text-xs font-bold text-slate-400 hover:text-white transition';
                    wallCol.classList.remove('hidden');
                    rankCol.classList.add('hidden');
                });

                tabRank.addEventListener('click', () => {
                    tabRank.className = 'py-2.5 rounded-xl text-xs font-bold bg-festive-red text-white shadow transition';
                    tabWall.className = 'py-2.5 rounded-xl text-xs font-bold text-slate-400 hover:text-white transition';
                    rankCol.classList.remove('hidden');
                    wallCol.classList.add('hidden');
                });

                // Botón reiniciar demo
                document.getElementById('reset-demo-btn').addEventListener('click', () => {
                    localStorage.removeItem(STORAGE_KEYS.USERS);
                    localStorage.removeItem(STORAGE_KEYS.SCRIPTS);
                    localStorage.removeItem(STORAGE_KEYS.USER);
                    currentUser = null;
                    loadStateFromStorage();
                    renderAll();
                    openWelcomeModal(true);
                    showToast('Datos de demostración restaurados 🎄', 'info');
                });
            }

            // ==========================================
            // 5. RENDERIZADO DE UI
            // ==========================================
            function renderAll() {
                renderHeader();
                renderScripts();
                renderLeaderboard();
            }

            function renderHeader() {
                if (currentUser) {
                    document.getElementById('user-name-display').textContent = currentUser.username;
                    document.getElementById('user-avatar-display').textContent = currentUser.avatar;
                    document.getElementById('user-coins-display').textContent = currentUser.copicoins;
                } else {
                    document.getElementById('user-name-display').textContent = 'Registrarse';
                    document.getElementById('user-avatar-display').textContent = '🎅';
                    document.getElementById('user-coins-display').textContent = '0';
                }
            }

            function renderScripts() {
                const container = document.getElementById('scripts-list');
                const filtered = activeFilter === 'ALL'
                    ? allScripts
                    : allScripts.filter(s => s.language.toLowerCase() === activeFilter.toLowerCase());

                document.getElementById('feed-counter').textContent = filtered.length;

                if (filtered.length === 0) {
                    container.innerHTML = `
                        <div class="glass-card rounded-3xl p-10 text-center">
                            <div class="text-4xl mb-3">❄️</div>
                            <h3 class="text-base font-bold text-white">No hay scripts en esta categoría</h3>
                            <p class="text-xs text-slate-400 mt-1">¡Publica el primer código en ${escapeHtml(activeFilter)} y llévate los Copicoins!</p>
                        </div>
                    `;
                    return;
                }

                container.innerHTML = filtered.map(script => {
                    const userId = currentUser ? currentUser.id : 'guest';
                    const liked = script.likes && script.likes.includes(userId);
                    const likeCount = script.likes ? script.likes.length : 0;
                    const langClass = getLangBadgeClass(script.language);
                    const hlLang = mapHighlightLang(script.language);

                    return `
                    <article class="glass-card rounded-3xl p-5 sm:p-6 shadow-xl hover:border-white/20 transition">
                        <div class="flex flex-wrap items-start justify-between gap-3 mb-4">
                            <div class="flex items-center gap-3">
                                <div class="w-11 h-11 rounded-2xl bg-night-950 border border-white/10 flex items-center justify-center text-2xl shrink-0">
                                    ${escapeHtml(script.authorAvatar)}
                                </div>
                                <div>
                                    <h3 class="font-bold text-white text-base sm:text-lg leading-snug">${escapeHtml(script.title)}</h3>
                                    <div class="flex items-center gap-2 text-xs text-slate-400 mt-0.5">
                                        <span class="font-semibold text-slate-200">${escapeHtml(script.authorName)}</span>
                                        <span>•</span>
                                        <span>${timeAgo(script.createdAt)}</span>
                                    </div>
                                </div>
                            </div>

                            <div class="flex items-center gap-2">
                                <span class="px-2.5 py-1 rounded-xl text-xs font-bold border ${langClass}">
                                    ${escapeHtml(script.language)}
                                </span>
                                <span class="px-3 py-1 rounded-xl text-xs font-extrabold bg-festive-gold/15 text-festive-gold border border-festive-gold/30 flex items-center gap-1">
                                    +${script.copicoins} 🪙
                                </span>
                            </div>
                        </div>

                        <!-- Análisis de SantaDev IA -->
                        <div class="bg-night-950/75 border border-white/10 rounded-2xl p-3.5 mb-4 space-y-2">
                            <p class="text-xs sm:text-sm text-slate-200">
                                <span class="text-festive-green font-bold">📋 Resumen IA:</span> ${escapeHtml(script.summary)}
                            </p>
                            <p class="text-xs text-amber-200/90 italic border-t border-white/5 pt-2">
                                🎅 "${escapeHtml(script.judgeComment)}"
                            </p>
                        </div>

                        <!-- Bloque de código con Highlight.js -->
                        <div class="rounded-2xl overflow-hidden border border-white/10 bg-[#070b14]">
                            <div class="flex items-center justify-between px-4 py-2 bg-white/[0.03] border-b border-white/10">
                                <div class="flex items-center gap-1.5">
                                    <span class="w-2.5 h-2.5 rounded-full bg-rose-500/80"></span>
                                    <span class="w-2.5 h-2.5 rounded-full bg-amber-400/80"></span>
                                    <span class="w-2.5 h-2.5 rounded-full bg-emerald-500/80"></span>
                                    <span class="ml-2 text-[11px] font-mono text-slate-400">${escapeHtml(script.language)}</span>
                                </div>
                                <button type="button" data-copy-id="${script.id}" class="copy-code-btn text-xs font-semibold text-slate-300 hover:text-white bg-white/5 hover:bg-white/10 px-3 py-1 rounded-lg transition">
                                    📋 Copiar código
                                </button>
                            </div>
                            <pre class="overflow-x-auto max-h-72 m-0"><code class="language-${hlLang}">${escapeHtml(script.code)}</code></pre>
                        </div>

                        <!-- Pie: Aplausos -->
                        <div class="mt-4 pt-3 border-t border-white/5 flex items-center justify-between">
                            <button type="button" data-like-id="${script.id}" class="like-script-btn flex items-center gap-2 px-3.5 py-1.5 rounded-xl text-xs font-bold border transition ${
                                liked
                                ? 'bg-festive-red/20 text-rose-300 border-festive-red/40'
                                : 'bg-night-950/70 hover:bg-white/5 text-slate-400 hover:text-white border-white/10'
                            }">
                                <span>👏</span>
                                <span>${liked ? 'Aplaudido' : 'Aplaudir'}</span>
                                <span class="px-1.5 py-0.5 rounded bg-black/40 text-white">${likeCount}</span>
                            </button>
                        </div>
                    </article>
                    `;
                }).join('');

                // Aplicar resaltado de sintaxis si Highlight.js está cargado
                if (window.hljs) {
                    container.querySelectorAll('pre code').forEach(block => window.hljs.highlightElement(block));
                }

                // Enlazar botones dinámicos de copiar y aplaudir
                container.querySelectorAll('.copy-code-btn').forEach(btn => {
                    btn.addEventListener('click', () => copyScript(btn.dataset.copyId));
                });
                container.querySelectorAll('.like-script-btn').forEach(btn => {
                    btn.addEventListener('click', () => toggleLike(btn.dataset.likeId));
                });
            }

            function renderLeaderboard() {
                const container = document.getElementById('leaderboard-container');
                document.getElementById('members-count').textContent = allUsers.length;

                const sorted = [...allUsers].sort((a, b) => b.copicoins - a.copicoins);

                container.innerHTML = sorted.map((u, idx) => {
                    const pos = idx + 1;
                    const isMe = currentUser && u.id === currentUser.id;
                    let medal = `<span class="w-6 text-center font-mono text-xs font-bold text-slate-500">#${pos}</span>`;
                    if (pos === 1) medal = `<span class="w-6 text-center text-base">🥇</span>`;
                    if (pos === 2) medal = `<span class="w-6 text-center text-base">🥈</span>`;
                    if (pos === 3) medal = `<span class="w-6 text-center text-base">🥉</span>`;

                    return `
                    <div class="flex items-center justify-between p-2.5 rounded-2xl border transition ${
                        isMe
                        ? 'bg-festive-gold/15 border-festive-gold/40'
                        : 'bg-night-950/60 border-white/5'
                    }">
                        <div class="flex items-center gap-2.5 min-w-0">
                            ${medal}
                            <span class="text-lg shrink-0">${escapeHtml(u.avatar)}</span>
                            <span class="text-xs sm:text-sm font-semibold truncate ${isMe ? 'text-festive-gold' : 'text-slate-200'}">
                                ${escapeHtml(u.username)} ${isMe ? '(Tú)' : ''}
                            </span>
                        </div>
                        <span class="font-extrabold text-xs sm:text-sm text-festive-gold shrink-0 pl-2">
                            ${u.copicoins} 🪙
                        </span>
                    </div>
                    `;
                }).join('');
            }

            // ==========================================
            // 6. ACCIONES Y UTILIDADES
            // ==========================================
            function copyScript(scriptId) {
                const script = allScripts.find(s => s.id === scriptId);
                if (!script) return;

                const temp = document.createElement('textarea');
                temp.value = script.code;
                temp.style.position = 'fixed';
                temp.style.left = '-9999px';
                document.body.appendChild(temp);
                temp.select();
                try {
                    document.execCommand('copy');
                    showToast('¡Código copiado al portapapeles! 📋', 'success');
                } catch (_) {
                    showToast('No se pudo copiar el código.', 'error');
                }
                document.body.removeChild(temp);
            }

            function toggleLike(scriptId) {
                if (!currentUser) {
                    openWelcomeModal(true);
                    return;
                }
                const script = allScripts.find(s => s.id === scriptId);
                if (!script) return;

                if (!Array.isArray(script.likes)) script.likes = [];
                const idx = script.likes.indexOf(currentUser.id);
                if (idx === -1) {
                    script.likes.push(currentUser.id);
                } else {
                    script.likes.splice(idx, 1);
                }
                saveStateToStorage();
                renderScripts();
            }

            function openWelcomeModal(isFirst) {
                const modal = document.getElementById('welcome-modal');
                const input = document.getElementById('input-username');
                if (!isFirst && currentUser) {
                    input.value = currentUser.username;
                    selectedAvatar = currentUser.avatar;
                    document.querySelectorAll('.avatar-choice').forEach(b => {
                        if (b.dataset.avatar === selectedAvatar) {
                            b.classList.add('border-2', 'border-festive-gold');
                            b.classList.remove('border', 'border-slate-700');
                        } else {
                            b.classList.remove('border-2', 'border-festive-gold');
                            b.classList.add('border', 'border-slate-700');
                        }
                    });
                }
                modal.classList.remove('hidden');
                modal.classList.add('flex');
            }

            function closeWelcomeModal() {
                const modal = document.getElementById('welcome-modal');
                modal.classList.add('hidden');
                modal.classList.remove('flex');
            }

            function showToast(msg, type = 'info') {
                const box = document.getElementById('toast-box');
                const el = document.createElement('div');
                const styles = type === 'success'
                    ? 'bg-emerald-950/95 border-festive-green text-emerald-100'
                    : type === 'error'
                    ? 'bg-rose-950/95 border-festive-red text-rose-100'
                    : 'bg-night-800/95 border-white/15 text-white';

                el.className = `pointer-events-auto border ${styles} px-4 py-3 rounded-2xl shadow-2xl text-xs sm:text-sm font-semibold backdrop-blur-md transition-all duration-300 opacity-0 translate-y-2`;
                el.textContent = msg;
                box.appendChild(el);

                requestAnimationFrame(() => el.classList.remove('opacity-0', 'translate-y-2'));
                setTimeout(() => {
                    el.classList.add('opacity-0', 'translate-y-2');
                    setTimeout(() => el.remove(), 300);
                }, 4000);
            }

            function createSnowflakes() {
                const container = document.getElementById('snow-layer');
                const symbols = ['❄', '❅', '•'];
                for (let i = 0; i < 28; i++) {
                    const flake = document.createElement('span');
                    flake.className = 'snowflake';
                    flake.textContent = symbols[i % symbols.length];
                    flake.style.left = `${Math.random() * 100}%`;
                    flake.style.fontSize = `${Math.random() * 10 + 8}px`;
                    flake.style.animationDuration = `${Math.random() * 10 + 10}s`;
                    flake.style.animationDelay = `${Math.random() * -15}s`;
                    container.appendChild(flake);
                }
            }

            function getLangBadgeClass(lang) {
                const l = lang.toLowerCase();
                if (l.includes('lua')) return 'bg-sky-500/15 text-sky-300 border-sky-500/30';
                if (l.includes('python')) return 'bg-emerald-500/15 text-emerald-300 border-emerald-500/30';
                if (l.includes('javascript')) return 'bg-amber-500/15 text-amber-300 border-amber-500/30';
                return 'bg-purple-500/15 text-purple-300 border-purple-500/30';
            }

            function mapHighlightLang(lang) {
                const l = lang.toLowerCase();
                if (l.includes('lua')) return 'lua';
                if (l.includes('python')) return 'python';
                if (l.includes('javascript')) return 'javascript';
                if (l.includes('html')) return 'xml';
                return 'csharp';
            }

            function timeAgo(ts) {
                const mins = Math.floor((Date.now() - ts) / 60000);
                if (mins < 1) return 'Ahora mismo';
                if (mins < 60) return `Hace ${mins} min`;
                const hrs = Math.floor(mins / 60);
                if (hrs < 24) return `Hace ${hrs} h`;
                return `Hace ${Math.floor(hrs / 24)} d`;
            }

            function escapeHtml(str) {
                return String(str || '')
                    .replace(/&/g, '&amp;')
                    .replace(/</g, '&lt;')
                    .replace(/>/g, '&gt;')
                    .replace(/"/g, '&quot;')
                    .replace(/'/g, '&#039;');
            }

            // Ejecutar de inmediato al parsear el script
            if (document.readyState === 'loading') {
                document.addEventListener('DOMContentLoaded', init);
            } else {
                init();
            }
        })();
    </script>
</body>
</html>
```
