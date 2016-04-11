<properties
   pageTitle="Présentation des stratégies de sécurité RunAs de l'application Service Fabric | Microsoft Azure"
   description="Vue d’ensemble de l’exécution d’une application Service Fabric sous des comptes de sécurité système et locaux, incluant le point SetupEntry où une application doit effectuer une action privilégiée avant de démarrer"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="msfussell"/>

# RunAs : exécution d’une application Service Fabric avec des autorisations de sécurité différentes
Azure Service Fabric permet de sécuriser les applications en cours d’exécution dans le cluster sous différents comptes utilisateurs, appelés **RunAs**. Service Fabric sécurise également les ressources utilisées par les applications avec le compte utilisateur, comme les fichiers, les répertoires et les certificats.

Par défaut, les applications Service Fabric s’exécutent sous le compte qui exécute le processus Fabric.exe. Service Fabric permet également d’exécuter des applications sous un compte utilisateur ou système local spécifié dans le manifeste de l’application. Les types de comptes système locaux pris en charge pour RunAs sont **LocalUser**, **NetworkService**, **LocalService** et **LocalSystem**.

> [AZURE.NOTE] Les comptes de domaine sont pris en charge sur les déploiements Windows Server où Azure Active Directory est disponible.

Il est possible de définir et créer des groupes d’utilisateurs de manière à pouvoir ajouter à chaque groupe un ou plusieurs utilisateurs qui seront gérés ensemble. Cela est particulièrement utile lorsqu’il existe plusieurs utilisateurs pour des points d’entrée de service différents et qu’ils doivent disposer de certains privilèges courants disponibles au niveau du groupe.

## Définition de la stratégie RunAs pour SetupEntryPoint

Comme décrit dans le [modèle d’application](service-fabric-application-model.md), **SetupEntryPoint** est un point d’entrée privilégié qui s’exécute avec les mêmes informations d’identification que Service Fabric (en général, le compte *NetworkService*) avant tout autre point d’entrée. Le fichier exécutable spécifié par **EntryPoint** étant généralement l’hôte de service de longue durée, la présence d’un point d’entrée de configuration distinct évite d’avoir à exécuter le fichier exécutable de l’hôte de service avec des privilèges élevés pendant de longues périodes de temps. Le fichier exécutable spécifié par **EntryPoint** est exécuté une fois que **SetupEntryPoint** se termine correctement. Le processus résultant fait l'objet d'une surveillance et est redémarré (à partir de **SetupEntryPoint**) en cas d'interruption ou de défaillance.

Vous trouverez ci-dessous un exemple simple de manifeste de service illustrant SetupEntryPoint et le point d’entrée (EntryPoint) principal du service.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Configurer la stratégie RunAs à l’aide d’un compte local

Après avoir configuré le service pour obtenir un point d’entrée de configuration, vous pouvez modifier les autorisations de sécurité sous lesquelles ce point d’entrée s’exécute dans le manifeste d’application. L’exemple ci-dessous montre comment configurer le service pour qu’il s’exécute avec des privilèges de compte d’administrateur de l’utilisateur.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Tout d’abord, créez une section **Principals** avec un nom d’utilisateur, par exemple SetupAdminUser. Ceci indique que l'utilisateur est membre du groupe des administrateurs du système.

Ensuite, sous la section **ServiceManifestImport**, configurez une stratégie pour appliquer ce principal à **SetupEntryPoint**. Ceci indique à Service Fabric que lorsque le fichier **MySetup.bat** est exécuté, il doit être exécuté en RunAs avec des privilèges d’administrateur. Étant donné que vous n’avez *pas* appliqué de stratégie au point d’entrée principal, le code dans **MyServiceHost.exe** s’exécutera sous le compte **NetworkService** du système. Il s’agit du compte par défaut avec lequel tous les points d’entrée de service sont exécutés en RunAs.

Nous allons maintenant ajouter le fichier MySetup.bat au projet Visual Studio pour tester les privilèges d’administrateur. Dans Visual Studio, cliquez avec le bouton droit sur le projet de service et ajoutez un nouveau fichier appelé MySetup.bat.

Vérifiez ensuite que le fichier MySetup.bat est inclus dans le package de service. Par défaut, il ne l’est pas. Sélectionnez le fichier, puis cliquez avec le bouton droit pour afficher le menu contextuel et choisissez **Propriétés**. Dans la boîte de dialogue Propriétés, vérifiez que **Copier dans le répertoire de sortie** est défini sur **Copier si plus récent**. Ceci est présenté dans la capture d'écran ci-dessous.

![Visual Studio CopyToOutput pour fichier batch SetupEntryPoint][image1]

Ouvrez maintenant le fichier MySetup.bat et ajoutez les commandes suivantes :

~~~
REM Définissez une variable d'environnement système. Cela requiert des privilèges d'administrateur
setx -m TestVariable "MyValue"
echo System TestVariable définie sur > test.txt
echo %TestVariable% >> test.txt

REM Pour supprimer cette variable système utilisez
REM REG delete "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment" /v TestVariable /f
~~~

Ensuite, générez et déployez la solution vers un cluster de développement local. Une fois que le service a démarré, comme illustré dans l’Explorateur Service Fabric, vous pouvez voir que le fichier MySetup.bat a réussi de deux façons. Ouvrez une invite de commandes PowerShell et entrez :

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Ensuite, notez le nom du nœud sur lequel le service a été déployé et démarré dans l’Explorateur de Service Fabric, par exemple, Nœud 2. Accédez au dossier de travail de l’instance d’application pour rechercher le fichier out.txt qui affiche la valeur de **TestVariable**. Par exemple, si le déploiement a été exécuté sur le Nœud 2, vous pouvez accéder à ce chemin pour **MyApplicationType** :

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  Configurer la stratégie RunAs à l’aide de comptes système locaux
Il est souvent préférable d’exécuter le script de démarrage à l’aide d’un compte système local plutôt que d’un compte d’administrateur, comme illustré ci-dessus. En règle générale, exécuter la stratégie RunAs en tant qu’administrateur ne fonctionne pas correctement, car le contrôle d’accès utilisateur est activé par défaut sur les ordinateurs. Dans ce cas, **nous vous recommandons d’exécuter SetupEntryPoint en tant que LocalSystem plutôt qu’utilisateur local ajouté au groupe Administrateurs**. L’exemple ci-dessous montre comment configurer SetupEntryPoint pour une exécution en tant que LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  Lancement de commandes PowerShell à partir de SetupEntryPoint
Pour exécuter PowerShell à partir du point **SetupEntryPoint**, vous pouvez exécuter **PowerShell.exe** dans un fichier de commandes qui pointe vers un fichier PowerShell. Tout d’abord, ajoutez un fichier PowerShell au projet de service, par exemple **MySetup.ps1**. N’oubliez pas de définir la propriété *Copier si plus récent* afin que le fichier soit également inclus dans le package de service. L’exemple suivant montre un exemple de fichier de commandes permettant de lancer un fichier PowerShell appelé MySetup.ps1, qui définit une variable d’environnement appelée **TestVariable**.


MySetup.bat pour lancer le fichier de PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Dans le fichier PowerShell, ajoutez la commande suivante pour définir une variable d’environnement système :

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Remarque :** par défaut, quand le fichier de commandes s’exécute, il détermine si le dossier d’application **work** contient des fichiers. Dans ce cas, quand MySetup.bat s’exécute, il doit rechercher le fichier MySetup.ps1 dans ce dossier, qui contient le **package de code** de l’application. À cette fin, définissez le dossier de travail comme indiqué ci-dessous.
    
~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint> 
~~~

## Utilisation de la stratégie de redirection de console pour le débogage local des points d’entrée
Parfois, à des fins de débogage, il est utile de visualiser la sortie de la console générée par l’exécution d’un script. Pour ce faire, vous pouvez définir une stratégie de redirection de console qui écrit la sortie dans un fichier. La sortie du fichier est écrite dans le dossier d’application **log** sur le nœud sur lequel l’application est déployée et exécutée (consultez l’exemple ci-dessus pour déterminer son emplacement).

**Remarque : n’utilisez jamais** la stratégie de redirection de console dans une application déployée dans un environnement de production, car cela peut influer sur le basculement de l’application. Utilisez **UNIQUEMENT** cette stratégie pour le développement local et à des fins de débogage.

L’exemple ci-dessous montre comment configurer la redirection de console avec une valeur FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint> 
~~~

Si vous modifiez le fichier MySetup.ps1 pour écrire une commande **Echo**, le contenu de celle-ci apparaît dans le fichier de sortie à des fins de débogage.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to" 
~~~

**Une fois que vous avez débogué votre script, supprimez immédiatement cette stratégie de redirection de console.**

## Application de RunAsPolicy aux services
Dans les étapes ci-dessus, vous avez vu comment appliquer une stratégie RunAs à un point SetupEntryPoint. Allons plus loin et voyons comment créer des principaux différents qui peuvent être appliqués en tant que stratégies de service.

### Création de groupes d'utilisateurs locaux
Il est possible de définir et de créer des groupes d’utilisateurs permettant d’ajouter un ou plusieurs utilisateurs à un groupe. Cela est particulièrement utile s'il existe plusieurs utilisateurs pour des points d'entrée de service différents et s'ils doivent disposer de certains privilèges courants disponibles au niveau du groupe. L’exemple ci-dessous montre un groupe local appelé **LocalAdminGroup** disposant de privilèges d’administrateur. Deux utilisateurs, Customer1 et Customer2, deviennent membres de ce groupe local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Création d'utilisateurs locaux
Vous pouvez créer un utilisateur local qui peut être utilisé pour sécuriser un service au sein de l'application. Quand un type de compte **LocalUser** est spécifié dans la section Principals du manifeste d’application, Service Fabric crée des comptes d’utilisateurs locaux sur les ordinateurs où l’application est déployée. Par défaut, ces comptes n’ont pas les mêmes noms que ceux spécifiés dans le manifeste d’application (Customer3 dans l’exemple ci-dessous). Ils sont générés dynamiquement et sont associés à des mots de passe aléatoires.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Affectation de stratégies aux packages de code de service
La section **RunAsPolicy** d’un **ServiceManifestImport** spécifie le compte de la section Principals qui doit être utilisé pour exécuter un package de code. Elle associe également des packages de code du manifeste de service à des comptes d’utilisateur dans la section Principals. Vous pouvez spécifier ce paramètre pour les points d’entrée de configuration ou principaux, ou appliquer ce paramètre aux deux en choisissant l’option Tout. L’exemple ci-dessous illustre les différentes stratégies appliquées :

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si **EntryPointType** n'est pas spécifié, la valeur par défaut est définie sur EntryPointType=« Main ». La spécification d’un **SetupEntryPoint** est particulièrement utile quand vous souhaitez exécuter certaines opérations d’installation à privilège élevé sous un compte système. Le code de service en tant que tel peut s’exécuter sous un compte à faible privilège.

### Application d’une stratégie par défaut à tous les packages de code de service
La section **DefaultRunAsPolicy** permet de spécifier un compte utilisateur par défaut pour tous les packages de code qui n’ont pas de stratégie **RunAsPolicy** spécifique définie. Si la plupart des packages de code spécifiés dans les manifestes de service utilisés par une application doivent s’exécuter sous le même utilisateur RunAs, l’application peut définir une stratégie RunAs par défaut avec ce compte utilisateur au lieu de spécifier une stratégie **RunAsPolicy** pour chaque package de code. L’exemple suivant spécifie que, si un package de code n’a pas de stratégie **RunAsPolicy** spécifiée, le package de code doit être exécuté sous le compte **MyDefaultAccount** spécifié dans la section Principals.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Affectation d’une stratégie SecurityAccessPolicy pour des points de terminaison HTTP et HTTPS
Si vous appliquez une stratégie RunAs à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole HTTP, vous devez spécifier une stratégie **SecurityAccessPolicy** pour vous assurer que les ports affectés à ces points de terminaison sont correctement au format ACL pour le compte utilisateur RunAs sous lequel le service est exécuté. Sinon, **http.sys** n’a pas accès au service et les appels en provenance du client échoueront. L’exemple suivant applique le compte Customer3 au point de terminaison **ServiceEndpointName** en lui attribuant des droits d’accès complets.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Pour le point de terminaison HTTPS, vous devez également indiquer le nom du certificat à retourner au client. Vous pouvez pour cela utiliser la stratégie **EndpointBindingPolicy**, avec le certificat défini dans une section dédiée aux certificats du manifeste d’application.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Exemple complet de manifeste d'application
Le manifeste d’application ci-dessous affiche un grand nombre des différents paramètres décrits ci-dessus :

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0330_2016-->