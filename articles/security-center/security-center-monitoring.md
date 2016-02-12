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
   ms.date="01/29/2016"
   ms.author="yurid"/>

#Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure
Ce document a pour but de vous aider à utiliser les fonctionnalités de surveillance du Centre de sécurité Azure afin de contrôler la conformité aux stratégies.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

##Qu’est-ce que la surveillance de l’intégrité de la sécurité ?
Nous pensons souvent que la surveillance consiste à veiller et à attendre qu’un événement se produise pour y répondre. Ici, la surveillance de la sécurité fait référence à une stratégie proactive qui audite vos ressources afin d’identifier les systèmes qui ne répondent pas aux normes organisationnelles ou aux meilleures pratiques.

##Surveillance de l'intégrité de la sécurité
Après l’activation des [stratégies de sécurité](security-center-policies.md) pour les ressources d’un l’abonnement, le Centre de sécurité Azure analyse la sécurité de vos ressources afin d’identifier les vulnérabilités potentielles. Contrairement aux informations sur la configuration réseau qui sont instantanément disponibles, l’affichage des informations concernant la configuration des machines virtuelles (telles que l’état des mises à jour de sécurité ou la configuration du système d’exploitation) peut prendre une heure ou plus. Vous pouvez afficher l'état de sécurité de vos ressources, ainsi que les problèmes éventuels, dans le panneau **Intégrité des ressources**. Vous pouvez également afficher une liste des problèmes dans le panneau **Recommandations**.

Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md).

La mosaïque **Intégrité des ressources** vous permet de surveiller l’état de sécurité de vos ressources. Vous serez amené à voir des problèmes avec un niveau de gravité élevé ou moyen qui nécessitent une attention particulière, comme indiqué ci-dessous :

![Intégrité des ressources](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

Les stratégies de sécurité qui sont activées ont un impact sur les types de contrôles surveillés. Si le Centre de sécurité Azure identifie une vulnérabilité qui doit être corrigée, par exemple, une machine virtuelle à laquelle il manque des mises à jour de sécurité ou un sous-réseau sans [groupe de sécurité réseau](virtual-networks-nsg.md), cette vulnérabilité sera identifiée dans ce panneau.

###Machine virtuelle
Quand vous cliquez sur **Machines virtuelles** dans la mosaïque **Intégrité des ressources**, le panneau **Machines virtuelles** s’ouvre et affiche des informations détaillées sur les étapes d’intégration et de prévention, ainsi que la liste de toutes les machines virtuelles surveillées par le Centre de sécurité Azure, comme indiqué ci-dessous :

![Mise à jour système manquante par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

Le panneau comprend trois sections :

- Étapes de mise en route
- Recommandations pour machines virtuelles
- Machines virtuelles

Dans chaque section, vous pouvez sélectionner une option afin d’afficher plus de détails sur l’étape recommandée pour résoudre le problème. Les sections ci-dessous apportent plus de détails sur ces sujets.

####Étapes de mise en route
Cette section indique la quantité totale de machines virtuelles qui ont été initialisées pour la collecte des données et son état actuel. Une fois la collecte des données initialisée sur chaque machine virtuelle, cette dernière est prête à recevoir les stratégies de sécurité du Centre de sécurité Azure. Lorsque vous cliquez sur cette entrée, le panneau **Initialisation en cours de la collecte des données** s'ouvre vous permettant de voir le nom des machines virtuelles et l'état actuel de la collecte des données, comme dans la colonne sur l'**état d'installation**, comme indiqué ci-dessous :

![État d'initialisation](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Recommandations pour machines virtuelles
Cette section contient une série de recommandations pour chaque machine virtuelle contrôlée par le Centre de sécurité Azure. La première colonne contient la description de la recommandation, la deuxième colonne contient la quantité totale de machines virtuelles qui sont affectées par ces recommandations et la troisième colonne contient la gravité, comme indiqué ci-dessous :

![Recommandations pour les machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Chaque recommandation dispose d'un ensemble d'actions qui peuvent être effectuées lorsque vous cliquez dessus. Par exemple, si vous cliquez sur **Mises à jour système manquantes**, le panneau **Mises à jour système manquantes** s'ouvre et affiche la liste des machines virtuelles auxquelles il manque des correctifs, ainsi que le niveau de gravité, comme indiqué ci-dessous :

![Mises à jour système manquantes](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

Le panneau Mises à jour système manquantes affiche un tableau avec les informations suivantes :

- **MACHINE VIRTUELLE** : nom de la machine virtuelle sur laquelle il manque des mises à jour.
- **MISES À JOUR DU SYSTÈME** : quantité de mises à jour système manquantes.
- **HEURE DE LA DERNIÈRE ANALYSE** : heure de la dernière analyse par le Centre de sécurité Azure de la machine virtuelle pour contrôler les mises à jour.
- **ÉTAT** : état actuel de la recommandation : 
	- **Ouverte** : la recommandation n’a pas encore été prise en compte
	- **En cours** : la recommandation est actuellement appliquée aux ressources, aucune action de votre part n’est nécessaire
	- **Résolue** : la recommandation a déjà été appliquée (la ligne est grisée)
- **GRAVITÉ** : donne le niveau de gravité de chaque recommandation : 
	- **Élevée** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau), le problème doit être corrigé
	- **Moyenne** : certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité
	- **Faible** :existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite pas une attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les afficher.

Pour afficher des informations détaillées sur les mises à jour recommandées, cliquez sur le nom de la machine virtuelle concernée. Un nouveau panneau s'ouvre pour cette machine virtuelle et affiche la liste des mises à jour manquantes, comme indiqué ci-dessous :

![Mises à jour système manquantes par machine virtuelle](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Les recommandations de sécurité de ce panneau sont les mêmes que celles figurant dans le panneau Recommandations. Pour plus d’informations sur la résolution des recommandations, consultez [Implémentation des recommandations de sécurité dans le Centre de sécurité Azure](security-center-recommendations.md). Cela s’applique non seulement aux machines virtuelles, mais aussi à toutes les ressources qui sont disponibles dans la mosaïque Intégrité des ressources.

####Machines virtuelles
La section Machines virtuelles vous donne une présentation de toutes les machines virtuelles et des recommandations. Chaque colonne de recommandation représente un ensemble de recommandations, comme indiqué ci-dessous :

![Machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

L'icône qui s'affiche sous chaque recommandation vous aide à identifier rapidement les machines virtuelles qui requièrent votre attention et pour quel type de recommandation.

Dans l'exemple ci-dessus, une machine virtuelle a une recommandation critique concernant le logiciel anti-programme malveillant. Pour obtenir plus d'informations sur la machine virtuelle, cliquez dessus pour ouvrir un nouveau panneau représentant cette machine virtuelle, comme indiqué ci-dessous :

![Détails de sécurité pour les machines virtuelles](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

Ce panneau comporte les détails de la sécurité pour la machine virtuelle. L'action recommandée et la gravité de chaque recommandation sont affichées en bas du panneau.

###Mise en réseau
Les réseaux virtuels surveillés par le Centre de sécurité Azure apparaissent dans la section d’état de la prévention du panneau Réseaux. Quand vous cliquez sur **Réseaux** dans la mosaïque Intégrité des ressources, le panneau **Réseaux** s’ouvre et affiche des informations détaillées, comme le montre l’illustration ci-dessous :

![Mise en réseau](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

Lorsque vous ouvrez ce panneau, deux sections s'affichent : - Recommandations pour la mise en réseau - Mise en réseau
 
Dans chaque section, vous pouvez sélectionner une option afin d’afficher plus de détails sur l’étape recommandée pour résoudre le problème. Les sections ci-dessous apportent plus de détails sur ces sujets.

####Recommandations pour la mise en réseau

En haut du panneau se trouve un récapitulatif des problèmes et au bas du panneau, la liste des réseaux surveillés. Ces informations sont similaires à celles qui figurent dans le panneau Intégrité des ressources.

Dans la section de répartition des états du réseau figurent des étapes de prévention, telles que [ACL sur les points de terminaison](virtual-machines-set-up-endpoints.md) désactivés, [Groupes de sécurité réseau](virtual-networks-nsg.md) désactivés, Sous-réseaux intègres et **Accès dans NSG non restreint**. Lorsque vous cliquez sur l'une de ces recommandations, un nouveau panneau avec plus de détails concernant la recommandation s'ouvre, comme illustré dans l'exemple ci-dessous :

![Point de terminaison](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

Dans cet exemple, le panneau **Restreindre l'accès via le point de terminaison externe public** dispose d'une liste de groupes de sécurité réseau (NSG) qui font partie de cette alerte, le sous-réseau et le réseau auxquels ce groupe de sécurité réseau est associé, l'état actuel de cette recommandation et sa gravité. Si vous cliquez sur le groupe de sécurité réseau, un autre panneau s'ouvre, comme indiqué ci-dessous :

![Limiter le point de terminaison](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

Ce panneau comporte les informations du groupe de sécurité réseau, son emplacement et la liste des règles de trafic entrant qui sont actuellement activées. La partie inférieure de ce panneau indique la machine virtuelle qui est associée à ce groupe de sécurité réseau. Si vous souhaitez activer les règles de trafic entrant pour bloquer un port non souhaité qui est actuellement ouvert ou modifier la source de la règle de trafic entrant actuelle, cliquez sur le bouton **Modifier la règle de trafic entrant** en haut du panneau.

####Mise en réseau
La section **Mise en réseau** contient un affichage hiérarchique des groupes de ressources, du sous-réseau et de l'interface réseau associés à votre machine virtuelle, comme indiqué ci-dessous :

![Arborescence réseau](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

Cette section différencie les [machines virtuelles basées sur Resource Manager des machines virtuelles classiques](resource-manager-deployment-model.md) pour vous aider à identifier rapidement si les fonctionnalités réseau Azure Service Management (ASM/classique) ou Azure Resource Management (ARM) sont disponibles pour la machine virtuelle. Si vous décidez d'accéder aux propriétés d'une carte d'interface réseau à partir de cet emplacement, vous devrez développer le sous-réseau et cliquer sur le nom de la machine virtuelle. Si vous effectuez cette action pour une machine virtuelle basée sur Resource Manager, un nouveau panneau semblable à ce qui suit s'affiche :

![Arborescence réseau](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

Ce panneau comporte un résumé de la carte d'interface réseau et les recommandations actuelles pour celle-ci. Si la machine virtuelle que vous avez sélectionnée est une machine virtuelle classique, un nouveau panneau avec des options différentes s'affiche, comme indiqué ci-dessous :

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

Ce panneau vous permet d'apporter des modifications au port public, au port privé et vous permet de créer une liste ACL pour cette machine virtuelle.

###SQL
Quand vous cliquez sur **SQL** dans la mosaïque **Intégrité des ressources**, le panneau SQL s'ouvre et affiche des recommandations relatives aux problèmes, telles que « audit désactivé » ou « chiffrement transparent des données désactivé » ou bien l'état d'intégrité général de la base de données.

![Intégrité des ressources SQL](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

Vous pouvez cliquer sur l’une de ces recommandations pour obtenir des informations détaillées qui vous aideront à résoudre le problème. L'exemple ci-dessous illustre en détail la recommandation **Audit de base de données non activé**.

![Intégrité des ressources SQL](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Le panneau **Activer l'audit sur les bases de données SQL** contient une liste des bases de données SQL, le serveur sur lequel elles se trouvent, si ce paramètre a été hérité du serveur ou s'il est unique dans cette base de données, l'état actuel de ce problème et le niveau de gravité. Lorsque vous cliquez sur la base de données pour résoudre cette recommandation, le panneau **Audit et détection des menaces** s'ouvre, comme indiqué ci-dessous :

![Intégrité des ressources SQL](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

Pour activer cette fonctionnalité, sélectionnez simplement **ON** sous **Audit**, puis cliquez sur **Enregistrer**.

###Applications
Si votre charge de travail Azure comprend des applications situées sur des [machines virtuelles Resource Manager](resource-manager-deployment-model.md) ayant des ports web exposés (ports TCP 80 et 443), le Centre de sécurité Azure peut les surveiller pour identifier les problèmes de sécurité potentiels et recommander des étapes de résolution. Quand vous cliquez sur la mosaïque **Applications**, le panneau **Applications** s’ouvre et affiche des recommandations dans la section Étapes de prévention, ainsi que la répartition des applications par hôte/adresse IP virtuelle, comme le montre l’illustration ci-dessous :

![État de sécurité des applications](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Comme pour les ressources précédentes, vous pouvez cliquer sur les recommandations pour afficher des informations détaillées et résoudre le problème. L’exemple ci-dessous représente une application ayant été identifiée comme une **Application web non sécurisée**. Quand vous sélectionnez une application considérée comme non sécurisée, un autre panneau s'ouvre avec l'option suivante disponible :

![Applications](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

Le panneau **Applications web non sécurisées** contient une liste de toutes les machines virtuelles qui contiennent des applications qui ne sont pas considérées comme sécurisées. La liste affiche le nom de la machine virtuelle, l'état actuel du problème et la sécurité. Si vous cliquez sur cette application web, le panneau **Ajouter un pare-feu d'applications web** s'ouvre avec des options pour installer un pare-feu d'applications web (WAF) tiers, comme indiqué ci-dessous :

![Ajouter WAF](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Étapes suivantes
Dans ce document, vous avez vu comment utiliser les fonctionnalités de surveillance du Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) – Découvrez comment configurer des paramètres de sécurité dans le Centre de sécurité Azure
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) – Recherchez des billets de blog sur la sécurité et la conformité Azure

<!---HONumber=AcomDC_0204_2016-->