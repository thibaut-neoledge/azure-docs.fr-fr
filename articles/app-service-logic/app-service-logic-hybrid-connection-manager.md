<properties 
	pageTitle="Utilisation du Gestionnaire de connexion hybride pour Azure App Service" 
	description="Installer et configurer le Gestionnaire de connexion hybride dans Azure App Service ; architecture des microservices" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>

# Utilisation du Gestionnaire de connexion hybride dans Azure App Service
Certains connecteurs peuvent se connecter à un système local, tels que SQL Server, SAP, SharePoint, etc. Pour utiliser un système local, Azure App Service utilise le Gestionnaire de connexion hybride.

Le Gestionnaire de connexion hybride (ou HCM) est un programme d'installation en un seul clic qui est installé sur un serveur IIS au sein de votre réseau, derrière le pare-feu. À l'aide d'un relais Azure Service Bus, HCM authentifie le système local avec le connecteur dans Azure.

> [AZURE.NOTE]Le Gestionnaire de connexion hybride est requis uniquement si vous vous connectez à une ressource locale derrière le pare-feu. Si vous ne vous connectez pas à un système local, alors vous n'avez pas besoin du Gestionnaire de connexion hybride.

Pour commencer, vous avez besoin des éléments suivants :

- Chaîne de connexion SAS de l'espace de noms du relais Azure Service Bus. Consultez [Tarification de Service Bus](http://azure.microsoft.com/pricing/details/service-bus/) pour déterminer quel niveau inclut des relais.
- Informations de connexion au système local, y compris le nom d'utilisateur et le mot de passe. Par exemple, si vous vous connectez à un serveur SQL Server local, vous avez besoin du compte et du mot de passe de connexion à SQL Server.
- Informations du serveur local, y compris le nom du serveur et le numéro de port. Par exemple, si vous vous connectez à un serveur SQL Server local, vous avez besoin du nom du serveur SQL Server et du numéro de port TCP.

## Obtenir la chaîne de connexion Service Bus

Dans le portail Azure, copiez la chaîne de connexion SAS racine de Service Bus. Cette chaîne de connexion connecte votre connecteur Azure à votre système local.

1. Dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), sélectionnez l'espace de noms Service Bus et sélectionnez les **informations de connexion** :

	![][SB_ConnectInfo]

2. Copiez la chaîne de connexion SAS :

	![][SB_SAS]

## Installer le Gestionnaire de connexion hybride

1. Dans la version préliminaire du [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), sélectionnez le connecteur que vous avez créé. Pour l'ouvrir, vous pouvez sélectionner **Parcourir**, **API Apps**, puis sélectionnez le connecteur ou une application API. <br/><br/> Sous **Connexion hybride**, l'installation est **incomplète** : <br/> ![][2] 

2. Sélectionnez **Connexion hybride**. La chaîne de connexion Service Bus entrée précédemment est répertoriée.
3. Copiez la **chaîne de configuration principale** : <br/> ![][PrimaryConfigString]

4. Sous **Gestionnaire de connexion hybride local**, vous pouvez télécharger le Gestionnaire de connexion hybride ou l'installer directement à partir du portail. <br/><br/> Pour l'installer directement à partir du portail, accédez à votre serveur IIS local, parcourez le portail et sélectionnez **Télécharger et configurer**. <br/><br/> Pour télécharger le Gestionnaire de connexion hybride, accédez à votre serveur IIS local et accédez à l'**application ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). L'installation démarre automatiquement afin que vous puissiez l'exécuter.

5. Dans la fenêtre **Configuration de l'écouteur**, entrez la **chaîne de configuration principale** collée précédemment (à l'étape 3) et sélectionnez **Installer**.

Une fois l'installation terminée, les éléments suivants s'affichent : <br/> ![][3]

À présent, lorsque vous accédez de nouveau au connecteur, l'état de la connexion hybride est **Connecté**. Vous devrez peut-être fermer le connecteur et le rouvrir : <br/> ![][4]

> [AZURE.NOTE]Pour basculer vers la chaîne de connexion secondaire, réexécutez le programme d'installation de la connexion hybride et entrez la **chaîne de configuration secondaire**.


## Ports TCP et sécurité

Lorsque vous créez une connexion hybride, un site Web est créé sur votre serveur IIS local. Le serveur IIS peut se trouver dans une zone DMZ. La configuration du port TCP sur le serveur IIS inclut :

Port TCP | Pourquoi
--- | ---
 | Aucun port TCP entrant n'est requis.
9350 - 9354 | Ces ports sont utilisés pour la transmission de données. Le Gestionnaire de relais Service Bus sonde le port 9350 pour déterminer si la connectivité TCP est disponible. Si elle est disponible, il suppose que le port 9352 est également disponible. Le trafic de données passe par le port 9352. <br/><br/>Autoriser les connexions sortantes à ces ports.
5671 | Lorsque le port 9352 est utilisé pour le trafic de données, le port 5671 est utilisé en tant que canal de contrôle. <br/><br/>Autoriser les connexions sortantes à ce port. 
80, 443 | Si les ports 9352 et 5671 ne sont pas utilisables, *alors* les ports 80 et 443 sont les ports de secours utilisés pour la transmission de données et le canal de contrôle.<br/><br/>Autoriser les connexions sortantes à ces ports.
Port du système local | Sur le système local, ouvrez le port utilisé par le système. Par exemple, SQL Server utilise généralement le port 1433. Ouvrez ce port TCP.

[Hébergement derrière un pare-feu avec Service Bus](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## Résolution de problèmes

![][HCMFlow]

### Résolution de problèmes en local

1. Sur le serveur IIS, vérifiez que le rôle Web IIS est installé et que tous les services IIS sont démarrés.
2. Sur le serveur IIS, vérifiez que le Gestionnaire de connexion hybride est installé et en cours d'exécution :
 - Dans le Gestionnaire des services Internet (inetmgr), le site Web ***MicrosoftAzureBizTalkHybridListener*** doit être répertorié et en cours d'exécution. 
 - Ce site Web utilise ***HybridListenerAppPool*** qui s'exécute comme compte utilisateur prédéfini local de *NetworkService*. Ce pool d'applications doit également être démarré.
3. Sur le serveur IIS, vérifiez que le connecteur est installé et en cours d'exécution : 
 - Un site Web est créé pour le connecteur App Service. Par exemple, si vous avez créé un connecteur SQL, il existe un site Web ***MicrosoftSqlConnector\_nnn***. Dans le Gestionnaire des services Internet (inetmgr), confirmez que ce site Web est répertorié et démarré. 
 - Ce site Web utilise son propre pool d'applications IIS nommé ***HybridAppPoolnnn***. Ce pool d'applications s'exécute en tant que compte utilisateur prédéfini local de *NetworkService*. Ce site Web et le pool d'applications doivent tous deux être démarrés. 
 - Parcourez le connecteur local. Par exemple, si le site Web du connecteur utilise le port 6569, accédez à http://localhost:6569. Un document par défaut n'est pas configuré, donc une `HTTP Error 403.14 - Forbidden error` est attendue.
4. Dans le pare-feu, vérifiez que les ports TCP répertoriés dans cette rubrique sont ouverts.
5. Examinez le système source ou de destination :
 - Certains systèmes locaux nécessitent des fichiers de dépendance supplémentaires. Par exemple, si vous vous connectez à un SAP local, certains fichiers SAP supplémentaires doivent être installés sur le serveur IIS.
 - Vérifiez la connectivité au système avec le compte de connexion. Par exemple, le port TCP utilisé par le système doit être ouvert, comme le port 1433 pour SQL Server. Le compte de connexion que vous avez entré dans le portail Azure doit avoir accès au système.
6. Sur le serveur IIS, vérifiez les journaux des événements pour rechercher des erreurs. 
7. Nettoyez et réinstallez le Gestionnaire de connexion hybride : 
 - Dans IIS, supprimez manuellement le site Web du connecteur et son pool d'applications. 
 - Exécutez à nouveau le Gestionnaire de connexion hybride et confirmez que vous entrez la bonne **chaîne de configuration principale** pour votre connecteur.



### Dans le portail Azure

1. Vérifiez que l'espace de noms Service Bus a un état **Actif**.
2. Lorsque vous créez le connecteur, entrez la chaîne de connexion SAS Service Bus. N'entrez pas la chaîne de connexion ACS.


## Forum Aux Questions

**Question** : il existe deux gestionnaires de connexion hybride. Quelle est la différence ?<br/> **Réponse** : il existe la technologie des [connexions hybrides](../integration-hybrid-connection-overview.md) qui est utilisée principalement par les applications Web (anciennement les sites Web) et les applications mobiles (anciennement les services mobiles) pour se connecter en local. Ce Gestionnaire de connexion hybride est son propre [programme d'installation](../integration-hybrid-connection-create-manage.md) et utilise un service Azure BizTalk (en arrière-plan). Il prend en charge les protocoles TCP et HTTP uniquement.

Avec les connecteurs Azure App Service, il existe également un Gestionnaire de connexion hybride. Ce Gestionnaire de connexion hybride n'utilise *pas* un service Azure BizTalk (en arrière-plan) et prend en charge davantage que les protocoles TCP et HTTP. Consultez la [Liste de connecteurs et d'applications API](app-service-logic-connectors-list.md).

Les deux utilisent Azure Service Bus pour se connecter au système local.

**Question** : lorsque je crée une application API personnalisée, puis-je utiliser le Gestionnaire de connexion hybride App Service pour la connexion au système local ? <br/> **Réponse** : pas dans le sens traditionnel. Vous pouvez utiliser un connecteur intégré, configurer le Gestionnaire de connexion hybride App Service pour la connexion au système local. Puis, utilisez ce connecteur avec votre application API personnalisée, probablement avec une application logique. Actuellement, vous ne pouvez pas développer ni créer votre propre application API hybride (comme le connecteur SQL ou le connecteur File).

Si votre API personnalisée utilise un port TCP ou HTTP, vous pouvez utiliser les [connexions hybrides](../integration-hybrid-connection-overview.md) et le Gestionnaire de connexion hybride. Dans ce scénario, un service Azure BizTalk est utilisé. La [connexion à un serveur SQL Server local à partir d'une application Web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) peut être utile.


## En savoir plus

[Analyser vos applications logiques](app-service-logic-monitor-your-logic-apps.md)<br/> [Tarification de Service Bus](http://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=August15_HO6-->