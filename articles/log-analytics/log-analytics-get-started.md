<properties
	pageTitle="Prise en main de Log Analytics | Microsoft Azure"
	description="Vous pouvez en quelques minutes être opérationnel avec Log Analytics dans Microsoft Operations Management Suite (OMS)."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Prise en main de Log Analytics

Vous pouvez en quelques minutes être opérationnel avec Log Analytics dans Microsoft Operations Management Suite (OMS). Deux options sont disponibles lorsque vous choisissez de créer un espace de travail OMS, qui est similaire à un compte :

- Site Web Microsoft Operations Management Suite
- Abonnement Microsoft Azure

Vous pouvez créer un espace de travail OMS via le site Web OMS. Vous pouvez également utiliser un abonnement Microsoft Azure pour créer un espace de travail OMS. Les deux espaces de travail sont fonctionnellement équivalents. Si vous utilisez un abonnement Azure, vous pouvez également utiliser cet abonnement pour accéder aux autres services Azure. Quelle que soit la méthode utilisée pour créer l’espace de travail, vous allez le créer avec un compte Microsoft ou un compte professionnel.

Voici le processus :

![diagramme d'intégration](./media/log-analytics-get-started/oms-onboard-diagram.png)

## S’inscrire en 3 étapes à l’aide d’Operations Management Suite

1. Accédez au site web [Operations Management Suite](http://microsoft.com/oms) et cliquez sur **Essai gratuit**. Connectez-vous avec votre compte Microsoft, par exemple Outlook.com, ou avec un compte professionnel fourni par votre société ou établissement d’enseignement à utiliser avec Office 365 ou d’autres services Microsoft.
2. Fournissez un nom d’espace de travail unique. Un espace de travail est un conteneur logique où sont stockées vos données de gestion. Il permet de partitionner les données entre les différentes équipes de votre organisation, car les données sont réservées à son espace de travail. Spécifiez une adresse de messagerie et la région où vous souhaitez stocker vos données.![créer un espace de travail et lier un abonnement](./media/log-analytics-get-started/oms-onboard-create-workspace-link.png)
3. Vous pouvez créer ensuite un abonnement Azure ou un lien à un abonnement Azure existant. Si vous souhaitez continuer à utiliser l’essai gratuit, cliquez sur **Pas maintenant**.

Vous êtes prêt à utiliser le portail Operations Management Suite.

Pour plus d’informations sur la configuration de votre espace de travail et la liaison des comptes Azure existants aux espaces de travail créés avec Operations Management Suite, consultez la rubrique [Gestion de l'accès à Log Analytics](log-analytics-manage-access.md).

## S’inscrire rapidement à l’aide de Microsoft Azure

1. Accédez au [portail Azure](https://portal.azure.com), connectez-vous, parcourez la liste des services, puis sélectionnez **Log Analytics (OMS)**. ![Portail Azure](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Cliquez sur **Ajouter**, puis sélectionnez les options pour les éléments suivants :
    - Nom de **l’espace de travail OMS**
    - **Abonnement** : si vous possédez plusieurs abonnements, choisissez celui que vous souhaitez associer au nouvel espace de travail.
    - **Groupe de ressources**
    - **Emplacement**
    - **Niveau tarifaire**![création rapide](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Cliquez sur **Créer** : les détails de l'espace de travail apparaissent dans le portail Azure. ![détails de l'espace de travail](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. Cliquez sur le lien **Portail OMS** pour ouvrir le site Web Operations Management Suite avec votre nouvel espace de travail.

Vous êtes prêt à utiliser le portail Operations Management Suite.

Pour plus d’informations sur la configuration de votre espace de travail et la liaison des espaces de travail existants créés à l’aide d’Operations Management Suite aux abonnements Azure, consultez la rubrique [Gestion de l’accès à Log Analytics](log-analytics-manage-access.md).

## Prise en main du portail Operations Management Suite
Pour choisir des solutions et connecter les serveurs que vous souhaitez gérer, cliquez sur la vignette **Paramètres**, puis procédez comme suit.

![prise en main](./media/log-analytics-get-started/oms-onboard-get-started.png)

- **Ajouter des solutions** : sélectionnez les solutions que vous souhaitez utiliser, puis cliquez sur **Ajouter les solutions sélectionnées**.![solutions](./media/log-analytics-get-started/oms-onboard-solutions.png)
- **Connecter une source de données** : choisissez la façon dont vous souhaitez vous connecter à votre environnement de serveur pour collecter des données :
    - Connectez directement un serveur ou un client Windows Server en installant un agent.
    - Utilisez System Center Operations Manager pour joindre vos groupes d’administration ou tout votre déploiement d’Operations Manager.
    - Utilisez un compte de stockage Azure configuré avec l’extension de machine virtuelle de diagnostic Azure Windows ou Linux.![sources de données](./media/log-analytics-get-started/oms-onboard-data-sources.png)    
- **Ajouter des journaux** : configurez au moins une source de données pour remplir vos données, puis sélectionnez **Enregistrer**. Pour les journaux des événements, vous pouvez spécifier le type de message, notamment d’erreur, d’avertissement et d’information à surveiller.    

    ![journaux](./media/log-analytics-get-started/oms-onboard-logs.png)


## Si vous le souhaitez, connectez directement les serveurs à Operations Management Suite en installant un agent.
1. Cliquez sur la mosaïque **Paramètres**, sur l’onglet **Sources connectées**, puis sur **Télécharger l'agent Windows** pour l'architecture de l'ordinateur sur lequel vous souhaitez l’installer. Vous ne pouvez installer l’agent que sur Windows Server 2008 SP 1 ou ultérieur ou sur Windows 7 SP1 ou ultérieur.
2. Installez l’agent sur un ou plusieurs serveurs. Vous pouvez installer les agents un par un ou à l’aide d’une méthode plus automatisée avec un [script personnalisé](log-analytics-windows-agents.md). Vous pouvez également utiliser une solution de distribution de logiciels existante dont vous pourriez disposer.
3. Après avoir accepté le contrat de licence et choisi votre dossier d’installation, sélectionnez **Connecter l’agent à Microsoft Azure Operational Insights**. (OMS s'appelait auparavant Operational Insights).![configuration de l’agent](./media/log-analytics-get-started/oms-onboard-agent.png)

4. Dans la page suivante, vous êtes invité à entrer votre ID d’espace de travail et votre clé d’espace de travail. Votre ID et votre clé d’espace de travail sont affichés dans l’écran où vous avez téléchargé le fichier de l’agent. ![clés de l'agent](./media/log-analytics-get-started/oms-onboard-mma-keys.png)![joindre des serveurs](./media/log-analytics-get-started/oms-onboard-key.png)
5. Pendant l’installation, vous pouvez cliquer sur **Avancé** pour configurer votre serveur proxy et fournir des informations d’authentification. Cliquez sur le bouton **Suivant** pour revenir à l’écran d’informations sur l’espace de travail.
6. Cliquez sur **Suivant** pour valider votre ID et votre clé d’espace de travail. Si des erreurs sont détectées, vous pouvez cliquer sur **Précédent** pour apporter des corrections. Une fois que votre ID et votre clé d’espace de travail sont validés, cliquez sur **Installer** pour terminer l’installation de l’agent.
7. Reconnectez-vous au portail Operations Management Suite, puis cliquez sur la vignette **Paramètres** sur la page de présentation. Une icône de coche verte s’affiche lorsque les agents communiquent avec le service Operations Management Suite. Au départ, cela prend environ 5 à 10 minutes.

>[AZURE.NOTE] Les solutions d’évaluation de la configuration et de la gestion de capacité ne sont actuellement pas prises en charge par les serveurs connectés directement à Operations Management Suite.


Vous pouvez également connecter l’agent à System Center Operations Manager 2012 SP1 et ultérieur. Pour ce faire, sélectionnez **Connecter l’agent à System Center Operations Manager**. Si vous choisissez cette option, vous envoyez des données au service sans besoin de matériel supplémentaire ni charge sur vos groupes d’administration.

Pour plus d’informations sur la connexion des agents à Operations Management Suite, consultez la rubrique [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md).

## Si vous le souhaitez, connectez les serveurs à l’aide de System Center Operations Manager.

1. Dans la console Operations Manager, sélectionnez **Administration**.
2. Développez le nœud **Operational Insights** et sélectionnez **Connexion à Operational Insights**.

  >[AZURE.NOTE] Selon les correctifs cumulatifs SCOM que vous utilisez, vous pouvez voir un nœud pour *System Center Advisor*, *Operational Insights* ou *Operations Management Suite*.

3. Cliquez sur le lien **S’inscrire à Operational Insights** en haut à droite, et suivez les instructions affichées.
4. Après avoir terminé l’Assistant Inscription, cliquez sur le lien **Ajouter un ordinateur/groupe**.
5. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d’ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à Log Analytics, cliquez sur **Ajouter**, puis sur **OK**. Vous pouvez vérifier que le service OMS reçoit des données en accédant à la vignette **Utilisation** dans le portail Operations Management Suite. Des données doivent apparaître dans 5 à 10 minutes environ.

Pour plus d’informations sur la connexion d’Operations Manager à Operations Management Suite, consultez [Connexion d'Operations Manager à Log Analytics](log-analytics-om-agents.md).

## Si vous le souhaitez, analysez les données des services cloud dans Microsoft Azure.

Avec Operations Management Suite, vous pouvez rechercher rapidement les journaux des événements et les journaux IIS pour les services cloud et les machines virtuelles en activant les diagnostics dans Azure Cloud Services. Vous pouvez également recevoir des informations supplémentaires relatives à vos machines virtuelles Azure en installant Microsoft Monitoring Agent. Pour plus d’informations sur la configuration de votre environnement Azure pour utiliser Operations Management Suite, consultez la rubrique [Connecter Azure Storage à Log Analytics](log-analytics-azure-storage.md).


## Étapes suivantes

- [Ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.
- Familiarisez-vous avec les [recherches de journal](log-analytics-log-searches.md) pour afficher les informations détaillées collectées par les solutions.
- Utilisez les [tableaux de bord](log-analytics-dashboards.md) pour enregistrer et afficher vos propres recherches personnalisées.

<!---HONumber=AcomDC_0504_2016-->