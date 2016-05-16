<properties
	pageTitle="Connexion d’Operations Manager à Log Analytics | Microsoft Azure"
	description="Pour conserver vos investissements existants dans System Center Operations Manager et utiliser des fonctionnalités étendues avec Log Analytics, vous pouvez intégrer Operations Manager à votre espace de travail OMS."
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

# Connexion d’Operations Manager à Log Analytics

Pour conserver vos investissements existants dans System Center Operations Manager et utiliser des fonctionnalités étendues avec Log Analytics, vous pouvez intégrer Operations Manager à votre espace de travail OMS. Cela vous permet de tirer parti des possibilités qu’offre OMS, tout en continuant à utiliser Operations Manager pour :

- Continuer à surveiller l’intégrité de vos services informatiques avec Operations Manager
- Conserver l’intégration avec vos solutions ITSM pour la gestion des incidents et des problèmes
- Gérer le cycle de vie des agents déployés sur des machines virtuelles IaaS, qu’elles soient locales ou dans le cloud public, et que vous surveillez avec Operations Manager

L’intégration avec System Center Operations Manager enrichit votre stratégie d’opérations de service en vous permettant de tirer parti de la vitesse et de l’efficacité d’OMS en termes de collecte, de stockage et d’analyse des données d’Operations Manager. OMS seconde votre processus existant de gestion des problèmes. Il vous aide ainsi à mettre en corrélation et à identifier les erreurs et les expositions répétées. La flexibilité du moteur de recherche en matière d’examen des performances, des événements et des données d’alerte, ainsi que les tableaux de bord complets et les fonctions de création de rapports permettant d’exposer ces données de manière utile, illustrent la puissance qu’OMS apporte à Operations Manager.

Les agents chargés de fournir des rapports au groupe d’administration d’Operations Manager collectent des données à partir de vos serveurs en se basant sur les solutions et les sources de données Log Analytics que vous avez activées dans votre abonnement OMS. En fonction de la solution que vous avez activée, les données de ces solutions sont soit envoyées directement à partir d’un serveur de gestion Operations Manager au service web OMS, ou, en raison du volume de données collecté sur le système géré par l’agent, envoyées directement à partir de l’agent au service web OMS. Le serveur de gestion transfère directement les données d’OMS au service web OMS. Elles ne sont jamais écrites dans la base de données OperationsManager ou OperationsManagerDW. Lorsqu’un serveur de gestion perd la connexion au service web OMS, il met en cache les données localement jusqu’à ce que la communication avec OMS soit rétablie. Si le serveur de gestion est en mode hors connexion en raison d’une maintenance planifiée ou d’une interruption non planifiée, un autre serveur de gestion du groupe d’administration assure la connectivité avec OMS.

Le diagramme suivant montre la relation entre System Center Operations Manager et OMS.

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## Conditions requises pour le système
Avant de commencer, veuillez prendre connaissance des informations suivantes pour vérifier que les conditions requises sont remplies.

- OMS prend uniquement en charge Operations Manager 2012 SP1 UR6 et versions supérieures et Operations Manager 2012 R2 UR2 et versions supérieures. La prise en charge du proxy a été ajoutée dans Operations Manager 2012 SP1 UR7 et Operations Manager 2012 R2 UR3.
- Un abonnement OMS. Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](log-analytics-get-started.md).

## Connexion d’Operations Manager à OMS
Suivez les étapes ci-après pour configurer votre groupe d’administration Operations Manager de façon à ce qu’il se connecte à l’un de vos espaces de travail OMS.

1. Dans la console Operations Manager, sélectionnez l’espace de travail **Administration**.
2. Développez le nœud Operations Management Suite, puis cliquez sur **Connexion**.
3. Cliquez sur le lien **S’enregistrer sur Operations Management Suite**.
4. Sur la page **Assistant d’intégration à Operations Management Suite : authentification**, saisissez l’adresse électronique ou le numéro de téléphone et le mot de passe du compte d’administrateur associé à votre abonnement OMS, puis cliquez sur **Se connecter**.
5. Une fois que vous êtes correctement authentifié, sur la page **Assistant d’intégration à Operations Management Suite : sélection d’un espace de travail**, vous serez invité à sélectionner votre espace de travail OMS. Si vous avez plusieurs espaces de travail, dans la liste déroulante, sélectionnez l’espace de travail que vous souhaitez enregistrer dans le groupe d’administration d’Operations Manager, puis cliquez sur **Suivant**.
    >[AZURE.NOTE] Operations Manager prend uniquement en charge un espace de travail OMS à la fois. La connexion et les ordinateurs enregistrés sur OMS avec l’espace de travail précédent sont supprimés d’OMS.
6. Sur la page **Assistant d’intégration à Operations Management Suite : résumé**, vérifiez vos paramètres et s’ils sont corrects, cliquez sur **Créer**.
7. Sur la page **Assistant d’intégration à Operations Management Suite : fin**, cliquez sur **Fermer**.

### Ajout d’ordinateurs gérés par des agents
La configuration de l’intégration avec votre espace de travail OMS établit uniquement une connexion à OMS, sans qu’aucune donnée soit collectée par les agents générant des rapports pour votre groupe d’administration. Les données seront collectées uniquement une fois que vous aurez configuré les ordinateurs gérés par des agents qui seront chargés de collecter les données pour Log Analytics. Vous pouvez sélectionner les objets ordinateur individuellement ou sélectionner un groupe qui contient des objets ordinateur Windows. Vous ne pouvez pas sélectionner un groupe qui contient des instances d’une autre classe, tels que des disques logiques ou des bases de données SQL.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration**.
2. Développez le nœud Operations Management Suite, puis cliquez sur **Connexion**.
3. Cliquez sur le lien **Ajouter un ordinateur/groupe** dans la section Actions sur le côté droit du volet.
4. Vous pouvez rechercher des ordinateurs ou des groupes surveillés par Operations Manager dans la boîte de dialogue **Recherche d’ordinateurs**. Sélectionnez les ordinateurs ou les groupes à intégrer à OMS, cliquez sur **Ajouter**, puis sur **OK**.

Vous pouvez afficher les ordinateurs et les groupes configurés pour collecter des données à partir du nœud des ordinateurs gérés sous Operations Management Suite dans l’espace de travail **Administration** de la console Operations. Cet emplacement vous permet d’ajouter ou de supprimer des ordinateurs et des groupes selon les besoins.

### Configuration des paramètres de proxy OMS dans la console Operations
Si un serveur proxy interne se trouve entre le groupe d’administration et le service web OMS, procédez comme suit. Ces paramètres sont gérés de manière centralisée à partir du groupe d’administration et distribués aux systèmes gérés par des agents qui sont inclus dans l’étendue de collecte des données pour OMS. Cette méthode présente des avantages lorsque certaines solutions contournent le serveur de gestion et envoient les données directement au service web OMS.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration**.
2. Développez Operations Management Suite, puis cliquez sur **Connexion**.
3. Dans la vue Connexion à OMS, cliquez sur **Configurer le serveur proxy**.
4. Sur la page **Assistant Operations Management Suite : serveur proxy**, sélectionnez **Utiliser un serveur proxy pour accéder à Operations Management Suite**, puis tapez l’URL avec le numéro de port, par exemple http://corpproxy:80, puis cliquez sur **Terminer**.

Si votre serveur proxy requiert une authentification, procédez comme suit pour configurer les identifiants et paramètres qui doivent se propager sur les ordinateurs gérés qui envoient des rapports à OMS dans le groupe d’administration.

1. Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration**.
2. Sous **Configuration d'identification**, sélectionnez **Profils**.
3. Ouvrez le profil **Proxy d’identification de System Center Advisor**.
4. Dans l’Assistant Profil d’identification, cliquez sur Ajouter pour utiliser un compte d’identification. Vous pouvez créer un [compte d’identification](https://technet.microsoft.com/library/hh321655.aspx) ou utiliser un compte existant. Ce compte doit disposer des autorisations suffisantes pour franchir le serveur proxy.
5. Pour définir le compte à gérer, choisissez **Une classe, un groupe ou un objet sélectionné(e)**, cliquez sur **Sélectionner...**, puis sur **Groupe...** pour ouvrir la zone **Recherche de groupes**.
6. Recherchez puis sélectionnez le **groupe de serveur de surveillance Microsoft System Center Advisor**. Une fois le groupe sélectionné, cliquez sur **OK** pour fermer la zone **Recherche de groupes**.
7.	Cliquez sur **OK** pour fermer la zone **Ajouter un compte d’identification**
8.	Cliquez sur **Enregistrer** pour fermer l’Assistant et enregistrer vos modifications.

Une fois la connexion créée et la configuration des agents collectant et envoyant les données à OMS terminée, la configuration suivante est appliquée au groupe d’administration (l’ordre peut varier) :

- Le compte d’identification **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** est créé. Il est associé au profil **Blob d’identification de Microsoft System Center Advisor** et est destiné à deux classes : **Serveur de collecte** et **Groupe d’administration Operations Manager**.
- Deux connecteurs sont créés. Le premier se nomme **Microsoft.SystemCenter.Advisor.DataConnector** et est automatiquement configuré avec un abonnement qui transmet toutes les alertes générées par les instances de toutes les classes dans le groupe d’administration pour OMS Log Analytics. Le second connecteur est **Advisor Connector**. Il est chargé de communiquer avec le service web OMS et de partager les données.
- Les agents et les groupes que vous avez sélectionnés pour la collecte de données dans le groupe d’administration seront ajoutés au **groupe de serveur de surveillance Microsoft System Center Advisor**.

## Mises à jour du pack d’administration
Une fois que la configuration est terminée, le groupe d’administration Operations Manager établit une connexion avec le service OMS. Le serveur d’administration est synchronisé avec le service web et reçoit des informations de configuration mises à jour sous la forme de packs d’administration pour les solutions que vous avez activées et qui s’intègrent avec Operations Manager. Operations Manager recherche des mises à jour de ces packs d’administration, puis les télécharge et les importe automatiquement. Deux règles principales contrôlent ce processus :

- **Microsoft.SystemCenter.Advisor.MPUpdate** : met à jour les packs d’administration de base d’OMS. Par défaut, elle s’exécute toutes les douze (12) heures.
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** : met à jour les packs d’administration de solution activés dans votre espace de travail. Par défaut, elle s’exécute toutes les cinq (5) minutes.

Vous pouvez désactiver ces deux règles pour empêcher le téléchargement automatique ou modifier la fréquence de la synchronisation du serveur d’administration avec OMS pour déterminer si un nouveau pack d’administration est disponible et doit être téléchargé. Suivez les étapes de la [procédure de remplacement d’une règle ou d’un moniteur](https://technet.microsoft.com/library/hh212869.aspx) pour modifier la valeur en secondes du paramètre **Fréquence** afin de changer la fréquence de synchronisation, ou pour modifier le paramètre **Activé** afin de désactiver les règles. Pour le remplacement, ciblez tous les objets de la classe Groupe d’administration Operations Manager.

Si vous souhaitez continuer à suivre votre processus habituel de contrôle des modifications pour contrôler les versions du pack d’administration dans votre groupe d’administration de production, vous pouvez désactiver les règles et les activer à des heures auxquelles les mises à jour sont autorisées. Si votre environnement dispose d’un groupe d’administration du développement ou de l’assurance qualité connecté à Internet, vous pouvez configurer ce groupe d’administration avec un espace de travail OMS de façon à ce qu’il prenne en charge ce scénario. Ainsi, vous pourrez examiner et évaluer les versions itératives des packs d’administration OMS avant de les introduire dans votre groupe d’administration de production.

## Validation de l’intégration d’Operations Manager avec OMS
Vous pouvez vérifier que votre intégration entre OMS et Operations Manager a réussi de plusieurs manières.

### Pour confirmer l’intégration à partir du portail OMS

1.	Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2.	Dans le menu du haut, cliquez sur **Sources connectées**.
3.	Dans la section System Center Operations Manager, vous devez voir l’état **1 groupe MGMT connecté** et dans le tableau en dessous, le nom du groupe d’administration répertorié avec le nombre d’agents et l’état lors de la dernière réception de données.

### Pour confirmer l’intégration à partir de la console Operations

1.	Ouvrez la console Operations Manager, puis sélectionnez l'espace de travail **Administration**.
2.	Cliquez sur le nœud **Packs d’administration** et dans la zone de texte **Rechercher :**, saisissez **Advisor** ou **Intelligence**.
3.	En fonction des solutions activées, le pack d’administration correspondant apparaîtra dans les résultats de recherche. Par exemple, si vous avez activé la solution de gestion des alertes, le pack d’administration Gestion des alertes Microsoft System Center Advisor figurera dans la liste.

## Étapes suivantes

- [Ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.
- [Configurez les paramètres de proxy et de pare-feu dans Log Analytics](log-analytics-proxy-firewall.md) si votre organisation utilise un serveur proxy ou un pare-feu pour que les agents puissent communiquer avec le service Log Analytics.

<!---HONumber=AcomDC_0504_2016-->