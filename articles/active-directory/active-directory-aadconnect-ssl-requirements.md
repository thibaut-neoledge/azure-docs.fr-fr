<properties 
	pageTitle="Azure AD Connect - Configuration requise des certificats SSL"
	description="Conditions requises des certificats SSL Azure AD Connect pour une utilisation avec AD FS."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Azure AD Connect - Configuration requise des certificats SSL

**Important :** il est fortement recommandé d’utiliser le même certificat SSL sur tous les nœuds de votre batterie de serveurs AD FS, ainsi que sur tous les serveurs proxy d’application web.

- Il doit s’agir d’un certificat X 509. 
- Vous pouvez utiliser un certificat auto-signé sur des serveurs de fédération dans un environnement de test. Toutefois, pour un environnement de production, nous vous recommandons d’obtenir le certificat auprès d’une autorité de certification publique. 
	- Si vous utilisez un certificat qui n'est pas approuvé publiquement, assurez-vous que le certificat installé sur chaque serveur proxy d’application web est approuvé sur le serveur local et sur tous les serveurs de fédération. 
- Les certificats basés sur les clés CNG (CryptoAPI Next Generation) et les fournisseurs de stockage de clés ne sont pas pris en charge. Cela signifie que vous devez utiliser un certificat basé sur un fournisseur de services de chiffrement et non sur un fournisseur de stockage de clés. 
- L'identité du certificat doit correspondre au nom du service de fédération (par exemple, fs.contoso.com). 
	- L'identité est soit une extension autre nom de l’objet (SAN) de type dNSName, soit à défaut d'entrée SAN, le nom d’objet spécifié comme nom commun.  
	- Le certificat peut contenir plusieurs entrées SAN, pour autant que l'une d'elles corresponde au nom de service de fédération. 
	- Si vous envisagez d'utiliser une jonction d'espace de travail, un autre nom d’objet supplémentaire est nécessaire avec la valeur « enterpriseregistration. », suivie du suffixe de nom d'utilisateur principal de votre organisation, par exemple, enterpriseregistration.contoso.com. 

Les certificats utilisant des caractères génériques sont pris en charge.
 

<!---HONumber=August15_HO9-->