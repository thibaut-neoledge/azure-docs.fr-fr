---

title: "Autres scénarios de licence basée sur le groupe Azure Active Directory | Microsoft Docs"
description: "Plus de scénarios pour la licence basée sur le groupe Azure Active Directory"
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dad9a176f5f60b9165c5a55628929460047e5fdd
ms.openlocfilehash: a1510240350d88ee140acb11b3f86fd7985b9427
ms.lasthandoff: 02/22/2017


---

# <a name="azure-active-directory-group-based-licensing-additional-scenarios"></a>Autres scénarios de licence basée sur le groupe Azure Active Directory

## <a name="group-based-licensing-using-dynamic-groups"></a>Licence basée sur le groupe à l’aide de groupes dynamiques

La licence basée sur le groupe peut être utilisée avec n’importe quel groupe de sécurité, ce qui signifie qu’elle peut être combinée avec des groupes dynamiques Azure AD. Les groupes dynamiques exécutent des règles sur les attributs d’objet utilisateur pour ajouter et supprimer des utilisateurs de groupes automatiquement.

Par exemple, vous pouvez créer plusieurs groupes dynamiques, un pour chaque ensemble de produits que vous souhaitez attribuer aux utilisateurs. Chaque groupe contient une règle examinant un attribut spécifique sur un utilisateur, décrivant l’ensemble de licences qu’il doit recevoir. L’attribut peut être affecté en local et synchronisé avec Azure AD, ou géré directement dans le cloud.

Lorsque l’attribut est spécifié, l’utilisateur est ajouté à un ou plusieurs de ces groupes de licences dynamiques. Les licences seront alors affectées à l’utilisateur peu de temps après. Lorsque l’attribut est supprimé, l’utilisateur quitte les groupes et les licences sont supprimées.

### <a name="example"></a>Exemple

Dans cet exemple, je dispose d’une solution de gestion des identités locale qui détermine quels utilisateurs doivent pouvoir accéder à quels Microsoft Online Services. Elle utilise extensionAttribute1 pour stocker une valeur de chaîne représentant les licences que l’utilisateur doit avoir et Azure AD Connect la synchronise avec Azure AD. Je souhaite distribuer des licences Office 365 E5 et EMS à mes utilisateurs. Dans Azure AD, j’ai créé deux groupes dynamiques, un pour chaque produit, car certains utilisateurs peuvent avoir besoin d’un des produits, mais pas de l’autre. J’ai spécifié les paramètres de règle d’appartenance et de licence dynamiques sur chaque groupe. Voici à quoi ils ressemblent :

#### <a name="o365-e5--base-services"></a>O365 E5 - Services de base

![Services de base O365 E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5 – Utilisateurs sous licence

![Utilisateurs sous licence O365 E5](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

En local, j’ai modifié un utilisateur et défini son extensionAttribute1 sur la valeur de `EMS;E5_baseservices;`, car je souhaite qu’il dispose des deux licences. Une fois la modification synchronisée avec le cloud, l’utilisateur a été ajouté automatiquement aux deux groupes et les licences ont été affectées.

![Définir extensionAttribute1 de l’utilisateur](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>Modification d’une règle d’appartenance au groupe dynamique

Faites preuve de vigilance lorsque vous modifiez la règle d’appartenance au groupe existante. Lorsqu’une règle est modifiée, tous les utilisateurs sont supprimés du groupe, la règle est évaluée, et les utilisateurs sont ajoutés au groupe sur la base des nouvelles conditions.

Si des licences sont affectées au groupe, elles seront supprimées pour tous les utilisateurs pendant le processus. Les nouvelles licences ne seront appliquées qu’une fois la nouvelle règle évaluée et les utilisateurs rajoutés. Cela signifie que les utilisateurs peuvent rencontrer une perte de service voire, dans certains cas, une perte de données.

Nous vous recommandons de ne pas modifier la règle d’appartenance à un groupe utilisé pour l’attribution de licence. Au lieu de cela, créez un nouveau groupe avec la nouvelle règle et spécifiez les mêmes paramètres de licence que sur le groupe d’origine. Attendez que les membres aient été ajoutés et que les licences aient été appliquées à tous les utilisateurs. Vous pourrez alors continuer et supprimer le groupe d’origine. Cette approche permet de garantir une transition intermédiaire sans échec vers la nouvelle règle d’appartenance sans perte d’accès ou de données pour les utilisateurs.


## <a name="multiple-groups-and-multiple-licenses"></a>Plusieurs groupes et plusieurs licences

Un utilisateur peut être membre de plusieurs groupes avec des licences. Voici quelques possibilités d’opérations à prendre en considération :

1. Plusieurs licences pour le même produit peuvent se chevaucher et entraîner l’application de tous les services activés à l’utilisateur. Par exemple, nous avons créé deux groupes de licences : *E3 - Services de base* contient les services de base que nous souhaitons déployer en premier, pour tous les utilisateurs ; *E3 – Services étendus* contient des services supplémentaires (Sway et Planner) que nous allons essayer avec certains utilisateurs, alors que Yammer reste désactivé pour un déploiement ultérieur. Dans cet exemple, l’utilisateur a été ajouté aux deux groupes :

  ![afficher les services activés](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  7 des 12 services du produit sont ainsi activés lors de l’utilisation d’une seule licence pour ce produit.

2. Sélectionnez la licence *E3* pour afficher plus de détails, notamment les informations relatives aux groupes ayant entraîné l’activation de services pour l’utilisateur.

  ![afficher les services activés par groupe](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>Coexistence de licences directes avec des licences de groupe

Lorsqu’un utilisateur hérite d’une licence d’un groupe, il n’est pas possible de supprimer ou de modifier directement cette attribution de licence sur l’objet utilisateur. Au lieu de cela, toutes les modifications doivent être apportées dans le groupe puis propagées par le système sur tous les utilisateurs.

Il est toutefois possible d’affecter la même licence de référence directement à l’utilisateur en plus de la licence héritée. Ceci peut être utilisé, par exemple, pour activer des services supplémentaires à partir de la référence pour un utilisateur, sans affecter d’autres utilisateurs.

Les licences affectées directement peuvent être supprimées, mais ceci n’affectera pas la licence héritée. Prenons l’utilisateur suivant qui hérite d’une licence *Office 365 Enterprise E3* d’un groupe, qui active un certain nombre de services.

1. Initialement, l’utilisateur hérite de la licence du groupe *E3 - Services de base* uniquement. Ceci active les 4 plans de services indiqués ci-dessous :

  ![services activés du groupe E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. En cliquant sur le bouton **Attribuer**, nous pouvons affecter directement une licence E3 à l’utilisateur. Dans ce cas, nous allons désactiver tous les plans de services, sauf Yammer Enterprise.

  ![affecter une licence à un utilisateur](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. L’utilisateur continue donc à utiliser 1 licence seulement du produit E3. L’affectation directe active toutefois le service *Yammer Enterprise* pour cet utilisateur uniquement. Dans la liste, vous pouvez voir les services activés par l’appartenance au groupe et l’affectation directe :

  ![affectation héritée et directe](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Les opérations suivantes sont autorisées avec l’affectation directe :

  a. *Yammer Enterprise* peut être désactivé sur l’objet utilisateur directement. Notez que le bouton Activé/Désactivé est activé par opposition aux autres bascules de service. Ceci est dû au fait que ce service est activé directement sur l’utilisateur et peut donc être modifié.

  b. Des services supplémentaires peuvent également être activés dans le cadre de la licence affectée directement.

  c. Le bouton **Supprimer** peut être utilisé pour supprimer la licence directe de l’utilisateur. Vous pouvez constater que l’utilisateur ne dispose maintenant que de la licence de groupe héritée et que seuls les services d’origine restent activés.

    ![supprimer une affectation directe](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Emplacement d’utilisation

Certains services Microsoft ne sont pas disponibles partout ; avant d’affecter une licence à un utilisateur, l’administrateur doit spécifier la propriété « Emplacement d’utilisation » pour l’utilisateur. Pour ce faire, il doit se rendre à la section Utilisateur &gt;Profil&gt; Paramètres dans le [portail Azure](https://portal.azure.com).

Lorsque vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié hériteront de l’emplacement du répertoire. Si vos utilisateurs se trouvent à différents emplacements, assurez-vous de bien prendre en compte cette situation dans vos objets utilisateur avant d’ajouter des utilisateurs à des groupes avec licences.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Utiliser PowerShell pour voir qui dispose de licences héritées et directes

Dans la version préliminaire publique, PowerShell ne peut pas être utilisé pour contrôler entièrement les affectations de licence de groupe. Il peut toutefois être utilisé pour consulter des informations de base sur l’état de l’utilisateur et si des licences sont héritées d’un groupe ou affectées directement. L’exemple de code ci-dessous montre comment un administrateur peut produire un rapport de base sur les affectations de licences.

1. Exécutez l’applet de commande `connect-msolservice` pour vous authentifier et vous connecter à votre locataire.

2. `Get-MsolAccountSku` peut être utilisé pour connaître toutes les licences de référence configurées dans le locataire.

  ![utilisation de l’applet de commande Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Dans cet exemple, nous souhaitons savoir à quels utilisateurs la licence *EMS* est affectée directement, à partir d’un groupe, ou les deux. Nous utiliserons un script PowerShell qui contient deux fonctions pouvant répondre à cette question pour un objet utilisateur et une référence
  ```
  \# Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)

      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if (\$license.GroupsAssigningLicense.Count -eq 0)
              {
                  return \$true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  if (\$assignmentSource -ieq \$user.ObjectId)
                  {
                      return \$true
                  }

              }
              return \$false
          }
      }
      return \$false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)
      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if (\$assignmentSource -ine \$user.ObjectId)
                  {
                      return \$true
                  }
              }
              return \$false
          }
      }
      return \$false
  }
  ```
4. Le reste du script permet d’obtenir tous les utilisateurs, d’exécuter ces fonctions sur chaque utilisateur, puis de mettre en forme la sortie dans une table.

  ```
  \# the license SKU we are interested in
  \$skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {\$\_.isLicensed -eq \$true -and \$\_.Licenses.AccountSKUID -eq \$skuId} | select \`
      ObjectId, \`
      @{Name="SkuId";Expression={\$skuId}}, \`
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly \$\_ \$skuId)}}, \`
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup \$\_ \$skuId)}}
  ```

5. La sortie du script terminé s’affiche comme suit :

  ![sortie de script PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

1. La licence basée sur le groupe ne prend actuellement pas en charge les « groupes imbriqués » (groupes contenant d’autres groupes). Si vous appliquez une licence à un groupe imbriqué, les licences ne seront appliquées qu’aux membres utilisateurs de premier niveau immédiat du groupe.

2. La licence basée sur le groupe est actuellement exposée via le [portail Azure](https://portal.azure.com) uniquement. Pour le moment, il n’est pas possible d’utiliser PowerShell pour définir ou modifier des licences sur des groupes.

3. Le [portail d’administration Office 365](https://portal.office.com ) ne prend actuellement pas en charge la licence basée sur le groupe. Si un utilisateur hérite d’une licence d’un groupe, cette licence apparaît dans le portail d’administration Office comme une licence utilisateur normale. Si vous essayez de modifier cette licence (par exemple, pour désactiver un service dans la licence, ou essayez de supprimer la licence), le portail renvoie un message d’erreur (car les licences de groupe héritées ne peuvent pas être modifiées directement sur un utilisateur).

  `To assign a license that contains Azure Information Protection Plan 1, you must also assign one of the following service plans: Azure Rights Management.`

4. Lorsqu’un utilisateur est supprimé d’un groupe et perd la licence, les plans de services provenant de cette licence (par exemple, Exchange Online ou SharePoint Online) sont définis sur un état « suspendu » par opposition à un état désactivé final. Ceci est fait par précaution afin d’éviter une suppression accidentelle de données utilisateur si un administrateur commet une erreur dans la gestion de l’appartenance au groupe.

  Nous allons implémenter un flux de travail dans lequel l’état de ces plans de services sera complètement désactivé pour ces utilisateurs. Tant qu’ils sont disponibles, certains services peuvent continuer à fonctionner pour les utilisateurs qui ont été supprimés d’un groupe et qui ne disposent plus d’une licence.

5. Lorsque des licences sont affectées ou modifiées sur un très grand groupe d’utilisateurs (par exemple, 100 000 utilisateurs), le grand nombre de modifications générées par Azure AD Automation peut affecter les performances de synchronisation de votre annuaire entre Azure AD et les systèmes locaux. Cela peut entraîner des retards de synchronisation d’annuaire dans votre environnement.

6. L’automatisation de la gestion des licences ne réagit pas automatiquement à tous les types de modifications dans l’environnement. Par exemple, vous avez pouvez manquer de licences et des utilisateurs peuvent être à l’état d’erreur « Nombre insuffisant de licences ». Vous pouvez ensuite supprimer des licences affectées directement d’autres utilisateurs afin de libérer le nombre de sièges disponibles. Le système ne réagira toutefois pas automatiquement à cette modification et ne corrigera pas les utilisateurs dans cet état d’erreur.

  Pour résoudre ces types de limitations, vous pouvez accéder au panneau du groupe dans Azure AD et cliquer sur le bouton **Retraiter**. Tous les utilisateurs de ce groupe seront traités et, si cela est possible, les états d’erreur seront résolus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais de la licence basée sur le groupe, consultez :

* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Affectation de licences à un groupe dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers une licence basée sur le groupe dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

