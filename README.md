# Jupita WebSocket SDK

Using the Jupita WebSocket SDK you can enable real time audio analytics with any base64 encoded audio received via WebSocket. This guide will help you set up the required accounts, implement the provided Jupita WebSocket SDK, as well as testing, maintenance & troubleshooting. Note that any speech-to-text API will work, however Google Speech-to-Text has been used for this SDK.

To get started you will need;

- [The Jupita WebSocket SDK](https://jupita.io/backend/audio-sdk/websocket)

- [A free Google Cloud Platform account](https://cloud.google.com/)

- [Ngrok](https://ngrok.com/)

## Getting started
The Jupita WebSocket SDK captures raw base64 audio streams which are then sent via Google Speech-to-Text to Jupita. When the transcriptions are received Jupita will apply timestamps to create the utterances in the order in which they occurred. There is a timestamp in each payload that increments from the time the stream starts. 

Multiple streams can be independently transcribed and fed to Jupita via .JSON format. All of this happens in real time during the audio. The Jupita WebSocket SDK can stream audio to and from any call made either to a phone, SIP, or any WebSocket endpoint that can accept and send base64 encoded audio.

Simply send your stream to the configured WebSocket in the SDK which runs on a simple App Engine flexible environment. From there, the audio is sent to Google Speech-to-Text, transcribed and sent to Jupita in real-time. The Jupita WebSocket SDK handles this entire process end-to-end.

Replace the URL attribute of the `Stream` tag and the phone number contained in the body of the `Dial` tag.

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
   <Start>
       <Stream URL="wss://yourdomain.com/media" />
   </Start>
   <Dial>+15550123456</Dial>
</Response>
```

The `Stream` tag starts the audio stream asynchronously and then control moves onto the `Dial` verb. `Dial` will call that number. The audio stream will end when the call is completed.

You will also need to setup your .env (environment variable) file – Note that this file must be placed inside your Node.js application folder.

## Getting started with Google Cloud Platform
This setup can either be done in an existing Google Cloud project or a new project. To set up a new project, follow the instructions [here](https://cloud.google.com/resource-manager/docs/creating-managing-projects).

Note, choose your app engine location carefully, you are unable to change this once created. Once you have the project selected that you want to work in, you’ll need to set up a few key things before getting started:

Enable APIs for Google Speech-to-Text by following the instructions [here](https://cloud.google.com/speech-to-text/docs/quickstart-ui).

Create a service account for your App Engine flexible environment to utilize when accessing other Google Cloud services. You’ll need to download the private key as a JSON file as well. Add firewall rules to allow your App Engine flexible environment to accept incoming connections for the WebSocket. A command such as the following should work from a Gcloud enabled terminal:

`Gcloud compute firewall-rules create default-allow-websockets-8080 --allow tcp:8080 --target-tags WebSocket --description "Allow WebSocket traffic on port 8080"`

## App spot
You will also require a URL from your selected hosting platform - such as GCP’s ‘App engine’ and apply that URL into your post request in the Jupita WebSocket SDK file ‘server.js’. This template is already setup in the Jupita WebSocket SDK. 

## App Engine flexible environment setup
Take the service account JSON key you downloaded earlier, rename it to ‘google_creds.json’ and replace the same named file in the Jupita WebSocket SDK with this file. 

Your ‘app.yaml’ file will look similar to the following (you will need to configure the variables of your app engine to suit your projects requirements);

- runtime: nodejs

- manual_scaling:

- instances: 1

- network:

- instance_tag: WebSocket

Once these two items are in order, you will be able to deploy your application with the command ‘Gcloud app deploy’. Once deployed, you can tail the console logs with the command ‘Gcloud app logs tail -s default’.
   
To setup your Google Cloud Platform account install & initialise the Cloud SDK by following the steps [here](https://cloud.google.com/sdk/docs/).

Additional Google Cloud Platform links –

- [App Engine](https://cloud.google.com/appengine/docs)

- [GCP pricing](https://cloud.google.com/appengine/pricing)

- [Instance classes](https://cloud.google.com/appengine/docs/standard#instance_classes)

- [Speech-to-Text basics](https://cloud.google.com/speech-to-text/docs/basics/)

- [Enhanced model](https://cloud.google.com/speech-to-text/docs/enhanced-models)

- [References](https://cloud.google.com/speech-to-text/docs/reference/rpc/google.cloud.speech.v1/)

- [Base64 encoding audio content](https://cloud.google.com/speech-to-text/docs/base64-encoding)

## Deploying the Jupita WebSocket SDK

1. Download the Jupita WebSocket SDK [here](https://jupita.io/backend/audio-sdk/websocket)

1. Place the folder on your local machine and open terminal

1. Run the command ‘NPM Install’ in the command line

1. To run the server in your local environment enter command ‘node ./server.js’

1. Make sure you have installed node JS version 14.16 or greater. Node -v will show current node.js version.

1. Install Google Cloud SDK. The command line which will be used to install SDK will be the same where Gcloud commands would work.

1. Find the latest version of speech to text here - [@google-cloud/speech](https://www.npmjs.com/package/@google-cloud/speech)

1. Initialise the SDK by running the command ‘Gcloud init’ inside your projects folder.

1. After Initialising SDK, terminal needs to be restarted.  

1. Choose your project in the command line.

1. To deploy your project to your Google Cloud instance, run command ‘Gcloud app deploy’

1. To test the output run command ‘Gcloud app logs tail -s default’

Custom Parameters: If you would like to add custom parameters, add them inside the prompt message function inside the ‘if’ statement and use ‘this.parameter’ name to retrieve the values.

Note, if using a repository for your project, when cloning, make sure to include the ‘google_creds.json’ file in your folder.

## Also required
1. Homebrew:  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

1. Cask : $ brew tap caskroom/cask

1. NGROK:  brew cask install ngrok

## Adding your credentials to your Node.js application
1. Streams.xml – found under ‘template’ directory, replace * with your Ngrok address in the stream URL. You find your Ngrok address in

1. Edit the gcloudignore & gitignore as per your requirements

1. Edit the app.yaml values as per your GCP app engine requirements

1. Edit the google_creds.json file replacing * with the required values

## Troubleshooting
1. If you are unable to get media transcriptions back in command line, check the path to the Speech to text API is correct in your .env file. Note that the environment variable (.env file) should remain inside the project folder so it may connect with the Google credentials JSON file.

1. If you are unable to connect with your phone number, check the local Ngrok address is the same as that of the ‘streams.xml’ file inside the template directory in your application.

1. Make sure to use Google Chrome for testing and downloading Service keys, it won’t work with other browsers.

1. Local host needs to be set at ‘8080’ to work with Google Cloud App Engine.

1. If you are getting ‘Deadline Exceeding’ error its due to a delay in connection with the GCP server or a possible error in ‘transcriptions.js’.

1. If you are getting ‘Internal Error’ it’s mostly due to a ‘Post request’ error in server.js

1. Always try to use latest version of Google Cloud SDK.

1. If you get the error message ‘Undefined’ note this is not a real error, it’s a condition set that if undefined transcriptions start the process to log transcriptions.

1. Internal server ‘500’ errors, most likely a ‘Post request’ error due to parsing of a string. Similar to ‘utterance’ due to interim results set to ‘true’ in config which should have been set to ‘false’.

1. If ‘message_type’ stays at ‘1’ and does not change, the issue is due to Google Cloud app engine not receiving enough values of ‘to’ and ‘from’ or ‘outbound’.

1. Ensure you update libraries regularly. Run ‘npm update’ in command line.

1. To update Gcloud SDK run ‘Gcloud components update’ in command line.


## The Jupita WebSocket SDK explained
Two tracks; outbound (touchpoint) + inbound (input)

In the below code snapshot you will see that the media stream handler connection is initiated by calling
‘connection.on’

```
classMediaStreamHandler {

  constructor(connection) {

    this.metaData = null;

    this.trackHandlers = {};

    connection.on('message', this.processMessage.bind(this));

    connection.on('close', this.close.bind(this));

  }
```

In the below code snapshot you will see that all required data objects are initiated null to start with such as touchpointID & inputID, to and from and they validate the `messageType`. If the app starts receiving event streams we specify that data at custom parameters such as touchpoint and input, if the data is null it returns null. We also track handlers by `messageType` 0 (touchpoint) & 1 (input).
```
processMessage(message){

    consttouchpointID = null;

    constinputID = null;

    constto = null;

    constfrom = null;

    if (message.type === 'utf8') {

      constdata = JSON.parse(message.utf8Data);

     

      if (data.event === "start") {

        this.metaData = data.start;

        this.touchpointID = data.start.customParameters['touchpoint'];

        this.inputID = data.start.customParameters['input'];

        this.to = data.start.customParameters['to'];

        this.from = data.start.customParameters['from'];

      

        returnthis.touchpointID,this.inputID;

      }

      if (data.event !== "media") {

        return;

      }

      consttrack = data.media.track;

      if (this.trackHandlers[track] === undefined) {

 

        varmessageType = 1;
```
This is where real validation for identifying two users happens as we check which user is outbound and apply `messageType` ‘0’ on each of those utterances, and type ‘1’ to the inbound user. There is also a log to check if you want to see the data pass by.
```
constservice = newTranscriptionService();

       

        service.on('transcription', (transcription) => {

                  log(`Transcription (${track}): ${transcription}`);

         

          if ((this.from == `input:a${this.inputID}` && track == "outbound") || (this.from == `input:a${this.touchpointID}` && track == "inbound")) {

            messageType = 0; // Touchpoint utterance

        } elseif ((this.from == `input:a${this.inputID}` && track == "inbound") || (this.from == `input:a${this.touchpointID}` && track == "outbound")) {

            messageType = 1;  // Input utterance

        } else {

            messageType = null;

        }

           

            log(`Touchpoint : ${this.touchpointID} || Input : ${this.inputID} || To: ${this.to} || From: ${this.from} || MessageType: ${messageType}` );
```
This is the post request, it also adds timestamps automatically. The last snippet is a validation for binary messages as they are not supported and will provide a log message.

```
axios.post('https://api.jupita.io/v1/dump', {

                  "input_id":this.inputID,

                  "touchpoint_id":this.touchpointID,

                  "message_type":String(messageType),

                  "text":transcription,

                  "isCall" :true,

                  "timestamp":log()

      })

      .then(function (response){

        console.log(response);

      })
   
      }); 

        this.trackHandlers[track] = service;

      }

      this.trackHandlers[track].send(data.media.payload);

    } elseif (message.type === 'binary') {

      log('Media WS: binary message received (not supported)');

    }

  }

  close(){
```

## Local testing using Ngrok
To test your application locally you will need to setup your local environment. Start by running the command ‘ngrok http 8080’ in terminal. This will show your local host URL.

Then, copy this URL and place into your streams ‘wss’ URL located in the Jupita Websocket SDK code. Replace App Spot URL with Ngrok URL, don’t forget to reverse when you have completed local testing.

```
$start = $response->start();
$stream = $start->stream(
    array(
        //'url' => 'wss://example.appspot.com',
        'url' => 'wss://12345678.ngrok.io',
        'track' => 'both_tracks'
    )
)
```

The recommended GCP instance class is ‘B4’ however you may experiment with other classes and lookout for any memory errors.

Simply add the instance value you wish to use to ‘instance_class:’ in your ‘app.yaml ‘ file. You may wish to add a second app instance in to avoid potential bottle necks. A 3rd option would be to create a Compute Engine within your Google Cloud account.

## Log reporting
You may wish to turn log reporting on or off. There could save resources by switching log reporting off.

In order to do this, navigate to the line of code `// log('Touchpoint : ${this.touchpointID} || Input : ${this.inputID} || To: ${this.to} || From: ${this.from}' );` and simply uncomment.


## Some useful VS Code Extensions
1. dotenv

1. Terminal

1. Preetier

1. ESLint

1. Beautify

## List of NPM Packages used within the Jupita WebSocket SDK - 
1. dotenv

1. @google-cloud/speech

1. axios

1. express

1. websocket

1. ssl-root-case

## How to reinstall node modules
Step 1. Install Homebrew - run this command `(from https://brew.sh/) - /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`

Then;

```

brew install node - NODE JS

brew install cask - CASK

brew cask install ngrok - NGROK

brew cask install google-cloud-sdk - GCP SDK

npm i for installing node_modules

npm update
```

## Dumping your audio to Jupita
URL https://api.jupita.io/v1/dump

Parameters

- “touchpoint_id” Your touchpoint ID here

- “input_id” Your input ID here

- “message_type” “0" for touchpoint or “1” for input (this is to inform Jupita who is speaking)

- “text” e.g. “Hello world”

```
axios.post('https://api.jupita.io/v1/dump', {

                  "input_id": this.inputID,

                  "touchpoint_id": this.touchpointID,

                  "message_type": String(messageType),

                  "text": transcription,

                  "isCall" : true,

                  "timestamp": log()

      })

      .then(function (response){

        console.log(response);

      })

```

### Sending parameters needed for API with stream
Custom parameters;

- ‘from’ – Inbound call

- ‘to’ – Outbound call

- touchpoint – user 1

- input – user 2

- message_type – This is a Boolean with either value of 0 for inbound and 1 for outbound

When one user calls another user it allocates the parameters as mentioned above and sends this to the Node.js application, this all happens within the Jupita WebSocket SDK while parameters are allocated in ‘server.js’ file. All of this is identified with the help of `messageType`.
   
## Further notes
1. The transcription-service.js file contains Google Speech-to-Text implementation and timestamp implementation. Unless you want to change any frequency of how you receive transcripts or confidence level for speech to text, that must not be changed.

1. The server.js file contains all the logic that connects the Jupita WebSocket SDK with the stream, including separating the streams and posting them to your backend.

1. Package.json contains all the dependencies of the project.

1. Google-creds.json contains the Speech to Text API key, you should paste your key in this file.

1. NGROK is used to test your application in a local developer environment while deployment to app engine should be for production code.

1. Ensure that parameter names and values are strings.

1. Make the POST request to the URL with all the required parameters.

1. The API will return a raw (not JSON) string.

1. ‘isCall’ is a required parameter & must be set to `true`.
   
If you require additional support please contact support@jupita.io
