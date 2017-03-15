---
title: "Azure AD Connect : Mettre à jour le certificat SSL pour une batterie de serveurs Active Directory Federation Services (AD FS) | Microsoft Docs"
description: "Ce document détaille les étapes permettant de mettre à jour le certificat SSL d’une batterie de serveurs AD FS à l’aide d’Azure AD Connect."
services: active-directory
keywords: "azure ad connect, mise à jour de ssl adfs, mise à jour d’un certificat adfs, modifier un certificat adfs, nouveau certificat adfs, certificat adfs, mettre à jour un certificat ssl adf, mettre à jour un certificat adf ssl, configurer un certificat ssl adf, adfs, ssl, certificat, adfs certificat de communication de service, mettre à jour la fédération, configurer la fédération, aad connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Mettre à jour le certificat SSL pour une batterie de serveurs Active Directory Federation Services (AD FS)

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment utiliser Azure AD Connect pour mettre à jour le certificat SSL d’une batterie de serveurs Active Directory Federation Services (AD FS).  Si vous avez défini la méthode d’authentification dans Azure AD Connect sur AD FS, vous pouvez utiliser l’outil Azure AD Connect pour mettre facilement à jour le certificat SSL de la batterie de serveurs AD FS sur l’ensemble des serveurs de fédération et WAP en 3 étapes simples :

![Trois étapes](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Pour en savoir plus sur les certificats utilisés par AD FS, consultez l’article [Présentation des certificats utilisés par les services ADFS (Active Directory Federation Services)](https://technet.microsoft.com/library/cc730660.aspx)

##<a name="pre-requisites"></a>Conditions préalables

* **Batterie de serveurs AD FS** : la version de la batterie de serveurs AD FS doit être 2012 R2 ou ultérieure.
* **Azure AD Connect** : vérifiez que la version d’Azure AD Connect est 1.1.443.0 ou plus récente. Vous trouverez la tâche « Mettre à jour le certificat SSL AD FS ».

![Mettre à jour la tâche SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>Étape 1 : Fournir les informations sur la batterie de serveurs AD FS

AAD Connect va tenter d’obtenir automatiquement les informations sur la batterie de serveurs AD FS en effectuant les opérations suivantes :
1. Interroger les informations sur la batterie de serveurs à partir d’AD FS (2016 ou ultérieur)
2. Référencer les informations obtenues à partir d’exécutions précédentes (stockées localement avec Azure AD Connect) 

Vous pouvez modifier la liste des serveurs affichés en ajoutant ou en supprimant des serveurs afin de refléter la configuration actuelle de la batterie de serveurs AD FS. Dès que les informations de serveur sont fournies, Azure AD Connect affiche la connectivité et l’état actuel du certificat SSL.

![Informations du serveur AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Si la liste contient un serveur qui ne fait plus partie de la batterie de serveurs AD FS, cliquez sur Supprimer pour le supprimer de la liste des serveurs de votre batterie de serveurs AD FS. 

![Serveur hors connexion dans la liste](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> Une suppression dans la liste des serveurs de la batterie de serveurs AD FS dans Azure AD Connect est une opération locale qui met à jour les informations relative à la batterie de serveurs AD FS qu’Azure AD Connect tient à jour localement. Azure AD Connect n’apporte aucune modification de configuration à AD FS pour refléter le changement.    

##<a name="step-2-provide-new-ssl-certificate"></a>Étape 2 : Fournir un nouveau certificat SSL

Une fois les informations sur les serveurs de la batterie de serveurs AD FS confirmées, Azure AD Connect demande le nouveau certificat SSL. Fournissez un certificat PFX protégé par mot de passe pour poursuivre l’installation. 

![Certificat SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
Une fois le certificat fourni, Azure AD Connect effectue une série de vérifications de prérequis sur le certificat pour garantir qu’il convient à la batterie de serveurs AD FS :
1.    Le nom de sujet (SN)/l’autre nom de sujet (SAN) du certificat est identique au nom du service de fédération ou est un certificat à caractères génériques.
2.    Le certificat est valide pendant plus de 30 jours
3.    La chaîne d’approbation du certificat est valide 
4.    Le certificat est protégé par un mot de passe

##<a name="step-3-select-servers-for-update"></a>Étape 3 : Sélectionner les serveurs concernés par la mise à jour

Dans l’étape suivante, sélectionnez les serveurs sur lesquels le certificat SSL doit être mis à jour. Les serveurs qui sont en mode hors connexion ne peuvent pas être sélectionnés pour la mise à jour. 

![Sélectionnez les serveurs à mettre à jour](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Une fois la configuration terminée, Azure AD Connect affiche un message indiquant l’état de la mise à jour et fournit une option permettant de vérifier la connexion AD FS.

![Configuration terminée](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>FAQ

* **Quel doit être le nom de sujet du nouveau certificat SSL AD FS ?**

    Azure AD Connect vérifie si le nom de sujet du certificat/l’autre nom de sujet contient le nom du service de fédération. Par exemple, si le nom de votre service de fédération est fs.contoso.com, le nom de sujet/l’autre nom de sujet doit être fs.contoso.com.  Les certificats à caractères génériques sont également acceptés. 

* **Pourquoi suis-je invité à respécifier des informations d’identification dans la page du serveur WAP ?**

    Si les informations d’identification fournies pour la connexion aux serveurs AD FS ne disposent pas du privilège permettant de gérer également les serveurs WAP, Azure AD Connect vous demande des informations d’identification bénéficiant du privilège administratif sur les serveurs WAP.

* **Que faire si le serveur est affiché comme étant hors connexion ?**

    Azure AD Connect ne peut effectuer aucune opération si le serveur est hors connexion. Si le serveur fait partie de la batterie de serveurs AD FS, vérifiez la connectivité au serveur, puis après avoir résolu le problème, appuyez sur l’icône d’actualisation pour mettre à jour l’état dans l’Assistant. Si le serveur faisait déjà partie de la batterie de serveurs et qu’il n’existe plus, cliquez sur Supprimer pour le supprimer de la liste des serveurs qu’Azure AD Connect tient à jour.  La suppression du serveur de la liste dans Azure AD Connect ne modifie pas la configuration AD FS elle-même. Si vous utilisez AD FS 2016 ou ultérieur, le serveur reste dans les paramètres de configuration et s’affichera lors de la prochaine exécution de la tâche.

* **Puis-je mettre à jour une partie des serveurs de ma batterie de serveurs avec le nouveau certificat SSL ?**

    Oui. Vous pouvez toujours exécuter la tâche « Mettre à jour le certificat SSL » pour mettre à jour les serveurs restants. Dans la page « Sélectionner des serveurs pour la mise à jour du certificat SSL », vous pouvez trier la liste des serveurs sur « Date d’expiration SSL » pour accéder facilement aux serveurs qui ne sont pas encore mis à jour. 

* **J’ai supprimé le serveur lors de l’exécution précédente, mais il est toujours affiché comme étant hors connexion et listé dans la page Serveurs AD FS. Pourquoi le serveur hors connexion est-il toujours affiché même après sa suppression ?**

    La suppression du serveur de la liste dans Azure AD Connect ne le supprime pas de la configuration AD FS. Azure AD Connect référence AD FS (2016 ou ultérieur) également pour toutes les informations relatives à la batterie de serveurs. Si le serveur est toujours présent dans la configuration AD FS, il sera à nouveau affiché dans la liste.  

## <a name="next-steps"></a>Étapes suivantes

[Fédération avec Azure AD Connect](active-directory-aadconnectfed-whatis.md)
[Gestion des services AD FS (Active Directory Federation Services) et personnalisation avec Azure AD Connect](active-directory-aadconnect-federation-management.md)

