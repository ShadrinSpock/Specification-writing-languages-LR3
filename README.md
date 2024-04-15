# LR3-diagrams-for-melnoma-detection-web-app

## Диаграмма состояний ##

Диаграмма состояний отображает различные состояния, через которые проходит приложение, начиная от загрузки изображения до окончательного диагноза. Она визуализирует изменения состояний системы в зависимости от результатов проверок и обработки данных, что помогает понять логику работы приложения и обработку ошибок.

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

Диаграмма классов показывает структуру веб-приложения, включая классы и их взаимосвязи, которые участвуют в процессе детекции меланомы. Классы включают контроллер, сервис для работы с изображениями, обработчик изображений, модель нейронной сети и детектор меланомы.

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

Сгенерированный программный код для диаграммы классов на языке Python:

```
from flask import Flask, request, jsonify
from keras.models import load_model
from keras.preprocessing import image
from PIL import Image
import numpy as np
import os

app = Flask(__name__)

class ImageProcessor:
    def preprocess_image(self, img):
        img = img.resize((224, 224))  # Assuming the model expects 224x224 images
        img_array = image.img_to_array(img)
        img_array_expanded = np.expand_dims(img_array, axis=0)
        return img_array_expanded / 255.0

class NeuralNetworkModel:
    def __init__(self, model_path):
        self.model_path = model_path
        self.model = None

    def load_model(self):
        self.model = load_model(self.model_path)

    def predict(self, processed_image):
        return self.model.predict(processed_image)[0][0]

class MelanomaDetector:
    def __init__(self, processor, model):
        self.processor = processor
        self.model = model

    def detect_melanoma(self, img):
        processed_image = self.processor.preprocess_image(img)
        prediction = self.model.predict(processed_image)
        return prediction > 0.5

# Initialize components
model_path = 'path_to_your_model.h5'
processor = ImageProcessor()
model = NeuralNetworkModel(model_path)
model.load_model()
detector = MelanomaDetector(processor, model)

@app.route('/upload', methods=['POST'])
def upload_image():
    if 'file' not in request.files:
        return jsonify({'error': 'No file part'})
    
    file = request.files['file']
    if file.filename == '':
        return jsonify({'error': 'No selected file'})
    
    if file and allowed_file(file.filename):
        img = Image.open(file.stream)
        result = detector.detect_melanoma(img)
        return jsonify({'melanoma_detected': result})
    
    return jsonify({'error': 'Invalid file'})

def allowed_file(filename):
    return '.' in filename and filename.rsplit('.', 1)[1].lower() in {'png', 'jpg', 'jpeg'}

if __name__ == '__main__':
    app.run(debug=True)
```

## Диаграмма последовательности ##

Диаграмма последовательности демонстрирует взаимодействие между компонентами системы в процессе обработки изображения. Она начинается с загрузки изображения пользователем и заканчивается выводом результатов детекции меланомы, подчеркивая коммуникацию между веб-интерфейсом, контроллером, сервисами обработки и моделями.

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

Диаграмма деятельности описывает шаги процесса веб-приложения от загрузки изображения пользователем до получения результатов о наличии меланомы. Эта диаграмма подчеркивает последовательность операций и условные переходы, зависящие от успешности загрузки файла и его типа.

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
