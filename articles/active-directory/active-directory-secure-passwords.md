---
title: "Sécuriser des mots de passe dans Azure AD et réinitialiser les mots de passe bloqués par le verrouillage de mot de passe intelligent | Microsoft Docs"
description: "Explique ce qu’est un client Azure AD et comment gérer Azure depuis Azure Active Directory"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Sécuriser des mots de passe dans Azure AD et réinitialiser les mots de passe bloqués par le verrouillage de mot de passe intelligent
Cet article décrit les meilleures pratiques que vous pouvez suivre en tant qu’utilisateur ou en tant qu’administrateur pour protéger vos comptes Azure Active Directory (Azure AD) et du service de comptes Microsoft. 

 >[!NOTE]
 >Les administrateurs Azure AD peuvent réinitialiser les mots de passe utilisateur en cliquant sur le nom du répertoire. Dans le [portail de gestion Azure](https://manage.windowsazure.com), sélectionnez la page Utilisateurs, cliquez sur le nom de l’utilisateur, puis sur 	Réinitialiser le mot de passe. 
 >

Azure AD intègre les approches courantes suivantes à la sécurisation des mots de passe :
 *    Exigences en termes de longueur du mot de passe
 *    Exigences en termes de « complexité » du mot de passe
 *    Expiration du mot de passe périodique et régulière 

Pour plus d’informations sur les fonctionnalités de gestion des mots de passe, consultez [Gestion des mots de passe dans Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Protection de mot de passe Azure AD
Azure AD et le système de comptes Microsoft utilisent des approches validées par le secteur pour assurer la protection sécurisée des mots de passe administrateur et utilisateur. 

Cette section explique comment Azure AD protège les mots de passe à l’aide des méthodes suivantes :
 *    Mots de passe interdits dynamiquement
 *    Verrouillage de mot de passe intelligent

Pour plus d’informations sur la gestion des mots de passe en fonction de la recherche en cours, consultez le livre blanc [Password Guidance](http://aka.ms/passwordguidance) (Conseils en matière de mots de passe). 

### <a name="dynamically-banned-passwords"></a>Mots de passe interdits dynamiquement
Azure AD et le système de comptes Microsoft assurent la protection des mots de passe en interdisant dynamiquement tous les mots de passe utilisés couramment. L’équipe Azure AD Identity Protection analyse régulièrement les listes de mots de passe interdits, empêchant ainsi les utilisateurs de choisir des mots de passe utilisés couramment. Ce service est disponible pour les clients Azure AD et du service de comptes Microsoft. 

Lorsque vous créez des mots de passe, il est important que les administrateurs encouragent les utilisateurs à choisir des phrases de mot de passe inhabituelles qui incluent une combinaison unique de lettres, de chiffres et de caractères. Cela contribue à réduire de façon considérable le risque de compromission des mots de passe utilisateur. 

**Listes de violations**

Azure AD cherche toujours à garder une longueur d’avance sur les cybercriminels. Empêcher les utilisateurs de créer des mots de passe figurant dans la liste actuelle des attaques fait partie de cette approche.

L’équipe Azure AD Identity Protection analyse en permanence les mots de passe couramment utilisés. Les cybercriminels utilisent également des stratégies similaires pour créer leurs attaques ; ils peuvent, par exemple, créer une [rainbow table](https://en.wikipedia.org/wiki/Rainbow_table) pour craquer les codes de hachage de mot de passe. 

Microsoft analyse sans cesse les [violations de données](https://www.privacyrights.org/data-breaches) pour maintenir à jour une liste des mots de passe interdits dynamiquement, qui garantit que les mots de passe vulnérables sont interdits avant de devenir une véritable menace pour les clients Azure AD. Pour plus d’informations sur nos travaux en matière de sécurité actuels, consultez le [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://www.microsoft.com/security/sir/default.aspx). 

### <a name="smart-password-lockout"></a>Verrouillage de mot de passe intelligent

Quand Azure AD détecte qu’un potentiel cybercriminel tente de pirater un mot de passe utilisateur, nous verrouillons le compte utilisateur avec le verrouillage de mot de passe intelligent. Azure AD est conçu pour déterminer les risques associés à des sessions de connexion spécifiques. 

À l’aide des données de sécurité les plus récentes, nous appliquons une sémantique de verrouillage aux cybermenaces. Ainsi, les utilisateurs ne sont pas bloqués dans le cas où un cybercriminel aurait piraté les mots de passe utilisateur sur votre réseau.

Si l’accès d’un utilisateur à son compte Azure AD est verrouillé, il verra ce qui suit :

  ![Accès verrouillé au compte Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Pour les autres comptes Microsoft, l’utilisateur verra ce qui suit :

  ![Accès verrouillé au compte Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Pour plus d’informations sur la gestion des mots de passe dans Azure Active Directory, consultez [Fonctionnement de la gestion des mots de passe dans Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works).

  >![REMARQUE] Si vous êtes administrateur Azure AD, vous souhaitez peut-être utiliser [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) pour éviter que vos utilisateurs créent des mots de passe classiques.
  >

## <a name="next-steps"></a>Étapes suivantes
[Comment mettre à jour votre mot de passe](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Principes de base de la gestion des identités Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Obtention d’informations grâce aux rapports sur la gestion des mots de passe](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



