<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Desafio Farmácia Clínica - UniEVANGÉLICA</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
        }

        .uni-red { color: #ce0e2d; }
        .uni-bg-red { background-color: #ce0e2d; }
        
        .fade-in {
            animation: fadeIn 0.5s ease-in;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Loading Spinner */
        .loader {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #ce0e2d;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Markdown styles for AI response */
        .ai-content h3 { font-weight: bold; margin-top: 10px; }
        .ai-content ul { list-style-type: disc; margin-left: 20px; }
        .ai-content strong { color: #ce0e2d; }
    </style>
</head>
<body class="h-screen flex flex-col overflow-hidden">

    <!-- Header -->
    <header class="bg-white shadow-sm z-10">
        <div class="max-w-5xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div class="w-8 h-8 uni-bg-red rounded text-white flex items-center justify-center font-bold text-lg">U</div>
                <div>
                    <h1 class="font-bold text-gray-800 leading-tight">Farmácia Hospitalar</h1>
                    <p class="text-xs text-gray-500">Simulador de Interações</p>
                </div>
            </div>
            <div class="hidden md:flex items-center gap-4 text-sm text-gray-600">
                <span><i class="fas fa-user-graduate mr-1"></i> 5º/6º Período</span>
                <span><i class="fas fa-university mr-1"></i> UniEVANGÉLICA</span>
            </div>
        </div>
    </header>

    <!-- Main Content Area -->
    <main class="flex-1 relative flex items-center justify-center p-4">
        
        <!-- Loading Overlay -->
        <div id="loading-overlay" class="hidden absolute inset-0 bg-white/80 z-50 flex flex-col items-center justify-center backdrop-blur-sm">
            <div class="loader mb-4"></div>
            <p id="loading-text" class="text-gray-600 font-bold animate-pulse">Consultando base de dados...</p>
        </div>

        <!-- Screen: Welcome -->
        <div id="screen-welcome" class="text-center max-w-lg w-full bg-white p-8 rounded-2xl shadow-xl fade-in">
            <div class="mb-6 inline-block p-4 bg-red-50 rounded-full">
                <i class="fas fa-prescription-bottle-alt text-4xl uni-red"></i>
            </div>
            <h2 class="text-2xl font-bold text-gray-800 mb-2">Plantão na Farmácia Clínica</h2>
            <p class="text-gray-600 mb-6">
                Você é o farmacêutico responsável pelo setor de UTI. 
                Analise as prescrições e evite erros graves.
            </p>
            
            <div class="bg-blue-50 p-4 rounded-lg text-left text-sm text-blue-800 mb-8 border-l-4 border-blue-500">
                <h3 class="font-bold mb-1"><i class="fas fa-info-circle mr-1"></i> Modos de Jogo:</h3>
                <ul class="list-disc pl-4 space-y-1">
                    <li><strong>Clássico:</strong> 10 casos essenciais pré-definidos.</li>
                    <li><strong>Infinito (IA):</strong> Casos novos gerados por Inteligência Artificial.</li>
                </ul>
            </div>

            <div class="space-y-3">
                <button onclick="game.start(false)" class="w-full uni-bg-red hover:bg-red-700 text-white font-bold py-4 px-6 rounded-xl transition shadow-lg transform hover:-translate-y-1">
                    Iniciar Modo Clássico
                </button>
                <button onclick="game.start(true)" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-4 px-6 rounded-xl transition shadow-lg transform hover:-translate-y-1 flex items-center justify-center gap-2">
                    <i class="fas fa-sparkles text-yellow-300"></i> Modo Infinito com IA
                </button>
            </div>
        </div>

        <!-- Screen: Game -->
        <div id="screen-game" class="hidden w-full max-w-2xl flex flex-col h-full max-h-[800px]">
            
            <!-- Status Bar -->
            <div class="flex justify-between items-center mb-4 bg-white p-3 rounded-lg shadow-sm">
                <div class="flex items-center gap-2">
                    <span class="text-sm font-bold text-gray-500">CASO</span>
                    <span id="case-counter" class="bg-gray-200 text-gray-800 px-2 py-1 rounded text-sm font-bold">1/10</span>
                </div>
                <div class="flex items-center gap-2">
                    <i class="fas fa-heart uni-red animate-pulse"></i>
                    <div class="w-32 h-3 bg-gray-200 rounded-full overflow-hidden">
                        <div id="health-bar" class="h-full bg-green-500 w-full transition-all duration-500"></div>
                    </div>
                </div>
                <div class="font-mono font-bold text-gray-700">
                    PTS: <span id="score-display">0</span>
                </div>
            </div>

            <!-- Patient Card (Scenario) -->
            <div class="bg-white rounded-2xl shadow-lg overflow-hidden flex-1 flex flex-col relative">
                <!-- Header Scenario -->
                <div class="bg-slate-800 text-white p-4 flex justify-between items-start">
                    <div>
                        <div class="flex items-center gap-2 mb-1">
                            <i class="fas fa-procedures text-gray-400"></i>
                            <h3 id="patient-name" class="font-bold text-lg">Paciente...</h3>
                        </div>
                        <p id="patient-context" class="text-slate-300 text-sm">...</p>
                    </div>
                    <div class="bg-slate-700 px-3 py-1 rounded text-xs font-mono text-yellow-400 border border-yellow-600">
                        <i class="fas fa-syringe mr-1"></i> VIA ENDOVENOSA
                    </div>
                </div>

                <!-- Drugs Display -->
                <div class="p-6 flex-1 flex flex-col justify-center items-center relative">
                    <div class="absolute top-4 right-4 text-gray-200 text-9xl opacity-20 pointer-events-none">
                        <i class="fas fa-file-medical"></i>
                    </div>

                    <div class="w-full grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
                        <!-- Drug A -->
                        <div class="border-2 border-blue-100 bg-blue-50 p-4 rounded-xl flex flex-col items-center text-center">
                            <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mb-2 text-blue-600 text-xl">
                                <i class="fas fa-pills"></i>
                            </div>
                            <h4 id="drug-a" class="font-bold text-gray-800 text-lg">...</h4>
                            <p class="text-xs text-gray-500 mt-1">Fármaco A</p>
                        </div>

                        <!-- Connector -->
                        <div class="md:hidden flex justify-center -my-2 z-10">
                            <span class="bg-white border border-gray-200 p-1 rounded-full shadow-sm text-gray-400 text-xs">
                                <i class="fas fa-plus"></i>
                            </span>
                        </div>

                        <!-- Drug B -->
                        <div class="border-2 border-indigo-100 bg-indigo-50 p-4 rounded-xl flex flex-col items-center text-center">
                            <div class="w-12 h-12 bg-indigo-100 rounded-full flex items-center justify-center mb-2 text-indigo-600 text-xl">
                                <i class="fas fa-prescription-bottle"></i>
                            </div>
                            <h4 id="drug-b" class="font-bold text-gray-800 text-lg">...</h4>
                            <p class="text-xs text-gray-500 mt-1">Fármaco B</p>
                        </div>
                    </div>

                    <div class="text-center max-w-md">
                        <p class="text-gray-600 text-sm mb-2">Situação Clínica:</p>
                        <p id="scenario-desc" class="font-medium text-gray-800 text-lg leading-relaxed">
                            ...
                        </p>
                    </div>
                </div>

                <!-- Action Buttons -->
                <div class="p-4 bg-gray-50 border-t border-gray-100 grid grid-cols-2 gap-4">
                    <button onclick="game.makeChoice(false)" class="group bg-white border-2 border-green-500 hover:bg-green-50 text-green-600 font-bold py-4 rounded-xl transition flex flex-col items-center justify-center gap-1">
                        <span class="text-lg"><i class="fas fa-check-circle"></i> APROVAR</span>
                        <span class="text-xs font-normal opacity-75 group-hover:opacity-100">Compatível / Seguro</span>
                    </button>
                    <button onclick="game.makeChoice(true)" class="group bg-white border-2 border-red-500 hover:bg-red-50 text-red-600 font-bold py-4 rounded-xl transition flex flex-col items-center justify-center gap-1">
                        <span class="text-lg"><i class="fas fa-hand-paper"></i> INTERVIR</span>
                        <span class="text-xs font-normal opacity-75 group-hover:opacity-100">Incompatível / Interação</span>
                    </button>
                </div>
            </div>
        </div>

        <!-- Screen: Feedback Modal -->
        <div id="screen-feedback" class="hidden fixed inset-0 bg-black/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 overflow-y-auto">
            <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full overflow-hidden transform transition-all scale-100 my-8">
                <div id="feedback-header" class="p-6 text-center text-white">
                    <div id="feedback-icon" class="text-5xl mb-3"></div>
                    <h3 id="feedback-title" class="text-2xl font-bold"></h3>
                </div>
                <div class="p-6">
                    <h4 class="text-xs font-bold text-gray-400 uppercase tracking-wider mb-2">Análise Farmacêutica</h4>
                    <p id="feedback-text" class="text-gray-700 leading-relaxed mb-4 text-sm"></p>
                    
                    <!-- AI Deep Dive Area -->
                    <div id="ai-explanation-area" class="hidden mb-4 bg-indigo-50 p-4 rounded-lg border border-indigo-100 text-sm">
                        <div class="flex items-center gap-2 text-indigo-700 font-bold mb-2">
                            <i class="fas fa-robot"></i> Análise Detalhada Gemini
                        </div>
                        <div id="ai-explanation-content" class="ai-content text-gray-700 text-xs leading-relaxed"></div>
                    </div>

                    <button onclick="game.getAIExplanation()" id="btn-explain-ai" class="w-full mb-4 border border-indigo-200 text-indigo-600 hover:bg-indigo-50 font-bold py-2 px-4 rounded-lg transition text-sm flex items-center justify-center gap-2">
                        <i class="fas fa-sparkles"></i> Explicar Mecanismo (IA)
                    </button>

                    <div class="bg-gray-100 rounded-lg p-3 mb-6">
                        <p class="text-xs text-gray-500 font-bold mb-1">Conceito Chave:</p>
                        <p id="feedback-tip" class="text-sm text-gray-600 italic"></p>
                    </div>

                    <button onclick="game.nextCase()" class="w-full bg-gray-800 hover:bg-gray-900 text-white font-bold py-3 rounded-xl transition">
                        Próximo Caso <i class="fas fa-arrow-right ml-2"></i>
                    </button>
                </div>
            </div>
        </div>

        <!-- Screen: Results -->
        <div id="screen-result" class="hidden text-center max-w-lg w-full bg-white p-8 rounded-2xl shadow-xl fade-in">
            <div class="mb-6 relative inline-block">
                <i class="fas fa-certificate text-6xl text-yellow-400"></i>
                <i class="fas fa-star text-2xl text-yellow-600 absolute -top-2 -right-2 animate-bounce"></i>
            </div>
            <h2 class="text-3xl font-bold text-gray-800 mb-2">Plantão Encerrado!</h2>
            <p class="text-gray-600 mb-6">Confira seu desempenho final.</p>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <div class="bg-gray-50 p-4 rounded-xl">
                    <p class="text-xs text-gray-500 uppercase">Pontuação</p>
                    <p id="final-score" class="text-3xl font-bold uni-red">0</p>
                </div>
                <div class="bg-gray-50 p-4 rounded-xl">
                    <p class="text-xs text-gray-500 uppercase">Precisão</p>
                    <p id="final-accuracy" class="text-3xl font-bold text-blue-600">0%</p>
                </div>
            </div>

            <div id="rank-badge" class="mb-8 border border-gray-200 rounded-lg p-4 bg-yellow-50">
                <p class="text-sm text-gray-600 mb-1">Classificação:</p>
                <h3 id="rank-title" class="text-xl font-bold text-yellow-700">Estudante Observador</h3>
            </div>

            <button onclick="location.reload()" class="w-full border-2 border-gray-200 hover:bg-gray-50 text-gray-700 font-bold py-3 px-6 rounded-xl transition">
                Jogar Novamente
            </button>
        </div>

    </main>

    <!-- Footer -->
    <footer class="text-center py-2 text-gray-400 text-xs">
        &copy; 2025 UniEVANGÉLICA &bull; Powered by Gemini
    </footer>

    <script>
        // --- Gemini API Helper ---
        const apiKey = ""; // Will be injected by environment
        
        async function callGemini(prompt, isJson = false) {
            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
            
            const payload = {
                contents: [{ parts: [{ text: prompt }] }]
            };

            if (isJson) {
                payload.generationConfig = {
                    responseMimeType: "application/json",
                    responseSchema: {
                        type: "OBJECT",
                        properties: {
                            patient: { type: "STRING" },
                            context: { type: "STRING" },
                            drugA: { type: "STRING" },
                            drugB: { type: "STRING" },
                            scenario: { type: "STRING" },
                            isIncompatible: { type: "BOOLEAN" },
                            reason: { type: "STRING" },
                            explanation: { type: "STRING" },
                            tip: { type: "STRING" }
                        }
                    }
                };
            }

            // Exponential backoff retry logic
            let delay = 1000;
            for (let i = 0; i < 5; i++) {
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (!response.ok) throw new Error(`API Error: ${response.status}`);

                    const data = await response.json();
                    const text = data.candidates?.[0]?.content?.parts?.[0]?.text;
                    
                    if (!text) throw new Error("No content in response");
                    
                    return isJson ? JSON.parse(text) : text;

                } catch (error) {
                    if (i === 4) throw error;
                    await new Promise(r => setTimeout(r, delay));
                    delay *= 2;
                }
            }
        }

        // --- Game Data (Fixed Cases) ---
        const fixedCases = [
            {
                id: 1,
                patient: "Maria L., 72 anos",
                context: "UTI - Pneumonia",
                drugA: "Ceftriaxona (IV)",
                drugB: "Ringer Lactato",
                scenario: "Administração simultânea em Y.",
                isIncompatible: true,
                reason: "Precipitação Fatal",
                explanation: "Ceftriaxona é incompatível com soluções contendo cálcio (como Ringer Lactato). Pode formar precipitados insolúveis nos pulmões e rins, risco fatal.",
                tip: "Nunca misture Ceftriaxona com Cálcio. Use SF 0,9% ou Glicose 5%."
            },
            {
                id: 2,
                patient: "João B., 55 anos",
                context: "Enfermaria - Fibrilação Atrial",
                drugA: "Varfarina (Oral)",
                drugB: "Amiodarona (Oral)",
                scenario: "Paciente iniciou Amiodarona mantendo dose de Varfarina.",
                isIncompatible: true,
                reason: "Interação Farmacocinética Grave",
                explanation: "Amiodarona inibe o metabolismo (CYP2C9) da Varfarina, aumentando drasticamente o INR e o risco de hemorragia.",
                tip: "Ao iniciar Amiodarona, deve-se reduzir a dose de Varfarina em 30-50% e monitorar o INR."
            },
            {
                id: 3,
                patient: "Pedro H., 40 anos",
                context: "Emergência - Crise Convulsiva",
                drugA: "Fenitoína",
                drugB: "Soro Glicosado 5%",
                scenario: "Preparo de infusão para dose de ataque.",
                isIncompatible: true,
                reason: "Incompatibilidade Física",
                explanation: "A Fenitoína precipita (cristaliza) em pH ácido. O Soro Glicosado tem pH levemente ácido (3.5-6.5), causando precipitação imediata.",
                tip: "Fenitoína só é compatível com Soro Fisiológico (NaCl 0,9%)."
            },
            {
                id: 4,
                patient: "Ana C., 28 anos",
                context: "Pós-operatório",
                drugA: "Midazolam",
                drugB: "Fentanil",
                scenario: "Mistura na mesma seringa para sedação.",
                isIncompatible: false,
                reason: "Compatível",
                explanation: "Esta é uma mistura clássica e estável utilizada em anestesia e sedação contínua.",
                tip: "Opioides e Benzodiazepínicos são frequentemente compatíveis fisicamente, mas exigem monitoramento respiratório (sinergismo)."
            },
            {
                id: 5,
                patient: "Carlos R., 60 anos",
                context: "UTI Cardiológica",
                drugA: "Furosemida",
                drugB: "Ondansetrona",
                scenario: "Administração sequencial ou em Y.",
                isIncompatible: true,
                reason: "Precipitação Imediata",
                explanation: "Há formação de precipitado branco imediato ao contato. O pH da Ondansetrona (ácido) é incompatível com o da Furosemida (básico).",
                tip: "Sempre lave o acesso com SF 0,9% entre administrações de medicamentos com pHs extremos."
            },
            {
                id: 6,
                patient: "Lucas M., 33 anos",
                context: "Tratamento de Infecção",
                drugA: "Ciprofloxacino (VO)",
                drugB: "Carbonato de Cálcio",
                scenario: "Administrados no mesmo horário.",
                isIncompatible: true,
                reason: "Quelação (Absorção)",
                explanation: "Cátions bivalentes (Cálcio, Magnésio, Ferro) quelam as quinolonas no trato gastrointestinal, impedindo a absorção do antibiótico.",
                tip: "Administrar com intervalo de pelo menos 2 horas."
            },
            {
                id: 7,
                patient: "Roberto G., 68 anos",
                context: "Insuficiência Cardíaca",
                drugA: "Digoxina",
                drugB: "Furosemida",
                scenario: "Uso crônico concomitante.",
                isIncompatible: true,
                reason: "Interação Farmacodinâmica (Indireta)",
                explanation: "A Furosemida causa hipocalemia (perda de potássio). A hipocalemia aumenta drasticamente a toxicidade da Digoxina, podendo causar arritmias fatais.",
                tip: "Monitorar níveis de K+ rigorosamente e repor se necessário."
            },
            {
                id: 8,
                patient: "Fernanda S., 22 anos",
                context: "Pronto Socorro",
                drugA: "Diazepam",
                drugB: "Soro Fisiológico 0,9%",
                scenario: "Diluição em bolsa de 250ml para correr lento.",
                isIncompatible: true,
                reason: "Precipitação/Adsorção",
                explanation: "O Diazepam é lipofílico. Ao diluir em grande volume, ele precipita (fica turvo) e adere ao plástico do equipo (PVC), perdendo eficácia.",
                tip: "Diazepam deve ser feito preferencialmente puro (EV direto lento) ou diluído minimamente se necessário, evitando bolsas grandes."
            },
            {
                id: 9,
                patient: "Paulo D., 50 anos",
                context: "Infecção Fúngica",
                drugA: "Anfotericina B",
                drugB: "Soro Fisiológico 0,9%",
                scenario: "Diluição para infusão.",
                isIncompatible: true,
                reason: "Precipitação Salina",
                explanation: "A Anfotericina B precipita em solução salina. Ela deve ser diluída exclusivamente em Soro Glicosado 5%.",
                tip: "Anfotericina B = Exclusivo em Glicose 5%."
            },
            {
                id: 10,
                patient: "Cláudia V., 45 anos",
                context: "Dor Aguda",
                drugA: "Morfina",
                drugB: "Dipirona",
                scenario: "Administração em Y.",
                isIncompatible: false,
                reason: "Compatível",
                explanation: "Estudos mostram compatibilidade física em Y para estas drogas nas concentrações usuais.",
                tip: "Sempre verifique a tabela de compatibilidade do seu hospital, mas esta combinação é geralmente segura."
            }
        ];

        // --- Game Logic ---
        const game = {
            score: 0,
            health: 100,
            currentCaseIndex: 0,
            isPlaying: false,
            isInfiniteMode: false,
            infiniteCaseCount: 0,
            currentCaseData: null,

            elements: {
                welcomeScreen: document.getElementById('screen-welcome'),
                gameScreen: document.getElementById('screen-game'),
                feedbackScreen: document.getElementById('screen-feedback'),
                resultScreen: document.getElementById('screen-result'),
                loadingOverlay: document.getElementById('loading-overlay'),
                loadingText: document.getElementById('loading-text'),
                
                patientName: document.getElementById('patient-name'),
                patientContext: document.getElementById('patient-context'),
                drugA: document.getElementById('drug-a'),
                drugB: document.getElementById('drug-b'),
                scenarioDesc: document.getElementById('scenario-desc'),
                
                caseCounter: document.getElementById('case-counter'),
                scoreDisplay: document.getElementById('score-display'),
                healthBar: document.getElementById('health-bar'),
                
                feedbackHeader: document.getElementById('feedback-header'),
                feedbackIcon: document.getElementById('feedback-icon'),
                feedbackTitle: document.getElementById('feedback-title'),
                feedbackText: document.getElementById('feedback-text'),
                feedbackTip: document.getElementById('feedback-tip'),
                aiExplanationArea: document.getElementById('ai-explanation-area'),
                aiExplanationContent: document.getElementById('ai-explanation-content'),
                btnExplainAI: document.getElementById('btn-explain-ai'),

                finalScore: document.getElementById('final-score'),
                finalAccuracy: document.getElementById('final-accuracy'),
                rankTitle: document.getElementById('rank-title'),
            },

            start(isInfinite = false) {
                this.score = 0;
                this.health = 100;
                this.currentCaseIndex = 0;
                this.infiniteCaseCount = 0;
                this.isPlaying = true;
                this.isInfiniteMode = isInfinite;
                this.updateUI();
                
                this.elements.welcomeScreen.classList.add('hidden');
                this.elements.resultScreen.classList.add('hidden');
                this.elements.gameScreen.classList.remove('hidden');
                
                this.loadCase();
            },

            async loadCase() {
                if (this.health <= 0) {
                    this.endGame();
                    return;
                }

                if (!this.isInfiniteMode && this.currentCaseIndex >= fixedCases.length) {
                    this.endGame();
                    return;
                }

                this.elements.loadingOverlay.classList.remove('hidden');
                this.elements.loadingText.innerText = this.isInfiniteMode ? "Gerando novo caso clínico com IA..." : "Carregando caso...";

                try {
                    let currentCase;

                    if (this.isInfiniteMode) {
                        // AI Generation Logic
                        const prompt = `Crie um caso clínico de interação medicamentosa (farmácia hospitalar) desafiador.
                        Retorne APENAS um objeto JSON (sem markdown) com estes campos: 
                        patient (Nome e idade), context (Situação clínica ex: UTI), 
                        drugA (Nome fármaco A + via), drugB (Nome fármaco B + via), 
                        scenario (Descrição do cenário de administração simultânea ou sequencial), 
                        isIncompatible (boolean), 
                        reason (Título curto da interação ou 'Compatível'), 
                        explanation (Explicação curta de 2 linhas), 
                        tip (Dica clínica curta).
                        Varie entre casos compatíveis e incompatíveis.
                        Evite casos extremamente óbvios.`;
                        
                        currentCase = await callGemini(prompt, true);
                        this.infiniteCaseCount++;
                    } else {
                        currentCase = fixedCases[this.currentCaseIndex];
                    }

                    this.currentCaseData = currentCase;
                    
                    // Update UI
                    this.elements.patientName.textContent = `Paciente: ${currentCase.patient}`;
                    this.elements.patientContext.textContent = currentCase.context;
                    this.elements.drugA.textContent = currentCase.drugA;
                    this.elements.drugB.textContent = currentCase.drugB;
                    this.elements.scenarioDesc.textContent = currentCase.scenario;
                    
                    if(this.isInfiniteMode) {
                        this.elements.caseCounter.textContent = `Infinito: #${this.infiniteCaseCount}`;
                    } else {
                        this.elements.caseCounter.textContent = `${this.currentCaseIndex + 1}/${fixedCases.length}`;
                    }

                    // Animation reset
                    this.elements.gameScreen.classList.remove('fade-in');
                    void this.elements.gameScreen.offsetWidth; // trigger reflow
                    this.elements.gameScreen.classList.add('fade-in');

                } catch (error) {
                    console.error("Erro ao gerar caso:", error);
                    alert("Erro ao conectar com a IA. Tente novamente.");
                    this.endGame();
                } finally {
                    this.elements.loadingOverlay.classList.add('hidden');
                }
            },

            makeChoice(userSaysIntervene) {
                if (!this.isPlaying) return;

                const currentCase = this.currentCaseData;
                const isCorrect = userSaysIntervene === currentCase.isIncompatible;

                if (isCorrect) {
                    this.score += 100;
                    this.showFeedback(true, currentCase);
                } else {
                    this.health -= 25;
                    this.showFeedback(false, currentCase);
                }

                this.updateUI();
            },

            showFeedback(isCorrect, caseData) {
                const el = this.elements;
                el.feedbackScreen.classList.remove('hidden');
                el.aiExplanationArea.classList.add('hidden');
                el.btnExplainAI.classList.remove('hidden'); // Reset AI button visibility
                el.aiExplanationContent.innerHTML = "";

                if (isCorrect) {
                    el.feedbackHeader.className = "p-6 text-center text-white bg-green-500";
                    el.feedbackIcon.innerHTML = '<i class="fas fa-check-circle"></i>';
                    el.feedbackTitle.textContent = "Correto!";
                    
                    if(caseData.isIncompatible) {
                        el.feedbackText.innerHTML = `Muito bem! Você evitou um erro. <strong>${caseData.reason}</strong>. <br>${caseData.explanation}`;
                    } else {
                        el.feedbackText.innerHTML = `Exato! A administração é segura. <br>${caseData.explanation}`;
                    }

                } else {
                    el.feedbackHeader.className = "p-6 text-center text-white bg-red-500";
                    el.feedbackIcon.innerHTML = '<i class="fas fa-exclamation-triangle"></i>';
                    el.feedbackTitle.textContent = "Incorreto";
                    
                    if(caseData.isIncompatible) {
                        el.feedbackText.innerHTML = `Cuidado! Esta combinação é <strong>INCOMPATÍVEL</strong>. <br>${caseData.explanation}`;
                    } else {
                        el.feedbackText.innerHTML = `Atenção desnecessária. Esta combinação é na verdade <strong>COMPATÍVEL</strong>. <br>${caseData.explanation}`;
                    }
                }

                el.feedbackTip.textContent = caseData.tip;
            },

            async getAIExplanation() {
                const el = this.elements;
                const caseData = this.currentCaseData;
                
                el.btnExplainAI.innerHTML = '<i class="fas fa-spinner fa-spin"></i> Analisando Mecanismo...';
                
                try {
                    const prompt = `Explique detalhadamente, para um estudante de farmácia, o mecanismo de interação (ou a razão da compatibilidade) entre ${caseData.drugA} e ${caseData.drugB} no cenário: "${caseData.scenario}".
                    Foque em farmacocinética (CYP450, absorção, etc) ou físico-química (pH, precipitação).
                    Seja didático e conciso (max 100 palavras). Use formatação simples.`;

                    const explanation = await callGemini(prompt, false);
                    
                    el.aiExplanationArea.classList.remove('hidden');
                    el.aiExplanationContent.innerText = explanation;
                    el.btnExplainAI.classList.add('hidden');

                } catch (error) {
                    console.error(error);
                    el.btnExplainAI.innerHTML = '<i class="fas fa-exclamation-circle"></i> Erro na IA';
                }
            },

            nextCase() {
                this.elements.feedbackScreen.classList.add('hidden');
                this.currentCaseIndex++;
                this.loadCase();
            },

            updateUI() {
                this.elements.scoreDisplay.textContent = this.score;
                this.elements.healthBar.style.width = `${this.health}%`;
                
                if(this.health <= 30) {
                    this.elements.healthBar.className = "h-full bg-red-500 w-full transition-all duration-500";
                } else if (this.health <= 60) {
                    this.elements.healthBar.className = "h-full bg-yellow-500 w-full transition-all duration-500";
                } else {
                    this.elements.healthBar.className = "h-full bg-green-500 w-full transition-all duration-500";
                }
            },

            endGame() {
                this.isPlaying = false;
                this.elements.gameScreen.classList.add('hidden');
                this.elements.feedbackScreen.classList.add('hidden');
                this.elements.loadingOverlay.classList.add('hidden');
                this.elements.resultScreen.classList.remove('hidden');

                this.elements.finalScore.textContent = this.score;
                
                // Calculate accuracy
                let totalCases = this.isInfiniteMode ? this.infiniteCaseCount : fixedCases.length;
                // Avoid divide by zero if infinite mode crashes on first load
                if (totalCases === 0) totalCases = 1; 

                // Simple estimate for infinite mode
                const percentage = Math.min(100, Math.max(0, Math.round((this.score / (totalCases * 100)) * 100)));
                
                this.elements.finalAccuracy.textContent = `${percentage}%`;

                // Rank Logic
                let rank = "";
                let rankColor = "";
                
                if (this.health <= 0) {
                    rank = "Residente em Treinamento (Game Over)";
                    rankColor = "text-red-600";
                } else if (percentage >= 90) {
                    rank = "Farmacêutico Especialista";
                    rankColor = "text-green-600";
                } else if (percentage >= 70) {
                    rank = "Farmacêutico Pleno";
                    rankColor = "text-blue-600";
                } else {
                    rank = "Farmacêutico Júnior";
                    rankColor = "text-yellow-600";
                }

                this.elements.rankTitle.textContent = rank;
                this.elements.rankTitle.className = `text-xl font-bold ${rankColor}`;
            }
        };
    </script>
</body>
</html>
