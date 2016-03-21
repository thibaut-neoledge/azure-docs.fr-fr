<properties
	pageTitle="Ajouter l’API Excel à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API Excel dans l’environnement App Service Environment de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Créer une API Excel dans PowerApps Enterprise

Ajoutez l’API Excel à l’environnement App Service (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][4]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][5]

4. Dans **PowerApps**, sélectionnez **Gérer les API** : ![Accès aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Tapez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour voir les API prédéfinies, puis sélectionnez **Excel** : ![sélection d’api Excel][3]

8. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API Excel est ajoutée à votre environnement App Service Environment.

## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Excel à PowerApps Enterprise. Ensuite, donnez accès à l’API aux utilisateurs afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)



<!--References-->
[1]: ./media/powerapps-create-api-excel/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-excel/add-api.PNG
[3]: ./media/powerapps-create-api-excel/select-excel-api.PNG
[4]: ./media/powerapps-create-api-excel/browseall.png
[5]: ./media/powerapps-create-api-excel/allresources.png

<!------HONumber=AcomDC_0309_2016-->