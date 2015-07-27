<properties
	pageTitle="Activation du débogage distant avec la remise continue"
	description="Découvrez comment activer le débogage distant lors de l'utilisation de la remise continue pour publier sur Azure."
	services="cloud-services"
	documentationCenter=".net"
	authors="kempb"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="kempb"/>
# Activation du débogage distant lors de l'utilisation de la remise continue pour publier sur Azure

En procédant comme suit, vous pouvez activer le débogage distant dans Azure lorsque vous utilisez la [remise continue](cloud-services-dotnet-continuous-delivery.md) afin de publier sur Azure.

Dans cette rubrique :

[Activation du débogage distant pour les services cloud](#cloudservice)

[Activation du débogage distant pour les machines virtuelles](#virtualmachine)

## <a name="cloudservice"></a>Activation du débogage distant pour les services cloud

1. Dans l'agent de build, configurez l'environnement de départ pour Azure comme expliqué à la page [Génération en mode ligne de commande pour Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. L'exécution du débogage distant (msvsmon.exe) étant nécessaire pour ce package, installez les [Outils de contrôle à distance pour Visual Studio 2015 RC](http://www.microsoft.com/download/details.aspx?id=46874) (ou les [Outils de contrôle à distance pour Visual Studio 2013 Update 5 RC](https://www.microsoft.com/fr-fr/download/details.aspx?id=46870) si vous utilisez Visual Studio 2013). Une autre méthode consiste à copier les fichiers binaires de débogage distant à partir d'un système sur lequel Visual Studio est installé.
3. Créez un certificat en suivant les instructions à la page [Créer un certificat de service pour Azure](http://msdn.microsoft.com/library/azure/gg432987.aspx). Conservez l'empreinte numérique de certificat .pfx et RDP et téléchargez le certificat sur le service cloud cible.
4. Utilisez les options suivantes dans la ligne de commande MSBuild pour générer et créer des packages en activant le débogage distant. (Remplacez les chemins d'accès réels de votre système et de vos fichiers de projet pour les éléments entre crochets.)

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.6" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` est le chemin d’accès au dossier contenant msvsmon.exe dans les Outils de contrôle à distance de Visual Studio.

5. Publiez sur le service cloud cible en utilisant le package et le fichier .cscfg générés à l'étape précédente.
6. Importez le certificat (fichier .pfx) sur l'ordinateur sur lequel Visual Studio et le Kit de développement logiciel (SDK) Azure SDK pour .NET sont installés.

## <a name="virtualmachine"></a>Activation du débogage distant pour les machines virtuelles

1. Créez une machine virtuelle Azure. Consultez [Création d'une machine virtuelle exécutant Windows Server](../virtual-machines-windows-tutorial.md) ou [Création de machines virtuelles Azure dans Visual Studio](http://msdn.microsoft.com/library/azure/dn569263.aspx).
2. Sur la [page du portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), affichez le tableau de bord de la machine virtuelle pour voir l'« empreinte numérique de certificat RDP » de la machine virtuelle. On l'utilise pour la valeur `ServerThumbprint` dans la configuration de l'extension.
3. Créez un certificat client comme expliqué dans [Créer un certificat de service pour Azure](http://msdn.microsoft.com/library/azure/gg432987.aspx) (conservez l'empreinte numérique de certificat .pfx et RDP).
4. Installez [Azure Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (version 0.7.4 ou ultérieure) à partir du Centre de téléchargement Microsoft.
5. Exécutez le script suivant pour activer l'extension RemoteDebug. Remplacez les chemins d’accès et les données personnelles par les vôtres, notamment le nom de l'abonnement, le nom du service et l'empreinte numérique. (REMARQUE : ce script est configuré pour Visual Studio 2015 RC. Si vous utilisez Visual Studio 2013, utilisez « RemoteDebugVS2013 » pour le nom de la référence et de l'extension.)

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
    $extensionName = "RemoteDebugVS2015"
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
 

<!----HONumber=July15_HO3-->