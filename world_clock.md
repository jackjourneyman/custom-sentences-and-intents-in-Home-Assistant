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
