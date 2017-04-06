---
title: "Prise en main de l’API REST de gestion de la Recherche Azure | Microsoft Docs"
description: "Gérez votre service cloud Azure Search hébergé à l&quot;aide d&quot;une API REST de gestion"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: cd4c41d8-81bd-4609-9a37-e112ddf1f21f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 4fe0db2a5fb9b7798d8583721ac35b2e02435d7d
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-with-azure-search-management-rest-api"></a>Prise en main de l'API REST de gestion Azure Search
> [!div class="op_single_selector"]
> * [Portail](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [API REST](search-get-started-management-api.md)
>
>

L'API REST de gestion Azure Search est une autre solution de programmation permettant d'effectuer des tâches d'administration dans le portail. Les opérations de gestion de service incluent la création ou la suppression du service, la mise à l'échelle du service et la gestion des clés. Ce didacticiel est fourni avec un exemple d'application cliente qui montre l'API de gestion des services. Il inclut également les étapes de configuration nécessaires pour exécuter l'exemple dans votre environnement de développement local.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2012 ou 2013
* Téléchargement de l’exemple d’application cliente

Avec ce didacticiel, vous allez approvisionner deux services : Azure Search et Azure Active Directory (AD). De plus, vous allez créer une application AD qui établit la relation d'approbation entre votre application cliente et le point de terminaison du gestionnaire de ressources dans Azure.

Pour suivre ce didacticiel, vous aurez besoin d'un compte Azure.

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application
Ce didacticiel est basé sur une application console Windows écrite en C#, que vous pouvez modifier et exécuter dans Visual Studio 2012 ou 2013

Vous pouvez trouver l’application cliente sur GitHub à la page [Démonstration de l’API de gestion .NET Azure Search](https://github.com/Azure-Samples/search-dotnet-management-api/).

## <a name="configure-the-application"></a>Configuration de l'application
Avant de pouvoir exécuter l'exemple d'application, vous devez activer l'authentification pour que les demandes envoyées de l'application cliente vers le point de terminaison du gestionnaire de ressources puissent être acceptées. Cette nécessité d’authentification provient [d’Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx), qui est au centre de toutes les opérations liées au portail demandées par le biais d’une API, y compris celles qui sont associées à la gestion du service de Recherche. L'API de gestion des services pour Azure Search est simplement une extension du gestionnaire de ressources Azure. Elle hérite donc de ses dépendances.  

Le gestionnaire de ressources Azure nécessite le service Azure Active Directory en tant que fournisseur d'identité.

Pour obtenir un jeton d'accès qui permet aux demandes d'atteindre le gestionnaire de ressources, l'application cliente inclut un segment de code qui appelle Active Directory. Le segment de code et les étapes préalables à son utilisation proviennent de cet article : [Demandes d'authentification Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Vous pouvez suivre les instructions fournies dans le lien ci-dessus ou les étapes de ce document si vous préférez parcourir le didacticiel étape par étape.

Dans cette section, vous allez effectuer les tâches suivantes :

1. Création d'un service Active Directory
2. Création d'une application Active Directory
3. Configuration de l'application AD en enregistrant des informations détaillées sur l'exemple d'application cliente téléchargé
4. Téléchargement de l'exemple d'application cliente avec les valeurs qu'il utilisera pour obtenir l'autorisation pour ses demandes

> [!NOTE]
> Ces liens fournissent le contexte d’utilisation d’Azure Active Directory pour l’authentification des demandes du client au gestionnaire de ressources : [Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790568.aspx), [Demandes d’authentification Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx) et [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).
>
>

### <a name="create-an-active-directory-service"></a>Création d'un service Active Directory
1. Connectez-vous au [portail Azure](https://manage.windowsazure.com).
2. Faites défiler le volet de navigation gauche, puis cliquez sur **Active Directory**.
3. Cliquez sur **NOUVEAU** pour ouvrir **App Services** | **Active Directory**. Dans cette étape, vous créez un service Active Directory. Celui-ci va héberger l'application Active Directory que vous allez définir dans quelques étapes. La création d'un service permet d'isoler le didacticiel d'autres applications qui sont déjà hébergées dans Azure.
4. Cliquez sur **Répertoire** | **Création personnalisée**.
5. Entrez le nom du service, le domaine et l’emplacement géographique. Le domaine doit être unique. Cochez la case pour créer le service.

### <a name="create-a-new-ad-application-for-this-service"></a>Création d'une application Active Directory pour ce service
1. Sélectionnez le service Active Directory « SearchTutorial » que vous venez de créer.
2. Dans le menu supérieur, cliquez sur **Applications**.
3. Cliquez sur **Ajouter une application**. Une application Active Directory stocke des informations sur les applications clientes qui l'utiliseront comme fournisseur d'identité.  
4. Choisissez **Ajouter une application développée par mon organisation**. Cette option fournit les paramètres d'inscription pour les applications qui ne sont pas publiées dans la galerie d'applications. L'application cliente ne faisant pas partie de la galerie d'applications, ceci est le bon choix pour ce didacticiel.
5. Entrez un nom, comme « Azure-Search-Manager ».
6. Choisissez **Application cliente native** comme type d’application. Cette valeur correspond à l'exemple d'application. Il s'agit d'une application cliente (console) Windows, pas une application web.
7. Dans l'URI de redirection, entrez « http://localhost/Azure-Search-Manager-App ». C'est l'URI vers lequel Azure Active Directory va rediriger l'agent utilisateur en réponse à une demande d'autorisation OAuth 2.0. La valeur n’a pas besoin d’être un point de terminaison physique, mais doit être un URI valide.

    Dans ce didacticiel, la valeur peut être quelconque, mais la valeur que vous entrez devient une entrée obligatoire pour la connexion d'administration dans l'exemple d'application.
8. Cochez la case pour créer l'application Active Directory. Vous devez voir « Azure-Search-Manager-App » dans le volet de navigation gauche.

### <a name="configure-the-ad-application"></a>Configuration de l'application Active Directory
1. Cliquez sur l'application Active Directory, « Azure-Search-Manager-App », que vous venez de créer. Elle doit être affichée dans le volet de navigation gauche.
2. Cliquez sur **Configurer** dans le menu supérieur.
3. Faites défiler jusqu'à Autorisations et sélectionnez **API de gestion Microsoft Azure**. Dans cette étape, vous spécifiez l'API (dans ce cas, l'API du gestionnaire de ressources Azure) à laquelle l'application cliente doit accéder, ainsi que le niveau d'accès nécessaire.
4. Dans Autorisations déléguées, cliquez sur la liste déroulante et sélectionnez **Accéder à la gestion du service Azure (version préliminaire)**.
5. Enregistrez les modifications.

Ne fermez pas la page de configuration d'application. Dans l'étape suivante, vous allez copier les valeurs à partir de cette page pour les entrer dans l'exemple d'application.

### <a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>Chargement de l’exemple de programme d’application avec des valeurs d’inscription et d’abonnement
Dans cette section, vous allez modifier la solution dans Visual Studio, en remplaçant les valeurs valides obtenues dans le portail.
Les valeurs que vous ajoutez apparaissent en haut du fichier Program.cs :

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Si vous n’avez pas encore [téléchargé l’exemple d’application à partir de GitHub](https://github.com/Azure-Samples/search-dotnet-management-api/), vous en aurez besoin pour cette étape.

1. Ouvrez **ManagementAPI.sln** dans Visual Studio.
2. Ouvrez le fichier Program.cs.
3. Spécifiez `ClientId`. À partir de la page de configuration de l'application AD ouverte depuis l'étape précédente, copiez l'ID client de la page de configuration d'application AD du portail et collez-le dans Program.cs.
4. Spécifiez `RedirectUrl`. Copiez l'URI de redirection de la même page de portail et collez-le dans le fichier Program.cs.
5. Spécifiez `TenantID.`

   * Revenez à Active Directory | SearchTutorial (service).
   * Cliquez sur **Applications** dans la barre supérieure.
   * Cliquez sur **Afficher les points de terminaison** au bas de la page.
   * Copiez le point de terminaison d'autorisation OAUTH 2.0 au bas de la liste.
   * Collez le point de terminaison dans TenantID, en ajustant la valeur de tous les paramètres d'URI à l'exception de l'ID de client.

     Étant donné « https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0 », supprimez tout sauf « 55e324c7-1656-4afe-8dc3-43efcd4ffa50 ».

6. Spécifiez `SubscriptionID`.

   * Accédez à la page principale du portail.
   * Cliquez sur **Paramètres** au bas du volet de navigation gauche.
   * Sous l’onglet Abonnements, copiez l’ID d’abonnement et collez-le dans Program.cs.
7. Enregistrez et générez la solution.

## <a name="explore-the-application"></a>Exploration de l'application
Ajoutez un point d'arrêt au premier appel de méthode pour parcourir le programme. Appuyez sur **F5** pour exécuter l’application et sur **F11** pour parcourir le code.

L’exemple d’application crée un service Azure Search gratuit pour un abonnement Azure existant. Si un service gratuit existe déjà pour votre abonnement, l’exemple d’application échoue. Un seul service Search est autorisé par abonnement.

1. Ouvrez Program.cs à partir de l’Explorateur de solutions et accédez à la fonction Main(string[] void).
2. Notez **qu’ExecuteArmRequest** est utilisé pour exécuter des demandes sur le point de terminaison Azure Resource Manager, `https://management.azure.com/subscriptions` pour un `subscriptionID` spécifié. Cette méthode est utilisée dans tout le programme pour effectuer des opérations à l'aide de l'API du gestionnaire de ressources Azure ou de l'API de gestion Search.
3. Les demandes effectuées au gestionnaire de ressources Azure doivent être authentifiées et autorisées, par le biais de la méthode **GetAuthorizationHeader**, appelée par la méthode **ExecuteArmRequest**, empruntée à [l’authentification des demandes Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx). Notez que **GetAuthorizationHeader** appelle `https://management.core.windows.net` pour obtenir un jeton d’accès.
4. Vous êtes invité à vous connecter avec un nom d’utilisateur et un mot de passe valides pour votre abonnement.
5. Ensuite, un nouveau service Azure Search est inscrit auprès du fournisseur du gestionnaire de ressources Azure. Là encore, c’est la méthode **ExecuteArmRequest**, utilisée cette fois pour créer le service de Recherche sur Azure pour votre abonnement avec `providers/Microsoft.Search/register`.
6. Le reste du programme utilise l' [API REST de gestion Azure Search](http://msdn.microsoft.com/library/dn832684.aspx). Notez que l' `api-version` de cette API est différent de l'« api-version » du gestionnaire de ressources Azure. Par exemple, `/listAdminKeys?api-version=2014-07-31-Preview` affiche l’`api-version` de l’API REST de gestion d’Azure Search.

    La prochaine série d’opérations récupère la définition de service que vous venez de créer, les api-keys d’administration, régénère et récupère les clés, modifie le réplica et la partition, et enfin supprime le service.

    Quand vous modifiez le nombre de réplicas ou de partitions de service, cette action échoue si vous utilisez l'édition gratuite. Seule l'édition standard peut utiliser des partitions et des réplicas supplémentaires.

    La suppression du service est la dernière opération.

## <a name="next-steps"></a>Étapes suivantes
Après avoir terminé ce didacticiel, vous souhaiterez peut-être en savoir plus sur la gestion des services ou l'authentification avec le service Active Directory :

* Découvrez plus en détail l'intégration d'une application cliente avec Active Directory. Consultez la rubrique [Intégration d'applications dans Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
* Découvrez les autres opérations de gestion des services dans Azure. Consultez la rubrique [Gestion de vos services](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

