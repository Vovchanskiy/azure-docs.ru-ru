
1. Выполните вход на [классический портал Azure](https://manage.windowsazure.com/), выберите пункт **Мобильные службы**, а затем щелкните свою мобильную службу.
2. Перейдите на вкладку **Push**, выберите значение **Только прошедшие проверку пользователи** для параметра **Разрешения**, нажмите **Сохранить**, а затем **Изменить скрипт**.
   
    Это позволит настроить функцию обратного вызова для регистрации push-уведомлений. Если для редактирования кода используется Git, ту же функцию регистрации можно найти в `.\service\extensions\push.js`.
3. Замените имеющуюся функцию **register** следующим кодом и нажмите кнопку **Сохранить**:
   
        exports.register = function (registration, registrationContext, done) {   
            // Get the ID of the logged-in user.
            var userId = registrationContext.user.userId;    
   
            // Perform a check here for any disallowed tags.
            if (!validateTags(registration))
            {
                // Return a service error when the client tries 
                // to set a user ID tag, which is not allowed.        
                done("You cannot supply a tag that is a user ID");        
            }
            else{
                // Add a new tag that is the user ID.
                registration.tags.push(userId);
   
                // Complete the callback as normal.
                done();
            }
        };
   
        function validateTags(registration){
            for(var i = 0; i < registration.tags.length; i++) { 
                console.log(registration.tags[i]);           
                if (registration.tags[i]
                .search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
                    return false;
                }
                return true;
            }
        }
   
    К регистрации будет добавлен тег, представляющий собой идентификатор вошедшего в систему пользователя. Указанные теги проверяются, чтобы пользователь не мог зарегистрироваться под идентификатором другого пользователя. Когда этому пользователю отправляется уведомление, оно приходит на это и все другие устройства, зарегистрированные пользователем.
4. Щелкните стрелку назад, затем вкладку **Данные**, **TodoItem**, **Скрипт** и выберите **Вставка**.

<!---HONumber=AcomDC_1203_2015-->