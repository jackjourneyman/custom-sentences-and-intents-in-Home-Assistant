# Custom sentences and intents in Home Assistant

There are good reasons for wanting to use custom sentences and intents in Home Assistant.

* A built-in sentence may not exist yet
* The built-in sentence may not return the exact response you are looking for
* You may want to send the response to a speaker other than the one in your Voice Assistant device
* Custom sentences give more control over input than sentence triggers in an automation

Documentation exists for [custom sentence syntax](https://developers.home-assistant.io/docs/voice/intent-recognition/template-sentence-syntax/), but there is very little for intents. They are referred to as [Intent scripts](https://www.home-assistant.io/integrations/intent_script/) but they are actually an odd mixture of script and template.

This repository aims to provide a few working examples.

Descriptions of intents are in three parts:

 * Custom sentence
 * Intent
 * Notes
   
Templates can be used to construct phrases outside the intent. This is good practice if you want to maintain your intents easily. It also means that common phrases can be reused in different intents.


## Index

### Intents

[Calendar - "What's in the diary?"](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/calendar.md)

[World Clock](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/world_clock.md)

### Scripts

[Speaker script](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/speaker_script.md)

### Templates

[Random phrases](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/random_phrases.md)

&nbsp;
## Storage

Custom sentences have to be saved as yaml files in ```config/custom_sentences/<language_code>/``` with a separate file for each sentence. Being in English, the examples here are saved in ```config/custom_sentences/en/```. The name of the yaml file doesn't matter, but they should all begin with
```
language: "en"
intents:
  <IntentName>:
```
where ```<IntentName>``` is the name of the intent the sentence points to.

Intents are stored in ```configuration.yaml```, but their number will grow rapidly (the same with templates), so it is good practice to keep them in yaml files of their own, then add this to ```configuration.yaml```
```
intent_script: !include intents.yaml
template: !include templates.yaml
```
For more information, see [splitting configuration.yaml](https://www.home-assistant.io/docs/configuration/splitting_configuration/)

## "Collisions"

In theory custom sentences should take precedence over built-in sentences, but this doesn't always happen. It's a particular problem when the custom sentence contains the words "in" or "on" - "What's in the diary" is likely to give the error "No area named diary".

You can avoid this by creating a unique sentence, then adding the problem sentence as an alernative. For example:
```
language: "en"
intents:
  CustomCalendarToday:
    data:
      - sentences:
          - "(What's | What is) in the (calendar | diary) [for] today"
          - "(What's | What is) in the (calendar | diary)"
```
## Speakers

By default Assist responds to built-in commands through the voice assistant that heard the command. This is difficult to change short of hacking the voice assistant device itself. (If your voice assistant is an ESPHome device there are some [configuration tools](https://esphome.io/components/voice_assistant.html).)

This is one reason for choosing to use custom sentences and intents - the response can be played through any speaker you like.

The examples in this repository call a [script](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/speaker_script.md) to deliver TTS sentences.
```
    - action: script.tts_response
      data:
        tts_sentence: "Whatever you need to say..."
```
If you don't want to do this you can simply use ```speech``` instead:
```
    speech:
      text: Whatever you need to say...
```

