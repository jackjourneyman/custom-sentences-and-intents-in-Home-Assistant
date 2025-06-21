# Shopping lists

## What's on the shopping list

**Custom sentence**
```
language: "en"
intents:
  CustomShoppingList:
    data:
      - sentences:
          - "Read me (the | my) shopping list"
          - "(What's | What is) (in | on) (the | my) shopping list"
```
**Intent**
```
CustomShoppingList:
  action:
    - action: todo.get_items
      target:
        entity_id: todo.shopping_list
      data:
        status: needs_action
      response_variable: shopping_list_data
    - action: script.willow_tts_response
      data:
        tts_sentence: >-
          {% if states('todo.shopping_list') | float(0) > 0 %}
          {{ states('sensor.starter_phrase') }} At the moment you've got,
          {% set x = shopping_list_data['todo.shopping_list']['items'] | map(attribute='summary')| list %}
          {{' and '.join((x|join(', ')).rsplit(', ', 1)) }}
          {% else %}
          Nothing on the shopping list at the moment.
          {% endif %}
```
**Notes**

```{{ states('sensor.starter_phrase') }}``` A random phrase to start off the response - "OK then, etc.
