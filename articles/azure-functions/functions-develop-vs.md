---
title: "Développer Azure Functions à l’aide de Visual Studio | Microsoft Docs"
description: "Apprenez à développer et à tester Azure Functions à l’aide d’Azure Functions Tools pour Visual Studio 2017."
services: functions
documentationcenter: .net
author: ggailey777
manager: erikre
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: glenga, donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 8b569727c51589bd622d41465bb3565220c19fca
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---
# <a name="azure-functions-tools-for-visual-studio"></a>Azure Functions Tools pour Visual Studio  

Azure Functions Tools pour Visual Studio 2017 est une extension pour Visual Studio qui vous permet de développer, de tester et de déployer des fonctions C# sur Azure. S’il s’agit de votre première expérience avec Azure Functions, vous pouvez en apprendre davantage dans l’article [Présentation d’Azure Functions](functions-overview.md).

Azure Functions Tools propose les avantages suivants : 

* Modifier, générer et exécuter des fonctions sur votre ordinateur de développement local. 
* Publier votre projet Azure Functions directement sur Azure. 
* Utiliser des attributs WebJobs pour déclarer des liaisons de fonction directement dans le code C# au lieu de maintenir une fonction.json distincte pour les définitions de liaison.
* Développer et déployer des fonctions précompilées C#. Les fonctions précompilées offrent de meilleures performances de démarrage à froid que les fonctions basées sur un script C#. 
* Coder vos fonctions en C# tout en bénéficiant de tous les avantages du développement Visual Studio. 

Cette rubrique vous montre comment utiliser Azure Functions Tools pour Visual Studio 2017 afin de développer vos fonctions en C#. Vous apprenez également à publier votre projet sur Azure en tant qu’assembly .NET.

## <a name="prerequisites"></a>Composants requis

Avant d’installer Azure Functions Tools, vous devez avoir installé la [préversion de Visual Studio 2017 15.3](https://www.visualstudio.com/vs/preview/), y compris l’une des charges de travail suivantes :

* Développement Azure
* Développement web et ASP.NET

Pour créer et déployer des fonctions, vous avez également besoin des éléments suivants :

* Un abonnement Azure actif. Si tel n’est pas le cas, des [comptes gratuits](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) sont disponibles.

* Un compte de stockage Azure. Pour créer un compte de stockage, consultez [Créez un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account).  

## <a name="install-the-azure-functions-tools"></a>Installer Azure Functions Tools

Vous pouvez [télécharger et installer le package d’extension](https://marketplace.visualstudio.com/vsgallery/e3705d94-7cc3-4b79-ba7b-f43f30774d28), ou suivre les étapes ci-après pour l’installer à partir de Visual Studio.  

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)] 


## <a name="create-an-azure-functions-project"></a>Créer un projet Azure Functions 

[!INCLUDE [Create a project using the Azure Functions ](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>Configurer le projet pour un développement local

Lorsque vous créez un projet à l’aide du modèle Azure Functions, vous obtenez un projet C# vide qui contient les fichiers suivants:

* **host.json** : vous permet de configurer l’hôte Functions. Ces paramètres s’appliquent lors de l’exécution en local et dans Azure. Pour plus d’informations, consultez l’article de référence sur [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
    
* **local.settings.json** : maintient les paramètres utilisés lors de l’exécution des fonction en local. Ces paramètres ne sont pas utilisés par Azure, ils sont utilisés par Azure Functions Core Tools. Utilisez ce fichier pour spécifier des paramètres, tels que des chaînes de connexion vers d’autres services Azure. Ajoutez une clé au tableau **Valeurs** pour chaque connexion requise par les fonctions dans votre projet. Pour plus d’informations, consultez [Local settings file](functions-run-local.md#local-settings-file) (Fichier de paramètres local) dans la rubrique Procédure locale de codage et de test d’Azure Functions.

Le runtime de Functions utilise un compte de stockage Azure en interne. Pour tous les types de déclencheur autres que HTTP et webhooks, vous devez définir la clé **Values.AzureWebJobsStorage** sur une chaîne de connexion de compte de stockage Azure valide. Pour définir la chaîne de connexion de compte de stockage :

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte de stockage, cliquez sur **Tous les paramètres** > **Clés d’accès**, puis copiez la **chaîne de connexion** pour l’une de vos clés. 

2. Dans votre projet dans Visual Studio, ouvrez le fichier de projet local.settings.json et définissez la valeur de la clé **AzureWebJobsStorage** sur la chaîne de connexion que vous avez copiée.

3. Répétez l’étape précédente pour ajouter des clés uniques au tableau **Valeurs** pour les autres connexions requises par vos fonctions.  

## <a name="create-a-function"></a>Créer une fonction

Dans les fonctions précompilées, les liaisons utilisées par la fonction sont définies en appliquant des attributs dans le code. Lorsque vous utilisez Azure Functions Tools pour créer vos fonctions à partir des modèles fournis, ces attributs sont appliqués pour vous. 

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le nœud de projet et sélectionnez **Ajouter** > **Nouvel élément**. Sélectionnez **Fonction Azure**, tapez un **nom** pour la classe, puis cliquez sur **Ajouter**.

2. Choisissez votre déclencheur, définissez les propriétés de liaison, puis cliquez sur **Créer**. L’exemple suivant montre les paramètres lors de la création d’une fonction déclenchée par le stockage File d’attente. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    Une clé de chaîne de connexion nommée **QueueStorage** est fournie, qui est définie dans le fichier local.settings.json. Le nom de la classe 
 
3. Examinez la classe qui vient d’être ajoutée. Vous voyez une méthode statique **Run**, qui est attribuée avec l’attribut **FunctionName**. Cet attribut indique que la méthode est le point d’entrée de la fonction. 

    Par exemple, la classe C# suivante représente une fonction de stockage déclenchée par le stockage File d’attente :

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    Un attribut spécifique à la liaison est appliqué à chaque paramètre de liaison fourni à la méthode de point d’entrée. L’attribut accepte les informations de liaison en tant que paramètres. Dans l’exemple précédent, le premier paramètre a un attribut **QueueTrigger** appliqué, qui indique la fonction déclenchée par la file d’attente. Le nom de la file d’attente et le nom du paramètre de la chaîne de connexion sont transmis en tant que paramètres.  

## <a name="testing-functions"></a>Tester les fonctions

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

Avec le projet en cours d’exécution, vous pouvez tester votre code comme vous testeriez la fonction déployée. Pour plus d’informations, consultez [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md). Lors de l’exécution en mode débogage, les points d’arrêt sont atteints dans Visual Studio comme prévu. 

Pour obtenir un exemple montrant comment tester une fonction déclenchée par une file d’attente, consultez le [didacticiel de démarrage rapide sur une fonction déclenchée par une file d’attente](functions-create-storage-queue-triggered-function.md#test-the-function).  

Pour en savoir plus sur l’utilisation d’Azure Functions Core Tools, consultez [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md).

## <a name="publish-to-azure"></a>Publication dans Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Functions Tools, consultez la section Common Questions (Questions courantes) de l’article de blog [Visual Studio 2017 Tools for Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) (Visual Studio 2017 Tools pour Azure Functions).

Pour en savoir plus sur Azure Functions Core Tools, consultez [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md).
