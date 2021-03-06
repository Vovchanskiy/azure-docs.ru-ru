---
title: Использование Azure Media Video Thumbnail для формирования сводных данных видео | Microsoft Docs
description: Формирование сводных данных видео помогает создавать краткие видеоролики длинных видеозаписей путем автоматического выбора интересных фрагментов из исходного видеоматериала. Используйте эту функцию, чтобы представить краткий обзор длинной видеозаписи.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/26/2016
ms.author: milanga;juliako;

---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Использование Azure Media Video Thumbnail для формирования сводных данных видео
## <a name="overview"></a>Обзор
Обработчик мультимедиа **Azure Media Video Thumbnail** позволяет создавать сводку видео, которая предоставляет пользователям предварительный просмотр сводки длинного видео. Например, пользователи хотят просмотреть короткую "сводку видео", когда они наводят указатель мыши на эскиз. Настроив параметры **Azure Media Video Thumbnail** в предустановке конфигурации, вы можете использовать технологию обнаружения и объединения кадров обработчика мультимедиа для создания описательного миниклипа.  

Сейчас обработчик мультимедиа **Azure Media Video Thumbnail** доступен в предварительной версии.

В этой статье приводятся сведения об обработчике **Azure Media Video Thumbnail** и демонстрируется его использование с пакетом SDK служб мультимедиа для .NET.

## <a name="video-summary-example"></a>Пример сводки видео
Ниже приведены некоторые примеры возможностей обработчика мультимедиа Azure Media Video Thumbnails.

### <a name="original-video"></a>Исходное видео
[Исходное видео](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Результат — эскиз видео
[Результат — эскиз видео](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-(preset)"></a>Конфигурация задачи (предустановка)
При создании эскиза видео с помощью **Azure Media Video Thumbnails**необходимо указать предустановку конфигурации. Пример эскиза, приведенный выше, создан с помощью следующей базовой конфигурации JSON:

    {"version":"1.0"}

В ней можно изменить следующие параметры.

| Параметр | Описание |
| --- | --- |
| outputAudio |Указывает, есть ли в итоговом видео аудио. <br/>Допустимые значения: True или False. Значение по умолчанию — True. |
| fadeInFadeOut |Указывает, используются ли плавные переходы при перемещении отдельных эскизов.  <br/>Допустимые значения: True или False.  Значение по умолчанию — True. |
| maxMotionThumbnailDurationInSecs |Целое число, указывающее продолжительность видео, полученного в результате.  По умолчанию зависит от продолжительности исходного видео. |

Следующая таблица содержит продолжительность по умолчанию, когда параметр **maxMotionThumbnailInSecs** не используется.

|  |  |  |
| --- | --- | --- | --- | --- |
| Продолжительность видео |видео < 3 мин |3 мин < видео < 15 мин |
| Продолжительность эскиза |15 с (2–3 сцены) |30 с (3–5 сцен) |

Следующий объект JSON задает доступные параметры.

    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="sample-code"></a>Пример кода
В следующей программе показано, как выполнить следующие задачи.

1. Создать ресурс-контейнера и отправить в него файл мультимедиа.
2. Создать задание с задачей создания эскиза видео на основе файла конфигурации, содержащего следующую предустановку JSON. 
   
        {               
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }
3. Загрузить выходные файлы. 

### <a name=".net-code"></a>Код .NET
    using System;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Threading.Tasks;

    namespace VideoSummarization
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;

            static void Main(string[] args)
            {

                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

                // Use the following event handler to check job progress.  
                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

                // Launch the job.
                job.Submit();

                // Check job execution and wait for job to finish.
                Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

                progressJobTask.Wait();

                // If job state is Error, the event handling
                // method for job progress should log errors.  Here we check
                // for error state and exit if needed.
                if (job.State == JobState.Error)
                {
                    ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                    Console.WriteLine(string.Format("Error: {0}. {1}",
                                                    error.Code,
                                                    error.Message));
                    return null;
                }

                return job.OutputMediaAssets[0];
            }

            static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
            {
                IAsset asset = _context.Assets.Create(assetName, options);

                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                assetFile.Upload(filePath);

                return asset;
            }

            static void DownloadAsset(IAsset asset, string outputDirectory)
            {
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    file.Download(Path.Combine(outputDirectory, file.Name));
                }
            }

            static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors
                    .Where(p => p.Name == mediaProcessorName)
                    .ToList()
                    .OrderBy(p => new Version(p.Version))
                    .LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor",
                                                               mediaProcessorName));

                return processor;
            }

            static private void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine();
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
                        // LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

        }
    }

### <a name="video-thumbnail-output"></a>Результат — эскиз видео
[Результат — эскиз видео](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Связанные ссылки
[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!--HONumber=Oct16_HO2-->


