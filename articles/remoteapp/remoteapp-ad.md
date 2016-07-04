
<properties 
    pageTitle="Configuration requise d’Azure AD et d’Active Directory pour Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment configurer Active Directory pour l'utiliser avec Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/13/2016" 
    ms.author="elizapo" />



# Configuration requise d’Azure AD et d’Active Directory pour Azure RemoteApp



Si vous souhaitez fédérer une collection hybride RemoteApp Azure ou une collection cloud à l’aide d’AD Connect, vous devez procéder ainsi.

### Connexion à Azure AD et Active Directory

Si vous souhaitez connecter votre locataire Azure AD et vos environnements Active Directory locaux, utilisez AD Connect. Il vous faudra simplement [cliquer 4 fois](http://blogs.technet.com/b/ad/archive/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect.aspx) pour connecter les deux annuaires.

Remarque : la synchronisation d’annuaires est requise pour les collections hybride.

### Vérification de l’adresse « @domain.com »
Avant de commencer, assurez-vous que l’UPN de votre forêt locale correspond au suffixe de votre domaine Azure AD.

Lorsque vous avez configuré le suffixe du domaine UPN dans Azure AD, tous les utilisateurs ouvrant une session dans Azure RemoteApp sont connectés en tant que « utilisateur@<the suffix you set up> ». Assurez-vous que les utilisateurs peuvent également ouvrir une session avec le même user@suffix dans le domaine local. Dans certains cas vous pouvez configurer un nom de domaine dans Azure AD lors de la spécification d’un suffixe de domaine différent pour l’utilisateur local. Dans ce cas, vos utilisateurs ne peuvent plus se connecter à des ordinateurs ou des ressources appartenant à un domaine via Azure RemoteApp.

Par exemple, si vous configurez votre suffixe de domaine UPN dans AAD sous la forme contoso.com, mais que certains utilisateurs locaux d’Active Directory sont configurés pour se connecter avec @contoso.uk, ces utilisateurs ne pourront pas se connecter correctement à la collection ARA. L’UPN des utilisateurs doit être le même pour AAD et AD pour permettre la connexion.

### Création d’objets pour Azure RemoteApp
Vous devez également créer les objets Active Directory locaux suivants :

- Un compte de service afin de fournir l'accès aux ressources du domaine pour les programmes RemoteApp, en rattachant des points de terminaison RDSH au domaine local.
- Une unité d'organisation pour contenir les objets ordinateur RemoteApp. L'utilisation de l'unité d'organisation est recommandée (mais pas obligatoire) pour isoler les comptes et les stratégies que vous utiliserez avec RemoteApp.

Vous avez besoin de ces deux objets lorsque vous créez votre collection RemoteApp ; veuillez donc vous assurer d’avoir déjà effectué ces étapes.

<!---HONumber=AcomDC_0622_2016-->