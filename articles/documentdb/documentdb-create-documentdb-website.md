<properties 
    pageTitle="Déploiement de DocumentDB et d’Azure App Service Web Apps avec un modèle Azure Resource Manager | Microsoft Azure" 
    description="Découvrez comment déployer un compte DocumentDB, Azure App Service Web Apps et un exemple d’application web avec un modèle Azure Resource Manager." 
    services="documentdb, app-service\web" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/02/2016" 
    ms.author="hawong"/>


# <a name="deploy-documentdb-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Déploiement de DocumentDB et d’Azure App Service Web Apps avec un modèle Azure Resource Manager

Ce didacticiel vous montre comment utiliser un modèle Azure Resource Manager pour déployer et intégrer [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/), une application web [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) et un exemple d’application web.

À l’aide de modèles Azure Resource Manager, vous pouvez facilement automatiser le déploiement et la configuration de vos ressources Azure.  Ce didacticiel montre comment déployer une application web et configurer automatiquement les informations de connexion du compte DocumentDB.

Après avoir terminé ce didacticiel, vous serez en mesure de répondre aux questions suivantes :  

-   Comment puis-je utiliser un modèle Azure Resource Manager pour déployer et intégrer un compte DocumentDB et une application web dans Azure App Service ?
-   Comment puis-je utiliser un modèle Azure Resource Manager pour déployer et intégrer un compte DocumentDB, une application web dans App Service Web Apps et une application Webdeploy ?

<a id="Prerequisites"></a>
## <a name="prerequisites"></a>Composants requis
> [AZURE.TIP] Ce didacticiel ne suppose pas d'être familiarisé avec les modèles Azure Resource Manager ou JSON, sauf si vous envisagez de modifier les modèles référencés ou les options de déploiement.

Avant de suivre les instructions de ce didacticiel, assurez-vous de disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement.  Pour plus d'informations sur l'obtention d'un abonnement, consultez les pages [Modes d’achat d’Azure](https://azure.microsoft.com/pricing/purchase-options/), [Offres spéciales membres](https://azure.microsoft.com/pricing/member-offers/) ou [Version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

##<a name="<a-id="createdb"></a>step-1:-download-the-template-files"></a><a id="CreateDB"></a>Étape 1 : téléchargement des fichiers de modèle ##
Commençons par télécharger les fichiers de modèle que nous utiliserons dans ce didacticiel.

1. Téléchargez le modèle [Exemple de création d’un compte DocumentDB, de Web Apps et de déploiement d’une application de démonstration](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) dans un dossier local (par exemple, C:\DocumentDBTemplates). Ce modèle déploie un compte DocumentDB, une application web App Service et une application web.  Il configure également automatiquement l'application web pour se connecter au compte DocumentDB.

2. Téléchargez le modèle [Exemple de création d’un compte DocumentDB et de Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) dans un dossier local (par exemple, C:\DocumentDBTemplates). Ce modèle déploie un compte DocumentDB et une application web App Service, ainsi qu’il modifie les paramètres de l’application du site pour faire facilement apparaître les informations de connexion DocumentDB. Toutefois, il n’inclut pas d’application web.  

<a id="Build"></a>
##<a name="step-2:-deploy-the-documentdb-account,-app-service-web-app-and-demo-application-sample"></a>Étape 2 : déploiement de l’exemple de compte DocumentDB, d’une application web App Service et d’une application de démonstration

Maintenant, nous allons déployer notre premier modèle.

> [AZURE.TIP] Ce modèle ne valide pas que le nom de l’application web et le nom du compte DocumentDB entrés ci-dessous sont a) corrects et b) disponibles.  Il est vivement recommandé de vérifier la disponibilité des noms que vous souhaitez fournir avant de soumettre le déploiement.

1. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur Nouveau et recherchez « Déploiement de modèle ».
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)

2. Sélectionnez l’élément Déploiement de modèle et cliquez sur **Créer**
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)

3.  Cliquez sur **Modifier le modèle**, collez le contenu du fichier de modèle DocDBWebsiteTodo.json, puis cliquez sur **Enregistrer**.
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)

4. Cliquez sur **Modifier les paramètres**, entrez des valeurs pour chacun des paramètres obligatoires, puis cliquez sur **OK**.  Les paramètres sont les suivants :

    1. SITENAME : spécifie le nom de l’application web App Service utilisée pour former l’URL d’accès à l’application web (par exemple, si vous spécifiez « mydemodocdbwebsite », l’URL par laquelle vous accéderez à l’application web sera mydemodocdbwebsite.azurewebsites.net).

    2. HOSTINGPLANNAME : spécifie le nom du plan d’hébergement App Service à créer.

    3. LOCATION : spécifie l’emplacement Azure dans lequel créer les ressources DocumentDB et d’application web.

    4. DATABASEACCOUNTNAME : spécifie le nom du compte DocumentDB à créer.   

    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)

5. Choisissez un groupe de ressources existant ou fournissez un nom afin de créer un nouveau groupe de ressources et choisissez un emplacement pour le groupe de ressources.
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
  
6.  Cliquez sur **Consulter les termes et conditions**, **Acheter**, puis cliquez sur **Créer** pour commencer le déploiement.  Sélectionnez **Épingler au tableau de bord** pour que le déploiement obtenu soit facilement visible sur votre page d’accueil du portail Azure.
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)

7.  Une fois le déploiement terminé, le panneau du groupe de ressources s’ouvre.
    ![Capture d’écran du panneau Groupe de ressources](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  

8.  Pour utiliser l’application, accédez simplement à l’URL de l’application web (dans l’exemple ci-dessus, l’URL serait http://mydemodocdbwebapp.azurewebsites.net).  Vous verrez l'application web suivante :

    ![Exemple d’application Todo](./media/documentdb-create-documentdb-website/image2.png)

9. Continuez et créez deux tâches dans l’application web, puis revenez dans le panneau du groupe de ressources du portail Azure. Cliquez sur la ressource de compte DocumentDB dans la liste des ressources, puis cliquez sur **Explorateur de requête**.
    ![Capture d’écran de l’objectif Résumé avec l’application web myotherdocumentdbwebapp mise en surbrillance](./media/documentdb-create-documentdb-website/TemplateDeployment8.png)  

10. Exécutez la requête par défaut, « SELECT * FROM c », et examinez les résultats.  Notez que la requête a récupéré la représentation JSON des éléments de tâche que vous avez créés à l'étape 7 ci-dessus.  N’hésitez pas à faire des essais avec des requêtes ; par exemple, essayez d’exécuter SELECT * FROM c WHERE c.isComplete = true pour retourner tous les éléments de tâche qui ont été marqués comme terminés.

    ![Capture d’écran des panneaux Explorateur de requête et Résultats affichant les résultats de requête](./media/documentdb-create-documentdb-website/image5.png)

11. N'hésitez pas à explorer l'utilisation du portail DocumentDB ou modifier l'exemple d'application Todo.  Lorsque vous êtes prêt, nous allons déployer un autre modèle.
    
<a id="Build"></a> 
## <a name="step-3:-deploy-the-document-account-and-web-app-sample"></a>Étape 3 : déploiement de l’exemple de compte DocumentDB et d’application web

Maintenant nous allons déployer notre deuxième modèle.  Ce modèle est utile pour indiquer comment vous pouvez injecter des informations de connexion DocumentDB comme le point de terminaison de compte et la clé principale dans une application web en tant que paramètres d’application ou que chaîne de connexion personnalisée. Par exemple, vous disposez peut-être de votre propre application web que vous souhaitez déployer avec un compte DocumentDB avec les informations de connexion renseignées automatiquement pendant le déploiement.

> [AZURE.TIP] Ce modèle ne valide pas que le nom de l’application web et le nom du compte DocumentDB entrés ci-dessous sont a) corrects et b) disponibles.  Il est vivement recommandé de vérifier la disponibilité des noms que vous souhaitez fournir avant de soumettre le déploiement.

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur Nouveau et recherchez « Déploiement de modèle ».
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)

2. Sélectionnez l’élément Déploiement de modèle et cliquez sur **Créer**
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment2.png)

3.  Cliquez sur **Modifier le modèle**, collez le contenu du fichier de modèle DocDBWebSite.json, puis cliquez sur **Enregistrer**.
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)

4. Cliquez sur **Modifier les paramètres**, entrez des valeurs pour chacun des paramètres obligatoires, puis cliquez sur **OK**.  Les paramètres sont les suivants :

    1. SITENAME : spécifie le nom de l’application web App Service utilisée pour former l’URL d’accès à l’application web (par exemple, si vous spécifiez « mydemodocdbwebsite », l’URL par laquelle vous accéderez à l’application web sera mydemodocdbwebsite.azurewebsites.net).

    2. HOSTINGPLANNAME : spécifie le nom du plan d’hébergement App Service à créer.

    3. LOCATION : spécifie l’emplacement Azure dans lequel créer les ressources DocumentDB et d’application web.

    4. DATABASEACCOUNTNAME : spécifie le nom du compte DocumentDB à créer.   

    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)

5. Choisissez un groupe de ressources existant ou fournissez un nom afin de créer un nouveau groupe de ressources et choisissez un emplacement pour le groupe de ressources.
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
  
6.  Cliquez sur **Consulter les termes et conditions**, **Acheter**, puis cliquez sur **Créer** pour commencer le déploiement.  Sélectionnez **Épingler au tableau de bord** pour que le déploiement obtenu soit facilement visible sur votre page d’accueil du portail Azure.
    ![Capture d’écran de l’interface utilisateur du déploiement de modèle](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)

7.  Une fois le déploiement terminé, le panneau du groupe de ressources s’ouvre.
    ![Capture d’écran du panneau Groupe de ressources](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  

8. Cliquez sur la ressource d’application web dans la liste des ressources, puis sur **Paramètres de l’application**
    ![Capture d’écran du groupe de ressources](./media/documentdb-create-documentdb-website/TemplateDeployment9.png)  

9. Remarquez l’existence de paramètres d’application pour le point de terminaison DocumentDB et chacune des clés principales DocumentDB.
    ![Capture d’écran des paramètres de l’application](./media/documentdb-create-documentdb-website/TemplateDeployment10.png)  

10. N'hésitez pas à continuer à explorer le portail Azure ou à suivre l'un de nos [exemples](http://go.microsoft.com/fwlink/?LinkID=402386) DocumentDB pour créer votre propre application DocumentDB.

    
    
<a name="NextSteps"></a>
## <a name="next-steps"></a>Étapes suivantes

Félicitations ! Vous avez déployé DocumentDB, une application web App Service et un exemple d’application web avec les modèles Azure Resource Manager.

- Pour en savoir plus sur DocumentDB, cliquez [ici](http://azure.com/docdb).
- Pour en savoir plus sur les applications web Azure App Service, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=325362).
- Pour en savoir plus sur les modèles Azure Resource Manager, cliquez [ici](https://msdn.microsoft.com/library/azure/dn790549.aspx).


## <a name="what's-changed"></a>Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez [Références sur la navigation dans le portail Azure Classic](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
 



<!--HONumber=Oct16_HO2-->


