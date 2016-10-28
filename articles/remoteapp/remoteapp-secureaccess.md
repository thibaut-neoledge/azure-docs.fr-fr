
<properties 
    pageTitle="Sécurisation de l’accès à Azure RemoteApp et au-delà | Microsoft Azure"
	description="Découvrir comment sécuriser l’accès à Azure RemoteApp à l’aide de l’accès conditionnel dans Azure Active Directory"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# Sécurisation de l’accès à Azure RemoteApp et au-delà

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Dans cet article, nous proposons une vue d’ensemble de la façon dont un administrateur peut configurer un canal d’accès sécurisé commençant à partir de l’utilisateur final, via Azure RemoteApp, et se terminant par une ressource sécurisée, comme une base de données SQL ou une autre application principale. L’objectif est de s’assurer que seuls les utilisateurs autorisés répondant aux conditions souhaitées peuvent accéder aux applications distantes, et que le serveur principal sécurisé est accessible uniquement à partir de l’environnement Azure RemoteApp contrôlé et non à partir d’autres emplacements.

L’administrateur doit examiner trois zones principales :

![Considérations relatives à l’accès conditionnel à Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Poursuivez la lecture pour prendre connaissance des informations et des réponses à ces questions.

## Qui peut accéder à la collection ?
L’administrateur choisit les utilisateurs qui peuvent accéder aux applications distantes de la collection. Vous pouvez utiliser des comptes professionnels ou scolaires Azure Active Directory (Azure AD) (précédemment appelés « comptes professionnels ») ou des comptes Microsoft (par exemple @outlook.com). La plupart des scénarios d’entreprise utilisent des comptes Azure AD. Ils vous permettent d’utiliser les fonctionnalités d’accès conditionnel abordées plus loin et constituent également le seul choix pour les collections jointes au domaine. Le reste de cet article s’appuie sur l’hypothèse que vous utilisez des comptes Azure AD avec Azure RemoteApp.

**Ce que nous avons accompli :**

L’utilisation des comptes Azure AD pour contrôler l’accès à Azure RemoteApp nous apporte deux choses :

1.	Nous savons toujours qui peut accéder aux applications que nous avons publiées et qui peut accéder aux serveurs principaux auxquels ces applications se connectent.
2.	Nous contrôlons l’annuaire Azure AD sous-jacent afin de pouvoir créer et supprimer des comptes d’utilisateurs, définir des stratégies de mot de passe, utiliser l’authentification multifacteur, etc.

## Comment la collection est-elle accessible ? À partir d’où ?
En général, les administrateurs souhaitent définir des stratégies pour l’accès à un environnement Internet public, par exemple Azure RemoteApp. Par exemple, ils veulent s’assurer que les utilisateurs qui accèdent à l’environnement en dehors du réseau d’entreprise utilisent l’authentification multifacteur (MFA) pour y accéder ou qu’ils soient complètement bloqués.

Les administrateurs Azure RemoteApp peuvent utiliser la fonctionnalité disponible via Azure AD Premium pour définir des stratégies d’accès conditionnel pour leur environnement Azure RemoteApp. Ils peuvent également utiliser des fonctionnalités enrichies de création de rapports et d’alertes pour surveiller l’accessibilité de l’environnement.

### Comment configurer un accès conditionnel pour Azure RemoteApp
Nous allons vous guider pas à pas dans un exemple de scénario : un administrateur Azure RemoteApp souhaite bloquer l’accès à l’environnement lorsque les utilisateurs sont en dehors du réseau d’entreprise.

>[AZURE.NOTE] Nous supposons que vous avez mis à niveau Azure AD vers le niveau Premium et que vous avez créé au moins une collection Azure RemoteApp.

1.	Dans le Portail Azure, cliquez sur l’onglet **Active Directory**. puis sur l’annuaire à configurer.

	Rappel : l’accès conditionnel est une propriété de votre annuaire et non d’Azure RemoteApp. Par conséquent, toute la configuration est effectuée au niveau de l’annuaire. En d’autres termes, vous devez être administrateur d’annuaire pour effectuer ces modifications.

2.	Cliquez sur **Applications**, puis sur **Microsoft Azure RemoteApp** pour configurer un accès conditionnel. Notez que vous pouvez configurer un accès conditionnel pour chaque application « logiciel en tant que service » séparément dans votre annuaire. ![Configuration de l’accès conditionnel pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.	Dans l’onglet **Configurer**, définissez **Activer les règles d’accès** sur ACTIVÉ. ![Activer des règles d’accès pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.	Vous pouvez maintenant configurer différentes règles et choisir à qui les appliquer :

	1. Choisissez **Bloquer l’accès quand l’utilisateur n’est pas au travail** pour empêcher complètement les utilisateurs d’accéder à Azure RemoteApp hors de l’environnement réseau que vous spécifiez.
	2. Cliquez sur l’option ci-dessous pour définir les plages d’adresses IP qui constituent votre « réseau approuvé ». Tous les éléments figurant en dehors de celles-ci seront rejetées.

5.	Testez votre configuration en lançant le client Azure RemoteApp à partir d’une adresse IP non comprise dans la plage spécifiée. Une fois que vous vous êtes connecté avec vos informations d’identification Azure AD, un message semblable à ce qui suit doit s’afficher :

![Accès refusé à Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### Futures fonctionnalités d’accès conditionnel 
L’équipe Azure Active Directory travaille sur de nouvelles fonctionnalités dans l’accès conditionnel. Les administrateurs pourront créer des types de règle autres que les règles basées sur l’emplacement réseau. Une version préliminaire publique des nouvelles fonctionnalités devrait être disponible bientôt.

### Comment surveiller l’accès à Azure RemoteApp
Une fonctionnalité intéressante à utiliser avec l’accès conditionnel est la fonctionnalité de création de rapports d’Azure Active Directory Premium. Vous pouvez utiliser des rapports pour surveiller qui accède à votre environnement et pour détecter toute activité suspecte.

Par exemple, vous pouvez voir les noms des utilisateurs qui ont accédé à Azure RemoteApp, ainsi que leur nombre et leurs heures d’accès.

1.	Dans le Portail Azure, cliquez sur **Active Directory**, puis sur votre annuaire.

2.	Accédez à l’onglet **Rapports**.

3.	Dans la liste des rapports, sélectionnez **Utilisation des applications** sous **Applications intégrées**.

	Des statistiques agrégées pour Azure RemoteApp s’affichent. ![Statistiques agrégées sur l’accès à Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.	Cliquez sur l’application pour afficher les informations relatives aux utilisateurs accédant à Azure RemoteApp. ![Statistiques des accès utilisateur pour Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### Résumé
Avec Azure Active Directory Premium, vous pouvez définir des règles d’accès à Azure RemoteApp (et à d’autres logiciels comme les applications de service disponibles via Azure AD). Les règles sont actuellement limitées aux stratégies basées sur l’emplacement réseau, mais elles seront étendues à l’avenir à d’autres aspects de la gestion d’entreprise.

Azure AD Premium inclut également des fonctionnalités de création de rapports et de surveillance qui renforcent le contrôle de l’administrateur sur son environnement Azure RemoteApp.

## Comment m’assurer que ma ressource sécurisée est accessible uniquement à partir de mon environnement Azure RemoteApp ?
Dans les sections précédentes de cet article, nous nous sommes concentrés sur la sécurisation de l’accès à l’environnement Azure RemoteApp. Ce faisant, nous avons choisi les utilisateurs autorisés à accéder à cet environnement et défini des règles d’accès pour contrôler davantage la façon dont ils peuvent utiliser le service.

L’un des scénarios courants pour les déploiements Azure RemoteApp est que les applications distantes doivent communiquer avec une ressource principale, par exemple une base de données SQL. Cette ressource est hébergée localement (par exemple, dans un réseau d’entreprise) ou dans le cloud (par exemple, dans Azure IaaS). Les administrateurs veulent souvent s’assurer que la ressource principale est accessible uniquement par les applications déployées via Azure RemoteApp et non par une application s’exécutant directement sur le PC d’un utilisateur et accédant à Internet public. Azure RemoteApp est souvent considéré comme l’environnement sécurisé et à gestion centralisée, et donc comme le seul chemin d’accès par le biais duquel les utilisateurs doivent interagir avec la ressource principale.

La solution consiste à placer l’environnement Azure RemoteApp et la ressource sécurisée dans le même réseau Azure Virtual Network (réseau virtuel). Si la ressource se trouve dans un autre site, vous pouvez établir une connexion VPN site à site, par exemple pour créer un réseau virtuel s’étendant sur le centre de données Azure et l’environnement local du client.

Azure RemoteApp prend en charge deux types de déploiement de collection dans lesquels vous pouvez fournir votre propre réseau virtuel :

-	Non joint à un domaine : les autres ressources du réseau virtuel sont dans la ligne de mire des applications. Par exemple, ce type peut servir à connecter des applications à une base de données SQL qui utilise l’authentification SQL (les applications authentifient l’utilisateur directement dans la base de données).

-	Joint à un domaine : les machines virtuelles utilisées par Azure RemoteApp sont jointes à un contrôleur de domaine dans le réseau virtuel. Cela s’avère utile lorsque les applications doivent s’authentifier auprès d’un contrôleur de domaine Windows pour accéder à une ressource principale. ![Collection jointe à un domaine dans Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### Comment créer une connexion sécurisée entre Azure et mon environnement local
Plusieurs options de configuration permettent de connecter vos environnements Azure et local. Une vue d’ensemble des options est disponible ici.

Avec Azure RemoteApp, vous devez d’abord configurer votre réseau virtuel et l’utiliser pendant le processus de création de votre collection.

## La solution complète
Le diagramme ci-dessous illustre la solution complète dans laquelle nous avons créé un canal d’accès sécurisé de l’utilisateur final, via Azure RemoteApp (ARA), à la ressource principale. ![Sécuriser Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) Dans l’étape 1, nous avons sélectionné les utilisateurs et créé des règles d’accès qui régissent l’accès à Azure RemoteApp. Dans l’exemple ci-dessous, nous autorisons uniquement l’accès aux utilisateurs qui travaillent à partir du réseau d’entreprise. Les utilisateurs qui ne remplissent pas cette condition ne peuvent pas accéder du tout à l’environnement Azure RemoteApp. Dans l’étape 2, nous avons exposé la ressource principale uniquement via la configuration VNet (réseau virtuel)/VPN que nous contrôlons. Azure RemoteApp a été placé dans le même réseau virtuel. Le résultat final est que la ressource est uniquement accessible via l’environnement Azure RemoteApp.

<!---HONumber=AcomDC_0817_2016-->