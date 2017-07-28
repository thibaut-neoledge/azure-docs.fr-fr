---
title: "Intégration des partenaires dans Azure Security Center | Microsoft Docs"
description: "Découvrez comment Azure Security Center s’intègre avec les partenaires pour améliorer la sécurité globale de vos ressources Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 4d0909e926de14a0cbe9799b969ac7a1946d69d1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---
# <a name="partner-integration-in-azure-security-center"></a>Intégration des partenaires dans Azure Security Center

Cet article explique comment Azure Security Center s’intègre avec les partenaires pour vous aider à améliorer la sécurité globale. Security Center offre une expérience intégrée dans Azure et tire parti de Place de marché Azure pour la certification et la facturation des partenaires.

> [!NOTE] 
> Depuis juin 2017, Security Center utilise Microsoft Monitoring Agent pour collecter et stocker des données. Pour plus d’informations, consultez l’article [Migration de plateforme Azure Security Center](security-center-platform-migration.md). Les informations contenues dans cet article représentent les fonctionnalités de Security Center après la transition vers Microsoft Monitoring Agent.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Pourquoi déployer des solutions de partenaire à partir de Security Center ?

L’intégration des partenaires dans Security Center offre quatre avantages majeurs :

- **Déploiement facile** : il est beaucoup plus facile de déployer une solution de partenaire en suivant la recommandation de Security Center. Le processus de déploiement peut être entièrement automatisé à l’aide d’une topologie de réseau et de configuration par défaut. Alternativement, les clients peuvent choisir une option semi-automatisée pour plus de flexibilité et de personnalisation.
- **Détections intégrées** : les événements de sécurité des solutions de partenaire sont automatiquement collectés, agrégés et affichés dans le cadre des alertes et des incidents de Security Center. Ces événements sont également fusionnés avec les détections d’autres sources pour fournir des fonctions de détection de menaces avancées.
- **Gestion et surveillance unifiées de l’intégrité** : Les clients peuvent utiliser des événements d’intégrité intégrés pour surveiller facilement les solutions des partenaires. La gestion de base offre un accès facile à la configuration avancée avec la solution de partenaire.
- **Export vers SIEM** : Les clients peuvent exporter toutes les alertes des partenaires et de Security Center au format CEF (Common Event Format) vers des systèmes SIEM (Security Information and Event Management) locaux grâce à l’intégration du journal Azure (préversion).


## <a name="partners-that-integrate-with-security-center"></a>Partenaires qui s’intègrent à Security Center

Pour le moment, Security Center s’intègre aux solutions suivantes :

- Endpoint protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec et [Microsoft Antimalware pour Azure Cloud Services et Machines Virtuelles](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Pare-feu d’applications web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Pare-feu de nouvelle génération ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) et [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Évaluation des vulnérabilités ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Au fil du temps, Security Center augmentera le nombre de partenaires au sein de ces catégories existantes et ajoutera de nouvelles catégories. 

## <a name="deploy-a-partner-solution"></a>Déploiement d’une solution de partenaire

Selon la configuration de votre environnement Azure et la stratégie de sécurité que vous avez définie, Security Center peut recommander le déploiement d’une solution de partenaire. La recommandation de Security Center vous aide à sélectionner et à installer la solution partenaire. L’expérience de déploiement global peut varier en fonction du type de solution et du partenaire. Pour plus d’informations, consultez les articles suivants :

- [Installer Endpoint Protection](security-center-install-endpoint-protection.md)
- [Ajouter un pare-feu d’applications web](security-center-add-web-application-firewall.md)
- [Ajouter un pare-feu de nouvelle génération](security-center-add-next-generation-firewall.md)
- [Évaluation des vulnérabilités non installée](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Gestion des solutions de partenaires

Après le déploiement, sélectionnez la vignette **Solutions de partenaires** dans le panneau **Security Center** pour afficher les informations concernant l’intégrité de la solution et effectuer les tâches de gestion basiques. Pour plus d’informations sur la gestion des solutions de partenaire dans Security Center, consultez [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md).

![Intégration des partenaires](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> La prise en charge du point de terminaison Symantec se limite à la détection. Aucune alerte d’intégrité n’est disponible.
>

## <a name="see-also"></a>Voir aussi

Dans cet article, vous avez appris à intégrer des solutions de partenaires dans Azure Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification de Security Center](security-center-planning-and-operations-guide.md)
* [Gérer et répondre aux alertes de sécurité dans Security Center](security-center-managing-and-responding-alerts.md)
* [Alertes de sécurité par type dans Security Center](security-center-alerts-type.md)
* [Surveillance de l’intégrité de la sécurité dans Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Surveillance des solutions de partenaires avec Security Center](security-center-partner-solutions.md). découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [Questions fréquentes : Azure Security Center](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

