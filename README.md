# Custom sentences and intents in Home Assistant

There are good reasons for wanting to use custom sentences and intents in Home Assistant.

* A built-in sentence may not exist yet
* The built-in sentence may not return the exact response you are looking for
* You may want to send the response to a speaker other than the one in your Voice Assistant device
* Custom sentences give more control over input than sentence triggers in an automation

Documentation exists for [custom sentence syntax](https://developers.home-assistant.io/docs/voice/intent-recognition/template-sentence-syntax/), but there is very little for intents. They are referred to as [Intent scripts](https://www.home-assistant.io/integrations/intent_script/) but they are actually an odd mixture of script and template.

This repository aims to provide a few working examples.

## Index

[World Clock](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/world_clock.md)

## Format

Examples may be in four parts:

* Notes
* Custom sentence
* Intent
* Templates

The fourth part contains templates which may be used to construct phrases outside the intent. This is good practice if you want to maintain your intents easily. It also means that common phrases can be reused in different intents.

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

