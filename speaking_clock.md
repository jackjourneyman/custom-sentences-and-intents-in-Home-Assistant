# Speaking Clock

The custom sentence and the intent here are simple. The challenge is to get your voice assistant to speak the time naturally - "a quarter to five" instead of "16:45".

## Custom sentence
```
language: "en"
intents:
  CustomWhatTime:
    data:
      - sentences:
          - "(What | What's | What is | Tell me) [the] time [is it]"
```

## Intent
```
CustomWhatTime:
  action:
    - action: script.tts_response
      data:
        tts_sentence: It's {{ states('sensor.speaking_clock') }}
```

## Templates

There isn't a way to do this elegantly.

First convert the hour to the 12-hour clock.
```
  - sensor:
      - name: "Hour"
        state: >
          {% if now().strftime('%-I') | int == 0 %}
            12
          {% else %}
            {{ now().strftime('%-I') }}
          {% endif %}
```
Then cover every possible permutation of hours and minutes...
```
  - sensor:
      - name: "Speaking clock"
        state: >

          {% if now().strftime('%-I') | int == 0 and now().strftime('%M') | int == 0 %}
            midnight 

          {% elif states('sensor.hour') | int != 0 and now().strftime('%M') | int == 0 %}
            {{ states('sensor.hour') }} o'clock

          {% elif now().strftime('%M') | int == 15 %}
            a quarter past {{ states('sensor.hour') }}

          {% elif now().strftime('%M') | int == 30 %}
            half past {{ states('sensor.hour') }}
            
          {% elif now().strftime('%M') | int == 1 %}
            {{ now().strftime('%-M') }} minute past {{ states('sensor.hour') }}

          {% elif now().strftime('%M') | int == 45 and states('sensor.hour') | int == 12 %}
            a quarter to one

          {% elif now().strftime('%M') | int % 5 == 0 and now().strftime('%M') | int > 30 and states('sensor.hour') | int == 12 %}
            {{ 60 - now().strftime('%-M') | int }} to one

          {% elif now().strftime('%M') | int == 59 and states('sensor.hour')| int == 12 %}
            one minute to one                     

          {% elif now().strftime('%M') | int > 30  and states('sensor.hour')| int == 12 %}
            {{ 60 - now().strftime('%M') | int }} minutes to one
            
          {% elif now().strftime('%M') | int == 45 %}
            a quarter to {{ states('sensor.hour') | int +1 }}            

          {% elif now().strftime('%M') | int % 5 == 0 and now().strftime('%M') | int < 30  %}
            {{ now().strftime('%-M') }} past {{ states('sensor.hour') }}

          {% elif now().strftime('%M') | int % 5 == 0 and now().strftime('%M') | int > 30  %}
            {{ 60 - now().strftime('%-M') | int }} to {{ states('sensor.hour') | int +1 }}

          {% elif now().strftime('%M') | int < 30 %}
            {{ now().strftime('%-M') }} minutes past {{ states('sensor.hour')}}

          {% elif now().strftime('%M') | int == 59 %}
            {{ 60 - now().strftime('%M') | int }} minute to {{ states('sensor.hour') | int +1 }}         

          {% elif now().strftime('%M') | int > 30 %}
            {{ 60 - now().strftime('%M') | int }} minutes to {{ states('sensor.hour') | int +1 }} 

          {% endif %}
```

## Notes

```now().strftime('%M') | int % 5 == 0``` minutes after or before then hour are divisible by five
