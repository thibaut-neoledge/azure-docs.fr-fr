<properties
	pageTitle="Correction des utilisateurs lors de l’accès aux applications protégées à accès conditionnel en fonction des appareils Azure AD| Microsoft Azure"
	description="Cette rubrique vous aide à identifier si une procédure de correction peut être suivie pour accéder à l’application que vous souhaitez utiliser."
	services="active-directory"
	keywords="accès conditionnel en fonction de l’appareil, inscription de l’appareil, activer l’inscription de l’appareil, inscription de l’appareil et GPM"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="markvi"/>


# Correction des utilisateurs lors de l’accès aux applications protégées à accès conditionnel en fonction des appareils Azure AD

Une page d’accès refusé s’affiche lors de l’accès à une application telle qu’Office 365 SharePoint Online. Que faire dans cette situation ?

Ce guide vous aide à identifier si une procédure de correction peut être suivie pour accéder à l’application que vous souhaitez utiliser.



Sur quelle plate-forme votre appareil est-il exécuté ? La réponse à cette question vous dirige vers l’une des sections suivantes :
 

-	Appareil Windows

-	Appareil iOS (iPhone ou iPad)

-	Appareil Android

## Accès à partir d’un appareil Windows

Si votre appareil exécute Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, sélectionnez la cause appropriée en identifiant la page affichée lors de votre tentative d’accès à l’application.

### L’appareil n’est pas inscrit

Si votre appareil n’est pas inscrit avec Azure Active Directory et que votre application est protégée à l’aide d’une stratégie basée sur l’appareil, une page présentant le contenu suivant peut s’afficher :

![Scénario](./media/active-directory-conditional-access-device-remediation/01.png "Scénario")

 

Si votre appareil est joint à un domaine Active Directory de votre organisation, vous pouvez appliquer la procédure suivante :

1.	Assurez-vous d’être connecté à Windows à l’aide de votre compte de travail (compte Active Directory).

2.	Connectez-vous à votre réseau d’entreprise via VPN ou Direct Access.

3.	Une fois connecté, verrouillez votre session Windows à l’aide de la combinaison touche Windows + L.

4.	Déverrouillez votre session Windows en saisissant les informations d’identification de votre compte de travail.

5.	Attendez une minute, puis réessayez d’accès à l’application.

6.	Si la même page s’affiche, contactez votre administrateur et fournissez les détails après avoir cliqué sur le lien Détails supplémentaires.

Si votre appareil n’est pas joint au domaine et exécute Windows 10, vous disposez de deux options :

1. Exécuter Azure AD Join
2. Ajoutez votre compte professionnel ou scolaire à Windows.

Pour plus d’informations sur les différences entre les deux, consultez la section [Utilisation d’appareils Windows 10 sur votre lieu de travail](active-directory-azureadjoin-windows10-devices.md).

Pour exécuter Azure AD Join, exécutez la procédure suivante (indisponible dans Windows Phone) :

**Mise à jour anniversaire Windows 10**

1.	Lancez l’application Paramètres.

2.	Accédez à « Comptes », puis choisissez entre le compte professionnel ou scolaire.

3.	Cliquez sur « Se connecter ».

4.	Choisissez l’option permettant de joindre l’appareil à Azure AD, en bas de la page.

5.	Authentifiez-vous à votre organisation, fournissez une preuve MFA si nécessaire, et suivez la procédure jusqu’à la fin.

6.	Déconnectez-vous et connectez-vous à l’aide de votre compte professionnel.

7.	Essayez d’accéder à nouveau à l’application.




**Mise à jour Windows 10 de novembre 2015**


1.	Lancez l’application Paramètres.

2.	Accédez à « Système », puis à « À propos ».
	
3.	Cliquez sur « Jointure Azure AD ».

4.	Authentifiez-vous à votre organisation, fournissez une preuve MFA si nécessaire, et suivez la procédure jusqu’à la fin.

5.	Déconnectez-vous et connectez-vous à l’aide de votre compte professionnel (compte Azure AD).

6.	Essayez d’accéder à nouveau à l’application.


Pour ajouter votre compte professionnel ou scolaire, suivez la procédure suivante :

**Mise à jour anniversaire Windows 10**

1.	Lancez l’application Paramètres.

2.	Accédez à « Comptes », puis choisissez entre le compte professionnel ou scolaire.

3.	Cliquez sur « Se connecter ».

4.	Authentifiez-vous à votre organisation, fournissez une preuve MFA si nécessaire, et suivez la procédure jusqu’à la fin.

5.	Essayez d’accéder à nouveau à l’application.


**Mise à jour Windows 10 de novembre 2015**
	
1.	Lancez l’application Paramètres.
2.	Accédez à « Comptes», puis à vos comptes.
3.	Cliquez sur l’option permettant d’ajouter un compte professionnel ou scolaire.
4.	Authentifiez-vous à votre organisation, fournissez une preuve MFA si nécessaire, et suivez la procédure jusqu’à la fin.
5.	Essayez d’accéder à nouveau à l’application.

Si votre appareil n’est pas joint au domaine et exécute Windows 8.1, vous pouvez procéder à une jonction d’espace de travail et vous inscrire à Microsoft Intune en appliquant la procédure suivante :

1.	Lancez les paramètres du PC.

2.	Accédez à « Réseau », puis à « Espace de travail ».

3.	Cliquez sur « Joindre ».

4.	Authentifiez-vous à votre organisation, fournissez une preuve MFA si nécessaire, et suivez la procédure jusqu’à la fin.

5.	Cliquez sur « Activer ».

6.	Patientez jusqu'à la fin.

7.	Essayez d’accéder à nouveau à l’application.


## Navigateur non pris en charge

Si vous accédez à l’application à partir des navigateurs suivants, une page similaire à la page représentée ci-dessous s’affiche :

1.	Chrome, Firefox ou tout autre navigateur différent de Microsoft Edge ou Microsoft Internet Explorer dans Windows 10 ou Windows Server 2016.

2.	Firefox dans Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.
 

![Scénario](./media/active-directory-conditional-access-device-remediation/02.png "Scénario")


La seule correction consiste à utiliser un navigateur pris en charge par la plateforme de votre appareil.

## Accéder à partir d’un appareil iOS

Revenez plus tard consulter les instructions pour iPhones ou iPads.

## Accéder à partir d’un appareil Android

Revenez plus tard consulter les instructions pour téléphones ou tablettes Android.


## Étapes suivantes

[Accès conditionnel Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0817_2016-->