# Timers

Built-in sentences for timers only work on ESPHome and Wyoming voice satellites, and even then the timer runs locally on the device - they are not exposed as entites in Home Assistant itself - which means you can't show progress on a dashboard, for example.

You can control timers previously created in HA with the [timer helper](https://www.home-assistant.io/integrations/timer/), however.

# Start timer

## Custom sentence
```
language: "en"
intents:
  CustomTimerSet:
    data:
      # All times
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {hours} (hour|hours) [and] {minutes} (minute|minutes) [and] {seconds} (second|seconds)"
      # All one hour
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {hours} (hour|hours)"
        slots:
          seconds: 0
          minutes: 0
      # All one minute
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {minutes} (minute|minutes)"
        slots:
          seconds: 0
          hours: 0
      # All one second
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {seconds} (second|seconds)"
        slots:
          minutes: 0
          hours: 0
      # Minutes and seconds
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {minutes} (minute|minutes) [and] {seconds} (second|seconds)"
        slots:
          hours: 0
      # Hours and minutes
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {hours} (hour|hours) [and] {minutes} (minute|minutes)"
        slots:
          seconds: 0
      # Halves
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) half a minute"
        slots:
          hours: 0
          minutes: 0
          seconds: 30
      - sentences:
          - "(start|set) [(a|the)] timer (for|to) {minutes} and a half minutes"
        slots:
          hours: 0
          seconds: 30
      - sentences:
          - "(start|move) [(a|the)] timer (for|to) {hours} and a half hours"
        slots:
          minutes: 30
          seconds: 0
lists:
  seconds:
    range:
      from: 0
      to: 120
  minutes:
    range:
      from: 0
      to: 120
  hours:
    range:
      from: 0
      to: 24
```

## Intent
```
CustomTimerSet:
  action:
    - action: script.voice_timer_2
      data:
        duration: "{{hours | int(default=0)}}:{{ minutes | int(default=0) }}:{{ seconds | int(default=0) }}"
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer started."
```
-------------------------------------
# Stop timer

## Custom sentence
```
language: "en"
intents:
  CustomTimerCancel:
    data:
      - sentences:
          - "(stop | cancel) [the] timer"
```

## Intent
```
CustomTimerCancel:
  action:
    - action: script.voice_timer_cancel
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer cancelled."
```
-------------------------------------
# Pause timer

## Intent
```
CustomTimerPause:
  action:
    - action: script.voice_timer_pause
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer paused."
```
--------------------------------------
# Resume timer

## Intent
```
CustomTimerResume:
  action:
    - action: script.voice_timer_resume
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer started again."
```
