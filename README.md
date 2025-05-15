<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>App de Mensagens</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        /* Estilos básicos para o corpo e container principal */
        body {
            font-family: 'Inter', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-color: #f0f2f5; /* Cor de fundo suave */
            margin: 0; /* Remove margem padrão do body */
            overflow: hidden; /* Evita scroll no body */
        }
        .chat-container {
            display: flex;
            height: 90vh; /* Altura ajustada */
            max-height: 800px; /* Altura máxima */
            width: 100%;
            max-width: 1200px; /* Largura máxima aumentada */
            box-shadow: 0 8px 20px rgba(0,0,0,0.2); /* Sombra mais proeminente */
            border-radius: 16px; /* Bordas mais arredondadas */
            overflow: hidden; /* Para manter as bordas arredondadas nos filhos */
            background-color: #fff; /* Fundo branco para o container */
             /* Adiciona um gradiente sutil ou textura de fundo */
            background-image: linear-gradient(to bottom right, #ffffff, #f9f9f9);
            display: none; /* Inicialmente escondido */
        }
        /* Estilos para a barra lateral */
        .sidebar {
            width: 35%; /* Largura da barra lateral */
            max-width: 350px; /* Largura máxima da barra lateral aumentada */
            background-color: #f8f9fa; /* Cor de fundo da barra lateral */
            border-right: 1px solid #e0e0e0; /* Divisor */
            display: flex;
            flex-direction: column;
            /* Responsividade: em telas pequenas, a barra lateral pode ocupar 100% da largura */
            @media (max-width: 768px) {
                width: 100%;
                max-width: none;
                height: 100%; /* Ocupa a altura total em mobile */
                position: absolute; /* Posiciona absolutamente para esconder/mostrar */
                left: 0;
                top: 0;
                z-index: 20; /* Garante que fique por cima do modal */
                transform: translateX(-100%); /* Animação */
                transition: transform 0.3s ease-in-out;
            }
        }
        .sidebar.open {
             transform: translateX(0); /* Mostra a barra lateral em mobile */
        }
        /* Estilos para a área de chat */
        .chat-area {
            flex-grow: 1;
            display: flex;
            flex-direction: column;
            background-color: #ffffff; /* Fundo da área de chat */
             /* Responsividade: em telas pequenas, a área de chat fica escondida por padrão */
            @media (max-width: 768px) {
                width: 100%; /* Ocupa a largura total em mobile */
                display: none; /* Esconde por padrão em mobile */
            }
        }
         .chat-area.active {
             /* Mostra a área de chat em mobile quando ativa */
             @media (max-width: 768px) {
                display: flex;
             }
         }
        /* Estilo para o item de contato ativo na lista */
        .contact.active {
            background-color: #e0e7ff; /* Destaque para contato ativo */
            border-left: 4px solid #3b82f6; /* Adiciona uma barra colorida */
            padding-left: calc(1rem - 4px); /* Ajusta o padding para compensar a borda */
        }
         .contact:hover {
             background-color: #f1f1f1; /* Fundo levemente mais escuro no hover */
         }
        /* Estilos para as bolhas de mensagem */
        .message-bubble {
            padding: 10px 15px;
            border-radius: 20px; /* Bolhas de mensagem mais arredondadas */
            max-width: 70%;
            word-wrap: break-word;
            filter: drop-shadow(0 1px 0.5px rgba(0,0,0,0.1)); /* Sombra sutil */
            transition: transform 0.2s ease-in-out; /* Animação ao aparecer */
            position: relative; /* Necessário para posicionar o ícone de lixeira */
        }
        .message-bubble.sent {
            background-color: #3b82f6; /* Azul para mensagens enviadas */
            color: white;
            align-self: flex-end;
            /* Adiciona um "bico" na bolha enviada */
            border-bottom-right-radius: 2px;
        }
        .message-bubble.received {
            background-color: #e5e7eb; /* Cinza claro para mensagens recebidas */
            color: #1f2937; /* Texto escuro para contraste */
            align-self: flex-start;
            /* Adiciona um "bico" na bolha recebida */
            border-bottom-left-radius: 2px;
        }
         /* Estilo específico para bolhas de arquivo */
        .message-bubble.file {
             background-color: #d1fae5; /* Verde claro para arquivos */
             color: #065f46; /* Texto verde escuro */
             border: 1px solid #a7f3d0;
        }
         .message-bubble.file .file-info {
             display: flex;
             align-items: center;
             font-weight: 500;
         }
         .message-bubble.file .file-icon {
             margin-right: 8px;
             color: #065f46;
         }
         .message-bubble.file .file-name {
             flex-grow: 1;
             overflow: hidden;
             text-overflow: ellipsis;
             white-space: nowrap;
         }
         .message-bubble.file .file-size {
             font-size: 0.75rem;
             color: #10b981;
             margin-left: 8px;
             flex-shrink: 0;
         }
        /* Estilo para o ícone de lixeira */
        .delete-message-icon {
            position: absolute;
            bottom: 5px; /* Ajusta a posição vertical */
            cursor: pointer;
            font-size: 0.7rem; /* Tamanho menor */
            opacity: 0.6; /* Levemente transparente por padrão */
            transition: opacity 0.2s ease-in-out, color 0.2s ease-in-out;
        }
        .message-bubble.sent .delete-message-icon {
            left: -15px; /* Posiciona à esquerda da bolha enviada */
            color: rgba(255, 255, 255, 0.8); /* Cor clara para contraste no fundo azul */
        }
        .message-bubble.received .delete-message-icon {
            right: -15px; /* Posiciona à direita da bolha recebida */
            color: rgba(0, 0, 0, 0.4); /* Cor escura para contraste no fundo cinza */
        }
        .delete-message-icon:hover {
            opacity: 1.0; /* Totalmente opaco no hover */
            color: #dc2626; /* Cor vermelha no hover */
        }


        /* Estilos para a área de input de chat */
        .chat-input input {
            border-radius: 20px; /* Input mais arredondado */
            padding-left: 20px;
            padding-right: 20px;
             transition: border-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .chat-input button {
            border-radius: 50%; /* Botão de enviar circular */
             transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
        }
         .chat-input button:hover:not(:disabled) {
             transform: scale(1.05); /* Efeito sutil no hover */
         }
        /* Estilo para o campo de busca com ícone */
        .search-input-container {
            position: relative;
        }
        .search-input-container input {
            padding-left: 2.5rem; /* Espaço para o ícone */
        }
        .search-input-container .search-icon {
            position: absolute;
            left: 0.75rem;
            top: 50%;
            transform: translateY(-50%);
            color: #9ca3af; /* Cor do ícone de busca */
            pointer-events: none; /* Garante que o clique passe para o input */
        }
         /* Estilo para os botões de ícone no cabeçalho */
        .chat-header button {
             transition: color 0.2s ease-in-out;
        }
         .chat-header button:hover {
             color: #3b82f6; /* Cor azul no hover */
         }
         /* Estilo para o botão de voltar em mobile */
        .back-button {
            display: none; /* Esconde por padrão */
            @media (max-width: 768px) {
                display: block; /* Mostra em mobile */
            }
        }
        /* Scrollbar customizada */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #c5c5c5;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #a5a5a5;
        }
         /* Estilo para a mensagem de "nenhum contato encontrado" */
        #no-contacts-found {
            text-align: center;
            color: #6b7280; /* Cor cinza */
            padding: 20px;
            font-size: 0.9rem;
        }

        /* --- Estilos para o Modal de Perfil/Configurações --- */
        .settings-modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5); /* Fundo semi-transparente */
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 30; /* Acima de tudo */
            visibility: hidden; /* Escondido por padrão */
            opacity: 0; /* Transparência inicial */
            transition: opacity 0.3s ease-in-out, visibility 0.3s ease-in-out;
        }
        .settings-modal-overlay.visible {
            visibility: visible;
            opacity: 1;
        }
        .settings-modal {
            background-color: #fff;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.2);
            width: 90%;
            max-width: 500px; /* Largura máxima do modal */
            transform: translateY(-20px); /* Começa um pouco acima */
            transition: transform 0.3s ease-in-out;
        }
         .settings-modal-overlay.visible .settings-modal {
             transform: translateY(0); /* Desliza para a posição final */
         }
        .settings-modal h2 {
            font-size: 1.5rem;
            font-weight: 600;
            margin-bottom: 20px;
            color: #1f2937;
        }
        .settings-modal label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: #374151;
        }
         .settings-modal .form-group {
             margin-bottom: 20px;
         }
        .settings-modal input[type="text"],
        .settings-modal input[type="email"],
        .settings-modal input[type="tel"],
        .settings-modal select { /* Adicionado select para status */
            width: 100%;
            padding: 10px;
            border: 1px solid #d1d5db;
            border-radius: 8px;
            font-size: 1rem;
             transition: border-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
         .settings-modal input[type="text"]:focus,
         .settings-modal input[type="email"]:focus,
         .settings-modal input[type="tel"]:focus,
         .settings-modal select:focus {
             outline: none;
             border-color: #3b82f6;
             box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
         }
        .settings-modal .button-group {
            display: flex;
            justify-content: flex-end;
            gap: 10px; /* Espaço entre os botões */
            margin-top: 30px; /* Espaço acima dos botões */
        }
        .settings-modal button {
            padding: 10px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 500;
             transition: background-color 0.2s ease-in-out, opacity 0.2s ease-in-out;
        }
        .settings-modal button.save-button {
            background-color: #3b82f6;
            color: white;
        }
        .settings-modal button.save-button:hover {
            background-color: #2563eb;
        }
        .settings-modal button.cancel-button {
            background-color: #e5e7eb;
            color: #1f2937;
        }
         .settings-modal button.cancel-button:hover {
             background-color: #d1d5db;
         }

         /* --- Estilos para a Tela de Cadastro --- */
         .registration-screen {
             position: fixed;
             top: 0;
             left: 0;
             width: 100%;
             height: 100%;
             background-color: #f0f2f5; /* Mesma cor de fundo do body */
             display: flex;
             justify-content: center;
             align-items: center;
             z-index: 40; /* Acima de tudo, incluindo modais */
             visibility: hidden; /* Escondido por padrão */
             opacity: 0; /* Transparência inicial */
             transition: opacity 0.3s ease-in-out, visibility 0.3s ease-in-out;
         }
         .registration-screen.visible {
             visibility: visible;
             opacity: 1;
         }
         .registration-form {
             background-color: #fff;
             padding: 40px;
             border-radius: 12px;
             box-shadow: 0 8px 20px rgba(0,0,0,0.2);
             width: 90%;
             max-width: 400px; /* Largura máxima do formulário */
             text-align: center;
         }
         .registration-form h2 {
             font-size: 1.8rem;
             font-weight: 700;
             margin-bottom: 30px;
             color: #1f2937;
         }
         .registration-form label {
             display: block;
             margin-bottom: 8px;
             font-weight: 500;
             color: #374151;
             text-align: left;
         }
         .registration-form input[type="text"],
         .registration-form input[type="email"],
         .registration-form input[type="tel"] {
             width: 100%;
             padding: 12px;
             border: 1px solid #d1d5db;
             border-radius: 8px;
             margin-bottom: 20px;
             font-size: 1rem;
              transition: border-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
         }
          .registration-form input[type="text"]:focus,
          .registration-form input[type="email"]:focus,
          .registration-form input[type="tel"]:focus {
              outline: none;
              border-color: #3b82f6;
              box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
          }
         .registration-form button {
             width: 100%;
             padding: 12px;
             background-color: #3b82f6;
             color: white;
             border-radius: 8px;
             font-size: 1.1rem;
             font-weight: 600;
             cursor: pointer;
              transition: background-color 0.2s ease-in-out;
         }
         .registration-form button:hover {
             background-color: #2563eb;
         }
         .error-message {
             color: #dc2626; /* Cor vermelha para erro */
             font-size: 0.9rem;
             margin-top: -10px;
             margin-bottom: 15px;
             text-align: left;
             display: none; /* Escondido por padrão */
         }

         /* Estilos para o indicador de status do usuário no header da sidebar */
        .user-status-indicator {
            position: absolute;
            bottom: 0;
            right: 0;
            display: block;
            height: 10px; /* Tamanho menor */
            width: 10px; /* Tamanho menor */
            border: 2px solid white; /* Borda branca */
            border-radius: 50%; /* Redondo */
        }
        .user-status-indicator.online {
            background-color: #22c55e; /* Verde */
        }
         .user-status-indicator.offline {
            background-color: #ef4444; /* Vermelho */
         }
         .user-status-indicator.busy {
            background-color: #f97316; /* Laranja */
         }


    </style>
</head>
<body>
    <div class="registration-screen" id="registration-screen" style="display: none;">
        <div class="registration-form">
            <h2>Criar Conta</h2>
            <div>
                <label for="full-name-input">Nome Completo:</label>
                <input type="text" id="full-name-input" placeholder="Seu nome completo">
            </div>
             <div>
                <label for="nickname-input">Apelido:</label>
                <input type="text" id="nickname-input" placeholder="Como quer ser chamado">
            </div>
            <div>
                <label for="phone-input">Número de Contato:</label>
                <input type="tel" id="phone-input" placeholder="Ex: (XX) 9XXXX-XXXX">
            </div>
            <div>
                <label for="email-input">Email:</label>
                <input type="email" id="email-input" placeholder="Seu melhor email">
            </div>
             <p class="error-message" id="registration-error-message"></p>
            <button id="register-button">Cadastrar</button>
        </div>
    </div>

    <div class="chat-container" id="chat-container" style="display: none;">
        <div class="sidebar" id="sidebar">
            <div class="p-4 border-b border-gray-300 flex items-center justify-between">
                 <button class="back-button mr-3 p-1 rounded-full hover:bg-gray-200" id="back-to-contacts" title="Voltar para Contatos">
                     <i class="fas fa-arrow-left"></i>
                </button>
                 <div class="relative flex-shrink-0 mr-3">
                     <button class="p-1 rounded-full hover:bg-gray-200" id="profile-button" title="Ver Perfil">
                         <i class="fas fa-user-circle fa-lg text-gray-600"></i>
                     </button>
                     <span id="user-status-dot" class="user-status-indicator"></span>
                 </div>

                <div class="search-input-container flex-grow">
                    <i class="fas fa-search search-icon"></i>
                    <input type="text" id="search-contact" placeholder="Buscar ou iniciar conversa" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow">
                </div>
            </div>

            <div class="p-4 border-b border-gray-300 text-center">
                <button class="text-sm text-blue-600 hover:text-blue-800" id="invite-contact-button" title="Convidar Novo Usuário">
                    <i class="fas fa-user-plus mr-1"></i> Convidar Novo Usuário
                </button>
            </div>

            <div id="contact-list" class="overflow-y-auto flex-grow">
                <div id="no-contacts-found" class="hidden">Nenhum contato encontrado.</div>
            </div>
            </div>

        <div class="chat-area" id="chat-area">
            <div id="chat-header" class="p-4 border-b border-gray-300 bg-gray-50 flex items-center space-x-3">
                <button class="back-button mr-3 p-1 rounded-full hover:bg-gray-200" id="back-to-contacts-header" title="Voltar para Contatos">
                    <i class="fas fa-arrow-left"></i>
                </button>
                <div class="w-12 h-12 bg-gray-300 rounded-full flex items-center justify-center text-xl text-white">
                    <i class="fas fa-user-circle"></i>
                 </div>
                 <div>
                    <h2 id="chat-with-name" class="font-semibold text-lg text-gray-800">Selecione um contato</h2>
                    <p id="chat-with-status" class="text-xs text-gray-500">Offline</p>
                 </div>
            </div>

            <div id="message-list" class="flex-grow p-6 space-y-4 overflow-y-auto bg-gray-100">
                <div class="text-center text-gray-500 py-10" id="initial-message-placeholder">
                    <i class="fas fa-comment-dots fa-3x mb-2"></i>
                    <p>Selecione um contato para iniciar a conversa.</p>
                 </div>
            </div>

            <div class="p-4 border-t border-gray-300 bg-gray-50">
                <div class="flex items-center space-x-3 chat-input">
                    <input type="file" id="file-input" multiple class="hidden">
                    <button class="p-2 text-gray-500 hover:text-blue-500" id="attach-button" title="Anexar arquivo">
                        <i class="fas fa-paperclip fa-lg"></i>
                    </button>
                    <input type="text" id="message-input" placeholder="Digite sua mensagem..." class="flex-grow p-3 border border-gray-300 rounded-full focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow" disabled>
                    <button id="send-button" class="bg-blue-500 hover:bg-blue-600 text-white p-3 rounded-full w-12 h-12 flex items-center justify-center transition-colors disabled:opacity-50 disabled:cursor-not-allowed" disabled title="Enviar mensagem">
                        <i class="fas fa-paper-plane fa-lg"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <div class="settings-modal-overlay" id="settings-modal-overlay">
        <div class="settings-modal" id="settings-modal">
            <h2>Meu Perfil</h2>
            <div class="form-group">
                <label for="profile-full-name-input">Nome Completo:</label>
                <input type="text" id="profile-full-name-input" placeholder="Seu nome completo" disabled> </div>
             <div class="form-group">
                <label for="profile-nickname-input">Apelido:</label>
                <input type="text" id="profile-nickname-input" placeholder="Como quer ser chamado">
            </div>
            <div class="form-group">
                <label for="profile-phone-input">Número de Contato:</label>
                <input type="tel" id="profile-phone-input" placeholder="Ex: (XX) 9XXXX-XXXX" disabled> </div>
            <div class="form-group">
                <label for="profile-email-input">Email:</label>
                <input type="email" id="profile-email-input" placeholder="Seu melhor email" disabled> </div>

            <div class="form-group">
                <label for="profile-status-select">Status:</label>
                <select id="profile-status-select" class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow">
                    <option value="online">Online</option>
                    <option value="offline">Offline</option>
                    <option value="busy">Ocupado</option>
                </select>
            </div>

            <div class="button-group">
                <button class="cancel-button" id="cancel-profile-button">Fechar</button>
                <button class="save-button" id="save-profile-button">Salvar Perfil</button>
            </div>
        </div>
    </div>


    <script>
        // --- Simulação de Dados e Backend ---
        // Estes dados simulariam a resposta de um backend real
        const initialSimulatedBackendData = {
            // Dados do usuário atual (serão preenchidos no cadastro ou carregados do localStorage)
            currentUser: null, // Começa como null
            contacts: [], // Lista de contatos começa vazia
            // Lista de TODOS os usuários potenciais no sistema simulado
            allPotentialUsers: [
                 // Inclui os contatos iniciais como potenciais usuários
                 // Definindo todos como online por padrão
                { id: 1, fullName: 'Alice Silva', name: 'Alice', phone: '11987654321', email: 'alice@example.com', avatar: 'https://placehold.co/100x100/A9D1F7/4F4F4F?text=AS', online: true },
                { id: 2, fullName: 'Bruno Costa', name: 'Bruno', phone: '21987654321', email: 'bruno@example.com', avatar: 'https://placehold.co/100x100/F7C9A9/4F4F4F?text=BC', online: true },
                { id: 3, fullName: 'Carla Dias', name: 'Carla', phone: '31987654321', email: 'carla@example.com', avatar: 'https://placehold.co/100x100/A9F7C0/4F4F4F?text=CD', online: true },
                { id: 4, fullName: 'Daniel Alves', name: 'Daniel', phone: '41987654321', email: 'daniel@example.com', avatar: 'https://placehold.co/100x100/F7A9A9/4F4F4F?text=DA', online: true },
                { id: 5, fullName: 'Eduarda Lima', name: 'Eduarda', phone: '51987654321', email: 'eduarda@example.com', avatar: 'https://placehold.co/100x100/D8A9F7/4F4F4F?text=EL', online: true },
                 // Novos usuários potenciais (serão adicionados aqui após o cadastro)
            ],
             // Mensagens associadas aos IDs de usuário (simuladas)
            messages: {
                1: [
                    { senderId: 1, text: 'Oi, tudo bem?', time: '10:28', type: 'received' },
                    { senderId: 0, text: 'Tudo ótimo, e com você?', time: '10:29', type: 'sent' }, // senderId 0 representa o usuário atual
                    { senderId: 1, text: 'Tudo certo por aqui também!', time: '10:30', type: 'received' }
                ],
                2: [
                    { senderId: 2, text: 'E aí, firmeza?', time: 'Ontem 09:15', type: 'received' },
                    { senderId: 0, text: 'Opa, tudo certo!', time: 'Ontem 09:16', type: 'sent' },
                    { senderId: 2, text: 'Vamos marcar aquele café!', time: 'Ontem 09:20', type: 'received' }
                ],
                3: [
                    { senderId: 3, text: 'Reunião às 14h?', time: 'Sexta 10:00', type: 'received' },
                    { senderId: 0, text: 'Confirmado!', time: 'Sexta 10:01', type: 'sent' },
                    { senderId: 3, text: 'Ok, combinado!', time: 'Sexta 10:02', type: 'received' }
                ],
                4: [
                    { senderId: 4, text: 'Preciso da sua ajuda com um relatório.', time: '12/05 15:30', type: 'received' },
                    { senderId: 0, text: 'Claro, pode mandar.', time: '12/05 15:31', type: 'sent' },
                    { senderId: 4, text: 'Vou te enviar o arquivo.', time: '12/05 15:32', type: 'received' }
                ],
                5: [], // Chat vazio
                 // Chats para novos contatos (inicialmente vazios ou com mensagem de boas-vindas)
                // IDs 6, 7, 8, 9 são apenas exemplos iniciais, novos IDs serão gerados
            }
        };

        // Repertório expandido de frases para respostas simuladas
        const responseRepertoire = {
            greeting: [
                "Olá!", "Oi!", "E aí!", "Tudo bem?", "Como vai?", "Oi, tudo certo?", "Saudações!", "Fala!", "Opa!", "E aí, tudo joia?", "Olá, como posso ajudar?", "Oi, tudo tranquilo por aqui?", "Bom dia!", "Boa tarde!", "Boa noite!", "Que bom te ver online!", "Olá! Como estão as coisas?", "E aí! Tudo bem por aí?", "Oi! Alguma novidade?", "Saudações! Como vai o dia?"
            ],
            howAreYou: [
                "Estou bem, obrigado por perguntar!", "Tudo tranquilo por aqui.", "Indo bem.", "Estou ótimo!", "Tudo certo por aqui.", "Muito bem, e você?", "Aqui tudo joia!", "Na paz, e você?", "Estou ótimo, e você?", "Tudo nos conformes, e com você?", "Bem por aqui, obrigado!", "Estou bem, e as novidades?", "Tudo tranquilo, e com você?", "Estou ótimo, pronto para o que precisar!", "Estou bem, na correria de sempre.", "Tudo certo, meio atarefado.", "Estou bem, e como você está se sentindo?", "Aqui tudo ótimo, e com você, tudo em ordem?"
            ],
            meeting: [
                "Claro, podemos marcar.", "Que dia e hora seria bom?", "Tenho a tarde livre.", "Confirme o horário.", "Podemos agendar.", "Quando você estaria disponível?", "Vamos combinar algo.", "Que tal amanhã?", "Me diga um horário que funcione para você.", "Podemos alinhar nossas agendas.", "Que dia é melhor para você?", "Estou com alguns horários livres na semana que vem.", "Podemos nos encontrar?", "Que tal um café para discutir isso?", "Estou aberto a sugestões de horário.", "Vamos marcar para breve.", "Que dia e horário funcionam para você?", "Podemos marcar para quando for melhor.", "Estou flexível para agendar.", "Que tal combinarmos um call rápido?"
            ],
            file: [
                "Ok, estou aguardando o arquivo!", "Pode enviar o documento.", "Recebi o arquivo, obrigado!", "Certo, vou dar uma olhada no relatório.", "Assim que receber, te aviso.", "Obrigado por enviar o arquivo.", "O arquivo chegou!", "Vou analisar o documento.", "Pode me mandar o anexo.", "Recebi o anexo.", "Obrigado pelo documento.", "Vou baixar o arquivo agora.", "O arquivo foi recebido com sucesso.", "Estou abrindo o documento.", "Parece que o arquivo veio.", "Obrigado por compartilhar o arquivo.", "Confirmei o recebimento do arquivo.", "Vou dar uma olhada no documento que você enviou.", "Obrigado por me enviar o anexo.", "Arquivo recebido, valeu!"
            ],
            help: [
                "Posso ajudar!", "Me diga o que precisa.", "Estou aqui para ajudar.", "Qual a questão?", "Em que posso ser útil?", "Pode perguntar!", "Estou à disposição.", "Conta comigo!", "No que posso te dar uma força?", "Qual a sua dúvida?", "Pode me explicar melhor?", "Estou pronto para ajudar.", "Como posso te auxiliar?", "Me diga o que está acontecendo.", "Vou tentar resolver isso para você.", "Estou aqui para o que precisar.", "Pode me dar mais detalhes sobre o problema?", "Vou investigar isso para você.", "Estou à sua disposição para ajudar.", "Como posso te dar suporte?"
            ],
            thanks: [
                "De nada! 😊", "Por nada!", "Disponha!", "Que bom que pude ajudar!", "Imagina!", "Sem problemas!", "Foi um prazer ajudar!", "Qualquer coisa, é só chamar.", "Não há de quê.", "Fico feliz em ajudar!", "À vontade!", "Sempre que precisar!", "Que bom que deu certo!", "Disponha sempre!", "Foi fácil ajudar!", "Que bom que ajudei!", "Não foi nada!", "Estou aqui para isso!", "Que bom que resolvi!"
            ],
            farewell: [
                "Até logo!", "Tchau, tchau!", "Nos falamos!", "Até a próxima!", "Um abraço!", "Até mais!", "Fui!", "A gente se fala!", "Tenha um bom dia/tarde/noite!", "Até breve!", "Tchau!", "Vou nessa!", "Até mais tarde!", "Bom descanso!", "Fique bem!", "Até a próxima!", "Nos vemos por aí!", "Tchau! Se cuida!", "Até mais, bom trabalho/estudo!"
            ],
            confirmation: [
                "Ótimo!", "Perfeito!", "Combinado!", "Entendido!", "Certo!", "Concordo!", "Isso!", "Beleza!", "Fechado!", "Confirmado!", "Exato!", "Certamente.", "Sem dúvida!", "Com certeza!", "Isso aí!", "Pode apostar!", "De acordo!", "Concordo plenamente!", "É isso mesmo!", "Perfeito, vamos em frente!", "Confirmadíssimo!", "Entendido, pode seguir.", "Ok, combinado!", "Perfeito!"
            ],
            negation: [
                "Ah, entendi.", "Sem problemas.", "Ok, talvez na próxima.", "Compreendo.", "Entendido, sem fazer isso então.", "Não será possível.", "Infelizmente, não.", "Que pena.", "Não dá para fazer isso.", "Não concordo com isso.", "Acho que não é o caso.", "Não é bem assim.", "Isso não vai funcionar.", "Melhor não.", "Não consigo fazer isso agora.", "Essa opção não está disponível.", "Não é a melhor ideia.", "Acho que não."
            ],
            general: [
                "Certo.", "Ok.", "Entendi.", "Hmm.", "Interessante.", "Legal!", "Bom saber.", "Pode crer.", "Faz sentido.", "Sim, sim.", "Entendido.", "Ok, prossiga.", "Continuando...", "E então?", "O que mais?", "Pensando aqui...", "Deixa eu ver...", "Um momento...", "Estou analisando.", "Processando...", "Aguarde um pouco.", "Faz sentido o que você disse.", "Estou acompanhando.", "Pode continuar.", "Estou ouvindo.", "Anotado.", "Entendido perfeitamente.", "Acho que sim.", "Talvez.", "Pode ser.", "Vamos ver como fica.", "Estou pensando sobre isso.", "É uma boa questão.", "Preciso verificar.", "Deixe-me pensar."
            ],
            question: [
                "O que você acha?", "Como podemos fazer?", "Alguma ideia?", "E sobre...?", "Você já viu isso?", "Qual o próximo passo?", "Como está indo?", "Alguma novidade?", "O que me diz?", "Qual a sua opinião?", "Tem alguma sugestão?", "E agora?", "O que faremos?", "Alguma pista?", "Você sabe algo sobre isso?", "O que pensa a respeito?", "Qual a sua proposta?", "Como proceder agora?", "Podemos seguir com isso?", "Qual a sua dúvida?", "Você entendeu?", "Ficou claro?", "Podemos avançar?", "O que sugere?", "Como podemos resolver isso?"
            ],
            availability: [
                "Estou livre agora.", "Tenho um tempo mais tarde.", "Estou um pouco ocupado no momento.", "Me avise quando estiver pronto.", "Posso falar agora.", "Estou disponível.", "Tenho uns minutos.", "Agora dá.", "Mais tarde fica melhor.", "Estou livre para conversar.", "Posso te atender em breve.", "Me chame quando puder.", "Estou com a agenda livre.", "Tenho disponibilidade.", "Posso te encaixar aqui.", "Estou livre nos próximos minutos.", "Tenho um espaço na minha agenda.", "Posso te ligar em instantes.", "Estou disponível para uma rápida conversa."
            ],
            agreement: [
                "Concordo.", "Isso mesmo.", "Perfeito.", "Sem dúvida.", "Estamos alinhados.", "Exato!", "Pensamos igual.", "Totalmente de acordo.", "É bem por aí.", "Assino embaixo!", "Concordo plenamente.", "Você tem razão.", "Estamos na mesma página.", "Compartilho da mesma opinião.", "Sim, concordo totalmente.", "É exatamente isso.", "Estamos em sintonia.", "Não poderia concordar mais.", "Você disse tudo."
            ],
            disagreement: [
                "Não tenho certeza.", "Talvez não seja a melhor ideia.", "Precisamos pensar melhor.", "Tenho outro ponto de vista.", "Não vejo bem assim.", "Discordo um pouco.", "Não concordo totalmente.", "Podemos discutir isso.", "Tenho minhas dúvidas.", "Acho que não funciona assim.", "Vamos analisar melhor.", "Não vejo por esse lado.", "Tenho uma ressalva.", "Não estou convencido.", "Acho que há um engano.", "Não é o que eu penso.", "Tenho uma perspectiva diferente."
            ],
             emotion_positive: [
                 "Que bom!", "Fico feliz!", "Excelente!", "Maravilha!", "Que notícia ótima!", "Adorei!", "Fantástico!", "Incrível!", "Muito bom!", "Que alívio!", "Estou animado!", "Que beleza!", "Sensacional!", "Muito feliz com isso!", "Que maravilha!", "Isso é ótimo!", "Excelente notícia!", "Que alegria!"
             ],
             emotion_negative: [
                 "Que pena.", "Que chato.", "Sinto muito.", "Puxa vida.", "Que complicado.", "Que notícia ruim.", "Isso é triste.", "Lamento por isso.", "Que situação difícil.", "Fiquei chateado.", "Que azar.", "Isso é lamentável.", "Que frustrante.", "Sinto muito em ouvir isso."
             ],
             suggestion: [
                 "Que tal se fizermos...?", "Podemos tentar...", "Sugiro que...", "Minha ideia é...", "Que acha de...?", "Uma sugestão seria...", "Podemos considerar...", "Recomendo que...", "Que tal esta abordagem?", "Minha proposta é...", "Pensei em algo diferente.", "Que tal invertermos a ordem?", "Uma alternativa seria...", "Que tal explorarmos essa opção?", "Minha recomendação é...", "Que tal fazermos assim?"
             ],
             inquiry: [ // Perguntas gerais
                 "O que aconteceu?", "Como foi?", "Alguma novidade?", "E aí?", "O que me conta?", "Tudo certo por aí?", "Como estão as coisas?", "O que você tem feito?", "Alguma notícia?", "Como está o projeto?", "E a vida?", "Tudo tranquilo?", "Como foi o seu dia?", "O que você está fazendo agora?", "Algum plano para hoje?", "O que me diz sobre...?"
             ],
             acknowledgement: [ // Reconhecimento simples
                 "Ah, sim.", "Entendi.", "Ok.", "Certo.", "Compreendo.", "Saquei.", "Hmm, entendi.", "Faz sentido.", "Tá.", "Beleza.", "Entendido.", "Ok.", "Certo.", "Compreendido."
             ],
             follow_up: [ // Frases de acompanhamento ou transição
                 "E depois?", "O que aconteceu em seguida?", "Qual o próximo passo?", "Como podemos continuar?", "O que faremos agora?", "Alguma atualização?", "Conte-me mais.", "Prossiga.", "E aí?", "O que mais?", "E sobre aquilo?", "Vamos em frente.", "O que vem depois?"
             ],
             casual: [ // Frases casuais
                 "Tudo bem por aqui.", "Dia corrido.", "Pausa para um café?", "Fim de semana chegando!", "Que calor/frio!", "Nada de novo.", "Só na correria.", "Relaxando um pouco.", "Tudo na boa.", "Sem estresse.", "Mais um dia.", "Vamos que vamos.", "Tranquilo por aqui."
             ],
             intensifiers: [ // Palavras ou frases para intensificar
                 "muito", "bastante", "realmente", "totalmente", "completamente", "de verdade", "sempre", "nunca", "quase", "apenas", "só", "muito mesmo"
             ],
             connectors: [ // Conectores para ligar frases
                 "e", "mas", "porém", "contudo", "então", "assim", "portanto", "além disso", "enquanto isso", "depois", "antes", "porque", "já que", "se", "quando"
             ],
             emojis: [ // Emojis para adicionar tom
                 "😊", "👍", "😂", "🤔", "💡", "✅", "❌", "👋", "☕", "📅", "📄", "❓", "❗", "😉", "👍", "😅", "🙂"
             ],
             random: [ // Frases aleatórias para variar
                "Que interessante!", "Espero que esteja tudo bem.", "Recebi sua mensagem.", "Estou pensando sobre isso.", "Parece bom.", "Vamos ver.", "Combinado!", "Ok, pode ser.", "Entendido, obrigado!", "Estou por aqui se precisar.", "Tudo certo.", "Sem novidades por aqui.", "Como está o tempo por aí?", "Algum plano para o fim de semana?", "Notícias?", "Novidades?", "Curioso para saber mais.", "Isso me fez pensar...", "Que coincidência!", "Pequeno mundo!", "Devo admitir...", "Para ser honesto...", "Sinceramente...", "Entre nós...", "Falando nisso...", "Por falar em...", "A propósito...", "Mudando de assunto...", "Só para constar...", "É importante notar que...", "Não se esqueça de...", "Lembre-se que...", "Pode ser útil saber...", "Só um lembrete rápido...", "Tenha em mente que...", "Vale a pena mencionar...", "Algo a considerar...", "Um ponto a pensar...", "Refletindo sobre...", "Do meu ponto de vista...", "Na minha opinião...", "Acredito que...", "Parece que...", "Tenho a impressão que...", "Me parece que...", "Pelo que entendi...", "Se não me engano...", "Corrija-me se estiver errado...", "Sei lá...", "Vai saber...", "Quem diria...", "Inacreditável!", "Impressionante!", "Surpreendente!", "Esperado.", "Previsível.", "Como imaginei.", "Sem surpresas.", "Era de se esperar.", "Nada fora do comum.", "Tudo normal por aqui.", "Rotina.", "Mais um dia na labuta.", "A vida segue.", "É isso.", "Paciência.", "Vamos em frente.", "Bola pra frente.", "Segue o jogo.", "Vida que segue.", "É assim mesmo.", "Faz parte.", "Acontece.", "Quem nunca?", "Normal.", "Comum.", "Típico.", "Característico.", "Padrão.", "Regra.", "Exceção.", "Raro.", "Incomum.", "Diferente.", "Interessante.", "Curioso.", "Peculiar.", "Estranho.", "Bizarro.", "Inesperado.", "Surpreendente.", "Chocante.", "Impressionante.", "Espetacular.", "Fantástico.", "Maravilhoso.", "Incrível.", "Excelente.", "Ótimo.", "Bom.", "Regular.", "Ruim.", "Péssimo.", "Terrível.", "Horrível.", "Lamentável.", "Triste.", "Feliz.", "Alegre.", "Contente.", "Satisfeito.", "Grato.", "Aliviado.", "Preocupado.", "Ansioso.", "Nervoso.", "Calmo.", "Tranquilo.", "Relaxado.", "Cansado.", "Esgotado.", "Com sono.", "Com fome.", "Com sede.", "Com calor.", "Com frio.", "Com pressa.", "Sem pressa.", "Com tempo.", "Sem tempo.", "Com dinheiro.", "Sem dinheiro.", "Com sorte.", "Sem sorte.", "Com razão.", "Sem razão.", "Certo.", "Errado.", "Verdadeiro.", "Falso.", "Possível.", "Impossível.", "Provável.", "Improvável.", "Necessário.", "Desnecessário.", "Importante.", "Irrelevante.", "Urgente.", "Não urgente.", "Fácil.", "Difícil.", "Simples.", "Complicado.", "Rápido.", "Lento.", "Perto.", "Longe.", "Aqui.", "Lá.", "Agora.", "Depois.", "Antes.", "Durante.", "Enquanto.", "Quando.", "Onde.", "Como.", "Por que.", "Para que.", "Quem.", "O que.", "Qual.", "Quanto.", "Quantos.", "Quais.", "Como assim?", "Por quê?", "Para quê?", "Quem é?", "O que é isso?", "Qual é?", "Quanto custa?", "Quantos são?", "Quais são?", "Como está?", "Por onde?", "Para onde?", "De onde?", "Até quando?", "Desde quando?", "Em que momento?", "De que forma?", "Com quem?", "Para quem?", "De quem?", "Sobre o que?", "A respeito de que?", "Em relação a que?", "No que diz respeito a?", "Falando sobre...", "Discutindo...", "Conversando a respeito de...", "Trocando ideias sobre...", "Pensando em...", "Refletindo sobre...", "Considerando...", "Analisando...", "Observando...", "Notando...", "Percebendo...", "Descobrindo...", "Aprendendo...", "Ensinando...", "Compartilhando...", "Trocando...", "Dando...", "Recebendo...", "Oferecendo...", "Aceitando...", "Recusando...", "Pedindo...", "Agradecendo...", "Desculpando-se...", "Cumprimentando...", "Despedindo-se...", "Apresentando...", "Introduzindo...", "Recomendando...", "Sugerindo...", "Aconselhando...", "Alertando...", "Avisando...", "Informando...", "Esclarecendo...", "Explicando...", "Detalhando...", "Resumindo...", "Concluindo...", "Iniciando...", "Terminando...", "Continuando...", "Parando...", "Começando...", "Finalizando...", "Prosseguindo...", "Interrompendo...", "Acelerando...", "Desacelerando...", "Avançando...", "Retornando...", "Indo...", "Vindo...", "Chegando...", "Saindo...", "Entrando...", "Voltando...", "Partindo...", "Ficando...", "Permanecendo...", "Esperando...", "Aguardando...", "Procurando...", "Encontrando...", "Perdendo...", "Achando...", "Olhando...", "Vendo...", "Ouvindo...", "Sentindo...", "Tocando...", "Cheirando...", "Degustando...", "Experimentando...", "Testando...", "Provando...", "Tentando...", "Conseguindo.", "Falhando.", "Vencendo.", "Perdendo.", "Ganhando.", "Gastando.", "Economizando.", "Investindo.", "Vendendo.", "Comprando.", "Alugando.", "Construindo.", "Reformando.", "Decorando.", "Limpando.", "Organizando.", "Bagunçando.", "Quebrando.", "Consertando.", "Criando.", "Destruindo.", "Fazendo.", "Desfazendo.", "Ligando.", "Desligando.", "Abrindo.", "Fechando.", "Empurrando.", "Puxando.", "Levantando.", "Sentando.", "Deitando.", "Dormindo.", "Acordando.", "Sonhando.", "Pensando.", "Refletindo.", "Meditando.", "Concentrando-se.", "Distraindo-se.", "Aprendendo.", "Estudando.", "Trabalhando.", "Descansando.", "Viajando.", "Explorando.", "Descobrindo.", "Aventurando-se.", "Arriscando-se.", "Protegendo-se.", "Defendendo-se.", "Atacando.", "Resistindo.", "Cedendo.", "Insistindo.", "Desistindo.", "Persistindo.", "Começando de novo.", "Recomeçando.", "Inovando.", "Copiando.", "Imitando.", "Criando algo novo.", "Melhorando.", "Piorando.", "Mudando.", "Mantendo.", "Transformando.", "Estacionando.", "Seguindo em frente.", "Voltando atrás.", "Acelerando o passo.", "Diminuindo o ritmo.", "Parando para pensar.", "Continuando a jornada.", "Chegando ao destino.", "Partindo para outro lugar.", "Ficando por aqui.", "Indo embora.", "Vindo para cá.", "Chegando agora.", "Saindo já.", "Entrando agora.", "Voltando depois.", "Partindo amanhã.", "Ficando mais um pouco.", "Esperando um pouco mais.", "Aguardando o momento certo.", "Procurando uma solução.", "Encontrando a resposta.", "Perdendo a paciência.", "Achando uma saída.", "Olhando ao redor.", "Vendo o que acontece.", "Ouvindo com atenção.", "Sentindo a emoção.", "Tocando a realidade.", "Cheirando o ar.", "Degustando a vida.", "Experimentando coisas novas.", "Testando os limites.", "Provando a coragem.", "Tentando ser feliz.", "Conseguindo ser você mesmo.", "Falhando em ser perfeito.", "Vencendo seus medos.", "Perdendo a vergonha.", "Ganhando confiança.", "Gastando tempo com quem importa.", "Economizando palavras.", "Investindo em si mesmo.", "Vendendo sonhos.", "Comprando ideias.", "Alugando um sorriso.", "Construindo pontes.", "Reformando conceitos.", "Decorando a alma.", "Limpando as mágoas.", "Organizando a vida.", "Bagunçando a rotina.", "Quebrando paradigmas.", "Consertando corações.", "Criando laços.", "Destruindo muros.", "Fazendo a diferença.", "Desfazendo mal-entendidos.", "Ligando pessoas.", "Desligando problemas.", "Abrindo caminhos.", "Fechando portas.", "Empurrando limites.", "Puxando oportunidades.", "Levantando a cabeça.", "Sentando para aprender.", "Deitando para sonhar.", "Dormindo em paz.", "Acordando para a vida.", "Sonhando acordado.", "Pensando alto.", "Refletindo profundamente.", "Meditando sobre o universo.", "Concentrando-se no presente.", "Distraindo-se com a beleza.", "Aprendendo com os erros.", "Estudando o sucesso.", "Trabalhando com paixão.", "Descansando a mente.", "Viajando na imaginação.", "Explorando o desconhecido.", "Descobrindo novos horizontes.", "Aventurando-se na aventura.", "Arriscando a sorte.", "Protegendo quem ama.", "Defendendo seus ideais.", "Atacando a ignorância.", "Resistindo à pressão.", "Cedendo à razão.", "Insistindo na bondade.", "Desistindo da maldade.", "Persistindo no bem."
             ]
        };


        // Variável que armazena o estado atual do backend simulado (será carregada ou usará dados iniciais)
        let simulatedBackend = {};

        // Chaves para o localStorage
        const LOCAL_STORAGE_CONTACTS_KEY = 'messagingAppSimContacts'; // Chave para contatos
        const LOCAL_STORAGE_USER_KEY = 'messagingAppCurrentUser'; // Chave para o usuário atual
        const LOCAL_STORAGE_ALL_USERS_KEY = 'messagingAppAllUsers'; // Chave para todos os usuários


        // Variável temporária para armazenar o ID do usuário que convidou (ao abrir o link de convite)
        let invitingUserIdOnLoad = null;


        // --- Funções de Persistência (localStorage) ---

        // Salva o estado atual no localStorage
        function saveStateToLocalStorage() {
            try {
                console.log("Tentando salvar estado no localStorage...");
                // Salva contatos e o usuário atual
                localStorage.setItem(LOCAL_STORAGE_CONTACTS_KEY, JSON.stringify(simulatedBackend.contacts));
                 if (simulatedBackend.currentUser) {
                     localStorage.setItem(LOCAL_STORAGE_USER_KEY, JSON.stringify(simulatedBackend.currentUser));
                     console.log("Usuário atual salvo no localStorage.");
                 } else {
                     localStorage.removeItem(LOCAL_STORAGE_USER_KEY);
                     console.log("Usuário atual removido do localStorage.");
                 }

                 // Salva a lista completa de usuários
                 localStorage.setItem(LOCAL_STORAGE_ALL_USERS_KEY, JSON.stringify(simulatedBackend.allPotentialUsers));
                 console.log("Lista completa de usuários salva no localStorage.");


            } catch (e) {
                console.error("Erro ao salvar estado no localStorage:", e);
            }
        }

        // Carrega o estado do localStorage na inicialização
        function loadStateFromLocalStorage() {
            try {
                console.log("Tentando carregar estado do localStorage...");
                const savedContacts = localStorage.getItem(LOCAL_STORAGE_CONTACTS_KEY);
                const savedUser = localStorage.getItem(LOCAL_STORAGE_USER_KEY);
                 const savedAllUsers = localStorage.getItem(LOCAL_STORAGE_ALL_USERS_KEY);

                 console.log("savedContacts:", savedContacts ? "Encontrado" : "Não encontrado");
                 console.log("savedUser:", savedUser ? "Encontrado" : "Não encontrado");
                 console.log("savedAllUsers:", savedAllUsers ? "Encontrado" : "Não encontrado");


                // Carrega dados iniciais como base
                simulatedBackend = JSON.parse(JSON.stringify(initialSimulatedBackendData)); // Cria uma cópia profunda
                console.log("Dados iniciais carregados como base.");


                 // Tenta carregar a lista completa de usuários do localStorage
                 if (savedAllUsers) {
                     simulatedBackend.allPotentialUsers = JSON.parse(savedAllUsers);
                     console.log("Lista de todos os usuários carregada do localStorage.");
                 } else {
                     console.log("Nenhuma lista de todos os usuários salva encontrada. Usando dados iniciais.");
                     // Se não encontrar, usa a lista inicial (que já está em simulatedBackend)
                 }


                if (savedContacts) {
                    const parsedContacts = JSON.parse(savedContacts);
                    // Usa os contatos salvos
                    simulatedBackend.contacts = parsedContacts;
                    console.log("Contatos carregados do localStorage.");
                } else {
                    console.log("Nenhum contato salvo encontrado.");
                     // Se não houver contatos salvos, a lista inicial já é vazia
                }

                 if (savedUser) {
                     simulatedBackend.currentUser = JSON.parse(savedUser);
                     console.log("Usuário carregado do localStorage.");
                 } else {
                     console.log("Nenhum usuário salvo encontrado.");
                 }

                 // Mensagens sempre vêm dos dados iniciais nesta simulação (não persistem)
                 // Em um app real, as mensagens seriam carregadas do backend para o currentUser
                 simulatedBackend.messages = initialSimulatedBackendData.messages;
                 console.log("Mensagens iniciais carregadas.");


            } catch (e) {
                console.error("Erro ao carregar estado do localStorage:", e);
                 // Em caso de erro fatal ao carregar, usa os dados iniciais como fallback
                 simulatedBackend = JSON.parse(JSON.stringify(initialSimulatedBackendData)); // Cria uma cópia profunda
                 console.log("Usando dados iniciais devido a erro de carregamento.");
                 // Garante que a tela de cadastro seja mostrada em caso de erro de carregamento
                 showRegistrationScreen();
                 registrationErrorMessageEl.textContent = "Ocorreu um erro inesperado ao carregar os dados. Por favor, tente novamente ou limpe o armazenamento local.";
                 registrationErrorMessageEl.style.display = 'block';
                 return; // Sai da função para não continuar a inicialização com dados inconsistentes
            }
             // Garante que allContacts reflita simulatedBackend.contacts após carregar
             allContacts = [...simulatedBackend.contacts];
             console.log("Variável local allContacts sincronizada com simulatedBackend.contacts.");
        }


        // --- Funções de Simulação de Backend (usando dados em memória) ---

        // Simula a busca de contatos do backend (usando dados em memória)
        function fetchContacts() {
            return new Promise(resolve => {
                setTimeout(() => {
                    // Retorna uma cópia dos contatos atuais do estado simulado
                    resolve(simulatedBackend.contacts.map(contact => ({ ...contact })));
                }, 50); // Atraso menor após implementar localStorage
            });
        }

        // Simula a busca de mensagens para um contato específico (usando dados em memória)
        function fetchMessages(contactId) {
             return new Promise(resolve => {
                setTimeout(() => {
                     // Retorna uma cópia das mensagens para o contactId, ou um array vazio se não houver
                    resolve((simulatedBackend.messages[contactId] || []).map(msg => ({ ...msg })));
                }, 50); // Atraso menor
            });
        }

        // Simula o envio de uma mensagem para o backend e o recebimento de uma resposta
        function sendMessageToBackend(contactId, text) {
             return new Promise(resolve => {
                setTimeout(() => {
                    const newMessage = {
                        senderId: simulatedBackend.currentUser.id, // Usa o ID do usuário atual
                        text: text,
                        time: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
                        type: 'sent'
                    };
                     // Adiciona a mensagem enviada aos dados simulados (opcional, para persistir na simulação)
                    if (!simulatedBackend.messages[contactId]) {
                        simulatedBackend.messages[contactId] = [];
                    }
                    simulatedBackend.messages[contactId].push(newMessage);

                    // Simula uma resposta do contato
                    const contact = allContacts.find(c => c.id === currentChatId); // Busca na lista atual de contatos
                    let replyMessage = null;
                    // Verifica se o contato está online antes de simular a resposta
                    if (contact && contact.online) {
                        // --- Lógica para gerar resposta contextual (SIMULADA) ---
                        const lowerText = text.toLowerCase();
                        let selectedPhrases = [];
                        let mainTopicFound = false; // Flag para verificar se um tópico principal foi abordado

                        // Função auxiliar para adicionar uma frase de uma categoria com chance
                        const addPhrase = (category, chance = 1.0) => {
                            if (Math.random() < chance && responseRepertoire[category] && responseRepertoire[category].length > 0) {
                                const phrase = responseRepertoire[category][Math.floor(Math.random() * responseRepertoire[category].length)];
                                if (!selectedPhrases.includes(phrase)) { // Evita frases duplicadas na mesma resposta
                                    selectedPhrases.push(phrase);
                                    return true; // Retorna true se adicionou
                                }
                            }
                            return false; // Retorna false se não adicionou
                        };

                        // Lógica de seleção e combinação de frases com base em palavras-chave
                        // Prioriza tópicos principais
                        if (lowerText.includes('marcar') || lowerText.includes('reunião') || lowerText.includes('café') || lowerText.includes('encontrar')) {
                            addPhrase('meeting', 1.0);
                            addPhrase('availability', 0.7);
                            addPhrase('question', 0.6);
                            mainTopicFound = true;
                        }

                        if (lowerText.includes('arquivo') || lowerText.includes('documento') || lowerText.includes('relatório') || lowerText.includes('anexo')) {
                             addPhrase('file', 1.0);
                             addPhrase('confirmation', 0.6);
                             mainTopicFound = true;
                        }

                        if (lowerText.includes('ajuda') || lowerText.includes('dúvida') || lowerText.includes('questão') || lowerText.includes('problema')) {
                             addPhrase('help', 1.0);
                             addPhrase('question', 0.8);
                             mainTopicFound = true;
                        }

                         if (lowerText.includes('sugere') || lowerText.includes('proposta') || lowerText.includes('ideia')) {
                             addPhrase('suggestion', 1.0);
                             addPhrase('question', 0.5);
                             mainTopicFound = true;
                         }


                         // Lógica para respostas mais gerais e combinações secundárias
                         if (lowerText.includes('olá') || lowerText.includes('oi') || lowerText.includes('tudo bem') || lowerText.includes('como você está')) {
                             addPhrase('greeting', mainTopicFound ? 0.3 : 1.0); // Alta chance se não for tópico principal
                             addPhrase('howAreYou', mainTopicFound ? 0.4 : 0.8); // Alta chance se não for tópico principal
                             if (!mainTopicFound) addPhrase('inquiry', 0.4); // Adiciona pergunta se for apenas saudação
                         }

                        if (lowerText.includes('obrigado') || lowerText.includes('valeu') || lowerText.includes('agradeço')) {
                             addPhrase('thanks', 1.0);
                             addPhrase('farewell', 0.4);
                        }

                        if (lowerText.includes('até mais') || lowerText.includes('tchau') || lowerText.includes('falou') || lowerText.includes('abraço')) {
                             addPhrase('farewell', 1.0);
                             addPhrase('thanks', 0.3);
                        }

                        if (lowerText.includes('sim') || lowerText.includes('ok') || lowerText.includes('certo') || lowerText.includes('beleza')) {
                             addPhrase('confirmation', 1.0);
                             addPhrase('general', 0.5);
                             addPhrase('acknowledgement', 0.4);
                        }

                        if (lowerText.includes('não')) {
                             addPhrase('negation', 1.0);
                             addPhrase('general', 0.4);
                             addPhrase('acknowledgement', 0.5);
                        }

                         if (lowerText.includes('feliz') || lowerText.includes('ótimo') || lowerText.includes('bom')) {
                             addPhrase('emotion_positive', 1.0);
                             addPhrase('general', 0.4);
                         }

                         if (lowerText.includes('triste') || lowerText.includes('chateado') || lowerText.includes('ruim')) {
                             addPhrase('emotion_negative', 1.0);
                             addPhrase('general', 0.4);
                         }

                         if (lowerText.includes('o que você faz')) {
                             replyText = `Sou um contato simulado neste aplicativo. 😊 Posso responder a algumas frases comuns e simular interações básicas!`;
                             selectedPhrases = [replyText]; // Substitui as frases selecionadas
                         }


                        // Se nenhuma frase foi adicionada por palavras-chave específicas, adicione frases gerais ou aleatórias
                        if (selectedPhrases.length === 0) {
                             if (lowerText.length > 10 && Math.random() < 0.8) { // Maior chance de resposta geral para mensagens mais longas
                                 addPhrase('general', 1.0);
                                 if (Math.random() < 0.5) { // Chance de adicionar uma pergunta
                                      addPhrase('question', 1.0);
                                 }
                             } else { // Para mensagens curtas ou sem match, use uma confirmação, reconhecimento ou aleatória
                                 const fallbackOptions = [...responseRepertoire.confirmation, ...responseRepertoire.acknowledgement, ...responseRepertoire.random];
                                 // Adiciona 1 a 3 frases de fallback para aumentar as combinações
                                 addPhrase(fallbackOptions[Math.floor(Math.random() * fallbackOptions.length)], 1.0);
                                 addPhrase(fallbackOptions[Math.floor(Math.random() * fallbackOptions.length)], 0.7); // Segunda frase com 70% de chance
                                 addPhrase(fallbackOptions[Math.floor(Math.random() * fallbackOptions.length)], 0.4); // Terceira frase com 40% de chance
                             }
                        }

                        // Adiciona frases de acompanhamento ou casuais com baixa chance
                        addPhrase('follow_up', 0.2);
                        addPhrase('casual', 0.3);
                        addPhrase('emojis', 0.5); // Adiciona um emoji com 50% de chance


                        // Junta as frases selecionadas para formar a resposta final
                         // Embaralha as frases para variar a ordem
                         selectedPhrases.sort(() => Math.random() - 0.5);
                         replyText = selectedPhrases.join(' ').replace(/\s+/g, ' ').trim(); // Junta, remove múltiplos espaços e espaços no início/fim

                         // Adiciona pontuação final se a última frase não tiver
                         if (replyText.length > 0 && !/[.!?]$/.test(replyText)) {
                             replyText += '.';
                         }


                        // --- Fim da Lógica de Resposta ---

                         replyMessage = {
                            senderId: contactId,
                            text: replyText,
                            time: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
                            type: 'received'
                        };
                        // Adiciona a resposta aos dados simulados
                        if (!simulatedBackend.messages[contactId]) {
                             simulatedBackend.messages[contactId] = [];
                        }
                        simulatedBackend.messages[contactId].push(replyMessage);
                    } else {
                         // Se o contato estiver offline, simula uma mensagem de erro ou ausência
                         replyMessage = {
                             senderId: contactId,
                             text: `(Este contato está offline e não pode responder agora.)`,
                             time: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
                             type: 'received' // Ainda é uma mensagem "recebida" do ponto de vista do chat
                         };
                          if (!simulatedBackend.messages[contactId]) {
                             simulatedBackend.messages[contactId] = [];
                          }
                         simulatedBackend.messages[contactId].push(replyMessage);
                         console.log(`Contato ${contactId} está offline. Resposta simulada de offline.`);
                    }


                    // Resolve com a mensagem enviada e a resposta simulada (se houver)
                    resolve({ sent: newMessage, received: replyMessage });

                }, 200 + Math.random() * 500); // Simula um atraso para envio e resposta (levemente maior)
            });
        }

        // --- Elementos do DOM ---
        const registrationScreenEl = document.getElementById('registration-screen');
        const chatContainerEl = document.getElementById('chat-container');
        const fullNameInputEl = document.getElementById('full-name-input');
        const nicknameInputEl = document.getElementById('nickname-input');
        const phoneInputEl = document.getElementById('phone-input');
        const emailInputEl = document.getElementById('email-input');
        const registerButtonEl = document.getElementById('register-button');
        const registrationErrorMessageEl = document.getElementById('registration-error-message');


        const sidebarEl = document.getElementById('sidebar');
        const chatAreaEl = document.getElementById('chat-area');
        const contactListEl = document.getElementById('contact-list');
        const messageListEl = document.getElementById('message-list');
        const chatHeaderEl = document.getElementById('chat-header');
        const messageInputEl = document.getElementById('message-input');
        const sendButtonEl = document.getElementById('send-button');
        const searchContactInputEl = document.getElementById('search-contact');
        const initialMessagePlaceholderEl = document.getElementById('initial-message-placeholder');
        const noContactsFoundEl = document.getElementById('no-contacts-found');
        const backButtonEl = document.getElementById('back-to-contacts');


        // Elementos do Modal de Perfil/Configurações
        const profileButtonEl = document.getElementById('profile-button'); // Novo botão de perfil
        const userStatusDotEl = document.getElementById('user-status-dot'); // Indicador de status do usuário
        const settingsModalOverlayEl = document.getElementById('settings-modal-overlay'); // Reutilizado para Perfil
        const settingsModalEl = document.getElementById('settings-modal'); // Reutilizado para Perfil
        const profileFullNameInputEl = document.getElementById('profile-full-name-input'); // Campo nome completo no perfil
        const profileNicknameInputEl = document.getElementById('profile-nickname-input'); // Campo apelido no perfil
        const profilePhoneInputEl = document.getElementById('profile-phone-input'); // Campo telefone no perfil
        const profileEmailInputEl = document.getElementById('profile-email-input'); // Campo email no perfil
        const profileStatusSelectEl = document.getElementById('profile-status-select'); // Select de status no perfil
        const saveProfileButtonEl = document.getElementById('save-profile-button'); // Botão salvar no perfil
        const cancelProfileButtonEl = document.getElementById('cancel-profile-button'); // Botão cancelar no modal de perfil

        // Elementos do Convite e Anexo
        const inviteContactButtonEl = document.getElementById('invite-contact-button');
        const attachButtonEl = document.getElementById('attach-button'); // Botão de anexar
        const fileInputEl = document.getElementById('file-input'); // Input de arquivo oculto


        // --- Variáveis de Estado ---
        let allContacts = []; // Lista completa de contatos (do backend simulado)
        let currentChatId = null; // ID do chat atualmente aberto
        let currentChatMessages = []; // Mensagens do chat atual

        // --- Funções de Persistência (localStorage) --- (Já atualizadas acima)

        // --- Funções de Simulação de Backend (usando dados em memória) --- (Já atualizadas acima)

        // --- Funções de Renderização ---

        // Renderiza a lista de contatos na barra lateral
        function renderContacts(contactsToRender) {
            contactListEl.innerHTML = ''; // Limpa a lista atual
            if (contactsToRender.length === 0) {
                 noContactsFoundEl.classList.remove('hidden'); // Mostra mensagem de nenhum contato
                 return;
            } else {
                 noContactsFoundEl.classList.add('hidden'); // Esconde mensagem
            }

            // Ordena os contatos pela última mensagem (simulado)
            contactsToRender.sort((a, b) => {
                 // Implementação de ordenação simplificada baseada no timestamp string
                 // Em um app real, usariamos objetos Date ou timestamps numéricos
                 const timeA = new Date(a.timestamp).getTime() || 0; // Tenta criar Data, fallback para 0
                 const timeB = new Date(b.timestamp).getTime() || 0;
                 return timeB - timeA; // Mais recente primeiro
            });


            contactsToRender.forEach(contact => {
                const contactEl = document.createElement('div');
                // Adiciona classes Tailwind e a classe 'active' se for o chat atual
                contactEl.className = `contact p-4 flex items-center space-x-3 hover:bg-gray-200 cursor-pointer border-b border-gray-200 transition-colors ${contact.id === currentChatId ? 'active bg-blue-100' : ''}`;
                contactEl.setAttribute('data-contact-id', contact.id); // Armazena o ID no elemento

                contactEl.innerHTML = `
                    <div class="relative flex-shrink-0">
                        <img src="${contact.avatar}" alt="${contact.name}" class="w-12 h-12 rounded-full object-cover" onerror="this.onerror=null;this.src='https://placehold.co/100x100/cccccc/ffffff?text=?';">
                        ${contact.online ? '<span class="absolute bottom-0 right-0 block h-3 w-3 bg-green-500 border-2 border-white rounded-full"></span>' : ''}
                    </div>
                    <div class="flex-grow overflow-hidden">
                        <h3 class="font-semibold text-gray-800 truncate">${contact.name}</h3>
                        <p class="text-sm text-gray-500 truncate">${contact.lastMessage || ''}</p> </div>
                    <div class="text-right text-xs text-gray-400 flex-shrink-0">
                        <p>${contact.timestamp || ''}</p> ${contact.unread > 0 ? `<span class="mt-1 inline-block bg-blue-500 text-white text-xs px-2 py-0.5 rounded-full">${contact.unread}</span>` : ''}
                    </div>
                `;
                // Adiciona o listener para selecionar o chat
                contactEl.addEventListener('click', () => selectChat(contact.id));
                contactListEl.appendChild(contactEl);
            });
        }

        // Renderiza as mensagens na área de chat
        function renderMessages(messagesToRender) {
            messageListEl.innerHTML = ''; // Limpa a lista atual

            if (messagesToRender.length === 0) {
                 // Mostra a mensagem de chat vazio se não houver mensagens
                 messageListEl.innerHTML = `
                    <div class="text-center text-gray-500 py-10">
                        <i class="fas fa-comment-dots fa-3x mb-2"></i>
                        <p>Nenhuma mensagem ainda. Envie uma para começar!</p>
                    </div>`;
                 return;
            }

            // Esconde o placeholder inicial se houver mensagens
            initialMessagePlaceholderEl.classList.add('hidden');

            messagesToRender.forEach((msg, index) => { // Adiciona index para identificar a mensagem
                const messageDiv = document.createElement('div');
                 // Determina o alinhamento e a cor da bolha com base no tipo (sent/received)
                messageDiv.className = `flex ${msg.type === 'sent' ? 'justify-end' : 'justify-start'} mb-2`;

                let messageContent = '';

                // Verifica se a mensagem é um arquivo
                if (msg.file) {
                     const file = msg.file;
                     let fileIconClass = 'fas fa-file'; // Ícone genérico
                     if (file.type.startsWith('image/')) {
                         fileIconClass = 'fas fa-image';
                     } else if (file.type.startsWith('video/')) {
                         fileIconClass = 'fas fa-video';
                     } else if (file.type === 'application/pdf') {
                         fileIconClass = 'fas fa-file-pdf';
                     } else if (file.type === 'application/vnd.openxmlformats-officedocument.wordprocessingml.document' || file.type === 'application/msword') {
                         fileIconClass = 'fas fa-file-word';
                     } else if (file.type === 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' || file.type === 'application/vnd.ms-excel') {
                         fileIconClass = 'fas fa-file-excel';
                     } else if (file.type === 'application/vnd.openxmlformats-officedocument.presentationml.presentation' || file.type === 'application/vnd.ms-powerpoint') {
                         fileIconClass = 'fas fa-file-powerpoint';
                     }


                     // Função para formatar o tamanho do arquivo
                     const formatBytes = (bytes, decimals = 2) => {
                        if (bytes === 0) return '0 Bytes';
                        const k = 1024;
                        const dm = decimals < 0 ? 0 : decimals;
                        const sizes = ['Bytes', 'KB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'];
                        const i = Math.floor(Math.log(bytes) / Math.log(k));
                        return parseFloat((bytes / Math.pow(k, i)).toFixed(dm)) + ' ' + sizes[i];
                     };


                    messageContent = `
                        <div class="message-bubble file ${msg.type}" data-message-index="${index}">
                            <div class="file-info">
                                <i class="${fileIconClass} file-icon"></i>
                                <span class="file-name">${file.name}</span>
                            </div>
                            <p class="file-size">${formatBytes(file.size)}</p>
                             ${msg.type === 'sent' ? '<i class="fas fa-trash-alt delete-message-icon"></i>' : ''}
                        </div>
                    `;
                     // Adiciona a classe 'sent' ou 'received' na div externa para alinhamento
                    messageDiv.className += ` ${msg.type}`;

                } else {
                    // Mensagem de texto normal
                    messageContent = `
                        <div class="message-bubble ${msg.type}" data-message-index="${index}">
                            <p class="text-sm">${msg.text}</p>
                            <p class="text-xs ${msg.type === 'sent' ? 'text-blue-200' : 'text-gray-400'} mt-1 text-right">${msg.time}</p>
                             ${msg.type === 'sent' ? '<i class="fas fa-trash-alt delete-message-icon"></i>' : ''}
                        </div>
                    `;
                }


                messageDiv.innerHTML = messageContent;
                messageListEl.appendChild(messageDiv);
            });

            // Adiciona listeners para os ícones de lixeira APÓS renderizar as mensagens
            messageListEl.querySelectorAll('.delete-message-icon').forEach(icon => {
                icon.addEventListener('click', handleDeleteMessage);
            });

            scrollToBottom(); // Rola para a última mensagem
        }

        // Função para lidar com a exclusão de mensagens
        function handleDeleteMessage(event) {
            console.log("Ícone de lixeira clicado.");
            const icon = event.target;
            // Encontra a bolha de mensagem pai
            const messageBubble = icon.closest('.message-bubble');
            if (!messageBubble) {
                console.error("Não foi possível encontrar a bolha de mensagem pai.");
                return;
            }

            // Obtém o índice da mensagem a partir do data attribute
            const messageIndex = parseInt(messageBubble.getAttribute('data-message-index'), 10);
            console.log(`Tentando deletar mensagem no índice: ${messageIndex} para o chat ID: ${currentChatId}`);


            if (currentChatId !== null && !isNaN(messageIndex) && messageIndex >= 0 && messageIndex < currentChatMessages.length) {
                 // Remove a mensagem do array local
                 const deletedMessage = currentChatMessages.splice(messageIndex, 1)[0];
                 console.log("Mensagem deletada do array local:", deletedMessage);

                 // Atualiza o array no simulatedBackend
                 if (simulatedBackend.messages[currentChatId]) {
                     simulatedBackend.messages[currentChatId].splice(messageIndex, 1);
                     console.log("Mensagem deletada do simulatedBackend.");
                 }

                 // Re-renderiza as mensagens para refletir a exclusão
                 renderMessages(currentChatMessages);

                 // Opcional: Atualizar a última mensagem na lista de contatos se a mensagem deletada for a última
                 const contact = allContacts.find(c => c.id === currentChatId);
                 if (contact) {
                     const lastMsg = currentChatMessages[currentChatMessages.length - 1];
                     contact.lastMessage = lastMsg ? (lastMsg.file ? `Arquivo: ${lastMsg.file.name}` : lastMsg.text) : ''; // Atualiza para a nova última mensagem ou vazio
                     contact.timestamp = lastMsg ? lastMsg.time : ''; // Atualiza timestamp ou vazio
                     filterAndRenderContacts(); // Re-renderiza a lista de contatos
                 }

                 // Em um app real, você enviaria uma requisição para o backend deletar a mensagem.
                 // saveStateToLocalStorage(); // Salva o estado (contatos e usuários), mensagens não persistem nesta simulação.

            } else {
                console.error(`Erro ao deletar mensagem: Índice inválido (${messageIndex}) ou chat não selecionado (${currentChatId}).`);
            }
        }


        // Atualiza o cabeçalho da área de chat com as informações do contato
        function updateChatHeader(contact) {
             chatHeaderEl.innerHTML = `
                <div class="flex items-center space-x-3 flex-grow">
                     <button class="back-button mr-3 p-1 rounded-full hover:bg-gray-200" id="back-to-contacts-header" title="Voltar para Contatos">
                        <i class="fas fa-arrow-left"></i>
                    </button>
                    <div class="relative flex-shrink-0">
                        <img src="${contact.avatar}" alt="${contact.name}" class="w-12 h-12 rounded-full object-cover" onerror="this.onerror=null;this.src='https://placehold.co/100x100/cccccc/ffffff?text=?';">
                        ${contact.online ? '<span class="absolute bottom-0 right-0 block h-3 w-3 bg-green-500 border-2 border-white rounded-full"></span>' : ''}
                    </div>
                    <div>
                        <h2 id="chat-with-name" class="font-semibold text-lg text-gray-800">${contact.name}</h2>
                        <p id="chat-with-status" class="text-xs ${contact.online ? 'text-green-600' : 'text-gray-500'}">${contact.online ? 'Online' : 'Offline'}</p>
                    </div>
                </div>
                <div class="flex items-center space-x-2 flex-shrink-0">
                    <button id="generate-link-btn" class="p-2 text-gray-500 hover:text-blue-500" title="Gerar link para esta conversa">
                        <i class="fas fa-link"></i>
                    </button>
                    <button class="p-2 text-gray-500 hover:text-blue-500" title="Chamada de áudio"><i class="fas fa-phone-alt"></i></button>
                    <button class="p-2 text-gray-500 hover:text-blue-500" title="Chamada de vídeo"><i class="fas fa-video"></i></button>
                    <button class="p-2 text-gray-500 hover:text-blue-500" title="Mais opções"><i class="fas fa-ellipsis-v"></i></button>
                </div>
            `;
             // Adiciona listener ao novo botão de link gerado
             const generateLinkBtn = document.getElementById('generate-link-btn');
             if(generateLinkBtn) generateLinkBtn.addEventListener('click', generateShareLink);

             // Adiciona listener ao novo botão de voltar no cabeçalho
             const backToContactsHeaderBtn = document.getElementById('back-to-contacts-header');
             if(backToContactsHeaderBtn) backToContactsHeaderBtn.addEventListener('click', showSidebar);
        }

        // Atualiza o indicador de status do usuário logado
        function updateUserStatusIndicator() {
            // Remove todas as classes de status existentes
            userStatusDotEl.classList.remove('online', 'offline', 'busy');
            if (simulatedBackend.currentUser && simulatedBackend.currentUser.status) {
                userStatusDotEl.style.display = 'block'; // Mostra o ponto
                userStatusDotEl.classList.add(simulatedBackend.currentUser.status); // Adiciona a classe de status correta
            } else {
                 // Esconde se não houver usuário ou status
                 userStatusDotEl.style.display = 'none';
            }
        }


        // --- Funções de Lógica ---

        // Seleciona um chat com base no ID do contato
        async function selectChat(contactId) {
            // Remove a classe 'active' do contato anteriormente selecionado
            if (currentChatId) {
                const prevActiveContactEl = contactListEl.querySelector(`.contact[data-contact-id="${currentChatId}"]`);
                if (prevActiveContactEl) {
                    prevActiveContactEl.classList.remove('active', 'bg-blue-100');
                }
            }

            currentChatId = contactId; // Atualiza o ID do chat atual

            // Adiciona a classe 'active' ao contato selecionado
            const activeContactEl = contactListEl.querySelector(`.contact[data-contact-id="${currentChatId}"]`);
             if (activeContactEl) {
                activeContactEl.classList.add('active', 'bg-blue-100');
                 // Remove o contador de mensagens não lidas ao abrir o chat
                 const unreadSpan = activeContactEl.querySelector('span:last-child');
                 if(unreadSpan && unreadSpan.classList.contains('rounded-full')) {
                     unreadSpan.remove();
                 }
                  // Atualiza o estado 'unread' no array allContacts
                 const contactData = allContacts.find(c => c.id === contactId);
                 if(contactData) {
                     contactData.unread = 0;
                 }
            }


            // Encontra o contato selecionado na lista completa
            const selectedContact = allContacts.find(contact => contact.id === contactId);

            if (selectedContact) {
                updateChatHeader(selectedContact); // Atualiza o cabeçalho do chat

                // Simula o carregamento das mensagens do backend
                currentChatMessages = await fetchMessages(contactId);
                renderMessages(currentChatMessages); // Renderiza as mensagens

                // Habilita a área de input de mensagem
                messageInputEl.disabled = false;
                sendButtonEl.disabled = false;
                messageInputEl.placeholder = `Mensagem para ${selectedContact.name}`;
                messageInputEl.focus(); // Coloca o foco no input
                 showChatArea(); // Mostra a área de chat em mobile
            } else {
                console.error(`Contato com ID ${contactId} não encontrado.`);
                 // Reseta a área de chat se o contato não for encontrado
                 currentChatId = null;
                 currentChatMessages = [];
                 updateChatHeader({ name: 'Selecione um contato', online: false, avatar: '' }); // Cabeçalho padrão
                 renderMessages([]); // Limpa as mensagens
                 messageInputEl.disabled = true; // Desabilita input
                 sendButtonEl.disabled = true;
                 messageInputEl.placeholder = "Selecione um contato...";
            }
        }

        // Envia uma mensagem
        async function sendMessage() {
            const text = messageInputEl.value.trim();
            if (text === '' || currentChatId === null) return; // Não envia se o campo estiver vazio ou nenhum chat selecionado

            messageInputEl.value = ''; // Limpa o input imediatamente
            messageInputEl.disabled = true; // Desabilita enquanto envia
            sendButtonEl.disabled = true;

            // Simula o envio para o backend e espera a resposta
            const result = await sendMessageToBackend(currentChatId, text);

            // Adiciona a mensagem enviada à lista de mensagens atual
            currentChatMessages.push(result.sent);

            // Se houver uma resposta simulada, adiciona também
            if (result.received) {
                 currentChatMessages.push(result.received);
            }

            // Re-renderiza as mensagens para incluir a nova(s) mensagem(ns)
            renderMessages(currentChatMessages);

            // Atualiza a última mensagem e timestamp na lista de contatos simulada
            const contact = allContacts.find(c => c.id === currentChatId);
            if (contact) {
                 // Usa a última mensagem real (pode ser a enviada ou a resposta)
                 const lastMsg = currentChatMessages[currentChatMessages.length - 1];
                 contact.lastMessage = lastMsg ? (lastMsg.file ? `Arquivo: ${lastMsg.file.name}` : lastMsg.text) : ''; // Atualiza para a nova última mensagem ou vazio
                 contact.timestamp = lastMsg ? lastMsg.time : ''; // Atualiza timestamp ou vazio
                 // Re-renderiza a lista de contatos para refletir a mudança e a ordenação
                 filterAndRenderContacts(); // Filtra e renderiza com base na busca atual
            }

            // Salva o estado após enviar uma mensagem (opcional, dependendo da granularidade do save)
            // saveStateToLocalStorage();


            messageInputEl.disabled = false; // Habilita input novamente
            sendButtonEl.disabled = false;
            messageInputEl.focus(); // Retorna o foco
            scrollToBottom(); // Rola para o final
        }

        // Rola a área de mensagens para o final
        function scrollToBottom() {
            messageListEl.scrollTop = messageListEl.scrollHeight;
        }

        // Gera e copia o link de compartilhamento do CHAT ATUAL
        function generateShareLink() {
            if (currentChatId === null) {
                showMessageBox("Por favor, selecione uma conversa primeiro para gerar um link.");
                return;
            }
            // Pega a URL base (sem hash ou query params)
            const baseUrl = window.location.href.split('#')[0].split('?')[0];
            const shareableLink = `${baseUrl}?chatId=${currentChatId}`;

            // Tenta copiar para a área de transferência
            navigator.clipboard.writeText(shareableLink).then(() => {
                 showMessageBox(`Link da conversa copiado!\n\n${shareableLink}\n\nPode colar numa nova aba para testar.`);
            }).catch(err => {
                console.warn('Falha ao copiar para a área de transferência: ', err);
                 // Fallback para prompt se a cópia falhar
                prompt("Copie este link manualmente:", shareableLink);
            });
        }

        // Gera e copia o link para ADICIONAR UM NOVO USUÁRIO (convite de usuário)
        function generateInviteLink() {
             if (!simulatedBackend.currentUser) {
                 showMessageBox("Você precisa estar cadastrado para convidar outros usuários.");
                 return;
             }

            // Pega a URL base (sem hash ou query params)
            const baseUrl = window.location.href.split('#')[0].split('?')[0];
            // Adiciona o parâmetro inviteUserId com o ID do usuário ATUAL
            const inviteLink = `${baseUrl}?inviteUserId=${simulatedBackend.currentUser.id}`;

            // Tenta copiar para a área de transferência
            navigator.clipboard.writeText(inviteLink).then(() => {
                 showMessageBox(`Link de convite gerado e copiado!\n\nEnvie este link para convidar alguém para se conectar com você:\n${inviteLink}\n\nInstruções:\n1. Peça para a pessoa abrir este link em um navegador onde ela AINDA NÃO se cadastrou neste app simulado.\n2. Ela fará o cadastro.\n3. Após o cadastro, você aparecerá automaticamente na lista de contatos dela, e ela aparecerá na sua lista (pode ser necessário recarregar a página).`);
            }).catch(err => {
                console.warn('Falha ao copiar para a área de transferência: ', err);
                prompt("Copie este link manualmente:", inviteLink);
            });
        }


        // Processa os parâmetros da URL na inicialização (chatId ou inviteUserId)
        async function processUrlParams() {
            console.log("Iniciando processUrlParams...");
            const params = new URLSearchParams(window.location.search);
            const chatIdFromUrl = params.get('chatId');
            const inviteUserIdFromUrl = params.get('inviteUserId');

            console.log("chatIdFromUrl:", chatIdFromUrl);
            console.log("inviteUserIdFromUrl:", inviteUserIdFromUrl);


            // Carrega o estado do localStorage (incluindo o usuário, contatos e ALL USERS)
            loadStateFromLocalStorage();

            console.log("simulatedBackend.currentUser após carregar:", simulatedBackend.currentUser);

            // Verifica se o usuário já está cadastrado
            if (simulatedBackend.currentUser) {
                console.log("Usuário logado encontrado. Mostrando tela de chat.");
                // Usuário cadastrado, mostra a tela de chat
                showChatScreen();
                updateUserStatusIndicator(); // Atualiza o indicador de status do usuário

                // --- Lógica para adicionar novos contatos ao usuário logado ---
                console.log("Verificando novos usuários para adicionar aos contatos...");
                // Itera sobre todos os usuários potenciais (carregados do localStorage)
                simulatedBackend.allPotentialUsers.forEach(potentialUser => {
                     // Verifica se não é o usuário logado E se não está já na lista de contatos
                    const isCurrentUser = simulatedBackend.currentUser && potentialUser.id === simulatedBackend.currentUser.id;
                    const isAlreadyContact = simulatedBackend.contacts.some(contact => contact.id === potentialUser.id);

                    if (!isCurrentUser && !isAlreadyContact) {
                         // Simula a adição de um novo contato "descoberto" (alguém que se cadastrou)
                         const newContact = {
                             id: potentialUser.id,
                             name: potentialUser.name,
                             avatar: potentialUser.avatar,
                             lastMessage: 'Novo usuário!', // Mensagem padrão para novo contato
                             timestamp: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
                             unread: 0, // Começa sem mensagens não lidas
                             online: potentialUser.online // Simula o status online (agora todos são online por padrão)
                         };
                         simulatedBackend.contacts.push(newContact);
                         allContacts.push(newContact); // Mantém a lista local sincronizada
                         console.log(`Novo usuário ${potentialUser.name} (ID: ${potentialUser.id}) adicionado aos contatos do usuário logado (ID: ${simulatedBackend.currentUser.id}).`);
                    }
                });
                // Salva a lista de contatos atualizada (com os novos) no localStorage
                saveStateToLocalStorage();
                console.log("Lista de contatos atualizada e salva.");
                // --- Fim da lógica de adição de novos contatos ---


                // Se houver um chatId na URL, tenta abrir o chat
                if (chatIdFromUrl) {
                    console.log(`Tentando abrir chat com ID da URL: ${chatIdFromUrl}`);
                    const contactId = parseInt(chatIdFromUrl);
                    const contactToOpen = allContacts.find(c => c.id === contactId);
                    if (contactToOpen) {
                        setTimeout(() => {
                             console.log(`Selecionando chat com contato: ${contactToOpen.name}`);
                             selectChat(contactToOpen.id);
                        }, 100);
                    } else {
                        console.warn(`Contato com ID ${chatIdFromUrl} (da URL) não encontrado na lista de contatos.`);
                    }
                }

                // Renderiza a lista de contatos (agora incluindo os novos contatos adicionados acima)
                console.log("Renderizando lista de contatos...");
                filterAndRenderContacts();

            } else {
                console.log("Nenhum usuário logado encontrado. Mostrando tela de cadastro.");
                // Usuário não cadastrado, mostra a tela de cadastro
                showRegistrationScreen();
                 // Se houver um inviteUserId na URL E o usuário NÃO ESTÁ CADASTRO, armazena o ID do convidante.
                 if (inviteUserIdFromUrl) { // Verifica se invitingUserIdOnLoad tem um valor (não null ou undefined)
                     invitingUserIdOnLoad = parseInt(inviteUserIdFromUrl);
                     console.log(`InviteUserId ${invitingUserIdOnLoad} detectado. Será processado após o cadastro.`);
                 } else {
                     invitingUserIdOnLoad = null; // Garante que seja null se não estiver na URL
                 }
            }
             console.log("Fim de processUrlParams.");
        }


        // Filtra e renderiza os contatos com base no termo de busca
        function filterAndRenderContacts() {
            console.log("Filtrando e renderizando contatos...");
            const searchTerm = searchContactInputEl.value.toLowerCase();
            // Filtra a partir da lista completa (allContacts)
            const filtered = allContacts.filter(contact =>
                contact.name.toLowerCase().includes(searchTerm) ||
                (contact.lastMessage && contact.lastMessage.toLowerCase().includes(searchTerm)) // Opcional: buscar na última mensagem (verifica se lastMessage existe)
            );
            renderContacts(filtered); // Renderiza a lista filtrada
             console.log("Contatos filtrados e renderizados.");
        }

        // Função para mostrar a barra lateral (usado em mobile)
        function showSidebar() {
             console.log("Mostrando sidebar.");
             sidebarEl.classList.add('open');
             chatAreaEl.classList.remove('active');
             // Reseta o chat atual ao voltar para a lista de contatos
             currentChatId = null;
             currentChatMessages = [];
             updateChatHeader({ name: 'Selecione um contato', online: false, avatar: '' }); // Cabeçalho padrão
             renderMessages([]); // Limpa as mensagens
             messageInputEl.disabled = true; // Desabilita input
             sendButtonEl.disabled = true;
             messageInputEl.placeholder = "Selecione um contato...";
        }

        // Função para mostrar a área de chat (usado em mobile)
        function showChatArea() {
             console.log("Mostrando área de chat.");
             sidebarEl.classList.remove('open');
             chatAreaEl.classList.add('active');
        }

         // Função para mostrar a tela de cadastro
        function showRegistrationScreen() {
            console.log("Mostrando tela de cadastro.");
            registrationScreenEl.style.display = 'flex'; // Usa display: flex para mostrar a tela
            registrationScreenEl.classList.add('visible');
            chatContainerEl.style.display = 'none'; // Esconde o container do chat
             registrationErrorMessageEl.style.display = 'none'; // Garante que a mensagem de erro esteja escondida
             registrationErrorMessageEl.textContent = '';
        }

         // Função para mostrar a tela de chat
        function showChatScreen() {
            console.log("Mostrando tela de chat.");
            registrationScreenEl.style.display = 'none'; // Esconde a tela de cadastro
            registrationScreenEl.classList.remove('visible');
            chatContainerEl.style.display = 'flex'; // Mostra o container do chat
             // Garante que a sidebar esteja visível (especialmente em desktop)
             sidebarEl.classList.add('open');
             // Esconde a área de chat por padrão ao entrar na tela de chat
             chatAreaEl.classList.remove('active');
             // Reseta o chat atual
             currentChatId = null;
             currentChatMessages = [];
             updateChatHeader({ name: 'Selecione um contato', online: false, avatar: '' }); // Cabeçalho padrão
             renderMessages([]); // Limpa as mensagens
             messageInputEl.disabled = true; // Desabilita input
             sendButtonEl.disabled = true;
             messageInputEl.placeholder = "Selecione um contato...";
        }


        // Função para abrir o modal de Perfil
        function openProfileModal() {
            console.log("Abrindo modal de perfil.");
            // Carrega os dados atuais do usuário simulado nos inputs do modal de perfil
            if (simulatedBackend.currentUser) {
                 profileFullNameInputEl.value = simulatedBackend.currentUser.fullName || '';
                 profileNicknameInputEl.value = simulatedBackend.currentUser.name || ''; // Apelido é o nome de exibição
                 profilePhoneInputEl.value = simulatedBackend.currentUser.phone || '';
                 profileEmailInputEl.value = simulatedBackend.currentUser.email || '';
                 profileStatusSelectEl.value = simulatedBackend.currentUser.status || 'online'; // Carrega o status atual (agora online por padrão)

                 // Campos desabilitados conforme a simulação permite editar apenas o apelido e status
                 profileFullNameInputEl.disabled = true;
                 profilePhoneInputEl.disabled = true;
                 profileEmailInputEl.disabled = true;
                 profileNicknameInputEl.disabled = false; // Apelido é editável
                 profileStatusSelectEl.disabled = false; // Status é editável
                 saveProfileButtonEl.disabled = false; // Habilita botão salvar
            } else {
                 // Se não houver usuário logado (não deveria acontecer se o botão estiver visível)
                 profileFullNameInputEl.value = '';
                 profileNicknameInputEl.value = '';
                 profilePhoneInputEl.value = '';
                 profileEmailInputEl.value = '';
                 profileStatusSelectEl.value = 'offline';
                 profileFullNameInputEl.disabled = true;
                 profileNicknameInputEl.disabled = true;
                 profilePhoneInputEl.disabled = true;
                 profileEmailInputEl.disabled = true;
                 profileStatusSelectEl.disabled = true;
                 saveProfileButtonEl.disabled = true;
            }
             // Muda o título do modal para "Meu Perfil"
            settingsModalEl.querySelector('h2').textContent = "Meu Perfil";
            settingsModalOverlayEl.classList.add('visible');
        }

        // Função para fechar o modal (Perfil/Configurações)
        function closeSettingsModal() {
            console.log("Fechando modal de perfil.");
            settingsModalOverlayEl.classList.remove('visible');
             // Limpa a mensagem de erro ao fechar (se estiver sendo usada no modal)
             registrationErrorMessageEl.style.display = 'none';
             registrationErrorMessageEl.textContent = '';
             // Restaura o título padrão do modal se necessário (embora Perfil seja o único uso agora)
             settingsModalEl.querySelector('h2').textContent = "Configurações do Usuário";
        }

        // Função para salvar as configurações do usuário (agora focado no Perfil)
        function saveProfileSettings() {
            console.log("Salvando configurações de perfil...");
            const newNickname = profileNicknameInputEl.value.trim();
            const newStatus = profileStatusSelectEl.value;

            if (newNickname) {
                if (simulatedBackend.currentUser) {
                    simulatedBackend.currentUser.name = newNickname; // Atualiza o apelido
                    simulatedBackend.currentUser.status = newStatus; // Atualiza o status

                    // Salva o estado atualizado no localStorage
                    saveStateToLocalStorage();
                    updateUserStatusIndicator(); // Atualiza o indicador visual de status

                    showMessageBox("Perfil atualizado com sucesso!");
                    closeSettingsModal();
                     // Em um app real, talvez fosse necessário re-renderizar algo que mostre o nome do usuário
                } else {
                     showMessageBox("Erro: Usuário não encontrado para salvar perfil.");
                }
            } else {
                showMessageBox("O apelido não pode estar vazio.");
            }
             console.log("Configurações de perfil salvas.");
        }


         // Função para lidar com o cadastro de um novo usuário
        function registerUser() {
            console.log("Tentando cadastrar usuário...");
            const fullName = fullNameInputEl.value.trim();
            const nickname = nicknameInputEl.value.trim();
            const phone = phoneInputEl.value.trim();
            const email = emailInputEl.value.trim();

            // Validação simples
            if (!fullName || !nickname || !phone || !email) {
                registrationErrorMessageEl.textContent = "Por favor, preencha todos os campos.";
                registrationErrorMessageEl.style.display = 'block';
                console.log("Campos de cadastro incompletos.");
                return;
            }

             // Verifica se email ou telefone já existem (simulação básica)
            const existingUser = simulatedBackend.allPotentialUsers.find(user => user.email === email || user.phone === phone);
            if (existingUser) {
                 registrationErrorMessageEl.textContent = "Email ou telefone já cadastrado.";
                 registrationErrorMessageEl.style.display = 'block';
                 console.log("Email ou telefone já cadastrado.");
                 return;
            }


            // Simula a criação de um novo usuário com um ID único (baseado no timestamp para simulação)
            const newUserId = Date.now() + Math.floor(Math.random() * 1000);
            console.log("Gerando novo ID de usuário:", newUserId);


            const newUser = {
                id: newUserId, // ID único para o usuário atual
                fullName: fullName,
                name: nickname, // Usa o apelido como nome de exibição
                phone: phone,
                email: email,
                avatar: `https://placehold.co/100x100/${Math.floor(Math.random()*16777215).toString(16)}/ffffff?text=${nickname.substring(0, 2).toUpperCase()}`, // Gera avatar com cor aleatória
                status: 'online' // Define o status inicial como online
            };
            console.log("Novo objeto de usuário criado:", newUser);


            simulatedBackend.currentUser = newUser; // Define o usuário atual
            simulatedBackend.contacts = []; // Garante que a lista de contatos do novo usuário comece vazia
            console.log("simulatedBackend.currentUser definido como novo usuário.");


            // *** Lógica para processar o inviteUserId APÓS o cadastro ***
            if (invitingUserIdOnLoad !== null) {
                 console.log(`Processando inviteUserIdOnLoad: ${invitingUserIdOnLoad}`);
                 const invitingUserId = invitingUserIdOnLoad;
                 const invitingUser = simulatedBackend.allPotentialUsers.find(user => user.id === invitingUserId);

                 if (invitingUser) {
                     // Adiciona o usuário que convidou como um contato para o NOVO usuário
                     const newContact = {
                         id: invitingUser.id,
                         name: invitingUser.name,
                         avatar: invitingUser.avatar,
                         lastMessage: 'Conectado via link!',
                         timestamp: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
                         unread: 1,
                         online: invitingUser.online // Mantém o status do convidante
                     };
                     simulatedBackend.contacts.push(newContact);
                     // allContacts será atualizado antes de renderizar
                     console.log(`Usuário ${invitingUser.name} (ID: ${invitingUserId}) adicionado aos contatos do novo usuário (ID: ${newUserId}).`);

                 } else {
                     console.warn(`Usuário convidante com ID ${invitingUserId} não encontrado na lista potencial durante o cadastro.`);
                 }
                 // Limpa o ID do convidante após processar
                 invitingUserIdOnLoad = null;
                 console.log("inviteUserIdOnLoad limpo.");
            }
             // *** Fim da Lógica de Processamento do Convite no Cadastro ***

             // *** Adiciona o NOVO usuário à lista de allPotentialUsers para que outros possam encontrá-lo ***
             // Isso simula que o novo usuário agora existe no "diretório" do sistema.
             simulatedBackend.allPotentialUsers.push(newUser);
             console.log(`Novo usuário (ID: ${newUserId}) adicionado à lista de allPotentialUsers.`);


            saveStateToLocalStorage(); // Salva o novo usuário, a lista de contatos (potencialmente com o convidante) e a lista global de usuários

            showMessageBox("Cadastro realizado com sucesso!");

            // Oculta a tela de cadastro e mostra a tela de chat
            showChatScreen();
            updateUserStatusIndicator(); // Atualiza o indicador de status do usuário
             // Re-renderiza a lista de contatos (que agora pode conter o convidante)
             allContacts = [...simulatedBackend.contacts]; // Atualiza a variável local para a lista do NOVO usuário
             filterAndRenderContacts();

             // Limpa os campos do formulário após o cadastro bem-sucedido
             fullNameInputEl.value = '';
             nicknameInputEl.value = '';
             phoneInputEl.value = '';
             emailInputEl.value = '';
             registrationErrorMessageEl.style.display = 'none';
             registrationErrorMessageEl.textContent = '';
             console.log("Cadastro concluído. Tela de chat mostrada.");
        }


        // Função para lidar com a seleção de arquivos
        function handleFileSelect(event) {
            const files = event.target.files; // Obtém a lista de arquivos selecionados

            if (!files || files.length === 0) {
                return; // Sai se nenhum arquivo foi selecionado
            }

            if (currentChatId === null) {
                 showMessageBox("Por favor, selecione um contato para enviar arquivos.");
                 // Limpa o input de arquivo para permitir a seleção novamente
                 fileInputEl.value = '';
                 return;
            }


            // Processa cada arquivo selecionado
            for (const file of files) {
                 // Simula o envio do arquivo como uma mensagem
                 const fileMessage = {
                     senderId: simulatedBackend.currentUser.id, // Usuário atual
                     file: { // Adiciona um objeto 'file' com detalhes
                         name: file.name,
                         type: file.type,
                         size: file.size
                         // Em um app real, aqui teríamos a URL do arquivo após o upload
                     },
                     time: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
                     type: 'sent' // Mensagem enviada pelo usuário
                 };

                 // Adiciona a mensagem de arquivo à lista de mensagens atual
                 currentChatMessages.push(fileMessage);

                 // Re-renderiza as mensagens para incluir o arquivo simulado
                 renderMessages(currentChatMessages);

                 // Opcional: Atualizar a última mensagem na lista de contatos para indicar o arquivo
                 const contact = allContacts.find(c => c.id === currentChatId);
                 if (contact) {
                      contact.lastMessage = `Arquivo: ${file.name}`; // Exibe o nome do arquivo
                     contact.timestamp = fileMessage.time;
                      filterAndRenderContacts(); // Re-renderiza a lista de contatos
                 }

                 // Em um aplicativo real, aqui você faria o upload do arquivo para o backend.
                 console.log(`Simulando envio do arquivo: ${file.name} (${file.type}, ${file.size} bytes) para o contato ${currentChatId}`);
            }

            // Limpa o input de arquivo para permitir a seleção do mesmo arquivo novamente
            fileInputEl.value = '';
        }


         // Função simples para exibir mensagens ao usuário (substitui alert)
        function showMessageBox(message) {
            // Implementação simples: usar alert por enquanto, mas idealmente seria um modal customizado
            alert(message);
        }


        // --- Event Listeners ---

        // Evento de clique no botão de enviar
        sendButtonEl.addEventListener('click', sendMessage);

        // Evento de tecla pressionada no input de mensagem (para enviar com Enter)
        messageInputEl.addEventListener('keypress', function(e) {
            // Envia se a tecla for Enter e Shift não estiver pressionado (para permitir quebra de linha)
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault(); // Previne a quebra de linha padrão do Enter
                sendMessage();
            }
        });

        // Evento de clique no botão de voltar (em mobile, na barra lateral)
        backButtonEl.addEventListener('click', showSidebar);

        // Eventos do Modal de Perfil/Configurações
        profileButtonEl.addEventListener('click', openProfileModal); // Listener para o novo botão de perfil
        cancelProfileButtonEl.addEventListener('click', closeSettingsModal); // Botão cancelar no modal de perfil
        saveProfileButtonEl.addEventListener('click', saveProfileSettings); // Botão salvar no modal de perfil

        // Fecha o modal clicando fora dele
        settingsModalOverlayEl.addEventListener('click', function(event) {
            if (event.target === settingsModalOverlayEl) {
                closeSettingsModal();
            }
        });

        // Evento de clique no botão "Convidar Novo Usuário"
        inviteContactButtonEl.addEventListener('click', generateInviteLink);

        // Evento de clique no botão de Cadastro
        registerButtonEl.addEventListener('click', registerUser);

         // Permite cadastrar pressionando Enter nos campos do formulário
        const registrationInputs = registrationScreenEl.querySelectorAll('.registration-form input');
        registrationInputs.forEach(input => {
            input.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    e.preventDefault();
                    registerUser();
                }
            });
        });

        // Eventos para Anexar Arquivo
        attachButtonEl.addEventListener('click', function() {
             // Verifica se um chat está selecionado antes de abrir o seletor de arquivo
             if (currentChatId !== null) {
                 fileInputEl.click(); // Simula um clique no input de arquivo oculto
             } else {
                 showMessageBox("Por favor, selecione um contato para enviar arquivos.");
             }
        });
        fileInputEl.addEventListener('change', handleFileSelect); // Lida com a seleção de arquivos


        // --- Inicialização ---

        // Adiciona um listener para garantir que o DOM esteja completamente carregado antes de iniciar
        window.addEventListener('load', () => {
            console.log("Janela carregada. Iniciando processUrlParams...");
             // Adiciona um try...catch para capturar erros na inicialização
            try {
                processUrlParams();

                // MOVIDO PARA DENTRO DO load event listener
                // Evento de input no campo de busca (para filtrar em tempo real)
                searchContactInputEl.addEventListener('input', filterAndRenderContacts);


            } catch (e) {
                console.error("Erro fatal durante a inicialização:", e);
                 // Em caso de erro fatal, garante que a tela de autenticação seja mostrada
                 showRegistrationScreen(); // Mostra a tela de cadastro
                 registrationErrorMessageEl.textContent = "Ocorreu um erro inesperado ao carregar os dados. Por favor, tente novamente ou limpe o armazenamento local.";
                 registrationErrorMessageEl.style.display = 'block';
            }
        });


    </script>
</body>
</html>
