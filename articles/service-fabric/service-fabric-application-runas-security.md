<properties
   pageTitle="Présentation des stratégies de sécurité RunAs de l'application Service Fabric | Microsoft Azure"
   description="Une vue d'ensemble de l'exécution d'une application Service Fabric sous des comptes de sécurité système et locaux, incluant le point SetupEntry où une application doit effectuer une action privilégiée avant de commencer"
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
   ms.date="11/21/2015"
   ms.author="mfussell"/>

# RunAs : exécution d'une application Service Fabric avec des autorisations de sécurité différentes
Service Fabric permet de sécuriser les applications en cours d'exécution dans le cluster sous différents comptes utilisateurs, appelés « RunAs ». En outre, il sécurise également les ressources utilisées par les applications avec le compte utilisateur, comme les fichiers, les répertoires et les certificats.

Par défaut, les applications Service Fabric s'exécutent sous le compte qui exécute le processus Fabric.exe. Il permet également d'exécuter des applications sous un compte utilisateur local spécifié dans le manifeste de l'application. Les types de comptes pris en charge pour RunAs sont **LocalUser**, **NetworkService**, **LocalService** et **LocalSystem**.

> [AZURE.NOTE]Les comptes de domaine sont pris en charge sur les déploiements Windows Server où Active Directory est disponible.

Il est possible de définir et de créer des groupes d'utilisateurs où un ou plusieurs utilisateurs peuvent être ajoutés à ce groupe pour être gérés ensemble. Cela est particulièrement utile s'il existe plusieurs utilisateurs pour des points d'entrée de service différents et s'ils doivent disposer de certains privilèges courants disponibles au niveau du groupe.

## Définition de la stratégie RunAs pour le point d'entrée de configuration

Comme décrit dans le [modèle d'application](service-fabric-application-model.md), **SetupEntryPoint** est un point d'entrée privilégié qui s'exécute avec les mêmes informations d'identification que Service Fabric (en général, le compte *Réseau*) avant tout autre point d'entrée. Le fichier exécutable spécifié par **EntryPoint** étant généralement l'hôte de service de longue durée, la présence d'un point d'entrée de configuration distinct évite d'avoir à exécuter le fichier exécutable de l'hôte de service avec des privilèges élevés pendant de longues périodes de temps. Le fichier exécutable spécifié par **EntryPoint** est exécuté une fois que **SetupEntryPoint** se termine correctement. Le processus résultant fait l'objet d'une surveillance et est redémarré (de nouveau à partir de **SetupEntryPoint**) en cas d'interruption ou de défaillance.

Vous trouverez ci-dessous un exemple simple de manifeste de service illustrant SetupEntryPoint et le point d'entrée (EntryPoint) principal pour le service.

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

### Configuration de la stratégie RunAs

Après avoir configuré le service pour avoir un SetupEntryPoint, vous pouvez modifier les autorisations de sécurité sous lesquelles ce point d'entrée s'exécute dans le manifeste d'application. L'exemple ci-dessous montre comment configurer le service pour qu'il s'exécute avec des privilèges de compte d'administrateur.

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

Tout d'abord, créez une section **Principals** avec un nom d'utilisateur, par exemple SetupAdminUser. Ceci indique que l'utilisateur est membre du groupe des administrateurs du système.

Ensuite, sous la section **ServiceManifestImport**, configurez une stratégie pour appliquer ce principal à **SetupEntryPoint**. Ceci indique à Service Fabric que lorsque le fichier MySetup.bat est exécuté, il doit être exécuté en RunAs avec des privilèges d'administrateur. Étant donné que vous n'avez *pas* appliqué une stratégie au point d'entrée principal, le code dans MyServiceHost.exe s'exécutera sous le compte NetworkService du système qui est le compte par défaut avec lequel tous les points d'entrée de service sont exécutés en RunAs.

Nous allons maintenant ajouter le fichier MySetup.bat au projet Visual Studio pour tester les privilèges d'administrateur. Dans Visual Studio, cliquez avec le bouton droit sur le projet de service et ajoutez un nouveau fichier appelé MySetup.bat. Ensuite, il est nécessaire de s'assurer que ce fichier est inclus dans le package de service, ce qui n'est pas le cas par défaut. Pour vous assurer que le fichier MySetup.bat est inclus dans le package, sélectionnez le fichier, cliquez avec le bouton droit pour obtenir le menu contextuel, sélectionnez Propriétés, puis dans la boîte de dialogue Propriétés, vérifiez que l'option **Copier vers le répertoire de sortie** est définie sur **Copier si plus récent**. Ceci est présenté dans la capture d'écran ci-dessous.

![Visual Studio CopyToOutput pour fichier batch SetupEntryPoint][Image1]

Ouvrez le fichier MySetup.bat et ajoutez les commandes suivantes.
~~~
REM Définissez une variable d'environnement système. Cela requiert des privilèges d'administrateur
setx -m TestVariable "MyValue"
echo System TestVariable définie sur > test.txt
echo %TestVariable% >> test.txt

REM Pour supprimer cette variable système utilisez
REM REG delete "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment" /v TestVariable /f
~~~

Ensuite, générez et déployez la solution vers un cluster de développement local. Une fois que le service a démarré, comme illustré dans l'Explorateur Service Fabric, vous pouvez voir que le fichier MySetup.bat a réussi de deux façons. Ouvrez une invite de commandes PowerShell et tapez
~~~
 [Environment]::GetEnvironmentVariable("TestVariable","Machine")
~~~
Comme ceci
~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine") MyValue
~~~

Notez ensuite le nom du nœud où le service a été déployé et démarré dans l'Explorateur Service Fabric, par exemple Node 1, et naviguez vers le dossier de travail de l'instance d'application pour rechercher le fichier out.txt qui affiche la valeur de **TestVariable**. Par exemple, si ceci a été déployé sur Node 2, alors vous pouvez accéder à ce chemin d'accès pour MyApplicationType

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  Lancement des commandes PowerShell à partir de SetupEntryPoint
Pour exécuter PowerShell à partir du point **SetupEntryPoint**, vous pouvez exécuter PowerShell.exe dans un fichier batch qui pointe vers un fichier PowerShell. Tout d'abord, ajoutez un fichier PowerShell au projet de service, par exemple MySetup.ps1. N'oubliez pas de définir la propriété *Copier si plus récent* afin que ce fichier soit également inclus dans le package de service. L'exemple suivant montre un exemple de fichier batch pour lancer un fichier PowerShell appelé MySetup.ps1 qui définit une variable d'environnement appelée *TestVariable*.

MySetup.bat pour lancer le fichier PowerShell.
~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\\MySetup.ps1"
~~~

Dans le fichier PowerShell, ajoutez ce qui suit pour définir une variable d'environnement système
~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

## Application de RunAsPolicy aux services 
Dans les étapes ci-dessus, vous avez vu comment appliquer une stratégie RunAs à un point SetupEntryPoint. Allons plus loin et explorons comment créer des principaux différents qui peuvent être appliqués en tant que stratégies de service.

### Création de groupes d'utilisateurs locaux
Il est possible de définir et de créer des groupes d'utilisateurs où un ou plusieurs utilisateurs peuvent être ajoutés à ce groupe. Cela est particulièrement utile s'il existe plusieurs utilisateurs pour des points d'entrée de service différents et s'ils doivent disposer de certains privilèges courants disponibles au niveau du groupe. L'exemple ci-dessous montre un groupe local appelé **LocalAdminGroup** avec des privilèges d'administrateur. Deux utilisateurs, Customer1 et Customer2 deviennent membres de ce groupe local.

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
Vous pouvez créer un utilisateur local qui peut être utilisé pour sécuriser un service au sein de l'application. Lorsqu'un type de compte LocalUser est spécifié dans la section Principals du manifeste d'application, Service Fabric crée des comptes d'utilisateurs locaux sur les ordinateurs où l'application est déployée. Ces comptes, par défaut, n'ont pas le même nom que celui spécifié dans le manifeste d'application (par exemple "Customer3" dans l'exemple ci-dessous), mais ils seront générés dynamiquement et auront des mots de passe aléatoires.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~
 
<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Affectation de stratégies aux packages de code de service
La section **RunAsPolicy** pour un **ServiceManifestImport** spécifie le compte de la section Principals qui doit être utilisé pour exécuter un package de code et associe des packages de code à partir du manifeste de service à des comptes d'utilisateurs dans la section Principals. Vous pouvez spécifier ceci pour les points d'entrée Setup ou Main, ou spécifiez All (Tout) pour appliquer ceci aux deux. L'exemple ci-dessous montre les différentes stratégies appliquées.

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si **EntryPointType** n'est pas spécifié, la valeur par défaut est définie sur EntryPointType=« Main ». La spécification d'un **SetupEntryPoint** est particulièrement utile lorsque vous souhaitez exécuter certaines opérations d'installation à privilège élevé sous un compte système, tandis que le code de service peut s'exécuter sous un compte à privilège inférieur.

### Application d'une stratégie par défaut à tous les packages de code de service
La section **DefaultRunAsPolicy** permet de spécifier un compte utilisateur par défaut pour tous les packages de code qui n'ont pas de **RunAsPolicy** spécifique définie. Si la plupart des packages de code spécifiés dans les manifestes de service utilisés par une application doivent s'exécuter sous le même utilisateur RunAs, l'application peut définir une stratégie RunAs par défaut avec ce compte utilisateur au lieu de spécifier une stratégie **RunAsPolicy** pour chaque package de code. Par exemple, l'exemple suivant spécifie que si un package de code n'a pas de stratégie **RunAsPolicy** spécifiée, le package de code doit être exécuté sous le compte MyDefaultAccount spécifié dans la section Principals.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Affectation de SecurityAccessPolicy pour des points de terminaison http et https
Si vous appliquez une stratégie RunAs à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole http, vous devez spécifier une stratégie **SecurityAccessPolicy** pour vous assurer que les ports affectés à ces points de terminaison sont correctement au format ACL pour le compte utilisateur RunAs sous lequel le service fonctionne. Sinon, http.sys n'a pas accès au service et vous obtiendrez un échec avec les appels à partir du client. L'exemple suivant applique le compte Customer3 au point de terminaison appelé *ServiceEndpointName* en lui attribuant des droits d'accès complets.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Pour le point de terminaison https, vous devez en plus indiquer le nom du certificat à renvoyer au client avec une stratégie **EndpointBindingPolicy** lorsque le certificat est défini dans une section de certificats dans le manifeste d'application.

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
Le manifeste d'application ci-dessous affiche un grand nombre des différents paramètres décrits ci-dessus.

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
         <User Name="Customer1" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécification des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[Image1]: media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=Nov15_HO4-->