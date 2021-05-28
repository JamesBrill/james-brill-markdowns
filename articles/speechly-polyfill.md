# Speechly enables full browser compability for Web Speech API

## What is the Web Speech API?

The [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API) is an experimental browser standard that enables web developers to effortlessly process voice input from their users. Its simple API can turn on the device's microphone and apply a speech-to-text algorithm to convert whatever the user says into text that the web app can process. At first glance, it seems to open the door to voice-enabled web apps.

## The problem

However, browser support for this API is limited. At the time of writing, the majority of its support is centralised in browsers made by Google, who authored much of the API's [specification](https://wicg.github.io/speech-api/). Indeed, the only browsers that do support it are owned by big tech companies that have the scale to afford to include a free speech-to-text service. Apple has [recently joined](https://firt.dev/ios-14.5/#speech-recognition-api) Google in offering a Siri-based equivalent in Safari.

This has a couple of consequences. Firstly, web apps that use this API have a fragmented experience across browsers. One [example](https://github.com/brave/brave-browser/issues/3725#issuecomment-555733958) is Duolingo, which only offers its voice exercises on Chrome. Indeed, even amongst the browsers that do offer the API, the speech-to-text algorithm differs between them, resulting in different transcriptions and different user experiences between browsers. For example, these are some ways different implementations of the API could yield different results:
1) A word may be transcribed correctly by some implementations and incorrectly by others.
2) A word may be transcribed incorrectly in different ways.
3) A word that is recognised correctly may still be formatted differently. A typical point of contention is how to format units, dates, numbers, times, etc. For example, "1cm", "1 cm", "1 centimetre", "1 centimeter", "8 December 2019", "8 Dec 2019", "08/12/2019", "08.12.19", "one million", "1000000", "1 000 000", "1,000,000", and so on.
4) Implementations across browsers are upgraded in a different cadence  and something that worked previously in one browser might not work in the next upgrade.

Secondly, there is a trust factor. Developers using the API probably don't realise that they are sending their users' voice data to a service owned by a big tech company like Google. They may assume that the transcription algorithm runs on the device when it is in fact performed in the cloud. Owning the browser and the speech recognition service also gives these companies the power to make arbitrary changes to the API, including turning it off, as well as lock out other browser vendors. An [example](https://github.com/brave/brave-browser/issues/3725#issuecomment-555694620) is Brave, a browser based on Chromium, which is unable to use Google's speech recognition service due to restrictions imposed by Google. Such restrictions widen the feature gap between browsers like Chrome and the rest of the field.

## The solution

Browsers do not have to be limited to using the speech recognition services owned by Google and Apple. There are more widely supported browser standards like the [Media Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Media_Streams_API) that can enable developers to stream audio data from a microphone to _any_ service. The Web Speech API can be replicated by building code on top of these APIs, escaping the vendor lock-in imposed by the browser's native choice of speech recognition service. Indeed, it can be replicated on browsers that _don't_ support it in the first place.

Code that implements missing browser functionality like this is called a [polyfill](https://developer.mozilla.org/en-US/docs/Glossary/Polyfill). The good news is that there exists a polyfill for the Web Speech API that uses Speechly's speech recognition service under the hood. Any web app using this polyfill would be able to provide a consistent voice-enabled user experience across all browsers, using an API that the developer has chosen, can configure, and can trust.

The code for the polyfill can be found [here](https://github.com/speechly/speech-recognition-polyfill). It can be used in isolation, but if you are using React to build your web app, we recommend you combine it with [react-speech-recognition](https://github.com/JamesBrill/react-speech-recognition) for the simplest set-up.

## An example using React

The repositories both include examples of the two libraries working together and full API documentation, but we'll repeat the basic example here to give you a taste.

First, sign up for free with Speechly and get an app ID. You can find a quick guide for that [here](https://docs.speechly.com/quick-start/stt-only/).

Next, install the two libraries in your React app:
```
npm i --save @speechly/speech-recognition-polyfill
npm i --save react-speech-recognition
``` 

We're going to make a simple push-to-talk button component. When held down, it will display a transcript from the microphone. When the button is released, transcription will end. Using your Speechly app ID, create a React component like the following:
```
import React from 'react';
import { createSpeechlySpeechRecognition } from '@speechly/speech-recognition-polyfill';
import SpeechRecognition, { useSpeechRecognition } from 'react-speech-recognition';

const appId = '<INSERT_SPEECHLY_APP_ID_HERE>';
const SpeechlySpeechRecognition = createSpeechlySpeechRecognition(appId);
SpeechRecognition.applyPolyfill(SpeechlySpeechRecognition);

const Dictaphone = () => {
  const { transcript, listening } = useSpeechRecognition();
  const startListening = () => SpeechRecognition.startListening({ continuous: true });

  return (
    <div>
      <p>Microphone: {listening ? 'on' : 'off'}</p>
      <button
        onTouchStart={startListening}
        onMouseDown={startListening}
        onTouchEnd={SpeechRecognition.stopListening}
        onMouseUp={SpeechRecognition.stopListening}
      >Hold to talk</button>
      <p>{transcript}</p>
    </div>
  );
};
export default Dictaphone;
```

Run your web app, hold down the button and speak into your microphone (you may need to give the browser permission to use the microphone first). You should see your speech transcribed like this:

![](../images/speechly-polyfill-example.png?raw=true)

Give it a try and let us know how you get on! If you have any feedback on either library, raise a GitHub issue on the [polyfill repository](https://github.com/speechly/speech-recognition-polyfill) or [react-speech-recognition](https://github.com/JamesBrill/react-speech-recognition).