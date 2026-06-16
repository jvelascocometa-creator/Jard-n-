# Jard-n-
Bot de reservas 
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chatbot Jardín de los Calima</title>
    <style>
        :root { --primary: #1b5e20; --accent: #fbc02d; --bg: #f1f8e9; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--bg); margin: 0; }
        
        #chat-container {
            width: 380px; height: 550px; background: white;
            position: fixed; bottom: 20px; right: 20px;
            border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            display: flex; flex-direction: column; overflow: hidden;
        }

        #chat-header { background: var(--primary); color: white; padding: 15px; text-align: center; font-weight: bold; }
        #chat-box { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        
        .msg { padding: 12px; border-radius: 12px; max-width: 85%; font-size: 14px; line-height: 1.4; }
        .bot { background: #e8f5e9; align-self: flex-start; color: #1b5e20; border-left: 4px solid var(--accent); }
        .user { background: var(--primary); color: white; align-self: flex-end; }

        #chat-input-area { display: flex; padding: 15px; border-top: 1px solid #eee; background: #fff; }
        input { flex: 1; padding: 10px; border: 1px solid #ddd; border-radius: 8px; outline: none; }
        button { background: var(--primary); color: white; border: none; padding: 10px 15px; margin-left: 8px; border-radius: 8px; cursor: pointer; }
    </style>
</head>
<body>

<div id="chat-container">
    <div id="chat-header">Jardín de los Calima - Reservas</div>
    <div id="chat-box" id="chatBox">
        <div class="msg bot">¡Hola! 🌿 Bienvenido a Jardín de los Calima. Para brindarte una mejor atención, ¿cuál es tu <b>nombre completo</b>?</div>
    </div>
    <div id="chat-input-area">
        <input type="text" id="userInput" placeholder="Escribe aquí..." autocomplete="off">
        <button onclick="sendMessage()">Enviar</button>
    </div>
</div>

<script>
    // --- CONFIGURACIÓN ---
    const WEB_APP_URL = "https://script.google.com/macros/s/AKfycbwzg54GCiM1HtSyC4KbrFS9pNc4UudkYp4T01PJGFVG8QbBTKyYcSTqHb3vFalTPt81/exec"; 
    
    let step = 0;
    const userData = {
        nombre: "", tipo_doc: "", num_doc: "", correo: "", telefono: "", plan: "", valor: ""
    };

    const planes = {
        "1": { nombre: "Pasadía Ecológico", precio: "50,000 COP" },
        "2": { nombre: "Camping Estelar", precio: "80,000 COP" },
        "3": { nombre: "Experiencia Completa (Hospedaje + Tour)", precio: "150,000 COP" }
    };

    const chatBox = document.getElementById("chat-box");
    const userInput = document.getElementById("userInput");

    function addMessage(text, sender) {
        const div = document.createElement("div");
        div.classList.add("msg", sender);
        div.innerHTML = text;
        chatBox.appendChild(div);
        chatBox.scrollTop = chatBox.scrollHeight;
    }

    function sendMessage() {
        const text = userInput.value.trim();
        if (text === "") return;
        addMessage(text, "user");
        userInput.value = "";
        setTimeout(() => processStep(text), 600);
    }

    function processStep(text) {
        switch(step) {
            case 0:
                userData.nombre = text;
                addMessage("Gracias. Ahora, ¿qué <b>tipo de documento</b> tienes? (C.C., T.I., CE)", "bot");
                step++; break;
            case 1:
                userData.tipo_doc = text;
                addMessage("Entendido. Por favor ingresa el <b>número de documento</b>:", "bot");
                step++; break;
            case 2:
                userData.num_doc = text;
                addMessage("¿A qué <b>correo electrónico</b> podemos enviarte la confirmación?", "bot");
                step++; break;
            case 3:
                userData.correo = text;
                addMessage("Casi terminamos. ¿Cuál es tu <b>número de teléfono</b>?", "bot");
                step++; break;
            case 4:
                userData.telefono = text;
                addMessage("¿Qué experiencia prefieres?<br>1. Pasadía Ecológico<br>2. Camping Estelar<br>3. Experiencia Completa", "bot");
                step++; break;
            case 5:
                if(planes[text]) {
                    userData.plan = planes[text].nombre;
                    userData.valor = planes[text].precio;
                    addMessage(`Has seleccionado <b>${userData.plan}</b>. El valor del plan es de <b>${userData.valor}</b>.`, "bot");
                    addMessage("Estamos guardando tu reserva...", "bot");
                    
                    // ENVÍO DE DATOS A GOOGLE SHEETS
                    fetch(WEB_APP_URL, {
                        method: "POST",
                        mode: "no-cors",
                        body: JSON.stringify(userData),
                        headers: { "Content-Type": "application/json" }
                    }).then(() => {
                        addMessage("✅ ¡Datos guardados exitosamente!", "bot");
                        addMessage("Si necesitas hablar con alguien o confirmar tu pago, contacta a <b>Jose Manuel (Director General)</b> al 📞 <b>3027234888</b>.", "bot");
                    });
                } else {
                    addMessage("Por favor, selecciona 1, 2 o 3 para elegir un plan.", "bot");
                }
                break;
        }
    }

    userInput.addEventListener("keypress", (e) => { if (e.key === "Enter") sendMessage(); });
</script>
</body>
</html>
