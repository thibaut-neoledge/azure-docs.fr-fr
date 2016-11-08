---
title: Utiliser Azure Functions pour effectuer une tâche de nettoyage planifiée | Microsoft Docs
description: Utilisez Azure Functions pour créer une fonction C# qui s’exécute en fonction d’un compteur d’événements.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''

ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2016
ms.author: glenga

---
# <a name="use-azure-functions-to-perform-a-scheduled-clean-up-task"></a>Utiliser Azure Functions pour effectuer une tâche de nettoyage planifiée
Cette rubrique vous montre comment utiliser Azure Functions pour créer une fonction en C#, qui s’exécute en fonction d’un compteur d’événements afin de nettoyer les lignes d’une table de base de données. La nouvelle fonction est créée selon un modèle prédéfini dans le portail Azure Functions. Pour prendre en charge ce scénario, vous devez également définir une chaîne de connexion de base de données comme paramètre App Service dans le conteneur de fonctions. 

## <a name="prerequisites"></a>Composants requis
Pour créer une fonction, vous devez avoir un compte Azure actif. Si tel n’est pas le cas, des [comptes gratuits sont disponibles](https://azure.microsoft.com/free/).

Cette rubrique montre une commande Transact-SQL qui exécute une opération de nettoyage en bloc dans une table nommée *TodoItems* d’une base de données SQL Database. Cette même table TodoItems est créée lorsque vous suivez le [Didacticiel de démarrage rapide Azure App Service Mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started.md). Vous pouvez également utiliser une base de données d’exemple. Si vous choisissez d’utiliser une autre table, vous devez modifier la commande.

Vous pouvez obtenir la chaîne de connexion utilisée par un serveur principal Mobile App dans le portail sous **Tous les paramètres** > **Paramètres de l’application** > **Chaînes de connexion** > **Afficher les valeurs des chaînes de connexion** > **MS_TableConnectionString**. Vous pouvez également obtenir la chaîne de connexion directement à partir d’une base de données SQL dans le portail sous **Tous les paramètres** > **Propriétés** > **Afficher les chaînes de connexion de la base de données** > **ADO.NET (authentification SQL)**.

Ce scénario utilise une opération en bloc sur la base de données. Pour que votre fonction traite des opérations CRUD dans une table Mobile Apps, utilisez plutôt une liaison Mobile Apps.

## <a name="set-a-sql-database-connection-string-in-the-function-app"></a>Définir une chaîne de connexion de base de données SQL dans le conteneur de fonctions
Une application de fonction héberge l’exécution de vos fonctions dans Azure. Il est recommandé de stocker les chaînes de connexion et autres secrets dans vos paramètres de conteneur de fonctions. Cela empêche les divulgations accidentelles lorsque votre code de fonction finit dans un référentiel quelque part. 

1. Accédez au [Portail Azure Functions](https://functions.azure.com/signin) et connectez-vous avec votre compte Azure.
2. Si vous avez une application de fonction existante à utiliser, sélectionnez-la à partir de vos **applications de fonction**, puis cliquez sur **Ouvrir**. Pour créer un nouveau conteneur de fonctions, dans **Nom**, tapez le nom unique de votre nouveau conteneur de fonctions ou acceptez le nom généré, sélectionnez votre **Région**, puis cliquez sur **Créer + Prise en main**. 
3. Dans votre conteneur de fonctions, cliquez sur les **Paramètres Function App** > **Accéder aux paramètres App Service**. 
   
    ![Paramètres Function App](./media/functions-create-an-event-processing-function/functions-app-service-settings.png)
4. Dans votre conteneur de fonctions, cliquez sur **Tous les paramètres**, accédez aux **Paramètres de l’application**, puis, sous **Chaînes de connexion**, tapez `sqldb_connection` pour **Nom**, collez la chaîne de connexion dans **Valeur**, cliquez sur **Enregistrer**, puis fermez le panneau du conteneur de fonctions pour retourner au portail Functions.
   
    ![Chaîne de connexion de paramètre App Service](./media/functions-create-an-event-processing-function/functions-app-service-settings-connection-strings.png)

À présent, vous pouvez ajouter le code de fonction C# qui se connecte à votre base de données SQL.

## <a name="create-a-timer-triggered-function-from-the-template"></a>Créer une fonction déclenchée par un minuteur à partir du modèle
1. Dans votre conteneur de fonctions, cliquez sur **+ Nouvelle fonction** > **TimerTrigger - C#** > **Créer**. Cela crée une fonction avec un nom par défaut, qui s’exécute selon la planification par défaut, c’est-à-dire une fois par minute. 
   
    ![Créer une nouvelle fonction déclenchée par un minuteur](./media/functions-create-an-event-processing-function/functions-create-new-timer-trigger.png)
2. Dans le volet **Code** de l’onglet **Développer**, ajoutez les références d’assembly suivantes en haut du code actuel de la fonction :
   
        #r "System.Configuration"
        #r "System.Data"
3. Ajoutez les instructions `using` suivantes à la fonction :
   
        using System.Configuration;
        using System.Data.SqlClient;
        using System.Threading.Tasks; 
4. Remplacez la fonction **Run** existante par le code suivant :
   
        public static async Task Run(TimerInfo myTimer, TraceWriter log)
        {
            var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
            using (SqlConnection conn = new SqlConnection(str))
            {
                conn.Open();
                var text = "DELETE from dbo.TodoItems WHERE Complete='True'";
                using (SqlCommand cmd = new SqlCommand(text, conn))
                {
                    // Execute the command and log the # rows deleted.
                    var rows = await cmd.ExecuteNonQueryAsync();
                    log.Info($"{rows} rows were deleted");
                }
            }
        }
5. Cliquez sur **Enregistrer**, surveillez la prochaine exécution de la fonction dans les fenêtres **Journaux**, puis notez le nombre de lignes supprimées de la table TodoItems.
6. (Facultatif) À l’aide de [l’application de démarrage rapide Mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started.md), marquez les éléments supplémentaires comme « terminés », puis revenez à la fenêtre **Journaux** et vérifiez que le même nombre de lignes est supprimé par la fonction pendant l’exécution suivante. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Functions, consultez ces rubriques.

* [Référence du développeur Azure Functions](functions-reference.md)  
  Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.
* [Test d’Azure Functions](functions-test-a-function.md)  
  décrit plusieurs outils et techniques permettant de tester vos fonctions.
* [Comment mettre à l’échelle Azure Functions](functions-scale.md)  
  présente les plans de services disponibles dans Azure Functions, dont le plan de service dynamique, et explique comment choisir le plan adapté à vos besoins.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]

<!--HONumber=Oct16_HO2-->


