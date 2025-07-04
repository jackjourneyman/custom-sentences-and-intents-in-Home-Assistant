# Custom sentences and intents in Home Assistant

There are good reasons for using custom sentences and intents in Home Assistant.

* A built-in sentence may not exist yet
* The built-in sentence may not return the exact response you are looking for
* You may want to send the response to a speaker other than the one in your Voice Assistant device
* You may want more control over input than sentence triggers in an automation can give you

Documentation exists for [custom sentence syntax](https://developers.home-assistant.io/docs/voice/intent-recognition/template-sentence-syntax/), but there is very little for intents. They are referred to as [Intent scripts](https://www.home-assistant.io/integrations/intent_script/) but they are actually an odd mixture of script and template.

Here are some simple, practical examples of what you can do.
   
They are quite modular - everything doesn't have to be crammed into the intent. Templates can be used to construct common phrases that can be repeated in different places. Scripts can be used to carry out common actions, and these may be easier to maintain, particularly if you prefer to use the UI.

Please make comments and suggestions in the [discussions page](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/discussions)

----------------------------------------

## Index

[The basics](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/the_basics.md)

### Intents

[Alarm clock](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/alarmclock.md)

[Calendar](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/calendar.md)

[News headlines](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/news_headlines.md)

[Shopping lists](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/shopping_list.md)

[Speaking clock](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/speaking_clock.md)

[Timers](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/timers.md)

[World clock](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/world_clock.md)

### Scripts

[Speaker script](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/speaker_script.md)

### Templates

[Random phrases](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/random_phrases.md)

[Weather](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/weather.md)

### Resources

[Tools and shortcuts](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/resources.md)

[Home Assistant Index](https://jackjourneyman.github.io/homeassistantindex/index.html)

----------------------------------------------

## Speakers

By default Assist responds to built-in commands through the voice assistant that heard the command. This is difficult to change short of hacking the voice assistant device itself. (If your voice assistant is an ESPHome device there are some [configuration tools](https://esphome.io/components/voice_assistant.html).)

This is one reason for choosing to use custom sentences and intents - the response can be played through any speaker you like.

The examples in this repository call a [script](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/speaker_script.md) to deliver TTS sentences.
```
    - action: script.tts_response
      data:
        tts_sentence: "Whatever you need to say..."
```
You can substitute your own script, of if you want responses to come through your voice assistant you can simply use ```speech```:
```
    speech:
      text: Whatever you need to say...
```

