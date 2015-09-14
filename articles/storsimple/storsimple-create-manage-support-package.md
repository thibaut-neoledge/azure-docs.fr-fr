<properties 
   pageTitle="Création d'un package de support StorSimple | Microsoft Azure"
	description="Apprenez à créer, déchiffrer et modifier un package de support pour votre appareil StorSimple."
	services="storsimple"
	documentationCenter=""
	authors="alkohli"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/31/2015"
	ms.author="alkohli"/>


# Création et gestion d’un package de prise en charge StorSimple

## Vue d’ensemble

Ce didacticiel décrit les différentes tâches associées à la création et la gestion d'un package de support. Un package de support inclut tous les journaux pertinents dans un format compressé et chiffré. Il est utilisé pour aider l'équipe de support technique de Microsoft à résoudre les problèmes d'appareil StorSimple.

Ce didacticiel inclut des instructions détaillées pour créer et gérer le package de support à l'aide des éléments suivants :

- Section **Package de support** de la page **Maintenance** dans le service StorSimple Manager
- Windows PowerShell pour StorSimple

Après avoir lu ce didacticiel, vous pourrez :

- créer un package de support ;
- déchiffrer et modifier un package de support.


## Création d'un package de support dans le portail de gestion

Pour résoudre les problèmes que vous pourriez rencontrer avec le service StorSimple Manager, vous pouvez créer et télécharger un package de support sur le site de support technique de Microsoft à l'aide de la page **Maintenance** du service dans le portail de gestion. Vous devrez fournir une clé de sécurité de support pour autoriser le téléchargement. La clé de sécurité de support doit vous être fournie par un ingénieur du support technique dans un message électronique. Un package de support non crypté et compressé est créé (fichier .cab). Ce package peut ensuite être récupéré par l'ingénieur du support technique à partir du site de support lorsqu'il vous fournit la clé de sécurité.

Procédez comme suit dans le portail de gestion pour créer un package de support :

#### Pour créer un package de support dans le portail de gestion

1. Accédez à **Appareils > Maintenance**.

2. Dans la section **Package de support**, cliquez sur **Créer et télécharger le package de support**.

3. Dans la boîte de dialogue **Créer et télécharger le package de support**, procédez comme suit :

	![Création d'un package de support](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- Indiquez la **clé de sécurité de support**. Cette clé doit vous être envoyée par un ingénieur du support de Microsoft dans un message électronique.
 	
	- Consultez la zone de liste modifiable pour accepter de **télécharger automatiquement le package de support sur le site de support de Microsoft**.
 	
	- Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-create-manage-support-package/IC740895.png).


## Création d'un package de support dans Windows PowerShell pour StorSimple

Si vous devez modifier vos fichiers journaux avant de créer un package, vous devrez créer votre package à l'aide de Windows PowerShell pour StorSimple.

Procédez comme suit pour créer un package de support dans Windows PowerShell pour StorSimple :


#### Pour créer un package de support dans Windows PowerShell pour StorSimple

1. Tapez la commande suivante pour démarrer une session Windows PowerShell en tant qu'administrateur sur l'ordinateur distant utilisé pour la connexion à votre appareil StorSimple :

	`Start PowerShell`

2. Dans la session Windows PowerShell, connectez-vous à l'instance d'exécution de la console SSAdmin votre appareil :


	-  À l’invite de commandes, tapez : 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. Dans la boîte de dialogue qui s'affiche, tapez votre mot de passe administrateur. Le mot de passe par défaut est :
	 
		`Password1`

		![Session PowerShell sur l'instance d'exécution SSAdminConsole](./media/storsimple-create-manage-support-package/IC740962.png)

	2. Cliquez sur **OK**.
	1.  À l’invite de commandes, tapez : 
		
		`Enter-PSSession $MS`


3. Dans la session qui s'ouvre, tapez la commande appropriée.


	- Pour les partages réseau protégés par un mot de passe, entrez :

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		Vous serez invité à entrer un mot de passe, le chemin d'accès au dossier réseau partagé et une phrase secrète de chiffrement (car le package de support est chiffré). Une fois ces éléments fournis, un package de support sera créé dans le dossier spécifié.
											

	- Pour les dossiers réseau partagés ouverts (ceux qui ne sont pas protégés par un mot de passe), vous n'avez pas besoin du paramètre `-Credential`. Entrez ce qui suit :

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		Le package de support sera créé pour les deux contrôleurs dans le dossier réseau partagé spécifié. Il s'agit d'un fichier compressé et chiffré qui peut être envoyé au support technique de Microsoft à des fins de dépannage. Pour plus d'informations, consultez [Contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md).


### Informations supplémentaires sur l'applet de commande Export-HcsSupportPackage
Les différents paramètres qui peuvent être utilisés avec l'applet de commande Export-HcsSupportPackage sont indiqués ci-dessous.

| S. N° | Paramètre | Obligatoire ou facultatif | Description |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Path | Requis | Permet d'indiquer l'emplacement du dossier réseau partagé dans lequel le package de support sera placé. |
| 2 | EncryptionPassphrase | Requis | Permet de fournir une phrase secrète permettant de chiffrer le package de support. |
| 3 | Informations d'identification | Facultatif | Permet de fournir des informations d'identification d'accès pour le dossier réseau partagé. |
| 4 | Force | Facultatif | Permet d'ignorer l'étape de confirmation de la phrase secrète de chiffrement. |
| 5 | PackageTag | Facultatif | Permet de spécifier un répertoire sous Chemin d'accès dans lequel le package de support sera placé. La valeur par défaut est [nom de l'appareil]-[date et heure actuelles : aaaa-MM-jj-HH-mm-ss]. |
| 6 | Étendue | Facultatif | Définir sur **Cluster** (valeur par défaut) pour créer un package de support pour les deux contrôleurs. Si vous souhaitez créer un package uniquement pour le contrôleur actuel, spécifiez **Contrôleur**. |


## Modification d'un package de support

Une fois que vous avez généré un package de support, vous devrez peut-être le modifier pour supprimer les informations spécifiques au client, comme les noms de volumes, les adresses IP d'appareils et les noms des sauvegardes des fichiers journaux.

> [AZURE.IMPORTANT]Vous pouvez uniquement modifier un package de support qui a été généré à l'aide de Windows PowerShell pour StorSimple. Vous ne pouvez pas modifier un package créé dans le portail de gestion avec le service StorSimple Manager.

Pour modifier un package de support avant de le télécharger sur le site de support technique de Microsoft, vous devez déchiffrer le package de support, modifier les fichiers et le chiffrer de nouveau. Procédez comme suit pour modifier un package de support :

#### Pour modifier un package de support dans Windows PowerShell pour StorSimple

1. Générez un package de support, comme décrit à la page [Création d'un package de support dans Windows PowerShell pour StorSimple](#create-a-support-package-in-windows-powershell-for-storsimple).

2. [Téléchargez le script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur votre ordinateur client.

3. Importez le module Windows PowerShell. Vous devrez spécifier le chemin d'accès au dossier local dans lequel vous avez téléchargé le script. Pour importer le module, entrez :
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Ouvrez le dossier du package de support. Notez que tous les fichiers sont des fichiers *.aes* compressés et chiffrés. Ouvrez les fichiers. Pour ouvrir les fichiers, entrez :

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	Ceci permet de décompresser et de déchiffrer les fichiers. Notez que les extensions sont maintenant affichées pour tous les fichiers.
	
	![Modifier le package de support 3](./media/storsimple-create-manage-support-package/IC750706.png)


5. Lorsque vous êtes invité à entrer la phrase secrète de chiffrement, tapez la phrase secrète utilisée lors de la création du package de support.

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	
6. Accédez au dossier qui contient les fichiers journaux. Étant donné que les fichiers journaux sont désormais décompressés et déchiffrés, leurs extensions d'origine sont affichées. Modifiez ces fichiers pour supprimer toutes les informations spécifiques au client, comme les noms de volumes et les adresses IP d'appareils, puis enregistrez les fichiers.

7. Fermez les fichiers. Lors de leur fermeture, les fichiers sont compressés au format Gzip et chiffrés avec AES-256. Cette opération est exécutée à des fins de sécurité et de rapidité lors du transfert du package de support sur un réseau. Pour fermer les fichiers, entrez :

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![Modifier le package de support 2](./media/storsimple-create-manage-support-package/IC750707.png)

8. Lorsque vous y êtes invité, fournissez une phrase secrète de chiffrement pour le package de support modifié.

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

9. Notez la nouvelle phrase secrète afin de pouvoir la partager avec le support technique de Microsoft si nécessaire.


### Exemple : Modification de fichiers dans un package de support sur un partage protégé par mot de passe

Vous trouverez ci-dessous un exemple illustrant comment déchiffrer, modifier et re-chiffrer un package de support.

![Modifier le package de support 1](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## Étapes suivantes

Découvrez comment [utiliser les packages de support et les journaux de l'appareil pour dépanner votre déploiement](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

<!---HONumber=September15_HO1-->