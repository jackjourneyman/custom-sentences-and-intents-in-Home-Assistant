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
        entity_id: calendar.stiltjack
      data:
        duration:
          hours: "{{ 23 - now().strftime('%H') | float(0) }}"
      response_variable: diary
    - action: script.willow_tts_response
      data:
        tts_sentence: >-
          {% if states('sensor.number_of_events_today') | float(0) > 0 %}
          Today you've got, 
          {% for event in diary['calendar.stiltjack'].events %} 
          {{ event.summary }}.
          {% endfor %}
          {% else %}
          Nothing today.
          {% endif %}
```
## Notes


