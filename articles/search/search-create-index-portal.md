<properties 
	pageTitle="Création d'un index Azure Search dans le portail" 
	description="Ajout d'un index au service Azure Search en renseignant les définitions de champ du portail de gestion" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Création d'un index Azure Search dans le portail

Dans Azure Search, vous pouvez rapidement créer un prototype d'index dans le portail de gestion Azure. Le portail est idéal pour les tests de validation technique, mais il sert également à afficher les définitions de schéma et l'utilisation des ressources pour tout index déployé sur votre service.

Pour effectuer cette tâche, assurez-vous que vous disposez d'un service Azure Search opérationnel. Consultez [Créer un service Azure Search dans le portail](search-create-service-portal.md) si vous avez besoin d'aide pour la configuration.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur le tableau de bord de votre service Azure Search. Voici quelques façons d'afficher le tableau de bord.
	- Dans la barre de lancement, cliquez sur **Accueil**. La page d'accueil comporte des vignettes pour chaque service de votre abonnement. Cliquez sur la vignette pour ouvrir le tableau de bord des services.
	- Dans la barre de lancement, cliquez sur **Parcourir** | **Filtrer par** | **Rechercher des services** pour trouver votre service de recherche dans la liste. 

3. Une barre de commande apparaît en haut du tableau de bord des services, avec notamment l'option **Ajouter un index**.
	
	Vérifiez le niveau de tarification. Si vous utilisez la version gratuite, vous pouvez créer jusqu'à 3 index. Vous devrez peut-être en supprimer un pour libérer de l'espace.

     ![][1]

4. Pour supprimer un index, cliquez dessus pour ouvrir un panneau. Cliquez sur **Supprimer**.

     ![][2]

5. Pour créer un nouvel index dans le portail, cliquez sur **Ajouter un index** et nommez-le par exemple *hôtels*.

	La création de l'index peut prendre une minute, mais une fois prêt à être utilisé, l'index apparaîtra dans la liste Index.

6. Cliquez sur *hôtels* pour ouvrir le panneau de définition d'index.

	Lorsque vous créez un index dans le portail, un champ requis (id) est créé pour vous. Il s'agit du champ clé permettant d'identifier de façon unique chaque document. Il n'existe qu'un seul champ par clé (aucune clé composite), et il s'agit toujours d'une chaîne.

	Si vous souhaitez renommer le champ clé, il est important d'effectuer cette étape maintenant, lors de la création de l'index. Vous ne pourrez plus renommer le champ après la création de l'index.

	![][3]

7. Pour modifier le nom du champ, cliquez sur la flèche droite dans la liste des champs.

8. Remplacez *id* par *hotelId*.

9. Cliquez sur **OK** dans chaque panneau (champs et index) pour créer l'index.

##Ajouter des champs

Dans Azure Search, les attributs d'index comme ceux pouvant faire l'objet de recherches, de facettes et de tris sont activés par défaut. En règle générale, lorsque vous définissez ces attributs, ils servent à empêcher des opérations de recherche illogiques (par exemple, les tris ou les facettes sur une description).

Le portail est différent. Dans le portail, les opérations de recherche sont désactivées par défaut pour vous permettre de sélectionner toutes les opérations qui s'appliquent, champ par champ.

1. Cliquez sur **Ajouter/modifier des champs** pour ajouter des champs. Dans cet exercice, nous allons recréer l'index *hôtels* mentionné dans l'article [Utilisation de Fiddler avec Azure Search](search-fiddler.md). 

	![][4]

2. Ajoutez et configurez des champs pour finaliser le schéma.

	![][5]

	Consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) et [Types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx) pour obtenir des informations de référence sur les noms et les types.

3. Cliquez sur **Enregistrer** en haut de la page.

  	![][6]

##Étapes suivantes

Bien que l'index est défini, il ne pourra être utilisé que si vous chargez des documents. Pour faciliter cette opération, consultez [Utilisation de Fiddler avec Azure Search](search-fiddler.md), en particulier la section **Chargement de documents**. Vous pouvez ensuite effectuer les étapes restantes de cet article pour exécuter quelques requêtes.

Une fois que vous êtes familiarisé avec l'index de base, vous pouvez ajouter un analyseur de langage ou un générateur de suggestions pour intégrer un support multilingue ou des suggestions prédictives. Ces deux fonctionnalités sont spécifiées dans le schéma d'index. Consultez [Support multilingue](https://msdn.microsoft.com/elibrary/azure/dn879793.aspx) et [Création d'un index](https://msdn.microsoft.com/library/azure/dn798941.aspx) pour plus d'informations.

<!--Image references-->
[1]: ./media/search-create-index-portal/AzureSearch-PortalIndex-1.PNG
[2]: ./media/search-create-index-portal/AzureSearch-PortalIndex-2.PNG
[3]: ./media/search-create-index-portal/AzureSearch-PortalIndex-3.PNG
[4]: ./media/search-create-index-portal/AzureSearch-PortalIndex-4.PNG
[5]: ./media/search-create-index-portal/AzureSearch-PortalIndex-5.PNG
[6]: ./media/search-create-index-portal/AzureSearch-PortalIndex-6.PNG
<!--HONumber=54--> 