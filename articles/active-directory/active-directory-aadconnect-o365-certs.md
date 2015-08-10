<properties 
	pageTitle="Instructions de renouvellement des certificats pour les utilisateurs Office 365 et Azure AD." 
	description="Cet article explique aux utilisateurs d’Office 365 comment procéder s’ils reçoivent un e-mail les invitant à renouveler un certificat." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="billmath"/>


# Renouvellement des certificats de fédération pour Office 365 et Azure AD

Si vous avez reçu un e-mail ou une notification du portail vous invitant à renouveler votre certificat pour Office 365, cet article vous explique comment procéder et éviter que le problème ne se reproduise. Ces informations reposent sur l’hypothèse que vous utilisez AD FS (services de fédération Active Directory) comme serveur de fédération.

## Vérifier si une intervention de votre part est nécessaire

Si vous utilisez AD FS 2.0 ou une version ultérieure, Office 365 et Azure AD mettent automatiquement à jour votre certificat avant qu’il n’arrive à expiration. Vous n’avez pas besoin de suivre de procédure manuelle ni d’exécuter de script sous la forme d’une tâche planifiée. Pour que ceci fonctionne, les deux paramètres de configuration AD FS par défaut ci-dessous doivent être en vigueur :

- La propriété AD FS AutoCertificateRollover doit être définie sur True, indiquant qu’AD FS génère automatiquement de nouveaux certificats de signature de jetons et de déchiffrement de jeton avant que les anciens certificats n’arrivent à expiration.
	- Si cette propriété est définie sur la valeur False, vous utilisez des paramètres de certificat personnalisés. Cliquez [ici](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) pour découvrir la procédure à suivre dans ce cas.
- Vos métadonnées de fédération doivent être accessibles par le réseau Internet public.
	
	Voici comment vérifier ce point :

	- Assurez-vous que votre installation AD FS utilise la substitution de certificat automatique en exécutant la commande suivante dans une fenêtre de commande PowerShell sur votre serveur de fédération principal :

	`PS C:\> Get-ADFSProperties`

(Notez que si vous utilisez AD FS 2.0, vous devrez commencer par exécuter Add-Pssnapin Microsoft.Adfs.Powershell.)

Vérifiez que vos métadonnées de fédération sont publiquement accessibles en accédant à l’URL ci-dessous à partir d’un ordinateur relié au réseau Internet public (en dehors du réseau de l’entreprise) :


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

où `(your_FS_name) ` est remplacé par le nom d’hôte du service de fédération utilisé par votre organisation, tel que fs.contoso.com. Si vous êtes parvenu à vérifier la présence de ces deux paramètres, vous n’avez rien d’autre à faire.

Exemple : https://fs.contos.com/federationmetadata/2007-06/federationmetadata.xml

## Si votre propriété AutoCertificateRollover est définie sur False

Si la propriété AutoCertificateRollover est définie sur False, vous utilisez des paramètres de certificat AD FS autres que ceux par défaut. Le plus souvent, ceci s’explique par le fait que votre organisation gère les certificats AD FS inscrits auprès d’une autorité de certification d’organisation. Dans ce cas, vous devez vous-même renouveler et mettre à jour vos certificats. Suivez les instructions fournies [ici](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

## Si vos métadonnées ne sont pas accessibles publiquement
Si votre paramètre AutocertificateRollover est défini sur True, mais que vos métadonnées de fédération ne sont pas disponibles publiquement, suivez la procédure ci-dessous pour vérifier que vos certificats sont mis à jour à la fois au niveau local et dans le cloud :

### Vérifier que votre système AD FS a généré un nouveau certificat 

- Vérifiez que vous êtes connecté au serveur AD FS principal.
- Vérifiez les certificats de signature actuels dans AD FS en ouvrant une fenêtre de commande PowerShell et en exécutant la commande suivante : 

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(Notez que si vous utilisez AD FS 2.0, vous devrez commencer par exécuter Add-Pssnapin Microsoft.Adfs.Powershell.)


- Examinez les certificats répertoriés dans la sortie de la commande. Si AD FS a généré un nouveau certificat, vous devez voir deux certificats dans la sortie : l’un dont la valeur IsPrimary est définie sur True et dont la date NotAfter correspond à 5 jours, et l’autre avec une valeur IsPrimary définie sur False et une valeur NotAfter correspondant à environ un an après la date du jour.
	
- Si vous ne voyez qu’un seul certificat et que la date NotAfter est définie sur 5 jours, vous devez générer un nouveau certificat en suivant la procédure ci-après.

- Pour générer un nouveau certificat, exécutez la commande ci-dessous au niveau d’une invite de commande PowerShell : `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

- Vérifiez la mise à jour en ré-exécutant la commande suivante : PS C:\>Get-ADFSCertificate –CertificateType token-signing
- Ensuite, procédez comme suit pour mettre à jour manuellement les propriétés d’approbation de fédération Office 365.

Vous devez alors voir apparaître deux certificats, dont l’un présente une date NotAfter correspondant à environ un an après la date du jour et dont la valeur IsPrimary est définie sur False.


### Mettez manuellement à jour les propriétés d’approbation de fédération Office 365 en procédant comme suit.

1.	Ouvrez le Module Microsoft Azure Active Directory pour Windows PowerShell.
2.	Exécutez la commande $cred=Get-Credential. Lorsque cette applet de commande vous demande des informations d’identification, tapez vos informations d’identification de compte administrateur de services cloud.
3.	Exécutez la commande Connect-MsolService –Credential $cred. Cette applet de commande vous connecte au service cloud. Avant d’exécuter l’une des applets de commande supplémentaires installées par l’outil, vous devez créer un contexte qui vous connecte au service cloud.
4.	Si vous exécutez ces commandes sur un ordinateur autre que le serveur de fédération principal AD FS, exécutez Set-MSOLAdfscontext -Computer <AD FS primary server>, où <AD FS primary server> est le nom de domaine complet interne du serveur AD FS principal. Cette applet de commande crée un contexte qui vous connecte à AD FS. 
5.	Exécutez Update-MSOLFederatedDomain –DomainName <domain>. Cette applet de commande met à jour les paramètres d’AD FS dans le service cloud et configure la relation d’approbation entre les deux.

>[AZURE.NOTE]Si vous avez besoin de prendre en charge plusieurs domaines de premier niveau, par exemple contoso.com et fabrikam.com, vous devez utiliser le commutateur SupportMultipleDomain avec les applets de commande. Pour plus d’informations, voir l’article concernant la prise en charge de plusieurs domaines de premier niveau. Pour finir, vérifiez que tous les serveurs Proxy d’application Web sont mis à jour avec le correctif cumulatif de [mai 2014 pour Windows Server](http://support.microsoft.com/kb/2955164) ; dans le cas contraire, les proxys risquent de ne pas se mettre à jour avec le nouveau certificat, ce qui entraînera une panne.

<!---HONumber=July15_HO5-->