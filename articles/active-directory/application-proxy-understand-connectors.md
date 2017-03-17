---
title: "Présentation des connecteurs de proxy d’application Azure AD | Microsoft Docs"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 41d6f678dba769cf7f949751da8cacf3df7f88c1
ms.lasthandoff: 03/03/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Présentation des connecteurs de proxy d’application Azure AD

Cet article traite des connecteurs, qui sont l’ingrédient secret de du proxy d’application Azure AD. Ils sont très puissants, simples et faciles à déployer et maintenir.

> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

## <a name="what-are-azure-ad-application-proxy-connectors"></a>Que sont les connecteurs de proxy d’application Azure AD ?
Le Proxy d’application fonctionne après l’installation d’un service Windows Server léger appelé connecteur sur votre réseau. Vous pouvez installer des connecteurs en fonction de vos besoins en évolutivité et en haute disponibilité. Commencez avec un connecteur et en ajoutez-en d’autres en fonction de vos besoins. Chaque fois qu’un connecteur est installé, il est ajouté au pool de connecteurs qui sert votre client.

Nous vous recommandons de ne pas pour installer de connecteurs sur les serveurs d’application eux-mêmes, même si cela est possible, en particulier pour de petits déploiements.

Il est inutile de supprimer les connecteurs que vous n’utilisez plus. Lorsqu’un connecteur est en cours d’exécution, il reste actif car il se connecte au service. Les connecteurs inutilisés sont marqués comme _inactifs_ et sont supprimés après 10 jours d’inactivité. 

Pour plus d’informations sur la résolution des problèmes de connectivité d’Azure AD, consultez [Guide pratique de résolution des problèmes de connectivité au proxy d’application Azure AD](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems). 

## <a name="what-are-the-cloud-rules-for-connectors"></a>Quelles sont les règles cloud pour les connecteurs ?
Les connecteurs et le service se chargent de toutes les tâches de haut niveau de disponibilité. Vous pouvez les ajouter ou supprimer de manière dynamique. Chaque fois qu’une nouvelle requête arrive, elle est acheminée vers un des connecteurs actuellement disponibles. Si un connecteur est temporairement indisponible, il ne répondra donc pas à ce trafic.

Les connecteurs sont sans état et n’ont aucune donnée de configuration sur l’ordinateur, autre que la connectivité avec les paramètres de service et le certificat qui authentifie ce connecteur. Lorsqu’ils se connectent au service, ils extraient toutes les données de configuration requises et les actualisent toutes les deux minutes.
Ils interrogent également le serveur pour déterminer s’il existe une version plus récente du connecteur. S’il en existe une, les connecteurs se mettent à jour.

Vous pouvez surveiller vos connecteurs à partir de l’ordinateur sur lequel ils s’exécutent, soit à l’aide du journal des événements et des compteurs de performances soit à partir du cloud avec la page État du connecteur, comme illustré ci-dessous.

 ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>Tous les réseaux sont sortants
Les connecteurs envoient uniquement des demandes sortantes, afin que la connexion soit toujours initiée par le ou les connecteurs. Il n’est pas nécessaire pour ouvrir des ports d’entrée, car une fois qu’une session a été établie, le trafic passe dans les deux sens.

Le trafic sortant est envoyé au service de proxy d’application et aux applications publiées. Le trafic vers le service est envoyé aux centres de données Azure sur plusieurs ports différents. Pour plus d’informations, consultez [ Activer le proxy d’application dans portail Azure](active-directory-application-proxy-enable.md).

Conséquence de la présence seule de trafic sortant, il est inutile de configurer d’équilibrage de charge entre les connecteurs ou de configurer l’accès entrant à travers vos pare-feu.

Pour plus d’informations sur la configuration des règles sortantes de pare-feu, consultez [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md).

Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue. 

## <a name="network-security"></a>Sécurité du réseau

Les connecteurs peuvent être installés n’importe où sur le réseau qui leur permet d’envoyer des requêtes à la fois vers le service et les applications principales. Ils fonctionnent si vous les avez installés à l’intérieur du réseau d’entreprise, au sein d’une zone démilitarisée (DMZ) ou même sur une machine virtuelle qui est en cours d’exécution dans le cloud qui a accès aux applications.

Les déploiements DMZ sont généralement plus complexes. Mais une des raisons de déployer des connecteurs dans la zone démilitarisée est d’utiliser une autre infrastructure qui est disponible pour les composants en cours d’exécution, par ex. les équilibreurs de charge d’application principale et/ou les contrôles de sécurité comme les systèmes de détection des intrusions.

## <a name="domain-joining"></a>Jonction de domaine

Les connecteurs peuvent s’exécuter sur une machine qui n’est pas jointe à un domaine. Toutefois, un ordinateur joint à un domaine est requis si vous choisissez d’utiliser l’authentification unique (SSO) pour les applications qui utilisent l’authentification Windows intégrée (IWA). 

Dans ce cas, les ordinateurs du connecteur doivent être joints à un domaine qui peut effectuer la délégation [Kerberos](https://web.mit.edu/kerberos) contrainte pour le compte des utilisateurs appropriés pour les applications publiées.

Les connecteurs peuvent également être joints à des domaines ou forêts qui disposent d’une approbation partielle ou pour les contrôleurs de domaine en lecture seule (RODC).

## <a name="connectors-on-hardened-environments"></a>Connecteurs sur les environnements de sécurité renforcés

Dans la plupart des cas, le déploiement de connecteurs est très simple et ne nécessite aucune configuration spéciale. Cependant, il existe plusieurs conditions uniques qui doivent être prises en considération :

* Les organisations qui limitent le trafic sortant doivent suivre les instructions qui suivent pour ouvrir les ports requis.
* Des machines conformes aux normes FIPS peuvent être requises pour modifier leur configuration afin d’autoriser le service du connecteur, le service de mise à jour du connecteur et son programme d’installation à générer et stocker un certificat sur cet ordinateur.
* Les organisations qui verrouillent leur environnement sur la base des processus qui émettent des requêtes réseau doivent s’assurer que les deux services de connecteur sont activés pour accéder à tous les ports et adresses IP nécessaires.
* Dans certains cas, les proxys de transfert sortants peuvent arrêter l’authentification de certificat bidirectionnelle et entraîner un échec de la communication.

## <a name="all-connectors-are-created-almost-equal"></a>Tous les connecteurs sont créés presque égaux

Tous les connecteurs sont censés être identiques aux autres, et chaque requête entrante peut arriver sur n’importe lequel des connecteurs. Cela signifie que tous les doivent avoir la même connectivité réseau et les mêmes paramètres [Kerberos](https://web.mit.edu/kerberos).

Toutes les communications de connecteur à service sont protégées par un certificat client qui est émis, puis installé sur l’ordinateur du connecteur. Pour plus d’informations sur le renouvellement des certificats de connecteur, consultez [Activer le proxy d’application dans le Portail Azure](active-directory-application-proxy-enable.md).

## <a name="connector-authentication"></a>Authentification du connecteur

Afin de fournir un service sécurisé, les connecteurs doivent s’authentifier sur le service, et le service doit s’authentifier sur les connecteurs. Cette opération est effectuée à l’aide de certificats client et serveur lorsque les connecteurs établissent la connexion. De cette façon, le nom d’utilisateur et le mot de passe de l’administrateur ne sont pas stockés sur l’ordinateur du connecteur.

Les certificats utilisés sont spécifiques au service de proxy d’application. Ils sont créés lors de l’inscription initiale et sont renouvelés automatiquement par les connecteurs tous les quelques mois. 

Si un connecteur n’est pas connecté au service pendant une période de plusieurs mois, il peut avoir des certificats expirés. Dans ce cas, l’inscription est requise, et vous devez donc désinstaller et réinstaller le connecteur pour déclencher l’inscription. Vous pouvez exécuter les commandes PowerShell suivantes :

```
* Import-module AppProxyPSModule
* Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Performances et évolutivité

Bien que la mise à l’échelle pour le service en ligne est transparente, l’échelle est un facteur lorsqu’il s’agit de connecteurs. Vous devez disposer de suffisamment de connecteurs pour gérer les pics de trafic. Étant donné que les connecteurs sont sans état, ils ne sont pas dépendants du nombre d’utilisateurs ou de sessions. Ils dépendent plutôt du nombre de requêtes et de leur taille de charge utile. Pour le trafic web standard, nous avons vu qu’une machine moyenne gérait quelques milliers de requêtes par seconde. Cela dépend des caractéristiques exactes de la machine.

Les performances du connecteur sont liées au processeur et à la mise en réseau. Les performances processeur sont nécessaires pour le chiffrement et le déchiffrement SSL, tandis que la mise en réseau est vitale pour obtenir une connectivité rapide aux applications et au service en ligne dans Azure. En revanche, la mémoire est moins problématique pour les connecteurs.

Pour le service du connecteur, nous faisons un effort pour décharger les connecteurs autant que possible. Le service en ligne s’occupe de la majeure partie du traitement et de tout le trafic non authentifié. Tout ce qui peut être effectué dans le cloud est réalisé dans le cloud.

Un autre facteur jouant sur les performances est la qualité de la connexion réseau entre les connecteurs, y compris :

* _Le service en ligne :_ si la connexion est lente ou a une latence élevée. il influence le niveau de service. Il est préférable que votre organisation soit connectée à Azure via Express Route. Sinon, assurez-vous que l’équipe réseau garantit que les connexions à Azure sont gérées de façon efficace.

* _Applications principales :_ dans certains cas, il existe des proxys supplémentaires entre le connecteur et les applications principales. Il est facile de résoudre ce problème en ouvrant un navigateur à partir de l’ordinateur du connecteur et en accédant à ces applications. Si vous exécutez les connecteurs dans Azure et que les applications sont locales, l’expérience peut ne pas être celle que vos utilisateurs attendent.
* _Contrôleurs de domaine :_ si les connecteurs effectuent l’authentification unique à l’aide de la délégation Kerberos contrainte (KCD), ils contactent les contrôleurs de domaine avant d’envoyer la requête au serveur principal. Les connecteurs ont un cache de tickets Kerberos, mais dans des environnements occupés, la réactivité des contrôleurs de domaine peut ralentir l’expérience. Cela est plus courant pour les connecteurs qui s’exécutent dans Azure, tandis que les contrôleurs de domaine sont locaux.

## <a name="automatic-updates-to-the-connector"></a>Mises à jour automatiques sur le connecteur

Avec le service de mise à jour du connecteur, nous proposons une solution automatisée pour rester à jour. Ainsi, vous avez l’avantage continu de nouvelles fonctionnalités et d’améliorations de sécurité et de performances.

Azure AD prend en charge les mises à jour automatiques pour tous les connecteurs que vous déployez. Tant que le service de mise à jour du connecteur de proxy d’application est en cours d’exécution, vos connecteurs se mettent automatiquement à jour sans interruption de service, et aucune opération manuelle n’est nécessaire. Si vous ne voyez pas le service de mise à jour du connecteur sur votre serveur, vous devez réinstaller votre connecteur afin d’obtenir les mises à jour. Si vous souhaitez en savoir plus sur l’installation des connecteurs, consultez la [Documentation de l’installation](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-enable.md).

### <a name="updater-impact"></a>Impact de l’outil de mise à jour

_Clients avec un connecteur :_ si vous avez un seul connecteur, ce connecteur sera mis à jour dans le cadre du dernier groupe. Étant donné qu’aucun autre connecteur n’est présent pour rediriger le trafic via, le service n’est pas disponible pendant la mise à jour. Pour éviter ce temps d’arrêt et garantir plus facilement une haute disponibilité, nous vous recommandons d’installer un second connecteur et de créer un groupe de connecteurs. Pour plus d’informations sur la façon de procéder, consultez la [documentation sur les groupes de connecteurs](https://azure.microsoft.com/en-us/documentation/articlesactive-directory-application-proxy-connectors.md).

_Autres clients :_ pendant la mise à jour du connecteur, le trafic est redirigé vers vos autres connecteurs pour une interruption minimale. Toutefois, toutes les transactions en cours au démarrage de la mise à jour peuvent être abandonnées. Votre navigateur devrait renouveler automatiquement l’opération, ce qui rend cet arrêt éventuel transparent pour vous. Dans le cas contraire, vous devrez peut-être actualiser votre page pour résoudre ce problème.

Nous savons qu’il peut être difficile d’avoir ne serait-ce qu’une minute d’indisponibilité, mais ces mises à jour nous permettent de vous donner un meilleur connecteur avec de nombreuses améliorations, qui selon nous en valent la peine.

Si vous souhaitez plus de détails sur les modifications apportées dans nos mises à jour de connecteur récentes, consultez la [dernière mise à jour](https://azure.microsoft.com/en-us/updates/app-proxy-connector-12sept2016). Nous actualisons cette page avec chaque mise à jour.

## <a name="under-the-hood"></a>Sous le capot

Nous vous proposons beaucoup d’outils utiles dans Azure. Les connecteurs,en particulier, ont de nombreuses fonctionnalités utiles. Étant donné que les connecteurs sont basés sur le proxy d’application web Windows Server, la plupart ont les mêmes outils de gestion ; y compris l’ensemble complet de compteurs de performances et de journaux d’événements Windows, comme illustré ci-dessous dans l’observateur d’événements :

 ![Observateur d’événements AzureAD](./media/application-proxy-understand-connectors/event-view-window.png)

Et l’analyseur de performances réseau :

 ![Analyseur de performances AzureAD](./media/application-proxy-understand-connectors/performance-monitor.png)

Les connecteurs ont des journaux de session et admin. Les journaux admin incluent les événements principaux et leurs erreurs. Les journaux de session incluent toutes les transactions et les détails de traitement. 

Pour les afficher, vous devez activer « Afficher les journaux d’analyse et de débogage » dans le menu « Affichage » de l’Observateur d’événements. Ensuite, vous devez leur permettre de lancer la collecte d’événements. Ces journaux n’apparaissent pas dans le proxy d’application web dans Windows Server 2012 R2, car les connecteurs sont basés sur une version plus récente.

 ![Session d’Observateur d’événements AzureAD](./media/application-proxy-understand-connectors/event-view-window-session.png)

Vous pouvez examiner l’état du service dans la fenêtre Services. Le connecteur se compose de deux services Windows ; le premier est le connecteur lui-même et l’autre prend en charge la mise à jour. Les deux doivent toujours être en cours d’exécution.

 ![AzureAD Services Local](./media/application-proxy-understand-connectors/aad-connector-services.png)

Pour plus d’informations sur la résolution des erreurs du connecteur de proxy d’application, consultez [Résoudre les problèmes du proxy d’application](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Étapes suivantes
[Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md)

[Comment installer silencieusement le connecteur du Proxy d'application Azure AD](active-directory-application-proxy-silent-installation.md)


