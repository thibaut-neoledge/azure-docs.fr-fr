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
ms.date: 05/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: d4c509c43ff647f3d9b96ffd136073312a81049b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---

# <a name="scenarios-limitations-and-known-issues-with-using-groups-to-manage-licensing-in-azure-active-directory"></a>Scénarios, limitations et problèmes connus liés à l’utilisation de groupes dans le cadre de la gestion des licences dans Azure Active Directory

Utilisez les informations et exemples suivants pour approfondir vos connaissances sur la gestion des licences par groupe Azure Active Directory (Azure AD).

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Utilisation de la gestion des licences par groupe avec des groupes dynamiques

Vous pouvez utiliser la gestion des licences par groupe avec n’importe quel groupe de sécurité, ce qui signifie que vous pouvez la combiner avec des groupes dynamiques Azure AD. Les groupes dynamiques exécutent des règles sur les attributs d’objet utilisateur pour ajouter et supprimer des utilisateurs de groupes automatiquement.

Par exemple, vous pouvez créer plusieurs groupes dynamiques, un pour chaque ensemble de produits que vous souhaitez attribuer aux utilisateurs. Chaque groupe contient une règle examinant un attribut spécifique sur un utilisateur, décrivant l’ensemble de licences qu’il doit recevoir. Vous pouvez affecter l’attribut en local puis le synchroniser avec Azure AD, ou bien vous pouvez gérer l’attribut directement dans le cloud.

Lorsque vous spécifiez l’attribut, l’utilisateur est ajouté à un ou plusieurs de ces groupes de licences dynamiques. Les licences sont affectées à l’utilisateur peu de temps après. Lorsque l’attribut est supprimé, l’utilisateur quitte les groupes et les licences sont supprimées.

### <a name="example"></a>Exemple

Supposons que vous disposez d’une solution de gestion des identités locale qui détermine quels utilisateurs doivent pouvoir accéder à quels services web Microsoft. Elle utilise **extensionAttribute1** pour stocker une valeur de chaîne représentant les licences que l’utilisateur doit avoir. Azure AD Connect la synchronise avec Azure AD.

Dans cet exemple, l’objectif est de distribuer des licences Office 365 Entreprise E5 et Enterprise Mobility + Security aux utilisateurs. Dans Azure AD, créez deux groupes dynamiques, un pour chaque produit. Certains utilisateurs peuvent en effet avoir besoin de l’un des produits, mais pas des deux. Ensuite, spécifiez les paramètres de règle d’appartenance et de licence dynamiques sur chaque groupe. Voici à quoi ils ressemblent :

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5 : services de base

![Capture d’écran des services de base Office 365 Enterprise E5](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security : utilisateurs sous licence

![Capture d’écran des utilisateurs sous licence Enterprise Mobility + Security](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

Dans cet exemple, modifiez un utilisateur et définissez extensionAttribute1 sur la valeur de `EMS;E5_baseservices;`. Vous procédez ainsi pour que cet utilisateur dispose des deux licences. Notez que vous pouvez effectuer cette modification en local. Une fois la modification synchronisée avec le cloud, l’utilisateur est ajouté automatiquement aux deux groupes et les licences sont affectées.

![Capture d’écran montrant comment définir extensionAttribute1 de l’utilisateur](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>Modification d’une règle d’appartenance au groupe dynamique

Faites preuve de vigilance lorsque vous modifiez la règle d’appartenance au groupe existante. Lorsqu’une règle est modifiée, tous les utilisateurs sont supprimés du groupe. La règle est évaluée, puis les utilisateurs sont ajoutés au groupe en fonction des nouvelles conditions.

Si des licences sont affectées au groupe, elles sont supprimées pour tous les utilisateurs pendant le processus. Les nouvelles licences ne sont appliquées qu’une fois la nouvelle règle évaluée et les utilisateurs rajoutés. Cela signifie que les utilisateurs peuvent rencontrer une perte de service voire, dans certains cas, une perte de données.

Il est préférable de ne pas modifier la règle d’appartenance à un groupe utilisé pour l’attribution de licence. Au lieu de cela, créez un nouveau groupe avec la nouvelle règle et spécifiez les mêmes paramètres de licence que sur le groupe d’origine. Attendez que les membres aient été ajoutés et que les licences aient été appliquées à tous les utilisateurs. Vous pouvez ensuite supprimer le groupe d’origine. Cette approche permet de garantir une transition intermédiaire sans échec vers la nouvelle règle d’appartenance sans perte d’accès ou de données pour les utilisateurs.


## <a name="multiple-groups-and-multiple-licenses"></a>Plusieurs groupes et plusieurs licences

Un utilisateur peut être membre de plusieurs groupes avec des licences. Voici quelques possibilités d’opérations à prendre en considération :

- Plusieurs licences pour le même produit peuvent se chevaucher et entraîner l’application de tous les services activés à l’utilisateur. L’exemple suivant montre deux groupes de licences : *E3 - Services de base* contient les services de base à déployer en premier, pour tous les utilisateurs. *E3 - Services étendus* contient les services supplémentaires (Sway et Planner) à déployer uniquement pour certains utilisateurs. Notez que Yammer reste désactivé pour un déploiement ultérieur. Dans cet exemple, l’utilisateur a été ajouté aux deux groupes :

  ![Capture d’écran des services activés](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  7 des 12 services du produit sont donc activés pour l’utilisateur alors qu’il n’utilise qu’une seule licence pour ce produit.

- Sélectionnez la licence *E3* pour afficher plus de détails, notamment les informations relatives aux groupes ayant entraîné l’activation de services pour l’utilisateur.

  ![Capture d’écran des services activés par groupe](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>Coexistence de licences directes avec des licences de groupe

Lorsqu’un utilisateur hérite d’une licence d’un groupe, il n’est pas possible de supprimer ou de modifier directement cette attribution de licence sur l’objet utilisateur. Au lieu de cela, toutes les modifications doivent être apportées dans le groupe puis propagées par le système sur tous les utilisateurs.

Il est toutefois possible d’affecter la même licence de produit directement à l’utilisateur en plus de la licence héritée. Ceci peut être utilisé, par exemple, pour activer des services supplémentaires à partir du produit pour un utilisateur, sans affecter d’autres utilisateurs.

Les licences affectées directement peuvent être supprimées, mais ceci n’affectera pas la licence héritée. Prenons l’utilisateur suivant qui hérite d’une licence Office 365 Enterprise E3 d’un groupe.

1. Initialement, l’utilisateur hérite de la licence du groupe *E3 - Services de base* uniquement. Cela active 4 plans de service, comme illustré dans l’image suivante.

  ![Capture d’écran des services activés par groupe E3](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. En cliquant sur **Affecter**, vous pouvez affecter directement une licence E3 à l’utilisateur. Dans ce cas, vous désactivez tous les plans de service, à l’exception de Yammer Enterprise.

  ![Capture d’écran montrant comment affecter une licence directement à un utilisateur](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. Par conséquent, l’utilisateur n’utilise toujours qu’une seule licence du produit E3. L’affectation directe active le service Yammer Enterprise pour cet utilisateur uniquement. L’image suivante montre quels services sont activés par l’appartenance au groupe et l’affectation directe.

  ![Capture d’écran de l’affectation héritée et directe](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. Lorsque vous utilisez une affectation directe, les opérations suivantes sont autorisées :

  a. Yammer Enterprise peut être désactivé sur l’objet utilisateur directement. Notez que le bouton **Activé/Désactivé** est activé pour ce service, ce qui n’est pas le cas des autres services. Ceci est dû au fait que ce service est activé directement sur l’utilisateur et peut donc être modifié.

  b. Des services supplémentaires peuvent également être activés dans le cadre de la licence affectée directement.

  c. Le bouton **Supprimer** peut être utilisé pour supprimer la licence directe de l’utilisateur. Vous pouvez constater que l’utilisateur ne dispose maintenant que de la licence de groupe héritée et que seuls les services d’origine restent activés.

    ![Capture d’écran montrant comment supprimer une affectation directe](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>Emplacement d’utilisation

Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété **Emplacement d’utilisation** sur l’utilisateur. Pour ce faire, il doit accéder au [portail Azure](https://portal.azure.com) et se rendre à la section **Utilisateur** &gt; **Profil** &gt; **Paramètres**.

Pour l’affectation d’une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire. Si vos utilisateurs se trouvent à différents emplacements, assurez-vous de bien prendre en compte cette situation dans vos objets utilisateur avant d’ajouter des utilisateurs à des groupes avec licences.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Utiliser PowerShell pour voir qui dispose de licences héritées et directes
Si la gestion des licences est effectuée dans la préversion publique, PowerShell ne peut pas être utilisé pour contrôler entièrement les affectations de licence de groupe. Il peut toutefois être utilisé pour consulter des informations de base sur l’état de l’utilisateur et déterminer si des licences sont héritées d’un groupe ou affectées directement. L’exemple de code suivant montre comment un administrateur peut produire un rapport de base sur les affectations de licences.

1. Exécutez l’applet de commande `connect-msolservice` pour vous authentifier et vous connecter à votre locataire.

2. `Get-MsolAccountSku` peut être utilisé pour connaître toutes les licences de produit configurées dans le locataire.

  ![Capture d’écran de l’applet de commande Get-Msolaccountsku](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. Dans cet exemple, vous souhaitez savoir à quels utilisateurs la licence Enterprise Mobility + Security est affectée directement, à partir d’un groupe, ou les deux. Vous pouvez utiliser le script PowerShell suivant.
  
  ```
  #Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          #we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              #This could be a group object or a user object (contrary to what the name suggests)
              #If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              #If the collection contains the ID of the user object, this means the license is assigned directly
              #Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  #Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses)
     {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  #Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. Le reste du script permet d’obtenir tous les utilisateurs et d’exécuter ces fonctions sur chacun d’eux. Ensuite il met en forme la sortie dans une table.
    
  ```
  #the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  #find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. La sortie du script terminé s’affiche comme suit :

  ![Capture d’écran de la sortie de script PowerShell](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

Si vous utilisez la gestion des licences par groupe, il est conseillé de vous familiariser avec la liste suivante des limitations et problèmes connus.

- La licence basée sur le groupe ne prend actuellement pas en charge les « groupes imbriqués » (groupes contenant d’autres groupes). Si vous appliquez une licence à un groupe imbriqué, les licences ne sont appliquées qu’aux membres utilisateurs de premier niveau immédiat du groupe.

- Une ou plusieurs licences n’ont pas pu être modifiées, car elles ont hérité d’une appartenance à un groupe. Pour afficher ou modifier des licences basées sur le groupe, visitez le portail d’administration d’Azure.

- Le [portail d’administration Office 365](https://portal.office.com ) ne prend actuellement pas en charge la licence basée sur le groupe. Si un utilisateur hérite d’une licence d’un groupe, cette licence apparaît dans le portail d’administration Office comme une licence utilisateur normale. Si vous essayez de modifier cette licence (par exemple, pour désactiver un service dans la licence, ou essayez de supprimer la licence), le portail renvoie un message d’erreur. Les licences de groupe héritées ne peuvent pas être modifiées directement sur un utilisateur.

- Lorsqu’un utilisateur est supprimé d’un groupe et perd la licence, les plans de services provenant de cette licence (par exemple, Exchange Online ou SharePoint Online) sont définis sur un état « suspendu ». Les plans de service ne sont pas définis sur un état final « désactivé ». Cette précaution permet d’éviter une suppression accidentelle de données utilisateur si un administrateur commet une erreur dans la gestion de l’appartenance au groupe.

- Lorsque les licences sont affectées ou modifiées pour un très grand groupe (par exemple, 100 000 utilisateurs), cela peut affecter les performances. Plus précisément, le volume de modifications générées par Azure AD Automation peut affecter les performances de synchronisation de votre annuaire entre Azure AD et les systèmes locaux. Cela peut entraîner des retards de synchronisation d’annuaire dans votre environnement.

- L’automatisation de la gestion des licences ne réagit pas automatiquement à tous les types de modifications dans l’environnement. Par exemple, il se peut que vous manquiez de licences et que certains utilisateurs se retrouvent dans un état d’erreur. Pour libérer le nombre de sièges disponibles, vous pouvez supprimer des licences affectées directement d’autres utilisateurs. Le système ne réagit toutefois pas automatiquement à cette modification et ne corrige pas les utilisateurs dans cet état d’erreur.

  Pour résoudre ces types de limitations, vous pouvez accéder au panneau **Groupe** dans Azure AD et cliquer sur **Retraiter**. Tous les utilisateurs de ce groupe sont traités et, si cela est possible, les états d’erreur sont résolus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des licences basées sur les groupes, consultez :

* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Affectation de licences à un groupe dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers une licence basée sur le groupe dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)

