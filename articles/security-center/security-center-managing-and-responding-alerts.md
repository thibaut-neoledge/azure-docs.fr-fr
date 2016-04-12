<properties
   pageTitle="Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/22/2016"
   ms.author="yurid"/>
 
# Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
 Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Que sont les alertes de sécurité ?
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des logiciels anti-programme malveillant et pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Une liste hiérarchisée d’alertes de sécurité, notamment les alertes provenant de solutions de partenaires intégrées, est affichée dans le Centre de sécurité, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.
 
Les chercheurs en matière de sécurité Microsoft analysent en permanence les nouvelles menaces qui apparaissent dans le monde entier, y compris les nouvelles tendances et stratégies d’attaque sur les produits Microsoft grand public et professionnels, ainsi que sur les services en ligne. Par conséquent, le Centre de sécurité peut mettre à jour ses algorithmes de détection au fur et à mesure que de nouvelles vulnérabilités et attaques sont découvertes ; ainsi, les clients ne se font pas rattraper par les menaces en constante évolution. Voici quelques exemples de menaces que le Centre de sécurité peut détecter :

- **Détection d’une attaque par force brute sur des données réseau** : ce type de détection utilise des modèles de Machine Learning qui se basent sur les données de trafic réseau de vos applications. Cela permet une détection plus fiable des tentatives d’accès provenant d’acteurs illégitimes au lieu d’utilisateurs légitimes.
- **Détection d’une attaque par force brute sur des données de point de terminaison** : scénario basé sur l’analyse des journaux des machines ; permet de distinguer les tentatives réussies des échecs.
- **Machines virtuelles communiquant avec des adresses IP malveillantes** : compare le trafic réseau aux renseignements de Microsoft en matière de menaces à l’échelle mondiale, et détecte les machines compromises qui communiquent avec leurs serveurs de commande et contrôle (C&C), et inversement.
- **Machines virtuelles compromises** : scénario basé sur l’analyse comportementale des journaux des machines et la corrélation avec les autres signaux ; identifie les événements anormaux qui résultent probablement de l’exploitation d’ordinateurs compromis.

## Gestion des alertes de sécurité 

Vous pouvez connaître vos alertes actuelles en consultant la mosaïque **Alertes de sécurité**. Suivez les étapes ci-dessous pour obtenir plus d’informations sur chaque alerte :

1. La mosaïque **Alertes de sécurité** s’affiche dans le tableau de bord Centre de sécurité.

    ![Vignette Alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Cliquez sur la mosaïque pour ouvrir le panneau **Alertes de sécurité**, qui fournit des détails supplémentaires sur les alertes, comme indiqué ci-dessous.

    ![Panneau Alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

Les détails de chaque alerte sont affichés au bas du panneau. Pour les organiser à votre convenance, cliquez sur la colonne que vous voulez trier. La définition de chaque colonne est indiquée ci-dessous :

- **Alerte** : brève explication de l’alerte.
- **Nombre** : liste de toutes les alertes d’un type spécifique qui ont été détectées un jour précis.
- **Détectée par** : service à l’origine du déclenchement de l’alerte.
- **Date** : date à laquelle l’événement s’est produit.
- **État** : état actuel de l’alerte. Il existe trois types d’états :
    - **Active** : l’alerte de sécurité a été détectée.
    - **Ignorée** : l’alerte de sécurité a été ignorée par l’utilisateur. Cet état est généralement attribué aux alertes qui ont été examinées, mais qui ont nécessité une correction ou qui ne correspondent pas à une attaque.

- **Gravité** : niveau de gravité (élevé, moyen ou bas).

Vous pouvez filtrer les alertes en fonction de la date, de l’état et du niveau de gravité. Le filtrage des alertes peut être utile quand vous avez besoin de restreindre le nombre d’alertes de sécurité qui s’affichent. Supposons que vous souhaitiez vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.

1. Cliquez sur **Filtrer** sur le panneau **Alertes de sécurité**. Le panneau **Filtrer** s’ouvre et vous pouvez sélectionner la date, l’état et les niveaux de gravité que vous souhaitez afficher.

	![Filtrer les alertes dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2. 	Pour certaines alertes, vous pouvez déterminer qu’il s’agit d’un faux positif pour votre environnement, ou que le comportement signalé est normal pour une ressource donnée. Si vous déterminez qu’une alerte de sécurité n’est pas applicable, vous pouvez l’ignorer, puis la faire disparaître à l’aide du filtre. Il existe deux façons d’ignorer une alerte de sécurité. Cliquez avec le bouton droit de la souris sur une alerte, puis sélectionnez **Ignorer**, ou pointez sur un élément, cliquez sur les trois points qui apparaissent à droite, puis sélectionnez **Ignorer**. Vous pouvez afficher les alertes de sécurité ignorées en cliquant sur **Filtrer**, puis en sélectionnant **Ignorées**.

	![Filtrer les alertes dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Répondre à des alertes de sécurité
Sélectionnez une alerte de sécurité pour en savoir plus sur les événements qui l’ont déclenchée et, le cas échéant, les étapes à suivre pour y remédier. Les alertes de sécurité sont regroupées par type et date d’apparition. Le fait de cliquer sur une alerte de sécurité ouvre un volet contenant une liste des alertes groupées.

![Répondre aux alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

Dans ce cas, les alertes qui ont été déclenchées concernent une activité suspecte utilisant le protocole RDP. Les première, deuxième, troisième et quatrième colonnes affichent respectivement les ressources qui ont été attaquées, l’heure à laquelle cette attaque a été détectée, l’état de l’alerte et le niveau de gravité de l’attaque. Après avoir examiné ces informations, cliquez sur la ressource qui a été attaquée. Un nouveau panneau s’ouvre avec plus de suggestions sur la marche à suivre, comme illustré dans l’exemple ci-dessous.

![Suggestions sur la façon de traiter les alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8-1.png)

Vous trouverez plus d’informations sur l’événement dans le champ **Alerte** de ce panneau. Ces informations permettent d’en savoir plus sur ce qui a déclenché l’alerte de sécurité, sur la ressource cible, sur l’adresse IP source (le cas échéant) et sur la manière de remédier au problème. Dans certains cas, l’adresse IP source sera vide (non disponible), car certains journaux d’événements Windows de la sécurité n’incluent pas l’adresse IP.

> [AZURE.NOTE] La correction suggérée par le Centre de sécurité dépend de l’alerte de sécurité. Dans certains cas, vous pouvez être amené à utiliser d’autres fonctionnalités Azure pour appliquer la correction recommandée. Par exemple, pour cette attaque, la correction consiste à mettre sur liste noire l’adresse IP à l’origine de l’attaque à l’aide d’une règle [ACL réseau](../virtual-network/virtual-networks-acl.md) ou de [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md).


## Étapes suivantes
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [FAQ Azure Security Center](security-center-faq.md) : Forum Aux Questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez des billets de blog sur la sécurité et la conformité Azure.

<!---HONumber=AcomDC_0323_2016-->