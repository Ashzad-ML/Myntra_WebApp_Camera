## Myntra Webapp with real time Classification

### File Structure
Myntra_WebApp_Webcam/
  │
  ├── Index.html
  ├── style.css
  ├── group.bin
  ├── model.json
  ├── classes.json


  ## **index.html**<br>
This is the html file where we have set the lamnding page and has used the webcam and our machine learning model for building the classifier app.

Function to set the Webcam
```
async function setupWebcam(useFrontCamera) {
      return new Promise((resolve, reject) => {
        const navigatorAny = navigator;
        navigator.getUserMedia = navigator.getUserMedia ||
          navigatorAny.webkitGetUserMedia || navigatorAny.mozGetUserMedia ||
          navigatorAny.msGetUserMedia;
        if (navigator.getUserMedia) {
          navigator.getUserMedia(
            { 
              video: { 
                facingMode: useFrontCamera ? 'user' : 'environment' 
              } 
            },
            stream => {
              webcamElement.srcObject = stream;
              webcamElement.addEventListener('loadeddata', () => resolve(), false);
            },
            error => reject());
        } else {
          reject();
        }
      });
```
**setupWebcam :** Here  we are creating as function where our system is navugatung the media deviced for in our system and is accessing the cameras.It also has a feature for flipping the camera for mobile interface.It is also loading all the meadia that is been captured

Function for doing the realtime predictions
```
async function predict() {
      const videoFrame = tf.browser.fromPixels(webcamElement).cast('float32');
      const resizedFrame = videoFrame.resizeBilinear([300, 300]);
      const batchedFrame = resizedFrame.expandDims(0);

      const prediction = await model.predict(batchedFrame);
      const class_scores = await prediction.data();  
      const max_score_id = class_scores.indexOf(Math.max(...class_scores));
      const classes = ["Pattern", "Solid"];

      resultElement.innerHTML = classes[max_score_id].toString();

      requestAnimationFrame(predict);
```
**predict :** In this function we are using the pretrained model of MobileNet on our custom dataset.We are capturing the video and resizing the pixels to amke sure it matched our model aspect ratio, we are using the bililinear function and then we are putting a dimension layer ontop of it which will help the model to identify the pixels that are captured by the webcam.
Then we are running the model.predict function where we are passing the captured frames.After that the max class score is been printed as **Solid** or **Pattern**.<br>

Main function
```
 async function startPrediction() {
      model = await tf.loadGraphModel('./model.json');
      await setupWebcam();
      predict();
```
**startPrediction:** Creating a function to call all the other fucntions one by one.First we are loading the model, then we are seeting up the camera and in the end we are calling the prediction function for calculatinh and providing the results.

