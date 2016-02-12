<properties
	pageTitle="Activation du débogage distant avec la remise continue | Microsoft Azure"
	description="Découvrez comment activer le débogage distant lors de l'utilisation de la remise continue pour publier sur Azure."
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Activation du débogage distant lors de l'utilisation de la remise continue pour publier sur Azure

En procédant comme suit, vous pouvez activer le débogage distant dans Azure, pour des services cloud ou des machines virtuelles, quand vous utilisez la [remise continue](cloud-services-dotnet-continuous-delivery.md) afin de publier sur Azure.

## Activation du débogage distant pour les services cloud

1. Dans l'agent de build, configurez l'environnement de départ pour Azure comme expliqué à la page [Génération en mode ligne de commande pour Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. L’exécution du débogage distant (msvsmon.exe) étant nécessaire pour ce package, installez les [Outils de contrôle à distance pour Visual Studio 2015](http://www.microsoft.com/fr-FR/download/details.aspx?id=48155) (ou les [Outils de contrôle à distance pour Microsoft Visual Studio 2013 Update 5](https://www.microsoft.com/fr-FR/download/details.aspx?id=48156) si vous utilisez Visual Studio 2013). Une autre méthode consiste à copier les fichiers binaires de débogage distant à partir d'un système sur lequel Visual Studio est installé.
3. Créez un certificat comme expliqué dans [Vue d’ensemble des certificats pour Azure Cloud Services](cloud-services-certs-create.md). Conservez l'empreinte numérique de certificat .pfx et RDP et téléchargez le certificat sur le service cloud cible.
4. Utilisez les options suivantes dans la ligne de commande MSBuild pour générer et créer des packages en activant le débogage distant. (Remplacez les chemins d'accès réels de votre système et de vos fichiers de projet pour les éléments entre crochets.)

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` est le chemin d’accès au dossier contenant msvsmon.exe dans les Outils de contrôle à distance de Visual Studio.

5. Publiez sur le service cloud cible en utilisant le package et le fichier .cscfg générés à l'étape précédente.
6. Importez le certificat (fichier .pfx) sur l'ordinateur sur lequel Visual Studio et le Kit de développement logiciel (SDK) Azure SDK pour .NET sont installés.

## Activation du débogage distant pour les machines virtuelles

1. Créez une machine virtuelle Azure. Consultez [Création d’une machine virtuelle exécutant Windows Server](/virtual-machines/virtual-machines-windows-tutorial.md) ou [Créer et gérer des machines virtuelles Azure dans Visual Studio](/vs-azure-tools-virtual-machines-create-manage.md).
2. Sur la [page du portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=269851), affichez le tableau de bord de la machine virtuelle pour voir l’**EMPREINTE NUMÉRIQUE DE CERTIFICAT RDP** de la machine virtuelle. Cette valeur est utilisée pour la valeur `ServerThumbprint` dans la configuration de l’extension.
3. Créez un certificat client comme expliqué dans [Vue d’ensemble des certificats pour Azure Cloud Services](cloud-services-certs-create.md) (conservez l’empreinte numérique de certificat .pfx et RDP).
4. Installez Azure PowerShell (version 0.7.4 ou ultérieure) comme indiqué dans [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).
5. Exécutez le script suivant pour activer l'extension RemoteDebug. Remplacez les chemins d’accès et les données personnelles par les vôtres, notamment le nom de l'abonnement, le nom du service et l'empreinte numérique.

	>[AZURE.NOTE] Ce script est configuré pour Visual Studio 2015. Si vous utilisez Visual Studio 2013, modifiez les affectations `$referenceName` et `$extensionName` ci-dessous pour utiliser `RemoteDebugVS2013` (au lieu de `RemoteDebugVS2015`).

	<pre>
Add-AzureAccount

Select-AzureSubscription "My Microsoft Subscription"

$vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

$endpoints = @(
,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
)

foreach($endpoint in $endpoints)
{
Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
}

$referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
$publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
$extensionName&#160;= "RemoteDebugVS2015"
$version = "1.*"
$publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

$vm | Set-AzureVMExtension `
-ReferenceName $referenceName `
-Publisher $publisher `
-ExtensionName $extensionName `
-Version $version `
-PublicConfiguration $publicConfiguration

foreach($extension in $vm.VM.ResourceExtensionReferences)
{
if(($extension.ReferenceName -eq $referenceName) `
-and ($extension.Publisher -eq $publisher) `
-and ($extension.Name -eq $extensionName) `
-and ($extension.Version -eq $version))
{
$extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
break
}
}

$vm | Update-AzureVM
</pre>

6. Importez le certificat (.pfx) sur l'ordinateur sur lequel Visual Studio et le Kit de développement logiciel (SDK) Azure pour .NET sont installés.

<!---HONumber=AcomDC_0204_2016-->