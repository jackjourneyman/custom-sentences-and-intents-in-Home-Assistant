# World Clock

## Custom sentence
```
language: "en"
intents:
  CustomWorldClock:
    data:
      - sentences:
          - "what time is it in  {city}"
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
    - action: script.willow_tts_response
      data:
        tts_sentence: >-
          {% if time != 'unknown' and time != 'unavailable' %}
            "The time in {{ city }} is {{ time }}"
          {% else %}
            "Sorry, I didn't catch the city name."
          {% endif %}
```
## Notes

* This sentence/intent makes use of the [Worldclock integration](https://www.home-assistant.io/integrations/worldclock/) which generates sensors for places and timezones in the format ```sensor.worldclock_<place>```

* You could list the locations for which sensors have been defined in the custom sentence:
  ```
  lists:
    city:
      values:
        - "London"
        - "New York"
        - "Paris"
  ```
But if the user gave a name not on the list, the intent would fall back on the default Assist message "Sorry I didn't understand that". Using a wildcard makes it possible to filter out unknown entities in the intent and provide a custom message. It is not possible to define a default value in a list.
