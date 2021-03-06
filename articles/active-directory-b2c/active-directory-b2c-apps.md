---
title: "Azure AD B2C | Документация Майкрософт"
description: "Типы приложений, которые можно создавать в Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d36102549550515e516522ecbd964cc7bd1b4d4


---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: типы приложений
Служба Azure Active Directory (Azure AD) B2C поддерживает проверку подлинности для различных архитектур современных приложений. Все они основаны на стандартных отраслевых протоколах: [OAuth 2.0](active-directory-b2c-reference-protocols.md) или [OpenID Connect](active-directory-b2c-reference-protocols.md). В этом документе кратко описаны типы приложений, которые можно создавать независимо от используемого языка или платформы. Кроме того, ознакомившись с ним, вы узнаете о сценариях, необходимых для [разработки приложений](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>Основные сведения
Каждое приложение, использующее Azure AD B2C, нужно зарегистрировать в [каталоге B2C](active-directory-b2c-get-started.md) на [портале Azure](https://portal.azure.com/). В процессе регистрации приложения будут установлены несколько параметров приложения:

* **идентификатор приложения** , определяющий конкретное приложение;
* **универсальный код ресурса (URI) перенаправления** , который можно использовать для направления ответов к приложению;
* несколько других зависящих от сценария значений. Дополнительные сведения см. в статье о [регистрации приложения](active-directory-b2c-app-registration.md).

После регистрации приложение взаимодействует со службой Azure AD, отправляя запросы к конечной точке Azure AD версии 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Каждый запрос, отправленный в Azure AD B2C, определяет какую-либо **политику**. Политика управляет поведением службы Azure AD. Кроме того, с помощью этих конечных точек вы можете создать настраиваемый набор действий пользователя. В число распространенных политик входят политики регистрации, входа и редактирования профилей. Если вы не знакомы с политиками, то, прежде чем продолжить, прочтите о [платформе расширяемых политик](active-directory-b2c-reference-policies.md) Azure AD B2C.

Взаимодействие каждого приложения с конечной точкой версии 2.0 использует аналогичный высокоуровневый шаблон.

1. Приложение направляет пользователя в конечную точку версии 2.0 для выполнения [политики](active-directory-b2c-reference-policies.md).
2. Пользователь выполняет политику в соответствии с ее определением.
3. Приложение получает определенный маркер безопасности от конечной точки версии 2.0.
4. Приложение использует маркер безопасности для доступа к защищенной информации или защищенному ресурсу.
5. Сервер ресурсов проверяет маркер безопасности для подтверждения того, что доступ может быть предоставлен.
6. Приложение периодически обновляет маркер безопасности.

<!-- TODO: Need a page for libraries to link to -->
Эти действия могут отличаться в зависимости от типа создаваемого приложения. Библиотеки с открытым исходным кодом обработают эти сведения за вас.

## <a name="web-apps"></a>веб-приложений:
Для размещенных на сервере веб-приложений (.NET, PHP, Java, Ruby, Python, Node.js и пр.), доступ к которым осуществляется через браузер, Azure AD B2C предоставляет поддержку всех пользовательских действий с помощью [OpenID Connect](active-directory-b2c-reference-protocols.md). К этим действиям относятся вход, регистрация и редактирование профилей. В реализации OpenID Connect в Azure AD B2C веб-приложение запускает соответствующие пользовательские интерфейсы, отправляя запросы проверки подлинности в Azure AD. Результат запроса — `id_token`. Этот маркер безопасности представляет удостоверение пользователя. Кроме того, он содержит сведения о пользователе в форме утверждений.

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Все типы токенов и утверждений, доступных для приложения, описаны в статье [Предварительная версия Azure AD B2C: справочник по маркерам](active-directory-b2c-reference-tokens.md).

В веб-приложениях выполнение любой [политики](active-directory-b2c-reference-policies.md) включает в себя следующие действия.

![Изображение веб-приложения Swimlanes](./media/active-directory-b2c-apps/webapp.png)

Чтобы проверить удостоверение пользователя, достаточно проверить `id_token` с помощью открытого ключа подписывания, полученного от службы Azure AD. Во время этой проверки также используется файл cookie сеанса, с помощью которого можно идентифицировать пользователя при последующих запросах страницы.

Чтобы увидеть этот сценарий в действии, изучите один из примеров кода входа в веб-приложение в разделе [Приступая к работе](active-directory-b2c-overview.md#getting-started).

Помимо простого входа в систему, приложению веб-сервера может потребоваться доступ к некоторым серверным веб-службам. В этом случае веб-приложение может выполнять несколько иной [поток OpenID Connect](active-directory-b2c-reference-oidc.md) и получать токены, используя коды авторизации и токены обновления. Этот сценарий представлен ниже в разделе [Веб-API](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Веб-API
Azure AD B2C может также служить для защиты веб-служб, таких как интерфейс веб-API RESTful приложения. Веб-API может использовать OAuth 2.0 для защиты данных, проверяя подлинность входящих HTTP-запросов с помощью токенов. Объект, вызывающий веб-API, добавляет маркер в начале заголовка авторизации HTTP-запроса.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Интерфейс веб-API может использовать маркер для проверки удостоверения объекта, вызывающего API, и получения сведений о нем из утверждений, которые закодированы в маркере. Все типы токенов и утверждений, доступных для приложения, описаны в [справочнике по токенам Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Azure AD B2C в настоящее время поддерживает только интерфейсы веб-API, используемые собственными известными клиентами. Например, готовое приложение может включать приложение iOS, приложение Android и серверный интерфейс веб-API. Такая архитектура поддерживается полностью. Сейчас не поддерживается предоставление доступа клиенту-партнеру (например, другому приложению iOS) к одному и тому же интерфейсу веб-API. У всех компонентов готового приложения должен быть один и тот же идентификатор приложения.
> 
> 

Интерфейс веб-API может получать маркеры от всех типов клиентов, включая веб-приложения, классические и мобильные приложения, одностраничные приложения, серверные управляющие программы и даже другие интерфейсы веб-API. В качестве примера рассмотрим полный поток веб-приложения, вызывающего интерфейс веб-API.

![Изображение веб-API веб-приложения Swimlanes](./media/active-directory-b2c-apps/webapi.png)

Дополнительные сведения о кодах авторизации, маркерах обновления, а также подробные инструкции о получении маркеров см. в описании [протокола OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Сведения о защите веб-API с помощью Azure AD B2C см. в руководствах по веб-API, перечисленных в разделе [Приступая к работе](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Мобильные и собственные приложения
Приложениям, установленным на устройстве, например мобильным и классическим приложениям, часто требуется доступ к внутренним службам или интерфейсам веб-API от имени пользователя. Добавить в собственные приложения настраиваемый интерфейс управления удостоверениями и обеспечить безопасный вызов серверных служб можно с помощью Azure AD B2C и [потока кода авторизации OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

В этом потоке приложение выполняет [политики](active-directory-b2c-reference-policies.md) и получает `authorization_code` из Azure AD, когда пользователь завершает политику. `authorization_code` означает, что приложение разрешает вызов серверных служб от имени пользователя, вошедшего в систему. Приложение может затем обмениваться кодом `authorization_code` в фоновом режиме для маркеров `id_token` и `refresh_token`.  Приложение может использовать `id_token` для проверки подлинности в серверном интерфейсе веб-API в HTTP-запросах. Оно также может использовать `refresh_token` для получения нового маркера `id_token` после истечения срока действия старого.

> [!NOTE]
> В настоящее время Azure AD B2C поддерживает только токены, которые используются для доступа к собственной серверной веб-службе приложения. Например, готовое приложение может включать приложение iOS, приложение Android и серверный интерфейс веб-API. Такая архитектура поддерживается полностью. Сейчас не поддерживается предоставление приложению iOS доступа к партнерскому интерфейсу веб-API по маркерам доступа OAuth 2.0. У всех компонентов готового приложения должен быть один и тот же идентификатор приложения.
> 
> 

![Изображение собственного приложения Swimlanes](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Текущие ограничения
В настоящее время Azure AD B2C не поддерживает приведенные ниже типы приложений, но их поддержка планируется в будущем. Другие ограничения Azure AD B2C описаны в статье [Предварительная версия Azure Active Directory B2C: пределы и ограничения](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Одностраничные приложения (Javascript)
Многие современные приложения содержат одностраничное приложение переднего плана, созданное преимущественно с помощью языка JavaScript. Они часто работают на таких платформах, как AngularJS, Ember.js или Durandal. Общедоступная служба Azure AD поддерживает эти приложения с помощью неявного потока OAuth 2.0. Однако этот поток еще недоступен в предварительной версии Azure AD B2C.

### <a name="daemonsserverside-apps"></a>Управляющие программы и серверные приложения
Приложениям, содержащим долговременные процессы или работающим без пользователя, тоже нужна возможность доступа к защищенным ресурсам, таким как интерфейсы веб-API. Такие приложения могут проверять подлинность и получать маркеры, используя удостоверение приложения (а не удостоверение делегированного пользователя) с помощью потока учетных данных клиента OAuth 2.0.

На текущий момент служба Azure AD B2C не поддерживает этот поток. Эти приложения могут получить маркеры, только после того как появится интерактивный пользовательский поток.

### <a name="web-api-chains-onbehalfof-flow"></a>Цепочки веб-API (поток On-Behalf-Of)
Многие архитектуры включают в себя интерфейс веб-API, которому требуется вызывать другой нисходящий веб-API. При этом Azure AD B2C защищает оба интерфейса. Этот сценарий характерен для собственных клиентов, содержащих интерфейс веб-API в качестве серверного компонента. Этот интерфейс вызывает службу Microsoft Online Service, например Azure AD API Graph.

Этот сценарий веб-API с цепочками может поддерживаться путем предоставления учетных данных носителя JWT OAuth 2.0 или потока On-Behalf-Of.  Однако в Azure AD B2C поток On-Behalf-Of еще не реализован.




<!--HONumber=Nov16_HO2-->


