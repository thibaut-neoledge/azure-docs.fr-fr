<properties
   pageTitle="Notes de publication sur Azure Data Catalog"
   description="Notes de publication pour la version du 13 juillet 2015 d’Azure Data Catalog"
   services="data-catalog"
   documentationCenter=""
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Notes pour la version du 13 juillet 2015 d’Azure Data Catalog

## Inscription et connexion à Oracle Database

Lors de la connexion aux sources de données Oracle Database, les utilisateurs doivent avoir installé les pilotes Oracle appropriés qui correspondent au nombre de bits (32 ou 64) du logiciel utilisé.

-	Lors de l’inscription des sources de données Oracle sur un ordinateur exécutant Windows 32 bits, les pilotes Oracle 32 bits seront utilisés.
-	Lors de l’inscription des sources de données Oracle sur un ordinateur exécutant Windows 64 bits, les pilotes Oracle 64 bits seront utilisés.
-	Lors de la connexion aux sources de données Oracle à l'aide d'Excel sur un ordinateur exécutant la version 32 bits de Microsoft Office, y compris sur Windows 64 bits, les pilotes Oracle 32 bits seront utilisés.
-	Lors de la connexion aux sources de données Oracle à l'aide d'Excel sur un ordinateur exécutant la version 64 bits de Microsoft Office, les pilotes Oracle 64 bits seront utilisés.

## Inscription et connexion à SQL Server Reporting Services

La prise en charge des sources de données SQL Server Reporting Services (SSRS) dans la version préliminaire initiale d’Azure Data Catalog est limitée aux serveurs en mode natif. La prise en charge de SSRS en mode SharePoint sera disponible dans une version ultérieure.

## Ouverture des ressources de données dans Excel

Lors de l’ouverture des ressources de données dans Microsoft Excel à partir du portail Azure Data Catalog, les utilisateurs peuvent voir apparaître la boîte de dialogue **Avis de sécurité Microsoft Excel**. Il s’agit là d’un comportement attendu, et les utilisateurs peuvent sélectionner **Activer** pour continuer.

Pour plus d’informations, consultez l’article [Activer ou désactiver les alertes de sécurité relatives aux liens et aux fichiers de sites web suspects](https://support.office.com/fr-fr/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

## Colonnes BLOB et UDT manquantes dans les versions préliminaires

Lors de l’inscription des tables et des vues contenant des colonnes d’objets binaires volumineux (BLOB) et de types de données définies par l'utilisateur (UDT), et de l’installation d’une version préliminaire des ressources de données, ces colonnes ne seront pas incluses dans la version préliminaire.

## Configuration du proxy et de la stratégie, et inscription de la source de données

Les utilisateurs parviennent parfois à se connecter au portail d’Azure Data Catalog, mais lorsqu'ils tentent de se connecter à l'outil d’inscription de la source de données, un message d'erreur s’affiche et les empêche de se connecter.

Deux causes possibles à ce problème de comportement :

**Cause n°1 : configuration d’Active Directory Federation Services** L’outil d’inscription de la source de données utilise l’authentification par formulaire pour valider les connexions utilisateur avec Active Directory. Pour une ouverture de session réussie, l'authentification par formulaire doit être activée dans la stratégie d'authentification globale par un administrateur Active Directory.

Cette erreur peut également survenir lorsque l'utilisateur est connecté au réseau d'entreprise ou lorsque l'utilisateur se connecte en dehors du réseau d'entreprise. La stratégie d'authentification globale permet d’activer séparément des méthodes d'authentification pour les connexions intranet et extranet. Des erreurs de connexion peuvent survenir si l'authentification par formulaire n'est pas activée pour le réseau à partir duquel l'utilisateur se connecte.

Pour plus d’informations, consultez la page [Configuring intranet forms-based authentication for devices that do not support WIA](https://technet.microsoft.com/library/dn727110.aspx) (en anglais).

**Cause n°2 : configuration du proxy réseau** Si le réseau d’entreprise utilise un serveur proxy, l’outil d’inscription ne peut peut-être pas se connecter à Azure Active Directory via le proxy. Les utilisateurs peuvent s’assurer de l'outil d'inscription en modifiant le fichier de configuration de l'outil, et en ajoutant au fichier la section suivante :


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


Pour localiser le fichier RegistrationTool.exe.config, lancez l'outil d'inscription, puis ouvrez l'utilitaire Gestionnaire des tâches de Windows. Sous l'onglet Détails du Gestionnaire des tâches, cliquez avec le bouton droit sur RegistrationTool.exe et choisissez Ouvrir l'emplacement du fichier dans le menu contextuel.

<!---HONumber=July15_HO4-->