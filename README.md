# LR3-diagram

## Диаграмма состояний ##

Диаграмма состояний UML представляет различные состояния и переходы в Telegram-боте для консультаций клиентов косметологической клиники. Она включает состояния, такие как просмотр новостей, просмотр услуг и выбор специалиста. Переходы показывают, как пользователь может перемещаться между этими состояниями на основе своих действий в Telegram-боте.

Диаграмма состоний Telegram-бота для консультаций клиентов косметологической клиники

![Изображение](https://www.plantuml.com/plantuml/png/XPCnJyCm48Lt_ugRaIhs3gW385K2L1NAWXYi-1fETSwLunGLn7_75pVqY2BerBxtlOlVrLcqoSRKDTwObujNcCskODdO6ZTx7wmp9ji9jGIV1l9dW6_9unehf0CwcCEcnGXT3xP0aXBwDpN4dgqdPnC57XWbvLc7yWDXcnr8npsQ2R0ScNjOdAVCvJ1jWOeucHbghcAei6s9wv8f2OcCPqf6MXTlb1NlFZZqTxbu5ipNi3cjZbOdQc_3lFyEZ5sq7XZJUuWxQCIJX28bjuheg4hbp4MMVQCxFrKXwfag6tZ9-Vnrp0K4c3HABVIMGsElCM6Lq9KzDISLFEtG7_yUyH3-6TAn6ZDczU97exGjvVKrdSCDftYyX4TxmDDLr7_66LWo9RAUFl2qyg68XjOTm_wd85XVXqZfhJ5cWUpa_Va6)

Код представлен ниже:
```
@startuml

[*] --> ImageUpload

state ImageUpload {
    [*] --> FileReceived : User uploads image
    FileReceived --> FileValidation
    FileValidation : validate file type

    FileValidation --> InvalidFileType : if invalid
    FileValidation --> ImageProcessing : if valid

    InvalidFileType --> [*]
    ImageProcessing --> ModelLoading
}

state ModelLoading {
    [*] --> LoadModel : Load neural network model
    LoadModel --> Predicting : Model loaded
}

state Predicting {
    [*] --> PredictionInProgress
    PredictionInProgress --> MelanomaDetected : if melanoma likely
    PredictionInProgress --> NoMelanomaDetected : if melanoma unlikely
    MelanomaDetected --> [*]
    NoMelanomaDetected --> [*]
}

ImageUpload : Entry/ Save image
ModelLoading : Entry/ Initialize model
Predicting : Entry/ Run prediction algorithm

@enduml

```

## Диаграмма классов ##

Диаграмма классов представляет различные классы и их взаимосвязи в Telegram-боте для консультаций клиентов косметологической клиники. К нему относятся классы ТелеграмБот, Новость, Услуга и Специалист, а также их атрибуты и методы. Telegram-бот имеет ассоциации с новостями, услугами и специалистами, что позволяет пользователям взаимодействовать с ними с помощью методов бота.

Диаграмма классов Telegram-бота для консультаций клиентов косметологической клиники

![Изображение](https://www.plantuml.com/plantuml/png/TP8nJyD038Nt-nMtqGhr3tH07Rfqe48ip6xkjJtXtaMs0og8_qwIInJQY0onzFpyENxAHet4cWixz-2tECO4RsVuYaaLwlhgtt3O9OCSgOB_Thvz7fkQCuNTXKvOnEwzB7g8MZDTNw4DsqBwidG_Ra3r0o-IAwXc6M6re2xQN-3QJphAgWcqHoF4UzXdblVd7C0ZZ1BnzGk5D2CKQeYLnPmct9QC3gGO8MfPCB-Ey-dsiY8BJ28-CFJQ7QNTygTS1KpTKZt7MjrDfglLqtqoQzyevhRfGmV3tLXdcKVJcXKfg0zav4R4_-Rndc70RP12zvFy0W00)

Код представлен ниже:
```
@startuml

class WebController {
    + upload_image(file: File) -> str
}

class ImageService {
    + save_image(file: File) -> Image
    + get_image(image_id: str) -> Image
}

class ImageProcessor {
    + preprocess_image(image: Image) -> ndarray
}

class NeuralNetworkModel {
    + model_path: str
    + load_model()
    + predict(image: ndarray) -> float
}

class MelanomaDetector {
    + __init__(processor: ImageProcessor, model: NeuralNetworkModel)
    + detect_melanoma(image: Image) -> bool
}

WebController --> ImageService : uses
ImageService --> "1" ImageProcessor : <<use>>
ImageProcessor --> "1" NeuralNetworkModel : <<use>>
NeuralNetworkModel --> "1" MelanomaDetector : <<use>>
ImageService --> "1" MelanomaDetector : uses

@enduml

```

## Диаграмма последовательности ##

Диаграмма последовательности изображает взаимодействие между клиентом, Telegram-ботом и косметологической клиникой. Она показывает, как клиент взаимодействует с ботом для просмотра новостей, услуг, специалистов и совершения выбора, а бот общается с клиникой для получения соответствующей информации.

Диаграмма последовательности Telegram-бота для консультаций клиентов косметологической клиники

![Изображение](https://www.plantuml.com/plantuml/png/bPG_JyCm4CLtVufJftIes3kWIdGfKl-8KZ569ZwGXHD7jbF4joTs4XBx0YfR_FpkvwVdkXlhk75DgLZDZPE5h7dbO76nQ1R0BVYFUEi5Ns5NEJHllC3WQPKVWNeVTEMCLWhDO1wLsBqh-Jl26SrLzkIWzCASzsHqWTPgCxe7AM4Vi35SmG7TfpOViDS2LHZgDdezoF7W7XMlTCbXYmuBrnyrg8F8c2yALlTH6R26Iwqq5nQapyQYpM2UbD6wJyVpCzmrOGQoCBDal73ooXqcrR5ewMbZM-5eow-O1mOXHRsEYn0ggR45rGRhRZcFIujF02iQhSUXw81Cu6yiaYvjlgM9y9cN_KwMqCUWzDeI8TH8Vn6m1dyZUUaNsOH7hOnAi9gF7MYv1wCO6vY5tfwpDYXauKW7j-OQ4L9NkK7RAFV_SFJ6mntDCqc6vCxfE_cJDXqcZuIypoTqZQcWmq3sU3uUbbDQD4_UxyM6E5jfQyM_UWhRO2NyF-et)

Код представлен ниже:
```
@startuml
participant "User" as User
participant "Web Interface" as WebInterface
participant "Web Controller" as WebController
participant "Image Service" as ImageService
participant "Image Processor" as ImageProcessor
participant "Neural Network Model" as NeuralNetworkModel
participant "Melanoma Detector" as MelanomaDetector

User -> WebInterface : Uploads image
WebInterface -> WebController : POST /upload (image)
activate WebController

WebController -> ImageService : save_image(image)
activate ImageService
ImageService -> ImageProcessor : preprocess_image(image)
activate ImageProcessor
ImageProcessor --> ImageService : processed_image
deactivate ImageProcessor

ImageService -> MelanomaDetector : detect_melanoma(processed_image)
activate MelanomaDetector
MelanomaDetector -> NeuralNetworkModel : load_model()
activate NeuralNetworkModel
NeuralNetworkModel --> MelanomaDetector : model_loaded
deactivate NeuralNetworkModel

MelanomaDetector -> NeuralNetworkModel : predict(processed_image)
NeuralNetworkModel --> MelanomaDetector : prediction_result
deactivate NeuralNetworkModel

MelanomaDetector --> ImageService : detection_result
deactivate MelanomaDetector
ImageService --> WebController : detection_result
deactivate ImageService

WebController -> WebInterface : Return result (JSON)
deactivate WebController
WebInterface -> User : Display result
@enduml

```

## Диаграмма деятельности ##

Диаграмма деятельности отображает последовательность действий в Telegram-боте для консультаций клиентов косметологической клиники. Она начинается с приветственного сообщения, а затем, в зависимости от ввода клиента, либо показывает список новостей, услуг, специалистов, либо завершает сеанс

Диаграмма деятельности Telegram-бота для консультаций клиентов косметологической клиники

![Изображение](http://www.plantuml.com/plantuml/png/TP51JiCm44NtFiLNL-qLsWLTiAa4284um13_50jd7DcJLhqz41UQA69dZT-R-PhP5vDiGnVT-73RjyACeOz9V47ev4YSWk3CTmGrvbOQxfmBBTQ7WZP4NcdwkmtiWuhrXMNZW2bZbvuuIGmB2jY-oebrrguMdZFxd1gMCYi_9F5G3baYb7PE-HDTyemtpOV6C9IWn-cF0_xYT8oYgHDu6XjRnXvRlT26h5Wz_jBtLtfLEp8MOgrf8Jmb_Eke3stDCsqmqmywBkcsjD6jvaIPpfiVuPklj2kMUhUd-f_hVW40)

Код представлен ниже:
```
@startuml
start
:User uploads image via web interface;

if (Is file uploaded?) then (yes)
  if (Is file type valid?) then (yes)
    :Save image;
    :Preprocess image;
    :Load neural network model;
    :Predict using model;
    
    if (Is melanoma detected?) then (yes)
      :Return "Melanoma Detected";
    else (no)
      :Return "No Melanoma Detected";
    endif
    
  else (no)
    :Return "Invalid file type";
  endif
else (no)
  :Return "No file uploaded";
endif

stop
@enduml


```# Specification-writing-languages-LR3
