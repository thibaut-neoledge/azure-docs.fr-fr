--- 
title: "Prise en main d’Azure Automation | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du service Azure Automation en examinant les principaux concepts et détails d’implémentation, dans l’optique d’intégrer l’offre à partir de la Place de marché Azure."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 8a04fda8eaf6e14a278941e7bb55b23012f67850
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017

---

## <a name="getting-started-with-azure-automation"></a>Prise en main d'Azure Automation

Ce guide de prise en main présente les concepts fondamentaux relatifs au déploiement d’Azure Automation. Si vous débutez avec Automation dans Azure ou si êtes familier avec les logiciels de workflow d’automatisation tels que System Center Orchestrator, ce guide vous décrit les concepts et les détails du déploiement. 

## <a name="key-concepts"></a>Concepts clés

### <a name="automation-service"></a>Service Automation
Automation est un service Azure qui utilise les technologies Azure et Windows PowerShell pour vous aider à contrôler la gestion de votre produit Azure, de votre cloud, de votre infrastructure locale et de vos applications.  Azure Automation vous permet de gérer le cycle de vie complet de vos services et de votre application grâce au déploiement automatisé à l’aide de l’automatisation du processus, à la configuration de système d’exploitation via la configuration de l’état souhaité PowerShell, à la surveillance et la mise à jour en continu avec la gestion des mises à jour et le suivi des modifications et à la résolution et la correction des problèmes automatisées.

### <a name="automation-account"></a>Compte Automation
Un compte Automation est une ressource Azure que vous créez.  Vous pouvez gérer toutes vos ressources Azure, cloud et locales avec un seul compte Automation.  Un compte Automation est un conteneur pour les éléments dont vous avez besoin pour que l’automatisation fonctionne : il s’agit de Runbooks, de modules, de ressources comme des informations de connexion et des plannings, et de configurations. Vous pouvez utiliser plusieurs comptes Automation pour séparer des ressources dans des environnements logiques distincts, comme le développement, le test et la production ou dans différentes régions géographiques.  

### <a name="hybrid-runbook-worker"></a>Runbook Worker hybride
Pour exécuter des runbooks sur des systèmes physiques ou virtuels dans votre centre de données local, Azure ou un autre fournisseur cloud, la fonctionnalité Runbook Worker hybride vous permet d’accéder à ces ressources locales et de les gérer.     

### <a name="automation-desired-state-configuration"></a>Configuration de l’état souhaité Automation
La configuration de l’état souhaité Automation, qui repose sur la configuration de l’état souhaité PowerShell, configure, surveille et met automatiquement à jour l’état souhaité de vos systèmes d’exploitation hébergés dans Azure, en local ou dans un autre cloud.  

### <a name="management-solutions"></a>Liste des solutions de gestion
Les solutions de gestion, telles que la gestion des mises à jour et le suivi des modifications, étendent les fonctionnalités d’Azure Automation et sont utilisées avec Log Analytics.  Elles peuvent inclure plusieurs ressources qui prennent en charge un scénario de gestion particulier, tel que les Runbooks Automation, les alertes et les requêtes de recherche Log Analytics prédéfinies et les visualisations.  

## <a name="architecture-overview"></a>Présentation de l'architecture

Azure Automation est une application SaaS qui propose un environnement mutualisé fiable et évolutif pour automatiser les processus avec des Runbooks et gérer les changements de configuration des systèmes Windows et Linux à l’aide de la configuration de l’état souhaité dans Azure, les autres services cloud ou en local. Les entités contenues dans votre compte Automation, telles que les Runbooks, les ressources et les comptes d’identification, sont isolées des autres comptes Automation de votre abonnement et d’autres abonnements.  

Les Runbooks que vous exécutez dans Azure sont exécutés dans des bacs à sable Automation, qui sont hébergés sur les machines virtuelles PaaS Azure.  Les bacs à sable Automation permettent d’isoler les clients pour tous les aspects de l’exécution du Runbook : modules, stockage, mémoire, communication réseau, flux de travail, etc. Ce rôle est géré par le service et n’est pas accessible à partir de votre compte Azure ou Azure Automation pour que vous puissiez le contrôler.         

Pour automatiser le déploiement et la gestion des ressources dans votre centre de données local ou d’autres services cloud, vous pouvez désigner une ou plusieurs machines pour exécuter le rôle Runbook Worker hybride.  Chaque Runbook Worker hybride a besoin d’un Microsoft Management Agent avec une connexion à un espace de travail Log Analytics et un compte Automation.  Log Analytics est utilisé pour démarrer l’installation, mettre à jour l’agent Microsoft Management Agent et surveiller la fonctionnalité du Runbook Worker hybride.  La remise de Runbooks et l'instruction visant à les exécuter sont effectuées par Azure Automation.

Vous pouvez déployer plusieurs Runbooks Worker hybrides pour fournir une haute disponibilité à vos runbooks, équilibrer la charge des travaux des runbooks, et dans certains cas, les allouer à des charges de travail ou environnements particuliers.  Le Runbook Worker hybride communique avec le service Automation via le port TCP sortant 443.  Une fois que le Runbook s’exécute sur un Runbook Worker hybride dans votre centre de données et si vous souhaitez que le Runbook effectue des tâches de gestion sur d’autres machines ou services dans le centre de données, le Runbook aura peut-être besoin d’accéder à d’autres ports.  Si vos stratégies de sécurité informatiques n’autorisent pas les ordinateurs de votre réseau à se connecter à Internet, consultez l’article sur la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md), qui joue le rôle de proxy pour que le Runbook Worker hybride collecte le statut du travail et reçoive les informations de configuration de votre compte Automation.

Les Runbooks s’exécutant sur un Runbook Worker hybride s’exécutent dans le contexte du compte système local sur l’ordinateur, qui est le contexte de sécurité recommandé lorsque des actions administratives sont effectuées sur la machine Windows locale. Si vous souhaitez que le Runbook exécute des tâches sur des ressources en dehors de la machine locale, vous devrez peut-être définir des ressources d’informations d’identification sécurisées dans le compte Automation auquel vous pouvez accéder à partir du Runbook et les utiliser pour l’authentification auprès de la ressource externe. Vous pouvez utiliser les ressources [Informations d’identification](automation-credentials.md), [Certificat](automation-certificates.md) et [Connexion](automation-connections.md) dans votre Runbook avec des cmdlets qui vous permettent de spécifier des informations d’identification pour que vous puissiez vous authentifier auprès de ces ressources.

Les configurations de l’état souhaité stockées dans Azure Automation peuvent être directement appliquées aux machines virtuelles Azure. Les autres machines physiques et virtuelles peuvent demander des configurations du serveur d’extraction Azure Automation DSC.  Pour la gestion des configurations de vos systèmes Windows et Linux virtuels ou physiques, vous n’avez pas besoin de déployer d’infrastructure pour prendre en charge le serveur Pull Automation DSC. Vous avez uniquement besoin de l’accès Internet sortant de chaque système à gérer par Automation DSC, communiquant sur le port TCP 443 avec le service OMS.   

![Vue d’ensemble d’Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

## <a name="prerequisites"></a>Composants requis

### <a name="automation-dsc"></a>Automation DSC
Azure Automation DSC peut servir à gérer diverses machines :

* Machines virtuelles Azure (classique) exécutant Windows ou Linux
* Machines virtuelles Azure exécutant Windows ou Linux
* Machines virtuelles Amazon Web Services (AWS) exécutant Windows ou Linux
* Ordinateurs physiques/virtuels Windows en local ou dans un cloud autre qu’Azure ou AWS
* Ordinateurs physiques/virtuels Linux en local ou dans un cloud autre qu’Azure ou AWS

La dernière version de WMF 5 doit être installée pour que l'agent PowerShell DSC pour Windows puisse communiquer avec Azure Automation. La dernière version de [l’agent PowerShell DSC pour Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) doit être installée pour que Linux puisse communiquer avec Azure Automation.

### <a name="hybrid-runbook-worker"></a>Runbook Worker hybride  
Lorsque vous configurez un ordinateur pour qu’il exécute les travaux de runbook hybride, cet ordinateur doit disposer des éléments suivants :

* Windows Server 2012 ou version ultérieure
* Windows PowerShell 4.0 ou version ultérieure.  Nous vous recommandons d’installer Windows PowerShell 5.0 sur l’ordinateur pour renforcer la fiabilité. Vous pouvez la télécharger la nouvelle version à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=50395).
* Deux cœurs minimum
* De 4 Go de RAM minimum

## <a name="security"></a>Sécurité
Azure Automation vous permet d’automatiser des tâches sur des ressources dans Azure, en local et avec d’autres fournisseurs cloud.  Pour qu’un Runbook exécute les actions requises, il doit avoir les autorisations nécessaires pour accéder en toute sécurité aux ressources avec les droits minimaux requis dans l’abonnement.  

### <a name="automation-account"></a>Compte Automation 
Toutes les tâches d’automatisation que vous effectuez sur les ressources à l’aide des cmdlets Azure dans Azure Automation s’authentifient sur Azure à l’aide de l’authentification basée sur les informations d’identification d’organisation Azure Active Directory.  Un compte Automation est séparé du compte que vous utilisez pour vous connecter au portail afin de configurer et d’utiliser des ressources Azure.  

Les ressources Automation de chaque compte Automation sont associées à une seule région Azure, mais les comptes Automation peuvent gérer toutes les ressources dans votre abonnement. Créez des comptes Automation dans différentes régions si vous avez des stratégies qui requièrent l’isolation des données et des ressources dans une région spécifique.

> [!NOTE]
> Les comptes Automation et les ressources qu’ils contiennent, créés dans le portail Azure, ne sont pas accessibles dans le portail Azure Classic. Si vous souhaitez gérer ces comptes ou leurs ressources avec Windows PowerShell, vous devez utiliser les modules Azure Resource Manager.
> 

Lorsque vous créez un compte Automation dans le portail Azure, deux entités d’authentification sont automatiquement créées :

* Compte d’identification Azure. Ce compte crée un principal de service dans Azure Active Directory (Azure AD) et un certificat. Il affecte également le rôle RBAC (contrôle d’accès en fonction du rôle) de contributeur qui gère les ressources Resource Manager à l’aide de Runbooks.
* Compte d’identification Classic. Ce compte charge un certificat de gestion, qui est utilisé pour gérer les ressources classiques à l’aide de runbooks.

Le contrôle d’accès en fonction du rôle est disponible avec Azure Resource Manager pour attribuer des actions autorisées à un compte d’utilisateur Azure AD et à un compte d’identification, et pour authentifier ce principal du service.  Pour obtenir plus d’informations susceptibles de vous aider à développer votre modèle de gestion des autorisations Automation, consultez l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Méthodes d’authentification
Le tableau suivant résume les différentes méthodes d’authentification pour chaque environnement pris en charge par Azure Automation.

| Méthode | Environnement 
| --- | --- | 
| Compte d’identification Azure et compte d’identification Classic |Déploiement Azure Resource Manager et déploiement Classic |  
| Compte d’utilisateur Azure AD |Déploiement Azure Resource Manager et déploiement Classic |  
| Authentification Windows |Centre de données local ou autre fournisseur cloud utilisant le Runbook Worker hybride |  
| Informations d’identification AWS |Amazon Web Services |  

La section **Procédures\Authentication et sécurité** comprend des articles proposant une vue d’ensemble et des étapes d’implémentation, afin de configurer l’authentification pour ces environnements avec un compte existant ou un nouveau compte dédié à l’environnement.  L’article [Update Automation Run As account using PowerShell](automation-update-account-powershell.md) (Mise à jour d’un compte d’identification Automation à l’aide de PowerShell) consacré aux comptes d’identification Azure et aux comptes d’identification Classic décrit comment mettre à jour votre compte Automation existant avec les comptes d’identification à l’aide de PowerShell si celui-ci n’était pas initialement configuré avec un compte d’identification Azure ou un compte d’identification Classic.   
 
## <a name="network"></a>Réseau
Pour que le Runbook Worker hybride se connecte à Microsoft Operations Management Suite (OMS) et soit inscrit, il doit avoir accès au numéro de port et aux URL décrits ci-dessous.  Il s’agit d’un ajout aux [ports et URL requis pour que Microsoft Monitoring Agent](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent) se connecte à OMS. Si vous utilisez un serveur proxy pour la communication entre l’agent et le service OMS, vous devez vous assurer que les ressources appropriées sont accessibles. Si vous utilisez un pare-feu pour restreindre l’accès à Internet, vous devez configurer votre pare-feu pour autoriser l’accès.

Les informations ci-dessous répertorient le port et les URL requis pour que le Runbook Worker hybride communique avec Automation.

* Port : seul TCP 443 est nécessaire pour l’accès Internet sortant
* URL globale : *.azure-automation.net

Si vous avez un compte Automation défini pour une région spécifique et que vous souhaitez limiter la communication avec ce centre de données régional, le tableau suivant fournit l’enregistrement DNS pour chaque région.

| **Région** | **Enregistrement DNS** |
| --- | --- |
| Centre-Sud des États-Unis |scus-jobruntimedata-prod-su1.azure-automation.net |
| Est des États-Unis 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centre-Ouest des États-Unis | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Europe de l'Ouest |we-jobruntimedata-prod-su1.azure-automation.net |
| Europe du Nord |ne-jobruntimedata-prod-su1.azure-automation.net |
| Centre du Canada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Asie du Sud-Est |sea-jobruntimedata-prod-su1.azure-automation.net |
| Inde centrale |cid-jobruntimedata-prod-su1.azure-automation.net |
| Est du Japon |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Sud-Est de l’Australie |ase-jobruntimedata-prod-su1.azure-automation.net |
| Sud du Royaume-Uni | uks-jobruntimedata-prod-su1.azure-automation.net |
| Gouvernement américain - Virginie | usge-jobruntimedata-prod-su1.azure-automation.us |

Pour obtenir la liste des adresses IP plutôt que celle des noms, téléchargez et consultez le fichier xml [Azure Datacenter IP address](https://www.microsoft.com/download/details.aspx?id=41653) à partir du Centre de téléchargement Microsoft. 

> [!NOTE]
> Ce fichier contient les plages d’adresses IP (y compris les plages de calcul, SQL et de stockage) utilisées dans les centres de données Microsoft Azure. Un fichier mis à jour est publié chaque semaine et reflète les plages actuellement déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne seront pas utilisées dans les centres de données avant une semaine minimum. Téléchargez le nouveau fichier xml chaque semaine et effectuez les modifications nécessaires sur votre site pour identifier correctement les services qui s’exécutent dans Azure. Les utilisateurs d’Express Route peuvent remarquer que ce fichier permet de mettre à jour la publication BGP de l’espace Azure la première semaine de chaque mois. 
> 


## <a name="implementation"></a>Implémentation

### <a name="creating-an-automation-account"></a>Création d’un compte Automation

Différentes options s’offrent à vous pour créer un compte Automation dans le portail Azure.  Le tableau suivant présente chaque type d’expérience de déploiement et leurs différences.  

|Méthode | Description |
|-------|-------------|
| Sélectionner Automation & Control dans la Place de marché | Une offre qui crée un compte Automation et un espace de travail OMS liés entre eux dans le même groupe de ressources et la même région.  Elle déploie également les solutions de gestion des mises à jour et de suivi des modifications, qui sont activées par défaut. |
| Sélectionner Automation dans la Place de marché | Crée un compte Automation dans un groupe de ressources nouveau ou existant qui n’est pas lié à un espace de travail OMS et qui n’inclut pas les solutions disponibles à partir de l’offre Automation & Control. Il s’agit d’une configuration de base qui vous présente Automation et peut vous aider à apprendre à écrire des runbooks, des configurations DSC et à utiliser les fonctionnalités du service. |
| Solutions de gestion sélectionnées | Si vous sélectionnez une solution (**[Gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md)**, **[Démarrer/arrêter les machines virtuelles durant les heures creuses](automation-solution-vm-management.md)**, ou **[Suivi des modifications](../log-analytics/log-analytics-change-tracking.md)**, vous êtes invité à choisir un compte Automation et un espace de travail OMS existant ou à les créer comme exigé pour que la solution soit déployée dans votre abonnement. |

Cette rubrique vous guide lors de la création d’un compte Automation et d’un espace de travail OMS en intégrant l’offre Automation & Control.  Pour créer un compte Automation à des fins de test ou pour avoir un aperçu du service, consultez l’article suivant [Create a standalone Azure Automation account](automation-create-standalone-account.md) (Créer un compte Azure Automation autonome).  

### <a name="create-automation-account-integrated-with-log-analytics"></a>Créer un compte Automation intégré avec Log Analytics
La méthode recommandée pour intégrer Automation est de sélectionner l’offre Automation & Control dans la Place de marché.  Cela crée un compte Automation et intègre un espace de travail OMS, y compris l’option permettant d’installer les solutions de gestion qui sont disponibles avec l’offre.  

>[!NOTE]
>Pour créer un compte Automation, vous devez posséder le rôle Administrateurs de services ou être coadministrateur de l’abonnement, ce qui permet d’accéder à l’abonnement. Vous devez également être ajouté en tant qu’utilisateur à l’instance Active Directory par défaut de cet abonnement. Il n’est pas nécessaire d’attribuer au compte un rôle doté de privilèges.
>
>Si vous n’êtes pas membre de l’instance d’Active Directory de l’abonnement avant d’être ajouté au rôle Coadministrateur de l’abonnement, vous êtes ajouté à Active Directory en tant qu’invité. Dans ce cas, vous recevez le message d’avertissement « Vous n’avez pas les autorisations pour créer... ». dans le panneau **Ajouter un compte Automation**.
>
>Les utilisateurs qui ont d’abord reçu le rôle Coadministrateur peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour en faire des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

2. Cliquez sur **Nouveau**.<br><br> ![Sélection de l’option Nouveau dans le portail Azure](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Recherchez **Automation**, puis dans les résultats de recherche, sélectionnez **Automation & Control***.<br><br> ![Recherche et sélection d’Automation & Control dans la Place de marché](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Après avoir lu la description de l’offre, cliquez sur **Créer**.  

5. Dans le panneau des paramètres **Automation & Control**, sélectionnez **Espace de travail OMS**.  Dans le panneau **Espaces de travail OMS**, sélectionnez un espace de travail OMS lié au même abonnement Azure que celui dans lequel le compte Automation se trouve ou créez un espace de travail OMS.  Si vous ne disposez pas d’espace de travail OMS, vous pouvez sélectionner **Créer un espace de travail**, puis dans le panneau **Espace de travail OMS**, procédez comme suit : 
   - Spécifiez un nom pour le nouvel **espace de travail OMS**.
   - Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   - Pour **Groupe de ressources**, vous pouvez créer un groupe de ressources ou sélectionner un groupe de ressources existant.  
   - Sélectionnez un **emplacement**.  Actuellement, les seuls emplacements disponibles sont **Sud-Est de l’Australie**, **États-Unis de l’Ouest**, **Sud-Est asiatique**, **Centre-Ouest des États-Unis** et **Europe de l’Ouest**.
   - Sélectionner un **niveau de tarification**.  Deux niveaux sont proposés pour la solution : gratuit et Par nœud (OMS).  La quantité de données collectées quotidiennement, la période de rétention et la durée d’exécution (en minutes) des tâches de runbook sont limitées pour le niveau gratuit.  Le niveau Par nœud (OMS) permet de collecter une quantité illimitée de données quotidiennement.  
   - Sélectionnez **Compte Automation**.  Si vous créez un espace de travail OMS, vous devez également créer un compte Automation associé au nouvel espace de travail OMS spécifié précédemment, y compris votre abonnement, votre groupe de ressources et votre région Azure.  Vous pouvez sélectionner **Créer un compte Automation** et dans le panneau **Compte Automation**, fournir les informations suivantes : 
  - Dans le champ **Nom**, saisissez le nom du compte Automation.

    Toutes les autres options sont renseignées automatiquement en fonction de l’espace de travail OMS sélectionné et ne peuvent pas être modifiées.  Un compte d’identification Azure est la méthode d’authentification par défaut pour l’offre.  Lorsque vous cliquez sur **OK**, les options de configuration sont validées et le compte Automation est créé.  Vous pouvez suivre la progression sous **Notifications** dans le menu. 

    Sinon, sélectionnez un compte d’identification Automation existant.  Le compte que vous sélectionnez ne peut pas déjà être lié à un autre espace de travail OMS, ou une notification s’affichera dans le panneau.  Si le compte est déjà lié, vous devez sélectionner un autre compte d’identification Automation ou en créer un.

    Après avoir renseigné les informations requises, cliquez sur **Créer**.  Les informations sont vérifiées et les comptes d’identification et Automation sont créés.  Vous êtes automatiquement redirigé vers le panneau **Espace de travail OMS**.  

6. Après avoir entré les informations requises dans le panneau **Espace de travail OMS**, cliquez sur **Créer**.  Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu.  Vous êtes redirigé vers le panneau **Ajouter une solution**.  

7. Dans le panneau des paramètres **Automation & Control**, confirmez que vous souhaitez installer les solutions recommandées présélectionnées. Si vous les désélectionnez, vous pourrez les installer séparément plus tard.  

8. Cliquez sur **Créer** pour poursuivre l’intégration d’Automation et d’un espace de travail OMS. Tous les paramètres sont validés et une tentative de déploiement de l’offre dans votre abonnement est effectuée.  Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu. 

Une fois l’offre intégrée, vous pouvez commencer à créer des runbooks, à utiliser les solutions de gestion que vous avez activées, ou commencer à utiliser [Log Analytics](https://docs.microsoft.com/azure/log-analytics) pour collecter les données générées par les ressources de vos environnements cloud ou locaux.   

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez confirmer que votre nouveau compte Automation s’authentifie auprès de ressources Azure en consultant [Test Azure Automation Run As account authentication](automation-verify-runas-authentication.md) (Test d’authentification du compte d’identification Azure Automation).
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).


