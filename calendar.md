# What's in the diary?

## Custom sentence
```
language: "en"
intents:
  CustomCalendarToday:
    data:
      - sentences:
          - "(What's | What is) happening"
          - "(What's | What is) in the (calendar | diary) [for] today"
          - "(What's | What is) in the (calendar | diary)"
```
## Intent
```
CustomCalendarToday:
  action:
    - action: calendar.get_events
      target:
        entity_id: calendar.mycalendar
      data:
        duration:
          hours: "{{ 23 - now().strftime('%H') | float(0) }}"
      response_variable: diary
    - action: script.tts_response
      data:
        tts_sentence: >-
          {% if states('sensor.number_of_events_today') | float(0) > 0 %}
          Today you've got, 
          {% for event in diary['calendar.mycalendar'].events %} 
          {{ event.summary }}.
          {% endfor %}
          {% else %}
          Nothing today.
          {% endif %}
```
## Notes

**In the custom sentence...**

```"(What's | What is) in the (calendar | diary)"``` is likely to create a [collision](https://github.com/jackjourneyman/Custom_sentences_and_intents_in_Home_Assistant/blob/main/the_basics.md#collisions) on its own. To avoid this, make it a secondary sentence and make the primary sentence unique.

**In the intent...**

```calendar.mycalendar``` Substitute the name of your calendar entity.

```
       duration:
         hours: "{{ 23 - now().strftime('%H') | float(0) }}"
```
Fetches calendar events for the rest of the day.

------------------------------------

# What's in the diary tomorrow?

## Custom sentence
```
language: "en"
intents:
  CustomCalendarTomorrow:
    data:
      - sentences:
          - "(What's | What is) happening tomorrow"
          - "(What's | What is) in the (calendar | diary) [for] tomorrow"
```

## Intent
```
CustomCalendarTomorrow:
  action:
    - action: calendar.get_events
      target:
        entity_id: calendar.mycalendar
      data:
        start_date_time: "{{ (now() + timedelta(days=1)).strftime('%Y-%m-%d 00:00:00') }}"
        end_date_time: "{{ (now() + timedelta(days=2)).strftime('%Y-%m-%d 00:00:00') }}"
      response_variable: diary
    - action: script.tts_response
      data:
        tts_sentence: >-
          {% if states('sensor.number_of_events_tomorrow') | float(0) > 0 %}
          Tomorrow you've got, 
          {% for event in diary['calendar.mycalendar'].events %} 
          {{ event.summary }}.
          {% endfor %}
          {% else %}
          Nothing tomorrow.
          {% endif %}
```

## Notes

**In the intent...**

```calendar.mycalendar``` Substitute the name of your calendar entity.

----------------------------------------------------------

# What's in the diary next week?

## Custom sentence
```
language: "en"
intents:
  CustomCalendarWeek:
    data:
      - sentences:
          - "(What's | What is) happening (this | next) week"
          - "(What's | What is) in the (calendar | diary) [for] (this | next) week"
```
## Intent
```
CustomCalendarWeek:
  action:
    - action: calendar.get_events
      target:
        entity_id: calendar.mycalendar
      data:
        start_date_time: "{{ (now() + timedelta(days=1)).strftime('%Y-%m-%d 00:00:00') }}"
        end_date_time: "{{ (now() + timedelta(days=7)).strftime('%Y-%m-%d 00:00:00') }}"
      response_variable: diary
    - action: script.tts_response
      data:
        tts_sentence: >-
          {% for event in diary['calendar.mycalendar'].events | sort(attribute='start') %} 
          {% set day = event.start %}
          On {{ as_timestamp(day) | timestamp_custom('%A') }} you've got, {{ event.summary }}.
          {% endfor %}
```
## Notes

**In the intent...**

```calendar.mycalendar``` Substitute the name of your calendar entity.

```end_date_time: "{{ (now() + timedelta(days=7)).strftime('%Y-%m-%d 00:00:00') }}"``` This provides calendar entries for the next seven days, not for the week beginning next Monday...






