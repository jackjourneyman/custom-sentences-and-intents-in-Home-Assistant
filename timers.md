# Timers

Built-in sentences for timers only work on ESPHome and Wyoming voice satellites, and even then the timer runs locally on the device - they are not exposed as entites in Home Assistant itself - which means you can't show progress on a dashboard, for example.

You can control [timer helpers](https://www.home-assistant.io/integrations/timer/) previously created in HA, however.

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
    - action: script.voice_timer
      data:
        duration: "{{hours | int(default=0)}}:{{ minutes | int(default=0) }}:{{ seconds | int(default=0) }}"
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer started."
```
## Script

```
sequence:
  - action: timer.start
    metadata: {}
    data:
      duration: "{{ duration }}"
    target:
      entity_id: timer.voice_timer
fields:
  duration:
    selector:
      time: {}
    name: duration
    default: "00:00:00"
    required: true
alias: Voice timer
```
## Notes

Most of the work here is done in the custom sentence, which tries to cover every possible permutation needed to deliver hours, minutes and seconds to the intent. The intent itslef is quite simple - it just launches a script to set the timer. This makes maintenance easier, particularly if you prefer to use the UI.

```{{ states('sensor.finished_phrase') }}``` is a random phrase equivalent to "OK, done that."

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
## Script
```
sequence:
  - action: timer.cancel
    metadata: {}
    data: {}
    target:
      entity_id: timer.voice_timer
alias: Voice timer cancel
```
-------------------------------------
# Pause timer

## Custom sentence
```
language: "en"
intents:
  CustomTimerPause:
    data:
      - sentences:
          - "(pause | hold) [the] timer"
```

## Intent
```
CustomTimerPause:
  action:
    - action: script.voice_timer_pause
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer paused."
```
## Script
```
sequence:
  - action: timer.pause
    metadata: {}
    data: {}
    target:
      entity_id: timer.voice_timer
alias: Voice timer pause
```
--------------------------------------
# Resume timer

## Custom sentence

```
language: "en"
intents:
  CustomTimerResume:
    data:
      - sentences:
          - "start [the] timer again"
          - "(restart | resume) [the] timer"
          - "carry on [with] the timer"
```

## Intent
```
CustomTimerResume:
  action:
    - action: script.voice_timer_resume
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer started again."
```
## Script
```
sequence:
  - action: timer.start
    metadata: {}
    data: {}
    target:
      entity_id: timer.voice_timer
fields: {}
alias: Voice timer resume
```
---------------------------------------

# Timer finished automation (optional)
```
alias: Voice timer finished
triggers:
  - trigger: state
    entity_id:
      - timer.voice_timer
    to: idle
    from: active
conditions: []
actions:
  - action: script.willow_chime
    metadata: {}
    data: {}
  - delay:
      hours: 0
      minutes: 0
      seconds: 2
      milliseconds: 0
  - action: script.willow_tts_response
    metadata: {}
    data:
      tts_sentence: Your timer has finished
mode: single
```
