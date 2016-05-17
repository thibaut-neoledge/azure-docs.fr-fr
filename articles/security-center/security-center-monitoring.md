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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="yurid"/>

#Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure
Ce document a pour but de vous aider à utiliser les fonctionnalités de surveillance du Centre de sécurité Azure, afin de contrôler la conformité aux stratégies.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus sur la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

##Qu’est-ce que la surveillance de l’intégrité de la sécurité ?
Nous pensons souvent que la surveillance consiste à veiller et à attendre qu’un événement se produise pour y répondre. Ici, la surveillance de la sécurité fait référence à une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques.

##Surveillance de l'intégrité de la sécurité
Après l’activation des [stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, le Centre de sécurité analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Contrairement aux informations sur la configuration réseau, qui sont instantanément disponibles, l’affichage des informations concernant la configuration des machines virtuelles (telles que l’état des mises à jour de sécurité ou la configuration du système d’exploitation) peut prendre une heure ou plus. Vous pouvez afficher l'état de sécurité de vos ressources, ainsi que les problèmes éventuels, dans le panneau **Intégrité des ressources**. Vous pouvez également afficher une liste des problèmes dans le panneau **Recommandations**.

Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md).

La mosaïque **Intégrité des ressources** vous permet de surveiller l’état de sécurité de vos ressources. L’exemple ci-dessous représente des problèmes associés à un niveau de gravité élevé ou moyen, qui nécessitent une attention particulière. Les stratégies de sécurité qui sont activées ont un impact sur les types de contrôles surveillés.

![Intégrité des ressources](./media/security-center-monitoring/security-center-monitoring-fig1-new2.png)

Si le Centre de sécurité identifie une vulnérabilité qui doit être corrigée (par exemple, une machine virtuelle à laquelle il manque des mises à jour de sécurité ou un sous-réseau sans [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)), cette vulnérabilité est identifiée dans ce panneau.

###Surveillance des machines virtuelles
Quand vous cliquez sur **Machines virtuelles** dans la mosaïque **Intégrité des ressources**, le panneau **Machines virtuelles** s’ouvre et affiche des informations détaillées sur les étapes d’intégration et de prévention, ainsi que la liste de toutes les machines virtuelles surveillées par le Centre de sécurité, comme indiqué ci-dessous.

![Mise à jour système manquante par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- Étapes de mise en route
- Recommandations pour machines virtuelles
- Machines virtuelles

Dans chaque section, vous pouvez sélectionner une option afin d’afficher plus de détails sur l’étape recommandée pour résoudre le problème. Les sections ci-dessous apportent plus de détails sur ces sujets.

####Étapes de mise en route
Cette section indique la quantité totale de machines virtuelles qui ont été initialisées pour la collecte des données et leur état actuel. Une fois la collecte des données initialisée sur chaque machine virtuelle, cette dernière est prête à recevoir les stratégies de sécurité du Centre de sécurité. Lorsque vous cliquez sur cette entrée, le panneau **Initialisation en cours de la collecte des données** s’ouvre, vous permettant de voir le nom des machines virtuelles et l’état actuel de la collecte des données, comme dans la colonne **ÉTAT D’INSTALLATION** (voir ci-dessous).

![État d'initialisation](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recommandations pour machines virtuelles
Cette section contient une série de recommandations pour chaque machine virtuelle contrôlée par le Centre de sécurité Azure. La première colonne contient la description de la recommandation ; la deuxième colonne indique la quantité totale de machines virtuelles qui sont affectées par ces recommandations et la troisième, le niveau de gravité du problème, comme indiqué ci-dessous.

![Recommandations pour les machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Chaque recommandation dispose d'un ensemble d'actions qui peuvent être effectuées lorsque vous cliquez dessus. Par exemple, si vous cliquez sur **Mises à jour système manquantes**, le panneau **Mises à jour système manquantes** s’ouvre. Ce dernier répertorie les machines virtuelles auxquelles il manque des correctifs ainsi que le niveau de gravité de la mise à jour manquante, comme indiqué ci-dessous.

![Mises à jour système manquantes](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

Le panneau **Mises à jour système manquantes** affiche un tableau incluant les informations suivantes :

- **MACHINE VIRTUELLE** : nom de la machine virtuelle sur laquelle il manque des mises à jour.
- **MISES À JOUR SYSTÈME** : quantité de mises à jour système manquantes.
- **HEURE DE LA DERNIÈRE ANALYSE** : heure de la dernière analyse par le Centre de sécurité de la machine virtuelle, afin de contrôler les mises à jour.
- **ÉTAT** : état actuel de la recommandation :
	- **Ouverte** : la recommandation n’a pas encore été prise en compte.
	- **En cours** : la recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
	- **Résolue** : la recommandation a déjà été appliquée (une fois le problème résolu, la ligne est grisée).
- **GRAVITÉ** : donne le niveau de gravité de chaque recommandation :
	- **Élevée** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
	- **Moyenne** : certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
	- **Faible** : existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.

Pour afficher des informations détaillées sur les recommandations, cliquez sur le nom de la machine virtuelle concernée. Un nouveau panneau s’ouvre pour cette machine virtuelle et affiche la liste des mises à jour manquantes, comme indiqué ci-dessous.

![Mises à jour système manquantes par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Les recommandations de sécurité de ce panneau sont les mêmes que celles figurant dans le panneau Recommandations. Pour plus d’informations sur la résolution des recommandations, consultez [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md). Cela s’applique non seulement aux machines virtuelles, mais aussi à toutes les ressources qui sont disponibles dans la mosaïque Intégrité des ressources.

####Section Machines virtuelles
La section Machines virtuelles vous donne une présentation de toutes les machines virtuelles et des recommandations. Chaque colonne représente un ensemble de recommandations, comme indiqué ci-dessous :

![Machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

L’icône qui s’affiche sous chaque recommandation vous aide à identifier rapidement les machines virtuelles qui requièrent votre attention, ainsi que le type de recommandation.

Dans l’exemple ci-dessus, une machine virtuelle est associée à une recommandation critique concernant des logiciels anti-programme malveillant. Pour obtenir plus d’informations sur la machine virtuelle, cliquez sur celle-ci. Un nouveau panneau représentant cette machine virtuelle s’affiche, comme illustré ci-dessous.

![Détails de sécurité pour les machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Ce panneau comporte les détails de la sécurité pour la machine virtuelle. L’action recommandée et le niveau de gravité de chaque recommandation sont affichés en bas du panneau.

###Surveillance des réseaux virtuels
Lorsque vous cliquez sur **Réseaux** dans la mosaïque **Intégrité des ressources**, le panneau **Réseaux** s’ouvre et affiche des informations détaillées, comme le montre l’illustration ci-dessous :

![Mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

####Recommandations pour la mise en réseau

En haut du panneau se trouve un récapitulatif des problèmes et au bas du panneau, la liste des réseaux surveillés. Ces informations sont similaires à celles qui figurent dans le panneau Intégrité des ressources.

![Panneau Mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-new2.png)

La section de répartition des états du réseau répertorie les problèmes de sécurité potentiels et propose des recommandations pour leur résolution. Voici des exemples de problèmes potentiels :

- Non-activation des groupes de sécurité réseau (NSG)
- Non-activation des groupes de sécurité réseau sur les machines virtuelles
- Restriction de l’accès externe via le point de terminaison externe public
- Intégrité des sous-réseaux

Lorsque vous cliquez sur l’une de ces recommandations, un nouveau panneau incluant plus de détails concernant la recommandation s’ouvre, comme illustré dans l’exemple ci-dessous.

![Limiter le point de terminaison](./media/security-center-monitoring/security-center-monitoring-fig11-new2.png)

Dans cet exemple, le panneau **Configure Missing Network Security Groups for Subnets** (Configurer les groupes de sécurité réseau manquants pour les sous-réseaux) contient une liste de sous-réseaux et de machines virtuelles sur lesquels la protection des groupe de sécurité réseau fait défaut. Un autre panneau s’ouvre lorsque vous cliquez sur le sous-réseau auquel vous souhaitez appliquer le groupe de sécurité réseau.

Dans le panneau **Choisir un groupe de sécurité réseau**, vous devez sélectionner le groupe de sécurité réseau le mieux adapté à votre sous-réseau, mais vous pouvez également créer un nouveau groupe de sécurité réseau.

####Section Mise en réseau

La section **Mise en réseau** contient une vue hiérarchique des ressources, comme indiqué ci-dessous :

![Arborescence réseau](./media/security-center-monitoring/security-center-monitoring-fig121-new2.png)

Ce tableau est trié (machines virtuelles et sous-réseaux) par niveau de gravité, comme indiqué ci-dessous :
- Rouge (en haut) : priorité élevée ; doivent être traités immédiatement 
- Orange : priorité moyenne ; doivent être traités dès que possible
- Vert (le dernier) : état d’intégrité

Dans cette hiérarchie, le premier niveau se décompose comme suit : [Réseaux virtuels](../virtual-network/virtual-networks-overview.md), [Passerelles de réseau virtuel](../vpn-gateway/vpn-gateway-site-to-site-create.md) et [Réseau virtuel (classique)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Le deuxième niveau comprend des sous-réseaux et le troisième niveau regroupe les machines virtuelles appartenant à ces sous-réseaux. La colonne de droite présente l’état actuel du groupe de sécurité réseau (NSG) pour ces ressources. L’exemple suivant illustre le résultat de la sélection de la machine virtuelle VM-CL-W1 :

![Arborescence réseau](./media/security-center-monitoring/security-center-monitoring-fig13-new2.png)

La partie inférieure de ce panneau regroupe les recommandations pour cette machine virtuelle, comme décrit ci-dessus. Vous pouvez cliquer sur une recommandation pour en savoir plus, ou appliquer la configuration ou le contrôle de sécurité nécessaire.

###Surveillance des ressources SQL
Lorsque vous cliquez sur l’option **SQL** de la mosaïque **Intégrité des ressources**, le panneau SQL s’ouvre et affiche des recommandations relatives aux problèmes, par exemple la désactivation de la fonction d’audit ou du chiffrement transparent des données. Il contient également des recommandations pour l’état général de la base de données.

![Intégrité des ressources SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Vous pouvez cliquer sur l’une de ces recommandations pour obtenir des informations détaillées qui vous aideront à résoudre le problème. L’exemple ci-dessous illustre en détail la recommandation **Audit de base de données non activé**.

![Intégrité des ressources SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Le panneau **Activer l’audit sur les bases de données SQL** comporte les informations suivantes :

- Une liste des bases de données SQL.
- Le serveur sur lequel elles se trouvent.
- Des informations indiquant si ce paramètre a été hérité du serveur ou s’il est unique dans cette base de données.
- L’état actuel du problème.
- Le niveau de gravité du problème.

Lorsque vous cliquez sur la base de données pour donner suite à cette recommandation, le panneau **Audit et détection des menaces** s’ouvre, comme indiqué ci-dessous.

![Intégrité des ressources SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Pour activer l’audit, sélectionnez simplement **ACTIVÉ** sous **Audit**, puis cliquez sur **Enregistrer**.

###Surveillance des applications
Si votre charge de travail Azure comprend des applications situées sur des [machines virtuelles Resource Manager](../resource-manager-deployment-model.md) ayant des ports web exposés (ports TCP 80 et 443), le Centre de sécurité peut les surveiller pour identifier les problèmes de sécurité potentiels et recommander des étapes de résolution. Lorsque vous cliquez sur la mosaïque **Applications**, le panneau **Applications** s’ouvre et affiche des recommandations dans la section Étapes de prévention. Il montre également la répartition des applications par hôte/IP virtuelle, comme indiqué ci-dessous.

![État de sécurité des applications](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Comme pour les recommandations précédentes, vous pouvez cliquer sur celle-ci pour afficher des informations détaillées sur le problème et la procédure à suivre pour le résoudre. L’exemple ci-dessous représente une application ayant été identifiée comme une application web non sécurisée. Quand vous sélectionnez une application considérée comme non sécurisée, un autre panneau s'ouvre avec l'option suivante disponible :

![Applications](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

Le panneau **Applications web non sécurisées** contient une liste de toutes les machines virtuelles qui contiennent des applications qui ne sont pas considérées comme sécurisées. La liste affiche le nom de la machine virtuelle, l’état actuel du problème et son niveau de gravité. Si vous cliquez sur cette application web, le panneau **Ajouter un pare-feu d’applications web** s’ouvre avec des options pour installer un pare-feu d’applications web (WAF) tiers, comme indiqué ci-dessous.

![Ajouter WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Étapes suivantes
Dans ce document, vous avez vu comment utiliser les fonctionnalités de surveillance du Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) – Découvrez comment configurer des paramètres de sécurité dans le Centre de sécurité Azure
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) -- Découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) – Recherchez des billets de blog sur la sécurité et la conformité Azure

<!---HONumber=AcomDC_0511_2016-->