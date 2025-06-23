# Custom sentences and intents - the basics

Custom sentences and intents come in pairs, linked by an intent name.
```
language: "en"
intents:
  CustomWhatTime:
    data:
      - sentences:
          - "what time is it"
          - "tell me the time"
```
```
CustomWhatTime:
  action:
    - action: tts.speak
      target:
        entity_id: tts.home_assistant_cloud
      data:
        cache: true
        media_player_entity_id: media_player.kitchen
        message: Too late
```
## Saving

Custom sentences should be saved as yaml files in ```config/custom_sentences/<language_code>/``` with a separate file for each sentence. Being in English, the examples here are saved in ```config/custom_sentences/en/```. The name of the yaml file doesn't matter, but they should all begin with
```
language: "en"
intents:
  <IntentName>:
```
where ```<IntentName>``` is the name of the intent the sentence points to.

Intents are stored together in ```configuration.yaml```, but their number will grow rapidly so it is good practice to keep them in a yaml file of their own, then add this to ```configuration.yaml``` with the line:
```
intent_script: !include intents.yaml
```
For more information, see [splitting configuration.yaml](https://www.home-assistant.io/docs/configuration/splitting_configuration/).

## Sentences

### Syntax

Complete custom sentences can be listed
```
      - sentences:
          - "what time is it"
          - "what is the time"
          - "what's the time"
          - "tell me the time"
```
...or a single sentence can be marked to show alternative words, and words which can be omitted
```
      - sentences:
          - "(what | what's | what is | tell me) [the] time [is it]"
```
Documentation on custom sentence syntax can be found [here](https://developers.home-assistant.io/docs/voice/intent-recognition/template-sentence-syntax/).

### Slots

Slots are placeholders in custom sentences containing values to be passed to the intent. In the sentence 

```
          - "play {album} by {artist}"
```

```{album}``` and ```{artist}``` are slots. When a user says "Play Aqualung by Jethro Tull", the slot ```album``` captures "Aqualung" and the slot ```artist``` captures "Jethro Tull". The matched intent would be able to generate the TTS sentence

```
"OK. Playing {{album}} by {{artist}}"
```

If your slot value is a string, you can restrict the user to a list of valid options
```
language: en
intents:
  PlayAlbum:
    data:
      - sentences:
          - "play {album} by jethro tull"
lists:
  album:
    values:
      - "Stand Up"
      - "Aqualung"
      - "Songs from the Wood"
      - "Heavy Horses"
```
...or you can use a wildcard
```
lists:
  album:
    wildcard: true
```
> **Note:** If you specify a list of options and the user provides a value not on the list, you will get the default error message from your voice assistant - "Sorry, I don't understand" or something similar.
>
> If you specify a wildcard, it's up to you to use the intent to filter out invalid options and provide an error message.

If your slot value is a number, you can specify the range.
```
lists:
  brightness:
    range:
      from: 1
      to: 255
```
### "Collisions"

In theory custom sentences should take precedence over built-in sentences, but this doesn't always happen. It's a particular problem when the custom sentence contains the words "in" or "on" - "What's in the diary?" is likely to give the error "No area named diary".

You can reduce the chances of this by creating a unique sentence first, then adding the problem sentence as an alternative. For example:
```
language: "en"
intents:
  CustomCalendarToday:
    data:
      - sentences:
          - "(What's | What is) happening"
          - "(What's | What is) in the (calendar | diary)"
```
## Intents

Intent scripts, like normal scripts, are series of actions to be executed. They are likely to contain more templating than normal scripts, and the last action will probably be a TTS statement of some kind.

If your custom sentence is a command, the first part of the intent will execute it and the second part will be TTS confirmation. For example:
```
language: "en"
intents:
  CustomSetTemperature:
    data:
      - sentences:
          - "(set|change) [the] (temperature | heating | thermostat) to {temp} [degrees]"
lists:
  temp:
    range:
      from: 0
      to: 35
```
```
CustomSetTemperature:
  action:
    - action: climate.set_temperature
      data:
        temperature: "{{ temp }}"
      target:
        entity_id: climate.hallway
    - action: script.tts_response
      data:
        tts_sentence: "Heating set to {{ temp }}"
```
When the sentence a is question it gets more complicated. In this example, the user has asked "Have I got coffee on the shopping list?" "Coffee" is held in the slot ```{item}```.
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
* The first action is a standard [todo list command](https://www.home-assistant.io/integrations/todo/), which finds incomplete items in the shopping list and lists them in the variable ```shopping_list_data```.

* The second action creates the variable ```found``` which will be true if coffee is in ```shopping_list_data```, false if not.

* The third action calls a script to deliver the TTS response, which will be "yes" if ```found``` is true, otherwise "no".

> Notice that in TTS statements and HA commands the slot value ```item``` is enclosed in curly brackets. In the template there are no curly brackets, and since this is a variable not a HA sensor value no normal brackets or quotes are needed either. More details [here](https://community.home-assistant.io/t/how-to-read-a-template-in-yaml-the-why-and-how-of-all-those-delimiters/843841).

In the previous example the ```if... else...``` structure was in the TTS statement. You can also use the ```choose:``` action specify different paths with different actions.
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
                tts_sentence: "Removed {{ item }}"                
        - conditions: "{{ not found }}"
          sequence:
            - action: script.tts_response
              data:
                tts_sentence: "Sorry. Can't find {{ item }} in the list"
```




