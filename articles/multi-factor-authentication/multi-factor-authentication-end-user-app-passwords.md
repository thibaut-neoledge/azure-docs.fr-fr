<properties 
	pageTitle="Quels sont les mots de passe d’application dans Azure MFA ?" 
	description="Cette page permet aux utilisateurs de comprendre ce que sont les mots de passe d’application et leur utilisation avec Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# Que sont les mots de passe d’application dans Azure Multi-Factor Authentication ?

Actuellement, certaines applications sans navigateur telles que le client de messagerie native Apple qui utilise Exchange Active Sync ne prennent pas en charge l’authentification multifacteur. L’authentification multifacteur est activée par l’utilisateur. Cela signifie que si un utilisateur a été activé pour l’authentification multifacteur et qu’il tente d’utiliser les applications sans navigateur, il ne peut le faire. Un mot de passe d’application permet de remédier à ce problème.

>[AZURE.NOTE]Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prennent désormais en charge de nouveaux protocoles d’authentification et ils peuvent être activés pour prendre en charge Multi-Factor Authentication. Cela signifie qu’une fois activés, les mots de passe d’application ne sont pas obligatoirement utilisés avec les clients Office 2013. Pour plus d’informations, consultez la page [Version préliminaire publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Utilisation de mots de passe d’application

Voici quelques points à retenir sur l’utilisation de mots de passe d’application.

- Le mot de passe est généré automatiquement et il n’est pas fourni par l’utilisateur. Le mot de passe automatiquement généré est en effet beaucoup plus difficile à pirater et bien mieux sécurisé.
- À l’heure actuelle, un utilisateur peut posséder jusqu’à 40 mots de passe. Si vous essayez d’en créer un une fois que vous avez atteint la limite, vous devrez supprimer l’un de vos mots de passe d’application existants pour en créer un autre.
- Il est recommandé de créer des mots de passe d’application par appareil et non par application. Par exemple, vous pouvez créer un mot de passe d’application pour votre ordinateur portable et utiliser ce mot de passe pour toutes vos applications sur cet ordinateur.
- Vous obtenez un mot de passe d’application quand vous vous connectez pour la première fois. Si vous avez besoin d’autres mots de passe, vous pouvez les créer.
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

Une fois que vous disposez d’un mot de passe d’application, vous l’utilisez à la place de votre mot de passe d’origine avec ces applications sans navigateur. Par exemple, dans le cas de l’authentification multifacteur et du client de messagerie native Apple sur votre téléphone. Le mot de passe d’application permet à celle-ci de contourner l’authentification multifacteur et de continuer à fonctionner.

### Création de mots de passe d’application
À la première connexion, vous obtenez un mot de passe d’application que vous pouvez utiliser. En outre, vous pouvez également créer des mots de passe d’application plus tard. La procédure à suivre dépend de l’utilisation de l’authentification multifacteur. Choisissez celle qui vous convient le mieux.

Mode d’utilisation de l’authentification multifacteur|Description
:------------- | :------------- | 
[Je l’utilise avec Office 365](multi-factor-authentication-end-user-manage-o365.md)| Cela signifie que vous souhaitez créer des mots de passe d’application par le biais du portail Office 365.
[Je l’utilise avec Microsoft Azure](multi-factor-authentication-end-user-manage-azure.md)| Cela signifie que vous souhaitez créer des mots de passe d’application par le biais du portail Azure.
[Je ne sais pas](multi-factor-authentication-end-user-manage-myapps.md)|Cela signifie que vous pouvez créer des mots de passe d’application par le biais de [https://myapps.microsoft.com](https://myapps.microsoft.com)




 

<!---HONumber=July15_HO2-->