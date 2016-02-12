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
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="yurid"/>

# Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
 Le Centre de sécurité vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Que sont les alertes de sécurité ?
 Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires, telles que les logiciels anti-programme malveillant et les pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Les alertes de sécurité comprennent une liste des alertes classées par ordre de priorité.

Vous pouvez connaître vos alertes actuelles en regardant la vignette **Alertes de sécurité**. Suivez les étapes ci-dessous pour obtenir plus d’informations sur chaque alerte :

1. Dans le tableau de bord Centre de sécurité, recherchez la vignette **Alertes de sécurité**.

    ![Vignette Alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Cliquez sur l’icône d’occurrences des alertes de sécurité. Le panneau **Alertes de sécurité** s’ouvre avec plus de détails sur les alertes, comme indiqué ci-dessous.

    ![Panneau Alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Les détails de chaque alerte sont affichés au bas du panneau. Pour les organiser à votre convenance, cliquez sur la colonne que vous voulez trier. La définition de chaque colonne est indiquée ci-dessous :

- **Alerte** : brève explication de l’alerte.
- **Nombre** : liste de toutes alertes d’un type spécifique qui ont été détectées un jour précis.
- **Détectée par** : service à l’origine du déclenchement de l’alerte.
- **Date** : date à laquelle l’événement s’est produit.
- **État** : état actuel de l’alerte. Il existe trois types d’états :
    - **Active** : l’alerte de sécurité a été détectée.
    - **Ignorée** : l’alerte de sécurité a été ignorée par l’utilisateur. Cet état est généralement attribué aux alertes qui ont été examinées, mais qui ont nécessité une correction ou qui ne correspondent pas à une attaque.

- **Gravité** : niveau de gravité (élevé, moyen ou bas).

### Répondre à des alertes de sécurité
De nombreuses activités peuvent indiquer une attaque potentielle de votre organisation. Par exemple, une capture réseau légitime effectuée par l’administrateur réseau peut ressembler à l’attaque d’un pirate. Dans d’autres cas, un système mal configuré peut amener le système de détection des intrusions à détecter de nombreux faux positifs, ce qui peut rendre plus difficile l’identification des incidents réels. Après avoir passé en revue les alertes de sécurité à l’aide du Centre de sécurité, vous pouvez commencer à prendre des mesures en fonction de la gravité d’une alerte.

Pour résoudre une alerte, sélectionnez-la. Un nouveau panneau s’ouvre alors sur la droite et affiche des informations détaillées sur l’alerte, comme dans l’illustration ci-dessous.

![Répondre aux alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

Dans ce cas, les alertes qui ont été déclenchées concernent une activité suspecte utilisant le protocole RDP. Les première, deuxième, troisième et quatrième colonnes affichent respectivement les ressources qui ont été attaquées, l’heure à laquelle cette attaque a été détectée, l’état de l’alerte et le niveau de gravité de l’attaque. Après avoir examiné ces informations, cliquez sur la ressource qui a été attaquée. Un nouveau panneau s’ouvre avec plus de suggestions sur la marche à suivre, comme illustré dans l’exemple ci-dessous.

![Suggestions sur la façon de traiter les alertes de sécurité dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)

> [AZURE.NOTE] La correction suggérée par le Centre de sécurité dépend de l’alerte de sécurité. Dans certains cas, vous pouvez être amené à utiliser d’autres fonctionnalités Azure pour appliquer la correction recommandée. Par exemple, pour cette attaque, la résolution consiste à mettre sur liste noire l’adresse IP à l’origine de l’attaque à l’aide d’une règle [ACL réseau](virtual-networks-acl.md) ou [groupe de sécurité réseau](virtual-networks-nsg.md).

### Gérer les alertes de sécurité
Vous pouvez filtrer les alertes en fonction de la date, de l’état et du niveau de gravité. Dans le panneau **Alertes de sécurité**, cliquez sur **Filtrer**, puis activez les options de votre choix, comme indiqué ci-dessous.

![Filtrer les alertes dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Le filtrage des alertes peut être utile quand vous avez besoin de restreindre l’étendue des informations qui s’affichent dans le tableau de bord. Par exemple, vous pouvez vouloir vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système.

Dans la plupart des cas, vous devez appliquer les recommandations indiquées dans les alertes de sécurité. Parfois, toutefois, vous pouvez être amené à ignorer une alerte, car il s’agit d’un faux positif pour votre environnement ou qu’elle indique un comportement normal pour une ressource donnée. Quel que soit le cas, vous pouvez masquer les recommandations pour une ressource spécifique à l’aide de l’option **Ignorer**.

Pour ignorer une tâche, cliquez dessus avec le bouton droit, afin d’afficher l’option **Ignorer**, comme dans l’illustration ci-dessous.

![Option Ignorer dans le centre de sécurité Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

Dans une collection d’alertes, chacune possède une description et une résolution. Les alertes que vous voyez dans le Centre de sécurité sont basées sur les scénarios d’attaque. Les alertes concernant les scénarios d’attaque suivants sont déclenchées par le moteur Microsoft :

- **Détection d’une attaque par force brute sur des données réseau** : scénario basé sur des modèles d’apprentissage automatique qui reposent sur les données de trafic réseau.
- **Détection d’une attaque par force brute sur des données de point de terminaison** : scénario basé sur des requêtes du Centre de sécurité des journaux des machines. Ainsi, vous pouvez distinguer les tentatives réussies et les échecs.
- **Machines virtuelles communiquant avec des adresses IP malveillantes** : scénario basé sur la découverte par le Centre de sécurité de machines compromises à cause de robots et sur la communication avec leurs serveurs de commande et contrôle (C&C).

## Étapes suivantes
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
- [FAQ du Centre de sécurité Azure](security-center-faq.md) : Forum Aux Questions concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!---HONumber=AcomDC_0204_2016-->