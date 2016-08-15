<properties
	pageTitle="FAQ sur l’itinérance des paramètres et des données | Microsoft Azure"
	description="Répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application."
	services="active-directory"
    keywords="paramètres enterprise state roaming, cloud windows, forum aux questions sur enterprise state roaming"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="femila"/>

# FAQ sur l’itinérance des paramètres et des données

Cette rubrique répond à certaines questions que les administrateurs informatiques peuvent se poser sur les paramètres et la synchronisation des données d’application.

## Quelles données sont itinérantes ?
Les **Paramètres Windows** : les paramètres du PC intégrés au système d’exploitation Windows. En règle générale, ce sont des paramètres qui personnalisent le PC de l’utilisateur. Ils incluent les catégories principales suivantes :

- Le **Thème** : le thème du bureau, les paramètres de la barre des tâches, etc.
- Les **Paramètres Internet Explorer** : les onglets récemment ouverts, les Favoris, etc.
- Les **Paramètres du navigateur Edge** : les Favoris, les listes de lecture
- Les **Mots de passe** : les mots de passe Internet, les profils Wi-Fi, etc.
- Les **Préférences linguistiques** : la disposition du clavier, la langue du système, la date et l’heure, etc.
- Les **Options d’ergonomie** : les thèmes à contraste élevé, le Narrateur, la Loupe, etc.
- Les **Autres paramètres Windows** : les paramètres d’invite de commandes, la liste des applications, etc.

Les **Données d’application** : les applications Windows universelles peuvent écrire des données de paramètres dans un dossier « roaming » (itinérance), et toutes les données écrites dans ce dossier sont automatiquement synchronisées. Il incombe aux développeurs d’applications de concevoir eux-mêmes une application pour tirer parti de cette fonctionnalité. Pour plus d’informations sur la façon de développer une application Windows universelle qui utilise l’itinérance, consultez l’[API de stockage des données d’application](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) et le [blog des développeurs d’applications Windows 8](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## Quel compte est utilisé pour la synchronisation des paramètres ?
Dans Windows 8 et Windows 8.1, la synchronisation des paramètres a toujours utilisé les comptes Microsoft consommateur. Les utilisateurs de l’entreprise avaient la possibilité de connecter un compte Microsoft à leur compte de domaine Active Directory pour avoir accès à la synchronisation des paramètres. Dans Windows 10, cette fonctionnalité « compte Microsoft connecté » est remplacée par une structure de compte principal et secondaire.

Le compte principal est défini comme le compte utilisé pour se connecter à Windows : il peut s’agir d’un compte Microsoft, d’un compte Azure Active Directory (Azure AD), d’un compte Active Directory local ou d’un compte local. En plus du compte principal, les utilisateurs de Windows 10 peuvent ajouter à leur appareil un ou plusieurs comptes cloud. Ces comptes secondaires sont généralement des comptes Microsoft, Active Directory, ou d’autres comptes tels que Gmail ou Facebook. Ces comptes secondaires permettent d’accéder à des services supplémentaires tels que l’authentification unique et le Windows Store, mais ne prennent pas en charge la synchronisation des paramètres.

Dans Windows 10, vous pouvez utiliser uniquement le compte principal de l’appareil pour synchroniser les paramètres (voir [Comment passer de la synchronisation des paramètres de compte Microsoft dans Windows 8 à la synchronisation des paramètres Azure AD dans Windows 10 ?] \(active-directory-windows-enterprise-state-roaming-faqs.md#Comment passer de la synchronisation des paramètres de compte Microsoft dans Windows 8 à la synchronisation des paramètres Azure AD dans Windows 10 )).

Les données des différents comptes d’utilisateur du périphérique ne sont jamais mélangées. Il existe deux règles de synchronisation des paramètres :
- les paramètres Windows sont toujours itinérants avec le compte principal ;
- les données d’application sont marquées avec le compte utilisé pour l’acquisition de l’application. Seules les applications marquées avec le compte principal sont synchronisées. Le marquage de propriété des applications est déterminé lors du chargement parallèle d’une application via le Windows Store ou lorsque l’application est chargée via la gestion des appareils mobiles (MDM).

S’il est impossible d’identifier le propriétaire de l’application, celle-ci est itinérante avec le compte principal. Si un appareil est mis à niveau vers Windows 10 à partir de Windows 8 ou Windows 8.1, toutes les applications sont marquées comme acquises par le compte Microsoft. En effet, d’une manière générale, la plupart des applications ont été acquises par le biais du Windows Store, qui n’était pas pris en charge par les comptes Azure AD antérieurs à Windows 10. Si une application est installée via une licence en mode hors connexion, l’application est marquée en utilisant le compte principal de l’appareil.

>[AZURE.NOTE]  
Les appareils Windows 10 détenus par une entreprise et connectés à Azure AD ne peuvent plus connecter leurs comptes Microsoft à un compte de domaine. Sur les appareils Windows 10 joints à un environnement Active Directory/Azure AD, il n’est plus possible de connecter un compte Microsoft à un compte de domaine et de synchroniser toutes les données de l’utilisateur sur le compte Microsoft (par exemple, le compte Microsoft en itinérance via la fonctionnalité « connected Microsoft Account and Active Directory », soit « compte Microsoft connecté et Active Directory »).

## Comment passer de la synchronisation des paramètres de compte Microsoft dans Windows 8 à la synchronisation des paramètres Azure AD dans Windows 10 ?
Un utilisateur joint au domaine Active Directory fonctionnant sous Windows 8 ou Windows 8.1 avec un compte Microsoft connecté synchronise les paramètres via le compte d’utilisateur Microsoft. Après la mise à niveau vers Windows 10, les utilisateurs joints au domaine continuent à synchroniser leurs paramètres via le compte Microsoft tant que le domaine Active Directory ne se connecte pas avec Azure AD. Si le domaine Active Directory local se connecte avec Azure AD, l’appareil de l’utilisateur essaie de synchroniser les paramètres à l’aide du compte Azure AD connecté. Si l’administrateur Azure AD n’active pas Enterprise State Roaming, la synchronisation des paramètres cesse pour les utilisateurs disposant d’un compte Azure AD connecté. Une fois la synchronisation des paramètres via Azure AD activée, la synchronisation des paramètres Windows commence immédiatement à l’aide d’Azure AD pour les utilisateurs de Windows 10, si ceux-ci ouvrent une session avec une identité Azure AD.

Les utilisateurs ayant stocké des données personnelles sur leur appareil d’entreprise doivent avoir conscience que le système d’exploitation Windows et les données d’application commencent à se synchroniser avec Azure AD. Cela entraîne les conséquences suivantes :

- Les utilisateurs constateront progressivement des différences entre les paramètres de leurs comptes Microsoft personnels et les paramètres de leurs comptes Azure AD professionnels ou scolaires puisque la synchronisation des paramètres de compte Microsoft et d’Azure AD utilisent désormais des comptes distincts.
- Les données personnelles (mots de passe Wi-Fi, informations d’identification web ou encore onglets et favoris d’Internet Explorer) qui étaient auparavant synchronisées via un compte Microsoft seront maintenant synchronisées via Azure AD.


## Comment fonctionne l’interopérabilité d’Azure AD Enterprise State Roaming et des comptes Microsoft ?
Dans les versions Windows 10 publiées à partir de novembre 2015 inclus, Enterprise State Roaming n’est pris en charge que pour un compte à la fois. Si l’utilisateur se connecte à Windows à l’aide d’un compte Azure AD professionnel ou scolaire, toutes les données seront synchronisées via Azure AD. Si l’utilisateur se connecte à Windows à l’aide d’un compte Microsoft personnel, toutes les données sont synchronisées via le compte Microsoft. En itinérance, les données d’application universelles utilisent le compte de connexion principal de l’appareil uniquement si la licence de l’application est détenue par le compte principal. Les données d’application universelles appartenant à un compte secondaire ne seront pas synchronisées.

## Les paramètres se synchronisent-ils pour les comptes Azure AD regroupant plusieurs clients ?
Lorsque plusieurs comptes Azure AD regroupant différents clients Azure AD se trouvent sur le même appareil, vous devez mettre à jour le registre de l’appareil afin de communiquer avec le service Azure Rights Management pour chaque client Azure AD.

1. Vous avez tout d’abord besoin du GUID de chaque client Azure AD. Ouvrez le portail Azure Classic et sélectionnez un client Azure AD. Le GUID du client se trouve dans l’URL affichée dans la barre d’adresse de votre navigateur, comme suit : `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Une fois que vous disposez du GUID, vous devez ajouter la clé de Registre suivante : **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC < GUID de l’ID du client >** À partir de la clé <**GUID de l’ID du client**>, créez une nouvelle valeur de chaînes multiples (REG-MULTI-SZ) nommée **AllowedRMSServerUrl**s et pour ses données, spécifiez les URL de point de distribution de licence des autres clients Azure auxquels l’appareil a accès.
3. Vous trouverez les URL de point de distribution de licence en exécutant l’applet de commande **Get-AadrmConfiguration**. Si les valeurs des paramètres **LicensingIntranetDistributionPointUrl** et **LicenseingExtranetDistributionPointUrl** sont différentes, spécifiez les deux valeurs. Si les valeurs sont les mêmes, ne spécifiez la valeur qu’une seule fois.


## Quelles sont les options disponibles pour les paramètres d’itinérance des applications de bureau Windows existantes ?
L’itinérance ne fonctionne qu’avec les applications Windows universelles. Deux options permettent d’activer l’itinérance sur une application de bureau Windows existante :

- [Desktop Bridge](http://aka.ms/desktopbridge) vous permet de transférer facilement vos applications de bureau Windows existantes vers la plateforme Windows universelle. À ce stade, seules quelques modifications de code mineures sont nécessaires pour pouvoir tirer parti de l’itinérance des données d’application d’Azure AD. Desktop Bridge fournit à vos applications l’identité d’application nécessaire pour activer l’itinérance des données d’application pour les applications de bureau existantes.
- [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) vous permet de créer un modèle de paramètres personnalisés pour les applications de bureau Windows existantes et d’activer le mode itinérant uniquement pour les applications Win32. Cette option n’oblige pas le développeur d’application à modifier le code de l’application. UE-V est limité à l’itinérance Active Directory en local pour les clients qui ont acheté Microsoft Desktop Optimization Package.

L’administrateur peut configurer UE-V pour déplacer simplement les données d’application de bureau Windows en désactivant l’itinérance des paramètres du système d’exploitation Windows et des données d’application universelle à l’aide des [stratégies de groupe UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2).

- Désactivez la stratégie de groupe « Roam Windows settings » (Activer l’itinérance des paramètres Windows)
- Activez la stratégie de groupe « Do not synchronize Windows Apps » (Ne pas synchroniser les applications Windows)
- Désactivez l’itinérance IE dans la section des applications

À l’avenir, il est possible que Microsoft cherche des moyens d’intégrer complètement UE-V à Windows et de l’étendre pour permettre l’itinérance des paramètres via le cloud Azure AD.


## Puis-je stocker localement les paramètres et les données synchronisés ?
Enterprise State Roaming stocke toutes les données synchronisées dans le cloud Azure, tandis qu’UE-V offre une solution d’itinérance locale pour les entreprises qui ont besoin d’une solution d’itinérance en local.

## À qui appartiennent les données en itinérance ?
Les données en itinérance via Enterprise State Roaming sont détenues par les entreprises. Les données sont stockées dans un centre de données Azure. Toutes les données utilisateur sont chiffrées en transit et au repos dans le cloud à l’aide d’Azure Rights Management (Azure RMS). Il s’agit d’une amélioration par rapport à la synchronisation des paramètres de compte Microsoft, où seules certaines données sensibles telles que les informations d’identification utilisateur sont chiffrées avant de quitter l’appareil.

Microsoft s’engage à protéger les données client. Les données de paramètres d’un utilisateur de l’entreprise sont automatiquement chiffrées par Azure RMS avant qu’elles quittent un appareil Windows 10, afin qu’elles soient illisibles pour les autres utilisateurs. Si votre organisation dispose d’un abonnement payant à Azure RMS, vous pouvez utiliser d’autres fonctionnalités Azure RMS, telles que le suivi et la suppression de documents, la protection automatique des messages électroniques contenant des informations sensibles et la gestion de vos propres clés (la solution « bring your own key », littéralement « apportez votre propre clé », également appelée BYOK). Pour plus d’informations sur ces fonctionnalités et sur le fonctionnement d’Azure RMS, consultez l’article [En quoi consiste Azure Rights Management ?](https://technet.microsoft.com/jj585026.aspx).

## Puis-je gérer la synchronisation d’un paramètre ou d’une application spécifique ?
Dans Windows 10, il n’existe aucun paramètre de MDM ou de stratégie de groupe permettant de désactiver l’itinérance pour une application en particulier. Les administrateurs client peuvent désactiver la synchronisation des données d’application pour toutes les applications sur un appareil géré, mais il n’existe aucun contrôle plus précis au niveau ou au sein de l’application.

## Que peut faire un utilisateur pour activer ou désactiver l’itinérance ?
Dans l’application **Paramètres**, accédez à **Comptes** -> **Synchroniser vos paramètres**. Depuis cette page, vous pouvez voir le compte utilisé pour l’itinérance des paramètres et activer ou désactiver l’itinérance pour chacun des groupes de paramètres.

##Quelle est, à ce jour, la recommandation de Microsoft pour l’activation de l’itinérance dans Windows 10 ?
Microsoft propose quelques solutions d’itinérances de paramètres, notamment les profils utilisateur itinérant, UE-V et Enterprise State Roaming. Microsoft s’engage à investir davantage dans la solution Enterprise State Roaming pour les futures versions de Windows. Si votre organisation n’est pas prête à déplacer les données dans le cloud ou qu’elle ne se sent pas à l’aise avec cette idée, Microsoft vous recommande d’utiliser UE-V comme principale technologie d’itinérance. Si votre organisation a besoin d’une prise en charge de l’itinérance pour les applications de bureau Windows existantes, mais qu’elle est prête à migrer vers le cloud, Microsoft vous recommande d’utiliser à la fois Enterprise State Roaming et UE-V. Bien qu’UE-V et Enterprise State Roaming soient des technologies très similaires, elles ne sont pas incompatibles et aujourd’hui, elles se complètent pour garantir que votre organisation fournit les services d’itinérance dont vos utilisateurs ont besoin.

Lorsque vous utilisez à la fois Enterprise State Roaming et UE-V, les règles suivantes s’appliquent :

- Enterprise State Roaming est l’agent itinérant principal sur l’appareil. UE-V est utilisé pour compléter l’« intervalle Win32 ».
- L’itinérance UE-V pour les paramètres Windows et les données d’application UWP modernes doit être désactivée à l’aide des stratégies de groupe UE-V car ils sont déjà pris en charge par Enterprise State Roaming.

##Comment la solution Enterprise State Roaming prend-elle en charge Virtual Desktop Infrastructure (VDI) ?
Enterprise State Roaming est pris en charge uniquement sur les SKU client Windows 10, mais pas sur les éditions serveur. Si une machine virtuelle cliente est hébergée sur un hyperviseur alors qu’un utilisateur se connecte à distance à la machine virtuelle, les données de l’utilisateur seront alors en mode itinérant. Si plusieurs utilisateurs partagent le même système d’exploitation et que les utilisateurs se connectent à distance à un serveur pour bénéficier d’une expérience de bureau complète, l’itinérance peut fonctionner ou non. Ce dernier scénario n’est pas officiellement pris en charge.


## Que se passe-t-il si mon organisation achète Azure RMS après avoir utilisé l’itinérance pendant un certain temps ?
Si votre organisation utilise déjà l’itinérance dans Windows 10 avec l’abonnement gratuit à Azure RMS en utilisation limitée, acheter un abonnement payant à Azure RMS n’aura aucun impact sur les fonctionnalités de l’itinérance, et votre administrateur informatique n’aura pas besoin de modifier la configuration.

## Problèmes connus

- Se connecter à Windows avec une carte à puce physique ou virtuelle provoque l’arrêt de la synchronisation des paramètres. Si vous essayez de vous connecter à votre appareil à l’aide d’une carte à puce physique ou virtuelle, la synchronisation cessera de fonctionner. Les mises à jour futures de Windows 10 résoudront peut-être ce problème.
- La synchronisation des favoris Internet Explorer ne fonctionne pas pour les versions plus anciennes de Windows 10. Vous devez installer la mise à jour cumulative de juillet pour Windows 10 (build 10586.494 ou version ultérieure) pour bénéficier de la synchronisation des favoris Internet Explorer.
- Il peut arriver qu’Enterprise State Roaming ne parvienne pas à synchroniser les données si l’authentification multifacteur (MFA) est configurée.
    - Si l’utilisateur est configuré pour utiliser [Azure MFA](multi-factor-authentication.md) sur le portail Azure AD, l’utilisateur risque de ne pas réussir à synchroniser les paramètres lors d’une connexion à un appareil Windows 10 à l’aide d’un mot de passe. Ce type de configuration de l’authentification multifacteur a pour but de protéger un compte d’administrateur Microsoft Azure. Les utilisateurs administrateurs peuvent cependant continuer de synchroniser les paramètres en se connectant à leurs appareils Windows 10 à l’aide du code confidentiel [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) ou, éventuellement, en effectuant une authentification multifacteur lors de l’accès à d’autres services Azure comme Office 365.
    - La synchronisation peut échouer si l’administrateur configure la stratégie d’accès conditionnel MFA AD FS et si le jeton d’accès de l’appareil arrive à expiration. Veillez à vous déconnecter et à vous connecter en utilisant le code confidentiel [Microsoft Passport for Work](active-directory-azureadjoin-passport.md) ou à effectuer une authentification multifacteur lorsque vous accédez à d’autres services Azure comme Office 365.
   
- Si un ordinateur est joint au domaine avec l’inscription automatique aux appareils Azure AD, la synchronisation peut échouer si l’ordinateur est hors site pendant de longues périodes et que l’authentification de domaine ne peut pas s’exécuter. Pour résoudre ce problème, connectez l’ordinateur à un réseau d’entreprise afin que la synchronisation puisse reprendre.


## Rubriques connexes
- [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Activer Enterprise State Roaming dans Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0803_2016-->