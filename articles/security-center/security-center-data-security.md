---
title: "Sécurité des données Azure Security Center| Microsoft Docs"
description: "Ce document explique comment les données sont gérées et protégées dans le Centre de sécurité Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0


---
# <a name="azure-security-center-data-security"></a>Sécurité des données du Centre de sécurité Azure
Pour aider les clients à prévenir, détecter et contrer les menaces, Azure Security Center collecte et traite des données de sécurité, notamment des informations de configuration, des métadonnées, des journaux d’événements et des fichiers de vidage sur incident. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service.

Cet article explique comment les données sont gérées et protégées dans le Centre de sécurité Azure.


## <a name="data-sources"></a>Sources de données
Azure Security Center analyse les données provenant des sources suivantes, afin d’assurer une visibilité sur l’état de la sécurité, d’identifier les vulnérabilités, de recommander des mesures d’atténuation et de détecter les menaces actives.

- Services Azure : utilise les informations relatives à la configuration des services Azure que vous avez déployés en communiquant avec le fournisseur de ressources de ce service.
- Trafic réseau : tire parti des métadonnées de trafic réseau échantillonnées provenant de l’infrastructure de Microsoft, telles que l’IP/le port source/de destination, la taille de paquet et le protocole réseau.
- Solutions de partenaires : collecte les alertes de sécurité des solutions de partenaires intégrées, telles que les solutions de pare-feu et anti-programme malveillant.
- Vos machines virtuelles : utilise les informations de configuration et les données relatives à des événements de sécurité, telles que des événements Windows et des journaux d’audit, des journaux IIS, des messages syslog et des fichiers de vidage sur incident, sur vos machines virtuelles.


## <a name="data-protection"></a>Protection des données
**Ségrégation des données :**les données client sont maintenues séparées logiquement sur chaque composant, dans l’ensemble du service. Toutes les données sont balisées en fonction de l'organisation. Ce balisage est conservé tout au long du cycle de vie des données, et il est appliqué dans chaque couche du service.

**Accès aux données** : pour fournir des recommandations en matière de sécurité et enquêter sur les éventuelles menaces de sécurité, le personnel de Microsoft peut accéder aux informations collectées ou analysées par les services Azure, notamment les fichiers de vidage sur incident, les événements de création de processus, les captures instantanées et artefacts de disque de machine virtuelle, qui peuvent involontairement exposer des données client ou personnelles provenant de vos machines virtuelles. Nous respectons les [Conditions d’utilisation et la Déclaration de confidentialité de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), qui indiquent que Microsoft n’utilisera pas les données client ou ne tirera pas d’informations de ces dernières à des fins commerciales, publicitaires ou similaires. Nous utilisons uniquement les données client en fonction des besoins pour vous proposer des services Azure, notamment des utilisations compatibles avec la fourniture de ces services. Vous conservez tous les droits sur les données client.

**Utilisation des données** : Microsoft utilise des modèles et des informations sur les menaces observées auprès de multiples locataires pour améliorer ses fonctionnalités de prévention et de détection. Cette utilisation s’effectue en accord avec les engagements de confidentialité décrits dans la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) de Microsoft.

## <a name="data-location"></a>Emplacement des données
**Vos comptes de stockage** : un compte de stockage est spécifié pour chaque région dans laquelle des machines virtuelles sont en cours d’exécution. Cela vous permet de stocker des données dans la même région que la machine virtuelle à partir de laquelle les données sont collectées. Ces données, y compris les fichiers de vidage sur incident, seront stockés de manière permanente dans votre compte de stockage. Les captures instantanées des disques de machine virtuelle sont stockées dans le même compte de stockage que celui de ces disques.

**Stockage Azure Security Center** : les informations relatives aux alertes de sécurité, notamment les alertes des partenaires, les recommandations et le statut d’intégrité de la sécurité, sont stockées de manière centralisée (actuellement aux États-Unis). Ces informations peuvent inclure des informations de configuration associées et des événements de sécurité collectés à partir de vos machines virtuelles, le cas échéant, pour vous fournir l’alerte de sécurité, la recommandation ou l’état d’intégrité de la sécurité.

Le Centre de sécurité Azure collecte des copies éphémères de vos fichiers de vidage sur incident et les analyse pour obtenir des preuves de tentatives d’attaque par le biais de code malveillant exploitant une faille de sécurité et de compromis ayant abouti. Azure Security Center effectue cette analyse dans la même région géographique que l’espace de travail, puis supprime les copies éphémères une fois l’analyse terminée.

Les artefacts des ordinateurs sont stockés de manière centralisée dans la même région que la machine virtuelle.


## <a name="managing-data-collection-from-virtual-machines"></a>Gestion de la collecte de données à partir de machines virtuelles
Lorsque vous choisissez d’activer le Centre de sécurité Azure, la collecte de données est activée pour chacun de vos abonnements. Vous pouvez également désactiver la collecte de données dans la section « Stratégie de sécurité » d’Azure Security Center. Lorsque la collecte de données est activée, le Centre de sécurité Azure approvisionne l’agent de surveillance Azure sur toutes les machines virtuelles prises en charge existantes et celles nouvellement créées. L’extension Surveillance de la sécurité Azure analyse diverses configurations de sécurité et crée des événements sous la forme de traces de [suivi d’événements pour Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). En outre, le système d’exploitation déclenche des événements du Journal des événements au cours de l’exécution de la machine. Il peut s’agir des données suivantes : type et version de système d’exploitation, journaux de système d’exploitation (journaux d’événements Windows), processus en cours d’exécution, nom de machine, adresses IP, utilisateur connecté et ID de locataire. L’agent de surveillance Azure lit les entrées du journal des événements et les traces ETW, puis les copie dans votre compte de stockage pour les analyser.

Vous pouvez désactiver la collecte des données à partir des machines virtuelles à tout moment, ce qui entraîne la suppression des agents de surveillance précédemment installés par le Centre de sécurité Azure. La collecte des artefacts et des captures instantanées des disques de machine virtuelle reste activée, même si la collecte de données est désactivée.


## <a name="see-also"></a>Voir aussi
Ce document explique comment les données sont gérées et protégées dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.



<!--HONumber=Feb17_HO1-->


