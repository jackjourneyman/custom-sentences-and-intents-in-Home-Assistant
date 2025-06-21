# Random Phrases

If you want to give your voice a bit of character, you can add random phrases to your intents. The same selection of phrases can get a bit old, however, even if there are half a dozen of them. This template includes a few empty strings, so the phrase will only *sometimes* be heard.

```
  - trigger:
      platform: time_pattern
      minutes: /1
    sensor:
      - name: Starter phrase
        state: >
          {{ [
          "Well.",
          "",
          "Okay.",
          "",
          "Right. well.",
          "",
          "So.",
          "",
          "Okay. So.",
          ""
          "Okay then.",
          ""
          ] | random }}
```

To include this in an intent sentence, add ```{{ starter_phrase }}```.
