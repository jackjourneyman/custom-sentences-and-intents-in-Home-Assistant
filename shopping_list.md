# Shopping lists

## What's on the shopping list?

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
    - action: script.tts_response
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

This assumes the [shopping list](https://www.home-assistant.io/integrations/shopping_list/) integration has been set up in Home Assistant.

```{{ states('sensor.starter_phrase') }}``` A [random phrase](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/random_phrases.md) to start off the response - "OK then", etc.

```{{' and '.join((x|join(', ')).rsplit(', ', 1)) }}``` inserts "and" between the last two items.

-----------------------------------------

## Have I got coffee on the shopping list?

**Custom sentence**
```
language: "en"
intents:
  QueryShoppingListItem:
    data:
      - sentences:
          - "(did | have) i (put | add) {item} (on | to) (my|the) shopping list"
          - "have I added {item} to (my | the) shopping list"
          - "is {item} on (my|the) shopping list"
          - "have I got {item} (on | in) (my | the) shopping list"
lists:
  item:
    wildcard: true
```

**Intent**
```
QueryShoppingListItem:
  action:
    - action: todo.get_items
      target:
        entity_id: todo.shopping_list
      data:
        status: needs_action
      response_variable: shopping_list_data
    - variables:
        found: "{{ shopping_list_data['todo.shopping_list']['items']|selectattr('summary','search',item)|list|count > 0 }}"
    - action: script.tts_response
      data:
        tts_sentence: >-
          {% if found %}
              "Yes. {{item}} is already there."
          {% else %}
              "No. Can't find {{item}}"
          {% endif %}
```
**Notes**

Not just coffee, obviously...

```found: "{{ shopping_list_data['todo.shopping_list']['items']|selectattr('summary','search',item)|list|count > 0 }}"``` searches the shopping list for {{item}} and returns true or false.

---------------------------------
## Put coffee on the shopping list

**Custom sentence**
```
language: "en"
intents:
  AddShoppingListItem:
    data:
      - sentences:
          - "add {item} to (my|the) shopping list"
          - "put {item} on (my|the) shopping list"
lists:
  item:
    wildcard: true
```

**Intent**
```
AddShoppingListItem:
  action:
    - action: todo.get_items
      target:
        entity_id: todo.shopping_list
      data:
        status: needs_action
      response_variable: shopping_list_data
    - variables:
        found: "{{ shopping_list_data['todo.shopping_list']['items']|selectattr('summary','search',item)|list|count > 0 }}"
    - choose:
        - conditions: "{{ not found }}"
          sequence:
            - action: shopping_list.add_item
              data:
                name: "{{ item }}"
            - action: script.tts_response
              data:
                tts_sentence: "{{ states('sensor.finished_phrase') }}. Added {{ item }}"                
        - conditions: "{{ found }}"
          sequence:
            - action: script.tts_response
              data:
                tts_sentence: "Looks like you've already got {{ item }} on the list"
```
**Notes**

```found: "{{ shopping_list_data['todo.shopping_list']['items']|selectattr('summary','search',item)|list|count > 0 }}"``` searches the shopping list for {{item}} and returns true or false.

```{{ states('sensor.finished_phrase') }}``` A [random phrase](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/random_phrases.md) to finish off the response - "OK done", etc.

--------------------------------------------------

## Take coffee off the shopping list

**Custom sentence**
```
language: "en"
intents:
  DeleteShoppingListItem:
    data:
      - sentences:
          - "remove {item} from (my|the) shopping list"
          - "delete {item} from (my|the) shopping list"
          - "take {item} off (my|the) shopping list"
          - "clear {item} from (my|the) shopping list"
lists:
  item:
    wildcard: true
```
**Intent**
```
DeleteShoppingListItem:
  action:
    - action: todo.get_items
      target:
        entity_id: todo.shopping_list
      data:
        status: needs_action
      response_variable: shopping_list_data
    - variables:
        found: "{{ shopping_list_data['todo.shopping_list']['items']|selectattr('summary','search',item)|list|count > 0 }}"
    - choose:
        - conditions: "{{ found }}"
          sequence:
            - action: shopping_list.remove_item
              data:
                name: "{{ item }}"
            - action: script.tts_response
              data:
                tts_sentence: "{{ states('sensor.finished_phrase') }}. Removed {{ item }}"                
        - conditions: "{{ not found }}"
          sequence:
            - action: script.tts_response
              data:
                tts_sentence: "Sorry. Can't find {{ item }} in the list"
```
**Notes**

```found: "{{ shopping_list_data['todo.shopping_list']['items']|selectattr('summary','search',item)|list|count > 0 }}"``` searches the shopping list for {{item}} and returns true or false.

```{{ states('sensor.finished_phrase') }}``` A [random phrase](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/random_phrases.md) to finish off the response - "OK done", etc.
