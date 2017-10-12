---
title: "Sécuriser votre coffre de clés | Microsoft Docs"
description: "Gérez les autorisations d’accès à un coffre de clés pour la gestion des clés et des secrets. Modèle d’authentification et d’autorisation d’un coffre de clés et procédure de sécurisation de votre coffre de clés"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: b81791f0bce7e6f57782dfe7bc5fb5fc21369e7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="secure-your-key-vault"></a>Sécuriser votre coffre de clés
Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement (tels que les certificats, les chaînes de connexion et les mots de passe) de vos applications cloud. Comme il s’agit de données sensibles et stratégiques, il est préférable de sécuriser l’accès à vos coffres de clés afin que seuls les applications et les utilisateurs autorisés puissent accéder à ces derniers. Cet article propose une vue d’ensemble du modèle d’accès à un coffre de clés, explique l’authentification et l’autorisation, et détaille la procédure de sécurisation de l’accès au coffre de clés pour vos applications cloud à l’aide d’un exemple.

## <a name="overview"></a>Vue d'ensemble
L’accès à un coffre de clés est contrôlé via deux interfaces distinctes : le plan de gestion et le plan de données. Pour ces deux plans, une autorisation et une authentification correctes sont nécessaires pour qu’un appelant (un utilisateur ou une application) puisse accéder au coffre de clés. L’authentification établit l’identité de l’appelant, tandis que l’autorisation détermine les opérations que ce dernier est autorisé à effectuer.

Pour l’authentification, le plan de gestion et le plan de données font tous deux appel à Azure Active Directory. Pour l’autorisation, le plan de gestion utilise le contrôle d’accès en fonction du rôle (RBAC), tandis que le plan de données s’appuie sur la stratégie d’accès au coffre de clés.

Voici un aperçu des sujets traités :

[Authentification à l’aide d’Azure Active Directory](#authentication-using-azure-active-directory) : cette section explique comment un appelant s’authentifie auprès d’Azure Active Directory pour accéder à un coffre de clés via le plan de gestion et le plan de données. 

[Plan de gestion et plan de données](#management-plane-and-data-plane) : le plan de gestion et le plan de données sont deux plans d’accès utilisés pour accéder à votre coffre de clés. Chaque plan d’accès prend en charge des opérations spécifiques. Cette section décrit les points de terminaison d’accès, les opérations prises en charge et la méthode de contrôle d’accès utilisée par chaque plan. 

[Contrôle d’accès au plan de gestion](#management-plane-access-control) : dans cette section, nous examinons comment autoriser l’accès aux opérations du plan de gestion à l’aide du contrôle d’accès en fonction du rôle.

[Contrôle d’accès au plan de données](#data-plane-access-control) : cette section explique comment utiliser la stratégie d’accès au coffre de clés pour contrôler l’accès au plan de données.

[Exemple](#example) : cet exemple explique comment configurer le contrôle d’accès pour votre coffre de clés afin d’autoriser trois équipes différentes (l’équipe de sécurité, les développeurs/opérateurs et les auditeurs) à effectuer des tâches spécifiques pour développer, gérer et surveiller une application dans Azure.

## <a name="authentication-using-azure-active-directory"></a>Authentification à l’aide d’Azure Active Directory
Lorsque vous créez un coffre de clés dans un abonnement Azure, il est automatiquement associé au client Azure Active Directory de cet abonnement. Tous les appelants (utilisateurs et applications) doivent être enregistrés dans ce client pour accéder à ce coffre de clés. Une application ou un utilisateur doit s’authentifier auprès d’Azure Active Directory pour accéder au coffre de clés. Cela concerne l’accès au plan de gestion et au plan de données. Dans les deux cas, une application peut accéder au coffre de clés de deux manières :

* **Accès de l’utilisateur et de l’application** : concerne généralement les applications qui accèdent au coffre de clés pour le compte d’un utilisateur connecté. Azure PowerShell et le portail Azure sont des exemples de ce type d’accès. Il existe deux façons d’accorder l’accès à des utilisateurs : la première consiste à accorder l’accès à des utilisateurs de sorte qu’ils puissent accéder au coffre de clés depuis n’importe quelle application et la seconde consiste à accorder l’accès au coffre de clés à un utilisateur uniquement lorsque ce dernier utilise une application spécifique (ce que l’on désigne sous le nom d’identité composée). 
* **Accès de l’application seule** : pour les applications qui exécutent des services démons, des tâches en arrière-plan, etc. L’identité de l’application se voit octroyer l’accès au coffre de clés.

Dans les deux types d’applications, l’application s’authentifie auprès d’Azure Active Directory à l’aide de n’importe laquelle des [méthodes d’authentification prises en charge](../active-directory/active-directory-authentication-scenarios.md) et obtient un jeton. La méthode d’authentification utilisée dépend du type d’application. L’application utilise ce jeton, puis envoie une demande d’API REST au coffre de clés. En cas d’accès au plan de gestion, les demandes sont acheminées via le point de terminaison Azure Resource Manager. Si elle accède au plan de données, l’application s’adresse directement à un point de terminaison de coffre de clés. Consultez d’autres informations sur le [flux d’authentification intégral](../active-directory/active-directory-protocols-oauth-code.md). 

Le nom de la ressource pour laquelle l’application demande un jeton est différent selon que l’application accède au plan de gestion ou au plan de données. Par conséquent, en fonction de l’environnement Azure, le nom de la ressource correspond soit à un point de terminaison du plan de gestion, soit à un point de terminaison du plan de données, tel que spécifié dans le tableau de la section suivante.

L’utilisation d’un mécanisme unique pour l’authentification auprès des plans de gestion et de données présente des avantages spécifiques :

* Les organisations peuvent contrôler de manière centralisée l’accès à tous leurs coffres de clés.
* Si un utilisateur part, il perd instantanément l’accès à tous les coffres de clés de l’organisation.
* Les organisations peuvent personnaliser l’authentification à l’aide des options offertes par Azure Active Directory (par exemple, activer l’authentification multifacteur pour renforcer la sécurité).

## <a name="management-plane-and-data-plane"></a>Plan de gestion et plan de données
Azure Key Vault est un service Azure disponible via le modèle de déploiement Azure Resource Manager. Lorsque vous créez un coffre de clés, vous obtenez un conteneur virtuel à l’intérieur duquel vous pouvez créer d’autres objets tels que des clés, des secrets et des certificats. Ensuite, vous accédez à votre coffre de clés à l’aide du plan de gestion et du plan de données pour effectuer des opérations spécifiques. L’interface du plan de gestion permet de gérer votre coffre de clés proprement dit : création, suppression ou mise à jour d’attributs de coffre de clés, définition de stratégies d’accès pour le plan de données, etc. L’interface du plan de données permet d’ajouter, de supprimer, de modifier et d’utiliser les clés, les secrets et les certificats stockés dans votre coffre de clés.

Les interfaces du plan de gestion et du plan de données sont accessibles via des points de terminaison différents (voir tableau). La deuxième colonne du tableau indique les noms DNS de ces points de terminaison dans les différents environnements Azure. La troisième colonne décrit les opérations que vous pouvez effectuer depuis chaque plan d’accès. Chaque plan d’accès présente également son propre mécanisme de contrôle d’accès : pour le plan de gestion, le contrôle d’accès est défini à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager, tandis que pour le plan de données, il est défini à l’aide de la stratégie d’accès au coffre de clés.

| Plan d’accès | Points de terminaison d’accès | Opérations | Mécanisme de contrôle d’accès |
| --- | --- | --- | --- |
| Plan de gestion |**Mondial :**<br> management.azure.com:443<br><br> **Azure China :**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government :**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> management.microsoftazure.de:443 |Créer/lire/mettre à jour/supprimer un coffre de clés <br> Définir des stratégies d’accès pour un coffre de clés<br>Définir des balises pour un coffre de clés |Contrôle d’accès en fonction du rôle (RBAC) Azure Resource Manager |
| Plan de données |**Mondial :**<br> &lt;nom du coffre&gt;.vault.azure.net:443<br><br> **Azure China :**<br> &lt;nom du coffre&gt;.vault.azure.cn:443<br><br> **Azure US Government :**<br> &lt;nom du coffre&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany :**<br> &lt;nom du coffre&gt;.vault.microsoftazure.de:443 |Pour les clés : Decrypt, Encrypt, UnwrapKey, WrapKey, Verify, Sign, Get, List, Update, Create, Import, Delete, Backup, Restore<br><br> Pour les secrets : Get, List, Set, Delete |Stratégie d’accès au coffre de clés |

Les contrôles d’accès au plan de gestion et au plan de données fonctionnent indépendamment. Par exemple, si vous souhaitez autoriser une application à utiliser les clés d’un coffre de clés, vous avez seulement besoin de lui accorder des autorisations d’accès au plan de données à l’aide de stratégies d’accès au coffre de clés. Aucun accès au plan de gestion n’est requis pour cette application. À l’inverse, si vous souhaitez qu’un utilisateur puisse lire les propriétés et les balises d’un coffre, mais pas accéder aux clés, secrets ou certificats, vous pouvez lui accorder un accès en lecture à l’aide de la fonctionnalité RBAC. Aucun accès au plan de données n’est requis.

## <a name="management-plane-access-control"></a>Contrôle d’accès au plan de gestion
Le plan de gestion se compose des opérations qui affectent le coffre de clés proprement dit. Par exemple, vous pouvez créer ou supprimer un coffre de clés. Vous pouvez obtenir la liste des coffres d’un abonnement. Vous pouvez récupérer les propriétés d’un coffre de clés (par exemple, sa référence SKU et ses balises) et définir des stratégies d’accès qui contrôlent les utilisateurs et les applications qui peuvent accéder aux clés et aux secrets du coffre de clés. Le contrôle d’accès au plan de gestion utilise la fonctionnalité RBAC. Vous trouverez la liste complète des opérations de coffre de clés qui peuvent être effectuées via le plan de gestion dans le tableau de la section précédente. 

### <a name="role-based-access-control-rbac"></a>Contrôle d’accès en fonction du rôle (RBAC)
À chaque abonnement Azure correspond un annuaire Azure Active Directory. Les utilisateurs, les groupes et les applications de cet annuaire peuvent être autorisés à gérer les ressources de l’abonnement Azure qui reposent sur le modèle de déploiement Azure Resource Manager. Ce type de contrôle d’accès porte le nom de contrôle d’accès en fonction du rôle (RBAC). Pour gérer cet accès, vous pouvez utiliser le [Portail Azure](https://portal.azure.com/), les [outils de l’interface de ligne de commande Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Avec le modèle Azure Resource Manager, vous créez votre coffre de clés dans un groupe de ressources et contrôlez l’accès au plan de gestion de ce coffre de clés à l’aide d’Azure Active Directory. Par exemple, vous pouvez autoriser des utilisateurs ou un groupe à gérer les coffres de clés d’un groupe de ressources spécifique.

Vous pouvez accorder l’accès aux utilisateurs, groupes et applications d’une étendue donnée en affectant les rôles RBAC appropriés. Par exemple, pour autoriser un utilisateur à gérer des coffres de clés, vous devez attribuer un rôle prédéfini « Collaborateur de coffre de clés » à cet utilisateur dans une étendue spécifique. L’étendue dans ce cas correspond à un abonnement, à un groupe de ressources ou simplement à un coffre de clés spécifique. Un rôle affecté au niveau d’un abonnement s’applique à tous les groupes de ressources et ressources au sein de cet abonnement. Un rôle affecté au niveau d’un groupe de ressources s’applique à toutes les ressources de ce groupe de ressources. Un rôle affecté pour une ressource spécifique s’applique uniquement à cette ressource. Il existe plusieurs rôles prédéfinis (voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md)), et si ces rôles prédéfinis ne répondent pas à vos besoins, vous pouvez également définir vos propres rôles.

> [!IMPORTANT]
> Notez que si un utilisateur dispose d’autorisations de collaborateur (RBAC) pour le plan de gestion d’un coffre de clés, il peut s’accorder à lui-même l’accès au plan de données en définissant la stratégie d’accès au coffre de clés qui contrôle l’accès au plan de données. Par conséquent, il est recommandé de contrôler étroitement quels utilisateurs disposent d’un accès « Collaborateur » à vos coffres de clés afin de vous assurer que seules les personnes autorisées peuvent gérer vos coffres de clés, clés, secrets et certificats.
> 
> 

## <a name="data-plane-access-control"></a>Contrôle d’accès au plan de données
Le plan de données d’un coffre de clés concerne les opérations qui affectent les objets d’un coffre de clés, telles que les clés, les secrets et les certificats.  Cela inclut les opérations liées aux clés (création, importation, mise à jour, énumération, sauvegarde et restauration de clés, par exemple), les opérations de chiffrement (signature, vérification, chiffrement, déchiffrement, encapsulage, désencapsulage, etc.), ainsi que la définition de balises et d’autres attributs pour les clés. De même, pour les secrets, le plan de données comprend les opérations get, set, list et delete.

L’accès au plan de données est octroyé en définissant des stratégies d’accès pour un coffre de clés. Un utilisateur, un groupe ou une application doit posséder des autorisations de collaborateur (RBAC) pour le plan de gestion d’un coffre de clés afin de pouvoir définir des stratégies d’accès pour ce coffre de clés. Un utilisateur, un groupe ou une application peut se voir autorisée à effectuer des opérations spécifiques pour les clés ou les secrets d’un coffre de clés. Un coffre de clés prend en charge jusqu’à 16 entrées de stratégie d’accès. Créez un groupe de sécurité Azure Active Directory et ajoutez des utilisateurs à ce groupe afin d’accorder l’accès au plan de données à plusieurs utilisateurs pour un coffre de clés.

### <a name="key-vault-access-policies"></a>Stratégies d’accès à un coffre de clés
Les stratégies d’accès à un coffre de clés accordent des autorisations s’appliquant soit aux clés, soit aux secrets, soit aux certificats. Par exemple, vous pouvez donner accès aux clés à un utilisateur, mais aucune autorisation pour les secrets. Toutefois, les autorisations d’accès aux clés, aux secrets ou aux certificats concernent le niveau du coffre. En d’autres termes, la stratégie d’accès à un coffre de clés ne prend pas en charge les autorisations de niveau objet. Vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [outils de la CLI Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) ou les [API REST de gestion de coffre de clés](https://msdn.microsoft.com/library/azure/mt620024.aspx) pour définir des stratégies d’accès pour un coffre de clés.

> [!IMPORTANT]
> Notez que les stratégies d’accès à un coffre de clés s’appliquent au niveau du coffre. Par exemple, lorsqu’un utilisateur est autorisé à créer et à supprimer des clés, il peut effectuer ces opérations sur toutes les clés de ce coffre de clés.
> 
> 

## <a name="example"></a>Exemple
Supposons que vous développiez une application qui utilise un certificat pour SSL, le stockage Azure pour stocker des données et une clé RSA 2 048 bits pour les opérations de signature. Supposons que cette application s’exécute dans une machine virtuelle (ou un groupe de machines virtuelles identiques). Vous pouvez utiliser un coffre de clés pour stocker tous les secrets de l’application et utiliser le coffre de clés pour stocker le certificat Bootstrap auquel l’application fait appel pour s’authentifier auprès d’Azure Active Directory.

Voici un résumé de l’ensemble des clés et des secrets qui doivent être stockés dans un coffre de clés.

* **Certificat SSL** : utilisé pour le protocole SSL
* **Clé de stockage** : utilisée pour accéder au compte de stockage
* **Clé RSA 2 048 bits** : utilisée pour les opérations de signature
* **Certificat Bootstrap** : utilisé pour l’authentification auprès d’Azure Active Directory, pour obtenir l’accès au coffre de clés afin de récupérer la clé de stockage et d’utiliser la clé RSA pour la signature.

Découvrons à présent les personnes qui gèrent, déploient et auditent cette application. Nous utiliserons trois rôles dans cet exemple.

* **Équipe de sécurité** : il s’agit généralement du personnel informatique du « bureau du responsable de la sécurité » ou équivalent, chargé de la conservation en toute sécurité des secrets tels que les certificats SSL, les clés RSA utilisées pour la signature, les chaînes de connexion pour les bases de données et les clés de compte de stockage.
* **Développeurs/opérateurs** : il s’agit des personnes qui développent cette application, puis la déploient dans Azure. En règle générale, ils ne font pas partie de l’équipe de sécurité et ne doivent donc pas avoir accès aux données sensibles (certificats SSL, clés RSA, etc.), mais l’application qu’ils déploient doit y avoir accès.
* **Auditeurs** : il s’agit le plus souvent d’un groupe différent de personnes, isolé des développeurs et du personnel informatique général. Ils sont chargés d’examiner l’utilisation et la maintenance correctes des certificats, des clés, etc. et d’assurer la conformité aux normes en matière de sécurité des données. 

Il existe un autre rôle qui dépasse le cadre de cette application, mais qu’il est pertinent de mentionner ici, à savoir l’administrateur d’abonnement (ou de groupe de ressources). L’administrateur d’abonnement configure les autorisations d’accès initiales pour l’équipe de sécurité. Ici, nous supposons que l’administrateur d’abonnement a autorisé l’équipe de sécurité à accéder à un groupe de ressources dans lequel se trouvent toutes les ressources nécessaires pour cette application.

Voyons maintenant quelles actions sont effectuées par chaque rôle dans le contexte de cette application.

* **Équipe de sécurité**
  * Création de coffres de clés
  * Activation de la journalisation de coffre de clés
  * Ajout de clés/secrets
  * Création d’une sauvegarde des clés pour la récupération d’urgence
  * Définition d’une stratégie d’accès à un coffre de clés pour autoriser des utilisateurs et des applications à effectuer des opérations spécifiques
  * Déploiement périodique de clés/secrets
* **Développeurs/opérateurs**
  * Obtention des références aux certificats Bootstrap et SSL (empreintes), à la clé de stockage (URI de secret) et à la clé de signature (URI de clé) auprès de l’équipe de sécurité
  * Développement et déploiement d’une application qui accède aux clés et aux secrets par programmation
* **Auditeurs**
  * Examen des journaux d’utilisation pour confirmer l’utilisation correcte des clés/secrets et la conformité aux normes en matière de sécurité des données

Voyons maintenant quelles autorisations d’accès au coffre de clés sont requises par les différents rôle (et l’application) pour effectuer les tâches qui leur sont affectées. 

| Rôle d’utilisateur | Autorisations de plan de gestion | Autorisations de plan de données |
| --- | --- | --- |
| Équipe de sécurité |Collaborateur de coffre de clés |Clés : sauvegarde, création, suppression, obtention, importation, énumération, restauration <br> Secrets : toutes |
| Développeurs/opérateurs |Autorisation de déploiement pour le coffre de clés de sorte que les machines virtuelles qu’ils déploient puissent récupérer les secrets dans le coffre de clés |Aucune |
| Auditeurs |Aucune |Clés : énumération<br>Secrets : énumération |
| Application |Aucune |Clés : énumération<br>Secrets : obtention |

> [!NOTE]
> Les auditeurs ont besoins d’une autorisation d’énumération pour les clés et les secrets afin de pouvoir inspecter les attributs de clés et de secrets qui ne sont pas inclus dans les journaux, tels que les balises et les dates d’activation et d’expiration.
> 
> 

Outre l’autorisation relative au coffre de clés, les trois rôles ont aussi besoin d’un accès aux autres ressources, par exemple pour pouvoir déployer des machines virtuelles (ou des applications web, etc.). Les développeurs/opérateurs ont également besoin d’un accès « Collaborateur » à ces types de ressources. Les auditeurs ont besoin d’un accès en lecture au compte de stockage où les journaux de coffre de clés sont conservés.

Comme cet article porte sur la sécurisation de l’accès à votre coffre de clés, nous illustrons uniquement les aspects ayant trait à ce sujet et ne détaillons pas le déploiement de certificats, l’accès par programmation aux clés et aux secrets, etc., qui ont déjà été traités ailleurs. Le déploiement de certificats stockés dans un coffre de clés sur des machines virtuelles est expliqué dans un [billet de blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/), et un [exemple de code](https://www.microsoft.com/download/details.aspx?id=45343) est disponible pour illustrer l’utilisation de certificats Bootstrap pour l’authentification auprès d’Azure AD à des fins d’accès au coffre de clés.

La plupart des autorisations d’accès peuvent être octroyées à l’aide du portail Azure, mais pour accorder des autorisations granulaires, il se peut que vous deviez utiliser Azure PowerShell (ou la CLI Azure) pour produire le résultat souhaité. 

Les extraits de code PowerShell suivants supposent que :

* L’administrateur Active Directory Azure ait créé des groupes de sécurité qui représentent les trois rôles, à savoir Contoso Security Team, Contoso App Devops et Contoso App Auditors. L’administrateur a également ajouté les utilisateurs aux groupes dont ils font partie.
* **ContosoAppRG** est le groupe de ressources où toutes les ressources se trouvent. **contosologstorage** est l’emplacement de stockage des journaux. 
* Le coffre de clés **ContosoKeyVault** et le compte de stockage utilisé pour les journaux de coffre de clés **contosologstorage** doivent se trouver dans le même emplacement Azure.

Tout d’abord, l’administrateur d’abonnement attribue les rôles « Collaborateur de coffre de clés » et « Administrateur de l’accès utilisateur » à l’équipe de sécurité. L’équipe de sécurité peut ainsi gérer l’accès aux autres ressources et gérer les coffres de clés du groupe de ressources ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Le script suivant illustre la manière dont l’équipe de sécurité peut créer un coffre de clés, configurer la journalisation, et définir des autorisations d’accès pour les autres rôles et l’application. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Le rôle personnalisé défini peut uniquement être assigné à l’abonnement où le groupe de ressources ContosoAppRG a été créé. Si les mêmes rôles personnalisés seront utilisés pour d’autres projets dans d’autres abonnements, des abonnements supplémentaires peuvent être ajoutés à sa portée.

L’attribution de rôles personnalisés aux développeurs/opérateurs pour l’autorisation « déploiement/action » se limite au groupe de ressources. De cette manière, seules les machines virtuelles créées dans le groupe de ressources « ContosoAppRG » obtiennent les secrets (certificat SSL et certificat Bootstrap). Les machines virtuelles créées par l’équipe de développeurs/opérateurs ne pourront pas obtenir ces secrets, même si elles connaissent les URI de secret.

Cet exemple illustre un scénario simple. Les scénarios réels peuvent être plus complexes et vous devrez peut-être modifier les autorisations d’accès à votre coffre de clés selon vos besoins. Par exemple, dans notre exemple, nous partons du principe que l’équipe de sécurité fournira les références de clé et de secret (URI et empreintes) que l’équipe de développeurs/opérateurs doit référencer dans ses applications. Par conséquent, elle n’a pas besoin d’accorder aux développeurs/opérateurs un accès au plan de données. Notez également que cet exemple se focalise sur la sécurisation de votre coffre de clés. Une considération similaire doit être accordée pour sécuriser [vos machines virtuelles](https://azure.microsoft.com/services/virtual-machines/security/), [comptes de stockage](../storage/common/storage-security-guide.md) et autres ressources Azure.

> [!NOTE]
> Remarque : cet exemple montre comment l’accès au coffre de clés sera verrouillé en production. Les développeurs doivent posséder leur propre abonnement ou groupe de ressources avec des autorisations complètes pour gérer leurs coffres de clés, les machines virtuelles et le compte de stockage où ils développent l’application.
> 
> 

## <a name="resources"></a>les ressources
* [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)
  
  Cet article décrit le contrôle d’accès en fonction du rôle d’Active Directory Azure et en explique le fonctionnement.
* [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md)
  
  Cet article explique en détail tous les rôles intégrés disponibles dans RBAC.
* [Présentation du déploiement de Resource Manager et du déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Cet article décrit le modèle de déploiement de Resource Manager et le modèle de déploiement classique et explique les avantages liés à l’utilisation de Resource Manager et des groupes de ressources.
* [Gestion du contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  
  Cet article explique comment gérer le contrôle d’accès en fonction du rôle avec Azure PowerShell
* [Gestion du contrôle d’accès basé sur les rôles à l’aide de l’API REST](../active-directory/role-based-access-control-manage-access-rest.md)
  
  Cet article montre comment utiliser l’API REST pour gérer RBAC.
* [Role-Based Access Control for Microsoft Azure from Ignite (Contrôle d’accès en fonction du rôle pour Microsoft Azure)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Ce lien donne accès à une vidéo sur Channel 9 enregistrée à l’occasion de la conférence MS Ignite en 2015. Cette session traite des fonctionnalités de gestion d’accès et de création de rapports d’Azure et explore les bonnes pratiques en ce qui concerne la sécurisation de l’accès aux abonnements Azure à l’aide d’Azure Active Directory.
* [Autoriser l’accès aux applications web à l’aide d’OAuth 2.0 et Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)
  
  Cet article décrit le flux OAuth 2.0 complet pour l’authentification auprès d’Azure Active Directory.
* [API REST de gestion de coffre de clés](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ce document contient des informations de référence sur les API REST permettant de gérer votre coffre de clés par programmation, y compris la définition de la stratégie d’accès au coffre de clés.
* [API REST de coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Lien vers la documentation de référence des API REST de coffre de clés.
* [Contrôle d’accès aux clés](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Lien vers la documentation de référence sur le contrôle d’accès aux secrets.
* [Contrôle d’accès aux secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Lien vers la documentation de référence sur le contrôle d’accès aux clés.
* [Définition](https://msdn.microsoft.com/library/mt603625.aspx) et [suppression](https://msdn.microsoft.com/library/mt619427.aspx) de la stratégie d’accès au coffre de clés à l’aide de PowerShell
  
  Liens vers la documentation de référence des applets de commande PowerShell permettant de gérer la stratégie d’accès au coffre de clés.

## <a name="next-steps"></a>Étapes suivantes
Pour un didacticiel de prise en main destiné aux administrateurs, consultez la page [Prise en main d’Azure Key Vault](key-vault-get-started.md).

Pour plus d’informations sur l’utilisation de la journalisation du coffre de clés, consultez [Journalisation d’Azure Key Vault](key-vault-logging.md).

Pour plus d’informations sur l’utilisation des clés et des secrets avec un coffre de clés Azure, consultez la page [À propos des clés et des clés secrètes](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pour toute question concernant les coffres de clés, rendez-vous sur les [forums Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

