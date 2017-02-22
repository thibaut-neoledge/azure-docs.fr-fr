---
title: "Mise à niveau de l’agent PhoneFactor vers le serveur Azure Multi-Factor Authentication | Microsoft Docs"
description: "Ce document décrit comment prendre en main le serveur Azure Multi-Factor Authentication lors d’une mise à niveau à partir de l’ancien agent PhoneFactor."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4547a805c1827a703bf0ef118387882e45c3f241
ms.openlocfilehash: 5bbd7088f5d4986a5b1c9f5695a5defb678307a9


---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Mise à niveau de l’agent PhoneFactor vers le serveur Azure Multi-Factor Authentication
Pour mettre à niveau la version 5.x de l’agent PhoneFactor ou une version antérieure vers le serveur Azure Multi-Factor Authentication, commencez par désinstaller l’agent PhoneFactor et les composants correspondants. Vous pouvez ensuite installer le serveur Azure Multi-Factor Authentication et les composants associés.

## <a name="uninstall-the-phonefactor-agent"></a>Désinstallation de l’agent PhoneFactor

1. Tout d'abord, sauvegardez le fichier de données PhoneFactor. L'emplacement d'installation par défaut est C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.

2. Si le portail de l'utilisateur est installé :
  1. Accédez au dossier d'installation et sauvegardez le fichier web.config. L'emplacement d'installation par défaut est C:\inetpub\wwwroot\PhoneFactor.

  2. Si vous avez ajouté des thèmes personnalisés au portail, sauvegardez votre dossier personnalisé sous le répertoire C:\inetpub\wwwroot\PhoneFactor\App_Themes.

  3. Désinstallez le portail de l'utilisateur via l’agent PhoneFactor (disponible uniquement s’il est installé sur le même serveur que l’agent PhoneFactor) ou les programmes et fonctionnalités Windows.

3. Si le service web de l’application mobile est installé :

  1. Accédez au dossier d’installation et sauvegardez le fichier web.config. L’emplacement d’installation par défaut est C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

  2. Désinstaller le service web de l’application mobile via les programmes et fonctionnalités Windows.

4. Si le Kit de développement logiciel (SDK) Web Service est installé, désinstallez-le via l’agent PhoneFactor ou les programmes et fonctionnalités Windows.

5. Désinstallez l’agent PhoneFactor via les programmes et fonctionnalités Windows.

## <a name="install-the-multi-factor-authentication-server"></a>Installation du serveur Azure Multi-Factor Authentication

Récupérez le chemin d’installation à partir du Registre de l’installation précédente. L’agent PhoneFactor doit donc être installé dans le même emplacement (par exemple, C:\Program Files\PhoneFactor). Les nouvelles installations ont un chemin d'accès par défaut différent (par exemple, C:\Program Files\Multi-Factor Authentication Server). Le fichier de données de l’agent PhoneFactor doit être mis à niveau lors de l'installation pour que vos utilisateurs et les paramètres puissent toujours être présents après l’installation du nouveau serveur Multi-Factor Authentication.

1. Si vous y êtes invité, activez le serveur Multi-Factor Authentication et assurez-vous qu'il est affecté au groupe de réplication approprié.

2. Si le Kit de développement logiciel (SDK) Web Service a été précédemment installé, installez le nouveau Kit de développement logiciel (SDK) Web Service via l'Interface utilisateur du serveur Multi-Factor Authentication.

  Le nom de répertoire virtuel par défaut est désormais « MultiFactorAuthWebServiceSdk » au lieu de « PhoneFactorWebServiceSdk ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Sinon, si vous autorisez le programme d'installation à utiliser le nouveau nom par défaut, vous devez modifier l'URL dans toutes les applications qui référencent le Kit de développement logiciel (SDK) Web Service, tels que le portail de l'utilisateur et le service web de l'application mobile pour pointer vers le bon emplacement.

3. Si le portail de l'utilisateur a été précédemment installé sur le serveur de l'agent PhoneFactor, installez le nouveau portail utilisateur Multi-Factor Authentication via l'Interface Multi-Factor Authentication.

  Le nom de répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PhoneFactor ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Sinon, si vous autorisez le programme d'installation à utiliser le nouveau nom par défaut, vous cliquez sur l'icône Portail de l'utilisateur sur le serveur Multi-Factor Authentication et mettez à jour l'URL du portail utilisateur sous l'onglet Paramètres.

4. Si le Portail de l’utilisateur et/ou le service web de l’application mobile ont été installés précédemment sur un serveur différent de l’agent PhoneFactor :

  1. Accédez à l’emplacement d’installation (par exemple, C:\Program Files\PhoneFactor) et copiez les fichiers d’installation appropriés sur l’autre serveur. Il existe des programmes d'installation 32 bits et 64 bits pour le portail de l'utilisateur et le service web de l'application mobile. Ils sont respectivement appelés MultiFactorAuthenticationUserPortalSetupXX.msi et MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

  2. Pour installer le portail de l’utilisateur sur le serveur web, ouvrez une invite de commandes en tant qu’administrateur et exécutez MultiFactorAuthenticationUserPortalSetupXX.msi.

    Le nom de répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PhoneFactor ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Sinon, si vous autorisez le programme d'installation à utiliser le nouveau nom par défaut, vous cliquez sur l'icône Portail de l'utilisateur sur le serveur Multi-Factor Authentication et mettez à jour l'URL du portail utilisateur sous l'onglet Paramètres. Les utilisateurs existants doivent être informés de la nouvelle URL.

  3. Accédez à l’emplacement d’installation du Portail de l’utilisateur (par ex. C:\inetpub\wwwroot\MultiFactorAuth) et modifiez le fichier web.config. Copier les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d'origine qui a été sauvegardé avant la mise à niveau vers le nouveau fichier web.config. Si le nouveau nom de répertoire virtuel par défaut a été conservé lors de l'installation du Kit de développement logiciel (SDK) Web Service, modifiez l'URL dans la section applicationSettings pour pointer vers le bon emplacement. Si les valeurs par défaut ont été modifiées dans le précédent fichier web.config, appliquez les mêmes modifications au nouveau fichier web.config.

  4. Pour installer le service web de l’application mobile sur le serveur web, ouvrez une invite de commandes en tant qu’administrateur et exécutez MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

    Le nom de répertoire virtuel par défaut est désormais « MultiFactorAuthMobileAppWebService » au lieu de « PhoneFactorPhoneAppWebService ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Vous devriez probablement choisir un nom plus court pour faciliter la saisie des utilisateurs finaux sur leurs périphériques mobiles. Sinon, si vous autorisez le programme d’installation à utiliser le nouveau nom par défaut, vous cliquez sur l’icône Application mobile sur le serveur Multi-Factor Authentication et mettez à jour l’URL du service web de l’application mobile.

  5. Accédez à l’emplacement d’installation du service web (par ex. C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) et modifiez le fichier web.config. Copier les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d'origine qui a été sauvegardé avant la mise à niveau vers le nouveau fichier web.config. Si le nouveau nom de répertoire virtuel par défaut a été conservé lors de l'installation du Kit de développement logiciel (SDK) Web Service, modifiez l'URL dans la section applicationSettings pour pointer vers le bon emplacement. Si les valeurs par défaut ont été modifiées dans le précédent fichier web.config, appliquez les mêmes modifications au nouveau fichier web.config.

## <a name="next-steps"></a>Étapes suivantes

- [Installez le portail des utilisateurs](multi-factor-authentication-get-started-portal.md) du serveur Azure Multi-Factor Authentication.

- [Configurez l’authentification Windows](multi-factor-authentication-get-started-server-windows.md) pour vos applications. 



<!--HONumber=Feb17_HO1-->


