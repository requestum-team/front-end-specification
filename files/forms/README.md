# Forms

1. Поля форм должны проходить обязательную валидацию на:
    a) соответствие ожидаемым шаблонам (шаблоны для email, web page, name, age и т.д.), что может быть реализовано через регулярные выражения;
    б) допустимое число символов (например для логина не менее 2-х символов и не более 20-ти);
    в) наличие ожидаемых символов (например для пароля: прописные, строчные и цифры);
    г) отсутствие запрещенных символов, которые либо:
        - не соответствуют названию поля (например цифры и спецсимволы в поле имени/фамилии, буквы в поле возраста);
        - ставят под угрозу работу клиентской или серверной части приложения (<, >, *, ...).
2. Вся валидация, которая уместна и возможна на клиенте, должна осуществлять на клиенте.
3. По мере ввода значения в поле или после нажатия кнопки отправки формы должны выводиться предупреждения о невалидности вводимых данных (если это имеет место), например:
    а) Выделение невалидного или обязательного к заполнению, но пустого поля броским стилевым оформлением;                   б) Сообщение-подсказка, например: “Неверный пароль”, “Поле ‘возраст’ принимает только численные значения”, “Недопустимые символы в поле ‘Имя’”;
    в) Комбинация а) и б);
    г) Наиболее общее сообщение об ошибке (для небольших форм, а для больших только в сочетании с пунктом а)): “Пожалуйста, проверьте поля ввода на наличие ошибок”, “Пожалуйста, заполните обязательные поля”.
4. Сообщения об ошибках должны быть:
    а) понятны и недвусмысленны, вне зависимости от языка;
    б) свободны от грамматических и синтаксических ошибок;
    в) характерны для разрабатываемого приложения, языковых и культурных особенностей пользователей.
5. Сообщения об ошибках могут выводиться как до отправки на сервер, так и после получения ответа.
6. До получения положительного ответа от сервера, маршрутизатор приложения не должен давать доступ пользователю к страницам, требующим особых прав доступа.
7. Необходимо отсекать непечатные символы в начале и конце ввода, например значение поля “    someName ” должно быть передано на сервер как “someName”. (Использование string.trim()).
8. В полях ввода должны присутствовать подсказки (placeholder), сообщающие пользователю имя и назначение поля, или же, что более актуально в отдельных случаях, формат ввода. Например: “First Name”, “mm/dd/yyyy”, “+380661234567”.
9. В случае заполнения объемных форм регистрации необходимо предупреждать пользователя о потере введенных данных, если он намерен покинуть страницу, не завершив процесс.
