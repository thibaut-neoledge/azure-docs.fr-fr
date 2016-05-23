<properties
   pageTitle="Meilleures pratiques en matière de mises à jour logicielles sur Microsoft Azure IaaS | Microsoft Azure"
   description="L’article offre un ensemble de meilleures pratiques pour les mises à jour logicielles dans un environnement Microsoft Azure IaaS. Il est destiné aux professionnels de l’informatique et analystes de sécurité qui s’occupent au quotidien de la gestion du changement, la mise à jour du logiciel et la gestion des actifs, et notamment, des responsables en charge de la sécurité et de la conformité de l’organisation."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriD"
   manager="swadhwa"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/11/2016"
   ms.author="yurid"/>

#Bonnes pratiques pour les mises à jour sur Microsoft Azure IaaS

Avant d’entamer une discussion quelle qu’elle soit sur les bonnes pratiques dans l’environnement IaaS Azure, il est important de connaître les scénarios dont vous devrez gérer les mises à jour logicielles. Le diagramme ci-dessous devrait vous y aider :

![Modèles et responsabilités de cloud](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

Dans le modèle de centre de données traditionnel où l’infrastructure entière est située sur site, l’entière responsabilité de la gestion des mises à jour des systèmes d’exploitation, des applications, du réseau matériel (microprogramme) et des périphériques (routeurs, commutateurs, etc.) vous incombe. Dans un scénario IaaS, vous devez toujours gérer les mises à jour des systèmes d’exploitation et des applications. Toutefois, l’intégralité de l’infrastructure qui sous-tend les systèmes d’exploitation et les applications est gérée par Microsoft. Dans tous ces modèles, les clients restent propriétaires de leurs données et conservent la responsabilité de leur protection au niveau du point de terminaison.

Dans un scénario PaaS, vous aurez encore moins de responsabilités en matière de mises à jour logicielles, la gestion des mises à jour du système du système d’exploitation étant de la responsabilité de Microsoft. Dans un scénario SaaS, c’est Microsoft qui a la responsabilité des mises à jour logicielles de l’ensemble de la pile.

Ces mêmes principes s’appliquent dans un scénario hybride où votre entreprise utilise les machines virtuelles IaaS Azure communiquant avec des ressources locales, comme indiqué dans le schéma ci-dessous.

![Scénario hybride classique avec Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## Évaluation initiale

Même si votre entreprise utilise déjà un système de gestion des mises à jour et si des stratégies de mise à jour logicielle sont déjà en place, il est important de revoir les évaluations de stratégie précédentes fréquemment et de les mettre à jour en fonction des exigences actuelles. Cela signifie que vous devez connaître l’état actuel des ressources de votre entreprise. Pour obtenir des informations relatives à cet état, vous devez connaître :

-   les ordinateurs physiques et virtuels dans votre entreprise,

-   les systèmes d’exploitation et versions s’exécutant sur chacun de ces ordinateurs physiques et virtuels,

-   les mises à jour de logiciels actuellement installés sur chaque ordinateur (versions de service packs, mises à jour logicielles et autres modifications),

-   la fonction de chaque ordinateur dans votre entreprise,

-   les applications et les programmes en cours d’exécution sur chaque ordinateur,

-   la propriété et les coordonnées de chaque ordinateur,

-   les actifs présents dans votre environnement et leur valeur relative pour déterminer les domaines nécessitant le plus d’attention et de protection,

-   les problèmes de sécurité connus et les processus que votre entreprise a mis en place pour identifier de nouveaux problèmes de sécurité ou des modifications de niveau de sécurité.

-   Des contre-mesures ont été déployées pour sécuriser votre environnement.

Vous devez mettre à jour ces informations régulièrement, et elles doivent être à la disposition des personnes intervenant dans votre processus de gestion des mises à jour de logiciel.

## Établir une ligne de base

Une partie importante du processus de gestion des mises à jour logicielles crée des installations standard initiales des versions de système d’exploitation, applications et matériels informatiques de votre entreprise. Il s’agit des lignes de base. Une ligne de base est la configuration d’un produit ou d’un système établi à un moment précis dans le temps. Une application ou une ligne de base du système d’exploitation, par exemple, offre la possibilité de reconstruire un ordinateur ou un service à un état spécifique.

Les lignes de base fournissent les références permettant de détecter et de corriger et simplifier le processus de mise à jour logicielle, ce qui permet de réduire le nombre de mises à jour logicielles à déployer dans votre entreprise et en accroissant votre capacité à surveiller la conformité.

Après avoir effectué l’audit initial de votre entreprise, vous devez utiliser les informations obtenues de l’audit pour définir une ligne de base opérationnelle pour les composants de l’informatique au sein de votre environnement de production. Un certain nombre de lignes de base peut être requis, en fonction des différents types de matériels et logiciels déployés en production.

Par exemple, certains serveurs nécessitent une mise à jour logicielle pour éviter le blocage lorsqu’ils entament le processus d’arrêt lors de l’exécution de Windows Server 2012. Une ligne de base pour ces serveurs doit inclure cette mise à jour logicielle.

Dans les grandes organisations, il est souvent utile de diviser les ordinateurs de votre entreprise en catégories d’actifs et de garder chacune des catégories au niveau d’une ligne de base en utilisant les mêmes versions de logiciels et de mises à jour logicielles. Vous pouvez utiliser les catégories d’actifs en établissant une priorité pour la distribution de mise à jour de logiciel.

## S’abonner aux services de notification de mise à jour logicielle appropriés

Une fois que vous effectuez un audit initial du logiciel en cours d’utilisation dans votre entreprise, vous devez déterminer la meilleure méthode pour recevoir des notifications de nouvelles mises à jour pour chaque produit logiciel et la version. Selon le produit logiciel, la meilleure méthode de notification peut être les notifications par courrier électronique, les sites web ou les publications de l’ordinateur.

Par exemple, Microsoft Security Response Center (MSRC) répond à tous les problèmes liés à la sécurité sur les produits Microsoft et fournit le Service de Bulletin de sécurité Microsoft, une notification par courrier électronique gratuite des vulnérabilités récemment identifiées et des mises à jour logicielles publiées pour résoudre ces vulnérabilités. Vous pouvez vous abonner à ce service à l’adresse http://www.microsoft.com/technet/security/bulletin/notify.mspx

## Considérations relatives à la mise à jour logicielle

Une fois l’audit initial du logiciel en cours d’utilisation dans votre entreprise effectué, vous devez déterminer les exigences de configuration de votre système de gestion des mises à jour logicielle, qui varient en fonction du système de gestion des mises à jour logicielles que vous utilisez. S’il s’agit de WSUS, lisez [Meilleures pratiques Windows Server Update Services](https://technet.microsoft.com/library/Cc708536). S’il s’agit de System Center, lisez [Planification des mises à jour de logiciel dans le gestionnaire de configuration](https://technet.microsoft.com/library/gg712696).

Toutefois, il existe certaines considérations générales et des meilleures pratiques que vous pouvez appliquer quelle que soit la solution que vous utilisez, comme indiqué dans les sections qui suivent.

### Configuration de l’environnement

Prenez en compte les pratiques suivantes lors de la planification de la configuration d’environnement de gestion de mise à jour logicielle :

-   **Créer des regroupements de mises à jour logicielles de production en fonction de critères stables** : en général, l’utilisation de critères stables pour créer des regroupements de votre inventaire de mise à jour et de distribution de logiciels vous permet de simplifier tous les stades du processus de gestion des mises à jour logicielles. Les critères stables peuvent inclure la version du système d’exploitation client installé et du niveau du service pack, le rôle système ou l’organisation cible.

-   **Créer des regroupements de préproduction qui incluent les ordinateurs de référence** : le regroupement de préproduction doit inclure les configurations représentatives des versions de système d’exploitation, la ligne de logiciel d’organisation et d’autres logiciels s’exécutant dans votre entreprise.

Vous devez également prendre en compte l’emplacement dans lequel le serveur de mise à jour sera situé, s’il sera placé dans l’infrastructure IaaS Azure dans le cloud ou s’il sera sur site. Il s’agit d’une décision importante, car vous devez évaluer le volume de trafic entre des ressources locales et l’infrastructure Azure. Lisez la section [Connecter un réseau local à un réseau virtuel Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) pour plus d’informations sur la façon de connecter votre infrastructure locale à Azure.

Les options de conception qui déterminent l’emplacement du serveur de mise à jour varient également en fonction de votre infrastructure actuelle et de la mise à jour logicielle système que vous utilisez actuellement. Pour WSUS, lisez [Déployer Windows Server Update Services dans votre organisation](https://technet.microsoft.com/library/hh852340.aspx) et, pour System Center Configuration Manager, lisez [Planification des sites et des hiérarchies dans Configuration Manager](https://technet.microsoft.com/library/Gg712681.aspx).

### Sauvegarde

Les mises à jour régulières sont importantes, non seulement pour la plateforme de gestion des mises à jour logicielles, mais également pour les serveurs qui doivent être mis à jour. Pour les organisations ayant mis en place un [processus de gestion du changement](https://technet.microsoft.com/library/cc543216.aspx) le service informatique devra justifier les raisons pour lesquelles le serveur doit être mis à jour, les temps d’arrêt estimés et l’impact possible. Pour vous assurer que vous disposez d’une configuration de restauration en cas d’échec de la mise à jour, assurez-vous d’effectuer régulièrement une sauvegarde système.

Certaines options de sauvegarde d’IaaS d’Azure incluent :

-   [Protection de la charge de travail IaaS Azure à l’aide de Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Sauvegarde des machines virtuelles Azure](./backup/backup-azure-vms.md)

### Surveillance

Vous devez exécuter périodiquement des rapports pour contrôler le nombre de mises à jour installées ou manquantes, avec un statut incomplet, pour chaque mise à jour logicielle autorisée. De même, les rapports de mises à jour logicielles non autorisées encore peuvent faciliter des décisions de déploiement.

Vous pouvez également envisager les tâches qui suivent :

-   effectuer un audit des mises à jour de sécurité applicables et installées sur l’ensemble des ordinateurs de votre société ;

-   autoriser et déployer les mises à jour sur les ordinateurs appropriés,

-   assurer le suivi de l’inventaire et mettre à jour l’état d’installation et de progression pour tous les ordinateurs de votre société.

Outre les considérations générales expliquées dans cet article, vous devez prendre en compte les meilleures pratiques correspondant à chacun des produits, par exemple : si vous disposez d’une machine virtuelle SQL Server Azure, assurez-vous que vous suivez les mises à jour de recommandation dudit produit.

## Étapes suivantes

Utilisez les directives décrites dans cet article pour déterminer les meilleures options de mises à jour logicielles de machines virtuelles au sein d’IaaS Azure. Il existe de nombreuses similitudes entre les pratiques recommandées de mise à jour de logiciel dans un centre de données traditionnel et Azure IaaS, et il est par conséquent recommandé d’évaluer vos stratégies de mise à jour logicielle en cours pour inclure des machines virtuelles Azure et les meilleures pratiques figurant dans cet article dans votre processus de mise à jour logicielle globale.

<!---HONumber=AcomDC_0511_2016-->