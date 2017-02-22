---
title: Azure Government Operations Management Suite | Microsoft Docs
description: "Cet article décrit comment Log Analytics dans Operations Management Suite s’applique aux agences et fournisseurs de solutions du gouvernement américain"
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Cybersécurité Azure Government : Surveillance et sécurisation de vos ressources avec Operations Management Suite 

## <a name="cybersecurity-in-the-cloud"></a>Cybersécurité dans le cloud
L’une des questions essentielles des clients qui procèdent à une migration vers le cloud est la conservation de la gestion et de la sécurité des ressources des services Azure Government qu’ils ont déployés dans le cloud. Les pare-feu des machines virtuelles doivent être correctement configurés. Les groupes de sécurité réseau appropriés doivent être appliqués sur les réseaux virtuels. L’accès à vos ressources doit être verrouillé au moment opportun. L’ensemble de ces processus correspondent aux flux de travail à planifier, concevoir et à configurer pour la prise en charge d’une infrastructure sécurisée que votre agence mettra à profit.

La configuration d’un tel environnement peut s’avérer difficile. L’intégration de votre parc de serveurs à un service de surveillance est une opération difficile à mettre l’échelle, et il peut également être difficile de mettre à jour le service de surveillance. La surveillance de l’infrastructure sur différents fournisseurs cloud, ainsi que sur le cloud et sur site, s’avère difficile. Enfin, l’actualisation de votre surveillance et l’activation d’Azure Application Insights pour surveiller, détecter, alerter et traiter les menaces de cybersécurité sont des opérations qui nécessitent du temps, des ressources et de la puissance de calcul.

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
Microsoft Operations Management Suite, désormais disponible dans Azure Government, est un ensemble de services Azure qui vous permet d’effectuer toutes ces tâches rapidement et facilement. Cet article se concentre sur l’utilisation de Log Analytics, qui emploie la recherche de journaux hyperscale pour analyser rapidement vos données et exposer les menaces dans votre environnement.

Azure Log Analytics peut :

* déployer des agents sur des machines virtuelles individuelles (Linux et Windows) sur Azure, d’autres fournisseurs cloud et sur site.
* connecter vos fichiers journaux existants via un compte de stockage Azure Government ou un point de terminaison System Center Operations Manager avec des données de journalisation existantes.

Voyons comment nous pouvons intégrer Log Analytics à votre flotte et examinons quelques-unes des solutions standard qui répondent aux problèmes que nous avons décrits ici.

## <a name="onboarding-servers-to-log-analytics"></a>Intégration des serveurs sur Log Analytics
La première étape dans l’intégration de vos ressources cloud avec Log Analytics est l’installation de l’agent Log Analytics sur toutes les sources de journalisation. Pour Machines virtuelles, cette opération est très simple, car vous pouvez télécharger manuellement l’agent à partir du portail Log Analytics.

![Figure 1 : Serveurs Windows connectés à Operations Management Suite](./media/documentation-government-oms-figure1.png)
<p align="center">Figure 1 : serveurs Windows connectés à Log Analytics</p>

Vous pouvez connecter les machines virtuelles Azure à Log Analytics directement via le portail Azure. Pour les instructions complètes, consultez la page [Nouvelles façons d’activer Log Analytics sur vos machines virtuelles Azure](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

Vous pouvez également procéder à la connexion par programme ou configurer l’extension de machine virtuelle Log Analytics directement dans vos modèles Azure Resource Manager. Suivez les instructions pour les ordinateurs Windows dans [Connecter des ordinateurs Windows à Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) et celles pour Linux dans [Connecter des ordinateurs Linux à Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>Intégration des comptes de stockage et d’Operations Manager à Log Analytics
Log Analytics peut également se connecter à votre compte de stockage et/ou à vos déploiements System Center Operations Manager existants afin de prendre en charge des scénarios hybrides (sur les fournisseurs cloud ou sur des infrastructures cloud/en local) de gestion des opérations.

![Figure 2 : connexion du stockage Azure et d’Operations Manager à Log Analytics](./media/documentation-government-oms-figure2.png)
<p align="center">Figure 2 : connexion du stockage Azure et d’Operations Manager à Log Analytics</p>

Log Analytics prend également en charge la collecte des informations de journalisation d’autres services de surveillance, comme Chef ou Puppet. En outre, pour les déploiements Azure, nous disposons de machines virtuelles avec des modèles Azure Resource Manager configurés pour Log Analytics, ce qui vous permet de simultanément déployer les ressources de calcul et procéder à l’intégration à votre espace de travail Log Analytics.

![Figure 3 : Modèles Azure Resource Manager pour les machines virtuelles Azure avec extension de machine virtuelle Log Analytics](./media/documentation-government-oms-figure3a.png)
![Figure 3 : Modèles Azure Resource Manager pour les machines virtuelles Azure avec extension de machine virtuelle Log Analytics](./media/documentation-government-oms-figure3b.png)
<p align="center">Figure 3 : Modèles Azure Resource Manager pour les machines virtuelles Azure avec extension de machine virtuelle Log Analytics</p>

Vous trouverez plus d’informations sur la configuration de Log Analytics avec votre implémentation locale Operations Manager existante dans [Connexion d’Operations Manager à Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>Application de l’analyse décisionnelle via les packs de solutions Operations Management Suite
Maintenant que vous disposez de diverses sources pour la journalisation de données, vous devez rendre ces données intelligibles.

Log Analytics, en son cœur, est un service de recherche de journaux prenant en charge l’écriture de puissantes requêtes interrogeant des milliers, voire des millions de documents. Toutefois, il peut être très difficile de découvrir les problèmes à l’origine des requêtes que vous formulez.

Accédez aux solutions d’Operations Management Suite. Il s’agit de packs de requêtes qui sont intégrés de base à Log Analytics pour vous donner, de façon proactive, des informations sur votre flotte gérée par Log Analytics.

Sur le thème de la cybersécurité, j’évoquerai brièvement trois scénarios de cybersécurité que Log Analytics peut résoudre en standard.

### <a name="antimalware-assessment"></a>Analyse anti-programme malveillant
L’analyse anti-programme malveillant vous procure un ensemble de tableaux de bord de requêtes, de notifications et de surveillance afin de vous octroyer une visibilité en temps réel sur l’efficacité de la protection de votre parc contre les logiciels malveillants.

Ce tableau de bord vous donne une liste des quatre éléments suivants :
* Les serveurs présentant des menaces actives et/ou corrigées.
* Les menaces actuellement détectées.
* Les ordinateurs non suffisamment protégés. Log Analytics trouve ces informations en indexant les fichiers journaux de vos ordinateurs et identifie les sites de pare-feu ouverts ou les règles configurées de manière inappropriée dans les navigateurs web communs.
* Analyse de la façon dont vos serveurs protégés sont protégés, par exemple par une protection antivirus native du système d’exploitation Windows ou une solution telle que System Center Endpoint Protection.

Par exemple, vous constatez que la menace identifiée suivante a été automatiquement triée par System Center :

![Figure 4 : solution d’analyse anti-programme malveillant Operations Management Suite](./media/documentation-government-oms-figure4.png)
<p align="center">Figure 4 : solution d’analyse anti-programme malveillant Operations Management Suite</p>

Vous trouverez plus d’informations sur l’évaluation des programmes malveillants dans l’article [Solution d’évaluation des programmes malveillants dans Log Analytics](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/).

### <a name="identity-and-access"></a>Identité et accès
La compromission des informations d’identification représente un autre scénario commun de cybersécurité. Votre abonnement au cloud présente des informations d’identification, mais la sécurité ne s’arrête pas là. Chaque machine virtuelle se voit attribuer un utilisateur et/ou une clé secrète (habituellement un certificat ou un mot de passe).

Log Analytics organise l’ensemble des tentatives de connexion dans votre parc et les répertorie en fonction du type (distante, locale, par nom d’utilisateur et ainsi de suite). Par exemple, dans l’exemple ci-dessous, j’identifie une quantité considérable de tentatives de connexion mises en échec, provenant de noms d’utilisateur sous la forme de chaînes largement aléatoires. Cela indique qu’il est très probable que mes ordinateurs ont été touchés, et qu’ils ne sont pas correctement protégés par les pare-feu et les listes de contrôle d’accès.

![Figure 5 : Échec de 97,3 % des tentatives de connexion au cours des dernières 24 h](./media/documentation-government-oms-figure5.png)
<p align="center">Figure 5 : Échec de 97,3 % des tentatives de connexion au cours des dernières 24 h</p>

### <a name="threat-intelligence"></a>Informations sur les menaces
Log Analytics fournit également une protection contre les scénarios d’attaques malveillantes en interne, dans les situations où la sécurité de votre organisation est compromise et qu’un utilisateur malintentionné essaie d’exfiltrer des données.

Log Analytics Threat Intelligence examine l’ensemble des fichiers journaux du réseau sur votre ordinateur, et vérifie automatiquement l’absence de connexions réseau entrantes/sortantes à des adresses IP malveillantes (par exemple, des adresses IP sur le darknet non indexé) ; le cas échéant, les informations vous sont communiquées.

Par exemple, dans la capture d’écran suivante, je constate l’existence de connexions réseau entrantes et sortantes vers la République populaire de Chine.

En double-cliquant sur la balise entrante, je découvre qu’une machine virtuelle Linux gérée par Log Analytics établit des connexions sortantes vers une adresse IP connue du darknet en Chine.

Vous pouvez également définir des alertes sur des solutions Operations Management Suite, comme les informations sur les menaces. Dans la capture d’écran suivante, nous avons configuré une alerte afin que Log Analytics envoie une alerte par message électronique s’il détecte plus de 10 connexions sortantes vers une adresse IP malveillante connue. Je configure ensuite cette alerte pour le déclenchement d’un travail Azure Automation, dédiée à l’arrêt automatique de cette machine virtuelle.

![Figure 6 : alertes et automatisation d’Operations Management Suite](./media/documentation-government-oms-figure6.png)
<p align="center">Figure 6 : alertes et automatisation d’Operations Management Suite</p>

Il s’agit simplement d’une solution Operations Management Suite standard qui peut être appliquée à votre parc, qu’il soit exécuté sur Azure, un autre fournisseur de service cloud ou sur site.

Operations Management Suite continuera à mettre automatiquement à jour son apprentissage automatique pour combattre les dernières menaces ; nous continuerons également à déployer de nouvelles solutions sur l’Azure Marketplace.

Pour plus d’informations sur Operations Management Suite, consultez [notre page de documentation](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/).



<!--HONumber=Jan17_HO1-->


