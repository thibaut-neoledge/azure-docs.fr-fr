---
title: "H264 – Vitesse de transmission unique -4K | Microsoft Docs"
description: "Cette rubrique offre une vue d’ensemble de la présélection de travaux **H264 - Vitesse de transmission unique -4K**."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 55bfd5b283e6159e0a06b3107de31d1722796bf1
ms.openlocfilehash: f47a3baf4504307fce0baf726980d58ec10ba629


---

# <a name="h264-single-bitrate-4k"></a>H264 - Vitesse de transmission unique -4 K
`Media Encoder Standard` définit un ensemble de présélections d’encodage à utiliser lors de la création de travaux d’encodage. Vous pouvez utiliser un `preset name` afin de spécifier le format dans lequel vous souhaitez encoder votre fichier multimédia. Sinon, vous pouvez créer vos propres présélections basées sur JSON ou XML (à l’aide de l’encodage UTF-8 ou UTF-16). Vous pouvez ensuite transmettre la présélection personnalisée à l’encodeur. Pour obtenir la liste de tous les noms de présélections pris en charge par cet encodeur `Media Encoder Standard`, consultez [Présélections de tâches pour Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Cette rubrique représente la présélection `H264 Single Bitrate 4K` aux formats XML et JSON.  
  
 Cette présélection produit un fichier MP4 unique avec une vitesse de transmission de 18 000 kbit/s, et de l’audio stéréo AAC. Pour plus d’informations sur le profil, la vitesse de transmission, la fréquence d’échantillonnage, etc. de cette présélection, examinez le format XML ou JSON défini ci-dessous. Pour consulter une explication de la signification des éléments des présélections et les valeurs valides pour chaque élément, consultez la rubrique [Media Encoder Standard schema](media-services-mes-schema.md) (Schéma Media Encoder Standard).  
  
> [!NOTE]
>  Pour les encodages 4K, vous devez obtenir le type d’unité réservée Premium. Pour plus d’informations, consultez [Mise à l’échelle de l’encodage](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 128,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```


<!--HONumber=Jan17_HO1-->


