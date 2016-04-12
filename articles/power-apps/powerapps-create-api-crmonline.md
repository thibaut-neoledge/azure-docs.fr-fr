<properties
	pageTitle="Ajouter l’API Dynamics CRM Online à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API Dynamics CRM Online dans l’environnement App Service Environment de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/29/2016"
   ms.author="sameerch"/>

# Créer une API Dynamics CRM Online dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-crmonline.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-crmonline.md)

Ajoutez l’API Dynamics CRM Online à l’environnement App Service (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.

2. Dans la barre des tâches, sélectionnez **Parcourir** :  
![][1]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* :  
![][2]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** : 
![Accès aux API inscrites][3]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API :  
![Add API][4]

6. Tapez un **nom** descriptif pour votre API.

7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **Dynamics CRM Online** :  
![Sélection d’API Dynamics CRM Online][5]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** :  
![Configuration des paramètres de l’API Dynamics CRM Online][6]

9. Entrez l’**ID client** et la **Clé d’application** de votre application Azure Active Directory (AAD) Dynamics CRM Online. Si vous n’avez pas d’ID et de clé secrète, consultez la section « Inscrire une application AAD pour une utilisation avec PowerApps » dans cette rubrique pour savoir comment les créer.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.

Lorsque vous avez terminé, une nouvelle API Dynamics CRM Online est ajoutée à votre environnement App Service Environment.

## Inscrire une application AAD pour une utilisation avec l’API Dynamics CRM Online PowerApps

1. Ouvrez le [portail Azure](https://portal.azure.com).

2. Sélectionnez **PARCOURIR**, puis **ACTIVE DIRECTORY** :

	> [AZURE.NOTE] Active Directory s’ouvre dans le portail Azure Classic.

3. Sélectionnez le nom de client de votre organisation :  
![Démarrage d’Azure Active Directory][7]

4. Sélectionnez l’onglet **Applications**, puis sélectionnez **Ajouter** : 
	![Applications clientes AAD][8]

5. Dans **AJOUTER UNE APPLICATION** :

	1. Entrez un **Nom** pour votre application.  
	2. Laissez le type d’application sur **Web**.  
	3. Sélectionnez **Suivant**.

	![Ajouter une application AAD – Informations sur l’application][9]

6. Dans **Propriétés de l’application** :

	1. Entrez l’**URL de connexion** de votre application. Étant donné que vous allez vous authentifier dans AAD pour PowerApps, définissez l’URL de connexion sur \__https://login.windows.net_.
2. Entrez un **URI ID d’application** valide pour votre application.  
	3. Sélectionnez **OK**.  

	![Ajouter une application AAD – Propriétés de l’application][10]

7. Une fois l’opération réussie, vous êtes redirigé vers la nouvelle application AAD. Sélectionnez **Configurer** :  
![Application AAD Contoso][11]

8. Définissez l’**URL de réponse** située sous la section _OAuth 2_ sur l’URL de redirection que vous avez reçue quand vous avez ajouté la nouvelle API Dynamics CRM Online dans le portail Azure (dans cette rubrique) :  
![Configuration de l’application AAD Contoso][12]

9. Sélectionnez **Enregistrer**.

Une nouvelle application Azure Active Directory est créée. Vous pouvez utiliser cette application dans la configuration de votre API Dynamics CRM Online dans le portail Azure.

## Consulter les API REST

Informations de référence sur l’[API REST Dynamics CRM Online](../connectors/connectors-create-api-crmonline.md).


## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Dynamics CRM Online à PowersApps Enterprise. Ensuite, donnez accès à l’API aux utilisateurs afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0330_2016-->