<properties 
	pageTitle="Mise à niveau de l'agent PhoneFactor vers le serveur Azure Multi-Factor Authentication" 
	description="Ce document décrit comment démarrer avec le serveur Azure MFA et mettre à niveau à partir de l'ancien agent phonefactor." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Mise à niveau de l'agent PhoneFactor vers le serveur Azure Multi-Factor Authentication

La mise à niveau à partir de l’agent PhoneFactor v5.x ou version antérieure vers le serveur Azure Multi-Factor Authentication requiert l’agent PhoneFactor et les composants associés pour être désinstallé avant que le serveur Multi-Factor Authentication et ses composants associés puissent être installés.

## Mise à niveau de l'agent PhoneFactor vers le serveur Azure Multi-Factor Authentication
<ol>
<li>Tout d'abord, sauvegardez le fichier de données PhoneFactor. L'emplacement d'installation par défaut est C:\\Program Files\\PhoneFactor\\Data\\Phonefactor.pfdata.


<li>Si le portail de l'utilisateur est installé&#160;:</li>
<ol>
<li>Accédez au dossier d'installation et sauvegardez le fichier web.config. L'emplacement d'installation par défaut est C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Si vous avez ajouté des thèmes personnalisés au portail, sauvegardez votre dossier personnalisé sous le répertoire C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Désinstallez le portail de l'utilisateur via l’agent PhoneFactor (disponible uniquement s’il est installé sur le même serveur que l’agent PhoneFactor) ou les programmes et fonctionnalités Windows.</li></ol>




<li> Si le service web de l’application mobile est installé :
<ol>
<li>Accédez au dossier d’installation et sauvegardez le fichier web.config. L’emplacement d’installation par défaut est C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Désinstaller le service web de l’application mobile via les programmes et fonctionnalités Windows.</li></ol>

<li>Si le Kit de développement logiciel (SDK) Web Service est installé, désinstallez-le via l’agent PhoneFactor ou les programmes et fonctionnalités Windows.

<li>Désinstallez l’agent PhoneFactor via les programmes et fonctionnalités Windows.

<li>Installez le serveur Azure Multi-Factor Authentication Notez que le chemin d'installation est récupéré à partir du Registre de l'installation précédente de l’agent PhoneFactor. Il doit donc être installé dans le même emplacement (par exemple, C:\\Program Files\\PhoneFactor). Les nouvelles installations ont un chemin d'accès par défaut différent (par exemple, C:\\Program Files\\Multi-Factor Authentication Server). Le fichier de données de l’agent PhoneFactor doit être mis à niveau lors de l'installation pour que vos utilisateurs et les paramètres puissent toujours être présents après l’installation du nouveau serveur Multi-Factor Authentication.

<li>Si vous y êtes invité, activez le serveur Multi-Factor Authentication et assurez-vous qu'il est affecté au groupe de réplication approprié.

<li>Si le Kit de développement logiciel (SDK) Web Service a été précédemment installé, installez le nouveau Kit de développement logiciel (SDK) Web Service via l'Interface utilisateur du serveur Multi-Factor Authentication. Notez que le nom de répertoire virtuel par défaut est désormais « Multifactorauthwebservicesdk » au lieu de « Phonefactorwebservicesdk ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Sinon, si vous autorisez le programme d'installation à utiliser le nouveau nom par défaut, vous devez modifier l'URL dans toutes les applications qui référencent le Kit de développement logiciel (SDK) Web Service, tels que le portail de l'utilisateur et le service web de l'application mobile pour pointer vers le bon emplacement.

<li>Si le portail de l'utilisateur a été précédemment installé sur le serveur de l'agent PhoneFactor, installez le nouveau portail utilisateur Multi-Factor Authentication via l'Interface Multi-Factor Authentication. Notez que le nom de répertoire virtuel par défaut est désormais « MultiFactorAuth » au lieu de « PPhoneFactor ». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Sinon, si vous autorisez le programme d'installation à utiliser le nouveau nom par défaut, vous cliquez sur l'icône Portail de l'utilisateur sur le serveur Multi-Factor Authentication et mettez à jour l'URL du portail utilisateur sous l'onglet Paramètres.

<li>Si le Portail de l’utilisateur et/ou le service web de l’application mobile ont été installés précédemment sur un serveur différent de l’agent PhoneFactor :
<ol>
<li>Accédez à l’emplacement d’installation (par exemple, C:\Program Files\PhoneFactor) et copiez les fichiers d’installation appropriés sur l’autre serveur. Il existe des programmes d'installation 32&#160;bits et 64&#160;bits pour le portail de l'utilisateur et le service web de l'application mobile. Ils sont respectivement appelés MultiFactorAuthenticationUserPortalSetupXX.msi et MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.</li>
<li>Pour installer le Portail de l’utilisateur sur le serveur web, ouvrez une invite de commandes en tant qu’administrateur et exécutez MultiFactorAuthenticationUserPortalSetupXX.msi. Notez que le nom de répertoire virtuel par défaut est désormais «&#160;MultiFactorAuth&#160;» au lieu de «&#160;PPhoneFactor&#160;». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Sinon, si vous autorisez le programme d'installation à utiliser le nouveau nom par défaut, vous cliquez sur l'icône Portail de l'utilisateur sur le serveur Multi-Factor Authentication et mettez à jour l'URL du portail utilisateur sous l'onglet Paramètres. Les utilisateurs existants doivent être informés de la nouvelle URL.</li>
<li>Accédez à l’emplacement d’installation du Portail de l’utilisateur (par ex. C:\inetpub\wwwroot\MultiFactorAuth) et modifiez le fichier web.config. Copier les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d'origine qui a été sauvegardé avant la mise à niveau vers le nouveau fichier web.config. Si le nouveau nom de répertoire virtuel par défaut a été conservé lors de l'installation du Kit de développement logiciel (SDK) Web Service, modifiez l'URL dans la section applicationSettings pour pointer vers le bon emplacement. Si les valeurs par défaut ont été modifiées dans le précédent fichier web.config, appliquez les mêmes modifications au nouveau fichier web.config.</li>
<li>Pour installer le service web de l’application mobile sur le serveur web, ouvrez une invite de commandes en tant qu’administrateur et exécutez MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Notez que le nom de répertoire virtuel par défaut est désormais «&#160;MultiFactorAuthMobileAppWebService&#160;» au lieu de «&#160;PhoneFactorPhoneAppWebService&#160;». Si vous souhaitez utiliser le nom précédent, vous devez modifier le nom du répertoire virtuel pendant l'installation. Vous devriez probablement choisir un nom plus court pour faciliter la saisie des utilisateurs finaux sur leurs périphériques mobiles. Sinon, si vous autorisez le programme d’installation à utiliser le nouveau nom par défaut, vous cliquez sur l’icône Application mobile sur le serveur Multi-Factor Authentication et mettez à jour l’URL du service web de l’application mobile.</li>
<li>Accédez à l’emplacement d’installation du service web (par ex. C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) et modifiez le fichier web.config. Copier les valeurs dans les sections appSettings et applicationSettings à partir de votre fichier web.config d'origine qui a été sauvegardé avant la mise à niveau vers le nouveau fichier web.config. Si le nouveau nom de répertoire virtuel par défaut a été conservé lors de l'installation du Kit de développement logiciel (SDK) Web Service, modifiez l'URL dans la section applicationSettings pour pointer vers le bon emplacement. Si les valeurs par défaut ont été modifiées dans le précédent fichier web.config, appliquez les mêmes modifications au nouveau fichier web.config.</li></ol>


 


 

<!---HONumber=AcomDC_0810_2016-->