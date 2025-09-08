<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Форма заявки на автозапчасти</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .container {
            background-color: white;
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
            width: 100%;
            max-width: 500px;
            overflow: hidden;
        }
        
        .header {
            background: #2c3e50;
            color: white;
            padding: 20px;
            text-align: center;
        }
        
        .header h1 {
            font-size: 1.5rem;
            margin-bottom: 5px;
        }
        
        .header p {
            font-size: 0.9rem;
            opacity: 0.8;
        }
        
        .form-container {
            padding: 25px;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #2c3e50;
            font-size: 0.9rem;
        }
        
        input, select, textarea {
            width: 100%;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: 6px;
            font-size: 1rem;
            transition: all 0.3s;
        }
        
        input:focus, select:focus, textarea:focus {
            outline: none;
            border-color: #3498db;
            box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.2);
        }
        
        textarea {
            min-height: 100px;
            resize: vertical;
        }
        
        .required::after {
            content: " *";
            color: #e74c3c;
        }
        
        button {
            background: #3498db;
            color: white;
            border: none;
            padding: 14px 20px;
            border-radius: 6px;
            width: 100%;
            cursor: pointer;
            font-size: 1.1rem;
            font-weight: 600;
            transition: background 0.3s;
            margin-top: 10px;
        }
        
        button:hover {
            background: #2980b9;
        }
        
        .optional {
            font-size: 0.8rem;
            color: #7f8c8d;
            font-style: italic;
        }
        
        .notification {
            padding: 15px;
            margin-top: 20px;
            border-radius: 6px;
            text-align: center;
            display: none;
        }
        
        .success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        
        .error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        
        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid rgba(255,255,255,.3);
            border-radius: 50%;
            border-top-color: #fff;
            animation: spin 1s ease-in-out infinite;
            margin-right: 10px;
        }
        
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        
        @media (max-width: 600px) {
            .container {
                border-radius: 8px;
            }
            
            .header {
                padding: 15px;
            }
            
            .form-container {
                padding: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>Заявка на автозапчасти</h1>
            <p>Заполните форму для быстрого поиска деталей</p>
        </div>
        
        <div class="form-container">
            <form id="carForm">
                <div class="form-group">
                    <label for="vin" class="required">VIN-код автомобиля</label>
                    <input type="text" id="vin" name="vin" placeholder="Введите 17-значный VIN-код" required>
                </div>
                
                <div class="form-group">
                    <label for="brand">Марка автомобиля</label>
                    <input type="text" id="brand" name="brand" placeholder="Например: Toyota, BMW, Volkswagen">
                </div>
                
                <div class="form-group">
                    <label for="year">Год выпуска</label>
                    <input type="number" id="year" name="year" min="1950" max="2030" placeholder="Например: 2018">
                </div>
                
                <div class="form-group">
                    <label for="article">Артикул детали (при наличии)</label>
                    <input type="text" id="article" name="article" placeholder="Если известен артикул нужной детали">
                    <p class="optional">Необязательное поле</p>
                </div>
                
                <div class="form-group">
                    <label for="description" class="required">Описание проблемы</label>
                    <textarea id="description" name="description" placeholder="Подробно опишите проблему или нужную деталь..." required></textarea>
                </div>
                
                <button type="submit" id="submitBtn">Отправить заявку</button>
            </form>
            
            <div id="notification" class="notification"></div>
        </div>
    </div>

    <script>
        // Замените этот URL на URL вашего веб-приложения Google Apps Script
        const GAS_URL = 'https://script.google.com/macros/s/ВАШ_ИДЕНТИФИКАТОР_СКРИПТА/exec';
        
        document.getElementById('carForm').addEventListener('submit', async function(e) {
            e.preventDefault();
            
            const submitBtn = document.getElementById('submitBtn');
            const originalBtnText = submitBtn.textContent;
            
            // Показываем индикатор загрузки
            submitBtn.innerHTML = '<span class="loading"></span> Отправка...';
            submitBtn.disabled = true;
            
            const vin = document.getElementById('vin').value;
            const brand = document.getElementById('brand').value;
            const year = document.getElementById('year').value;
            const article = document.getElementById('article').value;
            const description = document.getElementById('description').value;
            
            // Простая валидация
            if (!vin || !description) {
                showNotification('Заполните обязательные поля (VIN-код и описание проблемы)', 'error');
                submitBtn.textContent = originalBtnText;
                submitBtn.disabled = false;
                return;
            }
            
            if (vin.length !== 17) {
                showNotification('VIN-код должен содержать 17 символов', 'error');
                submitBtn.textContent = originalBtnText;
                submitBtn.disabled = false;
                return;
            }
            
            try {
                // Отправка данных в Google Apps Script
                const response = await fetch(GAS_URL, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        vin: vin,
                        brand: brand,
                        year: year,
                        article: article,
                        description: description
                    })
                });
                
                const result = await response.json();
                
                if (result.result === "success") {
                    showNotification('Заявка успешно отправлена! Мы свяжемся с вами в ближайшее время.', 'success');
                    document.getElementById('carForm').reset();
                } else {
                    showNotification('Произошла ошибка при отправке. Попробуйте еще раз.', 'error');
                }
            } catch (error) {
                console.error('Ошибка:', error);
                showNotification('Произошла ошибка при отправке. Проверьте подключение к интернету.', 'error');
            } finally {
                // Восстанавливаем кнопку
                submitBtn.textContent = originalBtnText;
                submitBtn.disabled = false;
            }
        });
        
        function showNotification(message, type) {
            const notification = document.getElementById('notification');
            notification.textContent = message;
            notification.className = 'notification ' + type;
            notification.style.display = 'block';
            
            // Скрыть уведомление через 5 секунд
            setTimeout(() => {
                notification.style.display = 'none';
            }, 5000);
        }
    </script>
</body>
</html>
