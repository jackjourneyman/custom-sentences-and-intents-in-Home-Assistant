# What's in the diary?

## Custom sentence
```
language: "en"
intents:
  CustomCalendarToday:
    data:
      - sentences:
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
    - action: script.willow_tts_response
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

```"(What's | What is) in the (calendar | diary)"``` is likely to create a [collision](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/tree/main?tab=readme-ov-file#collisions) on its own. To avoid this, make the primary sentence unique.

**In the intent...**

```calendar.mycalendar``` Substitute the name of your calendar entity.

```
       duration:
         hours: "{{ 23 - now().strftime('%H') | float(0) }}"
```
Fetches calendar events for the rest of the day.



