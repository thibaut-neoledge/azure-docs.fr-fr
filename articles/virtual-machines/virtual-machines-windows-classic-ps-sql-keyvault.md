<properties
	pageTitle="Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour SQL Server sur des machines virtuelles Azure (classiques)"
	description="Apprenez à automatiser la configuration du chiffrement de SQL Server pour une utilisation avec Azure Key Vault. Cette rubrique explique comment utiliser l’intégration de coffre de clés Azure avec des machines virtuelles SQL Server créées avec un modèle de déploiement classique."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/05/2016"
	ms.author="jroth"/>

# Configurer Azure Key Vault Integration (Intégration du coffre de clés Azure) pour SQL Server sur des machines virtuelles Azure (classiques)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-ps-sql-keyvault.md)
- [Classique](virtual-machines-windows-classic-ps-sql-keyvault.md)

## Vue d'ensemble
Il existe plusieurs fonctionnalités de chiffrement SQL Server, telles que [ le chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [le chiffrement au niveau des colonnes (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) et [le chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces types de chiffrement nécessitent que vous gériez et stockiez les clés de chiffrement que vous utilisez pour le chiffrement. Le service Azure Key Vault (coffre de clés Azure, AKV) est conçu pour optimiser la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le [connecteur SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server d’utiliser ces clés depuis le coffre de clés Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Si vous exécutez SQL Server sur des machines locales, vous devez [suivre la procédure d’accès au coffre de clés Azure à partir de votre machine SQL Server locale](https://msdn.microsoft.com/library/dn198405.aspx). Mais, pour SQL Server dans des machines virtuelles Azure, vous pouvez gagner du temps à l'aide de la fonctionnalité *Azure Key Vault Integration*. Avec quelques applets de commande Azure PowerShell pour activer cette fonctionnalité, vous pouvez automatiser la configuration nécessaire pour qu'une machine virtuelle SQL puisse accéder à votre coffre de clés.

Lorsque cette fonctionnalité est activée, elle installe automatiquement le connecteur SQL Server, configure le fournisseur EKM pour accéder à Azure Key Vault et crée les informations d'identification vous permettant d'accéder à votre coffre. Si vous avez examiné les étapes décrites dans la documentation locale mentionnée précédemment, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. La seule étape que vous devez encore exécuter manuellement consiste à créer le coffre de clé et des clés. À partir de là, la configuration complète de votre machine virtuelle SQL est automatisée. Une fois que cette fonctionnalité a terminé cette configuration, vous pouvez exécuter des instructions T-SQL pour crypter vos bases de données et vos sauvegardes comme vous y êtes habitué.

[AZURE.INCLUDE [Préparation pour l’intégration du coffre de clés Azure](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## Configuration d'AKV Integration
Utilisez PowerShell pour configurer Azure Key Vault Integration. Les sections suivantes fournissent une vue d'ensemble des paramètres requis, puis un exemple de script PowerShell.

### Paramètres d'entrée
Le tableau suivant répertorie les paramètres requis pour exécuter le script PowerShell dans la section suivante.

|Paramètre|Description|Exemple|
|---|---|---|
|**$akvURL**|**L'URL du coffre de clés**|« https://contosokeyvault.vault.azure.net/ »|
|**$spName**|**Nom du principal du service**|« fde2b411-33d5-4e11-af04eb07b669ccf2 »|
|**$spSecret**|**Secret du principal du service**|« 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= »|
|**$credName**|**Nom d’identification** : le module d’intégration du coffre de clés Azure crée des informations d’identification dans SQL Server, permettant ainsi à la machine virtuelle d’accéder au coffre de clés. Choisissez un nom pour cette identification.|« mycred1 »|
|**$vmName**|**Nom de machine virtuelle** : le nom d’une machine virtuelle SQL créée précédemment.|« myvmname »|
|**$serviceName**|**Nom du service** : le nom du service Cloud associé à la machine virtuelle SQL.|« mycloudservicename »|

### Activation d'AKV Integration avec PowerShell
L'applet de commande **New-AzureVMSqlServerKeyVaultCredentialConfig** crée un objet de configuration pour la fonctionnalité Azure Key Vault Integration. **Set-AzureVMSqlServerExtension** configure cette intégration avec le paramètre **KeyVaultCredentialSettings**. Les étapes suivantes indiquent comment utiliser ces commandes.

1. Dans Azure PowerShell, configurez tout d'abord les paramètres d'entrée avec des valeurs spécifiques comme décrit dans les sections précédentes de cette rubrique. Vous trouverez ci-dessous un exemple de script.

		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	Utilisez ensuite le script suivant pour configurer et activer AKV Integration.

		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

L'extension de l'Agent SQL IaaS met à jour la machine virtuelle SQL avec cette nouvelle configuration.

[AZURE.INCLUDE [Étapes suivantes de l’intégration du coffre de clés Azure](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0706_2016-->