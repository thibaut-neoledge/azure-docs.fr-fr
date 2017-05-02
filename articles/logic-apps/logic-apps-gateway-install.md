---
title: "Installation d’une passerelle de données locale - Azure Logic Apps | Microsoft Docs"
description: "Installez une passerelle de données locale pour accéder aux données locales à partir de vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b9971117d5f61669a5161a28c96b11b2fd600b61
ms.lasthandoff: 04/14/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>Installation d’une passerelle de données locale pour Azure Logic Apps

La passerelle de données locale prend en charge les connexions suivantes :

*   BizTalk Server
*   DB2  
*   Système de fichiers
*   Informix
*   MQ
*   MySQL
*   Oracle Database 
*   Serveur d’applications SAP 
*   Serveur de messagerie SAP
*   SharePoint pour le protocole HTTP uniquement, pas le protocole HTTPS
*   SQL Server
*   Teradata

Pour plus d’informations sur ces connexions, consultez la [liste des connecteurs Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="installation-and-configuration"></a>Installation et configuration

### <a name="requirements"></a>Configuration requise

Minimum :

* .NET Framework 4.5
* Version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

Recommandé :

* Processeur 8 cœurs
* 8 Go de mémoire
* Version 64 bits de Windows 2012 R2 (ou version ultérieure)

Considérations connexes :

* Installez la passerelle de données locale uniquement sur un ordinateur local.
Vous ne pouvez pas installer la passerelle sur un contrôleur de domaine.

* N’installez pas la passerelle sur un ordinateur qui risque de mettre hors tension, de se mettre en veille ou de ne pas réussir à se connecter à Internet, car la passerelle ne peut pas s’exécuter dans de telles circonstances. De même, les performances de la passerelle peuvent être moindres sur un réseau sans fil.

* Vous ne pouvez utiliser qu’une adresse de messagerie professionnelle ou scolaire dans Azure pour associer la passerelle de données locale à votre compte Azure Active Directory.

    Si vous utilisez un compte Microsoft (par exemple, @outlook.com), vous pouvez utiliser votre compte Azure pour   [créer une adresse de messagerie professionnelle ou scolaire](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

### <a name="install-the-gateway"></a>Installation de la passerelle

1.    [Téléchargez ici le programme d’installation de la passerelle de données locale](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2.    Spécifiez le mode **Passerelle de données locale**.

3. Connectez-vous avec votre compte professionnel ou scolaire Azure. 

4. Configurez une nouvelle passerelle ; sinon, vous pouvez migrer, restaurer ou reprendre une passerelle existante.

    Pour configurer une passerelle, attribuez un nom à votre passerelle, spécifiez une clé de récupération, puis sélectionnez **Configurer**.
  
    Spécifiez une clé de récupération contenant au moins huit caractères, et conservez-la en lieu sûr. Vous aurez besoin de cette clé si vous souhaitez migrer, restaurer ou reprendre la passerelle.

    Pour migrer, restaurer ou reprendre une passerelle existante, indiquez la clé de récupération spécifiée lors de la création de la passerelle.

### <a name="restart-the-gateway"></a>Redémarrez la passerelle

La passerelle s’exécute en tant que service Windows et, comme avec n’importe quel autre service Windows, vous pouvez la démarrer et l’arrêter de plusieurs façons. Par exemple, vous pouvez ouvrir une invite de commandes avec des autorisations élevées sur l’ordinateur sur lequel la passerelle est en cours d’exécution, puis exécuter l’une de ces commandes :

* Pour arrêter le service, exécutez cette commande :
  
    `net stop PBIEgwService`

* Pour démarrer le service, exécutez cette commande :
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configuration d’un pare-feu ou d’un proxy

Pour spécifier les informations proxy pour votre passerelle, consultez [Configurer les paramètres proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Vous pouvez vérifier si votre pare-feu ou proxy peut bloquer les connexions en exécutant la commande suivante à partir d’une invite PowerShell. Cette commande teste la connectivité à Azure Service Bus et uniquement la connexion réseau ; autrement dit, elle n’a rien à voir avec le service de serveur cloud ou avec la passerelle. Ce test permet de déterminer si votre ordinateur peut effectivement se connecter à Internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Les résultats devraient être semblables à cet exemple. Si **TcpTestSucceeded** n’affiche pas la valeur true, il est possible que vous soyez bloqué par un pare-feu.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Pour être complet, vous pouvez remplacer les valeurs **ComputerName** et **Port** par celles répertoriées dans la section [Configuration des ports](#configure-ports) de cette rubrique.

Le pare-feu peut également bloquer les connexions effectuées par Azure Service Bus vers les centres de données Azure. Si tel est le cas, vous devez approuver (débloquer) toutes les adresses IP correspondant aux centres de données de votre région.
Vous pouvez obtenir une liste [des adresses IP Azure ici](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configuration des ports
La passerelle crée une connexion sortante vers Azure Service Bus et communique sur les ports de sortie : TCP 443 (port par défaut), 5671, 5672 et 9350 à 9354. La passerelle ne nécessite pas de ports entrants.

En savoir plus sur les [solutions hybrides](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMS DE DOMAINE | PORTS SORTANTS | DESCRIPTION |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Permet de tester la connectivité Internet lorsque la passerelle est inaccessible par le service Power BI. | 

Si vous devez mettre sur liste approuvée des adresses IP au lieu des domaines, vous pouvez télécharger et utiliser la [liste des plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dans certains cas, les connexions Azure Service Bus s’effectuent avec l’adresse IP plutôt qu’avec le nom de domaine complet.

### <a name="sign-in-accounts"></a>Comptes de connexion

Vous pouvez vous connecter avec un compte professionnel ou scolaire correspondant au compte de votre organisation. Si vous avez souscrit une offre Office 365 sans fournir votre adresse de messagerie professionnelle réelle, votre adresse de connexion peut ressembler à ceci : jeff@contoso.onmicrosoft.com. Dans un service cloud, votre compte est stocké au sein d’un locataire dans Azure Active Directory (Azure AD). En règle générale, l’UPN de votre compte Azure AD correspond à l’adresse de messagerie.

### <a name="windows-service-account"></a>Compte de service Windows

Pour les informations d’identification d’ouverture de session du service Windows, la passerelle de données locale est configurée afin d’utiliser NT SERVICE\PBIEgwService. Par défaut, la passerelle détient le droit de « Se connecter en tant que service », dans le contexte de l’ordinateur sur lequel vous installez la passerelle.

Ce compte de service est différent du compte utilisé pour la connexion aux sources de données locales et du compte professionnel ou scolaire qui vous permet de vous connecter aux services cloud.

## <a name="how-the-gateway-works"></a>Fonctionnement de la passerelle
Lorsque d’autres utilisateurs interagissent avec un élément connecté à une source de données locale :

1. Le service cloud crée une requête ainsi que les informations d’identification chiffrées de la source de données, puis envoie la requête vers la file d’attente de la passerelle pour traitement.
2. Le service analyse la requête et envoie la requête vers Azure Service Bus.
3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.
4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte à la source de données avec ces informations d’identification.
5. La passerelle envoie la requête à la source de données pour exécution.
6. Les résultats sont renvoyés de la source de données vers la passerelle, puis vers le service cloud. Le service utilise ensuite les résultats.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

### <a name="general"></a>Généralités

**Question**: ai-je besoin d’une passerelle pour les sources de données dans le cloud, comme SQL Azure ? <br/>
**Réponse** : non. Une passerelle se connecte uniquement aux sources de données locales.

**Question** : comment s’appelle en fait le service Windows ?<br/>
**Réponse** : Dans Services, la passerelle s’appelle Power BI Enterprise Gateway Service.

**Question**: existe-t-il des connexions entrantes vers la passerelle à partir du cloud ? <br/>
**Réponse** : non. La passerelle utilise des connexions sortantes vers Azure Service Bus.

**Question**: que se passe-t-il si je bloque les connexions sortantes ? Que dois-je ouvrir ? <br/>
**Réponse** : vérifiez les ports et les hôtes qu’utilise la passerelle.

**Question**: la passerelle doit-elle être installée sur le même ordinateur que la source de données ? <br/>
**Réponse** : non. La passerelle se connecte à la source de données en utilisant les informations de connexion fournies. En ce sens, considérez la passerelle comme une application cliente. La passerelle doit juste être en mesure de se connecter au nom du serveur qui a été fourni.

**Question**: quelle est la latence pour exécuter des requêtes vers une source de données à partir de la passerelle ? Quelle est la meilleure architecture ? <br/>
**Réponse** : pour réduire la latence du réseau, installez la passerelle aussi près que possible de la source de données. Si vous pouvez installer la passerelle sur la source de données réelle, cette proximité réduit le temps de latence. Pensez également aux centres de données. Par exemple, si votre service utilise le centre de données États-Unis de l’Ouest et que SQL Server est hébergé sur une machine virtuelle Azure, votre machine virtuelle Azure doit également se situer dans les États-Unis de l’Ouest. Cette proximité réduit la latence et évite des frais d’acheminement sur la machine virtuelle Azure.

**Question**: existe-t-il des conditions requises concernant la bande passante réseau ? <br/>
**Réponse** : nous vous recommandons d’utiliser une connexion réseau offrant un bon débit. Chaque environnement est différent et la quantité de données envoyées affecte les résultats. L’utilisation d’ExpressRoute peut vous aider à garantir le niveau de débit entre les centres de données locaux et les centres de données Azure.

Vous pouvez utiliser l’application tierce Azure Speed Test pour mesurer votre débit.

**Question**: la passerelle du service Windows peut-elle s’exécuter avec un compte Azure Active Directory ? <br/>
**Réponse** : non. Le service Windows doit avoir un compte Windows valide. Par défaut, le service sera exécuté avec le SID du service, NT SERVICE\PBIEgwService.

**Question**: comment les résultats sont-ils renvoyés vers le cloud ? <br/>
**Réponse** : les résultats sont envoyés via Azure Service Bus.

**Question**: où mes informations d’identification sont-elles stockées ? <br/>
**Réponse** : les informations d’identification que vous entrez pour une source de données sont chiffrées et stockées dans le service cloud de la passerelle. Les informations d’identification sont déchiffrées au niveau de la passerelle locale.

### <a name="high-availabilitydisaster-recovery"></a>Haute disponibilité et récupération d'urgence
**Question**: existe-t-il des plans pour activer les scénarios de haute disponibilité avec la passerelle ? <br/>
**Réponse** : ces possibilités sont inscrites sur notre feuille de route mais nous ne pouvons pas encore donner de date.

**Question**: quelles sont les options de récupération d’urgence disponibles ? <br/>
**Réponse** : vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.

**Question**: quel avantage la clé de récupération offre-t-elle ? <br/>
**Réponse** : la clé de récupération permet de migrer ou de récupérer les paramètres de votre passerelle après un sinistre.

## <a name="troubleshooting"></a>résolution des problèmes

**Question**: où se situent les journaux de la passerelle ? <br/>
**Réponse** : consultez la section Outils plus loin dans cette rubrique.

**Question**: comment puis-je savoir quelles requêtes soient envoyées à la source de données locale ? <br/>
**Réponse** : vous pouvez activer le traçage des requêtes, y compris des requêtes envoyées. N’oubliez pas de rétablir la valeur d’origine du traçage des requêtes une fois les problèmes résolus. Le fait de laisser activé le traçage des requêtes contribue à augmenter la taille des journaux.

Vous pouvez également utiliser les outils de suivi des requêtes proposés par votre source de données. Par exemple, vous pouvez utiliser Extended Events ou SQL Profiler for SQL Server et Analysis Services.

### <a name="update-to-the-latest-version"></a>Mise à jour avec la version la plus récente

Beaucoup de problèmes peuvent survenir si la version de la passerelle est obsolète. En règle générale, veillez toujours à utiliser la version la plus récente. Si vous n’avez pas mis à jour la passerelle depuis un mois ou plus, vous pouvez installer la dernière version de la passerelle pour vérifier si le problème se reproduit.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erreur : Impossible d’ajouter l’utilisateur au groupe. (-2147463168 PBIEgwService Performance Log Users)

Cette erreur peut apparaître si vous essayez d’installer la passerelle sur un contrôleur de domaine qui n’est pas pris en charge. Veillez à déployer la passerelle sur un ordinateur qui n’est pas un contrôleur de domaine.

## <a name="tools"></a>Outils
### <a name="collect-logs-from-the-gateway-configurer"></a>Collecter des journaux à partir de l’outil de configuration de passerelle

Vous pouvez recueillir plusieurs journaux pour la passerelle. Commencez toujours par consulter les journaux !

#### <a name="installer-logs"></a>Journaux du programme d’installation

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Journaux de configuration

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Journaux du service de passerelle Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Journaux d’événements

Les journaux de passerelle de gestion des données et PowerBIGateway figurent sous **Journaux des applications et services**.

### <a name="fiddler-trace"></a>Trace Fiddler

[Fiddler](http://www.telerik.com/fiddler) est un outil gratuit développé par Telerik, qui surveille le trafic HTTP. Il permet de visualiser le trafic entre le service Power BI et l’ordinateur client. Ce service peut également indiquer les éventuelles erreurs et autres informations connexes.

## <a name="next-steps"></a>Étapes suivantes
    
* [Connexion à des données locales à partir d’applications logiques](../logic-apps/logic-apps-gateway-connection.md)
* [Fonctionnalités d’intégration d'entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md)

