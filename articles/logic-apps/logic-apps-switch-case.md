---
title: "Utiliser l’instruction switch dans Azure Logic Apps | Microsoft Docs"
description: "L’instruction switch vous permet d’exécuter facilement des actions différentes selon la valeur d’une expression dans Logic Apps."
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>Utiliser l’instruction switch dans Logic Apps
Lorsque vous créez un flux de travail, vous devez souvent exécuter des actions différentes selon la valeur d’un objet ou d’une expression. Par exemple, vous pouvez avoir besoin que votre application logique se comporte différemment selon le code d’état d’une demande HTTP ou l’option sélectionnée pour un message électronique d’approbation.

Ces scénarios peuvent être mis en œuvre à l’aide d’une instruction switch : Logic App évalue un jeton ou une expression et choisit le cas de figure présentant la même valeur pour déterminer les actions à exécuter. L’instruction switch ne doit correspondre qu’à un seul cas de figure.

 > [!TIP]
 > Comme tous les langages de programmation, l’instruction switch prend uniquement en charge les opérateurs d’égalité. Si vous avez besoin d’autres opérateurs relationnels (par exemple, supérieur à), utilisez une instruction de condition.
 >
 > Pour garantir un comportement d’exécution constant, les cas de figure doivent contenir une valeur statique unique et non des jetons ou une expression dynamiques.

## <a name="prerequisites"></a>Composants requis

- Abonnement Azure actif.
    - Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free/) ou essayez [Logic Apps gratuitement](https://tryappservice.azure.com/).
- [Connaissance de base de Logic Apps](logic-apps-what-are-logic-apps.md).

## <a name="working-with-switch-statement-in-designer"></a>Utilisation de l’instruction switch dans le concepteur
Pour illustrer l’utilisation de l’instruction switch, nous allons créer une application logique qui surveille les fichiers chargés sur la Dropbox. L’application logique envoie un courrier électronique d’approbation pour déterminer si un transfert vers SharePoint est requis. Nous utiliserons l’instruction switch pour exécuter des actions différentes en fonction de la valeur sélectionnée par l’approbateur.

1. Commencez par créer une application logique, puis sélectionnez le déclencheur **Dropbox - When a file is created** (Dropbox - Lorsqu’un fichier est créé).

 ![Utiliser le déclencheur Dropbox - When a file is created (Dropbox - Lorsqu’un fichier est créé)](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. À la suite du déclencheur, définissez l’action **Outlook.com - Send approval email** (Outlook.com - Envoyer un message électronique d’approbation).

 > [!TIP]
 > Logic Apps prend également en charge l’envoi d’un message électronique d’approbation à partir d’un compte Office 365 Outlook.

 - Si vous n’avez pas de connexion existante, vous serez invité à en créer une.
 - Remplissez les champs obligatoires. Pour notre exemple, nous enverrons le message électronique à l’adresse approvers@contoso.com.
 - Sous *Options utilisateur*, entrez `Approve, Reject`.

 ![Configurer la connexion](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Ajoutez une instruction switch.
 - Sélectionnez **+ Nouvelle étape**, **... Plus**, **Add a switch statement** (Ajouter une instruction switch).
 - Nous souhaitons sélectionner l’action à exécuter selon la sortie `SelectedOptions` de l’action *Envoyer un message électronique d’approbation*, ce que permet de faire le sélecteur **Ajouter du contenu dynamique**.
 - Utilisez *Case 1* (Cas de figure 1) pour gérer la sélection de `Approve` par l’utilisateur.
    - En cas d’approbation, copiez le fichier d’origine dans SharePoint Online avec l’action **SharePoint Online - Create file** (SharePoint Online - Créer un fichier).
    - Ajoutez une autre action au cas de figure pour notifier les utilisateurs qu’un nouveau fichier est disponible dans SharePoint.
 - Ajoutez un autre cas de figure pour gérer la sélection de `Reject` par l’utilisateur.
    - En cas de refus, envoyez un message électronique de notification informant les autres approbateurs que le fichier a été rejeté et qu’aucune action supplémentaire n’est requise.
 - Nous savons que deux options seulement sont fournies pour `SelectedOptions`. *Par défaut* peut donc être laissé vide.

 ![Instruction switch](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > L’instruction switch requiert au moins un cas de figure en plus de celui par défaut.

4. Après l’instruction switch, supprimez le fichier d’origine chargé sur la Dropbox avec l’action **Dropbox - Delete file** (Dropbox - Supprimer le fichier).

5. Enregistrez votre application logique et testez-le en chargeant un fichier sur la Dropbox. Vous devriez recevoir un message électronique d’approbation peu après. Sélectionnez une option et observez ce qui se passe.
 > [!TIP]
 > Découvrez comment [analyser vos Logic Apps](logic-apps-monitor-your-logic-apps.md).

## <a name="understanding-code-behind"></a>Présentation du code associé
Maintenant que vous avez créé une application logique utilisant l’instruction switch, examinons le code associé ci-dessous.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

`"Switch"` est le nom de l’instruction switch. Il peut être modifié pour plus de lisibilité. `"type": "Switch"` indique que l’action est une instruction switch. `"expression"` : dans le cas présent, l’option sélectionnée par l’utilisateur est évaluée en fonction de chaque cas de figure déclaré plus loin dans la définition. `"cases"` peut contenir un nombre illimité de cas de figure. Si aucun de ces cas de figure ne correspond à l’expression de switch, les actions définies dans `"default"` sont exécutées.

`"cases"` peut contenir un nombre illimité de cas de figure. `"Case 1"` est le nom du cas de figure. Il peut être modifié pour plus de lisibilité. `"case"` spécifie l’étiquette du cas de figure, utilisée pour la comparaison de l’expression de switch. Il doit s’agir d’une valeur unique et constante.  

## <a name="next-steps"></a>Étapes suivantes
- Essayez les autres [fonctionnalités de Logic Apps](logic-apps-use-logic-app-features.md).
- Consultez des informations sur la [gestion des erreurs et des exceptions](logic-apps-exception-handling.md).
- Découvrez les autres [fonctionnalités en matière de langage de flux de travail](logic-apps-author-definitions.md).
- Laissez un commentaire avec vos questions ou vos remarques, ou [dites-nous comment nous pouvons améliorer Logic Apps](https://feedback.azure.com/forums/287593-logic-apps).


<!--HONumber=Feb17_HO2-->


