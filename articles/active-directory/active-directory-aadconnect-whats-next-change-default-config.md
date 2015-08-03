<properties 
	pageTitle="Modification de la configuration par défaut de Azure AD Connect" 
	description="Apprenez à modifier la configuration par défaut pour Azure AD Connect." 
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
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Modification de la configuration par défaut de Azure AD Connect 


La configuration par défaut d’Azure AD Connect est suffisante dans la plupart des instances pour étendre facilement vos annuaires locaux dans le cloud. Toutefois il existe certains cas dans lesquels vous devrez peut-être modifier la valeur par défaut et l’adapter à la logique métier de votre entreprise. Dans ce cas, vous pouvez modifier la configuration par défaut. Cependant, il existe certaines choses que vous devez savoir avant de le faire.

Lorsque vous devez modifier la configuration par défaut, procédez comme suit :

- Lorsque vous devez modifier le flux d’attributs d’une règle de synchronisation de base, ne modifiez pas cette règle. Créez plutôt une nouvelle règle de synchronisation avec une priorité plus élevée (valeur numérique inférieure), qui contient le flux d’attributs souhaité.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. Cela vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.
- Si vous devez modifier la portée ou le paramètre de jointure dans une règle de synchronisation de base, documentez l’opération et appliquez la modification après la mise à niveau vers une version plus récente d’Azure AD Connect. 

<!---HONumber=July15_HO4-->