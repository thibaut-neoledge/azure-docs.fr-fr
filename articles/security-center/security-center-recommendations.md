<properties
   pageTitle="Gestion des recommandations de sécurité dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Gestion des recommandations de sécurité dans le Centre de sécurité Azure

Ce document explique comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure.

> [AZURE.NOTE]Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document constitue une présentation du service et utilise un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Quelles sont les recommandations de sécurité ?
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Quand des failles de sécurité potentielles sont identifiées, des recommandations sont créées. Les recommandations vous guident tout au long du processus de configuration du contrôle.

## Mise en œuvre des recommandations de sécurité

### Définition des recommandations

Dans [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md), vous apprendrez à :

- configurer des stratégies de sécurité ;
- activer la collecte des données ;
- choisir les recommandations à afficher dans le cadre de votre stratégie de sécurité.

Les recommandations de stratégie actuelles se concentrent sur les mises à jour système, les règles de ligne de base, les logiciels anti-programme malveillant, les [ACL pour les points de terminaison](virtual-machines-set-up-endpoints.md), les [groupes de sécurité réseau](virtual-networks-nsg.md) pour les sous-réseaux et les interfaces réseau, l’audit des bases de données SQL, le chiffrement transparent des données de base de données SQL et les pare-feu d’applications web. L’article [Définition de stratégies de sécurité](security-center-policies.md) fournit une description de chacune des recommandations.

### Recommandations concernant la surveillance

Après la définition d’une stratégie de sécurité, le Centre de sécurité Azure analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. La mosaïque **Recommandations** du panneau **Centre de sécurité** vous permet de connaître le nombre total de recommandations fournies par le Centre de sécurité Azure.

![][2]

Pour afficher les détails de chaque recommandation :

1. Cliquez sur la **mosaïque Recommandations** dans le panneau **Centre de sécurité**. Le panneau **Recommandations** s’ouvre.
2. Vous pouvez filtrer les recommandations par état et par niveau de gravité. Cliquez sur **Filtrer** dans le panneau **Recommandations**. Le panneau Filtrer s’ouvre et vous permet d’afficher uniquement certains états ou certains niveaux de gravité. ![][3]

3. Si vous déterminez qu’une recommandation n’est pas applicable, vous pouvez l’ignorer pour la faire disparaître. Il existe deux façons d’ignorer une recommandation. Cliquez avec le bouton droit sur un élément, puis sélectionnez **Ignorer**, ou pointez sur un élément, cliquez sur les trois points qui apparaissent à droite, puis sélectionnez **Ignorer**. Vous pouvez afficher les recommandations ignorées en cliquant sur **Filtrer**, puis en sélectionnant **Ignorées**. ![][4]

Les recommandations sont affichées dans un tableau où chaque ligne correspond à une recommandation. Les colonnes du tableau sont les suivantes :

- **DESCRIPTION** : explication de la recommandation et de ce qui doit être fait pour résoudre le problème
- **RESSOURCE** : ressources auxquelles s’appliquent les recommandations
- **ÉTAT** : état actuel de la recommandation :
  - **Ouverte** : la recommandation n’a pas encore été prise en compte
  - **En cours** : la recommandation est actuellement appliquée aux ressources, aucune action de votre part n’est nécessaire
  - **Résolue** : la recommandation a déjà été appliquée (la ligne est grisée)
- **GRAVITÉ** : donne le niveau de gravité de chaque recommandation :
  - **Élevée** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau), le problème doit être corrigé
  - **Moyenne** : certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité
  - **Faible** :existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite pas une attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les afficher.

Utilisez le tableau ci-dessous pour mieux comprendre les recommandations et leurs effets :

| Recommandation | Description |
|----- |-----|
| Activer la collecte des données pour des machines virtuelles ou des abonnements | Recommande l’activation de la collecte des données dans la stratégie de sécurité pour certaines machines virtuelles. |
| Résoudre une non-conformité aux règles de ligne de base | Recommande d’aligner les configurations de système d’exploitation sur les lignes de base recommandées, comme le fait de ne pas permettre l’enregistrement des mots de passe. |
| Appliquer des mises à jour système | Recommande le déploiement des mises à jour manquantes sur les machines virtuelles (Windows uniquement). |
| Configurer des listes ACL pour les points de terminaison | Recommande la configuration d’une liste de contrôle d’accès pour restreindre l’accès entrant aux machines virtuelles (de type Classique uniquement). |
| Ajouter un pare-feu d’applications web | Recommande le déploiement d’un pare-feu d’applications web pour les points de terminaison web (machines virtuelles de type Resource Manager uniquement). |
| Finaliser la configuration du pare-feu d’applications web | Pour terminer la configuration d’un pare-feu d’applications web, le trafic doit être redirigé vers l’appliance de pare-feu d’applications web. Cette recommandation permet d’apporter les modifications nécessaires à la configuration. |
| Activer le logiciel anti-programme malveillant | Recommande l’approvisionnement d’un logiciel anti-programme malveillant sur les machines virtuelles (Windows uniquement). |
| Activer des groupes de sécurité réseau sur les sous-réseaux et les interfaces réseau | Recommande l’activation de groupes de sécurité réseau sur les sous-réseaux et les interfaces réseau (machines virtuelles de type Resource Manager uniquement). |
| Restreindre l’accès à l’aide de points de terminaison externes publiques | Recommande la configuration de règles de trafic entrant pour les groupes de sécurité réseau. |
| Activer l’audit des serveurs SQL | Recommande l’activation de l’audit pour les serveurs SQL Azure (service Azure SQL uniquement, ne comprend pas les serveurs SQL exécutés sur des machines virtuelles). |
| Activer l’audit des bases de données SQL | Recommande l’activation de l’audit pour les bases de données SQL Azure (service Azure SQL uniquement, ne comprend pas les bases de données SQL exécutées sur des machines virtuelles). |
| Activer le chiffrement transparent des données des bases de données SQL | Recommande l’activation du chiffrement pour les bases de données SQL (service Azure SQL uniquement). |
| Déployer l’agent de machine virtuelle | Vous permet de connaître les machines virtuelles qui nécessitent l’agent de machine virtuelle. L’agent de machine virtuelle doit être installé sur les machines virtuelles pour approvisionner l’analyse des correctifs, l’analyse des lignes de base et les logiciels anti-programme malveillant. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle. |

### Application des recommandations
Après avoir examiné toutes les recommandations, vous pouvez décider d’en appliquer une en priorité. Les recommandations à appliquer en priorité sont celles dont le niveau de gravité est le plus élevé. Voyons comment appliquer une recommandation en prenant comme exemple la recommandation Activer le logiciel anti-programme malveillant :

1. Dans le panneau **Recommandations** , sélectionnez **Activer le logiciel anti-programme malveillant**. ![][5]

2. Dans le panneau **Installer le logiciel anti-programme malveillant**, sélectionnez les machines virtuelles pour lesquelles aucun logiciel anti-programme malveillant n’est activé, puis cliquez sur **Installer le logiciel anti-programme malveillant**.
3. Le panneau **Nouvelle ressource** s’ouvre et vous permet de sélectionner la solution anti-programme malveillant à utiliser. Sélectionnez **Microsoft Antimalware**.
4. Des informations supplémentaires sur la solution anti-programme malveillant s’affichent. Sélectionnez **Créer**.
5. Entrez les paramètres de configuration nécessaires dans le panneau **Ajouter une extension**, puis sélectionnez **OK**. ![][6]

[Microsoft Antimalware](azure-security-antimalware.md) est maintenant activé sur la machine virtuelle sélectionnée.

### Déploiement de solutions partenaires recommandées

L’une des recommandations peut concerner le déploiement d’une solution de sécurité intégrée fournie par un partenaire Microsoft. Prenons un exemple pour voir comment procéder :

1. Retournons au panneau **Recommandations**.
2.	Sélectionnez la recommandation **Sécuriser l’application web à l’aide du pare-feu d’applications web**. Le panneau **Applications web non protégées** s’ouvre. ![][7]
3. Sélectionnez une application web. Le panneau **Ajouter un pare-feu d’applications web** s’ouvre.
4. Sélectionnez **Pare-feu d’applications web Barracuda**. Un panneau s’ouvre et affiche des informations sur le **Pare-feu d’applications web Barracuda**.
5. Cliquez sur **Créer** dans le panneau d’informations. Le panneau **Nouveau pare-feu d’applications web** s’ouvre. Vous pouvez y **configurer des machines virtuelles** et fournir des **informations sur le pare-feu d’applications web**.
6. Sélectionnez **Configuration de machine virtuelle**. Dans le panneau **Configuration de machine virtuelle**, entrez les informations nécessaires pour créer la machine virtuelle qui doit exécuter le pare-feu d’applications web. ![][8]
7. Retournez au panneau **Nouveau pare-feu d’applications web**, puis sélectionnez **Informations sur le pare-feu d’applications web**. Dans le panneau **Informations sur le pare-feu d’applications web**, vous pouvez configurer le pare-feu d’applications web. L’étape 6 vous permet de configurer la machine virtuelle sur laquelle le pare-feu d’applications web doit être exécuté, et l’étape 7 vous permet d’approvisionner le pare-feu d’applications web.

8. Retournons au panneau **Recommandations**. Une nouvelle entrée a été générée après la création du pare-feu d’applications web : **Finaliser la configuration du pare-feu d’applications web**. Cela vous indique que vous devez terminer le processus de configuration du pare-feu d’applications web dans le réseau virtuel Azure pour qu’il puisse protéger l’application. ![][9]

9. Sélectionnez **Finaliser la configuration du pare-feu d’applications web**. Un nouveau panneau s’ouvre. Vous voyez que le trafic d’une application web doit être redirigé.
10. Sélectionnez l’application web. Un panneau s’ouvre et affiche des instructions pour finaliser la configuration du pare-feu d’applications web. Effectuez les étapes, puis cliquez sur **Restreindre le trafic**. Le Centre de sécurité Azure effectue la configuration à votre place. ![][10]

Les journaux du pare-feu d’applications web sont maintenant entièrement intégrés. Le Centre de sécurité Azure peut commencer automatiquement à collecter et à analyser les journaux afin de vous informer des alertes de sécurité les plus importantes.

## Étapes suivantes
Ce document vous a présenté les recommandations de sécurité du Centre de sécurité Azure. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md) – Découvrez comment configurer des stratégies de sécurité
- [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md) – Découvrez comment surveiller l’intégrité de vos ressources Azure
- [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md) – Découvrez comment gérer et résoudre les alertes de sécurité
- [FAQ du Centre de sécurité Azure](security-center-faq.md) – Forum Aux Questions concernant l’utilisation de ce service
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) – Recherchez des billets de blog sur la sécurité et la conformité Azure

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png
[7]: ./media/security-center-recommendations/secure-web-application.png
[8]: ./media/security-center-recommendations/vm-configuration.png
[9]: ./media/security-center-recommendations/finalize-waf.png
[10]: ./media/security-center-recommendations/restrict-traffic.png

<!---HONumber=AcomDC_1217_2015-->