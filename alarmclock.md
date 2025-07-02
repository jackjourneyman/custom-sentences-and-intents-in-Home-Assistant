# Alarm clock

This human-friendly alarm clock uses colloquial expressions for times - "a quarter to five" rather than 4:45.

As it stands it has limitations. You can only set the alarm for five minute intervals - "five past", "ten past" etc. - and the alarm can only be set 12 hours ahead.

## Custom sentence

```
language: "en"
intents:
  CustomAlarmclockSet:
    data:
      - sentences:
          - "wake me [up] ( at | for ) [a] {minute} {hour}"
          - "wake me [up] ( at | for ) {hour} {minute}"
          - "set ( the | an ) alarm for [a] {minute} {hour}"
          - "set ( the | an ) alarm for {hour} {minute}"          
lists:
  minute:
    values: 
      - in: "o'clock"
        out: "00"
      - in: "five past"
        out: "05"
      - in: "5 past"
        out: "05"        
      - in: "ten past"
        out: "10"
      - in: "10 past"
        out: "10"
      - in: "quarter past"
        out: "15"
      - in: "twenty past"
        out: "20"
      - in: "20 past"
        out: "20"
      - in: "twentyfive past"
        out: "25"
      - in: "25 past"
        out: "25"
      - in: "half past"
        out: "30"
      - in: "twenty-five to"
        out: "35"
      - in: "25 to"
        out: "35"
      - in: "twenty to"
        out: "40"
      - in: "20 to"
        out: "40"
      - in: "quarter to"
        out: "45"
      - in: "ten to"
        out: "50"
      - in: "10 to"
        out: "50"
      - in: "five to"
        out: "55"
      - in: "5 to"
        out: "55"
  hour:
    range:  
      from: 1
      to: 12  

```

## Intent

```

CustomAlarmclockSet:
  action:
    - variables:
    
        hour_val: >-
          {% if minute | int(0) > 30 %}
            {{ hour | int(0) -1 }}  
          {% else %}
            {{ hour | int(0) }}
          {% endif %}
        
        time: >- 
          {% if now().strftime('%H') | int(0) > 12 %}
            {{ now().strftime('%H') | int(0) - 12 }}
          {% else %}
            {{ now().strftime('%H') | int(0) }}
          {% endif %}
          
        now_val: "{{ time | float(0) * 3600 + now().strftime('%M') | int(0) * 60 + now().strftime('%S') | int(0) }}"
        
        alarm_val: "{{ hour_val | int(0) * 3600 + minute | int(0) * 60 }}"
        
        alarm: >-
          {% if alarm_val | int(0) < now_val | int(0) %}
            {{ alarm_val | int(0) + 43200 - now_val | int(0) }}
          {% else %}
            {{ alarm_val | int(0) - now_val | int(0) }}
          {% endif %}

    - action: timer.start
      target:
        entity_id: timer.voice_alarm
      data:
        duration: "{{ alarm }}"
       
    - action: script.tts_response
      data:
        tts_sentence: >-
          {% if minute | int(0) == 0 %}
             {{ states('sensor.finished_phrase') }}. Alarm set for {{ hour_val }}.
          {% else %}
             {{ states('sensor.finished_phrase') }}. Alarm set for {{ hour_val }} {{ minute }}.
          {% endif %} 

```
## Notes

## In the custom sentence...

For the minute values, ```in:``` is what the voice assistant hears, ```out:``` is what it passes to the intent script. In the intent they still have to be treated as strings and converted to numbers with ```| int(0)``` or ```| float(0)```.

## In the intent...

```hour_val``` will be the same as ```hour``` (received from the custom sentence) for "five past five" (5.05) etc., but one less for "five to six" (5.55) etc.

```time``` is the current hour expressed in terms of the 12-hour clock.

```now_val``` and ```alarm_val``` - times are much easier to compare if they are converted to seconds. ```timer.start``` also accepts duration in seconds.

If the alarm time ```alarm``` is less than the current time (ie the next day) add 12 hours.

```sensor.finished_phrase``` is a [random phrase](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/random_phrases.md) to start the response. "OK then," etc.

## Timer finished...

The alarm can sound with an automation similar to the one suggested in [Timers](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/timers.md#timer-finished-automation-optional).

