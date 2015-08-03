<properties
    pageTitle="Embarquer dans Operational Insights en quelques minutes"
    description="En savoir plus sur la maîtrise rapide d’Operational Insights"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/09/2015"
    ms.author="banders"/>

# Embarquer dans Operational Insights en quelques minutes


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Vous pouvez devenir opérationnel avec Operational Insights en quelques minutes. Deux options sont disponibles lorsque vous choisissez de créer un espace de travail Operational Insights, qui est similaire à un compte :

- Microsoft Operations Management Suite
- Abonnement Microsoft Azure

Vous pouvez créer un espace de travail Microsoft Operations Management Suite à l’aide du site web Microsoft Operations Management Suite. Vous pouvez également utiliser un abonnement Microsoft Azure pour créer un espace de travail Operational Insights. Actuellement, les deux espaces de travail sont fonctionnellement équivalents. Leur nom est la seule différence. Si vous utilisez un abonnement Azure, vous pouvez également utiliser cet abonnement pour accéder aux autres services Azure. Quelle que soit la méthode utilisée pour créer l’espace de travail, vous allez le créer avec un compte Microsoft ou un compte professionnel.

## S’inscrire en 3 étapes à l’aide d’Operations Management Suite

1. Accédez au site web [Microsoft Operations Management Suite](http://microsoft.com/oms) et cliquez sur **Essai gratuit**. Connectez-vous avec votre compte Microsoft, par exemple Outlook.com, ou avec un compte professionnel fourni par votre société ou établissement d’enseignement à utiliser avec Office 365 ou d’autres services Microsoft.
2. Fournissez un nom d’espace de travail unique. Un espace de travail est un conteneur logique où sont stockées vos données de gestion. Il permet de partitionner les données entre les différentes équipes de votre organisation, car les données sont réservées à son espace de travail. Spécifiez une adresse de messagerie et la région où vous souhaitez stocker vos données.![créer un espace de travail et lier un abonnement](./media/operational-insights-onboard-in-minutes/create-workspace-link-sub.png)
3. Vous pouvez créer ensuite un abonnement Azure ou un lien à un abonnement Azure existant. Si vous souhaitez continuer à l’aide de l’essai gratuit, cliquez sur **Pas maintenant**.

Vous êtes prêt à utiliser le portail Operations Management Suite.

Pour plus d’informations sur la configuration de votre espace de travail et la liaison des comptes Azure existants aux espaces de travail créés avec Operations Management Suite, consultez [Configurer votre espace de travail et gérer des paramètres](operational-insights-setup-workspace.md).

## S’inscrire rapidement à l’aide de Microsoft Azure

1. Accédez au [portail de gestion Azure](https://manage.windowsazure.com) et connectez-vous, puis, dans la liste des services, sélectionnez **Operational Insights**. ![Portail Azure](./media/operational-insights-onboard-in-minutes/azure-portal-op-insights.png)
2. Cliquez sur **Créer un espace de travail** et sur **Création rapide**, puis, sous Compte, tapez un nom d’espace de travail, choisissez un niveau et un emplacement pour stocker les données de l’espace de travail. Si vous possédez plusieurs abonnements, vous pouvez choisir celui que vous voulez utiliser, puis cliquer sur **Créer un espace de travail**. ![Portail Azure](./media/operational-insights-onboard-in-minutes/quick-create.png)
3. Sélectionnez l’espace de travail que vous avez créé, puis cliquez sur **Consultez votre compte Operational Insights** pour ouvrir le site web Operations Management Suite. ![consulter un compte](./media/operational-insights-onboard-in-minutes/visit-account.png)
4. Sur le site web Operations Management Suite, entrez votre adresse de messagerie, puis cliquez sur **Confirmer et continuer**. Un e-mail de confirmation va vous être envoyé. Ouvrez-le, puis dedans, cliquez sur **Confirmer maintenant**.
5. Le site web Operations Management Suite affiche la page de présentation. Pour continuer, cliquez sur **Prise en main**.

Vous êtes prêt à utiliser le portail Operations Management Suite.

Pour plus d’informations sur la configuration de votre espace de travail et la liaison des espaces de travail existants créés à l’aide d’Operations Management Suite aux abonnements Azure, consultez [Configurer votre espace de travail et gérer des paramètres](operational-insights-setup-workspace.md).

## Prise en main du portail Operations Management Suite
Pour choisir des solutions et connecter les serveurs que vous souhaitez gérer, cliquez sur la vignette **Prise en main**, puis procédez comme suit :

![créer un espace de travail et lier un abonnement](./media/operational-insights-onboard-in-minutes/get-started.png)

- Sélectionnez les solutions que vous souhaitez utiliser, puis cliquez sur **Ajouter les solutions sélectionnées**. ![solutions](./media/operational-insights-onboard-in-minutes/solutions.png)
- Choisissez la façon dont vous souhaitez vous connecter à votre environnement de serveur pour collecter des données :
    - Connectez directement un serveur ou un client Windows Server en installant un agent.
    - Utilisez System Center Operations Manager pour joindre vos groupes d’administration ou tout votre déploiement d’Operations Manager.
    - Utilisez un compte de stockage Azure configuré avec l’extension de machine virtuelle de diagnostic Azure Windows ou Linux.
- Configurez au moins un journal pour renseigner vos données. Vous pouvez sélectionner les journaux IIS et/ou ajouter des journaux des événements et sélectionner **Enregistrer** en bas de la page. Pour les journaux des événements, vous pouvez spécifier le type de message, notamment d’erreur, d’avertissement et d’information à surveiller. ![solutions](./media/operational-insights-onboard-in-minutes/logs.png)

## Si vous le souhaitez, connectez directement les serveurs à Operations Management Suite en installant un agent.
1. Dans la vue Prise en main, cliquez sur le nœud **Connecter une source de données**, puis sur **Télécharger l’agent Windows**. Vous ne pouvez installer l’agent que sur Windows Server 2008 SP 1 ou ultérieur ou sur Windows 7 SP1 ou ultérieur. Les serveurs doivent disposer d’une architecture x64.
2. Installez l’agent sur un ou plusieurs serveurs. Vous pouvez installer des agents un par un ou à l’aide d’une méthode plus automatisée avec un [script personnalisé](operational-insights-direct-agent.md#configure-the-microsoft-monitoring-agent-optional). Vous pouvez également utiliser une solution de distribution de logiciels existante dont vous pourriez disposer.
3. Après avoir accepté le contrat de licence et choisi votre dossier d’installation, sélectionnez **Connecter l’agent à Microsoft Azure Operational Insights**. ![configuration de l’agent](./media/operational-insights-onboard-in-minutes/agent.png)
4. Dans la page suivante, vous êtes invité à entrer votre ID d’espace de travail et votre clé d’espace de travail. Votre ID et votre clé d’espace de travail sont affichés dans l’écran où vous avez téléchargé le fichier de l’agent. ![joindre des serveurs](./media/operational-insights-onboard-in-minutes/key.png)
5. Pendant l’installation, vous pouvez cliquer sur **Avancé** pour configurer votre serveur proxy et fournir des informations d’authentification. Cliquez sur **Suivant** pour revenir à l’écran d’informations sur l’espace de travail.
6. Cliquez sur **Suivant** pour valider votre ID et votre clé d’espace de travail. Si des erreurs sont détectées, vous pouvez cliquer sur **Précédent** pour apporter des corrections. Une fois que votre ID et votre clé d’espace de travail sont validés, cliquez sur **Installer** pour terminer l’installation de l’agent.
7. Reconnectez-vous au portail Operations Management Suite, puis cliquez sur la vignette **Paramètres** dans la page de présentation. Une icône de coche verte s’affiche lorsque les agents communiquent avec le service Operations Management Suite. Au départ, cela prend environ 5 à 10 minutes.

> [AZURE.NOTE]Les solutions d’évaluation de la configuration et de la gestion de capacité ne sont actuellement pas prises en charge par les serveurs connectés directement à Operations Management Suite.

Vous pouvez également connecter l’agent à System Center Operations Manager 2012 SP1 et ultérieur. Pour ce faire, vous pouvez sélectionner **Connecter l’agent à System Center Operations Manager**. Si vous choisissez cette option, vous envoyez des données au service sans besoin de matériel supplémentaire ni charge sur vos groupes d’administration.

Pour plus d’informations sur la connexion directe des agents à Operations Management Suite, consultez [Connecter directement des ordinateurs à Operational Insights](operational-insights-direct-agent.md).

## Si vous le souhaitez, connectez les serveurs à l’aide de System Center Operations Manager.

1. Dans la console Operations Manager, sélectionnez **Administration**.
2. Développez le nœud **Operational Insights** et sélectionnez **Connexion à Operational Insights**.
3. Cliquez sur le lien **S’inscrire à Operational Insights** en haut à droite, et suivez les instructions affichées.
4. Après avoir terminé l’Assistant Inscription, cliquez sur le lien **Ajouter un ordinateur/groupe**.
5. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d’ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à Operational Insights, cliquez sur **Ajouter**, puis sur **OK**. Vous pouvez vérifier que le service Operational Insights reçoit des données en accédant à la vignette **Utilisation** dans le portail Operations Management Suite. Des données doivent apparaître dans 5 à 10 minutes environ.

Pour plus d’informations sur la connexion d’Operations Manager à Operations Management Suite, consultez [Se connecter à Operational Insights à partir de System Center Operations Manager](operational-insights-connect-scom.md).

## Si vous le souhaitez, analysez les données des services cloud dans Microsoft Azure.

Avec Operations Management Suite, vous pouvez rechercher rapidement les journaux des événements et les journaux IIS pour les services cloud et les machines virtuelles en activant les diagnostics dans Azure Cloud Services. Vous pouvez également recevoir des informations supplémentaires relatives à vos machines virtuelles Azure en installant Microsoft Monitoring Agent. Pour plus d’informations sur la configuration de votre environnement Azure pour utiliser Operations Management Suite, consultez [Analyser les données des serveurs dans Microsoft Azure](operational-insights-analyze-data-azure.md).


## Étapes suivantes
- Commencer à utiliser les [solutions](operational-insights-solutions.md)
- Se familiariser avec la [recherche](operational-insights-search.md)
- Utiliser les [tableaux de bord](operational-insights-use-dashboards.md) pour enregistrer et afficher les recherches personnalisées

<!---HONumber=July15_HO4-->