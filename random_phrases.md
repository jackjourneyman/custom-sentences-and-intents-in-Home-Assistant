# Random Phrases

If you want to give your voice a bit of character, you can add random phrases to your intents. The same selection of phrases can get a bit old, however, even if there are half a dozen of them. These templates include a few empty strings, so the phrases will only *sometimes* be heard. The phrases change every minute.

### Starter phrase
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

To include this in an intent sentence, add ```{{ states('sensor.starter_phrase') }}```.

### Finished phrase
```
  - trigger:
      platform: time_pattern
      minutes: /1
    sensor:
      - name: Finished phrase
        state: >
          {{ [
          "Done.",
          "",
          "Okay.",
          "",
          "Okay. Done.",
          "",
          "Right. Done.",
          "",
          "Right.",
          ""
          ] | random }}
```
To include this in an intent sentence, add ```{{ states('sensor.finished_phrase') }}```.

### Wait phrase
```
  - trigger:
      platform: time_pattern
      minutes: /1
    sensor:
      - name: Wait phrase
        state: >
          {{ [
          "Just a moment.",
          "Just a minute.",
          "Wait a moment.",
          "Wait a minute.",
          "Hang on.",
          "Just need a moment.",
          "Just need a minute."
          ] | random }}
```
To include this in an intent sentence, add ```{{ states('sensor.wait_phrase') }}```. There are no empty strings - if there's going to be a delay you need to say so.
