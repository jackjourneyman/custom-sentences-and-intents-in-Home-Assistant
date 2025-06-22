# Weather

There is a set of [recommended codes](https://developers.home-assistant.io/docs/core/entity/weather) for weather conditions in Home Assistant weather integrations (recommended because they are included in translation files and will be displayed with the correct icon).

![Weather codes](https://github.com/jackjourneyman/custom-sentences-and-intents-in-Home-Assistant/blob/main/table_of_weather_codes.png)

These will appear as the state of the weather entity.



Some of them are challenging for TTS because of the hyphens, and Amazon Polly at least insists on rendering partlycloudy as "part*lie* cloudy".

You can iron out most of these problems with a short template:
```
# Correct text in weather summary that voice assistants find hard to pronounce

template:
  - sensor:
      - name: Weather voice
        state: >
          {% if is_state('weather.your_weather_entity', 'partlycloudy') %}
              partly cloudy
          {% else %}
              {{ states('weather.your_weather_entity') | replace('-', ' ') }}
          {% endif %}
```
