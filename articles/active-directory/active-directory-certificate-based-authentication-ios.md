<properties 
    pageTitle="Prise en main de l’authentification par certificat sur iOS | Microsoft Azure" 
    description="Découvrez comment configurer l’authentification par certificat dans des solutions avec les appareils iOS" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/15/2016" 
    ms.author="markvi" />



# Prise en main de l’authentification par certificat sur iOS - Version préliminaire publique

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


Cette rubrique vous montre comment configurer et utiliser l’authentification par certificat (CBA) sur un appareil iOS pour les utilisateurs de clients dans les plans Office 365 Enterprise, Business et Education.

CBA vous permet d’être authentifié par Azure Active Directory avec un certificat client sur un appareil Android ou iOS lors de la connexion de votre compte Exchange Online à :

- Des applications mobiles Office, telles que Microsoft Outlook et Microsoft Word ;
- Des clients Exchange ActiveSync (EAS).

La configuration de cette fonctionnalité élimine le besoin d’entrer un nom d’utilisateur et un mot de passe dans certaines applications de messagerie et Microsoft Office sur votre appareil mobile.
 

## Configuration requise et scénarios pris en charge  



### Conditions générales 


Pour tous les scénarios de cette rubrique, les tâches suivantes sont requises :

- Accédez aux autorités de certification pour émettre des certificats clients.

- Les autorités de certification doivent être configurées dans Azure Active Directory. Vous trouverez des instructions détaillées sur l’exécution de la configuration dans la section Prise en main.

- L’autorité de certification racine et les autorités de certification intermédiaires doivent être configurées dans Azure Active Directory.

- Chaque autorité de certification doit avoir une liste de révocation de certificat (CRL) qui peut être référencée via une URL accessible sur Internet.

- Le certificat client doit être émis pour l’authentification du client.


- Pour les clients Exchange ActiveSync uniquement, le certificat client doit avoir l’adresse de messagerie routable de l’utilisateur dans Exchange Online, dans la valeur Nom du principal ou la valeur Nom RFC822 du champ Autre nom de l’objet. Azure Active Directory mappe la valeur RFC822 à l’attribut Adresse proxy dans le répertoire.



### Prise en charge des applications mobiles Office 

| Applications | Support |
| ---                       | ---          |
| OneDrive | Oui |
| Outlook | Bientôt disponible |
| Word / Excel / PowerPoint | Oui |
| Skype Entreprise | Bientôt disponible |


### Configuration requise  

La version du système d’exploitation de l’appareil doit être iOS 9 ou toute version ultérieure

Un serveur de fédération doit être configuré pour exécuter l’authentification par certificat sur les applications Office mobiles.

Azure Authenticator est requis pour les applications Office sur iOS.

Pour qu’Azure Active Directory révoque un certificat client, le jeton ADFS doit posséder les déclarations suivantes :

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Le numéro de série du certificat client)

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (La chaîne de l’émetteur du certificat client)

Azure Active Directory ajoute ces déclarations au jeton d’actualisation si elles sont disponibles dans le jeton ADFS (ou n’importe quel autre jeton SAML). Lorsque le jeton d’actualisation doit être validé, ces informations sont utilisées pour vérifier la révocation.

La meilleure pratique consiste à mettre à jour les pages d’erreur ADFS avec les éléments suivants :

- La configuration requise pour l’installation d’Azure Authenticator sur iOS

- Instructions sur l’obtention d’un certificat utilisateur.

Pour plus d’informations, consultez [Personnalisation des pages de connexion AD FS](https://technet.microsoft.com/library/dn280950.aspx).



### Prise en charge des clients Exchange ActiveSync 


Certaines applications Exchange ActiveSync sur iOS 9 ou version ultérieure sont prises en charge. Pour déterminer si votre application de messagerie prend en charge cette fonctionnalité, contactez le développeur de votre application.



## Prise en main 


Pour commencer, vous devez configurer les autorités de certification dans Azure Active Directory. Pour chaque autorité de certification, vous devez télécharger les éléments suivants :

- La partie publique du certificat, au format *.cer*

- Les URL accessibles sur Internet où résident les listes de révocation de certificat (CRL)
 

Voici le schéma d’une autorité de certification :

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Pour télécharger les informations, vous pouvez utiliser le module Azure AD via Windows PowerShell. Vous trouverez ci-dessous des exemples d’ajout, de suppression ou de modification d’une autorité de certification.



### Configuration de votre client Azure AD pour l’authentification par certificat 

1. Démarrez Windows PowerShell avec les privilèges administrateur.

2. Installez le module Azure AD. Vous devez installer la version [1\.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) ou une version ultérieure.

        Install-Module -Name AzureAD –RequiredVersion 1.1.143.0 

3. Connectez-vous à votre client cible :

        Connect-AzureAD 

### Ajout d’une nouvelle autorité de certification

1. Définissez différentes propriétés de l’autorité de certification et ajoutez celle-ci à Azure Active Directory :

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Obtenez les autorités de certification :

        Get-AzureADTrustedCertificateAuthority 


### Récupération de la liste des autorités de certification

Récupérez les autorités de certification actuellement stockées dans Azure Active Directory pour votre client :

        Get-AzureADTrustedCertificateAuthority 


### Suppression d’une autorité de certification

1.	Récupérez les autorités de certification :

		$c=Get-AzureADTrustedCertificateAuthority 


2. Supprimez le certificat de l’autorité de certification :

		Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### Modification d’une autorité de certification 

1.	Récupérez les autorités de certification :

		$c=Get-AzureADTrustedCertificateAuthority 


2. Modifiez les propriétés de l’autorité de certification :

		$c[0].AuthorityType=1 

3. Définissez **l’autorité de certification** :

		Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## Test des applications Office mobiles  

Pour tester l’authentification par certificat sur votre application Office mobile :

1.	Sur votre appareil de test, installez une application Office mobile (par exemple, OneDrive) à partir de l’App Store.

2.	Vérifiez que le certificat utilisateur a été configuré sur votre appareil de test.

3.	Lancez l’application.

4.	Entrez votre nom d’utilisateur et choisissez le certificat utilisateur que vous souhaitez utiliser.

Vous devez être connecté.





## Test des applications clientes Exchange ActiveSync

Pour accéder à Exchange ActiveSync via l’authentification par certificat, un profil EAS contenant le certificat client doit être disponible pour l’application. Le profil EAS doit contenir les informations suivantes :

- Le certificat utilisateur à utiliser pour l’authentification

- Le point de terminaison EAS doit être outlook.office365.com (actuellement, cette fonctionnalité est uniquement prise en charge dans l’environnement mutualisé Exchange Online)

Un profil EAS peut être configuré et placé sur l’appareil via l’utilisation d’une gestion des appareils mobiles, comme Intune, ou en plaçant manuellement le certificat dans le profil EAS sur l’appareil.

### Test des applications clientes EAS sur iOS 

Pour tester l’authentification par certificat avec l’application de messagerie native sur iOS 9 ou version ultérieure :

1.	Configurez un profil EAS qui respecte les spécifications citées ci-dessus.

2.	Installez le profil sur l’appareil iOS (en utilisant une gestion des appareils mobiles, comme Intune, ou l’application Apple Configurator)

3.	Une fois que le profil est installé correctement, ouvrez l’application de messagerie native et vérifiez la synchronisation de la messagerie



## Révocation

Pour révoquer un certificat client, Azure Active Directory extrait la liste de révocation de certificat (CRL) à partir des URL téléchargées dans le cadre des informations sur l’autorité de certification et la met en cache. L’horodateur de la dernière publication (propriété **Effective Date**) dans la liste de révocation de certificat permet de vérifier si la CRL est toujours valide. La CRL est référencée périodiquement pour révoquer l’accès à des certificats qui font partie de la liste.

Si une révocation plus instantanée est requise (par exemple, si un utilisateur perd un appareil), le jeton d’autorisation de l’utilisateur peut être invalidé. Pour invalider le jeton d’autorisation, définissez le champ **StsRefreshTokenValidFrom** pour cet utilisateur à l’aide de Windows PowerShell. Vous devez mettre à jour le champ **StsRefreshTokenValidFrom** pour chaque utilisateur pour lequel vous souhaitez révoquer l’accès.
 
Pour garantir que la révocation persiste, vous devez définir la propriété **Effective Date** de la CRL sur une date postérieure à la valeur définie par **StsRefreshTokenValidFrom** et vérifiez que le certificat en question est inclus dans la CRL.
 
Les étapes suivantes décrivent le processus de mise à jour et d’invalidation du jeton d’autorisation avec la définition du champ **StsRefreshTokenValidFrom**.

1. Connectez-vous au service MSOL avec les informations d’identification administrateur :

		$msolcred = get-credential 
		connect-msolservice -credential $msolcred 

1.	Récupérez la valeur StsRefreshTokensValidFrom actuelle pour un utilisateur :

		$user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
		$user.StsRefreshTokensValidFrom 


1.	Configurez une nouvelle valeur StsRefreshTokensValidFrom pour l’utilisateur. Elle doit être égale à l’horodateur actuel :

		Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La date que vous définissez doit être dans le futur. Si la date n’est pas dans le futur, la propriété **StsRefreshTokensValidFrom** n’est pas définie. Si la date est dans le futur, la propriété **StsRefreshTokensValidFrom** est définie sur l’heure actuelle (et non la date indiquée par la commande Set-MsolUser).

<!---HONumber=AcomDC_0720_2016-->