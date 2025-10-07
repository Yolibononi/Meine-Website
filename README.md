<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="color-scheme" content="dark">
    <title>Mathe-GPT Profi Chat</title>
    <link rel="preconnect" href="https://fonts.googleapis.com" crossorigin>
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Lobster&display=swap');
        * { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 10px;
            color-scheme: dark;
        }
        .chat-container {
            width: 700px;
            max-width: 100%;
            height: 90vh;
            max-height: 900px;
            display: flex;
            flex-direction: column;
            background: rgba(0, 0, 0, 0.85);
            border-radius: 12px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.37);
            padding: 20px;
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
        }
        .chat-header {
            text-align: center;
            margin-bottom: 15px;
            padding-bottom: 15px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        .chat-header h1 {
            margin: 0;
            font-size: 36px;
            font-family: 'Lobster', cursive;
            background: linear-gradient(90deg, #ff7f50, #ff4500, #1e90ff, #32cd32);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        #chatHistory { 
            flex-grow: 1; 
            overflow-y: auto; 
            padding: 10px; 
            border-radius: 8px; 
            background: rgba(255, 255, 255, 0.05); 
            margin-bottom: 15px; 
            scroll-behavior: smooth; 
            scrollbar-width: thin; 
            scrollbar-color: rgba(255, 127, 80, 0.5) rgba(255, 255, 255, 0.05);
        }
        #chatHistory::-webkit-scrollbar { width: 8px; }
        #chatHistory::-webkit-scrollbar-track { background: rgba(255, 255, 255, 0.05); border-radius: 4px; }
        #chatHistory::-webkit-scrollbar-thumb { background: rgba(255, 127, 80, 0.5); border-radius: 4px; }
        #chatHistory::-webkit-scrollbar-thumb:hover { background: rgba(255, 127, 80, 0.7); }
        .msg { 
            padding: 12px 15px; 
            border-radius: 12px; 
            margin-bottom: 10px; 
            word-break: break-word; 
            max-width: 85%; 
            line-height: 1.5; 
            animation: fadeIn 0.3s ease; 
            white-space: pre-wrap;
        }
        @keyframes fadeIn { 
            from { opacity: 0; transform: translateY(10px); } 
            to { opacity: 1; transform: translateY(0); } 
        }
        .msg.user { 
            background: linear-gradient(135deg, #4a4e69, #5a5f7a); 
            margin-left: auto; 
            border-bottom-right-radius: 4px; 
        }
        .msg.bot { 
            background: linear-gradient(135deg, #22223b, #32324b); 
            margin-right: auto; 
            border-bottom-left-radius: 4px; 
        }
        .msg.bot.loading { 
            background: linear-gradient(135deg, #2a2a4b, #3a3a5b); 
            animation: pulse 1.5s ease-in-out infinite; 
        }
        @keyframes pulse { 
            0%, 100% { opacity: 0.6; } 
            50% { opacity: 1; } 
        }
        .input-container { 
            display: flex; 
            gap: 10px; 
            align-items: center; 
        }
        .input-container input { 
            flex: 1; 
            padding: 12px 15px; 
            border-radius: 8px; 
            border: 2px solid rgba(255, 255, 255, 0.1); 
            font-size: 16px; 
            background: rgba(255, 255, 255, 0.05); 
            color: #fff; 
            outline: none; 
            transition: all 0.3s ease; 
        }
        .input-container input:focus { 
            border-color: #ff7f50; 
            background: rgba(255, 255, 255, 0.08); 
        }
        .input-container input::placeholder { 
            color: rgba(255, 255, 255, 0.5); 
        }
        .input-container button { 
            padding: 12px 24px; 
            border: none; 
            border-radius: 8px; 
            background: linear-gradient(135deg, #ff7f50, #ff4500); 
            color: #fff; 
            cursor: pointer; 
            font-size: 16px; 
            font-weight: 600; 
            transition: all 0.3s ease; 
            white-space: nowrap; 
        }
        .input-container button:hover:not(:disabled) { 
            transform: translateY(-2px); 
            box-shadow: 0 4px 12px rgba(255, 127, 80, 0.4); 
        }
        .input-container button:active:not(:disabled) { 
            transform: translateY(0); 
        }
        .input-container button:disabled { 
            opacity: 0.5; 
            cursor: not-allowed; 
        }
        @media (max-width: 600px) { 
            .chat-container { padding: 15px; height: 95vh; } 
            .chat-header h1 { font-size: 28px; } 
            .msg { max-width: 90%; } 
            .input-container button { padding: 12px 18px; } 
        }
        @media (prefers-reduced-motion: reduce) {
            * { animation: none !important; transition: none !important; }
        }
        .sr-only { 
            position: absolute; 
            width: 1px; 
            height: 1px; 
            padding: 0; 
            margin: -1px; 
            overflow: hidden; 
            clip: rect(0, 0, 0, 0); 
            white-space: nowrap; 
            border-width: 0; 
        }
    </style>
</head>
<body>
<div class="chat-container" role="main">
    <div class="chat-header">
        <h1>Luca</h1>
    </div>
    <div id="chatHistory" role="log" aria-live="polite" aria-relevant="additions" aria-label="Chat-Verlauf" tabindex="0"></div>
    <div class="input-container">
        <label for="chatInput" class="sr-only">Nachricht eingeben</label>
        <input id="chatInput" type="text" placeholder="Stelle eine Mathe-Frage..." aria-label="Mathe-Frage eingeben" autocomplete="off">
        <button id="sendBtn" type="button" aria-label="Nachricht senden">Senden</button>
    </div>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/11.8.0/math.min.js"></script>
<script>
    math.config({ number: 'BigNumber', precision: 256 });

    const chatHistory = document.getElementById('chatHistory');
    const chatInput = document.getElementById('chatInput');
    const sendBtn = document.getElementById('sendBtn');

    let isProcessing = false;

    function addMessage(text, sender, isLoading = false) {
        const messageDiv = document.createElement('div');
        messageDiv.className = `msg ${sender}`;
        if (isLoading) {
            messageDiv.classList.add('loading');
            messageDiv.setAttribute('aria-label', 'Bot denkt nach');
        }
        messageDiv.textContent = text;
        chatHistory.appendChild(messageDiv);
        setTimeout(() => { chatHistory.scrollTop = chatHistory.scrollHeight; }, 10);
        return messageDiv;
    }

    const conversationalResponses = {
        'hallo': 'Hallo! 👋 Ich bin dein Mathe-Assistent. Frag mich alles, was mit Zahlen zu tun hat.',
        'hi': 'Hey! 😊 Bereit für Berechnungen?',
        'hey': 'Hey! Was kann ich für dich berechnen?',
        'guten tag': 'Guten Tag! Wie kann ich dir helfen?',
        'guten morgen': 'Guten Morgen! ☀ Lass uns rechnen!',
        'guten abend': 'Guten Abend! 🌙 Bereit für Mathe?',
        'danke': 'Gern geschehen! 👍',
        'vielen dank': 'Sehr gerne! Immer wieder.',
        'wie gehts': 'Mir geht es prima! 🤖 Ich bin bereit zu rechnen.',
        'wie geht es dir': 'Ausgezeichnet! Zahlen machen mich glücklich. 😊',
        'tschüss': 'Auf Wiedersehen! Bis zum nächsten Mal! 👋',
        'bye': 'Tschüss! Komm bald wieder! 😊',
        'hilfe': `📚 Hilfe - Was ich kann:

• Grundrechenarten: 5 + 3, 10 * 2, 100 / 4
• Potenzen: 2^8, 5 hoch 3
• Wurzeln: sqrt(64), wurzel aus 144, wurzel 17363
• Trigonometrie: sin(45°), cos(pi/4), tan(30°)
• Logarithmen: log(100), log(8, 2)
• Fakultät: 5! (= 120)
• Konstanten: pi, e
• Einheiten: 2 km in m, 5 kg in g
• Komplexe Formeln: (2+3)*4^2-sqrt(16)

Probier's einfach aus! 🚀`,
        'help': 'Gib "hilfe" ein für Informationen auf Deutsch.',
        'was kannst du': 'Ich kann mathematische Berechnungen durchführen! Gib "hilfe" ein für alle Funktionen.',
        'beispiele': 'Beispiele: "5 + 3", "wurzel aus 64", "sin(45)", "5!", "2 km in m". Gib "hilfe" für mehr!',
    };

    function preprocessInput(input) {
        let processed = input
            .replace(/^(bitte\s+|kannst\s+du\s+|könntest\s+du\s+)/gi, '')
            .replace(/was ist|berechne|rechne|wie viel ist/gi, '');
        processed = processed
            .replace(/wurzel\s+aus\s+(\d+(?:\.\d+)?)/gi, 'sqrt($1)')
            .replace(/wurzel\s+(\d+(?:\.\d+)?)/gi, 'sqrt($1)')
            .replace(/quadratwurzel\s+(?:von\s+)?(\d+(?:\.\d+)?)/gi, 'sqrt($1)')
            .replace(/fakultät\s+(?:von\s+)?(\d+)/gi, '$1!')
            .replace(/(\d+)\s+fakultät/gi, '$1!')
            .replace(/(\d+(?:\.\d+)?)\s+hoch\s+(\d+(?:\.\d+)?)/gi, '$1^$2')
            .replace(/\bhoch\b/gi, '^')
            .replace(/\bmal\b/gi, '*')
            .replace(/\bplus\b/gi, '+')
            .replace(/\bminus\b/gi, '-')
            .replace(/geteilt\s+durch/gi, '/')
            .replace(/\bdurch\b/gi, '/')
            .replace(/×/g, '*')
            .replace(/÷/g, '/')
            .replace(/π/g, 'pi')
            .replace(/sinus\s+(?:von\s+)?(\d+(?:\.\d+)?)/gi, 'sin($1 deg)')
            .replace(/cosinus\s+(?:von\s+)?(\d+(?:\.\d+)?)/gi, 'cos($1 deg)')
            .replace(/tangens\s+(?:von\s+)?(\d+(?:\.\d+)?)/gi, 'tan($1 deg)')
            .replace(/sin\s+(\d+(?:\.\d+)?)/gi, 'sin($1 deg)')
            .replace(/cos\s+(\d+(?:\.\d+)?)/gi, 'cos($1 deg)')
            .replace(/tan\s+(\d+(?:\.\d+)?)/gi, 'tan($1 deg)')
            .replace(/,(\d)/g, '.$1')
            .trim();
        processed = processed.replace(/\b(sin|cos|tan)\(\s*(\d+(?:\.\d+)?)\s*\)/gi, (_, fn, n) => `${fn}(${n} deg)`);
        return processed;
    }

    function generateResponse(input) {
        const normalizedInput = input.toLowerCase().trim();
        if (conversationalResponses[normalizedInput]) return conversationalResponses[normalizedInput];
        if (!normalizedInput) return '🤔 Du hast nichts eingegeben. Stell mir eine Mathe-Frage!';

        const expression = preprocessInput(normalizedInput);
        try {
            if (!expression) return '🤔 Ich habe deine Frage nicht verstanden. Versuch es mit einer mathematischen Formel oder gib "hilfe" ein.';
            const result = math.evaluate(expression);
            let formattedResult = math.format(result, { notation: 'auto', precision: 15 });
            const numericValue = Number.parseFloat(formattedResult);
            if (Number.isFinite(numericValue)) {
                if (Math.abs(numericValue) > 1e10 || (Math.abs(numericValue) < 1e-4 && numericValue !== 0)) {
                    formattedResult = math.format(result, { notation: 'exponential', precision: 6 });
                }
            }
            return '✅ Ergebnis: ' + formattedResult;
        } catch (error) {
            const errorMsg = String(error && error.message ? error.message : error).toLowerCase();
            if (errorMsg.includes('undefined symbol') || errorMsg.includes('unexpected')) {
                return '❌ Ich kenne diesen Befehl nicht. Gib "hilfe" ein, um zu sehen, was ich kann.';
            } else if (errorMsg.includes('parenthesis') || errorMsg.includes('bracket')) {
                return '❌ Fehler: Überprüfe deine Klammern. Sie müssen paarweise geschlossen sein.';
            } else if (errorMsg.includes('dimension') || errorMsg.includes('unit')) {
                return '❌ Fehler: Die Einheiten passen nicht zusammen.';
            } else {
                return '❌ Das konnte ich nicht berechnen. Überprüfe deine Eingabe oder gib "hilfe" ein.';
            }
        }
    }

    function sendMessage() {
        const userText = chatInput.value.trim();
        if (!userText || isProcessing) return;

        isProcessing = true;
        sendBtn.disabled = true;
        chatHistory.setAttribute('aria-busy', 'true');

        addMessage(userText, 'user');
        chatInput.value = '';

        const loadingMsg = addMessage('💭 Denke nach...', 'bot', true);

        setTimeout(() => {
            loadingMsg.remove();
            const botReply = generateResponse(userText);
            addMessage(botReply, 'bot');
            isProcessing = false;
            sendBtn.disabled = false;
            chatHistory.removeAttribute('aria-busy');
            chatInput.focus();
        }, 300 + Math.random() * 200);
    }

    sendBtn.addEventListener('click', sendMessage);
    chatInput.addEventListener('keydown', (event) => {
        if (event.key === 'Enter' && !event.shiftKey) {
            event.preventDefault();
            sendMessage();
        }
    });

    addMessage('🤖 Mathe-GPT ist bereit! Stelle mir eine Frage oder gib einen Ausdruck ein.\n\nTippe "hilfe" für Beispiele und alle Funktionen.', 'bot');
    chatInput.focus();
</script>
</body>
</html>
