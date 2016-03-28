<properties
   pageTitle="Kit de développement logiciel (SDK) Resource Manager pour Java| Microsoft Azure"
   description="Une vue d’ensemble des exemples d’authentification et de cas d’utilisation Kit de développement logiciel (SDK) Resource Manager pour Java"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>
   
# Kit de développement logiciel (SDK) Azure Resource Manager pour Java
   
Des kits de développement logiciel (SDK) Azure Resource Manager en version préliminaire sont disponibles en plusieurs langues sur plusieurs plates-formes. Chacune de ces langue est disponible à travers leurs gestionnaires de package d’écosystème et GitHub.

Le code dans chaque kit de développement logiciel (SDK) est généré à partir de [spécifications de l’API Azure RESTful](https://github.com/azure/azure-rest-api-specs). Ces spécifications sont open source et basées sur la spécification Swagger v2. Le code de kit de développement logiciel (SDK) est généré par le biais d’un projet open source appelé [AutoRest](https://github.com/azure/autorest). AutoRest transforme ces spécifications d’API RESTful en bibliothèques clientes dans plusieurs langues. Si vous souhaitez améliorer des aspects du code généré dans les kits de développement logiciel, l’ensemble des outils permettant de créer les kits de développement logiciels sont ouverts, disponibles gratuitement et basés sur le format de spécification API largement adopté.

Le kit de développement logiciel (SDK) Azure Resource Manager pour Java est hébergé dans le [référentiel de kit de développement logiciel (SDK) Azure](https://github.com/azure/azure-sdk-for-java) de GitHub. Notez que le kit de développement logiciel (SDK) est disponible en version préliminaire au moment de la rédaction de cet article. Les packages suivants sont disponibles :

* Gestion de calcul : (azure-mgmt-compute)
* Gestion du service DNS : (azure-mgmt-dns)
* Gestion de réseau : (azure-mgmt-network)
* Gestion des ressources : (azure-mgmt-resources)
* Gestion SQL : (azure-mgmt-sql)
* Gestion du stockage : (azure-mgmt-storage)
* Gestion de Traffic Manager : (azure-mgmt-trafic-manager)
* Utilitaires et programmes d’assistance : (azure-mgmt-utility)
* Gestion de sites Web / d’applications Web : (azure-mgmt-websites)

Suivez le Kit de développement logiciel (SDK) Azure sur la [page Wiki des fonctionnalités Java](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features) pour obtenir une liste actuelle.

## Composants requis
1. Java v1.6+
2. [maven](https://maven.apache.org/) si vous souhaitez développer sur le Kit de développement logiciel (SDK)

## Obtention du Kit de développement logiciel (SDK)
Les fichiers JAR distribués[Maven](https://maven.apache.org/) sont la méthode recommandée de prise en main du Kit de développement logiciel (SDK) Java Azure. Vous pouvez ajouter ces dépendances à de nombreux outils de gestion de la dépendance Java (Maven, Gradle, Ivy...). Suivez ce [lien](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22) pour obtenir la liste des bibliothèques disponibles dans maven.

Vous pouvez également vous obtenir le Kit de développement logiciel (SDK) directement à partir de la source à l’aide de git. Pour obtenir le code source du Kit de développement logiciel (SDK) par le biais de Git :

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(Les exemples de cette vue d’ensemble utilisent Maven comme source pour les packages de Kit de développement logiciel (SDK))

Le Kit de développement logiciel (SDK) comprend des exemples pour certains scénarios : authentification, approvisionnement d’une machine virtuelle et bien plus encore. Ils se trouvent tous dans le référentiel GitHub [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples).

## Classes de programmes d’assistance

Le Kit de développement logiciel (SDK) comprend des classes de programmes d’assistance pour plusieurs packages principaux. Les classes de programmes d’assistance sont implémentées dans le package auzre-mgmt-utility :

* AuthHelper - classe de programmes d’assistance d’authentification
* ComputeHelper - classe de programmes d’assistance de calcul
* NetworkHelper - classe de programmes d’assistance de réseau
* ResourceHelper - classe de programmes d’assistance de déploiements
* StorageHelper - classe de programmes d’assistance de stockage

**Informations sur la dépendance Maven**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

La version 0.9.1 du package d’utilitaire est utilisée.

## Authentification

L’authentification pour ARM est gérée par Azure Active Directory (AD). Pour vous connecter à une API quelconque, vous devez tout d’abord vous authentifier auprès d’Azure AD pour recevoir un jeton d’authentification que vous pouvez transmettre à chaque requête. Pour obtenir ce jeton, vous devez d’abord créer ce que l’on appelle une application Azure AD et un principal du service qui seront utilisés pour la connexion. Référez-vous à [Création de l’application Active Directory et du principal du service](./resource-group-create-service-principal-portal.md) pour obtenir des instructions étape par étape.

Une fois le principal du service créé, vous devez avoir les éléments suivants :
* ID de client (GUID)
* Clé secrète client (chaîne de caractères)
* L’ID de client (GUID) ou le nom de domaine (chaîne de caractères) Une fois que vous disposez de ces valeurs, vous pouvez obtenir un jeton d’accès Active Directory valide pendant une heure.

Le Kit de développement logiciel (SDK) Java comprend une classe de programmes d’assistance AuthHelper qui crée le jeton d’accès, une fois fournis l’ID de client et la clé secrète client. L’exemple suivant (dans la classe de programmes d’assistance [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java)) utilise la méthode AuthHelper *getAccessTokenFromServicePrincipalCredentials* pour obtenir le jeton d’accès :

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## Création d'une machine virtuelle 
Le package d’utilitaire comprend une classe de programmes d’assistance [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java) pour créer une machine virtuelle. Vous trouverez quelques exemples d’utilisation des machines virtuelles dans le package azure-mgmt-samples sous [compute](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute).

L’exemple suivant montre un flux simple pour la création d’une machine virtuelle. Il comprend une classe de programmes d’assistance qui permet de créer le stockage et le réseau au cours de la création de la machine virtuelle :

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## Déploiement d’un modèle
La classe [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) a été créée pour faciliter le processus de déploiement d’un modèle ARM avec le Kit de développement logiciel (SDK) Java.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```

Vous trouverez d’autres exemples dans les packages d’exemples sous [templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments).

## Pour aller plus loin et trouver de l’aide
Documentation pour le Kit de développement logiciel (SDK) Azure pour Java : [documents Java](http://azure.github.io/azure-sdk-for-java/) Si vous rencontrez des bogues avec le Kit de développement logiciel (SDK), ouvrez un ticket sous [Problèmes](https://github.com/Azure/azure-sdk-for-java/issues) ou référez-vous à [StackOverflow pour le Kit de développement logiciel (SDK) Azure pour Java](http://stackoverflow.com/questions/tagged/azure-java-sdk).

<!---HONumber=AcomDC_0316_2016-->