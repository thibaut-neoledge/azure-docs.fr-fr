---
title: Azure Government OMS | Microsoft Docs
description: "Cet article décrit le scénario OMS applicable aux agences gouvernementales et aux fournisseurs de solutions des États-Unis."
services: Azure-Government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
editor: 
translationtype: Human Translation
ms.sourcegitcommit: 1f44321bc0ec33362c64da9a19d3309c45783ed2
ms.openlocfilehash: fb38649d6f40e24849ba5a8de371b1c911089fc7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite-oms"></a>Cybersécurité Azure Government : Surveillance et sécurisation de vos ressources avec Operations Management Suite (OMS)

## <a name="cybersecurity-in-the-cloud"></a>Cybersécurité dans le cloud
L’une des priorités essentielles des clients qui procèdent à une migration vers le cloud est la conservation de la gestion et de la sécurité des ressources des services Azure Government que vous avez déployés dans le cloud. Les pare-feux des machines virtuelles doivent être correctement configurés. Les groupes de sécurité réseau appropriés doivent être appliqués sur les réseaux virtuels. L’accès à vos ressources doit être verrouillé sur les personnes appropriées, au moment opportun. L’ensemble de ces processus correspondent aux flux de travail à planifier, concevoir et à configurer pour la prise en charge d’une infrastructure sécurisée que votre agence mettra à profit.

La configuration d’un tel environnement peut s’avérer difficile. L’intégration de votre parc de serveurs pour un service de surveillance est difficile à mettre à l’échelle et à mettre à jour. La surveillance de l’infrastructure sur différents fournisseurs cloud, ainsi que sur le cloud et sur site, s’avère difficile. Enfin, l’actualisation de votre surveillance et l’activation de la lecture pour surveiller, détecter, alerter et traiter les menaces de cybersécurité sont des opérations qui nécessitent du temps, des ressources et de la puissance de calcul.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)
Microsoft Operations Management Suite (désormais disponible dans Azure Government) est un service qui vous permet d’effectuer toutes ces opérations en valorisant Map Reduce et Machine Learning en tant que service. Le service OMS peut :
* déployer des agents sur des machines virtuelles individuelles (Linux et Windows) sur Azure, d’autres fournisseurs cloud et/ou sur site ;
* connecter vos fichiers journaux existants via un compte de stockage Azure Government ou un point de terminaison SCOM avec des données de journalisation existantes ;
* exécuter des services Machine Learning et Map Reduce performants sur la durée, optimisés par la recherche de journal à très grande échelle afin d’exposer les menaces au sein de votre environnement prêt à l’emploi.

Examinons comment intégrer OMS dans votre parc et quelques-unes des solutions out-of-box dédiées aux points ci-dessus.

## <a name="onboarding-servers-to-oms"></a>Intégration des serveurs sur OMS
Pour intégrer vos ressources cloud à Operations Management Suite, la première chose à effectuer est l’installation de l’agent OMS sur vos sources de journaux.
Pour Machines virtuelles, cette opération est très simple ; vous pouvez télécharger manuellement l’agent à partir de votre espace de travail OMS.

![Figure 1 : serveurs Windows connectés à OMS](./media/documentation-government-oms-figure1.png)
<p align="center">Figure 1 : serveurs Windows connectés à OMS</p>

Vous pouvez connecter les machines virtuelles Azure à OMS directement via le portail. Consultez les instructions [ici](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

Vous pouvez également procéder à la connexion par programme et/ou configurer l’extension OMS directement dans vos modèles Azure Resource Manager. Pour bénéficier d’instructions sur les machines Windows et sur les machines Linux, accédez respectivement à [cette section](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) et à [cette section](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-scom-to-oms"></a>Intégration des comptes de stockage et des points de terminaison SCOM à OMS
OMS peut également se connecter à votre compte de stockage et/ou à vos déploiements System Center Operations Manager existants afin de prendre en charge des scénarios hybrides (sur les fournisseurs cloud ou sur des infrastructures cloud/en local) de gestion des opérations.

![Figure 2 : Connexion du stockage Azure et de SCOM à OMS](./media/documentation-government-oms-figure2.png)
<p align="center">Figure 2 : Connexion du stockage Azure et de SCOM à OMS</p>

OMS prend également en charge la consignation des informations d’autres services de surveillance, comme Chef et/ou Puppet. En outre, pour les déploiements Azure, nous disposons également de machines virtuelles avec des modèles Azure Resource Manager configurés pour OMS, ce qui vous permet de simultanément déployer les ressources de calcul et procéder à l’intégration à votre espace de travail OMS. 

![Figure 3 : Modèles Azure Resource Manager pour les machines virtuelles Azure avec extension OMS](./media/documentation-government-oms-figure3a.png)
![Figure 3 : Modèles Azure Resource Manager pour les machines virtuelles Azure avec extension OMS](./media/documentation-government-oms-figure3b.png)
<p align="center">Figure 3 : Modèles Azure Resource Manager pour les machines virtuelles Azure avec extension OMS</p>

Pour bénéficier d’informations sur la configuration d’OMS avec votre implémentation SCOM en local existante, accédez [à cette adresse](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="leveraging-intelligence-through-oms-solution-packs"></a>Valorisation des informations via les packs de solutions OMS
Maintenant que vous disposez de diverses sources de consignation des données, vous êtes confronté à une nouvelle problématique : comment exploiter ces données ?
OMS, en son cœur, est un service de recherche de journaux prenant en charge l’écriture de puissantes requêtes interrogeant des milliers, voire des millions de documents. Toutefois, il est très difficile de découvrir les problèmes à l’origine des requêtes que vous formulez.

Intégrez les solutions OMS : des packs de requêtes intégrés de manière native avec les technologies Map Reduce et Machine Learning OMS pour vous offrir de manière proactive une visibilité sur le parc OMS géré.

En parallèle du thème de la cybersécurité, j’évoquerai brièvement trois scénarios de cybersécurité qu’OMS peut résoudre en standard.

###<a name="antimalware-assessment"></a>Analyse anti-programme malveillant
L’analyse anti-programme malveillant vous procure un ensemble prédéfini de tableaux de bord de requêtes, de notifications et de surveillance afin de vous octroyer une visibilité en temps réel sur l’efficacité de la protection de votre parc contre les logiciels malveillants.

Ce tableau de bord vous renseigne sur 4 éléments :
* Les serveurs présentant des menaces actives et/ou corrigées.
* Les menaces actuellement détectées.
* Les ordinateurs non suffisamment protégés. Pour ce faire, OMS indexe les fichiers journaux de vos ordinateurs et identifie les sites de pare-feux ouverts et/ou les règles configurées de manière inappropriée dans les navigateurs web communs.
* Analyse de la protection des serveurs protégés Par exemple, via une protection native antivirus du système d’exploitation Windows et/ou une solution comme System Center Endpoint Protection.

Par exemple, ci-dessous, vous constatez qu’une menace identifiée a été automatiquement triée par System Center :

![Figure 4 : Solution OMS d’analyse anti-programme malveillant](./media/documentation-government-oms-figure4.png)
<p align="center">Figure 4 : Solution OMS d’analyse anti-programme malveillant</p>

Pour plus d’informations sur l’analyse anti-programme malveillant, accédez à cette adresse : [https://docs.microsoft.com/fr-fr/azure/log-analytics/log-analytics-malware](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/).

### <a name="identity-and-access"></a>Identité et accès
La compromission des informations d’identification représente un autre scénario commun de cybersécurité. Votre abonnement au cloud présente des informations d’identification, mais la sécurité ne s’arrête pas là. Chaque machine virtuelle se voit attribuer un utilisateur et/ou une clé secrète (habituellement un certificat et/ou un mot de passe).

OMS additionne et organise l’ensemble des tentatives de connexion dans votre parc et les répertorie en fonction du type (distante, locale, par nom d’utilisateur, etc.). Par exemple, dans l’élément ci-dessous, j’identifie une quantité considérable de tentatives de connexion mises en échec, provenant de noms d’utilisateur sous la forme de chaînes largement aléatoires. Ces éléments sont très probablement associés aux ordinateurs touchés, qui ne sont pas correctement protégés par les pare-feux et les listes de contrôle d’accès.

![Figure 5 : Échec de 97,3 % des tentatives de connexion au cours des dernières 24 h](./media/documentation-government-oms-figure5.png)
<p align="center">Figure 5 : Échec de 97,3 % des tentatives de connexion au cours des dernières 24 h</p>

### <a name="threat-intelligence"></a>Informations sur les menaces
OMS fournit également une protection contre les scénarios d’attaques malveillantes en interne, dans les situations où la sécurité de votre organisation est compromise et qu’un utilisateur malintentionné essaie d’exfiltrer des données.

OMS Threat Intelligence examine l’ensemble des fichiers journaux du réseau sur votre ordinateur, et vérifie automatiquement l’absence de connexions réseau entrantes/sortantes à des adresses IP malveillantes (par exemple, des adresses IP sur le darknet non indexé) ; le cas échéant, les informations vous sont communiquées.
Par exemple, ci-dessous, je constate l’existence de connexions réseaux entrantes et sortantes vers la République populaire de Chine. 

En double-cliquant sur la balise entrante, j’apprends qu’une machine virtuelle Linux gérée par OMS établit des connexions sortantes vers une adresse IP connue du darknet en Chine.

Vous pouvez également configurer des alertes sur les solutions OMS, comme Threat Intelligence. Ci-dessous, j’ai configuré une alerte configurée pour qu’OMS me prévienne par voie électronique si un volume supérieur à 10 connexion sortantes à une adresse IP malveillante connue est détecté. Je configure ensuite cette alerte pour le déclenchement d’un travail Azure Automation, dédiée à l’arrêt automatique de cette machine virtuelle.

![Figure 6 : Alertes OMS et Automation](./media/documentation-government-oms-figure6.png)
<p align="center">Figure 6 : Alertes OMS et Automation</p>

Il s’agit simplement d’une solution OMS out-of-box qui peut être appliquée à votre parc, qu’il soit exécuté sur Azure, un autre fournisseur de service cloud ou sur site.
OMS continuera à mettre automatiquement à jour son apprentissage automatique avec les dernières menaces ; nous continuerons également à déployer de nouvelles solutions sur la galerie de solutions OMS.

Pour plus d’informations sur OMS, consultez notre page de documentation ici : [https://docs.microsoft.com/fr-fr/azure/azure-government/documentation-government-overview](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/) 



<!--HONumber=Dec16_HO1-->


