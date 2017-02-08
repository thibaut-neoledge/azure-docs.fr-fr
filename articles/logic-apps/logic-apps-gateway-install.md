---
title: "Installation de la passerelle de données locale pour Logic Apps | Microsoft Docs"
description: "Vous trouverez ici des informations sur l’installation de la passerelle de données locale pour une utilisation dans une application logique."
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
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 4c7cd42f6cbe9519e23e781a82c017e0119a28a5


---
# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Installation de la passerelle de données locale pour les applications logiques
## <a name="installation-and-configuration"></a>Installation et configuration
### <a name="prerequisites"></a>Configuration requise
Minimum :

* .NET Framework 4.5
* Version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

Recommandé :

* Processeur&8; cœurs
* 8 Go de mémoire
* Version 64 bits de Windows 2012 R2 (ou version ultérieure)

Considérations connexes :

* Vous ne pouvez pas installer une passerelle sur un contrôleur de domaine.
* Vous ne devriez pas installer une passerelle sur un ordinateur, un portable par exemple, qui peut être mis hors tension, en veille, ou non connecté à Internet car la passerelle ne peut pas s’exécuter dans une de ces circonstances. En outre, les performances de la passerelle peuvent être moindre sur un réseau sans fil.

### <a name="install-a-gateway"></a>Installation d’une passerelle
Vous pouvez obtenir le [programme d’installation de la passerelle de données locale ici](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Spécifiez **Passerelle de données locale** comme mode, connectez-vous avec votre compte professionnel ou scolaire, puis configurez une passerelle ou migrez, restaurez ou reprenez une passerelle existante.

* Pour configurer une passerelle, tapez un **nom** et une **clé de récupération**, puis cliquez ou appuyez sur **Configurer**.
  
    Spécifiez une clé de récupération contenant au moins huit caractères, et conservez-la en lieu sûr. Vous aurez besoin de cette clé si vous souhaitez migrer, restaurer ou reprendre la passerelle.
* Pour migrer, restaurer ou reprendre une passerelle existante, indiquez la clé de récupération spécifiée lors de la création de la passerelle.

### <a name="restart-the-gateway"></a>Redémarrez la passerelle
La passerelle s’exécute comme un service Windows et, comme avec n’importe quel autre service Windows, vous pouvez la démarrer et l’arrêter de plusieurs façons. Par exemple, vous pourrez ouvrir une invite de commandes avec des autorisations élevées sur l’ordinateur sur lequel la passerelle est en cours d’exécution puis exécuter ces commandes :

* Pour arrêter le service, exécutez cette commande :
  
    `net stop PBIEgwService`
* Pour démarrer le service, exécutez cette commande :
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configuration d’un pare-feu ou d’un proxy
Pour plus d’informations sur la façon de spécifier les informations proxy pour votre passerelle, consultez [Configurer les paramètres proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Vous pouvez vérifier si votre pare-feu ou proxy peut bloquer les connexions en exécutant la commande suivante à partir d’une invite PowerShell. Cette opération testera la connectivité vers Azure Service Bus. Elle ne teste que la connectivité réseau et n’a aucun impact sur le service de serveur cloud ou la passerelle. Elle permet de déterminer si votre ordinateur peut effectivement accéder à Internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Les résultats devraient être semblables à cet exemple. Si **TcpTestSucceeded** n’affiche pas la valeur true, vous pouvez être bloqué par un pare-feu.

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

Pour être complet, vous pouvez remplacer les valeurs **ComputerName** et **Port** par celles répertoriées dans la section [Configuration des ports](#configure-ports) plus loin dans cette rubrique.

Le pare-feu peut également bloquer les connexions effectuées par Azure Service Bus vers les centres de données Azure. Dans ce cas, vous pouvez mettre sur liste approuvée (débloquer) toutes les adresses IP de votre région pour ces centres de données. Vous pouvez obtenir une liste [des adresses IP Azure ici](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configuration des ports
La passerelle crée une connexion sortante vers Azure Service Bus. Elle communique sur les ports sortants : TCP 443 (par défaut), 5671, 5672 et 9350 à 9354. La passerelle ne nécessite pas de ports entrants.

En savoir plus sur les [solutions hybrides](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMS DE DOMAINE | PORTS SORTANTS | DESCRIPTION |
| --- | --- | --- |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Récepteurs du Service Bus Relay sur TCP (nécessite le port 443 pour l’acquisition du jeton Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Permet de tester la connectivité internet si la passerelle est inaccessible par le service Power BI. |

Si vous avez besoin de mettre sur liste approuvée des adresses IP au lieu des domaines, vous pouvez télécharger et utiliser la [liste des plages d’adresses IP du centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dans certains cas, les connexions Azure Service Bus s’effectueront avec l’adresse IP plutôt qu’avec le nom de domaine complet.

### <a name="sign-in-account"></a>Compte de connexion
Les utilisateurs se connecteront avec un travail compte professionnel ou scolaire. Il s’agit du compte de votre organisation. Si vous avez souscrit une offre Office 365 sans fournir votre adresse de messagerie professionnelle réelle, le compte peut ressembler à ceci : jeff@contoso.onmicrosoft.com. Dans un service cloud, votre compte est stocké au sein d’un locataire dans Azure Active Directory (AAD). Dans la plupart des cas, l’UPN de votre compte AAD correspond à l’adresse de messagerie.

### <a name="windows-service-account"></a>Compte de service Windows
La passerelle de données locale est configurée afin d’utiliser NT SERVICE\PBIEgwService pour les informations d’identification d’ouverture de session du service Windows. Par défaut, elle est autorisée à se connecter en tant que service. Cela s’inscrit dans le contexte de l’ordinateur sur lequel vous installez la passerelle.

Ce n’est pas le compte utilisé pour se connecter à des sources de données locales ni le compte professionnel ou scolaire avec lequel vous vous connectez aux services cloud.

## <a name="frequently-asked-questions"></a>Forum Aux Questions
### <a name="general"></a>Généralités
**Question** : quelles sources de données la passerelle prend-elle en charge ?<br/>
**Réponse** : à ce jour, SQL Server.

**Question**: ai-je besoin d’une passerelle pour les sources de données dans le cloud, comme SQL Azure ? <br/>
**Réponse** : non. Une passerelle se connecte uniquement aux sources de données locales.

**Question** : comment s’appelle en fait le service Windows ?<br/>
**Réponse** : Dans Services, la passerelle s’appelle Power BI Enterprise Gateway Service.

**Question**: existe-t-il des connexions entrantes vers la passerelle à partir du cloud ? <br/>
**Réponse** : non. La passerelle utilise des connexions sortantes vers Azure Service Bus.

**Question**: que se passe-t-il si je bloque les connexions sortantes ? Que dois-je ouvrir ? <br/>
**Réponse** : vérifiez les ports et les hôtes qu’utilise la passerelle.

**Question**: la passerelle doit-elle être installée sur le même ordinateur que la source de données ? <br/>
**Réponse** : non. La passerelle se connecte à la source de données en utilisant les informations de connexion fournies. En ce sens, considérez la passerelle comme une application cliente. Elle doit juste être en mesure de se connecter au nom du serveur qui a été fourni.

**Question**: quelle est la latence pour exécuter des requêtes vers une source de données à partir de la passerelle ? Quelle est la meilleure architecture ? <br/>
**Réponse** : pour réduire la latence du réseau, installez la passerelle aussi près que possible de la source de données. Si vous pouvez installer la passerelle sur la source de données réelle, cela réduira le temps de latence. Pensez également aux centres de données. Par exemple, si votre service utilise le centre de données États-Unis de l’Ouest et que SQL Server est hébergé sur une machine virtuelle Azure, vous souhaiterez également que la machine virtuelle Azure se situe dans États-Unis de l’Ouest. Cela réduira la latence et évitera des frais d’acheminement sur la machine virtuelle Azure.

**Question**: existe-t-il des conditions requises concernant la bande passante réseau ? <br/>
**Réponse** : il est recommandé d’avoir un débit relativement élevé pour votre connexion réseau. Chaque environnement est différent et la quantité de données envoyées affecte les résultats. L’utilisation d’ExpressRoute peut vous aider à garantir le niveau de débit entre les centres de données locaux et les centres de données Azure.

Vous pouvez utiliser l’application tierce Azure Speed Test pour mesurer votre débit.

**Question**: la passerelle du service Windows peut-elle s’exécuter avec un compte Azure Active Directory ? <br/>
**Réponse** : non. Le service Windows doit avoir un compte Windows valide. Par défaut, elle sera exécutée avec le SID du service, NT SERVICE\PBIEgwService.

**Question**: comment les résultats sont-ils renvoyés vers le cloud ? <br/>
**Réponse** : cette opération est effectuée via Azure Service Bus. Pour plus d’informations, découvrez le fonctionnement de ce processus.

**Question**: où mes informations d’identification sont-elles stockées ? <br/>
**Réponse** : les informations d’identification que vous entrez pour une source de données sont stockées dans le service cloud de la passerelle. Les informations d’identification sont déchiffrées au niveau de la passerelle locale.

### <a name="high-availabilitydisaster-recovery"></a>Haute disponibilité et récupération d'urgence
**Question**: existe-t-il des plans pour activer les scénarios de haute disponibilité avec la passerelle ? <br/>
**Réponse** : cette possibilité est inscrite sur notre feuille de route mais nous ne pouvons pas encore donner de date.

**Question**: quelles sont les options de récupération d’urgence disponibles ? <br/>
**Réponse** : vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.

**Question**: quel avantage la clé de récupération offre-t-elle ? <br/>
**Réponse** : elle permet de migrer ou de récupérer les paramètres de votre passerelle après un sinistre.

### <a name="troubleshooting"></a>Résolution de problèmes
**Question**: où se situent les journaux de la passerelle ? <br/>
**Réponse** : consultez la section Outils plus loin dans cette rubrique.

**Question**: comment puis-je savoir quelles requêtes soient envoyées à la source de données locale ? <br/>
**Réponse** : vous pouvez activer le traçage des requêtes, y compris des requêtes envoyées. N’oubliez pas de rétablir la valeur d’origine une fois les problèmes résolus. Si vous laissez le traçage des requêtes activé, vous allez créer des journaux plus volumineux.

Vous pouvez également utiliser les outils de suivi des requêtes proposés par votre source de données. Par exemple, vous pouvez utiliser Extended Events ou SQL Profiler for SQL Server et Analysis Services.

## <a name="how-the-gateway-works"></a>Fonctionnement de la passerelle
Lorsqu’un utilisateur interagit avec un élément connecté à une source de données locale :

1. Le service cloud crée une requête ainsi que les informations d’identification chiffrées de la source de données, puis envoie la requête vers la file d’attente de la passerelle pour traitement.
2. Le service analyse la requête et envoie la requête vers Azure Service Bus.
3. La passerelle de données locale interroge Azure Service Bus pour connaître les requêtes en attente.
4. La passerelle reçoit la requête, déchiffre les informations d’identification et se connecte aux sources de données avec ces informations d’identification.
5. La passerelle envoie la requête à la source de données pour exécution.
6. Les résultats sont renvoyés de la source de données vers la passerelle, puis vers le service cloud. Le service utilise ensuite les résultats.

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="update-to-the-latest-version"></a>Mise à jour avec la version la plus récente
Beaucoup de problèmes peuvent survenir si la version de la passerelle est obsolète.  Nous vous recommandons de vérifier que vous disposez de la dernière version.  Si vous n’avez pas mis à jour la passerelle depuis un mois ou plus, vous pouvez installer la dernière version de la passerelle pour vérifier si le problème se reproduit.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Erreur : Impossible d’ajouter l’utilisateur au groupe. (-2147463168 PBIEgwService Performance Log Users )
Cette erreur peut apparaître si vous essayez d’installer la passerelle sur un contrôleur de domaine qui n’est pas pris en charge. Vous devrez déployer la passerelle sur un ordinateur qui n’est pas un contrôleur de domaine.

## <a name="tools"></a>Outils
### <a name="collecting-logs-from-the-gateway-configurator"></a>Collecte des journaux à partir de l’outil de configuration de passerelle
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
[Fiddler](http://www.telerik.com/fiddler) est un outil gratuit développé par Telerik, qui surveille le trafic HTTP.  Il permet de visualiser le trafic entre le service Power BI et l’ordinateur client. Il indique également les éventuelles erreurs et autres informations connexes.

## <a name="next-steps"></a>Étapes suivantes
* [Création d’une connexion locale vers les applications logiques](../logic-apps/logic-apps-gateway-connection.md)
* [Fonctionnalités d’intégration d'entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connecteurs d’applications logiques](../connectors/apis-list.md)




<!--HONumber=Jan17_HO3-->


