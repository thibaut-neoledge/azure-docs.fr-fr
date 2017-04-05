# [Vue d'ensemble](media-services-overview.md)
## [Concepts ](media-services-concepts.md)

# Prise en main
## [Créer et gérer un compte](media-services-portal-create-account.md)
## [Configurer votre environnement de développement](media-services-set-up-computer.md)
###[.NET](media-services-dotnet-how-to-use.md)
###[REST](media-services-rest-how-to-use.md)  
## Se connecter par programme
### [.NET](media-services-dotnet-connect-programmatically.md)
### [REST](media-services-rest-connect-programmatically.md)

## Proposer la vidéo à la demande
### [Portail](media-services-portal-vod-get-started.md)
### [Kit SDK .NET](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Exécuter la vidéo en flux continu
### [Portail](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procédure
## Gérer
### Comptes
#### [PowerShell](media-services-manage-with-powershell.md)
#### [REST](/rest/api/media/mediaservice)
### Entités
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Points de terminaison de streaming](media-services-streaming-endpoints-overview.md)
#### [Portail](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Storage
#### [Mettre à jour Media Services après la substitution de clés d’accès de stockage](media-services-roll-storage-access-keys.md)
#### [Gérer les éléments multimédias de plusieurs comptes de stockage](meda-services-managing-multiple-storage-accounts.md)
### [Quotas et limitations](media-services-quotas-and-limitations.md)

## Téléchargement de contenu
### Charger des fichiers dans un compte
#### [Portail](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Télécharger des fichiers volumineux avec Aspera](media-services-upload-files-with-aspera.md)
### [Charger des fichiers avec StorSimple](media-services-upload-files-from-storsimple.md)
### [Copier des objets blob existants](media-services-copying-existing-blob.md)

## [Encodage de contenu](media-services-encode-asset.md)
### [Comparaison des encodeurs](media-services-compare-encoders.md)
### [Gérer la vitesse et la simultanéité de votre encodage](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Codecs et formats standard de l’encodeur multimédia](media-services-media-encoder-standard-formats.md)
#### [Utiliser MES pour générer automatiquement une échelle des vitesses de transmission](media-services-autogen-bitrate-ladder-with-mes.md)
#### Encoder avec Media Encoder Standard
##### [Portail](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Fonctionnalités d’encodage avancées avec MES](media-services-advanced-encoding-with-mes.md)
##### [Personnaliser des présélections Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Génération de miniatures à l’aide de Media Encoder Standard avec .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Rogner des vidéos avec l’encodeur multimédia standard](media-services-crop-video.md)
#### Schémas MES
##### [Schéma Media Encoder Standard](media-services-mes-schema.md)
##### [Métadonnées d’entrée](media-services-input-metadata-schema.md)
##### [Métadonnées de sortie](media-services-output-metadata-schema.md)
#### [Présélections MES](media-services-mes-presets-overview.md) 
##### [H264 – Vitesse de transmission multiple – 1 080 pixels – Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 – Vitesse de transmission multiple – 1 080 pixels](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 – Vitesse de transmission multiple – 16 x 9 SD – Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 – Vitesse de transmission multiple – 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 – Vitesse de transmission multiple – 16 x 9 pour iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 – Vitesse de transmission multiple – 4 K – Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 – Vitesse de transmission multiple – 4 K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 – Vitesse de transmission multiple – 4 x 3 SD – Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 – Vitesse de transmission multiple – 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 – Vitesse de transmission multiple – 4 x 3 pour iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 – Vitesse de transmission multiple – 720 pixels – Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 – Vitesse de transmission multiple – 720 pixels](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 – Vitesse de transmission unique – 1 080 pixels – Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 – Vitesse de transmission unique – 1 080 pixels](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 – Vitesse de transmission unique – 16 x 9 SD – Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 – Vitesse de transmission unique – 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 – Vitesse de transmission unique – 4 K – Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 – Vitesse de transmission unique – 4 K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 – Vitesse de transmission unique – 4 x 3 SD – Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 – Vitesse de transmission unique – 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 – Vitesse de transmission unique – 720 pixels – Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 – Vitesse de transmission unique – 720 pixels](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 – Vitesse de transmission unique – 720 pixels pour Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 – Vitesse de transmission unique haute qualité SD pour Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 – Vitesse de transmission unique qualité faible SD pour Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Media Encoder Premium Workflow
#### [Codecs et formats de Media Encoder Premium Workflow](media-services-premium-workflow-encoder-formats.md)
#### Encoder avec Media Encoder Premium Workflow.
##### [Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md)
##### [Didacticiels de workflow Media Encoder Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Créer des flux de travail d’encodage avancé avec le Concepteur de flux de travail](media-services-workflow-designer.md)
##### [Workflow Premium avec plusieurs entrées](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Créer une tâche qui génère des segments fMP4](media-services-generate-fmp4-chunks.md)
### Processeurs multimédias
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Codes d’erreur](media-services-encoding-error-codes.md)
### Déconseillé
#### [Empaquetage et chiffrement statiques](media-services-static-packaging.md)

## [Diffuser en direct](media-services-manage-channels-overview.md)
### [Encodeurs locaux](media-services-live-streaming-with-onprem-encoders.md)
#### [Portail](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/channel)
### [Streaming en direct à l’aide d’un encodeur cloud](media-services-manage-live-encoder-enabled-channels.md)
#### [Portail](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Configurer des encodeurs locaux pour une utilisation avec un encodeur cloud](media-services-live-encoders-overview.md)
#### [Encodeur Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Encodeur FMLE](media-services-configure-fmle-live-encoder.md)
#### [Encodeur NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Encodeur Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Traiter des opérations de longue durée](media-services-dotnet-long-operations.md)
### [Spécification d’ingestion en direct au format MP4 fragmenté](media-services-fmp4-live-ingest-overview.md)

## [Protéger](media-services-content-protection-overview.md)
### [Configurer la protection du contenu dans le portail](media-services-portal-protect-content.md)
### [Configurer une clé en clair AES-128 pour votre diffusion](media-services-protect-with-aes128.md)
### [Utiliser REST pour chiffrer votre contenu avec le chiffrement de stockage](media-services-rest-storage-encryption.md)
### [Présentation du modèle de licence PlayReady de Media Services](media-services-playready-license-template-overview.md)
### [Vue d’ensemble du modèle de licence Widevine](media-services-widevine-license-template-overview.md)
### [Remise de licence DRM](media-services-deliver-keys-and-licenses.md)
### [Utilisation de partenaires pour fournir des licences Widevine à Media Services](media-services-licenses-partner-integration.md)
### [Utilisation du chiffrement commun dynamique PlayReady et/ou Widevine](media-services-protect-with-drm.md)
### [Diffuser en continu votre contenu HLS protégé avec Apple FairPlay](media-services-protect-hls-with-fairplay.md)
### [CENC avec Multi-DRM et Access Control](media-services-cenc-with-multidrm-access-control.md)
### [Utilisation d’Axinom pour fournir des licences Widevine à Media Services](media-services-axinom-integration.md)
### [Utilisation de castLabs pour fournir des licences Widevine à Media Services](media-services-castlabs-integration.md)

### Distribution d’éléments multimédia
#### Configurer des stratégies de distribution d’éléments multimédia
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Créer des ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Configuration de la stratégie d’autorisation de clé de contenu
#### [Portail](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [Analyser](media-services-analytics-overview.md)
### [Analyser les supports à l’aide du portail](media-services-portal-analyze.md)
### [Traiter avec Indexer 2](media-services-process-content-with-indexer2.md)
### [Traiter avec Indexer](media-services-index-content.md)
### [Traiter avec Hyperlapse](media-services-hyperlapse-content.md)
### [Traiter avec Face Detector](media-services-face-and-emotion-detection.md)
### [Traiter avec Motion Detector](media-services-motion-detection.md)
### [Utiliser Face Redactor](media-services-face-redaction.md)
#### [Procédure pas à pas de rédaction de face](media-services-redactor-walkthrough.md)
### [Traiter avec Video Thumbnails](media-services-video-summarization.md)
### [Traiter avec OCR](media-services-video-optical-character-recognition.md)

## [Configurer la télémétrie](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Mettre à l'échelle
### [Traitement multimédia](media-services-scale-media-processing-overview.md)
#### [Portail](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
### Points de terminaison de diffusion en continu
#### [Portail](media-services-portal-scale-streaming-endpoints.md)

## [Distribuer le contenu](media-services-deliver-content-overview.md)
### [mise en package dynamique](media-services-dynamic-packaging-overview.md)
### [Présentation des filtres et des manifestes dynamiques](media-services-dynamic-manifest-overview.md)
#### [Créer des filtres avec .NET](media-services-dotnet-dynamic-manifest.md)
#### [Créer des filtres avec REST](media-services-rest-dynamic-manifest.md)
### [Stratégie de mise en cache CDN dans l'extension Media Services](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publication de contenu
#### [Portail](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Distribuer par téléchargement](media-services-deliver-asset-download.md)
### [Scénario de basculement de diffusion en continu](media-services-implement-failover.md)

## Utiliser
### [Lecture de contenu multimédia avec des lecteurs existants](media-services-playback-content-with-existing-players.md)
### [Lecture de contenu multimédia avec le lecteur multimédia](media-services-develop-video-players.md)
### Autres options de lecture
#### [Application Windows Store de diffusion en continu lisse](media-services-build-smooth-streaming-apps.md)
#### [Application HTML5 avec DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Lecteurs Adobe Open Source Media Framework](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Insérer des publicités côté client](media-services-inserting-ads-on-client-side.md)
### [Licence du kit de portage du client de Smooth Streaming Microsoft](media-services-sspk.md)

## Integrate
### [Utiliser Azure Functions avec Media Services](media-services-dotnet-how-to-use-azure-functions.md)
### [Azure Functions avec des exemples Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Surveiller
### Vérifier la progression des travaux
#### [REST](media-services-rest-check-job-progress.md)
#### [Portail](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Surveiller les notifications de travaux avec le Stockage File d’attente](media-services-dotnet-check-job-progress-with-queues.md)
### [Surveiller les notifications de travaux avec WebHooks](media-services-dotnet-check-job-progress-with-webhooks.md)

## Résolution des problèmes
### [Forum Aux Questions (FAQ)](media-services-frequently-asked-questions.md)
### [Guide de dépannage de la vidéo en flux continu](media-services-troubleshooting-live-streaming.md)
### [Codes d’erreur](media-services-error-codes.md)
### [Logique de nouvelle tentative](media-services-retry-logic-in-dotnet-sdk.md)

# Référence
## [PowerShell (Resource Manager)](/powershell/resourcemanager/azurerm.media/v0.3.1/azurerm.media)
## [PowerShell (gestion des services)](/powershell/servicemanagement/azure.compute/v3.1.0/azure.compute)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media)

# les ressources
## [Notes de publication](media-services-release-notes.md)
## [Tarification](https://azure.microsoft.com/pricing/details/media-services/)
## [Communauté Azure Media Services](media-services-community.md)
## [Vidéos](https://azure.microsoft.com/resources/videos/index/?services=media-services)
