---
title: "Instructions de gestion des prospects pour une table Azure sur la Place de marché Microsoft Azure | Microsoft Docs"
description: "Cet article guide les éditeurs, étape par étape, dans la configuration de leur gestion des prospects avec un stockage de table azure."
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 45aa2e0b178969215df9ae305d0a13a350ae0fda
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-azure-table"></a>Instructions de gestion des prospects pour une table Azure

Ce document contient des instructions pour la configuration de votre table Azure afin que Microsoft puisse vous fournir des prospects. Une table Azure est le meilleur choix par défaut si vous souhaitez personnaliser la manière dont vous stockez vos prospects.

1. Si vous n’avez pas de compte Azure, commencez par en [créer un](https://azure.microsoft.com/pricing/free-trial/).

2. Une fois votre compte Azure prêt, connectez-vous au [Portail Azure](https://portal.azure.com) et créez un compte de stockage. Regardez la capture d’écran ci-dessous pour obtenir des instructions, et cliquez ici pour [en savoir plus sur la tarification du stockage](https://azure.microsoft.com/pricing/details/storage/). <br/>
  ![Image de flux de création d’un stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

3. Ensuite, copiez la chaîne de connexion pour la clé, puis collez-la dans le champ <b>Chaîne de connexion de compte de stockage</b> sur le portail Microsoft Cloud Partner. <br/> 
  ![Image de clé de stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

  Exemple de chaîne de connexion finale : <br/>
`{"connectionString":"DefaultEndpointsProtocol=https;AccountName=leadaccount;AccountKey=ObS0EW7tDmXrC8oNeG6IRHpx2IUioBQTQynQcR/MUMqrNqQ/RC6zctP8HfucNJO+ond7dJHTROO9ziiPNspjEg=="}`

Vous pouvez utiliser l’[Explorateur de stockage Microsoft Azure](http://azurestorageexplorer.codeplex.com/) (application tierce) ou tout autre outil pour afficher ou exporter les données de votre table de stockage.

### <a name="optional-azure-functions--azure-table"></a>**(Facultatif)**  Fonctions Azure + Table Azure
Si vous souhaitez personnaliser la façon dont vous recevez ces prospects, le service [Azure Functions](https://azure.microsoft.com/services/functions/) offre un maximum de souplesse pour automatiser votre processus de génération de prospects. Voici un exemple montrant comment créer une fonction Azure avec un minuteur qui s’exécute toutes les cinq minutes, recherche de nouveaux enregistrements dans une table Azure et utilise le niveau gratuit du service SendGrid pour envoyer une simple notification par e-mail.

1. [Créez](https://portal.azure.com/#create/SendGrid.SendGrid) un compte gratuit du service SendGrid dans votre abonnement Azure.
  
    ![Créer SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)
  
2. Créez une clé API SendGrid (cliquez sur Gérer la clé pour accéder à l’interface utilisateur de SendGrid, cliquez sur Paramètres, Clés API, créez une clé avec Envoi de courrier -> Accès total, puis enregistrez la clé API).
 
    ![Clé API SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)

3. [Créez](https://portal.azure.com/#create/Microsoft.FunctionApp) une application de fonction Azure à l’aide de l’option Plan d’hébergement nommée « Plan Consommation ». 
  
    ![Créer une fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)

4. Créez une définition de fonction. 
  
    ![Création d’une définition de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)

5. Si vous souhaitez que la fonction envoie une mise à jour à une heure spécifique, sélectionnez ici TimerTrigger-CSharp comme option de démarrage.
  
    ![Option de déclencheur de temps de la fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)
  
6. Remplacez le code de « Develop » par celui de la table ci-dessous. Vous pouvez modifier les adresses de messagerie pour que celles-ci correspondent à ce que vous souhaitez pour l’expéditeur et le destinataire.
Il s’agit d’un simple exemple de code que vous pouvez modifier pour l’améliorer si nécessaire.
  
        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;

        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }

        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";

            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();

            // Check how many rows were added
            int rowsCount = rowsList.Count;
            
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");

                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };

                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);

                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }
   ![Extrait de code de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)

7. Cliquez sur « Intégrer » et « Entrées » pour définir la connexion de la table Azure :
  
    ![Intégration de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)

8. Entrez le nom de la table et définissez la chaîne de connexion en cliquant sur « Nouvelle ».
  
    ![Connexion de table de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9. Définissez à présent la sortie en tant que SendGrid et conservez toutes les valeurs par défaut.
  
    ![Sortie de SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)
    ![Valeurs de sortie par défaut de SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Ajouter une clé API SendGrid aux paramètres d’application de fonction à l’aide du nom « SendGridApiKey » et de la valeur obtenue des clés API dans l’interface utilisateur de SendGrid
  
    ![Gérer SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid managekey](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Une fois tout cela configuré, la section Integrate doit contenir un code tel que celui-ci :

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }
  
Enfin, vous devez revenir à l’interface utilisateur Develop de la fonction et cliquer sur **Exécuter** pour démarrer la minuterie. Vous êtes à présent configuré pour recevoir des notifications chaque fois qu’un nouveau prospect arrive.

