<properties 
pageTitle="Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services"
	description="Configuration de l’application de service cloud Azure pour autoriser les connexions Bureau à distance"
	services="cloud-services"
	documentationCenter=""
	authors="sbtron"
	manager="timlt"
	editor=""/>
<tags 
ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="saurabh"/>

# Activer une connexion Bureau à distance pour un rôle dans Azure Cloud Services avec PowerShell

>[AZURE.SELECTOR]
- [Azure Portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


Le Bureau à distance vous permet d'accéder au bureau d'un rôle en cours d'exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour dépanner et diagnostiquer les problèmes rencontrés par votre application lorsqu'elle est en cours d'exécution.

Cet article décrit comment activer Bureau à distance sur vos rôles Service Cloud à l’aide de PowerShell. Voir [Installer et configurer Azure PowerShell Azure](powershell-install-configure.md) pour connaître les conditions requises pour cet article. PowerShell utilise l’extension Bureau à distance, ce qui vous permet d’activer le Bureau à distance même après avoir déployé l’application.


## Configurer le Bureau à distance à partir de PowerShell

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) permet d’activer le Bureau à distance sur des rôles spécifiés ou sur tous les rôles de déploiement de votre service cloud. L’applet de commande vous permet de spécifier le nom d’utilisateur et le mot de passe de l’utilisateur de bureau à distance par le biais du paramètre *informations d’identification* qui accepte un objet PSCredential.

Si vous utilisez PowerShell de manière interactive, vous pouvez facilement définir l’objet PSCredential en appelant l’applet de commande [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx).

```
	$remoteusercredentials = Get-Credential
```

Cette opération permettra d’afficher une boîte de dialogue vous permettant de saisir le nom d’utilisateur et un mot de passe utilisateur distant de manière sécurisée.

Dans la mesure où PowerShell sera principalement utilisé dans les scénarios d’automatisation, vous pouvez également configurer l’objet PSCredential d’une façon qui ne nécessite pas l’interaction utilisateur. Pour ce faire, tout d’abord vous devez configurer un mot de passe sécurisé. Commencez par spécifier un mot de passe en texte brut et convertissez-le en chaîne sécurisée à l’aide [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Vous devez ensuite convertir cette chaîne sécurisée en chaîne standard chiffrée avec [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Vous pouvez maintenant enregistrer cette chaîne chiffrée standard dans un fichier [Set-Content](https://technet.microsoft.com/library/ee176959.aspx). Lors de la création de l’objet PSCredential, vous pouvez lire ce fichier pour définir le mot de passe de manière sécurisée, sans avoir à spécifier le mot de passe sur une invite ou à stocker le mot de passe en texte brut.

Utilisez l’opération PowerShell suivante pour créer un fichier de mot de passe sécurisé :

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

Une fois le fichier de mot de passe (password.txt) créé, vous n’aurez qu’à utiliser ce fichier et n’aurez qu’à spécifier le mot de passe en texte brut. Si vous devez mettre à jour le mot de passe, vous pouvez réexécuter le powershell ci-dessus en utilisant le nouveau mot de passe pour générer un nouveau fichier password.txt.

>[AZURE.IMPORTANT]Lors de la définition du mot de passe, assurez-vous que vous remplissez les [exigences de complexité](https://technet.microsoft.com/library/cc786468.aspx).

Pour créer l’objet d’informations d’identification à partir du fichier de mot de passe sécurisé, vous devez lire le contenu du fichier et le reconvertir en chaîne sécurisée à l’aide [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). En plus des informations d’identification de l’applet de commande [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) accepte également un paramètre *Expiration* qui spécifie une valeur DateTime à laquelle le compte d’utilisateur expire. Vous le définissez en spécifiant une date et une heure statique ou vous pouvez simplement choisir de définir une date d’expiration du compte quelques jours après la date réelle.

Cet exemple PowerShell vous montre comment définir l’extension Bureau à distance sur un service cloud :

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
Vous pouvez également spécifier l’emplacement de déploiement et les rôles desquels vous souhaitez supprimer l’extension de bureau distant. Si ces paramètres ne sont pas spécifiés, l’applet de commande sera défini par défaut pour utiliser l’emplacement de production et permettre l’activation du bureau distant sur tous les rôles de l’environnement de production.

L’extension Bureau à distance est associée au déploiement. Si vous deviez créer un nouveau déploiement du service, vous devrez de nouveau activer le Bureau à distance sur le déploiement. Si vous souhaitez toujours que le Bureau à distance soit activé sur vos déploiements, vous devez envisager d’intégrer les scripts PowerShell pour activer le Bureau à distance dans votre flux de travail de déploiement.


## Bureau à distance dans une instance de rôle
L’applet de commande [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) peut être utilisé pour les services bureau à distance dans une instance de rôle spécifique de votre service cloud. Vous pouvez utiliser le paramètre *LocalPath* sur l’applet de commande pour télécharger le fichier RDP localement ou vous pouvez utiliser le paramètre *Lauch* pour lancer directement la boîte de dialogue Connexion Bureau à distance pour accéder à l’instance du rôle de service cloud.

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## Vérifiez si l’extension des services de Bureau à distance est activée sur un service 
L’applet de commande [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) montre si le Bureau à distance est activé sur un déploiement de service. L’applet de commande renvoie le nom d’utilisateur de l’utilisateur de bureau à distance et les rôles sur lesquels l’extension de bureau distant est activée. Vous pouvez également spécifier un emplacement de déploiement, la valeur par défaut étant la production.

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## Supprimer l’extension de bureau distant d’un service 
Si vous avez déjà activé l’extension du bureau à distance sur un déploiement et si vous devez mettre à jour les paramètres du bureau distant, vous devez tout d’abord supprimer l’extension du bureau à distance, puis le réactiver avec les nouveaux paramètres. Par exemple, si vous souhaitez définir un nouveau mot de passe du compte d’utilisateur distant ou si le compte d’utilisateur a expiré, vous devrez supprimer l’extension et l’ajouter à nouveau avec le nouveau mot de passe ou l’expiration. Cette opération est obligatoire uniquement sur les déploiements existants dont l’extension de bureau à distance est activée. Pour les nouveaux déploiements, vous pouvez simplement appeler pour appliquer directement l’extension.

Pour supprimer l’extension de bureau à distance à partir du déploiement de service s, vous pouvez utiliser l’applet de commande [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx). Vous pouvez également spécifier l’emplacement de déploiement et le rôle duquel vous souhaitez supprimer l’extension de bureau distant.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE]Le paramètre *UninstallConfiguration* va désinstaller toute configuration d’extension appliquée au service. Toutes les configurations d’extension sont associées à la configuration de service pour activer l’extension avec un déploiement. Le déploiement doit être associé à cette configuration d’extension. L’appel de l’applet de commande Remove sans *UninstallConfiguration* dissocie le déploiement de la configuration d’extension, supprimant de manière effective l’extension du déploiement. Cependant, la configuration d’extension reste associée au service. Pour supprimer complètement la configuration d’extension, vous devez appeler l’applet de commande Remove avec le paramètre *UninstallConfiguration*.



## Ressources supplémentaires

[Configuration des services cloud](cloud-services-how-to-configure.md)

<!---HONumber=September15_HO1-->