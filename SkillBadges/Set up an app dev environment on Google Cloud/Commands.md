# Define região e zona especificida
export REGION="us-east4"
export ZONE="us-east4-c"

# Cria um bucket numa determinada região
gcloud storage buckets create gs://qwiklabs-gcp-03-b67838ab9299-bucket --location="$REGION"

# Criação do tópico Pub/Sub
gcloud pubsub topics create topic-memories-942

# Ativam os serviços do Google Cloud necessários para a execução do projeto
# Cloud Functions para processamento de imagem.
# Pub/Sub para gerenciamento de mensagens.
# Cloud Run para execução de contêineres (opcional caso se queira executar funções como contêineres).
# Storage para armazenamento das fotos.
# Eventarc para gerenciamento de eventos entre serviços.
gcloud services enable cloudfunctions.googleapis.com \
    pubsub.googleapis.com \
    run.googleapis.com \
    storage.googleapis.com \
    eventarc.googleapis.com

# Arquivo index.json
```javascript
const functions = require('@google-cloud/functions-framework');
const crc32 = require("fast-crc32c");
const { Storage } = require('@google-cloud/storage');
const gcs = new Storage();
const { PubSub } = require('@google-cloud/pubsub');
const imagemagick = require("imagemagick-stream");

functions.cloudEvent('memories-thumbnail-generator', cloudEvent => {
  const event = cloudEvent.data;

  console.log(`Event: ${event}`);
  console.log(`Hello ${event.bucket}`);

  const fileName = event.name;
  const bucketName = event.bucket;
  const size = "64x64";
  const bucket = gcs.bucket(bucketName);
  const topicName = "topic-memories-600";
  const pubsub = new PubSub();

  if (fileName.search("64x64_thumbnail") == -1) {
    var filename_split = fileName.split('.');
    var filename_ext = filename_split[filename_split.length - 1];
    var filename_without_ext = fileName.substring(0, fileName.length - filename_ext.length);

    if (filename_ext.toLowerCase() == 'png' || filename_ext.toLowerCase() == 'jpg') {
      console.log(`Processing Original: gs://${bucketName}/${fileName}`);

      const gcsObject = bucket.file(fileName);
      let newFilename = filename_without_ext + size + '_thumbnail.' + filename_ext;
      let gcsNewObject = bucket.file(newFilename);
      let srcStream = gcsObject.createReadStream();
      let dstStream = gcsNewObject.createWriteStream();
      let resize = imagemagick().resize(size).quality(90);

      srcStream.pipe(resize).pipe(dstStream);

      return new Promise((resolve, reject) => {
        dstStream
          .on("error", (err) => {
            console.log(`Error: ${err}`);
            reject(err);
          })
          .on("finish", () => {
            console.log(`Success: ${fileName} → ${newFilename}`);
            gcsNewObject.setMetadata({
              contentType: 'image/' + filename_ext.toLowerCase()
            }, function(err, apiResponse) {});
            pubsub
              .topic(topicName)
              .publisher()
              .publish(Buffer.from(newFilename))
              .then(messageId => {
                console.log(`Message ${messageId} published.`);
              })
              .catch(err => {
                console.error('ERROR:', err);
              });
          });
      });
    } else {
      console.log(`gs://${bucketName}/${fileName} is not an image I can handle`);
    }
  } else {
    console.log(`gs://${bucketName}/${fileName} already has a thumbnail`);
  }
});
```

# Arquivo package.json
```javascript
{
  "name": "thumbnails",
  "version": "1.0.0",
  "description": "Create Thumbnail of uploaded image",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0",
    "@google-cloud/pubsub": "^2.0.0",
    "@google-cloud/storage": "^5.0.0",
    "fast-crc32c": "1.0.4",
    "imagemagick-stream": "4.1.1"
  },
  "engines": {
    "node": ">=4.3.2"
  }
}
```

# Implantação da função
# --gen2: Usar a segunda geração de Cloud Functions.
# --entry-point: Define a função de entrada (memories-thumbnail-generator) do código.
# --trigger-event-filters: Define o evento (upload de arquivo no bucket) que aciona a função (type=google.cloud.storage.object.v1.finalized").
# --trigger-event-filters="bucket=qwiklabs-gcp-03-b67838ab9299-bucket" - especifica o bucket em que esse evento deve ser monitorado.
gcloud functions deploy memories-thumbnail-creator \
    --gen2 \
    --region="$REGION" \
    --runtime=nodejs18 \
    --source=. \
    --entry-point=memories-thumbnail-generator \
    --trigger-event-filters="type=google.cloud.storage.object.v1.finalized" \
    --trigger-event-filters="bucket=qwiklabs-gcp-03-b67838ab9299-bucket"

# Verifica a política de IAM do projeto
gcloud projects get-iam-policy qwiklabs-gcp-03-b67838ab9299

# Remove o acesso do engenheiro anterior
gcloud projects remove-iam-policy-binding qwiklabs-gcp-03-b67838ab9299 \
    --member="user:student-00-fc74bc19c7a4@qwiklabs.net" \
    --role="roles/viewer"
