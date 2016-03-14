<properties
	pageTitle="Résolution des problèmes des diagnostics Azure"
	description="Résolution des problèmes lors de l'utilisation des diagnostics Azure dans Azure Cloud Services, Virtual Machines et"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Résolution des problèmes des diagnostics Azure
Informations de résolution des problèmes liés à l’utilisation des diagnostics Azure. Pour plus d’informations sur les diagnostics Azure, voir [Présentation des diagnostics Azure](azure-diagnostics.md#cloud-services).

## Azure Diagnostics ne démarre pas
Diagnostics comprend deux composants : un plug-in d’agent invité et l’agent de surveillance.

Dans un rôle Cloud Service, les fichiers journaux du plug-in d'agent invité sont situés dans le fichier :

	*%SystemDrive%\ WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>*\CommandExecution.log

Dans une machine virtuelle Azure, les fichiers journaux du plug-in d'agent invité sont situés dans le fichier :

		C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\CommandExecution.log

Les codes d'erreur suivants sont renvoyés par le plug-in :

Code de sortie|Description
---|---
0|Vous avez réussi !
-1|Erreur générique.
-2|Impossible de charger le fichier rcf.<p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle.
-3|Impossible de charger le fichier de configuration Diagnostics. <p><p>Solution : cette erreur se produit quand un fichier de configuration ne réussit pas la validation de schéma. La solution consiste à fournir un fichier de configuration qui est conforme au schéma.
-4|Une autre instance de l'agent de surveillance Diagnostics utilise déjà le répertoire de ressources local. <p><p>Solution : indiquez une autre valeur pour **LocalResourceDirectory**.
-6|Le lanceur du plug-in d'agent invité a tenté de démarrer Diagnostics avec une ligne de commande non valide.<p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement, de manière incorrecte, sur la machine virtuelle.
-10|Le plug-in Diagnostics s'est terminé avec une exception non prise en charge.
-11|L'agent invité n'a pas pu créer le processus responsable du lancement et de la surveillance de l'agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.<p>
-101|Arguments non valides lors de l’appel du plug-in Diagnostics. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle.
-102|Le processus du plug-in ne peut pas démarrer tout seul.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.
-103|Le processus du plug-in n'arrive pas à s'initialiser. Plus précisément, il n’est pas en mesure de créer l’objet de journalisation. <p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.
-104|Impossible de charger le fichier rcf fourni par l’agent invité. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le lanceur du plug-in d'agent invité est appelé manuellement et de manière incorrecte sur la machine virtuelle.
-105|Le plug-in Diagnostics ne parvient pas à ouvrir le fichier de configuration Diagnostics. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le plug-in Diagnostics est appelé manuellement et de manière incorrecte sur la machine virtuelle.
-106|Impossible de lire le fichier de configuration Diagnostics. <p><p>Solution : cette erreur se produit quand un fichier de configuration ne réussit pas la validation de schéma. La solution consiste alors à fournir un fichier de configuration qui est conforme au schéma. Le XML qui est fourni à l'extension Diagnostics est présent dans le dossier *%SystemDrive%\\WindowsAzure\\Config* sur la machine virtuelle. Ouvrez le fichier XML approprié et recherchez **Microsoft.Azure.Diagnostics**, puis le champ **xmlCfg**. Les données sont encodées en base64. Vous devrez donc [les décoder](http://www.bing.com/search?q=base64+decoder) pour afficher le XML qui a été chargé par Diagnostics.<p>
-107|La transmission du répertoire de ressources à l’agent de surveillance n’est pas valide. <p><p>Il s'agit d'une erreur interne qui ne peut se produire que si l'agent de surveillance est appelé manuellement et de manière incorrecte sur la machine virtuelle.</p>
-108 |Impossible de convertir le fichier de configuration Diagnostics dans le fichier de configuration de l'agent de surveillance.<p><p>Il s'agit d'une erreur interne qui doit se produire uniquement si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide.
-110|Erreur de configuration générale de Diagnostics.<p><p>Il s'agit d'une erreur interne qui ne peut se produire que si le plug-in Diagnostics est appelé manuellement avec un fichier de configuration non valide.
-111|Impossible de démarrer l’agent de surveillance.<p><p>Solution : vérifiez que les ressources système disponibles sont suffisantes pour lancer de nouveaux processus.
-112|Erreur générale


## Les données des diagnostics ne sont pas consignées dans Azure Storage
Les diagnostics Azure stockent toutes les données dans Azure Storage.

La principale cause de données d'événement manquantes est une mauvaise définition des informations du compte de stockage.

Solution : corrigez votre fichier de configuration Diagnostics et réinstallez Diagnostics. Si le problème persiste après avoir réinstallé l'extension de diagnostic, il pourra être nécessaire de pousser plus loin le débogage en parcourant toutes les erreurs de l'agent de surveillance. Avant que les données d'événement ne soient téléchargées sur votre compte de stockage, elles sont stockées dans LocalResourceDirectory.

Pour le rôle Service Cloud, LocalResourceDirectory est :

	C:\Resources\Directory<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Pour Virtual Machines, LocalResourceDirectory est :

	C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Si le dossier LocalResourceDirectory ne contient aucun fichier, l'agent de surveillance ne peut être lancé. Cette erreur est généralement causée par un fichier de configuration non valide. Un tel événement doit être signalé dans CommandExecution.log.

Si l'agent de surveillance collecte avec succès des données d'événement, vous verrez les fichiers .tsf pour chaque événement défini dans votre fichier de configuration. L'agent de surveillance consigne ses erreurs dans le fichier MaEventTable.tsf. Pour inspecter le contenu de ce fichier, vous pouvez utiliser l'application tabel2csv pour convertir le fichier .tsf en un fichier de valeurs séparées par des virgules (.csv) :

Sur un rôle Cloud Service :

	%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Le *%SystemDrive%* sur un rôle Cloud Service est généralement D :

Sur une machine virtuelle :

	C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Les commandes ci-dessus génèrent le fichier journal *maeventtable.csv*, que vous pouvez ouvrir et inspecter pour consulter les messages d'échec.


## Tables des données de diagnostic introuvables
Les tables d’Azure Storage qui contiennent les données des diagnostics Azure sont nommées en utilisant le code ci-dessous :

		if (String.IsNullOrEmpty(eventDestination)) {
		    if (e == "DefaultEvents")
		        tableName = "WADDefault" + MD5(provider);
		    else
		        tableName = "WADEvent" + MD5(provider) + eventId;
		}
		else
		    tableName = "WAD" + eventDestination;

Voici un exemple :

		<EtwEventSourceProviderConfiguration provider=”prov1”>
		  <Event id=”1” />
		  <Event id=”2” eventDestination=”dest1” />
		  <DefaultEvents />
		</EtwEventSourceProviderConfiguration>
		<EtwEventSourceProviderConfiguration provider=”prov2”>
		  <DefaultEvents eventDestination=”dest2” />
		</EtwEventSourceProviderConfiguration>

Cela générera 4 tables :

Événement|Nom de la table
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

<!---HONumber=AcomDC_0302_2016-->