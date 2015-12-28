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
   ms.date="12/16/2015"
   ms.author="yurid"/>

# Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure
Ce document est conçu pour vous aider à utiliser les fonctionnalités du Centre de sécurité Azure pour gérer et résoudre les alertes de sécurité.

> [AZURE.NOTE]Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Que sont les alertes de sécurité ?
Le Centre de sécurité Azure collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires, telles que les logiciels anti-programme malveillant et les pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs. Les alertes de sécurité comprennent une liste des alertes classées par ordre de priorité.

Vous pouvez connaître les alertes actuelles en regardant la mosaïque Alertes de sécurité. Suivez les étapes ci-dessous pour obtenir plus d’informations sur chaque alerte :

1. Dans le tableau de bord **Centre de sécurité Azure**, recherchez la vignette **Alertes de sécurité**. 

    ![Activation de la collecte des données](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Cliquez sur l’occurrence d’alerte de sécurité dans le graphique pour ouvrir le panneau **Alertes de sécurité** et afficher les détails de cette alerte, comme dans l’illustration ci-dessous :
    
    ![Activation de la collecte des données](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

Les détails de chaque alerte sont affichés au bas du panneau. Vous pouvez trier les alertes en cliquant sur les colonnes du panneau. La définition de chaque colonne est indiquée ci-dessous :

- **Alerte** : brève explication de l’alerte.
- **Nombre** : nombre d’alertes d’un type spécifique qui ont été détectées un jour précis.
- **Détectée par** : service à l’origine du déclenchement de l’alerte.
- **Date** : date à laquelle s’est produit l’événement.
- **État** : indique l’état actuel de l’alerte. Il existe trois types d’états :
    - **Active** : l’alerte de sécurité a été détectée.
    - **Ignorée** : l’alerte de sécurité a été ignorée par l’utilisateur. Cet état est généralement attribué aux alertes qui ont été examinées et qui ne correspondent pas à une attaque, ainsi qu’aux alertes qui ont été examinées et qui ont nécessité une correction.

- **Gravité** : indique le niveau de gravité, qui peut être élevé, moyen ou bas.
  

### Répondre à des alertes de sécurité
De nombreuses activités peuvent indiquer une attaque potentielle de votre organisation. Par exemple, une capture réseau légitime effectuée par l’administrateur réseau peut ressembler à l’attaque d’un pirate. Dans d’autres cas, un système mal configuré peut amener le système de détection des intrusions à détecter de nombreux faux positifs, ce qui peut rendre plus difficile l’identification des incidents réels. Après avoir passé en revue les alertes de sécurité à l’aide du Centre de sécurité Azure, vous pouvez commencer à prendre des mesures en fonction de la gravité des alertes.

Pour résoudre une alerte, sélectionnez-la. Un nouveau panneau s’ouvre alors sur la droite et affiche des informations détaillées sur l’alerte, comme dans l’illustration ci-dessous :

![Activation de la collecte des données](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

Dans ce cas, les alertes qui ont été déclenchées concernent une activité suspecte utilisant le protocole RDP. La première colonne affiche les ressources qui ont été attaquées, l’heure à laquelle cette attaque a été détectée, ainsi que l’état de l’alerte et le niveau de gravité. Après avoir lu ces informations, cliquez sur la ressource qui a été attaquée. Un nouveau panneau s’ouvre et affiche des suggestions supplémentaires concernant la marche à suivre, comme le montre l’exemple ci-dessous :

![Activation de la collecte des données](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]Les corrections suggérées par le Centre de sécurité Azure varient en fonction de l’alerte de sécurité. Dans certains cas, vous devrez peut-être utiliser d’autres fonctionnalités Azure pour appliquer les corrections recommandées. Par exemple, pour cette attaque, la résolution consiste à mettre sur liste noire l’adresse IP à l’origine de l’attaque à l’aide d’une règle [ACL réseau](virtual-networks-acl.md) ou [Groupe de sécurité réseau](virtual-networks-nsg.md).

### Gestion des alertes de sécurité
Vous pouvez filtrer les alertes en fonction de la date, de l’état et du niveau de gravité. Dans le panneau Alertes de sécurité, cliquez sur Filtrer, puis activez les options de votre choix, comme indiqué ci-dessous :

![Activation de la collecte des données](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Le filtrage des alertes peut être utile quand vous avez besoin de restreindre l’étendue des informations qui s’affichent dans le tableau de bord. Par exemple, vous pouvez vouloir vérifier les alertes de sécurité qui se sont produites au cours des dernières 24 heures, car vous recherchez une violation de sécurité potentielle du système. Même si la plupart des alertes de sécurité contiennent des recommandations qui doivent être appliquées, il arrive que vous puissiez ignorer certaines alertes, car, elles constituent de faux positifs pour votre environnement, ou bien parce qu’il s’agit d’un comportement normal pour une ressource en particulier. Quel que soit le cas, vous pouvez masquer les recommandations pour une ressource spécifique à l’aide de l’option **Ignorer**.

Pour ignorer une tâche, cliquez sur la ressource pour afficher les informations supplémentaires ou appliquer la configuration recommandée. Cliquez avec le bouton droit sur la tâche à ignorer pour afficher l’option **Ignorer**, comme dans l’illustration ci-dessous :

![Activation de la collecte des données](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

Dans une collection d’alertes, chacune possède une description et une résolution. Les alertes que vous voyez dans le Centre de sécurité Azure sont basées sur des scénarios d’attaque. Les scénarios d’attaque qui suivent sont déclenchés par le moteur Microsoft :

- **Détection d’une attaque par force brute sur des données réseau** : ces détections utilisent des modèles de machine learning qui se basent sur les données de trafic réseau. 
- **Détection d’une attaque par force brute sur des données de point de terminaison** : ces détections sont basées sur des requêtes du Centre de sécurité Azure des journaux des machines. Ainsi, vous pouvez distinguer les tentatives réussies et les échecs. 
- **Machines virtuelles communiquant avec des adresses IP malveillantes** : ces détections sont basées sur la découverte par le Centre de sécurité Azure de machines compromises à cause de robots et sur la communication avec leurs serveurs de commande et contrôle (C&C). 

## Étapes suivantes
Dans ce document, vous avez appris à configurer des stratégies de sécurité dans le Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure
- [FAQ du Centre de sécurité Azure](security-center-faq.md) : questions fréquentes concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure

<!---HONumber=AcomDC_1217_2015-->