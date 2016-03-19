<properties
   pageTitle="Azure Automation et gestion de cloud hybride : automatisation du déploiement d’une machine virtuelle dans Amazon Web Services"
   description="Azure Automation et gestion de cloud hybride : automatisation du déploiement d’une machine virtuelle dans Amazon Web Services"
   services="automation"
   documentationCenter=""
   authors="tiander"
   manager="stevenka"
   editor="" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="tiandert; bwren" />

# Azure Automation et gestion de cloud hybride : automatisation du déploiement d’une machine virtuelle dans Amazon Web Services

Pendant les événements de la [Tournée Cloud](https://microsoftcloudroadshow.com/cities) où j’ai présenté Azure Automation, de nombreuses personnes m’ont demandé si Azure Automation avait été conçu uniquement pour Azure. Heureusement, j’étais en possession d’une démonstration dont je vais parler dans cet article de blog, mais la réponse est **qu’Azure Automation n’est pas uniquement réservé à Azure. Cette application est conçue aussi bien pour des ressources locales *QUE POUR* la gestion des environnements de cloud hybrides.** Par exemple, si vous êtes un client Amazon Web Services (AWS), Vous pouvez vous aussi utiliser Azure Automation !

## Objectifs

Dans cet article, je vais vous expliquer comment exploiter Azure Automation pour configurer une machine virtuelle dans AWS et lui attribuer un nom spécifique qui, dans l’environnement AWS, est appelé « identification » de la machine virtuelle.

## Hypothèses

Aux fins de cet article, il est supposé que le compte Azure Automation est configuré et que vous disposez d’un abonnement AWS.

Pour plus d’informations sur la configuration d’un compte Azure Automation, consultez notre [page de documentation](automation-configuring.md).

## Préparation : stockage de vos informations d’identification AWS

Pour qu’Azure Automation « communique » avec AWS, vous devez récupérer vos informations d’identification AWS et les stocker en tant que ressources dans Azure Automation.

Une fois que vous êtes connecté au portail AWS, cliquez sur le petit triangle figurant sous votre nom, cliquez sur **Informations d’identification de sécurité**.

![Informations d’identification de sécurité AWS](./media/automation-aws-deployment/73522ff9-30b5-431c-a3b6-5a33167827ab.png "Informations d’identification de sécurité AWS")

Cliquez sur **Clés d’accès**.

![Clés d’accès AWS](./media/automation-aws-deployment/215d76d0-6e7f-4f55-9128-ba618d4514be.jpg "Clés d’accès AWS")

Copiez votre clé d’accès et la clé d’accès secrète (vous pouvez éventuellement télécharger votre fichier de clé pour la stocker dans un endroit sûr).

![Créer des touches d’accès AWS](./media/automation-aws-deployment/81ca1d36-2814-478b-858f-8346f2a28211.png "Créer des touches d’accès AWS")

## Actifs AWS dans Azure Automation

À l’étape précédente, vous avez copié et enregistré votre **ID de clé d’accès** AWS et **Clé d’accès secrète**. Nous allons maintenant les stocker dans Azure Automation.

Accédez à votre compte Automation et sélectionnez **Actifs** - **Informations d’identification**, et ajoutez de nouvelles informations d’identification et nommez-le **AWScred**.

![image](./media/automation-aws-deployment/19c3669d-5259-4566-9479-5dbebb8ac733.png "image")

Fournissez une description facultative et assurez-vous que vous entrez votre **ID d’accès** dans le champ **Nom d’utilisateur** et votre **clé d’accès secrète** dans le champ **Mot de passe**. Enregistrez vos informations d’identification AWS.

## Module PowerShell Amazon Web Services

Votre runbook d’approvisionnement de machine virtuelle utilisera le module PowerShell AWS pour exécuter cette tâche. Le module PowerShell AWS fourni est sur la [Galerie PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) et peut facilement être ajouté à un compte Automation via le bouton **Déployer dans Azure Automation**.

![Importation du module PS AWS vers AA](./media/automation-aws-deployment/daa07d22-0464-4ec2-8c85-35525f95e5e4.png "Importation du module PS AWS vers AA")

Lorsque vous cliquez sur **Déployer vers Azure Automation**, vous êtes dirigé sur la page de connexion Azure et une fois que vous avez fourni vos informations d’identification, vous pouvez voir l’écran suivant.

![Assistant Importation de module de AWS](./media/automation-aws-deployment/575cd4d9-2ca5-4540-869d-3919f91294af.png "Assistant Importation de module de AWS")

Sélectionnez un compte Automation existant ou nouveau. Veuillez noter qu’il n’existe aucune zone de liste déroulante de compte existant. Assurez-vous que vous n’introduisez pas de faute de frappe et que la région et le groupe de ressources de votre compte Automation sont corrects. Passez en revue les autres étapes pour achever la configuration, puis cliquez sur **Créer**.

Si vous accédez au compte Automation sélectionné et accédez à **Actifs** - **Modules**, vous pouvez maintenant voir le module AWS avec les activités 1427. Génial !

>[AZURE.NOTE] L’importation d’un module Powershell dans Azure Automation se fait en deux étapes :
>
> 1.  Importation du module
> 2.  Extraction des applets de commande
>
>Les activités ne s’afficheront pas avant que le module ait complètement terminé l’importation et l’extraction des applets de commande, ce qui peut prendre quelques minutes.

## Approvisionnement de runbook de machine virtuelle AWS

Maintenant que toutes les conditions préalables sont réunies, il est temps de créer le runbook qui mettra en service un ordinateur virtuel dans AWS. Nous allons également faire une démonstration qui prouve qu’il est possible d’utiliser un script PowerShell natif dans Azure Automation au lieu d’un flux de travail PowerShell.

Vous trouverez d’autres informations sur les différences qui existent entre PowerShell natif et un flux de travail PowerShell [ici](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/). Vous trouverez des informations sur la création d’un runbook graphique [ici](https://azure.microsoft.com/blog/azure-automation-graphical-and-textual-runbook-authoring/).

Le script PowerShell pour notre Runbook peut être téléchargé à partir de la [Galerie PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).

Vous pouvez télécharger le script en ouvrant une session PowerShell et en entrant : **Save-Script-Nom New-AwsVM-< chemin >**

Une fois que vous avez enregistré le script PowerShell, vous pouvez l’ajouter à Azure Automation en tant que runbook en procédant comme suit :

Sous votre **Compte Automation**, cliquez sur **Runbooks**, puis sélectionnez **Ajouter un runbook**. Sélectionnez **Création rapide** (Créer un runbook), indiquez un nom pour votre runbook, puis, sous **Type de runbook**, sélectionnez **PowerShell**. Cliquez ensuite sur **Créer**.

![Création d’un runbook AWS](./media/automation-aws-deployment/4759ad00-f800-44ba-94be-307cf034a9df.png "Création d’un runbook AWS")

Lorsque l’éditeur de runbook s’ouvre, copiez et collez le script PowerShell dans la zone de création de runbook.

![image](./media/automation-aws-deployment/7e13addf-cae3-49a5-9d6c-28aa0b7263f4.png "image")

Notez les points suivants lorsque vous utilisez le script PowerShell téléchargé :

-   le runbook contient un certain nombre de valeurs de paramètre par défaut, comme indiqué à la section #ToDo. Veuillez évaluer toutes les valeurs par défaut et procéder aux mises à jour nécessaires.
-   Assurez-vous que vous avez stocké vos informations d’identification AWS sous **Actifs** et appelé **AWScred**.
-   La région que vous utiliserez dépendra de votre abonnement AWS. Dans le portail AWS dans lequel vous avez recherché vos informations d’identification de sécurité, cliquez sur la flèche en regard de votre compte pour vérifier votre région.

![Région AWS](./media/automation-aws-deployment/5a789953-7329-4f0e-9501-e2d3347a8730.png "Région AWS")

-   Déterminez [la région](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) que vous devez fournir dans votre runbook. Par exemple, ma région est l’Ouest des États-Unis (Oregon), ce qui se traduit en us-west-2.

![Régions AWS](./media/automation-aws-deployment/3ee8065d-0480-4c78-a2e3-6062653e9cb6.png "Régions AWS")

-   Vous pouvez obtenir une liste de noms d’images lorsque vous utilisez PowerShell ISE, importez le module, procédez à une authentification par rapport à AWS en remplaçant **Get-AutomationPSCredential** dans votre environnement ISE par **$AwsCred = Get-Credential.** Vous serez invité à saisir vos informations d’identification et vous pouvez fournir votre **ID de clé d’accès** et **clé d’accès secrète** en tant que nom d’utilisateur et mot de passe. Voir l’exemple ci-dessous :


		#Sample to get the AWS VM available images
		#Please provide the path where you have downloaded the AWS PowerShell module
		Import-Module AWSPowerShell
		$AWSRegion = "us-west-2"
		$AwsCred = Get-Credential
		$AwsAccessKeyId = $AwsCred.UserName
		$AwsSecretKey = $AwsCred.GetNetworkCredential().Password
		
		# Set up the environment to access AWS
		Set-AWSCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
		Set-DefaultAWSRegion -Region $AWSRegion
		
		Get-EC2ImageByName -ProfileName AWSProfile 


Vous obtiendrez cette sortie :

![Obtenir des images AWS](./media/automation-aws-deployment/1b1b0e9f-1af0-471c-9f5e-43f4bb29f4ed.png "Obtenir des images AWS")

Copiez et collez le nom d’image de votre choix dans une variable Automation, comme indiqué dans le runbook en tant que **$InstanceType**. Comme j’utilise un niveau AWS gratuit, j’ai sélectionné **t2.micro** dans mon runbook.

Pour en savoir plus sur les types d’instances Amazon EC2 [ici](https://aws.amazon.com/ec2/instance-types/).

## Test de l’approvisionnement de runbook de machine virtuelle AWS

Liste de vérifications préliminaires :

-   Les ressources permettant de s’authentifier auprès de AWS ont été créées et ont été nommées **AWScred**
-   Le module PowerShell AWS a été importé dans Azure Automation
-   Un runbook a été créé et les valeurs de paramètre ont été vérifiées et mises à jour lorsque c’était nécessaire
-   Les options **Journaliser les enregistrements détaillés** et éventuellement, **Journaliser les informations de progression** sous les paramètres de runbook ont été définis sur **Activé**

Commençons notre nouveau runbook en fournissant un nom **VMname.**

![Démarrer un runbook New-AwsVM](./media/automation-aws-deployment/31cd9d1d-208e-4e96-897f-05e37bf5ee7d.png "Démarrer un runbook New-AwsVM")

Comme nous avons activé les options **Journaliser les enregistrements détaillés** et **Journaliser les informations de progression** nous pouvons voir nos **flux de sortie** sous **Tous les journaux**.

![Sortie de flux](./media/automation-aws-deployment/77191c69-37fa-4598-82d8-cf5dbadfaff5.png "Sortie de flux")

Vérifions dans AWS :

![Machine virtuelle déployée par la console AWS](./media/automation-aws-deployment/c5a16bd4-6c54-40d6-9811-7d8e6c5ec74d.png "Machine virtuelle déployée par la console AWS")

Cool !

Dans cet article, j’ai expliqué comment Azure Automation peut être utilisé pour gérer des clouds hybrides, par exemple, pour la création d’une machine virtuelle dans Amazon Web Services et l’application d’une balise personnalisée lui attribuant un nom.

Heureuse automatisation et à la prochaine !

Tiander.

<!---HONumber=AcomDC_0204_2016-->