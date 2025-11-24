<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FaceAuth - Система биометрической аутентификации</title>
    
    <!-- Favicon -->
    <link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><defs><linearGradient id='grad' x1='0%' y1='0%' x2='100%' y2='100%'><stop offset='0%' stop-color='%231a2a6c'/><stop offset='50%' stop-color='%23b21f1f'/><stop offset='100%' stop-color='%23fdbb2d'/></linearGradient></defs><circle cx='50' cy='50' r='48' fill='url(%23grad)' stroke='%23fff' stroke-width='1'/><text x='50' y='52' font-family='Arial' font-size='8' font-weight='bold' text-anchor='middle' fill='%23fff'>FA</text></svg>">
    
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcode-generator/1.4.4/qrcode.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            color: #fff;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            padding: 30px 0;
            margin-bottom: 30px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }

        .subtitle {
            font-size: 1.2rem;
            opacity: 0.9;
        }

        .status-bar {
            background: rgba(0, 0, 0, 0.4);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        .user-status {
            font-size: 1.1rem;
        }

        .auth-status {
            padding: 8px 15px;
            border-radius: 20px;
            font-weight: bold;
        }

        .authenticated {
            background: #4CAF50;
        }

        .not-authenticated {
            background: #f44336;
        }

        .main-content {
            display: grid;
            grid-template-columns: 1fr 350px;
            gap: 30px;
        }

        .actions-panel {
            background: rgba(0, 0, 0, 0.4);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }

        .actions-panel h2 {
            margin-bottom: 20px;
            text-align: center;
            font-size: 1.8rem;
            border-bottom: 2px solid rgba(255, 255, 255, 0.2);
            padding-bottom: 10px;
        }

        .action-buttons {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }

        .btn {
            background: rgba(255, 255, 255, 0.1);
            border: none;
            color: white;
            padding: 15px;
            border-radius: 10px;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
            min-height: 100px;
        }

        .btn:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
        }

        .btn i {
            font-size: 2rem;
            margin-bottom: 10px;
        }

        .btn-primary {
            background: rgba(76, 175, 80, 0.3);
        }

        .btn-primary:hover {
            background: rgba(76, 175, 80, 0.5);
        }

        .btn-danger {
            background: rgba(244, 67, 54, 0.3);
        }

        .btn-danger:hover {
            background: rgba(244, 67, 54, 0.5);
        }

        .btn-warning {
            background: rgba(255, 152, 0, 0.3);
        }

        .btn-warning:hover {
            background: rgba(255, 152, 0, 0.5);
        }

        .info-panel {
            background: rgba(0, 0, 0, 0.4);
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }

        .info-panel h2 {
            margin-bottom: 20px;
            text-align: center;
            font-size: 1.8rem;
            border-bottom: 2px solid rgba(255, 255, 255, 0.2);
            padding-bottom: 10px;
        }

        .camera-feed {
            width: 100%;
            height: 200px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 10px;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }

        .camera-feed video {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .camera-placeholder {
            font-size: 4rem;
            opacity: 0.5;
        }

        .users-list {
            max-height: 300px;
            overflow-y: auto;
            margin-bottom: 20px;
        }

        .user-item {
            background: rgba(255, 255, 255, 0.1);
            padding: 12px 15px;
            margin-bottom: 10px;
            border-radius: 8px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .user-item:last-child {
            margin-bottom: 0;
        }

        .user-actions button {
            background: none;
            border: none;
            color: white;
            cursor: pointer;
            margin-left: 10px;
            font-size: 1.2rem;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }

        .modal-content {
            background: #2c3e50;
            width: 90%;
            max-width: 500px;
            border-radius: 15px;
            padding: 30px;
            box-shadow: 0 15px 50px rgba(0, 0, 0, 0.5);
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        }

        .modal-title {
            font-size: 1.5rem;
        }

        .close-modal {
            background: none;
            border: none;
            color: white;
            font-size: 1.5rem;
            cursor: pointer;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
        }

        .form-group input {
            width: 100%;
            padding: 12px;
            border-radius: 8px;
            border: none;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            font-size: 1rem;
        }

        .form-actions {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
            margin-top: 20px;
        }

        .btn-cancel {
            background: rgba(255, 255, 255, 0.1);
        }

        .btn-confirm {
            background: #4CAF50;
        }

        .logs {
            background: rgba(0, 0, 0, 0.4);
            border-radius: 15px;
            padding: 25px;
            margin-top: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }

        .logs h2 {
            margin-bottom: 15px;
            font-size: 1.5rem;
        }

        .log-content {
            height: 200px;
            overflow-y: auto;
            background: rgba(0, 0, 0, 0.3);
            padding: 15px;
            border-radius: 8px;
            font-family: monospace;
            font-size: 0.9rem;
            line-height: 1.5;
        }

        .log-entry {
            margin-bottom: 8px;
            padding-bottom: 8px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .log-entry:last-child {
            margin-bottom: 0;
            border-bottom: none;
        }

        .log-success {
            color: #4CAF50;
        }

        .log-error {
            color: #f44336;
        }

        .log-warning {
            color: #FFC107;
        }

        .log-info {
            color: #2196F3;
        }

        .public-access {
            text-align: center;
            margin-top: 30px;
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
        }

        .qr-code {
            margin: 20px auto;
            background: white;
            padding: 15px;
            border-radius: 10px;
            display: inline-block;
        }

        .hosting-info {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            margin-top: 15px;
            font-size: 0.9rem;
        }

        .hosting-steps {
            text-align: left;
            margin: 15px 0;
        }

        .hosting-step {
            margin: 10px 0;
            padding: 10px;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 5px;
        }

        .step-number {
            display: inline-block;
            width: 25px;
            height: 25px;
            background: #4CAF50;
            border-radius: 50%;
            text-align: center;
            line-height: 25px;
            margin-right: 10px;
            font-weight: bold;
        }

        .empty-state {
            text-align: center;
            padding: 20px;
            opacity: 0.7;
        }

        @media (max-width: 900px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            .action-buttons {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🚀 FaceAuth Demo</h1>
            <p class="subtitle">Демонстрационная система биометрической аутентификации</p>
        </header>

        <div class="status-bar">
            <div class="user-status">
                Статус: 
                <span id="auth-status" class="auth-status not-authenticated">Не аутентифицирован</span>
            </div>
            <div id="current-user" class="user-info">
                Текущий пользователь: <span id="username">-</span>
            </div>
        </div>

        <div class="main-content">
            <div class="actions-panel">
                <h2>Действия</h2>
                <div class="action-buttons">
                    <button class="btn btn-primary" id="register-btn">
                        <i>📝</i>
                        <span>Регистрация</span>
                    </button>
                    <button class="btn btn-primary" id="auth-btn">
                        <i>🔐</i>
                        <span>Аутентификация</span>
                    </button>
                    <button class="btn" id="list-users-btn">
                        <i>📊</i>
                        <span>Список пользователей</span>
                    </button>
                    <button class="btn btn-warning" id="edit-user-btn">
                        <i>✏️</i>
                        <span>Изменить ID</span>
                    </button>
                    <button class="btn btn-danger" id="delete-user-btn">
                        <i>🗑️</i>
                        <span>Удалить пользователя</span>
                    </button>
                    <button class="btn" id="logout-btn">
                        <i>🔓</i>
                        <span>Выход</span>
                    </button>
                </div>
            </div>

            <div class="info-panel">
                <h2>Информация</h2>
                <div class="camera-feed">
                    <div class="camera-placeholder">📷</div>
                </div>
                <div class="users-list" id="users-list">
                    <div class="empty-state">Нет зарегистрированных пользователей</div>
                </div>
            </div>
        </div>

        <div class="logs">
            <h2>Журнал событий</h2>
            <div class="log-content" id="log-content">
                <div class="log-entry log-info">Система инициализирована. Готов к работе.</div>
            </div>
        </div>

        <div class="public-access">
            <h2>🌐 Публичный доступ к системе</h2>
            <p>Отсканируйте QR-код для тестирования системы на любом устройстве</p>
            
            <div class="qr-code" id="qrcode"></div>
            
            <div class="hosting-info">
                <h3>🚀 Разместите систему онлайн бесплатно</h3>
                
                <div class="hosting-steps">
                    <div class="hosting-step">
                        <span class="step-number">1</span>
                        <strong>Сохраните этот файл</strong> как "faceauth-demo.html"
                    </div>
                    <div class="hosting-step">
                        <span class="step-number">2</span>
                        <strong>Загрузите на бесплатный хостинг:</strong>
                        <div style="margin: 10px 0; padding: 10px; background: rgba(255,255,255,0.1); border-radius: 5px;">
                            • <a href="https://pages.github.com/" target="_blank" style="color: #4CAF50;">GitHub Pages</a> (рекомендуется)<br>
                            • <a href="https://vercel.com/" target="_blank" style="color: #4CAF50;">Vercel</a><br>
                            • <a href="https://www.netlify.com/" target="_blank" style="color: #4CAF50;">Netlify</a><br>
                            • <a href="https://surge.sh/" target="_blank" style="color: #4CAF50;">Surge.sh</a>
                        </div>
                    </div>
                    <div class="hosting-step">
                        <span class="step-number">3</span>
                        <strong>Поделитесь ссылкой</strong> с QR-кодом
                    </div>
                </div>
                
                <div style="margin-top: 15px; padding: 10px; background: rgba(255,255,255,0.1); border-radius: 5px;">
                    <strong>📧 Ваш публичный URL будет выглядеть так:</strong><br>
                    <code id="public-url" style="display: block; margin: 10px 0; padding: 10px; background: rgba(255,255,255,0.2); border-radius: 3px;">
                        https://ваше-имя.github.io/faceauth-demo
                    </code>
                </div>
                
                <div style="margin-top: 15px; font-size: 0.8rem; opacity: 0.8;">
                    💡 <strong>Быстрый старт:</strong> Создайте аккаунт на GitHub, загрузите этот файл в репозиторий 
                    и активируйте GitHub Pages в настройках.
                </div>
            </div>
        </div>
    </div>

    <!-- Модальные окна -->
    <div class="modal" id="register-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Регистрация нового пользователя</h3>
                <button class="close-modal">&times;</button>
            </div>
            <div class="form-group">
                <label for="user-id">ID пользователя:</label>
                <input type="text" id="user-id" placeholder="Введите ID студента">
            </div>
            <div class="form-actions">
                <button class="btn btn-cancel">Отмена</button>
                <button class="btn btn-confirm">Зарегистрировать</button>
            </div>
        </div>
    </div>

    <div class="modal" id="auth-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Аутентификация</h3>
                <button class="close-modal">&times;</button>
            </div>
            <div class="auth-instructions">
                <p>Смотрите прямо в камеру для распознавания лица.</p>
            </div>
            <div class="form-actions">
                <button class="btn btn-cancel">Отмена</button>
                <button class="btn btn-confirm">Начать аутентификацию</button>
            </div>
        </div>
    </div>

    <div class="modal" id="edit-user-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Изменение ID пользователя</h3>
                <button class="close-modal">&times;</button>
            </div>
            <div class="form-group">
                <label for="old-user-id">Текущий ID:</label>
                <input type="text" id="old-user-id" placeholder="Введите текущий ID">
            </div>
            <div class="form-group">
                <label for="new-user-id">Новый ID:</label>
                <input type="text" id="new-user-id" placeholder="Введите новый ID">
            </div>
            <div class="form-actions">
                <button class="btn btn-cancel">Отмена</button>
                <button class="btn btn-confirm">Изменить</button>
            </div>
        </div>
    </div>

    <div class="modal" id="delete-user-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">Удаление пользователя</h3>
                <button class="close-modal">&times;</button>
            </div>
            <div class="form-group">
                <label for="delete-user-id">ID пользователя для удаления:</label>
                <input type="text" id="delete-user-id" placeholder="Введите ID пользователя">
            </div>
            <div class="form-actions">
                <button class="btn btn-cancel">Отмена</button>
                <button class="btn btn-confirm btn-danger">Удалить</button>
            </div>
        </div>
    </div>

    <script>
        // Имитация данных системы
        const systemData = {
            currentUser: null,
            users: [],
            logs: [
                { message: "FaceAuth Demo System инициализирована", type: "info" },
                { message: "Готов к тестированию биометрической аутентификации", type: "success" }
            ]
        };

        // Элементы DOM
        const authStatus = document.getElementById('auth-status');
        const username = document.getElementById('username');
        const usersList = document.getElementById('users-list');
        const logContent = document.getElementById('log-content');
        const qrcodeContainer = document.getElementById('qrcode');
        const publicUrlElement = document.getElementById('public-url');

        // Генерация QR-кода для текущей страницы
        function generateQRCode() {
            try {
                const currentUrl = window.location.href;
                
                // Показываем текущий URL
                publicUrlElement.textContent = currentUrl;
                
                // Генерируем QR-код
                const typeNumber = 0;
                const errorCorrectionLevel = 'L';
                const qr = qrcode(typeNumber, errorCorrectionLevel);
                qr.addData(currentUrl);
                qr.make();
                
                const canvas = document.createElement('canvas');
                const ctx = canvas.getContext('2d');
                const cellSize = 6;
                const margin = 4;
                const size = qr.getModuleCount() * cellSize + margin * 2;
                
                canvas.width = size;
                canvas.height = size;
                
                // Белый фон
                ctx.fillStyle = '#ffffff';
                ctx.fillRect(0, 0, size, size);
                
                // Черный QR-код
                ctx.fillStyle = '#000000';
                for (let row = 0; row < qr.getModuleCount(); row++) {
                    for (let col = 0; col < qr.getModuleCount(); col++) {
                        if (qr.isDark(row, col)) {
                            ctx.fillRect(
                                col * cellSize + margin,
                                row * cellSize + margin,
                                cellSize,
                                cellSize
                            );
                        }
                    }
                }
                
                qrcodeContainer.innerHTML = '';
                qrcodeContainer.appendChild(canvas);
                
                addLog('QR-код для публичного доступа сгенерирован', 'success');
                
            } catch (error) {
                console.error('Ошибка генерации QR-кода:', error);
                showManualQR();
            }
        }

        function showManualQR() {
            const url = window.location.href;
            qrcodeContainer.innerHTML = `
                <div style="color: black; background: white; padding: 20px; border-radius: 10px; max-width: 300px;">
                    <h4 style="margin: 0 0 10px 0; color: #1a2a6c;">🔗 Ссылка на FaceAuth Demo</h4>
                    <p style="margin: 0; font-size: 14px; word-break: break-all; background: #f5f5f5; padding: 10px; border-radius: 5px; font-family: monospace;">${url}</p>
                    <p style="margin: 10px 0 0 0; font-size: 12px; color: #666;">Используйте эту ссылку для доступа</p>
                </div>
            `;
            publicUrlElement.textContent = url;
        }

        // Функции системы
        function updateUI() {
            if (systemData.currentUser) {
                authStatus.textContent = 'Аутентифицирован';
                authStatus.className = 'auth-status authenticated';
                username.textContent = systemData.currentUser;
            } else {
                authStatus.textContent = 'Не аутентифицирован';
                authStatus.className = 'auth-status not-authenticated';
                username.textContent = '-';
            }
            renderUsersList();
        }

        function renderUsersList() {
            usersList.innerHTML = '';
            if (systemData.users.length === 0) {
                usersList.innerHTML = '<div class="empty-state">Нет зарегистрированных пользователей</div>';
                return;
            }
            systemData.users.forEach(user => {
                const userItem = document.createElement('div');
                userItem.className = 'user-item';
                userItem.innerHTML = `
                    <div class="user-info">
                        <strong>${user.id}</strong> - ${user.name}
                    </div>
                    <div class="user-actions">
                        <button title="Удалить" onclick="deleteUser('${user.id}')">🗑️</button>
                    </div>
                `;
                usersList.appendChild(userItem);
            });
        }

        function addLog(message, type = 'info') {
            const logEntry = document.createElement('div');
            logEntry.className = `log-entry log-${type}`;
            logEntry.textContent = `[${new Date().toLocaleTimeString()}] ${message}`;
            logContent.appendChild(logEntry);
            logContent.scrollTop = logContent.scrollHeight;
            systemData.logs.push({ message, type });
        }

        function registerUser(userId) {
            if (!userId) {
                addLog('Ошибка: ID пользователя не может быть пустым', 'error');
                return false;
            }

            const existingUser = systemData.users.find(user => user.id === userId);
            if (existingUser) {
                addLog(`Пользователь ${userId} уже зарегистрирован`, 'warning');
                if (!confirm(`Пользователь ${userId} уже зарегистрирован. Перезаписать?`)) {
                    return false;
                }
                systemData.users = systemData.users.filter(user => user.id !== userId);
                addLog(`Старая запись пользователя ${userId} удалена`, 'warning');
            }

            addLog(`Начата регистрация пользователя ${userId}`, 'info');
            
            setTimeout(() => {
                addLog(`Снимок 1/3... Смотрите в камеру`, 'info');
            }, 1000);
            
            setTimeout(() => {
                addLog(`Снимок 2/3... Смотрите в камеру`, 'info');
            }, 3000);
            
            setTimeout(() => {
                addLog(`Снимок 3/3... Смотрите в камеру`, 'info');
                
                const newUser = {
                    id: userId,
                    name: `Студент ${userId}`
                };
                
                systemData.users.push(newUser);
                addLog(`Пользователь ${userId} успешно зарегистрирован!`, 'success');
                closeModal('register');
                updateUI();
            }, 5000);
            
            return true;
        }

        function authenticate() {
            if (systemData.users.length === 0) {
                addLog('Ошибка: В базе нет зарегистрированных пользователей', 'error');
                closeModal('auth');
                return;
            }
            
            addLog('Начата аутентификация... Смотрите в камеру', 'info');
            
            setTimeout(() => {
                const randomUser = systemData.users[Math.floor(Math.random() * systemData.users.length)];
                
                if (randomUser && Math.random() > 0.3) {
                    systemData.currentUser = randomUser.id;
                    addLog(`Успешная аутентификация! Добро пожаловать, ${randomUser.id}!`, 'success');
                } else {
                    addLog('Аутентификация не удалась. Лицо не распознано.', 'error');
                }
                
                updateUI();
                closeModal('auth');
            }, 3000);
        }

        function deleteUser(userId) {
            if (!userId) {
                addLog('Ошибка: ID пользователя не может быть пустым', 'error');
                return false;
            }

            const userIndex = systemData.users.findIndex(user => user.id === userId);
            if (userIndex === -1) {
                addLog(`Пользователь ${userId} не найден в базе данных`, 'error');
                return false;
            }

            if (confirm(`Вы уверены, что хотите удалить пользователя ${userId}?`)) {
                systemData.users.splice(userIndex, 1);
                
                if (systemData.currentUser === userId) {
                    systemData.currentUser = null;
                    addLog(`Текущая сессия завершена, так как удален активный пользователь`, 'warning');
                }
                
                addLog(`Пользователь ${userId} удален из базы данных`, 'success');
                updateUI();
                closeModal('delete');
                return true;
            }
            
            return false;
        }

        function editUser(oldId, newId) {
            if (!oldId || !newId) {
                addLog('Ошибка: ID пользователя не может быть пустым', 'error');
                return false;
            }

            const userIndex = systemData.users.findIndex(user => user.id === oldId);
            if (userIndex === -1) {
                addLog(`Пользователь ${oldId} не найден в базе данных`, 'error');
                return false;
            }

            systemData.users[userIndex].id = newId;
            systemData.users[userIndex].name = `Студент ${newId}`;
            
            if (systemData.currentUser === oldId) {
                systemData.currentUser = newId;
            }
            
            addLog(`ID пользователя изменен: ${oldId} -> ${newId}`, 'success');
            updateUI();
            closeModal('edit');
            return true;
        }

        function logout() {
            if (systemData.currentUser) {
                addLog(`До свидания, ${systemData.currentUser}!`, 'info');
                systemData.currentUser = null;
            } else {
                addLog('Нет активной сессии', 'warning');
            }
            updateUI();
        }

        // Управление модальными окнами
        function openModal(modalName) {
            document.getElementById(modalName + '-modal').style.display = 'flex';
        }

        function closeModal(modalName) {
            document.getElementById(modalName + '-modal').style.display = 'none';
            document.querySelectorAll('input').forEach(input => input.value = '');
        }

        // Инициализация
        document.addEventListener('DOMContentLoaded', function() {
            updateUI();
            generateQRCode();
            addLog('Система готова к тестированию! Поделитесь QR-кодом с другими.', 'success');
            
            // Обработчики событий
            document.getElementById('register-btn').addEventListener('click', () => openModal('register'));
            document.getElementById('auth-btn').addEventListener('click', () => openModal('auth'));
            document.getElementById('list-users-btn').addEventListener('click', () => {
                addLog('Просмотр списка пользователей', 'info');
                renderUsersList();
            });
            document.getElementById('edit-user-btn').addEventListener('click', () => {
                if (!systemData.currentUser) {
                    addLog('ДОСТУП ЗАПРЕЩЕН! Требуется аутентификация.', 'error');
                    return;
                }
                openModal('edit');
            });
            document.getElementById('delete-user-btn').addEventListener('click', () => {
                if (!systemData.currentUser) {
                    addLog('ДОСТУП ЗАПРЕЩЕН! Требуется аутентификация.', 'error');
                    return;
                }
                openModal('delete');
            });
            document.getElementById('logout-btn').addEventListener('click', logout);

            // Обработчики для модальных окон
            document.querySelectorAll('.close-modal').forEach(button => {
                button.addEventListener('click', function() {
                    const modal = this.closest('.modal');
                    closeModal(modal.id.split('-')[0]);
                });
            });

            document.querySelectorAll('.btn-cancel').forEach(button => {
                button.addEventListener('click', function() {
                    const modal = this.closest('.modal');
                    closeModal(modal.id.split('-')[0]);
                });
            });

            document.querySelector('#register-modal .btn-confirm').addEventListener('click', function() {
                const userId = document.getElementById('user-id').value;
                registerUser(userId);
            });

            document.querySelector('#auth-modal .btn-confirm').addEventListener('click', authenticate);

            document.querySelector('#edit-user-modal .btn-confirm').addEventListener('click', function() {
                const oldId = document.getElementById('old-user-id').value;
                const newId = document.getElementById('new-user-id').value;
                editUser(oldId, newId);
            });

            document.querySelector('#delete-user-modal .btn-confirm').addEventListener('click', function() {
                const userId = document.getElementById('delete-user-id').value;
                deleteUser(userId);
            });

            window.addEventListener('click', function(event) {
                if (event.target.classList.contains('modal')) {
                    event.target.style.display = 'none';
                    closeModal(event.target.id.split('-')[0]);
                }
            });
        });
    </script>
</body>
</html>
