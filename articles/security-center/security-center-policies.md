<properties
   pageTitle="Prise en main du Centre de sécurité Azure | Microsoft Azure"
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
   ms.date="03/01/2016"
   ms.author="yurid"/>

# Définition des stratégies de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à configurer des stratégies de sécurité dans le Centre de sécurité Azure en vous guidant tout au long des étapes nécessaires à l’exécution de cette tâche.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement ou groupe de ressources spécifique. Dans le Centre de sécurité Azure, vous devez définir des stratégies pour vos abonnements ou groupes de ressources Azure en fonction des exigences de sécurité de votre société et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Par exemple, les ressources utilisées pour le développement ou le test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications dont les données sont réglementées (par exemple, les informations d’identification personnelle (PII)) peuvent nécessiter un niveau de sécurité plus élevé. Les stratégies de sécurité activées dans le Centre de sécurité Azure déterminent les recommandations de sécurité et la surveillance qui vous aideront à identifier les vulnérabilités potentielles et à éliminer les menaces.

## Définition des stratégies de sécurité pour les abonnements

Vous pouvez configurer des stratégies de sécurité pour chaque abonnement ou groupe de ressources. Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire ou de collaborateur pour l’abonnement concerné. Pour configurer des stratégies de sécurité dans le Centre de sécurité Azure, suivez les étapes ci-dessous :

1. Cliquez sur la vignette **Stratégie de sécurité** dans le tableau de bord du Centre de sécurité Azure.

2. Dans le panneau **Stratégie de sécurité – Définir une stratégie par abonnement ou par groupe de ressources** qui s’ouvre sur la droite, sélectionnez l’abonnement pour lequel vous voulez activer la stratégie de sécurité. Si vous préférez activer la stratégie de sécurité pour un groupe de ressources et non pour l’ensemble de l’abonnement, passez à la section suivante portant sur la configuration des stratégies de sécurité pour les groupes de ressources.

    ![Activation de la collecte des données](./media/security-center-policies/security-center-policies-fig01.png)

3. Le panneau **Stratégie de sécurité** de cet abonnement s’ouvre et affiche des options similaires à celles illustrées ci-dessous :

    ![Activation de la collecte des données](./media/security-center-policies/security-center-policies-fig1-1.png)

4. Vérifiez que l’option **Collecter des données à partir des machines virtuelles** est définie sur **Activé**. Cette option active la collecte automatique des journaux sur les ressources existantes et nouvelles.

    >[AZURE.NOTE] Il est fortement recommandé d’activer la collecte des données pour chacun de vos abonnements, car cela garantit que la surveillance de la sécurité est disponible pour toutes les machines virtuelles (nouvelles et existantes). L’activation de la collecte des données installe l’agent de surveillance. Si vous ne voulez pas activer la collecte des données depuis cet emplacement, vous pourrez le faire ultérieurement dans les panneaux Intégrité des ressources et Recommandations. Vous pouvez également activer la collecte des données pour l’abonnement seulement, ou pour certaines machines virtuelles. Pour plus d’informations sur les machines virtuelles prises en charge, consultez [FAQ du Centre de sécurité Azure](security-center-faq.md).

5. Si votre compte de stockage n’est pas encore configuré, un avertissement similaire à celui de la figure ci-dessous peut s’afficher quand vous ouvrez la **stratégie de sécurité** :

    ![Sélection du stockage](./media/security-center-policies/security-center-policies-fig2.png)

6. Si cet avertissement s’affiche, cliquez sur cette option, puis sélectionnez la région, comme illustré dans la figure ci-dessous :

    ![Sélection du stockage](./media/security-center-policies/security-center-policies-fig3.png)

7. Pour chaque région où s’exécutent des machines virtuelles, choisissez le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Ainsi, vous pouvez stocker les données dans une même région pour garantir la confidentialité et la souveraineté des données. Une fois que vous avez décidé de la région à utiliser, sélectionnez-la, puis sélectionnez le compte de stockage.

8. Dans le panneau **Choisir des comptes de stockage**, cliquez sur **OK**.

    > [AZURE.NOTE] Si vous préférez, vous pouvez agréger les données de machines virtuelles appartenant à des régions différentes dans un compte de stockage central. Pour plus d’informations, consultez [FAQ du Centre de sécurité Azure](security-center-faq.md).

9. Dans le panneau **Stratégie de sécurité**, cliquez sur **Activé** pour activer les recommandations de sécurité que vous voulez utiliser pour cet abonnement. Le tableau ci-dessous explique à quoi servent les options :

| Stratégie | Lorsque l’option est activée (On) |
|----- |-----|
| Mises à jour du système | Récupère la liste des mises à jour disponibles dans Windows Update ou WSUS (selon le service qui est configuré pour la machine virtuelle) toutes les 12 heures et recommande l’installation des mises à jour manquantes (mises à jour critiques et de sécurité) sur les machines virtuelles Windows. |
| Règles de ligne de base | Analyse l’ensemble des machines virtuelles prises en charge afin d’identifier les configurations de système d’exploitation susceptibles de rendre la machine virtuelle plus vulnérable aux attaques, et recommande des changements de configuration visant à résoudre ces problèmes. Pour plus d’informations sur les configurations surveillées, consultez la [liste des règles de base recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). |
| Logiciel anti-programme malveillant | Recommande l’approvisionnement d’un logiciel anti-programme malveillant pour toutes les machines virtuelles Windows afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants. |
| ACL sur les points de terminaison | Recommande qu’une [liste de contrôle d’accès](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) (ACL) soit configurée pour limiter l’accès aux points de terminaison d’une machine virtuelle de type Classique. En général, cette option est utilisée pour garantir que seuls les utilisateurs qui sont connectés au réseau de l’entreprise puissent accéder aux machines virtuelles. |
| Groupes de sécurité réseau | Recommande la configuration de [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour contrôler le trafic entrant et sortant entre les sous-réseaux et les interfaces réseau pour les machines virtuelles de type Resource Manager. Les groupes de sécurité réseau configurés pour un sous-réseau sont hérités par toutes les interfaces réseau de machine virtuelle, sauf indication contraire. Après la vérification de la configuration d’un groupe de sécurité réseau, cette option évalue les règles de sécurité de trafic entrant afin d’identifier toute règle autorisant le trafic entrant. |
| Pare-feu d’applications web | Recommande l’approvisionnement d’un pare-feu d’applications web sur les machines virtuelles quand une [adresse IP publique de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) est utilisée et que les règles de sécurité de trafic entrant des groupes de sécurité réseau associés sont configurées pour autoriser l’accès au port 80 ou 443. Les adresses IP dont la charge est répartie (adresses IP virtuelles) sont utilisées. Les règles d’équilibrage de charge et les règles NAT de trafic entrant associées sont configurées pour autoriser l’accès au port 80 ou 443 (pour plus d’informations, consultez [Support d’Azure Resource Manager pour l’équilibreur de charge](../load-balancer/load-balancer-arm.md)) |
| Audit SQL | Recommande l’activation de l’audit de l’accès aux serveurs et aux bases de données SQL Azure à des fins de conformité, de détection avancée et d’investigation. |
| Chiffrement transparent des données SQL | Recommande l’activation du chiffrement au repos pour les bases de données SQL Azure, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction. Ainsi, même en cas de violation des données, celles-ci ne pourront pas être lues. |

10\. Une fois toutes les options configurées, cliquez sur **Enregistrer** pour valider les modifications.

## Définition des stratégies de sécurité pour les groupes de ressources

Si vous préférez configurer vos stratégies de sécurité par groupe de ressources, vous devez suivre des étapes similaires à celles utilisées pour configurer les stratégies de sécurité pour les abonnements. La principale différence réside dans le fait que vous devez développer le nom d’abonnement et sélectionner le groupe de ressources de votre choix pour configurer la stratégie de sécurité spécifique :

![Sélection du groupe de ressources](./media/security-center-policies/security-center-policies-fig4.png)

Une fois le groupe de ressources sélectionné, vous accédez au panneau **Stratégie de sécurité**. Par défaut, l’option **Héritage** est activée, ce qui signifie que toutes les stratégies de sécurité associées à ce groupe de ressources sont héritées à partir du niveau d’abonnement. Vous pouvez modifier cette configuration si vous souhaitez une stratégie de sécurité personnalisée par groupe de ressources. Si tel est le cas, vous devez sélectionner l’option **Unique** et apporter vos modifications sous **Afficher les recommandations pour**.


![Stratégie de sécurité par groupe de ressources](./media/security-center-policies/security-center-policies-fig5.png)

> [AZURE.NOTE] En cas de conflit entre la stratégie de niveau abonnement et la stratégie de niveau groupe de ressources, la stratégie de niveau ressources est prioritaire.


## Étapes suivantes

Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure

<!---HONumber=AcomDC_0323_2016-->