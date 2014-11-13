<properties pageTitle="Notes de publication de Media Services" metaKeywords="Azure Media Services" description="Notes de publication de Media Services" metaCanonical="" services="media-services" documentationCenter="Media" title="Notes de publication de Media Services" authors="juliako" solutions="media" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="media" ms.devlang="dotnet" ms.topic="article" ms.date="10/15/2014" ms.author="juliako" />

# Notes de publication d'Azure Media Services

Ces notes de publication récapitulent les modifications par rapport aux précédentes versions et les problèmes connus.

> [AZURE.NOTE] Nous voulons être à l'écoute de nos clients et nous concentrer sur la résolution des problèmes que vous rencontrez. Pour signaler un problème ou poser des questions, publiez un billet sur le [Forum MSDN sur Azure Media Services][Forum MSDN sur Azure Media Services].

-   [Problèmes actuellement connus][Problèmes actuellement connus]
-   [Historique des versions de l'API REST][Historique des versions de l'API REST]
-   [Version d'octobre 2014][Version d'octobre 2014]
-   [Version de septembre 2014][Version de septembre 2014]
-   [Version d'août 2014][Version d'août 2014]
-   [Version de juillet 2014][Version de juillet 2014]
-   [Version de mai 2014][Version de mai 2014]
-   [Version d'avril 2014][Version d'avril 2014]
-   [Versions de janvier/février 2014][Versions de janvier/février 2014]
-   [Version de décembre 2014][Version de décembre 2014]
-   [Version de novembre 2013][Version de novembre 2013]
-   [Version d'août 2013][Version d'août 2013]
-   [Version de juin 2013][Version de juin 2013]
-   [Version de décembre 2012][Version de décembre 2012]
-   [Version de novembre 2012][Version de novembre 2012]
-   [Version préliminaire de juin 2012][Version préliminaire de juin 2012]

## <span id="issues"></span></a>Problèmes actuellement connus

### <span id="general_issues"></span></a>Problèmes généraux concernant Media Services

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Problème</th>
<th align="left">Description</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Plusieurs en-têtes HTTP courants ne sont pas fournis dans l'API REST.</td>
<td align="left">Si vous développez des applications Media Services à l'aide de l'API REST, vous constaterez que certains champs d'en-tête HTTP courants (notamment CLIENT-REQUEST-ID, REQUEST-ID et RETURN-CLIENT-REQUEST-ID) ne sont pas pris en charge. Les en-têtes seront ajoutés dans une prochaine mise à jour.</td>
</tr>
<tr class="even">
<td align="left">Le codage d'un élément multimédia avec un nom de fichier contenant un caractère d'échappement (par exemple, %20) échoue avec le message « MediaProcessor : Fichier introuvable. »</td>
<td align="left">Les noms des fichiers qui vont être ajoutés à un élément multimédia puis codés ne doivent contenir que des caractères alphanumérique et des espaces. Le problème sera résolu dans une prochaine mise à jour.</td>
</tr>
<tr class="odd">
<td align="left">La méthode ListBlobs intégrée à la version 3.x du Kit de développement logiciel (SDK) d'Azure Storage échoue.</td>
<td align="left">Media Services génère des URL SAS basées sur la version du <a href="http://msdn.microsoft.com/fr-fr/library/azure/dn592123.aspx">02/12/2012</a>. Si vous voulez utiliser le Kit de développement logiciel (SDK) d'Azure Storage pour répertorier les objets blob dans un conteneur d'objets blob, utilisez la méthode <a href="http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> intégrée à la version 2.x de ce kit. La méthode ListBlobs intégrée à la version 3.x du Kit de développement logiciel (SDK) d'Azure Storage échouera.</td>
</tr>
<tr class="even">
<td align="left">Le mécanisme de limitation de Media Services restreint l'utilisation des ressources pour les applications qui recourent de manière excessive au service. Le service peut renvoyer le code d'état HTTP Service indisponible (503).</td>
<td align="left">Pour plus d'informations, reportez-vous à la description du code d'état HTTP 503 dans la rubrique <a href="http://msdn.microsoft.com/fr-fr/library/azure/dn168949.aspx">Codes d'erreur d'Azure Media Services</a>.</td>
</tr>
</tbody>
</table>

### <span id="dotnet_issues"></span></a>Problèmes concernant le Kit de développement logiciel (SDK) de Media Services pour .NET

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Problème</th>
<th align="left">Description</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Les objets Media Services dans le Kit de développement logiciel (SDK) ne peuvent pas être sérialisés et, par conséquent, ne fonctionnent pas avec Azure Caching.</td>
<td align="left">Si vous essayez de sérialiser l'objet AssetCollection du Kit de développement logiciel (SDK) pour l'ajouter à Azure Caching, une exception est levée.</td>
</tr>
</tbody>
</table>

## <span id="rest_version_history"></span></a>Historique des versions de l'API REST

Pour des informations sur l'historique des versions de l'API REST, consultez la page [Référence de l'API REST d'Azure Media Services][Référence de l'API REST d'Azure Media Services].

## <span id="october_changes_14"></span></a>Version d'octobre 2014

### <span id="new_encoder_release"></span></a>Version de l'encodeur Media Services

Annonce de la nouvelle version de l'encodeur multimédia Azure de Media Services. Avec le tout dernier encodeur multimédia Azure, seuls les Go en sortie vous sont facturés. Par ailleurs, les fonctionnalités du nouvel encodeur sont compatibles avec celles de l'encodeur multimédia Azure. Pour plus d'informations, consultez la page [Media Services - Tarification][Media Services - Tarification].

### <span id="oct_sdk"></span></a>Kit de développement logiciel (SDK) .NET de Media Services

La dernière version du Kit de développement logiciel (SDK) de Media Services pour .NET est la version 3.0.0.8.

La dernière version du package Extensions du Kit de développement logiciel (SDK) de Media Services pour .NET est la version 2.0.0.3.

## <span id="september_changes_14"></span></a>Version de septembre 2014

La dernière version des métadonnées REST de Media Services est la version 2.7. Pour plus d'informations sur les dernières mises à jour REST, consultez la page [Référence de l'API REST d'Azure Media Services][Référence de l'API REST d'Azure Media Services].

La dernière version du Kit de développement logiciel (SDK) de Media Services pour .NET est la version 3.0.0.7

### <span id="sept_14_breaking_changes"></span></a>Dernières modifications

-   L'entité **Origin** a été renommée en [StreamingEndpoint][StreamingEndpoint].
-   Le comportement par défaut, lorsque le **portail de gestion Azure** est utilisé pour encoder puis publier les fichiers MP4, a changé.

    Auparavant, lorsque vous utilisiez le portail de gestion pour publier un élément multimédia vidéo MP4 correspondant à un seul fichier, une URL SAS était créée (les URL SAS vous permettent de télécharger la vidéo depuis un stockage d'objets blob). Aujourd'hui, lorsque vous vous servez du portail de gestion pour encoder, puis publier ce type d'élément, l'URL générée pointe vers un point de terminaison de diffusion en continu Azure Media Services. Cette modification n'a pas d'incidence sur les vidéos MP4 qui sont directement téléchargées vers Media Services et publiées sans être encodées par Azure Media Services.

    Actuellement, vous disposez des deux options suivantes pour résoudre le problème.

    -   Activer les unités de diffusion en continu et utiliser la mise en package dynamique pour diffuser l'élément multimédia .mp4 sous forme d'une présentation de diffusion en continu lisse.

    -   Créer une URL SAS pour télécharger (ou lire de manière progressive) le fichier .mp4. Pour plus d'informations sur la création d'un localisateur SAS, consultez la page [Distribution de contenu][Distribution de contenu].

### <span id="sept_14_GA_changes"></span></a>Nouvelles fonctionnalités\\nouveaux scénarios intégrés à la version grand public

-   **Processeur d'indexation multimédia**. Pour plus d'informations, consultez la page [Indexation des fichiers multimédias avec Azure Media Indexer][Indexation des fichiers multimédias avec Azure Media Indexer].

-   L'entité [StreamingEndpoint][StreamingEndpoint] vous permet désormais d'ajouter des noms de domaine (d'hôte) personnalisés.

    Pour utiliser un nom de domaine personnalisé comme nom de point de terminaison de diffusion en continu Media Services, vous devez ajouter des noms d'hôte personnalisés à votre point de terminaison de diffusion en continu. Utilisez les API REST de Media Services ou le Kit de développement logiciel (SDK) pour .NET pour ajouter des noms d'hôte personnalisés.

    Les considérations suivantes s'appliquent :

    -   Vous devez être propriétaire du nom de domaine personnalisé.

    -   La propriété du nom de domaine doit être validée par Azure Media Services.

    Pour plus d'informations, reportez-vous à la propriété **CustomHostNames** dans la rubrique [StreamingEndpoint][StreamingEndpoint].

### <span id="sept_14_preview_changes"></span></a>Nouvelles fonctionnalités\\nouveaux scénarios intégrés à la version préliminaire publique

-   Aperçu de la diffusion en continu. Pour plus d'informations, consultez la page [Utilisation de la diffusion en continu Azure Media Services][Utilisation de la diffusion en continu Azure Media Services].

-   Service de distribution des clés. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés][Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés].

-   Chiffrement dynamique AES. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés][Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés].

-   Service de distribution des licences PlayReady. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady][Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady].

-   Chiffrement dynamique PlayReady. Pour plus d'informations, consultez la page [Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady][Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady].

-   Modèle de licence PlayReady de Media Services. Pour plus d'informations, consultez la page [Présentation du modèle de licence PlayReady de Media Services][Présentation du modèle de licence PlayReady de Media Services].

-   Diffusion en continu d'éléments multimédias à chiffrement de stockage. Pour plus d'informations, consultez la page [Diffusion en continu de contenu à chiffrement de stockage][Diffusion en continu de contenu à chiffrement de stockage].

## <span id="august_changes_14"></span></a>Version d'août 2014

Lorsque vous encodez un élément multimédia, un élément multimédia de sortie est créé à la fin du travail d'encodage. Jusqu'à cette version, l'encodeur Media Services produisait des métadonnées sur les éléments multimédias de sortie. À partir de cette version, l'encodeur produit également des métadonnées sur les éléments multimédias d'entrée. Pour plus d'informations, consultez les rubriques [Métadonnées d'entrée][Métadonnées d'entrée] et [Métadonnées de sortie][Métadonnées de sortie].

## <span id="july_changes_14"></span></a>Version de juillet 2014

Plusieurs bogues ont été résolus pour le gestionnaire de package et le chiffreur d'Azure Media Services :

-   Seul le son est lu lors du transcodage/multiplexage (transmuxing) d'un élément multimédia d'archive actif pour la diffusion en continu HTTP (HLS). Le problème a été résolu et le son et la vidéo sont tous les deux lus.

-   Lors de la mise en package d'un élément multimédia pour la diffusion en continu HTTP (HLS) et le chiffrement d'enveloppe AES 128 bits, les flux packagés ne sont pas lus sur les appareils Android. Ce bogue a été résolu et le flux empaqueté est lu sur les appareils Android qui prennent en charge la diffusion en continu HTTP (HLS).

## <span id="may_changes_14"></span></a>Version de mai 2014

### <span id="may_14_changes"></span></a>Mises à jour générales de Media Services

Vous pouvez maintenant utiliser la [mise en package dynamique][mise en package dynamique] pour la diffusion en continu HTTP (HLS) v3. Pour la diffusion en continu HTTP (HLS) v3, ajoutez le format suivant au chemin d'accès du localisateur d'origine : \*.ism/manifest(format=m3u8-aapl-v3). Pour plus d'informations, consultez le [blog de Nick Drouin][blog de Nick Drouin].

Désormais, la mise en package dynamique prend également en charge la transmission du format HLS (v3 et v4) chiffré avec PlayReady sur la base de la diffusion en continu lisse statiquement chiffrée avec PlayReady. Pour des informations sur la façon de chiffrer la diffusion en continu lisse avec PlayReady, consultez la page [Protection de la diffusion en continu lisse avec PlayReady][Protection de la diffusion en continu lisse avec PlayReady].

### <a name="may_14_donnet_changes"></a>Mises à jour du Kit de développement logiciel (SDK) .NET de Media Services

Les améliorations suivantes sont incluses dans la version 3.0.0.5 du Kit de développement logiciel (SDK) .NET de Media Services :

-   Vitesse supérieure et meilleure résilience pour le chargement/téléchargement des éléments multimédias.

-   Améliorations apportées à la logique de nouvelle tentative et à la gestion des exceptions temporaires :

    -   La détection des erreurs temporaires et la logique de nouvelle tentative ont été améliorées pour les exceptions déclenchées par l'interrogation, l'enregistrement des modifications et le chargement ou téléchargement de fichiers.

    -   En cas d'exceptions Web (par exemple, lors d'une demande de jeton ACS), vous noterez que les erreurs irrécupérables échouent plus rapidement maintenant.

Pour plus d’informations, consultez la page [Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) de Media Services pour .NET][Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) de Media Services pour .NET].

## <span id="april_changes_14"></span></a>Version de l'encodeur d'avril 2014

### <a name="april_14_enocer_changes"></a>Mises à jour de l'encodeur Media Services

-   Ajout de la prise en charge pour la réception de fichiers AVI créés à l'aide de l'éditeur non linéaire Grass Valley EDIUS où la vidéo est légèrement compressée à l'aide du codec Grass Valley HQ/HQX. Pour plus d'informations, consultez l'article [Grass Valley annonce EDIUS 7 Streaming Through the Cloud][Grass Valley annonce EDIUS 7 Streaming Through the Cloud].

-   Ajout de la prise en charge de la spécification de la convention d'affectation de noms pour les fichiers générés par l'encodeur multimédia. Pour plus d’informations, consultez la page [Contrôle des noms de fichier de sortie de l'encodeur Media Services][Contrôle des noms de fichier de sortie de l'encodeur Media Services].

-   Ajout de la prise en charge des superpositions vidéo et/ou audio. Pour plus d’informations, consultez la page [Création de superpositions][Création de superpositions].

-   Ajout de la prise en charge de l'assemblage de plusieurs séquences vidéo. Pour plus d'informations, consultez la page [Assemblage de séquences vidéo][Assemblage de séquences vidéo].

-   Correction d'un bogue lié au transcodage de fichiers MP4 dans lesquels la partie audio a été encodée avec MPEG-1 Audio Layer 3 (aka MP3).

## <span id="jan_feb_changes_14"></span></a>Versions de janvier\\février 2014

### <a name="jan_fab_14_donnet_changes"></a>Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1, 3.0.0.2 et 3.0.0.3

Les modifications introduites dans les versions 3.0.0.1 et 3.0.0.2 sont les suivantes :

-   Résolution de problèmes concernant l'utilisation de requêtes LINQ avec des instructions OrderBy.

-   Division des solutions de test dans [Github][Github] en tests unitaires et tests basés sur des scénarios.

Pour plus de détails sur les modifications, consultez la page [Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1 et 3.0.0.2][Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1 et 3.0.0.2].

Les modifications suivantes ont été apportées à la version 3.0.0.3 :

-   Dépendances du stockage Azure mises à niveau pour utiliser la version 3.0.3.0.

-   Problème de compatibilité descendante résolu pour les versions 3.0*.*.

## <span id="december_changes_13"></span></a>Version de décembre 2013

### <a name="dec_13_donnet_changes"></a>Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.0

> [AZURE.NOTE] Les versions 3.0.x.x ne présentent pas de compatibilité descendante avec les versions 2.4.x.x.

La dernière version du Kit de développement logiciel (SDK) Media Services est maintenant la version 3.0.0.0.0. Vous pouvez télécharger le dernier package à partir de Nuget ou obtenir les différents composants sur [Github][Github].

À compter de version 3.0.0.0 du Kit de développement logiciel (SDK) Media Services, vous pouvez réutiliser les [jetons ACS (Access Control Service) d'Azure Active Directory][jetons ACS (Access Control Service) d'Azure Active Directory]. Pour plus d'informations, consultez la section « Réutilisation des jetons du service de contrôle d'accès » dans la rubrique [Connexion à Media Services à l'aide du Kit de développement logiciel (SDK) Media Services pour .NET][Connexion à Media Services à l'aide du Kit de développement logiciel (SDK) Media Services pour .NET].

### <a name="dec_13_donnet_ext_changes"></a>Extensions du Kit de développement logiciel (SDK) Media Services pour .NET 2.0.0.0

Le package Extensions du Kit de développement logiciel (SDK) Media Services pour .NET est un ensemble de méthodes d'extension et de fonctions d'assistance qui simplifient le code et le développement avec Azure Media Services. Pour obtenir les dernières informations disponibles, consultez la page [Extensions du Kit de développement logiciel (SDK) Media Services pour .NET][Extensions du Kit de développement logiciel (SDK) Media Services pour .NET].

## <span id="november_changes_13"></span></a>Version de novembre 2013

### <a name="nov_13_donnet_changes"></a>Modifications apportées au Kit de développement logiciel (SDK) Azure Media Services pour .NET

À partir de cette version, le Kit de développement logiciel (SDK) Azure Media Services pour .NET gère toutes les erreurs temporaires qui peuvent se produire lors des appels adressés à la couche API REST de Media Services.

## <span id="august_changes_13"></span></a>Version d'août 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets PowerShell de Media Services incluses dans les outils SDK de Microsoft Azure

Les cmdlets PowerShell de Media Services suivantes sont désormais incluses dans [azure-sdk-tools][azure-sdk-tools].

-   Get-AzureMediaServices

    Par exemple : `Get-AzureMediaServicesAccount`.

-   New-AzureMediaServicesAccount

    Par exemple : `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

-   New-AzureMediaServicesKey

    Par exemple : `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

-   Remove-AzureMediaServicesAccount

    Par exemple : `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <span id="june_changes_13"></span></a>Version de juin 2013

### <a name="june_13_general_changes"></a>Modifications apportées à Azure Media Services

Les changements mentionnés dans cette section correspondent aux mises à jour incluses dans les versions de Media Services de juin 2013.

-   Possibilité de lier plusieurs comptes de stockage à un compte Media Service.

    StorageAccount

    Asset.StorageAccountName and Asset.StorageAccount

-   Possibilité de mettre à jour Job.Priority.

-   Entités et propriétés liées aux notifications :

    JobNotificationSubscription

    NotificationEndPoint

    Job

-   Asset.Uri

-   Locator.Name

### <a name="june_13_dotnet_changes"></a>Modifications apportées au Kit de développement logiciel (SDK) Azure Media Services pour .NET

Les modifications suivantes sont incluses dans les versions du Kit de développement logiciel (SDK) Media Services de juin 2013. Le dernier Kit de développement logiciel (SDK) Media Services est disponible sur GitHub.

-   À partir de la version 2.3.0.0, le Kit de développement logiciel (SDK) Media Services prend en charge la liaison de plusieurs comptes de stockage à un compte Media Services. Les API suivantes prennent en charge cette fonctionnalité :

    Le type IStorageAccount type.

    La propriété Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.

    La propriété StorageAccount.

    La propriété StorageAccountName.

    Pour plus d'informations, consultez la page [Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage][Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage].

-   API liées aux notifications. À partir de la version 2.2.0.0, vous avez la possibilité d'écouter les notifications du service de stockage de files d'attente Azure. Pour plus d'informations, consultez la page [Gestion des notifications de travaux de Media Services][Gestion des notifications de travaux de Media Services].

    La propriété Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.

    Le type Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.

    Le type Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.

    Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.

    Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.

    Le type Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

-   Dépendance par rapport au Kit de développement logiciel (SDK) du client Azure Storage 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

-   Dépendance par rapport à OData 5.5 (Microsoft.Data.OData.dll).

## <span id="december_changes_12"></span></a>Version de décembre 2012

### <a name="dec_12_dotnet_changes"></a>Modifications apportées au Kit de développement logiciel (SDK) Azure Media Services pour .NET

-   Intellisense : ajout de la documentation Intellisense manquante pour de nombreux types.

-   Microsoft.Practices.TransientFaultHandling.Core : résolution d'un problème dû au fait que le Kit de développement logiciel (SDK) dépendait encore d'une ancienne version de cet assembly. Il fait dorénavant référence à la version 5.1.1209.1 de cet assembly.

Correctifs pour les problèmes détectés dans le Kit de développement logiciel (SDK) de novembre 2012 :

-   IAsset.Locators.Count : ce nombre est maintenant indiqué correctement sur les nouvelles interfaces IAsset après la suppression de tous les localisateurs.

-   IAssetFile.ContentFileSize : cette valeur est maintenant définie correctement après un téléchargement par IAssetFile.Upload(filepath).

-   IAssetFile.ContentFileSize : cette propriété peut désormais être définie lors de la création d'un fichier multimédia. Elle était jusqu'à présent en lecture seule.

-   IAssetFile.Upload(filepath) : un problème a été résolu avec cette méthode de téléchargement synchrone qui générait l'erreur suivante lorsque plusieurs fichiers étaient téléchargés vers l'élément multimédia : « Le serveur n'a pas pu authentifier la demande. Vérifiez que la valeur de l'en-tête d'autorisation est formée correctement, avec la signature. »

-   IAssetFile.UploadAsync : résolution du problème qui empêchait de télécharger simultanément plus de 5 fichiers.

-   IAssetFile.UploadProgressChanged : cet événement est désormais fourni par le Kit de développement logiciel (SDK).

-   IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken) : cette surcharge de méthode est désormais fournie.

-   IAssetFile.DownloadAsync : résolution du problème qui empêchait de télécharger simultanément plus de 5 fichiers.

-   IAssetFile.Delete() : résolution d'un problème concernant l'appel de la méthode delete, qui pouvait lever une exception si aucun fichier n'était téléchargé pour IAssetFile.

-   Travaux : résolution d'un problème concernant le chaînage d'une « tâche MP4 vers diffusion en continu lisse » avec une « tâche de protection PlayReady » à l’aide d'un modèle de travail, qui n'aboutissait à la création d'aucune tâche.

-   EncryptionUtils.GetCertificateFromStore() : cette méthode ne lève plus une exception de référence nulle suite à l'échec de la recherche du certificat en raison de problèmes de configuration du certificat.

## <span id="november_changes_12"></span></a>Version de novembre 2012

Les changements mentionnés dans cette section étaient des mises à jour incluses dans le Kit de développement logiciel (SDK) de novembre 2012 (version 2.0.0.0). Ces changements peuvent nécessiter une modification ou une réécriture du code écrit pour la version préliminaire du Kit de développement logiciel (SDK) de juin 2012.

-   Éléments multimédias

    IAsset.Create(assetName) est la SEULE fonction de création d'éléments multimédias. IAsset.Create ne télécharge plus les fichiers dans le cadre de l'appel de la méthode. Utilisez IAssetFile pour le téléchargement.

    La méthode IAsset.Publish et la valeur d'énumération AssetState.Publish ont été supprimées du Kit de développement logiciel (SDK) Services. Tous les codes qui dépendent de cette valeur doivent être réécrits.

-   FileInfo

    Cette classe a été supprimée et remplacée par IAssetFile.

    IAssetFiles

    IAssetFile remplace FileInfo et présente un comportement différent. Pour l'utiliser, vous devez instancier l'objet IAssetFiles, puis effectuer un téléchargement de fichier à l'aide du Kit de développement logiciel (SDK) Media Services ou du Kit de développement logiciel (SDK) Azure Storage. Les surcharges IAssetFile.Upload suivantes peuvent être utilisées :

    -   IAssetFile.Upload(filepath) : méthode synchrone qui bloque le thread et qui est recommandée uniquement lorsque vous téléchargez un seul fichier.

    -   IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken) : méthode asynchrone. Il s'agit du mécanisme de téléchargement privilégié.

    Bogue connu : l'utilisation du cancellationToken annulera en effet le téléchargement ; cependant, il existe plusieurs états d'annulation des tâches possibles. Vous devez saisir et traiter les exceptions correctement.

-   Localisateurs

    Les versions spécifiques d'origine ont été retirées. Le localisateur context.Locators.CreateSasLocator(asset, accessPolicy) spécifique à SAS sera marqué comme obsolète, ou supprimé par GA. Voir la section Localisateurs sous Nouvelles fonctionnalités pour connaître le comportement mis à jour.

## <span id="june_changes_12"></span></a>Version préliminaire de juin 2012

La fonctionnalité suivante est une nouveauté de la version de novembre du Kit de développement logiciel (SDK).

-   Suppression des entités

    Les objets IAsset, IAssetFile, ILocator, IAccessPolicy et IContentKey sont désormais supprimés au niveau de l'objet, c'est-à-dire IObject.Delete(), au lieu d'exiger une suppression dans la Collection : cloudMediaContext.ObjCollection.Delete(objInstance).

-   Localisateurs

    Les localisateurs doivent maintenant être créés avec la méthode CreateLocator et utiliser les valeurs d'énumération LocatorType.SAS ou LocatorType.OnDemandOrigin comme argument pour le type particulier de localisateur que vous souhaitez créer.

    De nouvelles propriétés ont été ajoutées aux localisateurs afin de faciliter l'obtention d'URI utilisables pour votre contenu. Cette nouvelle conception des localisateurs était destinée à fournir une plus grande flexibilité pour une extensibilité tierce ultérieure, et à faciliter davantage l'utilisation pour les applications clientes multimédias.

-   Prise en charge de la méthode asynchrone

    La prise en charge asynchrone a été ajoutée à toutes les méthodes.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Forum MSDN sur Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MediaServices
  [Problèmes actuellement connus]: #issues
  [Historique des versions de l'API REST]: #rest_version_history
  [Version d'octobre 2014]: #october_changes_14
  [Version de septembre 2014]: #september_changes_14
  [Version d'août 2014]: #august_changes_14
  [Version de juillet 2014]: #july_changes_14
  [Version de mai 2014]: #may_changes_14
  [Version d'avril 2014]: #april_changes_14
  [Versions de janvier/février 2014]: #jan_feb_changes_14
  [Version de décembre 2014]: #december_changes_13
  [Version de novembre 2013]: #november_changes_13
  [Version d'août 2013]: #august_changes_13
  [Version de juin 2013]: #june_changes_13
  [Version de décembre 2012]: #december_changes_12
  [Version de novembre 2012]: #november_changes_12
  [Version préliminaire de juin 2012]: #june_changes_12
  [Référence de l'API REST d'Azure Media Services]: http://msdn.microsoft.com/fr-fr/library/azure/hh973617.aspx
  [Media Services - Tarification]: http://azure.microsoft.com/fr-fr/pricing/details/media-services/
  [StreamingEndpoint]: http://msdn.microsoft.com/fr-fr/library/azure/dn783468.aspx
  [Distribution de contenu]: http://msdn.microsoft.com/fr-fr/library/azure/hh973618.aspx
  [Indexation des fichiers multimédias avec Azure Media Indexer]: http://msdn.microsoft.com/fr-fr/library/azure/dn783455.aspx
  [Utilisation de la diffusion en continu Azure Media Services]: http://msdn.microsoft.com/fr-fr/library/azure/dn783466.aspx
  [Utilisation du chiffrement dynamique AES-128 et du service de distribution des clés]: http://msdn.microsoft.com/fr-fr/library/azure/dn783457.aspx
  [Utilisation du chiffrement dynamique et du service de distribution des licences PlayReady]: http://msdn.microsoft.com/fr-fr/library/azure/dn783467.aspx
  [Présentation du modèle de licence PlayReady de Media Services]: http://msdn.microsoft.com/fr-fr/library/azure/dn783459.aspx
  [Diffusion en continu de contenu à chiffrement de stockage]: http://msdn.microsoft.com/fr-fr/library/azure/dn783451.aspx
  [Métadonnées d'entrée]: http://msdn.microsoft.com/fr-fr/library/azure/dn783120.aspx
  [Métadonnées de sortie]: http://msdn.microsoft.com/fr-fr/library/azure/dn783217.aspx
  [mise en package dynamique]: http://msdn.microsoft.com/fr-fr/library/azure/jj889436.aspx
  [blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
  [Protection de la diffusion en continu lisse avec PlayReady]: http://msdn.microsoft.com/fr-fr/library/azure/dn189154.aspx
  [Logique de nouvelle tentative dans le Kit de développement logiciel (SDK) de Media Services pour .NET]: http://msdn.microsoft.com/fr-fr/library/azure/dn745650.aspx
  [Grass Valley annonce EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
  [Contrôle des noms de fichier de sortie de l'encodeur Media Services]: http://msdn.microsoft.com/fr-fr/library/azure/dn303341.aspx
  [Création de superpositions]: http://msdn.microsoft.com/fr-fr/library/azure/dn640496.aspx
  [Assemblage de séquences vidéo]: http://msdn.microsoft.com/fr-fr/library/azure/dn640504.aspx
  [Github]: https://github.com/Azure/azure-sdk-for-media-services
  [Kit de développement logiciel (SDK) Azure Media Services pour .NET 3.0.0.1 et 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
  [jetons ACS (Access Control Service) d'Azure Active Directory]: http://msdn.microsoft.com/fr-fr/library/hh147631.aspx
  [Connexion à Media Services à l'aide du Kit de développement logiciel (SDK) Media Services pour .NET]: http://msdn.microsoft.com/fr-fr/library/azure/jj129571.aspx
  [Extensions du Kit de développement logiciel (SDK) Media Services pour .NET]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
  [azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
  [Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage]: http://msdn.microsoft.com/fr-fr/library/azure/dn271889.aspx
  [Gestion des notifications de travaux de Media Services]: http://msdn.microsoft.com/fr-fr/library/azure/dn261241.aspx
