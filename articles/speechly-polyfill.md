# Speechly enables full browser compability for Web Speech API

## What is the Web Speech API?

The [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API) is an experimental browser standard that enables web developers to effortlessly process voice input from their users. Its simple API can turn on the device's microphone and apply a speech-to-text algorithm to convert whatever the user says into text that the web app can process. At first glance, it seems to open the door to voice-enabled web apps.

# The problem

However, browser support for this API is limited. At the time of writing, the majority of its support is centralised in browsers made by Google, who authored much of the API's [specification](https://wicg.github.io/speech-api/). Indeed, the only browsers that do support it are owned by big tech companies that have the scale to afford to include a free speech-to-text service. Apple has [recently joined](https://firt.dev/ios-14.5/#speech-recognition-api) Google in offering a Siri-based equivalent in Safari.

This has a couple of consequences. Firstly, web apps that use this API have a fragmented experience across browsers. One [example](https://github.com/brave/brave-browser/issues/3725#issuecomment-555733958) is Duolingo, which only offers its voice exercises on Chrome. Indeed, even amongst the browsers that do offer the API, the speech-to-text algorithm differs between them, resulting in different transcriptions and different user experiences between browsers. For example, these are some ways different implementations of the API could yield different results:
1) A word may be transcribed correctly by some implementations and incorrectly by others.
2) A word may be transcribed incorrectly in different ways.
3) A word that is recognised correctly may still be formatted differently. A typical point of contention is how to format units, dates, numbers, times, etc. For example, "1cm", "1 cm", "1 centimetre", "1 centimeter", "8 December 2019", "8 Dec 2019", "08/12/2019", "08.12.19", "one million", "1000000", "1 000 000", "1,000,000", and so on.
4) Implementations across browsers are upgraded in a different cadence  and something that worked previously in one browser might not work in the next upgrade.

Secondly, there is a trust factor. Developers using the API probably don't realise that they are sending their users' voice data to a service owned by a big tech company like Google. They may assume that the transcription algorithm runs on the device when it is in fact performed in the cloud. Owning the browser and the speech recognition service also gives these companies the power to make arbitrary changes to the API, including turning it off, as well as lock out other browser vendors. An [example](https://github.com/brave/brave-browser/issues/3725#issuecomment-555694620) is Brave, a browser based on Chromium, which is unable to use Google's speech recognition service due to restrictions imposed by Google. Such restrictions widen the feature gap between browsers like Chrome and the rest of the field.

# The solution