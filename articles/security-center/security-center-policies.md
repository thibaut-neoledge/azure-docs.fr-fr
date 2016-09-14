<properties
   pageTitle="Définition des stratégies de sécurité dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="yurid"/>

# Définition des stratégies de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité en vous guidant tout au long des étapes nécessaires à l’exécution de cette tâche.

## Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement ou groupe de ressources spécifique. Dans le Centre de sécurité, vous devez définir des stratégies pour vos abonnements ou groupes de ressources Azure en fonction des exigences de sécurité de votre société et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Par exemple, les ressources utilisées pour le développement ou le test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications dont les données sont réglementées (par exemple, les informations d’identification personnelle (PII)) peuvent nécessiter un niveau de sécurité plus élevé. Les stratégies de sécurité activées dans le Centre de sécurité Azure déterminent les recommandations de sécurité et la surveillance qui vous aideront à identifier les vulnérabilités potentielles et à éliminer les menaces. Pour plus d’informations sur la façon de décider quelle option vous convient le mieux, consultez [Guide des opérations et de planification du Centre de sécurité Azure](security-center-planning-and-operations-guide.md).

## Définition des stratégies de sécurité pour les abonnements

Vous pouvez configurer des stratégies de sécurité pour chaque abonnement ou groupe de ressources. Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire ou de collaborateur pour l’abonnement concerné. Accédez au portail Azure et suivez les étapes suivantes pour configurer des stratégies de sécurité dans le Centre de sécurité :

1. Cliquez sur la mosaïque **Stratégie** dans le tableau de bord du Centre de sécurité.

2. Dans le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou par groupe de ressources** qui s’ouvre sur la droite, sélectionnez l’abonnement pour lequel vous voulez activer la stratégie de sécurité. Si vous préférez activer la stratégie de sécurité pour un groupe de ressources et non pour l’ensemble de l’abonnement, passez à la section suivante portant sur la configuration des stratégies de sécurité pour les groupes de ressources.

    ![Définition de stratégie](./media/security-center-policies/security-center-policies-fig1-ga.png)

3. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre et affiche des options similaires à celles de l’écran suivant :

    ![Activation de la collecte des données](./media/security-center-policies/security-center-policies-fig2-ga.png)

	Les options disponibles dans ce panneau sont :
	- **Stratégie de prévention** : cette option vous permet de configurer des stratégies par abonnement ou par groupe de ressources.
	- **Notification par e-mail** : une notification par e-mail est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Les préférences de courrier électronique peuvent uniquement être configurées pour les stratégies d’abonnement. Pour plus d’informations sur la configuration des notifications par e-mail, consultez [Fournir les détails du contact de sécurité dans Azure Security Center](security-center-provide-security-contact-details.md).
	- **Niveau tarifaire**: utilisez cette option pour mettre à niveau à partir de la sélection du niveau tarifaire. Pour plus d’informations sur les options de tarification, consultez la page [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

	
4.  Vérifiez que l’option **Collecter des données à partir des machines virtuelles** est définie sur **Activé**. Cette option active la collecte automatique des journaux sur les ressources existantes et nouvelles.

    >[AZURE.NOTE] Il est recommandé d’activer la collecte des données pour chacun de vos abonnements afin de garantir que la surveillance de la sécurité est disponible pour toutes les machines virtuelles (nouvelles et existantes). L’activation de la collecte des données installe l’agent de surveillance. Si vous ne voulez pas activer la collecte des données depuis cet emplacement, vous pourrez le faire ultérieurement dans les panneaux Intégrité des ressources et Recommandations. Vous pouvez également activer la collecte des données pour l’abonnement seulement, ou pour certaines machines virtuelles. Pour plus d’informations sur les machines virtuelles prises en charge, consultez [FAQ du Centre de sécurité Azure](security-center-faq.md).

5. Si votre compte de stockage n’est pas encore configuré, il se peut que vous voyiez s’afficher lors de l’ouverture de la **Stratégie de sécurité** un avertissement similaire à celui de l’écran suivant :

    ![Sélection du stockage](./media/security-center-policies/security-center-policies-fig2.png)

6. Si cet avertissement s’affiche, cliquez sur cette option, puis sélectionnez la région, comme illustré dans l’écran suivant :

    ![Sélection du stockage](./media/security-center-policies/security-center-policies-fig3-ga.png)

7. Pour chaque région où s’exécutent des machines virtuelles, choisissez le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Ainsi, vous pouvez stocker les données dans une même région pour garantir la confidentialité et la souveraineté des données. Une fois que vous avez décidé de la région à utiliser, sélectionnez-la, puis sélectionnez le compte de stockage.

8. Dans le panneau **Choisir des comptes de stockage**, cliquez sur **OK**.

    > [AZURE.NOTE] Si vous préférez, vous pouvez agréger les données de machines virtuelles appartenant à des régions différentes dans un compte de stockage central. Pour plus d’informations, consultez [FAQ du Centre de sécurité Azure](security-center-faq.md).

9. Dans le panneau **Stratégie de sécurité**, cliquez sur **Activé** pour activer les recommandations de sécurité que vous voulez utiliser pour cet abonnement. Cliquez sur l’option **Stratégie de prévention**. Le panneau **Stratégie de sécurité** s’ouvre, comme illustré sur l’écran suivant :

	![Sélection des stratégies de sécurité](./media/security-center-policies/security-center-policies-fig4-ga.png)

Le tableau ci-dessous explique à quoi servent les options :

| Stratégie | Lorsque l’option est activée (On) |
|----- |-----|
| Mises à jour du système | Récupère quotidiennement la liste des mises à jour de sécurité et critiques disponibles dans Windows Update ou WSUS (selon le service qui est configuré pour la machine virtuelle), et recommande que les mises à jour manquantes soient appliquées. Recherche les dernières mises à jour dans les systèmes Linux à l’aide du système de gestion des packages fourni par le distributeur afin de déterminer quels packages peuvent être mis à jour. Il vérifie également la disponibilité des mises à jour de sécurité et des mises à jour importantes à partir des machines virtuelles [Cloud Services](./cloud-services/cloud-services-how-to-configure.md). |
| Vulnérabilités du système d’exploitation | Analyse quotidiennement les configurations de système d’exploitation susceptibles de rendre la machine virtuelle plus vulnérable aux attaques, et recommande des changements de configuration visant à résoudre ces problèmes. Pour plus d’informations sur les configurations surveillées, consultez la [liste des règles de base recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). |
| Endpoint Protection | Recommande l’approvisionnement d’Endpoint Protection pour toutes les machines virtuelles Windows afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants.|
| Chiffrement de disque | Recommande d’activer le chiffrement de disque dans toutes les machines virtuelles pour améliorer la protection des données au repos. 
| Groupes de sécurité réseau | Recommande la configuration de [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG) pour contrôler le trafic entrant et sortant vers les machines virtuelles avec des points de terminaison publics. Les groupes de sécurité réseau configurés pour un sous-réseau sont hérités par toutes les interfaces réseau de machine virtuelle, sauf indication contraire. Après la vérification de la configuration d’un groupe de sécurité réseau, cette option évalue les règles de sécurité de trafic entrant afin d’identifier toute règle autorisant le trafic entrant. |
| Pare-feu d’applications web | Recommande l’approvisionnement d’un pare-feu d’applications web sur les machines virtuelles quand une [adresse IP publique de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) est utilisée et que les règles de sécurité de trafic entrant des groupes de sécurité réseau associés sont configurées pour autoriser l’accès au port 80 ou 443. Les adresses IP dont la charge est répartie (adresses IP virtuelles) sont utilisées. Les règles d’équilibrage de charge et les règles NAT de trafic entrant associées sont configurées pour autoriser l’accès au port 80 ou 443 (pour plus d’informations, consultez [Support d’Azure Resource Manager pour l’équilibreur de charge](../load-balancer/load-balancer-arm.md)) |
| Pare-feu de nouvelle génération | Cela permet d’étendre les protections du réseau au-delà des groupes de sécurité réseau, intégrés à Azure. Le Centre de sécurité détecte les déploiements pour lesquels un pare-feu de nouvelle génération est recommandé et vous permet d’approvisionner une appliance virtuelle. |
| Audit SQL | Recommande l’activation de l’audit de l’accès aux serveurs et aux bases de données SQL Azure à des fins de conformité, de détection avancée et d’investigation. |
| Chiffrement transparent des données SQL | Recommande l’activation du chiffrement au repos pour les bases de données SQL Azure, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction. Ainsi, même en cas de violation des données, celles-ci ne pourront pas être lues. |
	
Une fois que vous avez configuré toutes les options, cliquez sur **OK** dans le panneau **Stratégie de sécurité** contenant les recommandations, puis cliquez sur **Enregistrer** dans le panneau **Stratégie de sécurité** contenant les paramètres initiaux.

## Définition des stratégies de sécurité pour les groupes de ressources

Si vous préférez configurer vos stratégies de sécurité par groupe de ressources, vous devez suivre des étapes similaires à celles utilisées pour configurer les stratégies de sécurité pour les abonnements. La principale différence réside dans le fait que vous devez développer le nom d’abonnement et sélectionner le groupe de ressources de votre choix pour configurer la stratégie de sécurité spécifique :

![Sélection du groupe de ressources](./media/security-center-policies/security-center-policies-fig5-ga.png)

Une fois le groupe de ressources sélectionné, vous accédez au panneau **Stratégie de sécurité**. Par défaut, l’option **Héritage** est activée, ce qui signifie que toutes les stratégies de sécurité associées à ce groupe de ressources sont héritées à partir du niveau d’abonnement. Vous pouvez modifier cette configuration si vous souhaitez une stratégie de sécurité personnalisée par groupe de ressources. Si tel est le cas, vous devez sélectionner l’option **Unique** et apporter vos modifications sous **Stratégie de prévention**.

![Stratégie de sécurité par groupe de ressources](./media/security-center-policies/security-center-policies-fig6-ga.png)

> [AZURE.NOTE] En cas de conflit entre la stratégie de niveau abonnement et la stratégie de niveau groupe de ressources, la stratégie de niveau ressources est prioritaire.


## Voir aussi

Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
- [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!---HONumber=AcomDC_0907_2016-->