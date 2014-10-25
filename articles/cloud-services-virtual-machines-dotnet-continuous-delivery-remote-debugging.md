<properties linkid="cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging" urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Enable remote debugging with continuous delivery" metaKeywords="" description="Learn how to enable remote debugging when using continuous delivery to deploy to Azure." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Enable remote debugging when using continuous delivery to publish to Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Activation du débogage distant lors de l'utilisation de la remise continue pour publier sur Azure

En procédant comme suit, vous pouvez activer le débogage distant dans Azure lorsque vous utilisez la [remise continue][remise continue] afin de publier sur Azure.

Dans cette rubrique :

[Activation du débogage distant pour les services cloud][Activation du débogage distant pour les services cloud]

[Activation du débogage distant pour les machines virtuelles][Activation du débogage distant pour les machines virtuelles]

## <a name="cloudservice"></a>Activation du débogage distant pour les services cloud

1.  Dans l'agent de build, configurez l'environnement de départ pour Azure comme expliqué à la page [Génération en mode ligne de commande pour Azure][Génération en mode ligne de commande pour Azure].
2.  L'exécution du débogage distant (msvsmon.exe) étant nécessaire pour ce package, installez les [Outils de contrôle à distance pour Visual Studio 2013][Outils de contrôle à distance pour Visual Studio 2013] (ou les [Outils de contrôle à distance pour Visual Studio 2012 Update 4][Outils de contrôle à distance pour Visual Studio 2012 Update 4] si vous utilisez Visual Studio 2012). Une autre méthode consiste à copier les fichiers binaires de débogage distant à partir d'un système sur lequel Visual Studio est installé.
3.  Créez un certificat en suivant les instructions à la page [Créer un certificat de service pour Azure][Créer un certificat de service pour Azure]. Conservez l'empreinte numérique de certificat .pfx et RDP et téléchargez le certificat sur le service cloud cible.
4.  Utilisez les options suivantes dans la ligne de commande MSBuild pour générer et créer des packages en activant le débogage distant. Mettez à jour les chemins d'accès de vos fichiers système et projet.

    /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Publiez sur le service cloud cible en utilisant le package et le fichier .cscfg générés à l'étape précédente.
6.  Importez le certificat (fichier .pfx) sur l'ordinateur sur lequel Visual Studio avec le Kit de développement logiciel (SDK) Azure SDK 2.4 est installé.

## <a name="virtualmachine"></a>Activation du débogage distant pour les machines virtuelles

1.  Créez une machine virtuelle Azure. Consultez [Création d'une machine virtuelle exécutant Windows Server][Création d'une machine virtuelle exécutant Windows Server] ou [Création de machines virtuelles Azure dans Visual Studio][Création de machines virtuelles Azure dans Visual Studio].
2.  Sur la [page du portail Azure][page du portail Azure], affichez le tableau de bord de la machine virtuelle pour voir l'« empreinte numérique de certificat RDP » de la machine virtuelle. On l'utilise pour la valeur ServerThumbprint dans la configuration de l'extension.
3.  Créez un certificat client comme expliqué dans [Créer un certificat de service pour Azure][Créer un certificat de service pour Azure] (conservez l'empreinte numérique de certificat .pfx et RDP).
4.  Installez [Azure Powershell][Azure Powershell] (version 0.7.4 ou ultérieure) à partir du Centre de téléchargement Microsoft.
5.  Exécutez le script suivant pour activer l'extension RemoteDebug. Remplacez les données personnelles par les vôtres, notamment le nom de l'abonnement, le nom du service et l'empreinte numérique. (REMARQUE : ce script est configuré pour Visual Studio 2013. Si vous utilisez Visual Studio 2012, utilisez « RemoteDebugVS2013 » pour ReferenceName et ExtensionName.)

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

        $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
        $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
        $extensionName = "RemoteDebugVS2013"
        $version = "1.*"
        $publicConfiguration = "true56D7D1B25B472268E332F7FC0C87286458BFB6B2E7DCB00CB916C468CC3228261D6E4EE45C8ED3C63039831398"

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

6.  Importez le certificat (.pfx) sur l'ordinateur sur lequel Visual Studio avec le Kit de développement logiciel (SDK) Azure pour NET 2.4 est installé.

  [remise continue]: http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Activation du débogage distant pour les services cloud]: #cloudservice
  [Activation du débogage distant pour les machines virtuelles]: #virtualmachine
  [Génération en mode ligne de commande pour Azure]: http://msdn.microsoft.com/en-us/library/hh535755.aspx
  [Outils de contrôle à distance pour Visual Studio 2013]: http://www.microsoft.com/en-us/download/details.aspx?id=40781
  [Outils de contrôle à distance pour Visual Studio 2012 Update 4]: http://www.microsoft.com/en-us/download/details.aspx?id=38184
  [Créer un certificat de service pour Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Création d'une machine virtuelle exécutant Windows Server]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-windows-tutorial/
  [Création de machines virtuelles Azure dans Visual Studio]: http://msdn.microsoft.com/en-us/library/azure/dn569263.aspx
  [page du portail Azure]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [Azure Powershell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
