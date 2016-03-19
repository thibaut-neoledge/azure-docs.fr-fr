<properties
	pageTitle="Ajouter l’API Microsoft Translator à PowerApps Enterprise | Microsoft Azure"
	description="Création ou configuration d’une API Microsoft Translator dans l’environnement de service d’application de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Création d’une API Microsoft Translator dans l’environnement de service d’application de votre organisation

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Sélectionnez **Parcourir** dans la barre des tâches : ![][7]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][8]

4. Dans **PowerApps**, sélectionnez **Gérer les API** : ![Accédez aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Entrez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **Microsoft Translator** : ![Sélectionnez l’API Microsoft Translator][3]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** : ![Configurez les paramètres de l'API Microsoft Translator][4]

9. Entrez l’*ID client* et la *clé secrète client* de votre application Microsoft Translator. Si vous n’en avez pas, consultez la section « Enregistrer une application Microsoft Translator pour une utilisation avec PowerApps » dans cette rubrique pour savoir comment les créer.

9. Sélectionnez **OK** pour terminer les étapes.

Lorsque vous avez terminé, une nouvelle API Microsoft Translator est ajoutée à votre environnement App Service.


## Facultatif : inscription d’une application Microsoft Translator pour une utilisation avec PowerApps

Si vous ne disposez pas d’une application Microsoft Translator avec les valeurs clé et question secrète, procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.


1. Accédez à [page de développeur Azure Data Market][5] et connectez-vous avec votre compte Microsoft. 

2. Sélectionnez **Inscription**.

3. Dans **Inscrire votre application** :

	a) Saisissez une valeur pour **ID Client**. b) Saisissez le **nom** de votre application. c) Saisissez une valeur factice pour l’**URL de redirection**. Par exemple, entrez **https://contosoredirecturl*. d) Saisissez une **description**. e) Cliquez sur **Créer**.

	![Inscrivez votre application][6]

Une application Microsoft Translator est créée. Vous pouvez utiliser cette application dans la configuration de votre API Microsoft Translator dans le portail Azure.


## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Microsoft Translator à PowersApps Enterprise. Vous verrez ensuite comment accorder aux utilisateurs l’accès à l’API pour qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribution d’accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_1203_2015-->