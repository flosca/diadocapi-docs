CreateEmployee
==============

.. http:post:: /CreateEmployee

    :query boxId: идентификатор ящика

    :reqheader Authorization: необходимые данные для :doc:`авторизации <../Authorization>`

    :statuscode 200: операция успешно завершена
    :statuscode 400: данные в запросе имеют неверный формат или отсутствуют обязательные параметры
    :statuscode 401: в запросе отсутствует HTTP-заголовок ``Authorization``, или в этом заголовке содержатся некорректные авторизационные данные
    :statuscode 402: закончилась подписка на API
    :statuscode 403: доступ к ящику с предоставленным авторизационным токеном запрещен или запрос сделан не от имени администратора
    :statuscode 405: используется неподходящий HTTP-метод
    :statuscode 500: при обработке запроса возникла непредвиденная ошибка

**Тело запроса:** :doc:`../proto/EmployeeToCreate`

**Тело ответа:** :doc:`../proto/Employee`

Добавляет сотрудника с указанными реквизитами в организацию. В случае, если пользователя с указанными реквизитами не существует в Диадоке, создает его. Отправляет пользователю уведомление по электронной почте.

В ответе будет информация о созданном пользователе.

Запрос доступен только администраторам организации.

Примеры использования (C#)
--------------------------

**Пример создания сотрудника с логином**:

.. code-block:: csharp

    var employee = api.CreateEmployee(
        token,
        boxId,
        new EmployeeToCreate
        {
            Credentials = new EmployeeToCreateCredentials
            {
                Login = new EmployeeToCreateByLogin
                {
                    Login = "email@example.com",
                    FullName = new FullName
                    {
                        FirstName = "Иван",
                        MiddleName = "Иванович",
                        LastName = "Иванов"
                    }
                }
            },
            Position = "Бухгалтер",
            Permissions = new EmployeePermissions
            {
                UserDepartmentId = "15d57c9b-645d-4710-85fa-b166e2cfcfc8",
                IsAdministrator = false,
                DocumentAccessLevel = DocumentAccessLevel.DepartmentAndSubdepartments,
                Actions =
                {
                    new EmployeeAction { Name = "CreateDocuments", IsAllowed = true },
                    new EmployeeAction { Name = "SignDocuments", IsAllowed = true },
                    new EmployeeAction { Name = "AddResolutions", IsAllowed = false },
                    new EmployeeAction { Name = "RequestResolutions", IsAllowed = false }
                }
            }
        });

**Пример создания сотрудника с сертификатом**:

.. code-block:: csharp

    var employee = api.CreateEmployee(
        token,
        boxId,
        new EmployeeToCreate
        {
            Credentials = new EmployeeToCreateCredentials
            {
                Certificate = new EmployeeToCreateByCertificate
                {
                    Content = certificateBytes,
                    AccessBasis = "Доверенность №39 от 21.08.2018",
                    Email = "email@example.com"
                }
            },
            Position = "Директор",
            Permissions = new EmployeePermissions
            {
                UserDepartmentId = "00000000-0000-0000-0000-000000000000",
                IsAdministrator = true,
                DocumentAccessLevel = DocumentAccessLevel.SelectedDepartments,
                SelectedDepartmentIds = { "e97f0026-29e2-4b0f-bcc7-ebb31511e0f9", "4eef75de-44f3-4df6-8599-6c3fad74e31e" },
                Actions =
                {
                    new EmployeeAction { Name = "CreateDocuments", IsAllowed = true },
                    new EmployeeAction { Name = "SignDocuments", IsAllowed = true },
                    new EmployeeAction { Name = "AddResolutions", IsAllowed = true },
                    new EmployeeAction { Name = "RequestResolutions", IsAllowed = true }
                }
            }
        });

