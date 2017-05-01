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
ms.date: 04/02/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 5780c56a05ce1c40500927dec9df7906b02a1d13
ms.lasthandoff: 04/07/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Présentation des connecteurs de proxy d’application Azure AD

Les connecteurs rendent possible le proxy d’application Azure AD. Ils sont très puissants, simples et faciles à déployer et maintenir. Cet article présente les connecteurs, leur fonctionnement et fournissent quelques meilleures pratiques pour tirer le meilleur parti de votre déploiement. 

## <a name="connector-deployment"></a>Déploiement du connecteur

Le Proxy d’application fonctionne après l’installation d’un service Windows Server léger appelé connecteur sur votre réseau. Vous pouvez installer plusieurs connecteurs en fonction de vos besoins en évolutivité et en haute disponibilité. Commencez avec un connecteur et en ajoutez-en d’autres en fonction de vos besoins. Chaque fois qu’un connecteur est installé, il est ajouté au pool de connecteurs qui sert votre client.

Nous vous recommandons de ne pas installer les connecteurs sur les mêmes serveurs qui hébergent vos applications.

Vous n’êtes pas obligé de supprimer manuellement les connecteurs qui ne sont pas utilisés. Lorsqu’un connecteur est en cours d’exécution, il reste actif car il se connecte au service. Les connecteurs inutilisés sont marqués comme _inactifs_ et sont supprimés après 10 jours d’inactivité. 

## <a name="connector-maintenance"></a>Maintenance du connecteur
Les connecteurs et le service se chargent de toutes les tâches de haut niveau de disponibilité. Vous pouvez les ajouter ou supprimer de manière dynamique. Chaque fois qu’une nouvelle requête arrive, elle est acheminée vers un des connecteurs actuellement disponibles. Si un connecteur est temporairement indisponible, il ne répondra pas à ce trafic.

Les connecteurs sont sans état et n’ont aucune donnée de configuration sur l’ordinateur, autre que la connectivité avec les paramètres de service et le certificat qui authentifie ce connecteur. Lorsqu’ils se connectent au service, ils extraient toutes les données de configuration requises et les actualisent toutes les deux minutes.
Ils interrogent également le serveur pour déterminer s’il existe une version plus récente du connecteur. S’il en existe une, les connecteurs se mettent à jour.

Vous pouvez surveiller vos connecteurs à partir de l’ordinateur sur lequel ils s’exécutent, soit à l’aide du journal d’événements et des compteurs de performances soit à partir de la page Proxy d’application du portail Azure.

 ![Connecteurs de proxy d’application Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>Tous les réseaux sont sortants
Les connecteurs envoient uniquement des demandes sortantes, afin que la connexion soit toujours initiée par le ou les connecteurs. Il n’est pas nécessaire pour ouvrir des ports d’entrée car le trafic passe dans les deux sens une fois qu’une session a été établie.

Le trafic sortant est envoyé au service de proxy d’application et aux applications publiées. Le trafic vers le service est envoyé aux centres de données Azure sur plusieurs ports différents. Pour plus d’informations sur les ports utilisés, consultez [ Activer le proxy d’application dans portail Azure](active-directory-application-proxy-enable.md).

Conséquence de la présence seule de trafic sortant, il est inutile de configurer d’équilibrage de charge entre les connecteurs ou de configurer l’accès entrant à travers vos pare-feu.

Pour plus d’informations sur la configuration des règles sortantes de pare-feu, consultez [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md).

Utilisez [l’outil de test des ports du connecteur de proxy d’application Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) pour vous assurer que votre connecteur peut joindre le service de proxy d’application. Au minimum, assurez-vous d’avoir coché les États-Unis du Centre et les régions les plus proches de vous. En outre, un nombre plus élevé de coches vertes signifie une résilience accrue. 

## <a name="network-security"></a>Sécurité du réseau

Les connecteurs peuvent être installés n’importe où sur le réseau qui leur permet d’envoyer des requêtes à la fois vers le service et les applications principales. Ils fonctionnent correctement si vous les installez à l’intérieur du réseau d’entreprise, au sein d’une zone démilitarisée (DMZ), ou même sur une machine virtuelle. L’important est que l’ordinateur qui exécute le connecteur dispose également d’un accès à vos applications.

Les déploiements DMZ sont plus complexes. Une des raisons de déployer des connecteurs dans une zone démilitarisée est d’utiliser une autre infrastructure comme des équilibreurs de charge d’application principale ou des systèmes de détection des intrusions.

## <a name="domain-joining"></a>Jonction de domaine

Les connecteurs peuvent s’exécuter sur une machine qui n’est pas jointe à un domaine. Toutefois, si vous souhaitez utiliser l’authentification unique (SSO) pour les applications qui utilisent l’authentification Windows intégrée (IWA), vous avez besoin d’un ordinateur joint à un domaine. Dans ce cas, les ordinateurs du connecteur doivent être joints à un domaine qui peut effectuer la délégation [Kerberos](https://web.mit.edu/kerberos) contrainte pour le compte des utilisateurs appropriés pour les applications publiées.

Les connecteurs peuvent également être joints à des domaines ou forêts qui disposent d’une approbation partielle ou pour les contrôleurs de domaine en lecture seule.

## <a name="connectors-on-hardened-environments"></a>Connecteurs sur les environnements de sécurité renforcés

Généralement, le déploiement de connecteurs est simple et ne nécessite aucune configuration spéciale. Mais certaines conditions uniques doivent être prises en compte :

* Les organisations qui limitent le trafic sortant doivent [ouvrir les ports requis](active-directory-application-proxy-enable.md#open-your-ports).
* Des machines conformes aux normes FIPS peuvent être requises pour modifier leur configuration afin d’autoriser le service du connecteur, le service de mise à jour du connecteur et son programme d’installation à générer et stocker un certificat sur cet ordinateur.
* Les organisations qui verrouillent leur environnement sur la base des processus qui émettent des requêtes réseau doivent s’assurer que les deux services de connecteur sont activés pour accéder à tous les ports et adresses IP nécessaires.
* Dans certains cas, les proxys de transfert sortants peuvent arrêter l’authentification de certificat bidirectionnelle et entraîner un échec de la communication.

## <a name="connector-authentication"></a>Authentification du connecteur

Afin de fournir un service sécurisé, les connecteurs doivent s’authentifier sur le service, et le service doit s’authentifier sur les connecteurs. Cette opération est effectuée à l’aide de certificats client et serveur lorsque les connecteurs établissent la connexion. De cette façon, le nom d’utilisateur et le mot de passe de l’administrateur ne sont pas stockés sur l’ordinateur du connecteur.

Les certificats utilisés sont spécifiques au service de proxy d’application. Ils sont créés lors de l’inscription initiale et sont renouvelés automatiquement par les connecteurs tous les quelques mois. 

Si un connecteur n’est pas connecté au service pendant plusieurs mois, ses certificats ont peut-être expiré. Dans ce cas, désinstallez et réinstallez le connecteur pour déclencher l’inscription. Vous pouvez exécuter les commandes PowerShell suivantes :

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Performances et évolutivité

Bien que la mise à l’échelle pour le service en ligne est transparente, l’échelle est un facteur lorsqu’il s’agit de connecteurs. Vous devez disposer de suffisamment de connecteurs pour gérer les pics de trafic. Étant donné que les connecteurs sont sans état, ils ne sont pas affectés par le nombre d’utilisateurs ou de sessions. Ils varient plutôt selon le nombre de requêtes et leur taille de charge utile. Avec le trafic web standard, une machine moyenne peut gérer quelques milliers de requêtes par seconde. La capacité spécifique dépend des caractéristiques exactes de la machine.

Les performances du connecteur sont liées au processeur et à la mise en réseau. Les performances processeur sont nécessaires pour le chiffrement et le déchiffrement SSL, tandis que la mise en réseau est vitale pour obtenir une connectivité rapide aux applications et au service en ligne dans Azure. 

En revanche, la mémoire est moins problématique pour les connecteurs. Le service en ligne s’occupe de la majeure partie du traitement et de tout le trafic non authentifié. Tout ce qui peut être effectué dans le cloud est réalisé dans le cloud.

Un autre facteur affectant les performances est la qualité de la connexion réseau entre les connecteurs, y compris :

* **Le service en ligne :** les connexions à latence faible ou élevée affectent le service du connecteur. Il est préférable que votre organisation soit connectée à Azure via Express Route. Sinon, assurez-vous que l’équipe réseau garantit que les connexions à Azure sont gérées de façon efficace.  
* **Applications principales :** dans certains cas, il existe des proxys supplémentaires entre le connecteur et les applications principales. Résolvez ce problème en ouvrant un navigateur à partir de l’ordinateur du connecteur et en accédant à ces applications. Si vous exécutez les connecteurs dans Azure et que les applications sont locales, l’expérience peut ne pas être celle que vos utilisateurs attendent.
* **Contrôleurs de domaine :** si les connecteurs effectuent l’authentification unique à l’aide de la délégation Kerberos contrainte (KCD), ils contactent les contrôleurs de domaine avant d’envoyer la requête au serveur principal. Les connecteurs ont un cache de tickets Kerberos, mais dans un environnement occupé, la réactivité des contrôleurs de domaine peut affecter l’expérience. Ce problème est plus courant pour les connecteurs qui s’exécutent dans Azure, tandis que les contrôleurs de domaine sont locaux.

## <a name="automatic-updates-to-the-connector"></a>Mises à jour automatiques sur le connecteur

Avec le service de mise à jour du connecteur, nous proposons une solution automatisée pour rester à jour. Ainsi, vous avez l’avantage continu de nouvelles fonctionnalités et d’améliorations de sécurité et de performances.

Azure AD prend en charge les mises à jour automatiques pour tous les connecteurs que vous déployez. Tant que le service de mise à jour du connecteur de proxy d’application est en cours d’exécution, vos connecteurs se mettent automatiquement à jour. Si vous ne voyez pas le service de mise à jour du connecteur sur votre serveur, vous devez [réinstaller votre connecteur](active-directory-application-proxy-enable.md) afin d’obtenir les mises à jour.

Vous pouvez rencontrer des temps d’arrêt lors de la mise à jour de votre connecteur si :

- Vous n’avez qu’un seul connecteur. Étant donné qu’aucun autre connecteur n’est présent pour rediriger le trafic via, le service n’est pas disponible pendant la mise à jour. Pour éviter ce temps d’arrêt et optimiser la disponibilité, nous vous recommandons d’installer un second connecteur et de [créer un groupe de connecteurs](active-directory-application-proxy-connectors-azure-portal.md).

- Un connecteur se trouvait au milieu d’une transaction lorsque la mise à jour a commencé. Votre navigateur devrait automatiquement relancer l’opération, ou vous pouvez actualiser votre page. Lorsque la demande est renvoyée, le trafic est acheminé vers un connecteur de secours.

## <a name="under-the-hood"></a>Sous le capot

Comme les connecteurs sont basés sur le proxy d’application web Windows Server, la plupart ont les mêmes outils de gestion, y compris les journaux d’événements Windows

 ![Gérez les journaux d’événements avec l’Observateur d’événements](./media/application-proxy-understand-connectors/event-view-window.png)

et les compteurs de performances Windows. 

 ![Ajouter des compteurs au connecteur avec l’Analyseur de performances](./media/application-proxy-understand-connectors/performance-monitor.png)

Les connecteurs ont des journaux de session et admin. Les journaux admin incluent les événements principaux et leurs erreurs. Les journaux de session incluent toutes les transactions et les détails de traitement. 

Pour afficher les journaux, accédez l’Observateur d’événements, ouvrez le menu **Affichage**, puis activez **Afficher les journaux d’analyse et de débogage**. Ensuite, permettez-leur de lancer la collecte d’événements. Ces journaux n’apparaissent pas dans le proxy d’application web dans Windows Server 2012 R2, car les connecteurs sont basés sur une version plus récente.

Vous pouvez examiner l’état du service dans la fenêtre Services. Le connecteur compose de deux services Windows : le connecteur lui-même et l’outil de mise à jour. Les deux doivent toujours être en cours d’exécution.

 ![AzureAD Services Local](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Étapes suivantes
[Résoudre les erreurs du proxy d’application et du connecteur](active-directory-application-proxy-troubleshoot.md)

[Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md)

[Comment installer silencieusement le connecteur du Proxy d'application Azure AD](active-directory-application-proxy-silent-installation.md)


