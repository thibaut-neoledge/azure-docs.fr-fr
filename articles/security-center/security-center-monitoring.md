<properties
   pageTitle="Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document a pour but de vous aider à vous familiariser avec les fonctionnalités de surveillance du Centre de sécurité Azure d’analyse."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="yurid"/>

#Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure
Ce document a pour but de vous aider à utiliser les fonctionnalités de surveillance du Centre de sécurité Azure afin de contrôler la conformité aux stratégies.

> [AZURE.NOTE]Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

##Qu’est-ce que la surveillance de l’intégrité de la sécurité ?
Nous pensons souvent que la surveillance consiste à veiller et à attendre qu’un événement se produise pour y répondre. Ici, la surveillance de la sécurité fait référence à une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques.

##Surveillance de l'intégrité de la sécurité
Après l’activation des [stratégies de sécurité](security-center-policies.md) pour les ressources d’un l’abonnement, le Centre de sécurité Azure analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Contrairement aux informations sur la configuration réseau qui sont instantanément disponibles, l’affichage des informations concernant la configuration des machines virtuelles (telles que l’état des mises à jour de sécurité ou la configuration du système d’exploitation) peut prendre une heure ou plus. Vous pouvez afficher l’état de sécurité de vos ressources, ainsi que les problèmes éventuels, dans le panneau Intégrité des ressources. Vous pouvez également afficher une liste des problèmes dans le panneau Recommandations.

Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md).

La mosaïque **Intégrité des ressources** vous permet de surveiller l’état de sécurité de vos ressources. Vous serez amené à voir des problèmes avec un niveau de gravité élevé ou moyen qui nécessitent une attention particulière, comme indiqué ci-dessous :

![Intégrité des ressources](./media/security-center-monitoring/security-center-monitoring-fig1.png)

Les stratégies de sécurité qui sont activées ont un impact sur les types de contrôles surveillés. Si le Centre de sécurité Azure identifie une vulnérabilité qui doit être corrigée, par exemple, une machine virtuelle à laquelle il manque des mises à jour de sécurité ou un sous-réseau sans [groupe de sécurité réseau](virtual-networks-nsg.md), cette vulnérabilité sera identifiée dans ce panneau.

###Machine virtuelle
Quand vous cliquez sur **Machines virtuelles** dans la mosaïque **Intégrité des ressources**, le panneau **Machines virtuelles** s’ouvre et affiche des informations détaillées sur les étapes d’intégration et de prévention, ainsi que la liste de toutes les machines virtuelles surveillées par le Centre de sécurité Azure, comme indiqué ci-dessous :

![Mise à jour système manquante par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig2.png)

Le panneau comprend trois sections. Dans chaque section, vous pouvez sélectionner une option afin d’afficher plus de détails sur l’étape recommandée pour résoudre le problème.

Par exemple, si vous cliquez sur **Mises à jour système manquantes** sous **Étapes de prévention**, le panneau **Mises à jour système manquantes** s’ouvre et affiche la liste des machines virtuelles auxquelles il manque des correctifs, ainsi que le niveau de gravité, comme indiqué ci-dessous :

![Prévention de mise à jour système manquante](./media/security-center-monitoring/security-center-monitoring-fig3.png)

Pour afficher des informations détaillées sur les mises à jour recommandées, cliquez sur le nom de la machine virtuelle concernée. Un nouveau panneau s’ouvre pour cette machine virtuelle et affiche la liste des mises à jour manquantes :

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]Les recommandations de sécurité de ce panneau sont les mêmes que celles figurant dans le panneau Recommandations. Pour plus d’informations sur la résolution des recommandations, consultez [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md). Cela s’applique non seulement aux machines virtuelles, mais aussi à toutes les ressources qui sont disponibles dans la mosaïque Intégrité des ressources.

###Mise en réseau
Les réseaux virtuels surveillés par le Centre de sécurité Azure apparaissent dans la section d’état de la prévention du panneau Réseaux. Quand vous cliquez sur **Réseaux** dans la mosaïque Intégrité des ressources, le panneau **Réseaux** s’ouvre et affiche des informations détaillées, comme le montre l’illustration ci-dessous :

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig5.png)

En haut du panneau se trouve un récapitulatif des problèmes et au bas du panneau, la liste des réseaux surveillés. Ces informations sont similaires à celles qui figurent dans le panneau Intégrité des ressources.

Dans la section de répartition des états du panneau Réseaux figurent des étapes de prévention telles que [ACL sur les points de terminaison](virtual-machines-set-up-endpoints.md) désactivés, [Groupes de sécurité réseau](virtual-networks-nsg.md) désactivés ou Sous-réseaux intègres. Vous pouvez cliquer sur ces étapes pour afficher davantage d’informations et résoudre les problèmes.

Prenons, par exemple, l’alerte **Groupes de sécurité réseau sur les sous-réseaux non activés**. Quand vous cliquez sur cette alerte, vous ouvrez le panneau **Configurer les groupes de sécurité réseau manquants pour les sous-réseaux**. Vous verrez la description d’un sous-réseau pour lequel aucun groupe de sécurité réseau n’est activé, ainsi que la liste des cartes d’interface réseau qui appartiennent à ce sous-réseau. Sélectionnez le sous-réseau auquel vous voulez appliquer le groupe de sécurité, puis cliquez sur l’option **Configurer le groupe de sécurité réseau**. Le panneau **Choisir un groupe de sécurité réseau** s’ouvre alors, comme dans l’illustration ci-dessous :

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
Quand vous cliquez sur **SQL** dans la mosaïque **Intégrité des ressources**, le panneau **SQL** s’ouvre et affiche des recommandations relatives aux problèmes, telles que « audit désactivé » ou « chiffrement transparent des données désactivé », ou bien l’état d’intégrité général de la base de données. Au bas du panneau se trouve la répartition des serveurs SQL, ainsi que, pour chaque serveur, les bases de données qui lui appartiennent :

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig7.png)

Vous pouvez cliquer sur l’une de ces recommandations pour obtenir des informations détaillées qui vous aideront à résoudre le problème. Dans l’exemple ci-dessous, l’audit est activé pour les serveurs SQL.

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###Applications
Si votre charge de travail Azure comprend des applications situées sur des [machines virtuelles Resource Manager](resource-manager-deployment-model.md) ayant des ports web exposés (ports TCP 80 et 443), le Centre de sécurité Azure peut les surveiller pour identifier les problèmes de sécurité potentiels et recommander des étapes de résolution. Quand vous cliquez sur la mosaïque **Applications**, le panneau **Applications** s’ouvre et affiche des recommandations dans la section Étapes de prévention, ainsi que la répartition des applications par hôte/adresse IP virtuelle, comme le montre l’illustration ci-dessous :

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig9.png)

Comme pour les ressources précédentes, vous pouvez cliquer sur les recommandations pour afficher des informations détaillées et résoudre le problème. L’exemple ci-dessous représente une application ayant été identifiée comme une **Application web non sécurisée**. Quand vous sélectionnez une application considérée comme non sécurisée, un autre panneau s’ouvre et affiche une option permettant d’activer le pare-feu d’applications web.

![Mise à jour système recommandée par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## Étapes suivantes
Dans ce document, vous avez vu comment utiliser les fonctionnalités de surveillance du Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) – Découvrez comment configurer des paramètres de sécurité dans le Centre de sécurité Azure
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) – Recherchez des billets de blog sur la sécurité et la conformité Azure

<!---HONumber=AcomDC_1217_2015-->