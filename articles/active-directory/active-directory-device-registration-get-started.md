---
title: "Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine | Microsoft Docs"
description: "Configurez vos appareils Windows joints à un domaine pour les inscrire automatiquement et en mode silencieux auprès d’Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.lasthandoff: 03/10/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Prise en main du service Azure Active Directory Device Registration

Azure Active Directory Device Registration est la base des scénarios d’accès conditionnel en fonction de l’appareil. Lors de l’inscription d’un appareil, Azure Active Directory Device Registration fournit une identité à l’appareil qui sera utilisée pour authentifier cet appareil lors de la connexion de l’utilisateur. L’appareil authentifié et les attributs de l’appareil peuvent alors être utilisés pour appliquer des stratégies d’accès conditionnel pour les applications qui sont hébergées sur le cloud et localement.

Quand ils sont associés à une solution de gestion des appareils mobiles comme Microsoft Intune, les attributs de l’appareil dans Azure Active Directory sont mis à jour avec des informations supplémentaires sur l’appareil. Cela vous permet de créer des règles d’accès conditionnel qui imposent que l’accès à partir des appareils réponde à vos normes de sécurité et de conformité. Pour plus d’informations sur l’inscription d’appareils dans Microsoft Intune, consultez Inscrire des appareils pour la gestion dans Intune.
Les scénarios mis en œuvre par Azure Active Directory Device Registration incluent la prise en charge des appareils iOS, Android et Windows. Les scénarios individuels qui utilisent Azure AD Device Registration peuvent avoir une prise en charge des plateformes et des exigences plus spécifiques. 

Ces scénarios sont les suivants :

- **Accès conditionnel pour les applications Office 365 avec Microsoft Intune** : les administrateurs informatiques peuvent configurer des stratégies d’appareil d’accès conditionnel pour sécuriser les ressources d’entreprise, tout en autorisant les professionnels de l’information à accéder aux services sur les appareils conformes. Pour plus d’informations, consultez la rubrique Stratégies d’accès conditionnel basées sur les appareils pour les services Office 365.

- **Accès conditionnel aux applications hébergées en local** : vous pouvez utiliser des appareils inscrits avec des stratégies d’accès pour les applications qui sont configurées pour utiliser les services AD FS avec Windows Server 2012 R2. Pour plus d’informations sur la configuration d’un accès conditionnel en local, consultez la rubrique [Configuration d'un accès conditionnel en local à l'aide du service d'inscription d'appareils Azure Active Directory](active-directory-device-registration-on-premises-setup.md).

## <a name="setting-up-azure-active-directory-device-registration"></a>Configuration du service Azure Active Directory Device Registration

Vous avez plusieurs options pour configurer l’inscription d’appareils :

- Les appareils peuvent être inscrits lorsqu'ils sont joints au domaine Azure AD. Pour en savoir plus sur ce thème, vous pouvez trouver plus d’informations sur Azure AD Join et sur les paramètres requis pour les utilisateurs afin de joindre le domaine Azure AD.

- Les périphériques peuvent être enregistrés lorsque des utilisateurs ajoutent des comptes professionnels ou scolaires à Windows sur un appareil personnel ou lorsque des appareils mobiles se connectent à une ressource professionnelle nécessitant une inscription. Pour ce faire, vous devez activer l’inscription Azure AD Device Registration dans le portail Azure. 

- Les appareils peuvent être inscrits à l’aide d’une inscription automatique pour les ordinateurs traditionnels joints au domaine. Pour ce faire, vous devez d'abord configurer Azure AD Connect avant de poursuivre avec l’inscription automatique de l'appareil.

Pour connaître les instructions actuelles, consultez [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md).  
Vous pouvez également afficher et activer ou désactiver les appareils inscrits par le biais du portail d’administration dans Azure Active Directory.

## <a name="enable-the-azure-active-directory-device-registration-service"></a>Activer le service Azure Active Directory Device Registration

**Pour activer le service Azure Active Directory Device Registration :**

1.    Connectez-vous au portail Microsoft Azure en tant qu’administrateur.

2.    Dans le volet gauche, sélectionnez **Active Directory**.

3.    Sous l’onglet Annuaire, sélectionnez votre annuaire.

4.    Cliquez sur **Configurer**.

5.    Accédez à **Appareils**.

6.    Sélectionnez TOUS pour LES UTILISATEURS PEUVENT INSCRIRE LEURS APPAREILS SUR AZURE AD.

7.    Sélectionnez le nombre maximal d’appareils que vous souhaitez autoriser par utilisateur.

L’inscription auprès de Microsoft Intune ou de Mobile Device Management pour Office 365 nécessite l’enregistrement de l'appareil. Si vous avez configuré l’un de ces services, **TOUS** est sélectionné et **AUCUN** est désactivé. Assurez-vous qu’ils sont configurés correctement et disposent des licences appropriées.

Par défaut, l’authentification à deux facteurs n’est pas activée pour le service. Elle est toutefois recommandée lors de l’inscription d’un appareil.

- Avant de demander une authentification à deux facteurs pour ce service, vous devez configurer un fournisseur d’authentification à deux facteurs dans Azure Active Directory et configurer vos comptes d’utilisateur pour Multi-Factor Authentication. Consultez la page Ajout de Multi-Factor Authentication à Azure Active Directory

- Si vous utilisez les services AD FS avec Windows Server 2012 R2, vous devez configurer un module d’authentification à deux facteurs dans ces services. Pour plus d’informations, consultez la page Utilisation de Multi-Factor Authentication avec les services AD FS (Active Directory Federation Services).

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Afficher et gérer des objets appareil dans Azure Active Directory

Dans le portail d’administration Azure, vous pouvez afficher, bloquer et débloquer des appareils. Un appareil bloqué n’aura plus accès aux applications qui sont configurées de manière à n’autoriser que les appareils inscrits.

**Pour afficher et gérer des objets appareil dans Azure Active Directory :**
 
1.    Connectez-vous au portail Microsoft Azure en tant qu’administrateur.

2.    Dans le volet gauche, sélectionnez **Active Directory**.

3.    Sélectionnez votre annuaire.

4.    Sélectionnez **Utilisateurs**. 

5.  Cliquez sur l’utilisateur qui doit être autorisé à afficher les appareils.

6.    Sélectionnez **Appareils**.

7.    Sélectionnez **Appareils inscrits**.

Maintenant, vous pouvez réviser, bloquer ou débloquer les appareils inscrits des utilisateurs.
Les appareils Windows 10 qui sont joints localement au domaine et enregistrés automatiquement n’apparaissent pas sous l’onglet Utilisateurs. Utilisez la commande Get-MsolDevice PowerShell pour rechercher tous les appareils de votre entreprise. 


## <a name="next-steps"></a>Étapes suivantes

Pour configurer l'enregistrement automatique des appareils, consultez la rubrique [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md).



