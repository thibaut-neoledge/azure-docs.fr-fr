<properties
	pageTitle="Créer des applications .NET HDInsight d’authentification non interactives | Microsoft Azure"
	description="Apprenez à créer des applications .NET HDInsight d’authentification non interactives."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="jgao"/>

# Créer des applications .NET HDInsight d’authentification non interactives

Vous pouvez exécuter votre application .NET Azure HDInsight sous l’identité de l’application (non interactif) ou sous l’identité de l’utilisateur connecté à l’application (interactif). Pour voir un exemple de l’application interactive, consultez [Envoi de tâches Hive/Pig/Sqoop avec le kit de développement logiciel .NET HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Cet article vous présente comment créer une application .NET d’authentification non interactive pour se connecter à Azure HDInsight et envoyer une tâche Hive.

Dans votre application .NET, vous aurez besoin des éléments suivants :

- votre ID client d’abonnement Azure ;
- l’ID client d’application Azure Active Directory ;
- la clé secrète de l’application Azure Active Directory.  

Ce processus comprend les étapes suivantes :

2. Créer une application Azure Active Directory.
2. Affecter des rôles à l’application Active Directory.
3. Développer votre application cliente.


##Composants requis

- Cluster HDInsight. Vous pouvez en créer un en suivant les instructions stipulées dans le [didacticiel mise en route](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## Créer une application Azure Active Directory 
Lorsque vous créez une application Active Directory, l’application et le principal du service sont créés. Vous pouvez exécuter l’application sous l’identité de l’application.

Actuellement, vous devez utiliser le portail Azure Classic pour créer une application Active Directory. Cette possibilité sera ajoutée au portail Azure dans une version ultérieure. Vous pouvez également effectuer ces étapes via Azure PowerShell ou l’interface de ligne de commande Azure. Pour plus d’informations sur l’utilisation de PowerShell ou de l’interface de ligne de commande avec le principal du service, consultez [Authentifier un principal du service à l’aide d’Azure Resource Manager](../resource-group-authenticate-service-principal.md).

**Créer une application Azure Active Directory**

1.	Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).
2.	Sélectionnez **Active Directory** dans le volet gauche.

    ![Portail Azure Classic Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	Sélectionnez l’annuaire que vous souhaitez utiliser pour la création de la nouvelle application. Il doit s’agir de celui existant.
4.	Cliquez sur **Applications** en haut de la liste pour répertorier les applications existantes.
5.	Cliquez sur **Ajouter** en bas pour ajouter une nouvelle application.
6.	Entrez le **nom**, sélectionnez **Application web et/ou API web**, puis cliquez sur **Suivant**.

    ![nouvelle application Azure Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	Entrez l’**URL de connexion** et un **URI ID d’application**. Pour **URL de connexion**, indiquer l’URI vers un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour l’URI ID d’application, indiquez l’URI qui identifie votre application. Cliquez ensuite sur **Terminé**. Il faut quelques instants pour créer l’application. Une fois l’application créée, le portail affiche la page Aperçu rapide de la nouvelle application. Ne fermez pas le portail.

    ![Propriétés de la nouvelle application Azure Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Pour obtenir l’ID client d’application et la clé secrète**

1.	Dans la page de la nouvelle application Active Directory, cliquez sur **Configurer** dans le menu supérieur.
2.	Faites une copie de **ID client**. Vous en aurez besoin dans votre application .NET.
3.	Sous **Clés**, cliquez sur la liste déroulante **Sélectionner une durée**, sélectionnez **1 an** ou **2 ans**. La valeur de la clé s’affichera pas jusqu’à ce que vous ayez enregistré la configuration.
4.	Cliquez sur **Enregistrer** en bas de la page. Lorsque la clé secrète s’affiche, faites une copie de la clé. Vous en aurez besoin dans votre application .NET.

##Affecter l’application Active Directory à un rôle

Vous devez affecter l’application à un [rôle](../active-directory/role-based-access-built-in-roles.md) pour lui accorder les autorisations lui permettant de réaliser des opérations. Vous pouvez définir l’étendue au niveau de l’abonnement, du groupe de ressources ou de la ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue (par exemple, l’ajout d’une application au rôle Lecteur pour un groupe de ressources signifie qu’elle peut lire le groupe de ressources et toutes les ressources qu’il contient). Dans ce didacticiel, vous allez définir la portée au niveau du groupe de ressources. Étant donné que le Portail Azure Classic ne prend pas en charge les groupes de ressources, cette partie doit être effectuée à partir du portail Azure.

**Pour ajouter le rôle propriétaire à l’application Active Directory**

1.	Connectez-vous au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **Groupe de ressources** dans le volet de gauche.
3.	Cliquez sur le groupe de ressources qui contient le cluster HDInsight sur lequel vous exécuterez votre requête Hive plus tard dans ce didacticiel. S’il y a trop de groupes de ressources, vous pouvez utiliser le filtre.
4.	Dans le panneau du cluster, cliquez sur **Accéder**.

    ![icône nuage et foudre = démarrage rapide](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	Dans le panneau **Utilisateurs**, cliquez sur **Ajouter**.
6.	Suivez les instructions pour ajouter le rôle **Propriétaire** à l’application Active Directory que vous avez créée dans la dernière procédure. Lorsque la procédure se termine correctement, vous devez voir l’application répertoriée dans le panneau des utilisateurs, avec le rôle de propriétaire.


##Développer une application cliente HDInsight

Créez une application de console .NET Visual C# en suivant les instructions stipulées dans [Envoi de travaux Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Puis remplacez la méthode GetTokenCloudCredentials par les éléments suivants :

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Pour récupérer l’ID client par le biais de PowerShell :

    Get-AzureRmSubscription

Ou d’Azure CLI :

    azure account show --json

      
## Voir aussi

- [Envoi de tâches Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Création de l'application Active Directory et du principal du service à l'aide du portail](../resource-group-create-service-principal-portal.md)
- [Authentifier un principal du service à l’aide d’Azure Resource Manager](../resource-group-authenticate-service-principal.md)
- [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0615_2016-->