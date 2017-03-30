---
title: Liaisons SendGrid dans Azure Functions | Microsoft Docs
description: "Informations de référence sur les liaisons SendGrid dans Azure Functions"
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/16/2017
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 0cd7e7c55e77863c142800cdc11d6ea144c38293
ms.lasthandoff: 03/18/2017


---
# <a name="azure-functions-sendgrid-bindings"></a>Liaisons SendGrid dans Azure Functions

Cet article explique comment configurer et utiliser des liaisons SendGrid dans Azure Functions. Avec SendGrid, vous pouvez utiliser Azure Functions pour envoyer un e-mail personnalisé par programmation.

Cet article fournit des informations de référence pour les développeurs Azure Functions. Si vous ne connaissez pas bien Azure Functions, commencez par consulter les ressources suivantes :

[Créer votre première fonction Azure](functions-create-first-azure-function.md). 
Références pour les développeurs [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) ou [Node](functions-reference-node.md).

## <a name="functionjson-for-sendgrid-bindings"></a>function.json pour les liaisons SendGrid

Azure Functions fournit une liaison de sortie pour SendGrid. La liaison de sortie SendGrid vous permet de créer et d’envoyer un e-mail par programmation. 

La liaison SendGrid prend en charge les propriétés suivantes :

- `name` : obligatoire - nom de variable utilisé dans le code de la fonction pour la demande ou dans le corps de la demande. Cette valeur est ```$return``` lorsqu’il n’existe qu’une valeur de retour. 
- `type` : obligatoire - doit être « SendGrid ».
- `direction` : obligatoire - doit être « out ».
- `apiKey` :obligatoire - doit correspondre au nom de votre clé API stockée dans les paramètres d’application de Function App.
- `to` : adresse e-mail du destinataire.
- `from` : adresse e-mail de l’expéditeur.
- `subject` : objet de l’e-mail.
- `text` : contenu de l’e-mail.

Exemple de code **function.json** :

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Azure Functions stocke vos informations de connexion et les clés API en tant que paramètres d’application, de sorte qu’elles ne soient pas vérifiées dans votre référentiel de contrôle de code source. Ceci protège vos informations sensibles.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>Exemple de liaison de sortie SendGrid en C#

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>Exemple de liaison de sortie SendGrid en Node

```javascript
module.exports = function (context, input) {    
    var message = {
        to: "recipient@contoso.com",
        from: "sender@contoso.com",        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations sur les autres liaisons et déclencheurs pour Azure Functions, consultez : 
- [Informations de référence pour les développeurs sur les déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)

- [Meilleures pratiques pour Azure Functions](functions-best-practices.md) répertorie les meilleures pratiques à utiliser lors de la création de fonctions d’Azure.

- [Référence du développeur Azure Functions](functions-reference.md) Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.
