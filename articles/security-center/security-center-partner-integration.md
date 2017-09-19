---
title: "Intégration des partenaires et des solutions dans Azure Security Center | Microsoft Docs"
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 8cc44da0f61362018d2757da58ca4fb3a9a43764
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Intégration des partenaires et des solutions dans Azure Security Center

Cet article explique comment Azure Security Center s’intègre avec les partenaires pour vous aider à améliorer la sécurité globale. Security Center offre une expérience intégrée dans Azure et tire parti de Place de marché Azure pour la certification et la facturation des partenaires.

## <a name="why-deploy-partner-solutions-from-security-center"></a>Pourquoi déployer des solutions de partenaire à partir de Security Center ?

L’intégration des partenaires dans Security Center offre quatre avantages majeurs :

- **Déploiement facile** : il est beaucoup plus facile de déployer une solution de partenaire en suivant la recommandation de Security Center. Le processus de déploiement peut être entièrement automatisé à l’aide d’une topologie de réseau et de configuration par défaut. Alternativement, les clients peuvent choisir une option semi-automatisée pour plus de flexibilité et de personnalisation.
- **Détections intégrées** : les événements de sécurité des solutions de partenaire sont automatiquement collectés, agrégés et affichés dans le cadre des alertes et des incidents de Security Center. Ces événements sont également fusionnés avec les détections d’autres sources pour fournir des fonctions de détection de menaces avancées.
- **Gestion et surveillance unifiées de l’intégrité** : Les clients peuvent utiliser des événements d’intégrité intégrés pour surveiller facilement les solutions des partenaires. La gestion de base offre un accès facile à la configuration avancée avec la solution de partenaire.
- **Export vers SIEM** : Les clients peuvent exporter toutes les alertes des partenaires et de Security Center au format CEF (Common Event Format) vers des systèmes SIEM (Security Information and Event Management) locaux grâce à l’intégration du journal Azure (préversion).


## <a name="partners-that-integrate-with-security-center"></a>Partenaires qui s’intègrent à Security Center

Actuellement, voici les solutions d’intégration native de partenaire disponibles dans Azure Marketplace avec le centre de sécurité :

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

Pour afficher les informations concernant l’intégrité de la solution et effectuer les tâches de gestion basiques après le déploiement, accédez au panneau **Centre de sécurité** puis sélectionnez l’option **Solutions de partenaire**.

![Intégration de solutions de partenaire](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

Le contenu qui s’affiche au moment où vous ouvrez Solutions de sécurité peut varier en fonction de votre infrastructure. Cette page contient trois sections, sur le modèle de l’image précédente :

- **Solutions connectées** : affiche les solutions qui sont connectées au centre de sécurité.
- **Solutions découvertes** : affiche les solutions qui ne sont pas connectées au centre de sécurité. En connectant ces solutions, elles s’afficheront sous Solutions connectées.  Si le centre de sécurité ne détecte aucune solution non connectée, cette section est masquée.
- **Ajouter des sources de données** : affiche les sources de données Azure et non Azure que vous pouvez ajouter au centre de sécurité.

### <a name="connected-solutions"></a>Solutions connectées

La section **Solutions connectées** affiche toutes les solutions de sécurité qui sont actuellement connectées avec le centre de sécurité. 

![Solutions connectées](./media/security-center-partner-integration/security-center-partner-integration-fig10.png)

Les informations affichées sur chaque vignette peuvent varier en fonction de la solution. Certaines informations disponibles sur chaque vignette peuvent inclure :

- Icône de la société pour le partenaire.  Si le centre de sécurité ne possède pas l’icône de la société, les premiers caractères du nom du partenaire sont affichés.
- Type de solution.
- Le nom de l’ordinateur peut être affiché.
- État d’intégrité.  Si un indicateur d’intégrité n’est pas envoyé, le centre de sécurité affiche la date et l’heure du dernier événement reçu pour indiquer si l’appliance est en train de créer un rapport. Si le centre de sécurité ne reçoit pas l’indicateur d’intégrité d’une solution spécifique, la vignette de la solution n’apparaît pas dans cette section.

> [!NOTE]
> Le centre de sécurité Watch affiche la date et l’heure du dernier événement reçu pour indiquer si l’application est en train de créer un rapport. Les solutions qui n’envoient pas d’indicateur d’intégrité sont affichées comme connectées si l’alerte ou l’événement ont été envoyés au cours des 14 derniers jours.
>  

Certaines de ces solutions peuvent être entièrement intégrées dans Azure, d’autres peuvent être locales. Étant donné que le centre de sécurité prend en charge [le CEF (Common Event Format)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef), il peut se connecter avec des solutions qui utilisent le format CEF, tel qu’un pare-feu qui prend en charge le format CEF. Une fois que cette solution est ajoutée au centre de sécurité, le pare-feu envoi des journaux au format CEF au centre de sécurité, qui les traitent dans [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). Le pare-feu est une ressource non Azure. Il envoie les événements, mais n’envoie pas d’indicateur d’intégrité.  La seule information que possède le centre de sécurité sur l’intégrité concerne l’heure du dernier envoi d’un événement par l’appliance.  Le centre de sécurité affiche la date et l’heure du dernier événement reçu dans la zone d’intégrité de la vignette pour toutes les ressources non Azure, ce qui indique que la ressource non Azure continue de créer un rapport.

### <a name="discovered-solutions"></a>Solutions découvertes

La section **Solutions découvertes** affiche toutes les solutions qui ont été ajoutées via Azure, et le centre de sécurité suggère de s’y connecter.

![Solutions découvertes](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Le centre de sécurité peut intégrer des solutions Azure intégrées, telles qu’[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection). Si vous possédez une licence pour Azure AD Identity Protection, mais qu’elle n’est pas connectée au centre de sécurité, Azure AD Identity Protection est répertoriée sous **Solutions découvertes**. Pour intégrer cette solution avec le centre de sécurité, cliquez sur **CONNECTER** sur la vignette **Azure AD Identity Protection** et la page suivante s’affiche :

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Pour terminer la connexion d’Azure AD Identity Protection, vous devez sélectionner un espace de travail dans lequel sont enregistrées les données. Toutes les données d’Azure AD Identity Protection circulent à partir de la région de l’espace de travail qui a été sélectionnée dans cette étape.  Vous allez passer par le sélecteur d’espace de travail pour sélectionner l’espace de travail. Les données commenceront à circuler à partir de ce moment.

Pour pouvoir vous connecter au centre de sécurité, vous devez être un administrateur global ou un administrateur de sécurité.  Si vous ne possédez pas d’autorisations, le bouton **Connecter** est désactivé. De plus, si vous ne possédez pas d’autorisations, un message s’affiche, expliquant pourquoi le bouton est désactivé.

Les alertes d’Azure AD Identity Protection passent par le canal de détection du centre de sécurité, ce qui vous permet de recevoir des alertes à partir du centre de sécurité et d’Azure Active Directory Identity Protection. Le centre de sécurité fusionne toutes les alertes qui semblent appropriées pour créer un [incident de sécurité](https://docs.microsoft.com/azure/security-center/security-center-incident). La description de l’incident de sécurité vous donne plus d’informations sur une activité suspecte.

### <a name="add-data-sources"></a>Ajouter des sources de données

Vous pouvez ajouter des ordinateurs Azure et non Azure pour les intégrer au centre de sécurité.  Ajouter des ordinateurs non Azure signifie que vous pouvez ajouter un ordinateur local ou une appliance qui prend en charge le format CEF. 

![Sources de données](./media/security-center-partner-integration/security-center-partner-integration-fig11.png)


## <a name="see-also"></a>Voir aussi

Dans cet article, vous avez appris à intégrer des solutions de partenaires dans Azure Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification de Security Center](security-center-planning-and-operations-guide.md)
* [Gérer et répondre aux alertes de sécurité dans Security Center](security-center-managing-and-responding-alerts.md)
* [Alertes de sécurité par type dans Security Center](security-center-alerts-type.md)
* [Surveillance de l’intégrité de la sécurité dans Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Surveillance des solutions de partenaires avec Security Center](security-center-partner-solutions.md). découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [Questions fréquentes : Azure Security Center](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation du service.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

