---
title: "Utilisation des domaines personnalisés dans le proxy d’application Azure AD | Microsoft Docs"
description: "Explique comment utiliser les domaines personnalisés dans le proxy d&quot;application Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fedd64ad885bacedf01afd91adf0c4d05265c476


---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Utilisation des domaines personnalisés dans le proxy d'application Azure AD
En utilisant un domaine par défaut, vous pouvez définir une même URL en tant qu’URL interne et externe pour accéder à l’application. Vos utilisateurs n’ont ainsi qu’une seule URL à mémoriser pour accéder à l’application, quel que soit l’endroit à partir duquel ils y accèdent. Cela vous permet aussi de créer un raccourci unique dans le volet d’accès de l’application. Si vous utilisez le domaine par défaut fourni par le proxy d'application Azure AD, il n'y a aucune autre configuration nécessaire pour activer votre domaine. Dans le cas où vous utilisez un domaine personnalisé, vous avez quelques procédures à suivre pour vous assurer que le proxy d'application reconnaît bien votre domaine et valide ses certificats.

## <a name="selecting-your-custom-domain"></a>Sélection de votre domaine personnalisé
1. Publiez votre application en suivant les instructions fournies dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md).
2. Une fois que l'application s'affiche dans la liste des applications, sélectionnez-la et cliquez sur **Configurer**.
3. Sous **URL externe**, entrez votre domaine personnalisé.
4. Si votre URL externe est https, vous serez invité à télécharger un certificat afin qu’Azure puisse valider l'URL de l'application. Vous pouvez également télécharger un certificat générique qui correspond à l'URL externe de l'application. Ce domaine doit se trouver dans la liste de vos [domaines vérifiés par Azure](https://msdn.microsoft.com/library/azure/jj151788.aspx). Azure doit avoir un certificat pour l'URL du domaine de l'application ou un certificat générique qui correspond à l'URL externe pour l'application.
5. Veillez à ajouter un enregistrement DNS qui achemine l'URL interne vers l'application qui vous permet d'avoir la même URL pour l'accès interne et externe et un seul raccourci vers l'application dans la liste des applications de l'utilisateur.

## <a name="frequently-asked-questions-about-working-with-custom-domains"></a>Forum aux questions sur l'utilisation des domaines personnalisés
Q : Puis-je sélectionner un certificat déjà téléchargé sans avoir à le télécharger à nouveau ?  
R : Les certificats déjà chargés sur le serveur sont automatiquement liés à une application, et un seul certificat exactement correspond au nom d’hôte de l’application.  

Q : Comment ajouter un certificat et dans quel format le certificat exporté doit-il être chargé sur le serveur ?  
R : Le certificat doit être chargé sur le serveur à partir de la page de configuration de l’application. Le certificat doit être un fichier PFX.  

Q : Peut-on utiliser des certificats ECC ?  
R : Il n’existe aucune limitation explicite sur les méthodes de signature.  

Q : Peut-on utiliser des certificats SAN ?  
R. : Oui.  

Q : Peut-on utiliser des certificats génériques ?  
R. : Oui.  

Q : Peut-on utiliser un certificat différent pour chaque application ?  
R : Oui, sauf si les deux applications partagent le même hôte externe.  

Q : Si j'inscris un nouveau domaine, puis-je utiliser ce domaine ?  
R : Oui, la liste des domaines est chargée à partir de la liste des domaines vérifiés du client.  

Q : Que se passe-t-il quand un certificat expire ?  
R : Vous obtenez un avertissement dans la section Certificat de la page de configuration de l’application. Quand un utilisateur tente d’accéder à l’application, un avertissement de sécurité s’affiche. ...  

Q : Que dois-je faire si je souhaite remplacer un certificat pour une application donnée ?  
R : Chargez un nouveau certificat sur le serveur à partir de la page de configuration de l’application.  

Q : Puis-je supprimer un certificat et le remplacer ?  
R : Quand vous chargez un nouveau certificat sur le serveur, si l’ancien certificat n’est pas utilisé par une autre application, il est automatiquement supprimé.  

Q : Que se passe-t-il quand un certificat est révoqué ?  
R : Aucune vérification n’a lieu pour déterminer si les certificats sont révoqués. Quand un utilisateur tente d’accéder à l’application, selon le navigateur, un avertissement de sécurité peut apparaître.  

Q : Puis-je utiliser un certificat auto-signé ?  
R : Oui, les certificats auto-signés sont autorisés. Notez que si vous utilisez une autorité de certification privée, le point de distribution de la liste de révocation de certificats du certificat doit être public.  

Q : Existe-t-il un endroit où je peux afficher tous les certificats de mon client ?  
R : Cette fonctionnalité n’est pas prise en charge dans la version actuelle.  

## <a name="see-also"></a>Voir aussi
* [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
* [Ajout de votre propre nom de domaine à Azure AD](active-directory-add-domain.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Dec16_HO4-->


