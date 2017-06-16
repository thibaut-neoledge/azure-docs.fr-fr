---
title: "Prise en charge de l’architecture mutualisée pour la réplication de machines virtuelles VMware sur Azure (programme CSP) | Microsoft Docs"
description: "Décrit comment déployer Azure Site Recovery dans un environnement mutualisé pour orchestrer la réplication, le basculement et la récupération de machines virtuelles VMware locales vers Azure via le programme CSP avec le portail Azure"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: ed484afc59bbf48490e3ff4389e8e28c71a5e471
ms.contentlocale: fr-fr
ms.lasthandoff: 02/17/2017


---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-the-csp-program"></a>Prise en charge de la mutualisation dans Azure Site Recovery pour répliquer des machines virtuelles VMware vers Azure via le programme CSP

Azure Site Recovery prend en charge les environnements mutualisés pour les abonnements de locataire. La mutualisation est également prise en charge pour les abonnements de locataire créés et gérés par le biais du programme CSP. Cet article détaille les conseils d’implémentation et de gestion de scénarios VMware vers Azure mutualisés. La création et la gestion des abonnements de locataire via CSP sont également détaillées.

Notez que ces conseils proviennent principalement de la documentation existante sur la réplication des machines virtuelles VMware vers Azure. Ces conseils doivent être utilisés avec cette [documentation](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Environnements mutualisés
Il existe trois modèles mutualisés principaux :

1.  **Fournisseur de services d’hébergement partagé (HSP)** : le partenaire possède l’infrastructure physique et utilise des ressources partagées (vCenter, centres de données, stockage physique, etc.) pour héberger des machines virtuelles à plusieurs locataires sur la même infrastructure. La gestion de la récupération d’urgence peut être réalisée par le partenaire en tant que service géré, ou être détenue par le locataire en tant qu’une solution de récupération d’urgence en libre-service.
2.  **Fournisseur de services d’hébergement dédié** : le partenaire possède l’infrastructure physique mais utilise des ressources dédiées (plusieurs vCenter, magasins de données physiques, etc.) pour héberger les machines virtuelles de chaque locataire sur une infrastructure distincte. La gestion de la récupération d’urgence peut là encore être réalisée par le partenaire ou en libre-service par le locataire.
3.  **Fournisseur de services gérés (MSP)** : le client possède l’infrastructure physique qui héberge les machines virtuelles et les partenaires réalisent l’activation et la gestion de la récupération d’urgence.

## <a name="shared-hosting-multi-tenant-guidance"></a>Conseils de mutualisation de l’hébergement partagé
Ces conseils détaillent le scénario d’hébergement partagé. Les deux autres scénarios sont des sous-ensembles du scénario d’hébergement partagé et reposent sur les mêmes principes. Les différences sont décrites à la fin des conseils d’hébergement partagé.

La condition de base dans un scénario mutualisé consiste à isoler les différents locataires, c’est-à-dire qu’un locataire ne doit pas être capable de savoir ce qu’un autre locataire a hébergé. Dans un environnement entièrement géré par un partenaire, cette exigence n’est pas aussi importante que dans un environnement en libre-service où elle peut être critique. Ces conseils partent du principe que l’isolation des locataires est requise.

L’architecture se présente comme suit :

![architecture-hsp-partagé](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)

**Figure 1 : Scénario d’hébergement partagé avec un vCenter**

Comme illustré dans la représentation ci-dessus, chaque client aura un serveur d’administration distinct. Cela vise à limiter l’accès du locataire aux machines virtuelles spécifiques du locataire afin activer l’isolation des locataires. Le scénario de réplication de machine virtuelle VMware utilise le serveur de configuration pour gérer les comptes afin de découvrir des machines virtuelles et d’installer des agents. Nous suivons les mêmes principes pour les environnements mutualisés, en limitant en plus la découverte de machines virtuelles via le contrôle d’accès vCenter.

L’exigence d’isolation des données nécessite que toutes les informations sensibles sur l’infrastructure (telles que les informations d’identification d’accès) ne soient pas divulguées aux locataires. Pour cette raison, nous recommandons que tous les composants du serveur d’administration (serveur de configuration (CS), serveur de traitement (PS) et serveur cible maître (MT)) restent sous le contrôle exclusif du partenaire. Cela inclut le PS d’évolution.

### <a name="every-cs-under-the-multi-tenant-scenario-uses-two-accounts"></a>Dans le scénario mutualisé, chaque CS utilise deux comptes :

- **Compte d’accès vCenter** : ce compte est utilisé pour découvrir les machines virtuelles du locataire. C’est également le compte auquel les autorisations d’accès vCenter sont affectées (voir la section suivante). Il est recommandé que le partenaire entre lui-même ces informations d’identification dans l’outil de configuration afin d’éviter des fuites d’accès accidentelles.
- **Compte d’accès de machine virtuelle** : ce compte est utilisé pour installer l’agent de mobilité sur les machines virtuelles du locataire via une transmission automatique. Il s’agit généralement d’un compte de domaine qu’un locataire peut fournir au partenaire. Il peut également être géré directement par le partenaire. Si le client ne souhaite pas partager directement ses informations avec le partenaire, le locataire peut être autorisé à entrer ses informations d’identification via un accès limité dans le temps à CS ou peut installer des agents de mobilité manuellement avec l’aide du partenaire.

### <a name="requirements-for-vcenter-access-account"></a>Conditions requises pour le compte d’accès vCenter

Comme détaillé dans la section précédente, le CS doit être configuré avec un compte auquel un rôle spécial est affecté. Il est important de noter que cette affectation de rôle doit être effectuée sur le compte d’accès vCenter pour chaque objet vCenter et qu’elle ne doit pas être propagée sur les objets enfants. L’isolation des locataires est ainsi garantie, car la propagation d’accès peut entraîner un accès accidentel à d’autres objets également

![autorisations-sans-propagation](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

Une alternative consiste à affecter le compte d’utilisateur et un rôle au niveau de l’objet de centre de données et à les propager aux objets enfants. Après cela, un rôle « Aucun accès » doit être affecté au compte pour chaque objet (par exemple, les machines virtuelles d’autres locataires) qui ne doit pas être accessible à un locataire particulier. Ceci est fastidieux et entraîne des contrôles d’accès accidentels, car chaque nouvel objet enfant créé hérite automatiquement de l’accès de son parent. Il est donc conseillé d’adopter la première approche.

La procédure d’accès de compte vCenter est la suivante :

1.  Créez un nouveau rôle en clonant le rôle prédéfini « Lecture seule » et donnez-lui un nom pratique (comme Azure_Site_Recovery dans cet exemple).
2.  Attribuez les autorisations suivantes à ce rôle :
 *  Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle
 *  Réseau -> Attribution de réseau
 *  Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension
 *  Tâches -> Créer une tâche, Mettre à jour une tâche
 *  Machine virtuelle -> Configuration
 *  Machine virtuelle -> Interagir -> Répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware
 *  Machine virtuelle -> Stock -> Créer, inscrire, désinscrire
 *  Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle
 *  Machine virtuelle -> Instantanés -> Supprimer les instantanés.

    ![autorisations-rôle](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3.  Attribuez un niveau d’accès au compte vCenter (utilisé dans le CS du locataire) pour différents objets comme suit :

| **Object** | **Rôle** | **Remarques** |
| --- | --- | --- |
| vCenter | Lecture seule | Nécessaire uniquement pour autoriser l’accès vCenter pour la gestion de différents objets. Cette autorisation peut être supprimée si le compte ne sera jamais fourni à un locataire ou utilisé pour des opérations de gestion sur le vCenter. |
| Centre de données | Azure_Site_Recovery |  |
| Hôte et cluster hôte | Azure_Site_Recovery | Vérifiez à nouveau que l’accès se trouve au niveau de l’objet de sorte que seuls les hôtes comprenant des machines virtuelles de locataire soient accessibles avant le basculement et après la restauration automatique. |
| Magasin de données et cluster de magasin de données | Azure_Site_Recovery | Identique à ce qui précède |
| Réseau | Azure_Site_Recovery |  |
| Serveur d’administration | Azure_Site_Recovery | Cela inclut l’accès à tous les composants (CS, PS et MT) si l’un de ceux-ci ne se trouve pas sur l’ordinateur du CS. |
| Machines virtuelles de locataire | Azure_Site_Recovery | Veillez à ce que les nouvelles machines virtuelles d’un locataire disposent également de cet accès. Sinon, elles ne pourront pas être découvertes via le portail Azure. |

L’accès au compte vCenter est maintenant terminé. Cette procédure répond aux conditions minimales requises des autorisations pour effectuer des opérations de restauration. Notez que ces autorisations d’accès peuvent également être utilisées avec vos stratégies existantes. Modifiez simplement vos autorisations existantes pour inclure des autorisations de rôle du point 2 détaillé ci-dessus.

Pour limiter les opérations de récupération d’urgence jusqu’à l’état de basculement, à savoir sans les fonctionnalités de restauration automatique, suivez la procédure ci-dessus, mais au lieu d’affecter le rôle « Azure_Site_Recovery » au compte d’accès vCenter, affectez simplement un rôle « Lecture seule » à ce compte. Ce jeu d’autorisations permet la réplication et le basculement de la machine virtuelle, et n’autorise pas la restauration automatique. Notez que tout le reste de la procédure ci-dessus ne change pas. Chaque autorisation est toujours affectée au niveau de l’objet uniquement et n’est pas propagée aux objets enfants pour garantir l’isolation des locataires et limiter la découverte de la machine virtuelle.

## <a name="other-multi-tenant-environments"></a>Autres environnements mutualisés

Les conseils ci-dessus décrivent de manière détaillée la configuration d’un environnement mutualisé pour une solution d’hébergement partagé. Les deux autres solutions principales sont dédiées à l’hébergement et au service géré. Leur architecture se présente comme ci-dessous :

### <a name="dedicated-hosting-solution"></a>Solution d’hébergement dédié

![architecture-hsp-partagé](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)

**Figure 2 : Scénario d’hébergement dédié avec plusieurs vCenter**

La différence architecturale est telle que l’infrastructure de chaque locataire est configurée pour ce locataire uniquement. Le fournisseur d’hébergement toujours doit suivre les étapes CSP détaillées pour l’hébergement partagé, mais ne doit pas nécessairement se soucier de l’isolation des locataires puisque les locataires sont isolés à l’aide de vCenter distincts. L’approvisionnement CSP reste inchangé.

### <a name="managed-service-solution"></a>Solution de service géré

![architecture-hsp-partagé](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)

**Figure 3 : Scénario de service géré avec plusieurs vCenter**

La différence architecturale est telle que l’infrastructure de chaque locataire est également séparée physiquement des autres locataires. Ce scénario s’applique généralement lorsque le locataire possède l’infrastructure et souhaite simplement disposer d’un fournisseur de solutions pour gérer la récupération d’urgence. Là encore, le partenaire toujours doit suivre les étapes CSP détaillées pour l’hébergement partagé, mais ne doit pas nécessairement se soucier de l’isolation des locataires puisque les locataires sont isolés physiquement via des infrastructures différentes. L’approvisionnement CSP reste inchangé.


## <a name="csp-program-overview"></a>Vue d’ensemble du programme CSP
Le [programme](https://partner.microsoft.com/en-US/cloud-solution-provider) Fournisseur de solutions cloud (CSP) de Microsoft met en avant des récits de collaboration avec les partenaires pour offrir tous les services cloud de Microsoft, notamment O365, EMS et Microsoft Azure. Il permet à nos partenaires d’établir une relation de bout en bout avec les clients et de devenir le point de contact principal. Grâce à CSP, un partenaire peut déployer des abonnements Azure pour les clients et combiner ces abonnements avec leurs propres offres personnalisées à valeur ajoutée.

Dans le cas d’Azure Site Recovery, les partenaires peuvent gérer l’ensemble de la solution de récupération d’urgence pour les clients directement via CSP, ou utiliser CSP pour configurer les environnements Azure Site Recovery et permettre aux clients de gérer leurs propres besoins de récupération d’urgence en libre-service. Dans les deux cas, le partenaire est le lien entre Azure Site Recovery et les clients finaux, et le partenaire propose des services et facture aux clients son utilisation d’Azure Site Recovery.

## <a name="creating-and-managing-tenant-accounts"></a>Création et gestion des comptes locataires

### <a name="step-0-prerequisite-check"></a>Étape 0 : Vérifier les prérequis

Les prérequis de la machine virtuelle sont les mêmes que ceux décrits dans la [documentation](site-recovery-vmware-to-azure.md) d’Azure Site Recovery. Outre ces prérequis, les contrôles d’accès ci-dessus doivent être appliqués avant de procéder à la gestion des locataires via CSP. Créez un serveur d’administration distinct pour chaque locataire qui peut communiquer avec les machines virtuelles du locataire et le vCenter du partenaire. Seul le partenaire dispose de droits d’accès à ce serveur.

### <a name="step-1-create-tenant-account"></a>Étape 1 : Créer un compte locataire

1.  Via une connexion du [centre de partenaires](https://partnercenter.microsoft.com/) à votre compte CSP. Dans le menu du tableau de bord à gauche, sélectionnez l’option « Clients ».

    ![tableau de bord-csp](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

2.  Sur la page qui s’affiche, cliquez sur le bouton « Ajouter un client ».

    ![ajouter-client](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

3.  Sur la page Nouveau client, renseignez toutes les informations sur le compte pour le locataire, puis cliquez sur « Suivant : Abonnements ».

    ![renseigner-informations](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

4.  Sur la page de sélection des abonnements, faites défiler pour ajouter l’abonnement « Microsoft Azure ». D’autres abonnements peuvent être ajoutés maintenant ou à tout moment ultérieurement.

    ![ajouter-abonnement](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

5.  Continuez puis, sur la page suivante, vérifiez toutes les informations entrées pour le locataire et cliquez sur le bouton Envoyer.

    ![résumé-client](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)

6.  Une fois que le client est créé, vous accédez à une page de confirmation présentant les informations du compte et du mot de passe par défaut de cet abonnement. Enregistrez les informations et modifiez le mot de passe ultérieurement via une connexion au portail Azure. Ces informations peuvent être partagées telles quelles avec le locataire ou un compte distinct peut également être créé et partagé si nécessaire.

### <a name="step-2-access-tenant-account"></a>Étape 2 : Accéder au compte locataire

1.  Vous pouvez accéder à l’abonnement du locataire à partir de la page « Clients » via votre tableau de bord comme décrit à l’étape 1. Accédez-y et cliquez sur le nom du compte locataire que vous venez de créer.
2.  La section Abonnements du compte locataire s’affiche, où vous pouvez surveiller les abonnements existants du compte et ajouter d’autres abonnements si nécessaire. Pour gérer les opérations de récupération d’urgence du locataire, sélectionnez l’option « Toutes les ressources (portail Azure) » à droite de la page.

    ![toutes-les-ressources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)

3.  Cliquez sur le bouton « Toutes les ressources » pour pouvoir accéder aux abonnements Azure du locataire. Vous pouvez également le faire en vérifiant l’AAD affiché dans le coin supérieur droit du portail Azure.

    ![administrateur-aad](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Vous pouvez maintenant effectuer toutes les opérations de récupération de site pour le locataire via le portail Azure et gérer les opérations de récupération d’urgence. La procédure détaillée ci-dessus doit être appliquée à chaque fois pour accéder à l’abonnement locataire via CSP pour la récupération d’urgence gérée.

### <a name="step-3-deploy-resources-to-tenant-subscription"></a>Étape 3 : Déployer des ressources sur l’abonnement locataire
1.  Sur le portail Azure, créez un groupe de ressources et déployez un coffre Recovery Services en suivant la procédure habituelle. Téléchargez la clé d’inscription du coffre.
2.  Inscrivez le serveur de configuration du locataire à l’aide de la clé d’inscription du coffre.
3.  Entrez les informations d’identification pour les deux comptes d’accès : compte d’accès vCenter et compte d’accès de machine virtuelle.

    ![comptes-configuration](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-recovery-services-vault"></a>Étape 4 : Inscrire l’infrastructure de récupération de site dans le coffre Recovery Services
1.  Ouvrez le portail Azure puis, sur le coffre créé précédemment, inscrivez le serveur vCenter dans le CS inscrit à l’étape précédente. Pour cela, utilisez le compte d’accès vCenter.
2.  Terminez la « Préparation de l’infrastructure » pour la récupération de site en suivant la procédure habituelle.
3.  Les machines virtuelles sont maintenant prêtes pour la réplication. Vérifiez que seules les machines virtuelles du locataire sont visibles dans le panneau de sélection de machine virtuelle sous l’option Répliquer.

    ![machines-virtuelles-locataire](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-subscription"></a>Étape 5 : Affecter un accès locataire à l’abonnement

Pour une récupération d’urgence en libre-service, les informations sur le compte (telles que mentionnées dans le point 6 de l’étape 1) doivent être fournies au locataire. Cela doit être fait une fois après que le partenaire a configuré l’infrastructure de récupération d’urgence. Quel que soit le type de récupération d’urgence (gérée ou en libre-service), le partenaire doit accéder aux abonnements du locataire via le portail CSP uniquement, configurer le coffre et inscrire l’infrastructure détenue par le partenaire dans les abonnements du locataire.

Un partenaire peut également ajouter un nouvel utilisateur à l’abonnement du locataire via le portail CSP comme suit :

1.  Accédez à la page d’abonnement CSP du locataire et sélectionnez l’option « Utilisateurs et licences ».

    ![licences-utilisateur](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Vous pouvez maintenant créer un nouvel utilisateur en entrant les informations utiles et en sélectionnant des autorisations, vous pouvez également télécharger la liste des utilisateurs via un fichier CSV.
2.  Une fois les utilisateurs créés, revenez au portail Azure puis, sous le panneau Abonnement, sélectionnez l’abonnement approprié.
3.  Sur le nouveau panneau qui s’affiche, sélectionnez Contrôle d’accès (IAM) et cliquez sur +Ajouter pour ajouter un utilisateur avec le niveau d’accès approprié. Les utilisateurs créés via le portail CSP apparaissent automatiquement dans le panneau qui s’affiche après avoir cliqué sur un niveau d’accès.

    ![abonnement-utilisateur](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Le rôle Contributeur suffit pour la plupart des opérations de gestion. Un utilisateur disposant de ce niveau d’accès peut effectuer toutes les opérations sur un abonnement, à l’exception du changement de niveau d’accès (pour cette opération, un niveau d’accès Propriétaire est nécessaire). Vous pouvez également régler les niveaux d’accès en fonction des besoins.

