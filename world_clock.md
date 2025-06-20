# World Clock

## Custom sentence
```
language: "en"
intents:
  CustomWorldClock:
    data:
      - sentences:
          - "what time is it in {city}"
          - "what's the time in {city}"
lists:
  city:
    wildcard: true
```
## Intent
```
CustomWorldClock:
  action:
    - variables:
        time: "{{ states('sensor.worldclock_' ~ city|replace(' ', '_')|lower) }}"
    - action: script.tts_response
      data:
        tts_sentence: >-
          {% if time != 'unknown' and time != 'unavailable' %}
            "The time in {{ city }} is {{ time }}"
          {% else %}
            "Sorry, I didn't catch the city name."
          {% endif %}
```
## Notes

**In the custom sentence...**

This sentence/intent makes use of the internal [Worldclock integration](https://www.home-assistant.io/integrations/worldclock/) which generates sensors for places and timezones in the format ```sensor.worldclock_<place>```

You could list the locations for which sensors have been defined in the custom sentence:
  ```
  lists:
    city:
      values:
        - "London"
        - "New York"
        - "Paris"
  ```
But if the user gave a name not on the list, the intent would fall back on the default Assist message "Sorry I don't understand". Using a wildcard makes it possible to filter out unknown entities in the intent and provide a custom message. (It is not currently possible to define a default value in a list.)

**In the intent...**
```
    - variables:
        time: "{{ states('sensor.worldclock_' ~ city|replace(' ', '_')|lower) }}"
```
This takes the value passed from the custom sentence ```city``` and adds it to the string ```sensor.worldclock_``` to create a sensor name, then replaces spaces with underscores (New York becomes New_York) and changes everything to lowercase. The state of the resulting sensor is stored in the variable ```time```.

