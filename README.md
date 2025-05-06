<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тестовая система</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .login-form, .test-container, .results-container {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            margin-bottom: 20px;
        }
        h1, h2 {
            color: #333;
        }
        input, button {
            padding: 8px;
            margin: 5px 0;
            width: 100%;
            box-sizing: border-box;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        .question {
            margin-bottom: 15px;
        }
        .hidden {
            display: none;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #f2f2f2;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
    </style>
</head>
<body>
    <!-- Форма входа -->
    <div id="loginForm" class="login-form">
        <h1>Вход в систему</h1>
        <div>
            <label for="username">Логин:</label>
            <input type="text" id="username" required>
        </div>
        <div>
            <label for="password">Пароль:</label>
            <input type="password" id="password" required>
        </div>
        <button id="loginBtn">Войти</button>
    </div>

    <!-- Контейнер теста (для учеников) -->
    <div id="testContainer" class="test-container hidden">
        <h1>Тест по предмету</h1>
        <div id="questions">
            <div class="question">
                <p>1. Вопрос 1: Какой язык программирования вы изучаете?</p>
                <input type="radio" name="q1" value="a" id="q1a"> <label for="q1a">JavaScript</label><br>
                <input type="radio" name="q1" value="b" id="q1b"> <label for="q1b">Python</label><br>
                <input type="radio" name="q1" value="c" id="q1c"> <label for="q1c">Java</label><br>
                <input type="radio" name="q1" value="d" id="q1d"> <label for="q1d">C++</label>
            </div>
            
            <div class="question">
                <p>2. Вопрос 2: Что такое HTML?</p>
                <input type="radio" name="q2" value="a" id="q2a"> <label for="q2a">Язык программирования</label><br>
                <input type="radio" name="q2" value="b" id="q2b"> <label for="q2b">Язык разметки</label><br>
                <input type="radio" name="q2" value="c" id="q2c"> <label for="q2c">База данных</label><br>
                <input type="radio" name="q2" value="d" id="q2d"> <label for="q2d">Фреймворк</label>
            </div>
            
            <div class="question">
                <p>3. Вопрос 3: Какой тег используется для создания ссылки?</p>
                <input type="radio" name="q3" value="a" id="q3a"> <label for="q3a">&lt;link&gt;</label><br>
                <input type="radio" name="q3" value="b" id="q3b"> <label for="q3b">&lt;a&gt;</label><br>
                <input type="radio" name="q3" value="c" id="q3c"> <label for="q3c">&lt;href&gt;</label><br>
                <input type="radio" name="q3" value="d" id="q3d"> <label for="q3d">&lt;url&gt;</label>
            </div>
        </div>
        <button id="submitTest">Отправить тест</button>
    </div>

    <!-- Контейнер результатов (для учителя) -->
    <div id="resultsContainer" class="results-container hidden">
        <h1>Результаты тестирования</h1>
        <div id="resultsTable"></div>
        <button id="logoutBtn">Выйти</button>
    </div>

    <script>
        // База данных пользователей
        const users = {
            // Учитель
            "Зотова": { password: "4792hh", role: "teacher" },
            
            // Ученики
            "Вербульский": { password: "2990sg", role: "student" },
            "Гоглазин": { password: "5672sy", role: "student" },
            "Головырина": { password: "4627ej", role: "student" },
            "Гордиенко": { password: "5291ey", role: "student" },
            "Григорьевна": { password: "0937fh", role: "student" },
            "Гутаров": { password: "8787dy", role: "student" },
            "Давыдов": { password: "5638cj", role: "student" },
            "Дьячкова": { password: "3232db", role: "student" },
            "Журавлёв": { password: "4343dh", role: "student" },
            "Заходященко": { password: "9989eu", role: "student" },
            "Кайгородов": { password: "5678dh", role: "student" },
            "Камко": { password: "5252sh", role: "student" },
            "Клименко": { password: "7756eo", role: "student" },
            "Костина": { password: "5683dh", role: "student" },
            "Лопунова": { password: "6207Ys", role: "student" },
            "Лысенко": { password: "1838Ys", role: "student" },
            "Магер": { password: "6281Ag", role: "student" }
        };

        // Правильные ответы
        const correctAnswers = {
            q1: "a", // JavaScript
            q2: "b", // Язык разметки
            q3: "b"  // <a>
        };

        // "Облачное" хранилище результатов (в реальности - localStorage)
        let testResults = JSON.parse(localStorage.getItem('testResults')) || {};

        // Элементы DOM
        const loginForm = document.getElementById('loginForm');
        const testContainer = document.getElementById('testContainer');
        const resultsContainer = document.getElementById('resultsContainer');
        const loginBtn = document.getElementById('loginBtn');
        const submitTest = document.getElementById('submitTest');
        const logoutBtn = document.getElementById('logoutBtn');
        const resultsTable = document.getElementById('resultsTable');

        // Текущий пользователь
        let currentUser = null;

        // Обработчик входа
        loginBtn.addEventListener('click', () => {
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;
            
            if (users[username] && users[username].password === password) {
                currentUser = username;
                
                if (users[username].role === 'teacher') {
                    showTeacherView();
                } else {
                    showStudentView();
                }
            } else {
                alert('Неверный логин или пароль');
            }
        });

        // Обработчик отправки теста
        submitTest.addEventListener('click', () => {
            // Проверяем ответы
            let score = 0;
            const totalQuestions = Object.keys(correctAnswers).length;
            
            for (const question in correctAnswers) {
                const selectedAnswer = document.querySelector(`input[name="${question}"]:checked`);
                if (selectedAnswer && selectedAnswer.value === correctAnswers[question]) {
                    score++;
                }
            }
            
            // Сохраняем результат
            const result = {
                student: currentUser,
                score: score,
                total: totalQuestions,
                date: new Date().toLocaleString()
            };
            
            // Добавляем результат в хранилище
            if (!testResults[currentUser]) {
                testResults[currentUser] = [];
            }
            testResults[currentUser].push(result);
            localStorage.setItem('testResults', JSON.stringify(testResults));
            
            alert(`Тест завершен! Ваш результат: ${score} из ${totalQuestions}`);
            showLoginView();
        });

        // Обработчик выхода
        logoutBtn.addEventListener('click', showLoginView);

        // Показать форму входа
        function showLoginView() {
            currentUser = null;
            loginForm.classList.remove('hidden');
            testContainer.classList.add('hidden');
            resultsContainer.classList.add('hidden');
            document.getElementById('username').value = '';
            document.getElementById('password').value = '';
        }

        // Показать интерфейс ученика
        function showStudentView() {
            loginForm.classList.add('hidden');
            testContainer.classList.remove('hidden');
            resultsContainer.classList.add('hidden');
            
            // Сброс выбранных ответов
            const radioButtons = document.querySelectorAll('input[type="radio"]');
            radioButtons.forEach(radio => {
                radio.checked = false;
            });
        }

        // Показать интерфейс учителя
        function showTeacherView() {
            loginForm.classList.add('hidden');
            testContainer.classList.add('hidden');
            resultsContainer.classList.remove('hidden');
            
            // Отображаем результаты
            displayResults();
        }

        // Отобразить таблицу результатов
        function displayResults() {
            let html = '<table>';
            html += '<tr><th>Ученик</th><th>Результат</th><th>Дата</th></tr>';
            
            for (const student in testResults) {
                const lastResult = testResults[student][testResults[student].length - 1];
                html += `<tr>
                    <td>${student}</td>
                    <td>${lastResult.score} из ${lastResult.total}</td>
                    <td>${lastResult.date}</td>
                </tr>`;
            }
            
            html += '</table>';
            resultsTable.innerHTML = html;
        }

        // Инициализация
        showLoginView();
    </script>
</body>
</html>
