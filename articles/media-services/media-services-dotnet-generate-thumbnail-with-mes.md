---
title: ".NET で Media Encoder Standard を使用してサムネイルを生成する方法"
description: "このトピックでは、.NET で Media Encoder Standard を使用して、資産をエンコードするのと同時にサムネイルを生成する方法を説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f28c37b777bbd321c1c7ee8e7a18d92492a78d3e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>.NET で Media Encoder Standard を使用してサムネイルを生成する方法

Media Encoder Standard を使用してビデオ入力から 1 つまたは複数の [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)、または [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 画像ファイル形式のサムネイルを生成することができます。 画像のみを生成するタスクを送信するか、またはエンコーディングとサムネイルの生成を組み合わせることができます。 このトピックでは、そのようなシナリオ用のいくつかのサンプル XML および JSON サムネイル プリセットを提供します。 トピックの最後には、エンコーディング タスクを実行するための Media Services .NET SDK の使用方法を示す[サンプル コード](#code_sample)があります。

サンプルのプリセットで使用されている要素の詳細については、[Media Encoder Standard スキーマ](media-services-mes-schema.md)を参照してください。

必ず「 [考慮事項](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) 」セクションを確認してください。

## <a name="example--single-png-file"></a>例 – 単一の PNG ファイル

次の JSON および XML プリセットを用して、最初の数秒の入力ビデオから 1 つの出力 PNG ファイルを生成することができます。エンコーダーは、“興味深い” フレームを見つけ出すためにベストエフォートを試行します。 出力画像のサイズは 100% に設定され、これらは入力ビデオのサイズと一致しすることを意味します。 “Codecs” セクションでの “PngLayers” の使用と一致させるために “Outputs” でどのような “Format” 設定が必要かにも注意してください。 

### <a name="json-preset"></a>JSON プリセット

    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
    
### <a name="xml-preset"></a>XML プリセット

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="example--a-series-of-jpeg-images"></a>例 – 一連の JPEG 画像

次の JSON および XML プリセットは、入力タイムラインのタイムスタンプ、5%、15%、…、95%で 10 個の画像のセットを生成するために使用できます。画像のサイズは入力ビデオの 4 分の 1 になるように指定されます。

### <a name="json-preset"></a>JSON プリセット

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }

### <a name="xml-preset"></a>XML プリセット
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="example--one-image-at-a-specific-timestamp"></a>例 – 特定のタイムスタンプで 1 つの画像

次の JSON および XML プリセットは、入力ビデオの 30 秒の目盛りで 1 つの JPEG 画像を生成するために使用できます。 このプリセットは、入力の時間が 30 秒を超えることを想定しています (それ以外の場合、ジョブは失敗します)。

### <a name="json-preset"></a>JSON プリセット

    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
    
### <a name="xml-preset"></a>XML プリセット
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:02" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>

## <a id="code_sample"></a>例 - ビデオのエンコードとサムネイルの生成

次のコード サンプルでは、Media Services SDK を使用して次のタスクを実行します。

* エンコード ジョブを作成します。
* Media Encoder Standard エンコーダーの参照を取得します。
* エンコード用のプリセットとサムネイルの生成に必要な情報を含む、プリセット [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) または [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) を読み込みます。 この [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) または [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) はファイルに保存して、次のコードを使用してファイルを読み込みます。
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* 1 つのエンコード タスクをジョブに追加します。 
* エンコードする入力資産を指定します。
* エンコードされた資産が含まれる出力資産を作成します。
* ジョブの進行状況を確認するイベント ハンドラーを追加します。
* ジョブを送信します。

開発環境のセットアップ方法については、「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」を参照してください。

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace EncodeAndGenerateThumbnails
        {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

            private static CloudMediaContext _context = null;

            private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

            private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

            static void Main(string[] args)
            {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
            }

            private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
            {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
                case JobState.Canceled:
                case JobState.Error:

                // Cast sender as a job.
                IJob job = (IJob)sender;

                // Display or log error details as needed.
                break;
                default:
                break;
            }
            }

            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
            }
        }
        }

## <a id="json"></a>サムネイル JSON プリセット
スキーマの詳細については、 [この](https://msdn.microsoft.com/library/mt269962.aspx) トピックを参照してください。

    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="xml"></a>サムネイル XML プリセット
スキーマの詳細については、 [この](https://msdn.microsoft.com/library/mt269962.aspx) トピックを参照してください。
    
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>

## <a name="considerations"></a>考慮事項
次の考慮事項が適用されます。

* 明示的に Start、Step、Range でタイムスタンプを使用する場合、入力ソースは少なくとも 1 分であると仮定しています。
* Jpg/Png/BmpImage 要素には Start、Step、Range の文字列属性があり、次のように解釈できます。
  
  * 負ではない整数の場合は、フレーム番号 (例: "Start": "120")
  * % サフィックス付きで表現される場合は、ソース期間に対する相対値 (例: "Start": "15%")
  * HH:MM:SS… 形式で表現されている場合は、タイムスタンプ  ( 例: "Start" : "00:01:00")
    
    必要に応じて、表記法を混在させたり、一致させたりすることができます。
    
    また、Start は特殊なマクロの {Best} もサポートしています。このマクロは、コンテンツの最初の "関連する" フレームを決定しようと試みます。注: (Start が {Best} に設定されている場合、Step と Range は無視されます)
  * 既定: Start:{Best}
* 各画像形式の出力形式は明示的に指定する必要があります (Jpg/Png/BmpFormat)。 指定されている場合、JpgVideo は JpgFormat に、などと MES によって関連付けられます。 OutputFormat には新しい画像コーデック固有のマクロである {Index} が導入されました。このマクロは、画像出力形式を指定する場合に (1 度だけ) 指定する必要があります。

## <a name="next-steps"></a>次のステップ

エンコード ジョブが保留になっているときに、[ジョブの進行状況](media-services-check-job-progress.md)を確認できます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[Media Services Encoding の概要](media-services-encode-asset.md)


