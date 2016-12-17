---
title: "Guide de création d’un service de données pour le Marketplace | Microsoft Docs"
description: "Instructions détaillées pour créer, certifier et déployer un service de données que d’autres peuvent acheter sur Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 60d3225f276b54e08946744838a5028a02666149


---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mappage d’un service web existant à OData via des le langage CSDL
> [!IMPORTANT]
> **À ce stade, nous n’intégrons plus de nouveaux éditeurs de services de données. Le listing de nouveaux services de données ne sera pas approuvé.** Si vous avez une application SaaS professionnelle à publier sur AppSource, vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez une application IaaS ou un service de développement à publier sur Azure Marketplace, vous trouverez plus d’informations [ici](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Cet article offre une vue d’ensemble de l’utilisation d’un langage CSDL pour mapper un service existant à un service compatible OData. Il explique comment créer le document de mappage (CSDL) qui transforme la requête d’entrée provenant du client via un appel de service et la sortie (données) renvoyée au client via un flux compatible OData. Microsoft Azure Marketplace utilise le protocole OData pour exposer des services aux utilisateurs finaux. L’exposition des services par les fournisseurs de contenu (les propriétaires de données) s’effectue sous diverses formes, telles que REST, SOAP, etc.

## <a name="what-is-a-csdl-and-its-structure"></a>Qu’est un langage CSDL et sa structure ?
Un langage CSDL (Conceptual Schema Definition Language) est une spécification définissant la façon de décrire un service web ou un service de base de données dans un verbiage XML commun sur Azure Marketplace.

Vue d’ensemble simple du **flux de requêtes :**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Le **flux de données** est dans le sens opposé :

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**figure 1** présente un diagramme de la façon dont un client obtient des données provenant d’un fournisseur de contenu (votre service) en passant par Azure Marketplace.  Le langage CSDL est utilisé par le composant de mappage/transformation pour traiter le passage de requêtes et de données entre le ou les services du fournisseur de contenu et le client demandeur.

*Figure 1 : Flux détaillé du client demandeur vers le fournisseur de contenu via Azure Marketplace*

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Pour obtenir des informations générales sur Atom, Atom Pub et le protocole OData sur lequel reposent les extensions Azure Marketplace, consultez le site suivant : [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Extrait du site ci-dessus :       *« L’objectif du protocole Open Data (ci-après dénommé OData) est de fournir un protocole basé sur REST pour les opérations de style CRUD (Create, Read, Update et Delete, c’est-à-dire créer, lire, mettre à jour et supprimer) sur les ressources exposées en tant que services de données. Un « service de données » est un point de terminaison où des données sont exposées à partir d’une ou de plusieurs « collections », chacune comportant zéro « entrées » ou plus, qui consistent en des paires nom-valeur typées. OData est publié par Microsoft sous les normes OASIS (Organization for the Advancement of Structured Information Standards) afin que toute personne qui le souhaite puisse générer des serveurs, des clients ou des outils sans droits d’auteur ou restrictions. »*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Les trois éléments critiques qui doivent être définis par le langage CSDL sont les suivants :
* Le **point de terminaison** du service fournisseur : adresse web (URI) du service
* Les **paramètres de données** transmis en tant qu’entrée au fournisseur de services : définitions des paramètres envoyés au service du fournisseur de contenu jusqu’au type de données.
* **Schéma** des données renvoyées au service demandeur : schéma des données remises par le service du fournisseur de contenu, notamment le conteneur, les collections/tables, les variables/colonnes et les types de données.

Le diagramme suivant présente une vue d’ensemble du flux depuis lequel le client entre l’instruction OData (appel au service web du fournisseur de contenu) de récupération des résultats/données.

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Étapes :
1. Le client envoie la requête via l’appel du service avec les paramètres d’entrée définis en XML sur Azure Marketplace
2. Le langage CSDL est utilisé pour valider l’appel du service.
   * L’appel du service mis en forme est envoyé au service des fournisseurs de contenu par Azure Marketplace
3. Le service web s’exécute et effectue l’action du verbe HTTP (c’est-à-dire GET). Les données sont renvoyées à Azure Marketplace où les données demandées (le cas échéant) sont exposées au format XML au client à l’aide du mappage défini dans le langage CSDL.
4. Le client reçoit les données (le cas échéant) au format XML ou JSON

## <a name="definitions"></a>Définitions
### <a name="odata-atom-pub"></a>ATOM Pub OData
Extension du service ATOM Pub où chaque entrée représente une ligne d’un jeu de résultats. La partie de contenu de l’entrée a été améliorée de façon à contenir les valeurs de la ligne, sous la forme de paires clé/valeur. Vous trouverez des informations supplémentaires ici : [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL (Conceptual Schema Definition Language)
Permet de définir des fonctions (SPROC) et des entités qui sont exposées via une base de données. Vous trouverez des informations supplémentaires ici : [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> Cliquez sur la liste déroulante des **autres versions** et sélectionnez une version l’article ne s’affiche pas.
> 
> 

### <a name="edm---entry-data-model"></a>EDM (Entry Data Model)
* Vue d’ensemble : [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* Aperçu : [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* Types de données : [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Le diagramme suivant présente un flux de gauche à droite détaillé depuis lequel le client entre l’instruction OData (appel au service web du fournisseur de contenu) de récupération des résultats/données.

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>Concepts de base du langage CSDL
Un langage CSDL (Conceptual Schema Definition Language) est une spécification définissant la façon de décrire un service web ou un service de base de données dans un verbiage XML commun sur Azure Marketplace. Les données CSDL décrivent les éléments critiques qui **permettent la transmission de données à partir de la source de données à Azure Marketplace.**  Les éléments principaux sont décrits ici :

* Des informations d’interface qui décrivent toutes les fonctions disponibles publiquement (nœud FunctionImport)
* Des informations de type de données pour toutes les requêtes de messages (entrée) et réponses aux messages (sorties) (nœuds EntityContainer/EntitySet/EntityType)
* Des informations de liaison sur le protocole de transport à utilisé (nœud Header)
* Des informations d’adresse pour localiser le service spécifié (attribut BaseURI)

En résumé, le langage CSDL représente un contrat non spécifique à une plateforme et indépendant du langage entre le demandeur de services et le fournisseur de services. Le langage CSDL permet à un client de localiser un service web/service de base de données et d’appeler n’importe laquelle de ses fonctions disponibles publiquement.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Mise en relation d’un langage CSDL avec une base de données ou une collection
**La spécification CSDL**

CSDL est une grammaire XML utilisée pour décrire un service web. La spécification est elle-même divisée en 4 éléments principaux : EntitySet, FunctionImport, NameSpace et EntityType.

Pour rendre cette abstraction plus facile à comprendre, associons un CSDL à une table.

N’oubliez pas les points suivants :

  Le langage CSDL représente un contrat non spécifique à une plateforme et indépendant du langage entre le **demandeur de services** et le **fournisseur de services**. Le langage CSDL permet à un **client** de localiser un **service web/service de base de données** et d’appeler n’importe laquelle de ses **fonctions** disponibles publiquement.

Pour un service de données, vous pouvez considérer les quatre parties d’un élément CSDL comme une base de données, une table, une colonne et une procédure stockée.

Mise en relation de ces éléments comme suit pour un service de données :

* EntityContainer ~= Base de données
* EntitySet ~= Table
* EntityType ~= Colonnes
* FunctionImport ~= Procédure stockée

**Verbes HTTP autorisés**

* GET - renvoie les valeurs à partir de la base de données (renvoie une collection)
* POST - permet de transmettre des données et des valeurs de retour facultatives à partir de la base de données (créer une entrée dans la collection, renvoyer un ID/URI)
* DELETE - supprime les données de la base de données (supprime une collection)
* PUT - met à jour les données dans une base de données (remplace ou crée une collection)

## <a name="metadatamapping-document"></a>Document de métadonnées/de mappage
Le document de métadonnées/de mappage permet de mapper les services web existants d’un fournisseur de contenu afin qu’il puisse être exposé en tant que service web OData par le système Azure Marketplace. Il est basé sur le langage CSDL et implémente certaines extensions dans l’élément CSDL pour répondre aux besoins de REST en fonction des services web exposés via Azure Marketplace. Les extensions sont trouvent dans l’espace de noms [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Voici un exemple d’élément CSDL : (Copiez et collez l’exemple CSDL ci-dessous dans un éditeur XML et apportez les modifications nécessaires en fonction de votre service.  Collez-le ensuite dans un mappage CSDL sous l’onglet Service de données lors de la création de votre service dans le [portail de publication Azure Marketplace](https://publish.windowsazure.com).)

**Termes :** met en relation les termes CSDL avec les termes de l’interface utilisateur du [portail de publication](https://publish.windowsazure.com) (PPUI).

* Le « titre » de l’offre dans le PPUI est associé à MyWebOffer
* MyCompany dans le PPUI est associé au **nom d’affichage de l’éditeur** dans l’interface utilisateur [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure)
* Votre API est associée à un service web ou de données (un plan dans le PPUI)

**Hiérarchie :**
 une entreprise (fournisseur de contenu) possède une ou plusieurs offres qui comprennent des plans, c’est-à-dire des services, qui sont associés à une API.

### <a name="webservice-csdl-example"></a>Exemple de service web CSDL
L’exemple suivant établit une connexion à un service qui expose un point de terminaison d’application web (comme une application C#).

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> Affichez d’autres exemples de services web CSDL dans l’article [Exemples de mappage d’un service web existant à OData par le biais des données CSDL](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### <a name="dataservice-csdl-example"></a>Exemple de service de données CSDL
L’exemple suivant établit une connexion à un service qui expose une table ou une vue de base de données sous la forme d’un point de terminaison. Il présente deux API pour la base de données en fonction du CSDL de l’API (possibilité d’utiliser des vues plutôt que des tables).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Voir aussi
* Si vous souhaitez découvrir et comprendre les nœuds spécifiques et leurs paramètres, lisez l’article [Nœuds de mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) pour obtenir des définitions et des explications, des exemples, ainsi qu’un contexte de cas d’utilisation.
* Si vous souhaitez passer en revue des exemples, lisez l’article [Exemples de mappage du service de données OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour consulter des exemples de code et pour comprendre la syntaxe et le contexte du code.
* Pour retourner au chemin indiqué pour la publication d’un service de données sur Azure Marketplace, lisez l’article [Guide de publication de services de données](marketplace-publishing-data-service-creation.md).




<!--HONumber=Nov16_HO3-->


