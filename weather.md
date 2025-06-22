# Weather

There is a set of [recommended codes](https://developers.home-assistant.io/docs/core/entity/weather) for weather conditions in Home Assistant weather integrations (recommended because they are included in translation files and will be displayed with the correct icon).

![Screenshot 2025-01-11 16.00.00|243x500](upload://3pmWOwRzAYR0c0Z2z3gTA95pg1.png)

These will appear as the state of the weather entity.

![image|534x500](upload://zJLEOZDm0j1M1HDIAN37cGldW1.png)

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
