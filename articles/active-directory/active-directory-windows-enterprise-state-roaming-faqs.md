<properties
	pageTitle="FAQ sur l’itinérance des paramètres et des données | Microsoft Azure"
	description="Répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application."
	services="active-directory"
    keywords="paramètres enterprise state roaming, cloud windows, forum aux questions sur enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="femila"/>

# FAQ sur l’itinérance des paramètres et des données

Cette rubrique répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application.

## Quelles données sont itinérantes ?
Les **Paramètres Windows** : les paramètres du PC intégrés au système d’exploitation Windows. En règle générale, ce sont des paramètres qui personnalisent le PC de l’utilisateur. Ils incluent les catégories principales suivantes :

- Le **Thème** : le thème du bureau, les paramètres de la barre des tâches, etc. 
- Les **Paramètres Internet Explorer** : les onglets récemment ouverts, les Favoris, etc.
- Les **Paramètres du navigateur Edge** : les Favoris, les listes de lecture
- Les **Mots de passe** : les mots de passe Internet, les profils Wi-Fi, etc. 
- Les **Préférences linguistiques** : la disposition du clavier, la langue du système, la date et l’heure, etc. 
- Les **Options d’ergonomie** : les thèmes à contraste élevé, le Narrateur, la Loupe, etc. 
- Les **Autres paramètres Windows** : les paramètres d’invite de commandes, la liste des applications, etc. 

Les **Données d’application** : les applications Windows universelles peuvent écrire des données de paramètres dans un dossier « roaming » (itinérance), et toutes les données écrites dans ce dossier sont automatiquement synchronisées. Il incombe aux développeurs d’applications de concevoir eux-mêmes une application pour tirer parti de cette fonctionnalité. Pour plus d’informations sur la façon de développer une application Windows universelle qui utilise l’itinérance, consultez l’[API de stockage des données d’application](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) et le [blog des développeurs d’applications Windows 8](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## Quel compte est utilisé pour la synchronisation des paramètres ?
Dans Windows 8 et Windows 8.1, la synchronisation des paramètres a toujours utilisé les comptes Microsoft consommateur. Les utilisateurs de l’entreprise avaient la possibilité de connecter un compte Microsoft à leur compte de domaine Active Directory pour avoir accès à la synchronisation des paramètres. Dans Windows 10, cette fonctionnalité « compte Microsoft connecté » est remplacée par une structure de compte principal et secondaire.
 
Le compte principal est défini comme le compte utilisé pour se connecter à Windows : il peut s’agir d’un compte Microsoft, d’un compte Azure Active Directory (Azure AD), d’un compte Active Directory local ou d’un compte local. En plus du compte principal, les utilisateurs de Windows 10 peuvent ajouter à leur appareil un ou plusieurs comptes cloud. Ces comptes secondaires sont généralement des comptes Microsoft, Active Directory, ou d’autres comptes tels que Gmail ou Facebook. Ces comptes secondaires permettent d’accéder à des services supplémentaires tels que l’authentification unique et le Windows Store, mais ne prennent pas en charge la synchronisation des paramètres.

Dans Windows 10, seul le compte principal de l’appareil peut être utilisé pour la synchronisation des paramètres (pour connaître les exceptions, consultez la rubrique Comment passer de la synchronisation des paramètres de compte Microsoft dans Windows 8 à la synchronisation des paramètres Azure AD dans Windows 10 ?).

Les données des différents comptes d’utilisateur du périphérique ne sont jamais mélangées. La synchronisation des paramètres est régie par deux règles : - en itinérance, les paramètres Windows sont toujours associés au compte principal. - Les données d’application sont marquées au compte utilisé pour l’acquisition de l’application. Seules les applications marquées avec le compte principal sont synchronisées. Le marquage de propriété des applications est déterminé lors de l’installation d’une application via le Windows Store ou lorsque l’application est chargée via la gestion des appareils mobiles (MDM).

S’il est impossible d’identifier le propriétaire de l’application, celle-ci est itinérante avec le compte principal. Si un appareil est mis à niveau vers Windows 10 à partir de Windows 8 ou Windows 8.1, toutes les applications sont marquées comme acquises par le compte Microsoft. En effet, d’une manière générale, la plupart des applications ont été acquises par le biais du Windows Store, qui n’était pas pris en charge par les comptes Azure AD antérieurs à Windows 10. Si une application est installée via une licence en mode hors connexion, l’application est marquée en utilisant le compte principal de l’appareil.

>[AZURE.NOTE]  
Sur les appareils Windows 10 joints à un environnement Active Directory/Azure AD, il n’est plus possible de connecter un compte Microsoft à un compte de domaine et de synchroniser toutes les données de l’utilisateur sur le compte Microsoft (par exemple, le compte Microsoft en itinérance via la fonctionnalité « connected Microsoft Account and Active Directory », soit « compte Microsoft connecté et Active Directory »).
 
## Comment passer de la synchronisation des paramètres de compte Microsoft dans Windows 8 à la synchronisation des paramètres Azure AD dans Windows 10 ?
Un utilisateur joint au domaine Active Directory fonctionnant sous Windows 8 ou Windows 8.1 avec un compte Microsoft connecté synchronise les paramètres via le compte d’utilisateur Microsoft. Après la mise à niveau vers Windows 10, les utilisateurs joints au domaine continuent à synchroniser leurs paramètres via le compte Microsoft tant que le domaine Active Directory ne se connecte pas avec Azure AD. Si le domaine Active Directory local se connecte avec Azure AD, l’appareil de l’utilisateur essaie de synchroniser les paramètres à l’aide du compte Azure AD connecté. Si l’administrateur Azure AD n’active pas Enterprise State Roaming, la synchronisation des paramètres cesse pour les utilisateurs disposant d’un compte Azure AD connecté. Une fois la synchronisation des paramètres via Azure AD activée, la synchronisation des paramètres Windows commence immédiatement à l’aide d’Azure AD pour les utilisateurs de Windows 10, si ceux-ci ouvrent une session avec une identité Azure AD.

Les utilisateurs ayant stocké des données personnelles sur leur appareil d’entreprise doivent avoir conscience que le système d’exploitation Windows et les données d’application commencent à se synchroniser avec Azure AD. Cela entraîne les conséquences suivantes : - Les paramètres des utilisateurs de comptes Microsoft personnels « se séparent » petit à petit de ceux des comptes Azure AD, puisque le compte Microsoft et la synchronisation des paramètres Azure AD utilisent désormais des comptes distincts. - Les données personnelles telles que les mots de passe Wi-Fi, informations d’identification web ainsi que les Favoris et onglets d’Internet Explorer précédemment synchronisées via un compte Microsoft connecté sont synchronisées via Azure AD.


## Comment fonctionne l’interopérabilité d’Azure AD Enterprise State Roaming et des comptes Microsoft ? 
Dans les versions 2015 de Windows 10, Enterprise State Roaming n’est pris en charge que pour un compte à la fois. Si l’utilisateur se connecte à Windows à l’aide d’un compte Azure AD professionnel, toutes les données sont synchronisées via Azure AD. Si l’utilisateur se connecte à Windows à l’aide d’un compte Microsoft personnel, toutes les données sont synchronisées via le compte Microsoft. En itinérance, les données d’application utilisent le compte de connexion principal de l’appareil si la licence de l’application est détenue par le compte principal. Les applications appartenant à un compte secondaire ne synchronisent pas les données d’application dans Windows 10.

## Les paramètres se synchronisent-ils pour les comptes Azure AD regroupant plusieurs clients ?
Lorsque plusieurs comptes Azure AD regroupant différents clients Azure AD se trouvent sur le même appareil, vous devez mettre à jour le registre de l’appareil afin de communiquer avec le service Azure Rights Management pour chaque client Azure AD.

1. Vous avez tout d’abord besoin du GUID de chaque client Azure AD. Ouvrez le portail Azure Classic et sélectionnez un client Azure AD. Le GUID du client se trouve dans l’URL affichée dans la barre d’adresse de votre navigateur, comme suit : `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Une fois que vous disposez du GUID, vous devez ajouter la clé de Registre suivante : **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC < GUID de l’ID du client >** À partir de la clé <**GUID de l’ID du client**>, créez une nouvelle valeur de chaînes multiples (REG-MULTI-SZ) nommée **AllowedRMSServerUrl**s et pour ses données, spécifiez les URL de point de distribution de licence des autres clients Azure auxquels l’appareil a accès. 
3. Vous trouverez les URL de point de distribution de licence en exécutant l’applet de commande **Get-AadrmConfiguration**. Si les valeurs de **LicensingIntranetDistributionPointUrl ** et de **LicenseingExtranetDistributionPointUrl** sont différentes, spécifiez les deux valeurs. Si les valeurs sont les mêmes, ne spécifiez la valeur qu’une seule fois.


## Quelles sont les options pour les paramètres d’itinérance des applications Windows classiques ?
L’itinérance ne fonctionne qu’avec les applications Windows universelles. Il existe deux options pour activer l’itinérance sur une application Windows classique :

- À l’aide de Project Centennial, vous pouvez facilement convertir une application Windows classique en application Windows universelle. À ce stade, le développeur d’applications n’a besoin d’effectuer que des modifications minimes du code pour pouvoir tirer parti de l’itinérance des données d’application d’Azure AD. Il s’agit de la procédure recommandée pour activer l’itinérance des données d’application Win32. 
- À l’aide du générateur de modèles [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx), vous pouvez créer un modèle de paramètres personnalisé et l’appliquer à votre application chaque fois qu’elle se lance. Cette option n’entraîne aucune modification du code pour le développeur d’applications. Cependant, dans les versions 2015 de Windows 10, UE-V est limité à l’itinérance locale d’Active Directory pour les clients qui ont acheté Microsoft Desktop Optimization Pack. À l’avenir, il est possible que Microsoft cherche des moyens d’intégrer complètement UE-V à Windows et de l’étendre pour permettre l’itinérance des paramètres via le cloud Azure AD. 

## Puis-je stocker localement les paramètres et les données synchronisés ?
Enterprise State Roaming est conçu pour stocker les données de paramètres dans le cloud Azure. UE-V offre une solution d’itinérance locale pour Windows 10.

## Qui gère les données en itinérance ?
L’administrateur client gère les données, qui sont stockées dans un centre de données Azure. Toutes les données utilisateur sont chiffrées en transit et au repos dans le cloud à l’aide d’Azure Rights Management (Azure RMS). Il s’agit d’une amélioration par rapport à la synchronisation des paramètres de compte Microsoft, où seules certaines données sensibles telles que les informations d’identification utilisateur sont chiffrées avant de quitter l’appareil.

Microsoft s’engage à protéger les données client. Les données de paramètres d’un utilisateur de l’entreprise sont automatiquement chiffrées par Azure RMS chaque fois qu’elles quittent un appareil Windows 10, afin qu’elles soient illisibles pour les autres utilisateurs. Si votre organisation dispose d’un abonnement payant à Azure RMS, vous pouvez utiliser d’autres fonctionnalités Azure RMS, telles que le suivi et la suppression de documents, la protection automatique des messages électroniques contenant des informations sensibles et la gestion de vos propres clés (la solution « bring your own key », littéralement « apportez votre propre clé », également appelée BYOK). Pour plus d’informations sur ces fonctionnalités et le fonctionnement d’Azure RMS, consultez [Qu’est-ce qu’Azure Rights Management](https://technet.microsoft.com/jj585026.aspx) ?

## Puis-je gérer la synchronisation d’un paramètre ou d’une application spécifique ?
Dans Windows 10, il n’existe aucun paramètre de MDM ou de stratégie de groupe permettant de désactiver l’itinérance pour une application en particulier. Les administrateurs client peuvent désactiver la synchronisation des données d’application pour toutes les applications sur un appareil géré, mais il n’existe aucun contrôle plus précis au niveau ou au sein de l’application.

## Que peut faire un utilisateur pour activer ou désactiver l’itinérance ?
Dans l’application Paramètres, accédez à Comptes -> Synchroniser vos paramètres. Depuis cette page, vous pouvez voir le compte utilisé pour l’itinérance des paramètres et activer ou désactiver l’itinérance pour chacun des groupes de paramètres.
 
##Quelle est, à ce jour, la recommandation de Microsoft pour l’activation de l’itinérance dans Windows 10 ? 
Microsoft propose quelques solutions d’itinérances de paramètres, notamment les profils utilisateur itinérant, UE-V et Azure AD Roaming. Si votre organisation n’est pas prête à déplacer les données dans le cloud ou qu’elle ne se sent pas à l’aise avec cette idée, Microsoft vous recommande d’utiliser UE-V comme principale technologie d’itinérance. Si votre organisation a besoin d’une prise en charge de l’itinérance pour des applications Windows classiques, mais qu’elle est prête à migrer vers le cloud, Microsoft vous recommande d’utiliser à la fois la synchronisation des paramètres d’entreprise et UE-V. Bien qu’UE-V et la synchronisation des paramètres d’entreprise soient des technologies très similaires, elles ne sont pas incompatibles et aujourd’hui, elles se complètent pour garantir que votre organisation fournit les services d’itinérance dont vos utilisateurs ont besoin. Lorsque vous utilisez à la fois la synchronisation des paramètres d’entreprise et UE-V, les règles suivantes s’appliquent :

- Enterprise State Roaming est l’agent itinérant principal sur l’appareil. UE-V est utilisé pour compléter l’« intervalle Win32 ». 
- L’itinérance UE-V pour les paramètres Windows et les données d’application UWP modernes doit être désactivée car ils sont déjà pris en charge par Enterprise State Roaming. 

## Que se passe-t-il si mon organisation achète Azure RMS après avoir utilisé l’itinérance pendant un certain temps ? 
Si votre organisation utilise déjà l’itinérance dans Windows 10 avec l’abonnement gratuit à Azure RMS, acheter un abonnement payant à Azure RMS n’aura aucun impact sur les fonctionnalités de l’itinérance, et votre administrateur informatique n’aura pas besoin de modifier la configuration.

## Problèmes connus

- Se connecter à Windows avec une carte à puce physique ou virtuelle provoque l’arrêt de la synchronisation des paramètres. Si vous essayez de vous connecter à votre appareil à l’aide d’une carte à puce physique ou virtuelle, la synchronisation cessera de fonctionner. Les mises à jour futures de Windows 10 résoudront peut-être ce problème.
- Il existe un problème avec la synchronisation des **Favoris** dans Internet Explorer. La synchronisation des Favoris d’Internet Explorer est temporairement désactivée pour éviter de causer d’autres problèmes. Les mises à jour futures de Windows 10 résoudront ce problème.


## Rubriques connexes
- [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Activer Enterprise State Roaming dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->