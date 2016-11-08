---
title: Qu’est-ce qu’une inscription libre-service à Azure ? | Microsoft Docs
description: Présentation de l’inscription libre-service à Azure, gestion du processus d'inscription, et prise en charge d'un nom de domaine DNS.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/23/2016
ms.author: curtand

---
# Qu’est-ce qu’une inscription libre-service à Azure ?
Cette rubrique vous explique le processus d‘inscription libre-service et vous indique comment prendre en charge un nom de domaine DNS.

## Pourquoi utiliser l’inscription libre-service ?
* Permettre aux clients de bénéficier très rapidement de services dont ils ont besoin.
* Créer des offres envoyées par e-mail pour un service.
* Créer des flux d’inscription par e-mail permettant aux utilisateurs de créer très rapidement des identités à l'aide de leurs alias de messagerie professionnelle, faciles à mémoriser.
* Les répertoires Azure non gérés peuvent être transformés en répertoires gérés ultérieurement et être réutilisés pour d‘autres services.

## Termes et définitions
* **Inscription libre-service** : méthode via laquelle un utilisateur s‘abonne à un service cloud et bénéficie automatiquement d’une identité créée pour lui dans Azure Active Directory (Azure AD) en fonction de son domaine de messagerie.
* **Répertoire Azure non géré** : répertoire dans lequel cette identité est créée. Un répertoire non géré est un répertoire qui ne possède aucun administrateur général.
* **Utilisateur vérifié par e-mail** : type de compte d'utilisateur dans Azure AD. Un utilisateur qui possède une identité créée automatiquement après s’être abonné à une offre libre-service est considéré comme un utilisateur vérifié par e-mail. Un utilisateur vérifié par e-mail est un membre ordinaire d'un répertoire marqué par la valeur creationmethod=EmailVerified.

## Expérience utilisateur
Par exemple, un utilisateur ayant pour adresse e-mail Dan@BellowsCollege.com reçoit les fichiers sensibles par e-mail. Les fichiers ont été protégés par Azure Rights Management (Azure RMS). Mais la société de Dan, Bellows College, n’est pas abonnée à Azure RMS, et elle n’a pas déployé Active Directory RMS. Dans ce cas, Dan peut souscrire un abonnement gratuit à RMS, destiné aux particuliers, pour pouvoir lire les fichiers protégés.

Si Dan est le premier utilisateur disposant d’une adresse e-mail BellowsCollege.com pour s‘abonner à cette offre libre-service, un répertoire non géré sera créé pour BellowsCollege.com dans Azure AD. Si d‘autres utilisateurs du domaine BellowsCollege.com s’abonnent à cette offre ou à une offre libre-service similaire, ils posséderont également des comptes d’utilisateurs vérifiés par e-mail, créés dans le même répertoire non géré d’Azure.

## Expérience administrateur
Un administrateur qui possède le nom de domaine DNS d‘un répertoire Azure non géré peut prendre en charge ou fusionner le répertoire après avoir prouvé qu’il en est le propriétaire. Les sections suivantes vous expliquent plus en détail l'expérience administrateur, mais en voici un résumé :

* Lorsque vous prenez en charge un répertoire Azure non géré, vous devenez tout simplement l‘administrateur général du répertoire non géré. On appelle parfois cela une prise en charge interne.
* Lorsque vous fusionnez un répertoire Azure non géré, vous ajoutez le nom de domaine DNS du répertoire non géré à votre répertoire Azure géré. Un mappage des utilisateurs jusqu’aux ressources est de ce fait créé pour que les utilisateurs puissent continuer à accéder aux services sans interruption. On appelle parfois cela une prise en charge externe.

## Que permet de créer Azure Active Directory ?
#### Répertoire
* Un répertoire Azure Active Directory est créé pour le domaine (un répertoire par domaine).
* Le répertoire Azure AD ne possède aucun administrateur général.

#### Utilisateurs
* Chaque fois qu’un utilisateur s‘abonne, un objet utilisateur est créé dans le répertoire Azure AD.
* Chaque objet utilisateur est considéré comme externe.
* Chaque utilisateur a accès au service auquel il s’est abonné.

### Comment puis-je revendiquer un répertoire Azure AD libre-service pour un domaine qui m’appartient ?
Vous pouvez revendiquer un répertoire Azure AD libre-service en effectuant la validation de domaine. La validation de domaine prouve que le domaine vous appartient, en créant des enregistrements DNS.

Il existe deux façons de réaliser une prise en charge DNS d‘un répertoire Azure AD :

* prise en charge interne (l’administrateur découvre un répertoire Azure non géré et souhaite le convertir en répertoire géré)
* prise en charge externe (l’administrateur essaie d‘ajouter un nouveau domaine à son répertoire Azure géré)

Vous voudrez peut-être valider le fait qu’un domaine vous appartient lorsque vous prendrez en charge un répertoire non géré après qu’un utilisateur ait réalisé une inscription libre-service ou lorsque vous ajouterez un nouveau domaine à un répertoire géré existant. Par exemple, vous possédez un domaine nommé contoso.com et vous souhaitez ajouter un nouveau domaine nommé contoso.co.uk ou contoso.uk.

## Qu’est-ce que la prise en charge d’un domaine ?
Cette section vous explique comment valider le fait qu’un domaine vous appartient

### Qu’est-ce que la validation de domaine et pourquoi est-elle utilisée ?
Pour réaliser des opérations sur un répertoire, Azure AD requiert que vous validiez le fait que le domaine DNS vous appartient. La validation du domaine vous permet d’obtenir le répertoire et de convertir le répertoire libre-service en répertoire géré ou de fusionner le répertoire libre-service avec un répertoire géré existant.

## Exemples de validation de domaine
Il existe deux façons de réaliser une prise en charge DNS d‘un répertoire :

* prise en charge interne (par exemple, un administrateur découvre un répertoire libre-service, non géré, et souhaite le convertir en répertoire géré)
* prise en charge externe (par exemple, un administrateur essaie d‘ajouter un nouveau domaine à un répertoire géré)

### Prise en charge interne : convertir un répertoire libre-service non géré en répertoire géré
Lorsque vous effectuez une prise en charge interne, le répertoire non géré est converti en répertoire géré. Vous devez effectuer la validation du nom de domaine DNS, lorsque vous créez un enregistrement MX ou un enregistrement TXT dans la zone DNS. Cette action :

* Valide le fait que le domaine vous appartient
* Convertit le répertoire en répertoire géré
* Vous désigne administrateur général du répertoire

Supposons qu'un administrateur informatique de Bellows College découvre que les utilisateurs de l'école se sont abonnés aux offres libre-service. En tant que propriétaire enregistré du nom DNS BellowsCollege.com, l‘administrateur informatique peut valider le fait que le nom DNS lui appartient dans Azure, puis prendre en charge le répertoire non géré. Le répertoire devient alors un répertoire géré et l‘administrateur informatique est désigné administrateur général du répertoire BellowsCollege.com.

### Prise en charge externe : fusionner un répertoire libre-service avec un répertoire géré existant
Lors d’une prise en charge externe, vous disposez déjà d‘un répertoire géré et vous souhaitez que tous les utilisateurs et groupes d‘un répertoire non géré rejoignent ce répertoire géré, plutôt que de posséder deux répertoires différents.

En tant qu‘administrateur d‘un répertoire géré, vous ajoutez un domaine qui possède un répertoire non géré qui lui est associé.

Supposons par exemple que vous êtes un administrateur informatique et que vous disposez déjà d‘un répertoire géré pour Contoso.com, un nom de domaine qui a été créé pour votre société. Vous découvrez que les utilisateurs de votre société ont réalisé une inscription libre-service à une offre à l'aide du nom de domaine de messagerie user@contoso.co.uk, qui est un autre nom de domaine appartenant à votre société. Ces utilisateurs possèdent actuellement des comptes dans un répertoire non géré pour contoso.co.uk.

Vous ne souhaitez pas gérer deux répertoires différents, alors vous fusionnez le répertoire non géré pour contoso.co.uk avec votre répertoire informatique géré existant pour contoso.com.

La prise en charge externe suit le même processus de validation DNS que la prise en charge interne. La seule différence étant que les utilisateurs et services sont remappés vers le répertoire informatique géré.

#### Quel est l'impact de l'exécution d'une prise en charge externe ?
Lors d’une prise en charge externe, un mappage des utilisateurs jusqu’aux ressources est créé pour que les utilisateurs puissent continuer à accéder aux services sans interruption. De nombreuses applications, y compris RMS pour les particuliers, gèrent le mappage des utilisateurs jusqu’aux ressources, et les utilisateurs peuvent continuer à accéder à ces services sans la moindre modification. Si une application ne gère pas efficacement le mappage des utilisateurs jusqu’aux ressources, la prise en charge externe peut être explicitement interrompue pour que les utilisateurs ne vivent pas une mauvaise expérience.

#### Prise en charge du répertoire par le service
Les services qui assurent actuellement la prise en charge :

* RMS

Les services qui assureront bientôt la prise en charge :

* PowerBI

L’élément suivant ne requiert aucune action d’administration supplémentaire pour migrer des données utilisateur suite à une prise en charge externe.

* SharePoint/OneDrive

## Comment réaliser la prise en charge d’un nom de domaine DNS
Certaines options vous permettent d’effectuer une validation de domaine (et de réaliser une prise en charge si vous le souhaitez) :

1. Portail de gestion Azure
   
   Une prise en charge est déclenchée lors de l’ajout d’un domaine. Si un répertoire existe déjà pour le domaine, vous avez la possibilité d’effectuer une prise en charge externe.
   
   Connectez-vous au portail de gestion Azure à l’aide de vos informations d’identification. Accédez à votre répertoire existant, puis à **Ajouter un domaine**.
2. Office 365
   
   Vous pouvez utiliser les options de la page [Gérer les domaines](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) d’Office 365 pour vos domaines et enregistrements DNS. Consultez [Vérifier votre domaine dans Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).
3. Windows PowerShell
   
   Pour effectuer une validation à l'aide de Windows PowerShell, procédez comme suit :
   
   | Étape | Applet de commande à utiliser |
   | --- | --- |
   | Créer un objet d'informations d'identification |Get-Credential |
   | Se connecter à Azure AD |Connect-MsolService |
   | obtenir une liste de domaines |Get-MsolDomain |
   | Créer un test |Get-MsolDomainVerificationDns |
   | Créer un enregistrement DNS |Effectuer cette opération sur votre serveur DNS |
   | Vérifier le test |Confirm-MsolEmailVerifiedDomain |

Par exemple :

1. Connectez-vous à Azure AD à l’aide des informations d'identification qui ont été utilisées pour répondre à l'offre libre-service : import-module MSOnline $msolcred = get-credential connect-msolservice -credential $msolcred
2. Obtenez une liste de domaines :
   
    Get-MsolDomain
3. Exécutez ensuite l'applet de commande Get-MsolDomainVerificationDns pour créer un test :
   
    Get-MsolDomainVerificationDns –DomainName *votre\_nom\_de\_domaine* – Mode DnsTxtRecord
   
    Par exemple :
   
    Get-MsolDomainVerificationDns –DomainName contoso.com – Mode DnsTxtRecord
4. Copiez la valeur (le test) qui est renvoyée depuis cette commande.
   
    Par exemple :
   
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. Dans votre espace de noms DNS public, créez un enregistrement DNS txt qui contient la valeur que vous avez copiée lors de l'étape précédente.
   
    Le nom de cet enregistrement représente le nom du domaine parent, donc si vous créez cet enregistrement de ressource à l'aide du rôle DNS de Windows Server, n’attribuez aucun nom à l'enregistrement et collez simplement la valeur dans la zone de texte
6. Exécutez l'applet de commande Confirm-MsolDomain pour vérifier le test :
   
    Confirm-MsolEmailVerifiedDomain -DomainName*votre\_nom\_de\_domaine*
   
    Par exemple,
   
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

Un test réalisé avec succès vous renvoie à l'invite sans erreur.

## Comment vérifier les paramètres libre-service ?
Les administrateurs peuvent désormais effectuer deux vérifications libre-service. Ils peuvent vérifier :

* Si les utilisateurs peuvent rejoindre le répertoire par e-mail.
* Si les utilisateurs peuvent s’abonner eux-mêmes aux applications et services.

### Comment puis-je vérifier ces fonctionnalités ?
Un administrateur peut configurer ces fonctionnalités à l'aide des paramètres Set-MsolCompanySettings de cet applet de commande Azure AD :

* **AllowEmailVerifiedUsers** vérifie si un utilisateur peut créer ou rejoindre un répertoire non géré. Si vous définissez ce paramètre sur $false, aucun utilisateur vérifié par e-mail ne peut rejoindre le répertoire.
* **AllowAdHocSubscriptions** vérifie la capacité des utilisateurs à réaliser une inscription libre-service. Si vous définissez ce paramètre sur $false, aucun utilisateur ne peut effectuer une inscription libre-service.

### Comment les vérifications parviennent-elles à fonctionner ensemble ?
Ces deux paramètres peuvent être utilisés conjointement pour définir une vérification plus précise de l’inscription libre-service. Par exemple, la commande suivante permettra aux utilisateurs de réaliser une inscription libre-service, mais uniquement si ces utilisateurs possèdent déjà un compte dans Azure AD (en d'autres termes, les utilisateurs qui ont besoin d'un compte vérifié par e-mail ne peuvent pas réaliser d’inscription libre-service) :

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

L‘organigramme suivant décrit les différentes combinaisons de paramètres et les conditions qui en résultent pour le répertoire et l’inscription libre-service.

![][1]

Pour en savoir plus et obtenir des exemples d'utilisation de ces paramètres, consultez [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## Voir aussi
* [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Guide de référence des applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
* [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

<!---HONumber=AcomDC_0824_2016-->